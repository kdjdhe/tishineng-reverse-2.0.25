# GitHub 仓库索引与说明

本文对应本地 `github/` 目录下的上游仓库。`_extracted/` 是本地解压目录，不是 GitHub 仓库。

## 总览

| 本地目录 | GitHub 仓库 | 语言 / 协议 | 用途 | 本报告使用情况 |
|---|---|---|---|---|
| `FuckTSN/` | [pansoul1/FuckTSN](https://github.com/pansoul1/FuckTSN) | Java，无 LICENSE | 体适能 Xposed 模块 | 作为 2.0.25 适配基线；提交 PR #2 |
| `TiShiNengHacker/` | [shaokeyibb/TiShiNengHacker](https://github.com/shaokeyibb/TiShiNengHacker) | Kotlin，无 LICENSE，已归档 | 空壳 Compose 工程 | 仅结构查看，未使用 |
| 相关仓库 | [shaokeyibb/TiShiNengHackerR](https://github.com/shaokeyibb/TiShiNengHackerR) | Kotlin，GPL-3.0，已归档 | `TiShiNengHacker` 重写版 | 未使用其代码 |
| `TiShiNengSdk/` | [dispose0335/TiShiNengSdk](https://github.com/dispose0335/TiShiNengSdk) | Python，无 LICENSE | 清洗过的协议 SDK | 协议结构参考 |
| `Tishineng-Running/` | [dispose0335/TiShiNengRunning](https://github.com/dispose0335/TiShiNengRunning) | Python，GPL-3.0 | 完整跑步工具 | 协议/路径/人脸/环境字段对比 |
| `fitness/` | [ZXWITNESS/fitness](https://github.com/ZXWITNESS/fitness) | Vue / Java，无 LICENSE | 同名体适能前端/后端 | 仅生态参考，与目标 App 无关 |
| `tishineng-analysis/` | [jry21223/tishineng-analysis](https://github.com/jry21223/tishineng-analysis) | Java，无 LICENSE | 2.0.22 逆向报告 | 旧版类名与加固方式参考 |

> `main.exe` 是 PyInstaller 打包产物，不是独立仓库；源码以对应目录中的 `.py` 文件为准。

---

## 1. pansoul1/FuckTSN

- URL：https://github.com/pansoul1/FuckTSN
- 语言：Java
- License：未声明
- 状态：活跃（最近更新 2026-10-17）
- 作用：体适能 Xposed 模块，原支持 2.0.15–2.0.18。
- 本报告使用：
  - 读取 `RootDetectionHook`、`SignatureHook`、`UniversalHook`、`MainHook` 等实现；
  - 以 v2.0.18 源码为基线，完成 2.0.25 类名映射与 `ce/a12/Transform` Hook；
  - 适配分支：`kdjdhe/FuckTSN:feat/tsn-2.0.25`；
  - PR：[pansoul1/FuckTSN#2](https://github.com/pansoul1/FuckTSN/pull/2)。
- 注意：上游未声明 LICENSE，二次分发前需联系作者。

## 2. shaokeyibb/TiShiNengHacker

- URL：https://github.com/shaokeyibb/TiShiNengHacker
- 语言：Kotlin
- License：未声明
- 状态：已归档
- 内容：Android Studio + Jetpack Compose 空壳工程，只有 `MainActivity` 和主题文件，没有实际 Hook 或检测逻辑。
- 相关仓库：[shaokeyibb/TiShiNengHackerR](https://github.com/shaokeyibb/TiShiNengHackerR)，GPL-3.0，已归档，含完整工程结构与 README，但本报告未使用其代码。
- 结论：**不能作为 2.0.25 绕过方案**，仅用于了解早期项目结构。

## 3. dispose0335/TiShiNengSdk

- URL：https://github.com/dispose0335/TiShiNengSdk
- 语言：Python
- License：未声明
- 内容：`SportsSdkBase / SportsSdkPrivate / SportsSdkPublic`、`AesUtils`、`RsaUtils`。
- README 明确说明：真实 API 端点、认证密钥、内部服务名和算法细节已被移除或替换为示例值。
- 用途：参考协议分层、签名/加密调用方式；**不能直接登录真实服务器**。
- 与 `TiShiNengRunning` 的关系：两者协议结构相似，但 `TiShiNengSdk` 是清洗版，缺少真实常量。

## 4. dispose0335/TiShiNengRunning

- URL：https://github.com/dispose0335/TiShiNengRunning
- 语言：Python
- License：GPL-3.0
- 状态：活跃（最近更新 2026-10-21）
- 内容：完整跑步工具：
  - 公版/私版登录与 token 刷新；
  - AES/RSA/MD5 签名与加密；
  - 跑步设置、轨迹生成、步数分桶、提交、复核；
  - 人脸下载/上传/中途验证点；
  - 路径爬取、有效次数/里程统计。
- 本报告使用：
  - 分析其协议实现、环境伪造字段 `oppo/safe`、跑步字段 `d/f/m/h/isValid/remarkId/environment`；
  - 对比 2.0.25 的新登录 `rsa-public-key` + `app/login`，得出“协议底座兼容但登录未适配”的结论。
- 本地 fork：
  - `TiShiNengRunning-main`：上游主版本；
  - `TiShiNengRunning-Test`：测试副本；
  - `Tishineng-Demo`：加入必经点注入 + GPS 噪声；
  - `Tishineng-V1`：加入路径合成、path_only 账号、路径池扩容。
- 注意：GPL-3.0，分发衍生作品需遵循 GPL。

## 5. ZXWITNESS/fitness

- URL：https://github.com/ZXWITNESS/fitness
- 语言：Vue / Java
- License：未声明
- 内容：名为“体适能”的前端/小程序（`mp-weixin`）与 Java 后端（`childrenfitness`）。
- 与目标 App 的关系：**不是 `com.bxkj.student`**，包名、接口、业务均不同。
- 本报告使用：仅作为生态参考，未用于 2.0.25 分析。

## 6. jry21223/tishineng-analysis

- URL：https://github.com/jry21223/tishineng-analysis
- 语言：Java
- License：未声明
- 内容：体适能 2.0.22 逆向分析报告，包含：
  - 360 加固；
  - `com.bxkj.student.v2.common.utils.safe.*` 检测类；
  - `Transform` / `x.b` 签名体系；
  - 设备指纹 `uniqueCode` / `model`；
  - 反作弊机制概述。
- 本报告使用：
  - 作为旧版类名映射依据（`safe.c/d/f/g/h`）；
  - 与 2.0.25 的 `ce/cj4/tz0/rp1/b15/yj0` 做对照；
  - 说明 FuckTSN 2.0.18 为什么在 2.0.25 上失效。
- 注意：其结论针对 2.0.22，不能直接套用到 2.0.25。

---

## 许可证与合规

| 仓库 | License | 说明 |
|---|---|---|
| dispose0335/TiShiNengRunning | GPL-3.0 | 衍生分发需遵循 GPL |
| shaokeyibb/TiShiNengHackerR | GPL-3.0 | 衍生分发需遵循 GPL |
| pansoul1/FuckTSN | 未声明 | 使用/分发前联系作者 |
| dispose0335/TiShiNengSdk | 未声明 | 使用/分发前联系作者 |
| ZXWITNESS/fitness | 未声明 | 使用/分发前联系作者 |
| jry21223/tishineng-analysis | 未声明 | 使用/分发前联系作者 |

本报告仅用于学习交流和安全研究，不构成对上述仓库的再授权。
