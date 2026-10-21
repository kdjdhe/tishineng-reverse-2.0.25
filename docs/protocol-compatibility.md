# Tishineng-Running 与 2.0.25 协议兼容性对比

## 结论

Tishineng-Running 属于 **2.0.23 时代实现**：

- ✅ 加密常量、RSA 公钥、`appSignHash`、环境字段、旧路由兼容；
- ❌ 版本号仍是 `2.0.23 / 20230`；
- ❌ 登录仍是旧 OAuth + AES，2.0.25 已改为 RSA + `/app/login`；
- ❌ 旧 OAuth 现在要求验证码，项目没有处理；
- ❌ 新版 `/app/*` 接口未实现。

因此它**不是 2.0.25 开箱即用版本**。

## 对得上的部分

| 项目 | 2.0.25 实际情况 | 结论 |
|---|---|---|
| `appSecret` | `e8167ef026cbc5e456ab837d9d6d9254` 仍在 DEX | 一致 |
| 公版 `appId` | `c9292ee89d2f49492f983f5931af0d09` 仍在 DEX | 一致 |
| 密码 AES key | `thanks,pig4cloud` 仍在 DEX | 存在（旧登录） |
| RSA 公钥 | 项目中的公钥前缀在 `libtsnsecret.so` 中找到 | 一致 |
| `appSignHash` | 用 2.0.25 证书计算 = `d2tnIyqximO/L8Y4MzfRELa1hSAtSRxzmvXlcOCzRyk=` | 完全一致 |
| 43 条旧路由 | 全部在 `libtsnsecret.so` 中找到 | 老接口仍在 |
| `oppo/safe/checksum` | 2.0.25 DEX 中存在 | 一致 |
| `d/f/m/h/isValid/remarkId/environment` | 2.0.25 DEX 中存在 | 一致 |
| 基础学校接口 | `POST m.boxkj.com/app/province/findAllProvince` 正常 | 可用 |

## 没适配的部分

### 1. 登录方式

2.0.25：

```text
LoginActivity
  → LoginViewModel (xq2)
  → LoginRepository (tq2)
  → GET rsa-public-key
  → 原生 a12.d/r/e（RSA 加密密码）
  → POST app/login
     username=...
     password="RSA:" + Base64(RSA(password))
     grant_type=password
     type=app
     appType=stuApp
```

Tishineng-Running：

```text
AES-ECB(password, key/iv="thanks,pig4cloud")
  → POST auth/oauth/token
  → Basic base64(schoolCode + ":pig")
```

### 2. 验证码

实测旧 OAuth：

```http
POST https://a.tsnkj.com/auth/oauth/token
HTTP 428
{"code":1,"msg":"验证码不能为空","data":null}
```

附加 `captcha`、`code`、`verifyCode` 仍返回同样错误。项目没有验证码处理代码。

### 3. 新版接口

2.0.25 的 `kc` Retrofit 接口包含：

- `app/login`
- `app/getSchoolInfo`
- `app/getUserInfo`
- `app/exerciseSettingV2`
- `app/addExerciseRecord`
- `app/getStartTime`
- `app/checkFaceResult`
- `app/faceCheck`
- `app/faceDetect`
- `app/faceCollect`
- `app/getExerciseRecord`
- `app/getExerciseStatistics`
- `app/autoLogin`

项目使用：

- `auth/oauth/token`
- `exercise/exerciseSetting/2a36d143/getSetting`
- `exercise/exerciseRecord/2a36d143/addExerciseRecord`
- `upms/basUser/...`

老路由仍存在于 native，但 App 当前主流程已经走 `/app/*` 新接口。

### 4. 版本与域名

项目：

```python
self.versionName = '2.0.23'
self.version = '20230'
self.cloudUrl = 'http://a.sxstczx.com'
```

2.0.25：

- versionCode 20250；
- `/app/login` 实测在 `m.boxkj.com` 可用（假账号返回 `601 认证失败`）；
- `a.tsnkj.com` 上的 `/app/login`、`/rsa-public-key` 返回 404。

## 适配清单

1. `versionName = '2.0.25'`，`version = '20250'`。
2. 公版登录改为 `GET /rsa-public-key` + RSA 密码 + `POST /app/login`。
3. 处理验证码/风控（如果服务端返回 428）。
4. 增加 `/app/getSchoolInfo`、`/app/exerciseSettingV2`、`/app/addExerciseRecord`、`/app/getStartTime`、`/app/checkFaceResult` 等新接口。
5. 默认 `cloudUrl` 改为实际学校地址（`m.boxkj.com` 或 `lan_url`）。
6. 用测试账号验证登录、设置、提交、人脸四条链路。

## 测试说明

- 以上探测仅使用假账号（`nonexistent_test_user`）和不存在的密码，不涉及真实账号或 Token。
- 旧 OAuth 的 428、`app/login` 的 601、基础学校接口的成功返回，均为实际请求结果。
