# 虚魅灵影媒体混淆器

> 单文件、离线运行的图片 / GIF / 视频像素级混淆工具。混淆与还原必须使用同一密码。

---

## 一、工具名称

**虚魅灵影媒体混淆器**（Ghost Media Confuser），主程序为单文件 `虚魅灵影媒体混淆器.html`。

## 二、简介

本工具是一个**纯前端、零依赖、可离线**的媒体混淆器：把整个 HTML 文件双击即可运行，全部逻辑内联（含内联的第三方编解码库与内嵌 WASM），无任何外部资源与网络请求。

它基于**广义希尔伯特（Gilbert）空间填充曲线驱动的像素位置置换**，配合 `PBKDF2-HMAC-SHA256` 密钥派生，对**静态图片、GIF 动图、视频**三类媒体做「轻度混淆」；在同一密码下可按完全对称的逆操作还原原图/原片。默认只搬运像素位置、不改写任何颜色值（可选叠加负片，此时直方图被翻转），产物**仅能由本工具还原**。

需要特别注意定位：本工具属于**轻度混淆而非加密**——不做负片时直方图特征完全不变、密钥空间有限，不适用于高安全场景；遗忘密码后无法还原。

## 三、功能特性

**三大处理面板，混淆与还原对称**

| 面板 | 输入 | 输出 | 特色选项 |
| --- | --- | --- | --- |
| 静态图片混淆 | PNG / JPG / WebP / AVIF / BMP（多选、拖拽批量） | 自动 / PNG / JPEG / WebP / AVIF / BMP | 输出格式与保存质量、负片 |
| GIF 动图混淆 | .gif（多选批量） | 混淆 / 还原后的 GIF | 负片 |
| 视频混淆 | MP4 / WebM / MOV / MKV 等（多选批量，由浏览器解码） | 统一封装为 MP4 | 逐帧置换、线程并行、分辨率/帧率/码率自定义、音频策略与音频格式、编码格式、负片、密码参与帧间跳动、彩虹跑马灯 |

**通用能力**

- **单一密码体系**：任意长度、任意字符（含中文）整串参与派生，可留空（退化为默认打乱）；派生前会**剔除密码中的全部空白字符**（空格 / 制表符 / 换行），因此该类字符不影响结果；附随机混合密码生成（位数 12–128）、强度实时评估、一键复制 / 清空。
- **批量处理**：三个面板均支持多选 / 拖拽批量，逐项处理并单独记录成功失败。
- **处理与结果总览**：本次会话全部产物集中汇总，每行显示缩略图、类型标签、保存状态、密码标签、原文件名与可编辑的保存名；支持勾选后逐个保存、打包为 ZIP（文件名含时间）、清除；点行内「灯箱」按钮弹出预览（尺寸与音轨信息显示在灯箱标题）。
- **负片（自逆变换）**：RGB 逐通道取反、alpha 不变，混淆/还原两端对称勾选即可恢复。
- **视频参数细分**：线程数（自动 / 1 / 2 / 4 / 8）、编码实现（自动 / 硬件 / 软件）、视频编码器（H.264 / H.265 / VP9 / AV1，不可用自动回退）、分辨率 / 帧率 / 码率预设与自定义、音频选项（反转随机重排 / 保留原音轨 / 关闭）与音频格式（全频 AAC / 窄频电话带 AAC / PCM）。
- **AVIF 真实编码**：内嵌 libavif WASM 编码器，绕开浏览器 `canvas.toBlob('image/avif')` 恒回退 PNG 的限制。
- **界面体验**：中文 / English / 日本語 三语切换、日间 / 夜间主题、内置「开发者手册」弹窗（三语实现说明）。

## 四、快速开始

1. **打开工具**：直接双击 `虚魅灵影媒体混淆器.html`（Chrome / Edge 等现代浏览器；无需安装、无需联网）。视频功能依赖 WebCodecs，建议使用较新版本的 Chrome / Edge。
2. **选择文件**：点击或拖拽文件到对应面板的放置区；可按住 Ctrl / Shift 多选，或一次拖入多个文件。
3. **输入密码**：输入任意密码（可留空）。**请牢记密码**——混淆与还原必须使用完全相同的密码（空白字符会被剔除后比较），任一其余字符不同都无法还原。
4. **选择处理方式**：在「处理方式」中手动选择 **混淆** 或 **还原**（工具不会自动识别混淆标记，还原全靠手动选择）。
5. **开始处理**：点击「开始处理」，下方日志区显示逐项进度；视频面板另有进度条与耗时预估，可随时「取消」（中断当前及后续文件）。
6. **保存结果**：切换到「处理与结果总览」，勾选条目后「保存选中」或「打包保存选中」。

**还原注意事项**

- 图片 / GIF 还原必须使用与混淆时相同的密码；视频还原还需**手动对称设置**混淆时的状态——「密码参与帧间跳动」「彩虹跑马灯」「负片」以及音频选项与音频格式（这些状态一律不写入文件）。帧网格与置换段数不属于可设置项：置换段数两端固定为 8，帧网格由输出帧率推导，两端算法一致。
- 视频还原时，分辨率 / 帧率 / 码率会锁定为原混淆参数（界面禁用修改），因为逐帧逆置换需与混淆时完全一致。
- 建议使用 ≥12 位的混合字符密码（大小写字母 + 数字 + 符号）以提升可抗穷举性。

## 五、工具结构

**目录结构**

```
混淆器/
├── README.md                本说明文档
└── 虚魅灵影媒体混淆器.html    主程序（单文件，9528 行，含全部内联逻辑与内嵌引擎）
```

> 本目录只包含上述两个文件；主程序不依赖任何同目录资源，单独拷走亦可运行。

**HTML 内部结构**（单文件，按出现顺序）

| 区块 | 大致行范围 | 职责 |
| --- | --- | --- |
| `<style>` | 7–485 | 主题变量（日/夜各 3 个同色系主色 + 中性色）、布局、组件样式 |
| 页面结构 | 486–973 | 顶栏（语言 / 主题 / 板块切换）、GIF 面板、静态图片面板、视频面板、处理与结果总览、页脚（974 行为空行） |
| 开发者手册弹窗 | 976–1311 | 三语（`.manual-lang[data-lang]`）实现原理 / 流程 / 调试指引 |
| 灯箱 | 1313–1322 | 结果预览（图片 / GIF / 视频）与下载 |
| omggif | 1324–2137 | GIF 解码（第三方） |
| gifenc | 2139–2970 | GIF 量化 / 编码（第三方） |
| 密码派生 KDF + 媒体参数派生 | 2972–3285 | `PBKDF2-HMAC-SHA256`（RFC 2898/2104，纯 32 位整数实现）+ 同密码缓存 + `__dfqParamsImage / __dfqParamsVideo` + `__dfqSegOffsets` / `__dfqSegJuggle` / `__dfqMarqueeParams` |
| GifConfuser（`GC`） | 3287–3728 | `confuseGif / restoreGif`、`encryptPixels / decryptPixels`、`negatePixels`、`padPixels / cropPixels` |
| 图片置换核心 | 3730–3973 | `fjGilbert2d` 曲线生成、`fileToImageData`、`encryptImageData / decryptImageData`、`addPaddingToImageData / cropImageData`；BMP 编码 `encodeBmp` 与 AVIF WASM 调用 `encodeAvifWasm` 不在此区间，位于下面的面板区块（3978–5609）内 |
| JSZip | 3975–3977 | 结果打包为 ZIP |
| 三语本地化 + 面板交互 + 结果总览 | 3978–5609 | `I18N` 词典与基础设施、GIF / 图片面板交互、`processFile`、`ResHub`（结果总览）、灯箱控制 |
| mp4box.js | 5612–5618 | MP4 解析 / 封装（第三方） |
| 视频混淆 / 还原 | 5620–9299 | 抽帧、Worker 并行置换、WebCodecs 编解码、音频处理、mp4box 合成 |
| 手册弹窗控制 | 9301–9315 | 打开 / 关闭开发者手册 |
| 密码强度评估 | 9317–9485 | 字符集规模估算、随机混合密码、一键复制 / 清空 |
| AVIF WASM | 9487–9523 | `type="module"`，内联 base64 的 libavif 编码器（@jsquash/avif 2.1.0，Apache-2.0），位于文件末尾（其后 9524–9528 为空白与 `</body></html>`） |

**内嵌引擎链**（按文件顺序）：`omggif`（GIF 解码）→ `gifenc`（GIF 编码）→ 密钥派生与分段偏移核心 → `GifConfuser`（GIF 混淆）→ 图片混淆核心（`fjGilbert2d` 曲线置换）→ `JSZip` → 三语本地化 / 面板 / 结果总览 → `mp4box.js`（MP4 解析与封装）→ 视频面板（置换 Worker + WebCodecs `VideoEncoder` / `VideoDecoder`）→ 手册弹窗控制 → 密码强度与随机密码 → `libavif WASM`（AVIF 编码，最后一个脚本块）。

## 六、核心实现原理

### 6.1 曲线与置换

1. **生成曲线**：`fjGilbert2d(width, height)` 生成一条遍历矩形区域内全部像素的广义 Hilbert 曲线，得到 `curve[i]`（曲线上第 i 个像素的一维坐标）。
2. **密钥派生**：整串密码（先剔除全部空白字符）经 `PBKDF2-HMAC-SHA256(pw, 'XUMENG.SHUFFLE.KDF.SALT', 210000, 32B)` 摊平为 256 位密钥，按**大端序**拆成 8 个 32bit 词 `words[0..7]`（2^256 状态空间）；同密码结果缓存（`__dfqDeriveKey` / `__dfqKeyToWords`）。
3. **分段偏移**：`__dfqSegOffsets(total, step, words, segs, frameNo, jump)` 把曲线**等分为 `segs` 段**（有密码时图片 / GIF / 视频一律固定 8 段，无密码时 1 段），每段由 `words[k%8]` 与 `words[(k+4)%8]` 异或 + 乘法混合出种子，再叠**黄金比例种子** `round((√5 − 1) / 2 × total)` 与帧号混入，派生该段偏移 `s_k`（`s_k = 0` 时兜底非零，并对 2^k 自相似边界做确定性外偏修正）。偏移是**确定性纯函数**，混淆与还原两侧共用同一组 `s_k`，天然对称。
4. **循环平移**：`__dfqSegJuggle` 对每段按各自偏移做一次循环平移（juggling 游标法，零分配，段内按 `gcd(s_k, len_k)` 分解为等长循环）；**混淆前进、还原后退，互为逆操作**。
5. **加宽高**（图片与 GIF 共用）：混淆前按密钥派生的 `v = (words[2] >>> 16) % 10`（右侧列）、`h = (words[3] >>> 16) % 10`（底部行）扩充画布（图片 `addPaddingToImageData`，GIF `padPixels`）；还原时先裁掉边距再逆置换。无密码时 `v = h = 0`，不加宽高。
6. **负片（可选）**：对 RGB 三通道逐字节取反（`255 − val`），**alpha 不变**。该变换**自逆**且与位置置换**可交换**，因此两端对称勾选即可正确恢复。

### 6.2 参数派生规则

```
key    = PBKDF2-HMAC-SHA256(pw, 'XUMENG.SHUFFLE.KDF.SALT', 210000, 32B)   // 同密码缓存
words  = key 按大端序拆成 8 个 32bit 词
图片/GIF: step = 1 + (words[6] % 99);   v = (words[2]>>>16)%10;  h = (words[3]>>>16)%10;  segs = 8
视频:     step = 1 + (words[6] % 65536);                                               segs = 8
每段 k:   w0 = words[k%8];  w1 = words[(k+4)%8]
          seed = w0 ^ (w1 + 0x9E3779B9 + k*0x85EBCA6B)
          mix  = w1 ^ (w0 + f*(0x27D4EB2F+k))        // f = 帧号；跳动关闭或无密码时 f = 0
          s_k  = ((golden + seed%4096 + mix%4096) * (step + seed%64)) % len_k
          golden = round((√5−1)/2 × total);  s_k = 0 时兜底非零；2^k 边界外偏
无密码:   words = null → segs = 1 → 偏移即纯黄金比例种子（默认打乱，s = golden % total，0 则取 1）
```

**帧号混入**：静态图片帧号恒 0；GIF 按帧序 `idx` 混入；视频按**输出帧序** `job.i` 即时混入——同一密钥下相邻帧的 8 段偏移全部不同，帧间结构相关性被逐帧打散，两端帧号一一对应。「密码参与帧间跳动」开关（默认关闭）关闭时帧号混入项归零，各帧退化为恒定映射（即帧 0 的映射），还原仍须同一密码；该开关**不写入文件**，两端需手动对称设置。

### 6.3 三类媒体处理流程

- **静态图片**（输入 PNG / JPG / WebP / AVIF / BMP，支持多选、拖拽批量）
  - **输入与校验**：文件对话框限定 `accept=".png,.jpg,.jpeg,.webp,.avif,.bmp,image/png,image/jpeg,image/webp,image/avif,image/bmp"`（多选）。点击处理后先按扩展名 / MIME 类型剔除 GIF 与视频——命中 GIF 时提示「静态图混淆不支持 GIF 文件，请使用『GIF 混淆』板块」（`dyn.imgGifReject`）、命中视频时提示「静态图混淆不支持视频文件…」（`dyn.imgVideoReject`），不会静默跳过。面板不提供剪贴板粘贴入口。
  - **输出格式与编码实现**：输出格式 `#imgFormat` 六档——自动（按输入格式，默认）/ PNG（无损）/ JPEG（有损）/ WebP / AVIF / BMP（无损）。自动档读取文件前 **16 字节**交 `detectImgFormat` 按文件头识别：PNG `89 50 4E 47`、JPEG `FF D8 FF`、BMP `42 4D`、WebP `RIFF….WEBP`、AVIF（`ftyp` 且 brand = `avif` / `avis`）；命中即沿用该格式，其余回退 PNG。编码实现：PNG / JPEG 走原生 `canvas.toBlob`；BMP 走内置 `encodeBmp`（无损、不看质量）；WebP 优先原生 canvas，返回类型不符时回退 PNG；**AVIF 走内嵌 libavif WASM（`@jsquash/avif` 2.1.0，Apache-2.0，`type="module"` base64 内联）**，编码器不可用时回退 PNG。`encodeImage(imageData, fmt, quality)` 按**解析后的** `fmt === 'avif'` 判定，因此「自动」命中 AVIF 输入同样走 WASM。有损质量取 `#imgQuality` / `#imgQualityNum`（1–100，默认 80，`getImgQ` 归一为 0.01–1.00），对 **JPEG / WebP / AVIF** 生效（PNG、BMP 为无损格式不受影响）；选 BMP 时隐藏「保存质量」行。
  - **混淆**（`processFile(file, true)`）：读取图片（任意格式）→ 转 `ImageData` →（可选负片：置换前 RGB 取反 `encryptImageData(data, negate)`）→ 置换（`fjGilbert2d` 曲线 + 8 段密钥切片偏移，**帧号恒 0**）→ **加宽高**（右侧补 v 列 + 底部补 h 行，复制边缘像素填充）→ `encodeImage` 按所选格式编码输出。
  - **还原**（`processFile(file, false)`）：读取图片 → **先做尺寸校验**（`width ≤ v` 或 `height ≤ h` 时抛 `dyn.sizeTooSmall`「图片尺寸过小…」，提示密码可能与混淆时不一致）→ 裁掉右侧 v 列与底部 h 行（`cropImageData`）→（勾选负片时先 RGB 取反 `decryptImageData(cropped, negate)`）→ 逆置换（同曲线、同 8 段偏移，各段反向平移）→ `encodeImage` 按所选格式输出。
  - **关键参数**：加宽高的 v / h 由**密码派生**（`v = (words[2] >>> 16) % 10`、`h = (words[3] >>> 16) % 10`），故还原端裁边量同样由密码决定；未勾选负片时只搬运像素位置、**不改写任何颜色值**。
  - **命名**（`toNewName(name, enc, hasPw, ext)`）：混淆为 `基名 + -已混淆加密` 或 `基名 + -已混淆`（按是否输入密码）+ 原扩展名；还原为 `基名 + -已还原` + 原扩展名（后缀取自 `dyn.suffixImgEncPw` / `dyn.suffixImgEnc` / `dyn.suffixImgDec`）。

- **GIF 动图**（.gif，支持多选批量）
  - **输入与校验**：文件对话框限定 `accept=".gif,image/gif"`（多选）；批处理时逐个复核，非 `.gif` 且 `type ≠ image/gif` 时抛 `dyn.notGif2`「不是 GIF 文件」。
  - **混淆**（`GC.confuseGif(bytes, password, negate)`）：`parsePassword` 派生参数（**无密码时 `FALLBACK_STEP = 3`、`v = h = 0`、`words = null`**；有密码走 `__dfqParamsImage`）→ `decodeGif` 逐帧解码为 RGBA 并记录每帧延时 `delayMs` 与循环次数 `loopCount` →（可选负片：置换前逐帧 RGB 取反 `negatePixels`）→ **逐帧置换**（`encryptPixels(src, g.width, g.height, step, words, idx)`，`gilbert2d` 曲线 + `SEG_COUNT = 8` 段偏移，**帧号按帧序 `idx` 混入**，故各帧映射互不相同）→ **逐帧加宽高**（`padPixels` 右侧补 v 列 + 底部补 h 行，画布扩为 `(g.width + v) × (g.height + h)`）→ `encodeFramesToGif(ew, eh, frames, { loopCount })` 逐帧量化重新生成调色板并编码（gifenc，`dispose = 2` 每帧全幅绘制、写回原延时与循环次数）。
  - **还原**（`GC.restoreGif(bytes, password, negate)`）：`decodeGif` → 计算 `ow = g.width − v`、`oh = g.height − h`，`ow ≤ 0` 或 `oh ≤ 0` 时抛「裁切尺寸无效：请确认密码与混淆时一致」→ 逐帧裁掉 v / h 边距（`cropPixels`）→（勾选负片时先取反）→ 逐帧逆置换（帧号按同一帧序取，与混淆端一一对应）→ `encodeFramesToGif(ow, oh, …)` 重新量化编码输出。
  - **关键参数**：混淆 / 还原由界面按钮**手动选择**（`gifModeEnc` / `gifModeDec`），**不自动识别混淆标记**（还原端不读取任何混淆标记）；负片为可选；帧延时与循环次数原样保留。
  - **命名**：`原基名.replace(/\.gif$/i, '')` + `-已混淆加密.gif` / `-已混淆.gif`（按是否输入密码）/ `-已还原.gif`（后缀自带 `.gif`，取自 `dyn.suffixEncPw` / `dyn.suffixEnc` / `dyn.suffixRestored`）。
  - **要点**：GIF 与静态图片共用同一套曲线与段偏移派生，**同样使用加宽高**（`v / h` 由 `parsePassword` 派生，界面文案「帧像素打乱 + 加宽高」与代码一致）。另：代码保留 `GC.addGifComment`（可写 GIF 注释）且编码器支持 `comment` 入参，但 `confuseGif` / `restoreGif` 均**不传注释**，故产物**不写入任何混淆标记**。

- **视频**（MP4 / WebM / MOV / MKV 等，支持多选批量）
  - **输入与输出**：文件对话框限定 `accept="video/*"`（多选）；界面提示支持 `.mp4 .webm .mov .mkv .avi` 等；输出**统一封装为 MP4**（H.264 / HEVC / VP9 / AV1 均封入 MP4 容器）。
  - **混淆**（`process()` 批量入口 → `runOne(file, idx, total)` 单文件流水线）：解码视频 → 探测元数据（分辨率 / 帧率 / 时长；帧率优先 `probeFpsMeta` 按样本统计，失败回退 rVFC）→ 按所选参数缩放（`#vidRes` 默认「保持原视频」，可选**高度档** 8K / 4K / 1080p / 720p / 480p / 360p——宽度按源宽高比自动换算并取偶；选「自定义」则显示**宽 / 高两个输入框** `#vidResCustom` / `#vidResCustomH` 与「等比换算」勾选 `#vidResLock`（仅在只填一栏时可勾选，自动换算另一栏），宽度框也接受 `1920x1080` 形式，显式填入的高度优先；允许 64–7680，奇数向下取偶）→ 抽帧（**抽帧调度与两套路径详见 6.4 节**；输出帧时刻 `wantTs = (fi + 0.5) / outFps` 二分取「PTS ≤ wantTs 的最后一帧」）→ **N 路 Worker 并行逐帧置换**（主线程 `initFrame(w, h, words, step, segCount, jump, mqOn, mqx, mqy, mqv0, mqMul, mqPref, mqOffSeed, mqOffTbl, mqFps)` 一次性广播，Worker 内 `permuteFrame(data, mode, neg, frameNo)` 每帧即时派生 8 段偏移，负片以 `^0x00FFFFFF` 合并进每次写回；**帧号按输出帧序混入**，关闭「密码参与帧间跳动」时按帧 0 恒定映射）→（可选「彩虹跑马灯」：置换写回后叠加密钥派生的帧级色相偏移，见下条）→ `VideoEncoder` 编码（帧率 `#vidFps`、码率 `#vidBitrate`）→ 音轨处理（见后）→ `mp4box` 合成 MP4。
  - **彩虹跑马灯（可选）**：`#vidMarquee` 勾选后由 `__dfqMarqueeParams(words)` 从密钥派生跑马灯参数——周期表 `P = 512`、`x / y` 节奏 ∈ [1, 5] 秒（分别取 `words[0] / words[1]`）、基准速度 `v0` ∈ [30, 199] px/s（取 `words[2]`）、512 个速度倍率 ∈ [3.000, 5.000]（首尾两个固定为 3.000 / 5.000，中间 3001–4999 按 `words[3]` 派生的种子洗牌排列）、512 个色相偏移 ∈ [−30°, +30°]（`offSeed` 取 `words[4]`）；无密码时退化为匀速平移、无色相微调。参数随 `initFrame` 的 `mqOn…mqFps` 字段广播，Worker 侧按「**混淆＝先置换后色偏**、**还原＝先反色偏再逆置换**」的固定顺序执行，因此混淆产物在播放时即呈现七色跑马灯。该状态**不写入文件**，两端必须勾选一致；开启会增加每帧运算量（界面提示有说明）。
  - **还原**：只解析容器信息（视频轨 / 音频轨是否存在、帧率、分辨率），**不读任何自定义元数据 box** → 锁定分辨率 / 帧率 / 码率为原混淆参数（界面禁用修改，`#vidParamHint` 提示）→ 逐帧逆置换（同一抽帧路径与同一输出帧网格，帧号一一对应；段数固定 8、帧网格由输出帧率推导）→（对称勾选负片 / 跑马灯时一并还原）→ 音轨按同一选项与音频格式处理 → 重新合成 MP4。
  - **关键参数**：线程数 `#vidThreads`（自动（`min(8, max(1, 逻辑核心数 − 1))`，即按设备核心数减一、上限 8 路并行 Worker）/ 1 / 2 / 4 / 8，显式值夹到 1–8）；编码实现 `#vidEncImpl`（自动 / 硬件 / 软件；映射为 `hardwareAcceleration` + `latencyMode`——**`latencyMode` 只看所选档位**：硬件档 `prefer-hardware` + `'realtime'`，自动档与软件档为 `'quality'`；分辨率 ≥ 4K（约 830 万像素）时自动档会先试 `prefer-hardware`，但 `latencyMode` 仍是 `'quality'`；候选不可用时去掉 `hardwareAcceleration` 重试，**仅在显式指定硬件 / 软件时才告警**，自动档静默降级）；视频编码器 `#vidCodec`（H.264 默认 / AV1 / H.265 / VP9）由 `pickVideoCodec` 按候选链探测——`videoCodecChain` 把**用户所选置首**，其余按 **AV1 → H.264 → HEVC → VP9** 排列（因下拉默认选中 H.264，默认导出实际按 **H.264 → AV1 → HEVC → VP9** 探测）；H.264 / AV1 候选等级按**输出分辨率 + 输出帧率**推导（`avc1.6400xx / 4D00xx / 42E0xx`、`av01.0.xxM.08`）；**VP9 恒定垫底**（Chrome / Edge 4K 下仅软件实现，是导出卡顿 / 卡死主因）；全部候选探测失败抛 `dyn.vidNoCodec`「无可用编码器」并列出已试编码串。帧率档 `#vidFps`（保持原视频 / 60 / 30 / 25 / 24 / 自定义**正整数 1–2000**；自定义值仅在**低于源帧率**时生效，高于源帧率不会复制新帧）；码率 `#vidBitrate`（按原视频估算 / 自定义 Mbps，支持小数，上限 500 Mbps；最终夹到 200 kbps–500 Mbps，分辨率与帧率解析失败分别抛 `dyn.vidResRange` / `dyn.vidFpsRange` / `dyn.vidBrRange`）；负片、密码参与帧间跳动、彩虹跑马灯；音频选项与音频格式（见下条）。
  - **命名**：混淆为 `基名 + -视频已混淆加密` 或 `基名 + -视频已混淆`（按是否输入密码）、还原为 `基名 + -视频已还原`，扩展名统一 `.mp4`（`dyn.vidSuffixEncPw` / `dyn.vidSuffixEnc` / `dyn.vidSuffixDec`）。
  - **要点**：成片时长恒等于 `帧数 / 输出帧率`，与原视频一致；**所有影响还原的状态**（帧间跳动、跑马灯、负片，以及音频选项与音频格式）**一律不写入文件**，还原端须按混淆时的勾选状态手动对称设置；帧网格与置换段数由算法固定推导（段数恒 8、帧网格 = `round(时长 × 输出帧率)`），不需要也无法手动设置。

- **音轨混淆与还原（视频内音频）**：音频独立于画面，由 `vidAudio` 选项决定，三种模式互不相干。
  - **`keep` 原样保留**：`extractAudioTrack` 解析音轨样本后由封装器**字节级透拷贝**——不解码、不重编码、不做格式转换，**音轨内容完全不参与混淆 / 还原**，ac3 / flac / opus / aac 等任意原格式均可直出。
  - **`scramble` 反转随机重排（默认）**：解码音轨为 PCM（`AudioDecoder`）→ 按所选「音频格式」处理（**全频 AAC** 不做带限、全频输出；**窄频电话带 AAC** 做零相位带限 `300–3400 Hz`；**PCM** 不做带限、不重编码）→ 按段长档位表 `AUD_SEG_LEN_TBL = [511, 1021, 2047, 4093, 8191, 1877, 443]`（`audioSegLenAuto`：**只依赖容器声明的采样总数 N，与采样率无关**；常规长度默认 511 样本 ≈ 48 kHz 下 10.6 ms，短音轨依次下退，443 为兜底档）**把整条音轨切分为等长段**（全部参与、末段不补零）→ **段内波形时间反转** → **密码派生的 Fisher-Yates 置换对全部段随机重排** → 写回：默认 **AAC-LC**（`scrambleAudioTrackAac`：`encodePcmToAacPreferred` + `audioBitrateLadder` 取平台实际接受的最高码率档——每声道 256 kbps、整体 512 kbps 为上限，数据按 1024 帧边界对齐 / 补零，使容器声明样本数 = 重排样本数 N）；选 PCM 档时由 `scrambleAudioTrack` 以 **`in24`**（24bit 大端交错 PCM，强制 ≥ 48 kHz / ≥ 24 bit，必要时真正重采样）无损写回、不经 AAC 编码。样本不足两段时抛 `dyn.vidAudioScrFloor`，由调用处捕获并**回退为原样保留音轨**。
  - **`off` 关闭**：输出不含音轨。
  - **还原**：只解析容器信息（音轨是否存在、声明的采样总数），**不读任何自定义元数据 box**。`keep` 原样保留；`scramble` 以**容器声明的采样总数为锚点**，由结构自恢复 `audioSegNRecover`（稳健评分 + 候选段长档位 + 1024 样本网格搜索）同时恢复**真实重排样本数 N 与解码前导偏移**，再用**同一段长档位表**与**同一密码**逆重排（逐段还原波形方向 + 按逆排列放回原位），最后按**同一音频格式**做对应处理：全频 AAC 档不做带限，另按本段实测的内容带边只切除带边以上的高频噪声平台（①），并对低电平噪声做全带抑制（②）；电话带档做零相位带限；PCM 档不做带限、不重编码。输出**一律为无损 PCM**：**PCM 档**写 `in24` 24bit、强制 ≥ 48 kHz（源低于 48 kHz 或高于 `stsd` 上限 65535 Hz 时重采样）；**默认 AAC 档**写 `sowt` 16bit 并保持源采样率（AAC 产物只经历一代编码）。还原侧按容器 sample entry 分派：`sowt` / `in24` → 直接读样本并以声明样本数为锚点（`restoreAudioTrackPcm`），`mp4a` → 解码并以声明样本数为锚点（`restoreAudioTrackAac`）；`off` 不含音轨。
  - **两端约束**：音频选项、音频格式（`#vidAudioBand`）与密码必须在混淆端与还原端**手动对称设置且完全一致**，这些状态**一律不写入文件**；还原音频可能经过平台多次转码，工具会输出「结构自恢复置信比」（越接近 1 越不可信），提示可能经过强转码或两端音频选项 / 音频格式设置不一致。

### 6.4 视频性能与实现要点

- **Worker 置换内核**：主线程把 `w / h / words / step / segCount / jump` 以及跑马灯参数（`mqOn, mqx, mqy, mqv0, mqMul, mqPref, mqOffSeed, mqOffTbl, mqFps`）随 `initFrame(...)` 一次性广播（每帧零额外参数开销）；Worker 内 `permuteFrame(data, mode, neg, frameNo)` 每帧即时派生 8 段偏移再分段循环平移，负片以 32 位位运算 `^0x00FFFFFF` 合并进每次写回，**全程零分配**（无乒乓缓冲）。
- **抽帧调度**：优先 `VideoDecoder` 顺序解码——`tryVideoDecExtract()` 用 MP4Box 解析全部视频样本逐个送入解码器（`extractVideoSamples` 解析 `avcC / hvcC / vpcC / av1C` 构建设置，`pump()` 负责消费循环），输出帧按 PTS 排序，为每个输出帧时刻 `wantTs=(fi+0.5)/outFps` 二分取「PTS ≤ wantTs 的最后一帧」（严格取左，与 DOM seek 语义一致，两端对称；且必须等到该时刻之后的帧到达才消费，避免降帧率时退化为慢放）。配合 **帧率元数据优先探测**（`probeFpsMeta` 用样本统计避开低配环境把 30fps 误测为 32/31 的时间轴错位）。VideoDecoder 不可用、设置不受支持或解码停滞时自动回退 DOM seek 抽帧（`runOne` 内联循环 + `seekToDom`），两路径的置换算法、密码派生与帧网格口径完全一致，产物互可还原。顺序解码覆盖 H.264 / HEVC / VP9 / AV1 四种编码，解码前经 `isConfigSupported` 预检。
- **性能量级**：1080p 单帧置换约 7.6ms 量级；代价与像素数量线性，**与密码长度、字符集无关**。
- **音频策略**：音轨的三种模式（`keep` / `scramble` / `off`）、分段与段内反转重排、以及两端对称约束，统一见 6.3 节「音轨混淆与还原（视频内音频）」，此处不重复。

### 6.5 安全边界

密钥由固定盐、210000 次迭代的 `PBKDF2-HMAC-SHA256` 确定性导出。随机猜测的密码恰好派生出与混淆时完全相同的密钥与各段偏移，概率约 **2⁻²⁵⁶**；GIF / 视频还需在各独立帧上同时命中，完整还原概率实际趋近于 0，且每次猜测都要付出 210000 次 SHA-256 与全帧置换的算力成本。**但本工具本质是轻度混淆（非加密），实际安全性取决于密码本身强度**——请使用高熵密码并妥善保管。

---

## 附：修改与调试入口

- **主题配色**：改 `:root`（日间）与 `html[data-theme="dark"]`（夜间）中的 `--brand / --brand-2 / --brand-deep`。
- **多语言**：文案集中在 `I18N.dict[key][lang]`（`t(key)` 依次回退「当前语言 → zh → key 名」），状态存于 `localStorage['dfq-ui-lang']`；切换与重绘走 `I18N.apply(lang, opts)`、循环切换走 `I18N.cycle()`，对外导出表为 `window.I18N = { LANGS, dict, lang, t, fmt, apply, cycle }`（没有 `applyLang` 这一函数名，动态文案统一走 `dyn.*` 词条与 `I18N.fmt()`）。新增文案必须在 zh / en / ja 三处各补一条。**已知例外（待补词条）**：GIF 还原路径有三条提示是硬编码中文——`GC.restoreGif` 的「无法从该 GIF 中读取任何帧」（:3676 / :3695）与「裁切尺寸无效：请确认密码与混淆时一致」（:3699），改这三条时请顺手改为 `dyn.*` 词条。
- **核心算法**：`__dfqDeriveKey / __dfqKeyToWords`（派生）、`__dfqParamsImage / __dfqParamsVideo`（step 与 v/h）、`__dfqSegOffsets`（分段偏移）、`__dfqSegJuggle`（平移）、`__dfqMarqueeParams`（跑马灯）、`fjGilbert2d`（曲线）、`encryptImageData / decryptImageData`（图片）、`GC.confuseGif / GC.restoreGif`（GIF）、视频 Worker（`initFrame / permFrame` 消息协议与 `permuteFrame`）。
- **调试**：F12 控制台可见详细日志；建议断点设在 `fjGilbert2d`、`encryptImageData`、`negatePixels`、`process()`（视频参数解析在 `runOne`）。
- **重要约束**：修改任何置换参数（步进、偏移派生、段数、帧号混入方式）后，**必须同步修改混淆与还原两侧并保证互为逆操作**；视频端需同步 `__dfqSegOffsets`、Worker 内 `segOffsetsFn`、还原端帧网格取帧号方式与 `jump` 开关四处同构。负片取反不可作用于 alpha 通道。

---

## English Description

**Ghost Media Confuser**

> A single-file, offline pixel-level confuser for images / GIFs / videos. Confusion and restoration must use exactly the same password.

### 1. Tool Name

**Ghost Media Confuser** (Chinese name: 虚魅灵影媒体混淆器). The whole tool is a single HTML file, `虚魅灵影媒体混淆器.html`.

### 2. Introduction

Ghost Media Confuser is a **pure front-end, zero-dependency, fully offline** media confuser: double-click the HTML file and it runs. All logic is inlined — including third-party codec libraries and an embedded WASM encoder — with no external assets and no network requests.

It applies *lightweight* pixel confusion to **static images, animated GIFs and videos** by **permuting pixel positions along a generalized Hilbert (Gilbert) space-filling curve**, with parameters derived from the user password via `PBKDF2-HMAC-SHA256`. Restoration is the exact symmetric inverse under the same password. By default only pixel positions are moved and no color value is rewritten (an optional negative/inversion layer flips the histogram instead), so the output **can only be restored by this tool**.

Important positioning: this is **lightweight obfuscation, not encryption** — without the negative layer the histogram is completely unchanged and the key space is bounded, so it is unsuitable for high-security use; a forgotten password means the content cannot be restored.

### 3. Features

**Three processing panels — confusion and restoration are symmetric**

| Panel | Input | Output | Notable options |
| --- | --- | --- | --- |
| Still-image confusion | PNG / JPG / WebP / AVIF / BMP (multi-select, drag & drop) | Auto / PNG / JPEG / WebP / AVIF / BMP | Output format and quality, negative |
| GIF confusion | .gif (multi-select) | Confused / restored GIF | Negative |
| Video confusion | MP4 / WebM / MOV / MKV, etc. (multi-select, decoded by the browser) | Always muxed to MP4 | Per-frame permutation, worker parallelism, custom resolution / frame rate / bitrate, audio mode and audio format, codec, negative, password-driven frame jitter, rainbow marquee |

**General capabilities**

- **Single password system**: any length, any characters (including CJK); all whitespace is stripped before derivation, so spaces / tabs / newlines never change the result. The password may be left empty (falling back to a fixed default shuffle). Includes a random mixed-character generator (12–128 chars), live strength estimation, and one-click copy / clear.
- **Batch processing**: every panel accepts multi-select and drag & drop, processing items one by one and recording success or failure individually.
- **Results overview**: all outputs of the session are collected in one list showing a thumbnail, type tag, save state, password tag, original file name and an editable save name; items can be selected and saved one by one or packed into a ZIP (timestamped file name) and cleared. A lightbox button previews any item (dimensions and audio-track info appear in its title).
- **Negative (self-inverse)**: per-channel RGB inversion with alpha untouched; ticking it on both ends of a round trip restores the original exactly.
- **Fine-grained video controls**: worker count (auto / 1 / 2 / 4 / 8), encoder implementation (auto / hardware / software), video codec (H.264 / H.265 / VP9 / AV1 with automatic fallback), resolution / frame-rate / bitrate presets and custom values, audio options (reverse-and-shuffle / keep original track / off) and audio format (full-band AAC / telephone-band AAC / PCM).
- **Real AVIF encoding**: an embedded libavif WASM encoder avoids the browser's `canvas.toBlob('image/avif')` always falling back to PNG.
- **UI experience**: Chinese / English / Japanese switching, light / dark themes, and a built-in "Developer Manual" dialog explaining the implementation in all three languages.

### 4. Quick Start

1. **Open the tool**: double-click `虚魅灵影媒体混淆器.html` in a modern browser (Chrome / Edge; no installation, no network). Video features rely on WebCodecs, so a recent Chrome / Edge is recommended.
2. **Choose files**: click or drag files onto the drop zone of the matching panel; use Ctrl / Shift for multi-select, or drop several files at once.
3. **Enter the password**: any password (may be empty). **Remember it** — confusion and restoration must use the identical password (compared after whitespace removal); any other difference prevents restoration.
4. **Choose the direction**: explicitly pick **Confuse** or **Restore** under "Mode" (the tool never auto-detects a marker; restoration is always manual).
5. **Start processing**: click "Start"; the log shows per-item progress, and the video panel additionally shows a progress bar and estimated time remaining, with a "Cancel" that aborts the current and remaining files.
6. **Save results**: switch to "Results overview", tick items, then "Save selected" or "Save selected as ZIP".

**Restoration notes**

- Images / GIFs require the same password used for confusion. Video restoration additionally requires manually mirroring the confusion-time state: "password-driven frame jitter", "rainbow marquee", "negative", the audio option and the audio format (none of these states is written into the file). The frame grid and the number of permutation segments are not settable: segments are fixed at 8, the frame grid is derived from the output frame rate, and both ends use the same algorithm.
- During video restoration, resolution / frame rate / bitrate are locked to the original confusion parameters (the UI disables editing), because per-frame inverse permutation must match exactly.
- Prefer a mixed-character password of at least 12 characters (upper / lower case, digits, symbols) to raise brute-force resistance.

### 5. Project Structure

```
混淆器/
├── README.md                      this document
└── 虚魅灵影媒体混淆器.html           the main program (single file, all logic inlined)
```

> The directory contains exactly these two files; the main program depends on no sibling asset and can be copied away on its own.

### 6. How It Works (Essentials)

#### 6.1 Curve and permutation

1. **Curve generation**: `fjGilbert2d(width, height)` walks every pixel of the rectangle along a generalized Hilbert curve and returns `curve[i]`, the linear index of the i-th pixel on the curve.
2. **Key derivation**: the whole password (after whitespace removal) is stretched by `PBKDF2-HMAC-SHA256(pw, 'XUMENG.SHUFFLE.KDF.SALT', 210000, 32B)` into a 256-bit key, then split big-endian into 8 32-bit words `words[0..7]` (a 2^256 state space), with a per-password cache.
3. **Segment offsets**: `__dfqSegOffsets(total, step, words, segs, frameNo, jump)` splits the curve into `segs` equal segments (8 with a password, 1 without), mixes `words[k%8]` and `words[(k+4)%8]` into a per-segment seed, folds in the golden-ratio seed `round((√5 − 1) / 2 × total)` and the frame number, and derives the segment offset `s_k` (with a non-zero fallback and a deterministic outward nudge on power-of-two self-similar boundaries). The offsets are a deterministic pure function shared by both ends, hence symmetric by construction.
4. **Cyclic shifting**: `__dfqSegJuggle` cyclically shifts every segment by its own offset using a zero-allocation juggling cursor (decomposed by `gcd(s_k, len_k)`); confusion shifts forward, restoration shifts backward — exact inverses.
5. **Canvas padding** (shared by images and GIFs): before permutation the canvas is enlarged by `v = (words[2] >>> 16) % 10` columns on the right and `h = (words[3] >>> 16) % 10` rows at the bottom (key-derived); restoration crops the margins first, then inverse-permutes. Without a password `v = h = 0`.
6. **Negative (optional)**: every RGB byte becomes `255 − val`, alpha untouched. This transform is **self-inverse** and **commutes** with the position permutation, so ticking it symmetrically on both ends restores correctly.

#### 6.2 Parameter derivation

```
key    = PBKDF2-HMAC-SHA256(pw, 'XUMENG.SHUFFLE.KDF.SALT', 210000, 32B)   // cached per password
words  = key split big-endian into 8 × 32-bit words
image/GIF: step = 1 + (words[6] % 99);   v = (words[2]>>>16)%10;  h = (words[3]>>>16)%10;  segs = 8
video:     step = 1 + (words[6] % 65536);                                                segs = 8
segment k: w0 = words[k%8];  w1 = words[(k+4)%8]
           seed = w0 ^ (w1 + 0x9E3779B9 + k*0x85EBCA6B)
           mix  = w1 ^ (w0 + f*(0x27D4EB2F+k))        // f = frame number; f = 0 when jitter is off or no password
           s_k  = ((golden + seed%4096 + mix%4096) * (step + seed%64)) % len_k
           golden = round((√5−1)/2 × total);  s_k = 0 falls back to non-zero; power-of-two boundary nudge
no password: words = null → segs = 1 → offset is the plain golden-ratio seed (s = golden % total, or 1 if 0)
```

**Frame-number mixing**: still images always use frame 0; GIFs mix the frame index `idx`; videos mix the output frame index `job.i` on the fly — under one key, all 8 segment offsets differ between adjacent frames, destroying inter-frame structural correlation while both ends enumerate frames identically. When "password-driven frame jitter" (off by default) is disabled the mixing term is zero and every frame degenerates to a constant mapping (frame 0's), still requiring the same password; the switch is **not written into the file** and must be set symmetrically.

#### 6.3 Video specifics (short version)

- Decoding prefers `VideoDecoder` (MP4Box parses the samples) and falls back to DOM seeking; both paths share the same permutation, derivation and frame grid, and their outputs are mutually restorable.
- N workers permute frames in parallel; each worker derives the 8 segment offsets per frame at `permuteFrame`, applying the negative via `^0x00FFFFFF` inside the same write-back, with zero allocation.
- Optional **rainbow marquee**: key-derived per-frame hue offset applied after permutation on confusion and before inverse permutation on restoration; its parameters (`P = 512`, x/y cadence 1–5 s, base speed 30–199 px/s, 512 speed multipliers in [3.000, 5.000], 512 hue offsets in [−30°, +30°]) live only in memory.
- Output is always MP4 at `frames / outputFps` seconds; the resolution / frame-rate / bitrate, codec chain (user choice first, then AV1 → H.264 → HEVC → VP9, VP9 always last) and encoder implementation are described above.
- **Audio** is independent of the picture: `keep` copies the encoded track byte-for-byte; `scramble` (default) decodes to PCM, splits the track into equal segments from `AUD_SEG_LEN_TBL = [511, 1021, 2047, 4093, 8191, 1877, 443]`, time-reverses each segment, then reorders all segments with a password-derived Fisher-Yates permutation and re-encodes (AAC-LC by default, `in24` PCM when the PCM format is chosen); `off` drops the track. Restoration recovers the true segment count and decoder lead-in from the container's declared sample count (`audioSegNRecover`), then applies the same table and password in reverse; audio output is always lossless PCM and every audio state is also never written into the file.

### 7. Security Boundary

The key is derived deterministically from a fixed salt with 210000 PBKDF2 iterations. The chance that a random password yields exactly the same key and segment offsets is about **2⁻²⁵⁶**; GIFs and videos additionally require hitting every independent frame, so full restoration probability is effectively zero, and each guess costs 210000 SHA-256 rounds plus a full-frame permutation. **The tool is still lightweight obfuscation, not encryption** — real security depends on password strength, so use a high-entropy password and keep it safe.

### 8. Modification and Debug Entry Points

- **Theme colors**: edit `--brand / --brand-2 / --brand-deep` inside `:root` (light) and `html[data-theme="dark"]` (dark).
- **Localization**: all strings live in `I18N.dict[key][lang]` (`t(key)` falls back "current lang → zh → key"), the state is stored in `localStorage['dfq-ui-lang']`, switching uses `I18N.apply(lang, opts)` and cycling uses `I18N.cycle()`; the exported table is `window.I18N = { LANGS, dict, lang, t, fmt, apply, cycle }`. New strings must be added in all three languages (zh / en / ja).
- **Core algorithms**: `__dfqDeriveKey / __dfqKeyToWords` (derivation), `__dfqParamsImage / __dfqParamsVideo` (step and v/h), `__dfqSegOffsets` (segment offsets), `__dfqSegJuggle` (shifting), `__dfqMarqueeParams` (marquee), `fjGilbert2d` (curve), `encryptImageData / decryptImageData` (images), `GC.confuseGif / GC.restoreGif` (GIFs), the video worker (`initFrame / permFrame` protocol and `permuteFrame`).
- **Debugging**: the F12 console prints detailed logs; suggested breakpoints are `fjGilbert2d`, `encryptImageData`, `negatePixels` and `process()` (video parameter parsing happens in `runOne`).
- **Critical constraint**: whenever any permutation parameter changes (step, offset derivation, segment count, frame mixing), **change both the confusion and the restoration side so that they stay exact inverses**; on the video side keep `__dfqSegOffsets`, the worker's `segOffsetsFn`, the restoration frame-index derivation and the `jump` switch in sync in all four places. The negative inversion must never touch alpha.
