---
created: 2024-08-14 08:31
updated: 2024-08-14 08:32
---
```javascript
// 암호화를 위해 CryptoJS 라이브러리를 사용합니다. 
// 실제 구현 시 <script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.1.1/crypto-js.min.js"></script>를 HTML에 추가해야 합니다.

// 암호화 키 (실제 사용 시 서버에서 동적으로 생성하여 제공해야 합니다)
const ENCRYPTION_KEY = "YourSecretKey123!";

// 데이터 암호화 함수
function encrypt(data) {
    return CryptoJS.AES.encrypt(JSON.stringify(data), ENCRYPTION_KEY).toString();
}

// 데이터 복호화 함수
function decrypt(ciphertext) {
    const bytes = CryptoJS.AES.decrypt(ciphertext, ENCRYPTION_KEY);
    return JSON.parse(bytes.toString(CryptoJS.enc.Utf8));
}

// 쿠키 설정 함수
function setCookie(name, value, minutes) {
    const date = new Date();
    date.setTime(date.getTime() + (minutes * 60 * 1000));
    const expires = "; expires=" + date.toUTCString();
    document.cookie = name + "=" + (value || "")  + expires + "; path=/; Secure; SameSite=Strict";
}

// 쿠키 가져오기 함수
function getCookie(name) {
    const nameEQ = name + "=";
    const ca = document.cookie.split(';');
    for(let i = 0; i < ca.length; i++) {
        let c = ca[i];
        while (c.charAt(0) == ' ') c = c.substring(1, c.length);
        if (c.indexOf(nameEQ) == 0) return c.substring(nameEQ.length, c.length);
    }
    return null;
}

// 쿠키 삭제 함수
function eraseCookie(name) {   
    document.cookie = name +'=; Path=/; Expires=Thu, 01 Jan 1970 00:00:01 GMT; Secure; SameSite=Strict';
}

// 사용자 등록 프로세스
async function registerUser(username, password) {
    try {
        // 1. 데이터 암호화
        const encryptedData = encrypt({ username, password });

        // 2. 암호화된 데이터를 쿠키에 저장 (5분 동안 유효)
        setCookie('tempUserData', encryptedData, 5);

        // 3. API 호출
        const response = await fetch('/api/register', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify({ encryptedData }),
        });

        if (!response.ok) {
            throw new Error('Registration failed');
        }

        const result = await response.json();

        // 4. 성공 시 쿠키 삭제
        eraseCookie('tempUserData');

        return result;
    } catch (error) {
        console.error('Registration error:', error);
        // 실패 시에도 쿠키 삭제
        eraseCookie('tempUserData');
        throw error;
    }
}

// 사용 예시
// registerUser('john_doe', 'password123')
//     .then(result => console.log('Registration successful:', result))
//     .catch(error => console.error('Registration failed:', error));
```