---
tags: 
created: 2024-12-11 14:48
modified: 2024-12-11 14:48
related: []
updated: 2024-12-11 14:54
---
# ModelAndView 로 Data 보내는 방법
1. **단일 책임 원칙 적용**

```jsp
<!-- productList.tag - 상품 목록 표시만 담당 -->
<%@ tag body-content="empty" %>
<%@ attribute name="products" type="java.util.List" required="true" %>

<div class="product-grid">
    <c:forEach items="${products}" var="product">
        <jsp:include page="/WEB-INF/tags/product/productCard.tag">
            <jsp:param name="id" value="${product.id}" />
            <jsp:param name="name" value="${product.name}" />
            <jsp:param name="price" value="${product.price}" />
        </jsp:include>
    </c:forEach>
</div>

<!-- filter.tag - 필터링 UI만 담당 -->
<%@ tag body-content="empty" %>
<%@ attribute name="selectedCategory" required="true" %>
<%@ attribute name="categories" type="java.util.List" required="true" %>

<form id="filterForm" action="${pageContext.request.contextPath}/products" method="GET">
    <select name="category" onchange="this.form.submit()">
        <option value="">전체 카테고리</option>
        <c:forEach items="${categories}" var="category">
            <option value="${category.code}" ${category.code eq selectedCategory ? 'selected' : ''}>
                ${category.name}
            </option>
        </c:forEach>
    </select>
</form>

<!-- sort.tag - 정렬 UI만 담당 -->
<%@ tag body-content="empty" %>
<%@ attribute name="currentSort" required="true" %>

<form id="sortForm" action="${pageContext.request.contextPath}/products" method="GET">
    <select name="sort" onchange="this.form.submit()">
        <option value="price" ${currentSort eq 'price' ? 'selected' : ''}>가격순</option>
        <option value="name" ${currentSort eq 'name' ? 'selected' : ''}>이름순</option>
    </select>
</form>
```

메인 페이지에서 사용:
```jsp
<!-- productPage.jsp -->
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="product" tagdir="/WEB-INF/tags/product" %>

<div class="product-page">
    <!-- 필터 영역 -->
    <product:filter 
        selectedCategory="${param.category}"
        categories="${categoryList}"
    />
    
    <!-- 정렬 영역 -->
    <product:sort 
        currentSort="${param.sort}"
    />
    
    <!-- 상품 목록 영역 -->
    <product:productList 
        products="${filteredProducts}"
    />
</div>
```

서블릿에서의 처리:
```java
@WebServlet("/products")
public class ProductServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // 필터링과 정렬 파라미터 받기
        String category = request.getParameter("category");
        String sortBy = request.getParameter("sort");
        
        // 상품 목록 조회 및 필터링
        List<Product> products = productService.getProducts();
        List<Product> filteredProducts = products;
        
        if (StringUtils.isNotEmpty(category)) {
            filteredProducts = products.stream()
                .filter(p -> p.getCategory().equals(category))
                .collect(Collectors.toList());
        }
        
        // 정렬 적용
        if ("price".equals(sortBy)) {
            filteredProducts.sort(Comparator.comparing(Product::getPrice));
        } else if ("name".equals(sortBy)) {
            filteredProducts.sort(Comparator.comparing(Product::getName));
        }
        
        // 결과를 request에 저장
        request.setAttribute("filteredProducts", filteredProducts);
        request.setAttribute("categoryList", categoryService.getAllCategories());
        
        // JSP로 포워딩
        request.getRequestDispatcher("/WEB-INF/views/productPage.jsp")
               .forward(request, response);
    }
}
```

이렇게 구성하면 다음과 같은 장점이 있습니다:

1. **컴포넌트 재사용성**
   - 각 태그 파일은 독립적으로 재사용 가능
   - 다른 페이지에서도 동일한 필터나 정렬 컴포넌트를 사용 가능

2. **유지보수 용이성**
   - 각 태그 파일의 역할이 명확히 구분됨
   - 필터링 로직 수정시 filter.tag만 수정
   - 정렬 옵션 추가시 sort.tag만 수정

1. **코드 가독성**
   - 메인 페이지(productPage.jsp)가 깔끔해짐
   - 각 컴포넌트의 책임이 명확히 구분됨

# Ajax 통신 방법

1. **Ajax 통신과 화면 표시의 분리 (단일 책임 원칙)**
```jsp
<!-- productList.tag - 순수하게 화면 표시만 담당 -->
<%@ tag body-content="empty" %>
<%@ attribute name="containerId" required="true" %>

<div id="${containerId}" class="product-grid">
    <!-- 데이터가 들어갈 컨테이너 -->
</div>

<!-- 데이터 바인딩을 위한 템플릿 -->
<script id="product-template" type="text/x-handlebars-template">
    {{#each products}}
    <div class="product-card">
        <h3>{{name}}</h3>
        <p>{{price}}원</p>
    </div>
    {{/each}}
</script>

<script>
// 데이터 표시 전용 함수
function renderProducts(data) {
    const template = Handlebars.compile($("#product-template").html());
    $("#${containerId}").html(template({products: data}));
}
</script>
```

```javascript
// productAPI.js - API 통신만 담당
const ProductAPI = {
    getProducts: function(params) {
        return $.ajax({
            url: '/api/products',
            method: 'GET',
            data: params
        });
    },
    
    updateProduct: function(productId, data) {
        return $.ajax({
            url: `/api/products/${productId}`,
            method: 'PUT',
            data: JSON.stringify(data),
            contentType: 'application/json'
        });
    }
};
```

2. **상태 관리와 이벤트 처리의 명확한 분리 (상태 관리 원칙)**
```jsp
<!-- productManager.tag - 상태 관리와 이벤트 처리 담당 -->
<%@ tag body-content="empty" %>

<script>
const ProductManager = {
    // 상태 관리
    state: {
        filters: {},
        sort: 'price',
        products: []
    },
    
    // 이벤트 핸들러
    init: function() {
        this.bindEvents();
        this.loadProducts();
    },
    
    bindEvents: function() {
        $('#filterForm').on('change', 'select', (e) => {
            this.state.filters[e.target.name] = e.target.value;
            this.loadProducts();
        });
        
        $('#sortSelect').on('change', (e) => {
            this.state.sort = e.target.value;
            this.loadProducts();
        });
    },
    
    // 데이터 로딩
    loadProducts: function() {
        const params = {
            ...this.state.filters,
            sort: this.state.sort
        };
        
        ProductAPI.getProducts(params)
            .then(data => {
                this.state.products = data;
                renderProducts(data);
            })
            .catch(error => {
                console.error('Failed to load products:', error);
                alert('상품 로딩에 실패했습니다.');
            });
    }
};

$(document).ready(() => ProductManager.init());
</script>
```

3. **재사용 가능한 컴포넌트 인터페이스 설계**
```jsp
<!-- main.jsp - 컴포넌트 조합 -->
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="component" tagdir="/WEB-INF/tags/component" %>

<div class="product-page">
    <!-- 필터 컴포넌트 -->
    <component:productFilter 
        id="filterForm"
        defaultCategory="${param.category}"
    />
    
    <!-- 정렬 컴포넌트 -->
    <component:productSort 
        id="sortSelect"
        defaultSort="${param.sort}"
    />
    
    <!-- 상품 목록 컴포넌트 -->
    <component:productList 
        containerId="productContainer"
    />
    
    <!-- 상태 관리 컴포넌트 -->
    <component:productManager />
</div>
```

이러한 구조의 장점:

1. **유지보수성**
   - API 호출 로직, 상태 관리, UI 렌더링이 명확히 분리됨
   - 각 부분을 독립적으로 수정/개선 가능

2. **재사용성**
   - 컴포넌트들은 독립적으로 다른 페이지에서도 사용 가능
   - API 통신 모듈은 다른 기능에서도 재사용 가능

3. **확장성**
   - 새로운 필터나 정렬 옵션을 추가하기 쉬움
   - 에러 처리나 로딩 상태 관리를 쉽게 추가할 수 있음

4. **테스트 용이성**
   - API 호출, 상태 관리, UI 렌더링을 각각 독립적으로 테스트 가능
   - Mock 데이터를 사용한 테스트가 용이

이런 방식으로 설계하면, 비동기 데이터 처리와 UI 업데이트가 깔끔하게 분리되어 관리되며, 코드의 재사용성과 유지보수성이 크게 향상됩니다.
