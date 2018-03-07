## Big Picture
Simple authentication (authenticated/unauthenticated) and authorization (allowed/denied resp.) via **AWS Cognito User Pool & Federated Identities**.

![Big Picture](https://raw.githubusercontent.com/ttulka/aws-samples/master/cognito-federated-auth/CognitoBigPicture.png)

## Run the Sample

### 1. Create a stack based on the CloudFormation template `user-pool.yml`
- Cognito User Pool
- Cognito User Pool Client
- Cognito Identity Pool
- Roles for the authenticated and unauthenticated user
- Lambda for automatic sign-up confirmation

### 2. Create a stack based on the CloudFormation template `lambda-auth.yml`
- Lambda with an API Gateway for retrieving temporary AIM credentials based on Cognito-user credentials
 
### 3. Create a stack based on the CloudFormation template `lambda-helloworld.yml`
- HelloWorld Lambda with an API Gateway secured by the AWS_IAM Authorization

### 4. Sign-up a new Cognito-user via AWS CLI
```
aws cognito-idp sign-up --client-id CognitoUserPoolClientId --username myUser1 --password mySuper_password1 --user-attributes Name=given_name,Value=Tomas Name=family_name,Value=Tulka Name=email,Value=tomas.tulka@gmail.com
```
- The `CognitoUserPoolClientId` value is listed in the outputs of the stack from the first step. 
 
5. Get AIM credentials
Call `ApiUrl` listed in the outputs of the stack from the second step (eg. `https://xxxxxx.execute-api.eu-central-1.amazonaws.com/prod`) via `POST` method with the payload:
```json
{
	"username": "myUser1",
	"password" : "mySuper_password1"
}
```
The response contains the AIM credentials:
```
HTTP/1.1 200 OK
"{AccessKeyId: ASSSGFGHTSGF4HTGDGNQ,SecretKey: 15/ylGW9DC8jaISF+wXSsMSWoq+iPSdKSN0SeTp9,SessionToken: FgFGSFSpZ2luEJr//////////wEaGGG1DWElRnEyRWwGMGKHAg8ijonfAzJiaXJxOzveTqbSD1fShDsPiFWEEer2W6Ep4uxLvhOZZJ1kJlfHu6N2CT1CJy2ujYGuG9LmSDTNmVOFCcmoQ3Pp2jVvYXqh8LdBX96kRY6sgrD5Gng6RkKp2/Msg693XYheQlIDGuxQkshQoeR+CWyv1koiMDKaOYuPiIFXfwOuX1g67eooPnIfHjAmXbHCHPVOS7p0MsRDkLNenFOOG57ptbLQl5BLY8OfFbRwgWghm3DaMqh/2mgzGyZB7RjH8dxZrzyGp4+RKlfRimtFeKaw94dKkXyXbJJPmiDNTryUIx2bkLDaychny5bYrWM6k6V7ywLYBKniaxoqwQUIkP//////////ARAAGgwwNTcxNzQxNTM5ODEiDEoiR/F0JsxlR7MwPSqVBWNRO7otOoGbcPHk0gyHV9nLl8JiiJGPKVEgfxVVUrILzagDUflX+VnzFPPIB3tjod38EYLDzasNS1//8mu2hklny3YunPbCC4O6ogqJ8USmW9W7saLj50MTTvP3nFDbi3UOUwsxNBcOuovWm8HUzeJ8CLCUvOMWL8Y2lXvuvo6Eb+Os2on/Y79EH/9qLH784fZY9lGcaXSjMjYUW5w77v5VyHthwf+f/0d/PV1JmjtMkc4CGKZloXAWNUj7ZpnfUOrB/guaTVrWRDgvD+/NYTrba+HQwOG9O0WS/1JiYFPGZtNXD0uID/LizjWM1z68L3UNJRw5dUlRWaebkNH0S6JdWD2Sg3fJYkdO7dMiGxDFITwBlG+Csw9C1Y7OeHaKQMpUebWinLhgvFCnBYcCgfi7nSsThFs2g/D6S6I0RMQiuTh0b+OddBM6j15OmcjPyaRcdySwBt7L+Y0TyXMAah7qgh5JfEopuJ45H+TWpG/qvUXy4lzmtjbLwtO24FJq5RD6DzTeMx26l3pm9SJoNWod2YopGBG4FZ73PgOp/JzkZVS5xYECkUmeonwKcH5WqPzBRvpdibeLOuOm3quj5rdMw0PelK7gZvr6dvhW5fWRo+zWGR+SIHiCobT7ft2usa/rOndPtWnWQLBXpemHoyiWdX0Qxr5L0Bq51W60KDdGougTsDT62SmozcLvTTgunx0MWOznZjhOa/LnwtmBhAE5+f96Ge0Wr9HJq9rOmeTusZe1jBBpWftQbHqpmbENWW33/onLc/2yQwICVvJFWVqb6fSDssij4/OSDvT/jh8J77B/LhnVU/DO0mEE3h7vJ1KDZbFi+vPNHwKSDHHaFYyI5rPEWRt/tJjERi6gLgCdRREkuSRGMUwkWT/1AU=,Expiration: Wed Mar 07 15:41:21 UTC 2018}"
```

### 6. Call the *HelloWorld* via [Postman](https://www.getpostman.com)
- The URL `ApiUrl` is listed in the outputs of the stack from the third step.
- Create a request based on the result data from the step five. 
  
![Request via Postman](https://raw.githubusercontent.com/ttulka/aws-samples/master/cognito-federated-auth/postman.png)

Calling the URL without the AIM credentials must result to `403` HTTP Status.