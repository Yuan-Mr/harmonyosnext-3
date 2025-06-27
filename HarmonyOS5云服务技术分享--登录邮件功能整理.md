### HarmonyOS Authentication Service in Action: Comprehensive Guide to Email Login with ArkTS API 12  

#### —— A Developer-Friendly Tutorial  
Hi, HarmonyOS developers! Today, we'll delve into the **email login authentication** feature of HarmonyOS authentication services, implemented with ArkTS API 12. Whether you're new to the HarmonyOS ecosystem or looking to optimize your existing login process, this article will guide you through the complete email authentication workflow with clear code examples and straightforward explanations!  


### I. Preparation Steps  
1. **Enable Authentication Services**  
   Go to the Huawei AGC console to create a project and enable **authentication services**.  

2. **Integrate the SDK**  
   Add the `@hw-agconnect/auth` dependency to your project and configure the `agconnect-services.json` file (refer to the official integration documentation).  


### II. Full Implementation of Email Authentication  
1. **Register an Email Account**  
   **Core logic**: Verify email validity → Send verification code → Create user.  

   ```typescript  
   import auth from '@hw-agconnect/auth';  

   // Step 1: Send verification code  
   auth.requestVerifyCode({  
     action: VerifyCodeAction.REGISTER_LOGIN,  
     verifyCodeType: {  
       email: "user@example.com",  
       kind: "email"  
     },  
     sendInterval: 60 // 60-second interval for resending verification codes  
   }).then(result => {  
     console.log("Verification code sent to email!");  
   }).catch(error => {  
     console.error("Failed to send:", error);  
   });  

   // Step 2: Register user  
   auth.createUser({  
     kind: "email",  
     email: "user@example.com",  
     password: "your_secure_password",  
     verifyCode: "123456" // 6-digit code received by the user  
   }).then(user => {  
     console.log("Registration successful! UID:", user.uid);  
   }).catch(error => {  
     console.error("Registration failed:", error);  
   });  
   ```  

2. **Password Login**  
   ```typescript  
   auth.signIn({  
     credentialInfo: {  
       kind: 'email',  
       email: 'user@example.com',  
       password: 'your_secure_password'  
     }  
   }).then(user => {  
     console.log("Login successful! Current user:", user);  
   }).catch(error => {  
     console.error("Login failed:", error.code, error.message);  
   });  
   ```  

3. **Verification Code Login (Password-Free)**  
   ```typescript  
   // First request the verification code (parameters similar to registration)  
   auth.requestVerifyCode({...});  

   // Login with verification code  
   auth.signIn({  
     credentialInfo: {  
       kind: 'email',  
       email: 'user@example.com',  
       verifyCode: '123456' // Verification code only  
     }  
   }).then(user => {  
     console.log("Verification code login successful!");  
   });  
   ```  

4. **Sensitive Operation Handling**  
   **Re-authentication required for modifying email/password** (user must have logged in within 5 minutes):  

   ```typescript  
   auth.getCurrentUser().then(user => {  
     user.updateEmail({  
       email: "new_email@example.com",  
       verifyCode: "654321", // Verification code received by the new email  
       lang: "zh_CN"  
     }).then(() => {  
       console.log("Email updated successfully!");  
     });  
   });  

   // Change password (requires login)  
   user.updatePassword({  
     password: "new_secure_password",  
     providerType: 'email'  
   });  
   ```  

5. **Password Reset**  
   ```typescript  
   // Step 1: Request verification code for password reset  
   auth.requestVerifyCode({  
     action: VerifyCodeAction.RESET_PASSWORD,  
     verifyCodeType: { email: "user@example.com", kind: "email" }  
   });  

   // Step 2: Reset password  
   auth.resetPassword({  
     kind: 'email',  
     email: 'user@example.com',  
     verifyCode: '112233',  
     password: 'fresh_password' // New password  
   }).then(() => {  
     console.log("Password reset successful!");  
   });  
   ```  


### III. Key Considerations  
- **Security Safeguards**: Sensitive operations (e.g., password changes) require the user to be logged in. Consider adding a secondary confirmation pop-up in the frontend.  
- **Verification Code Management**: The server limits the validity period of verification codes (default: 5 minutes) to prevent brute-force attacks.  
- **Error Handling**: Catch `authError` via `.catch()` to handle common error codes like `ERR_AUTH_INVALID_VERIFY_CODE`.  
- **Multi-Device Synchronization**: After a user modifies their information, other devices should require re-login (implementable by listening for token change events).  


### IV. Extension Suggestions  
- **Account Linking**: Support binding email accounts with WeChat, Huawei Accounts, etc., to enhance user experience.  
- **Risk Control Strategies**: Configure login frequency limits,异地登录提醒 (异地 login alerts), and other rules in the AGC console.  
- **Cloud Function Extensions**: Use authentication triggers to implement scenarios like automatically sending welcome emails upon successful registration.  


### Conclusion  
As a fundamental capability of user systems, HarmonyOS provides a highly encapsulated email authentication solution through ArkTS API 12. We hope this article helps you quickly implement the feature while balancing security and user experience. If you have practical questions, feel free to share them in the comments—let's explore the HarmonyOS ecosystem together!  

Happy Coding! 🚀  
— Your Technical Partner
