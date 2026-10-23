# hpma-spellbook

《：》（HPMA）**魔咒书（spellbook）数据专用仓**：魔咒书卡牌与回响两个平级数据模块的发布层。

## 仓定位

- 本仓是 spellbook 域数据链（** → 数据包仓 → 消费者**）的第二层，**只收发布产物**，不做。
-  = [](https://github.com//)（私有研究仓：`.py` / `.py`  + 按模块导出/打包/发布）。
- 消费方式：git submodule 按模块 tag 固定，或取发布 zip 后以各模块 `checksums.txt` 校验。

## 模块一览

| 模块 | 数据集 | 内容 | 条数 | 版本轴 | 当前版本 |
|---|---|---|---|---|---|
| `cards/` | `cards.json` | 魔咒书卡牌：141 张卡（双语 name/desc/quote/tags/stats + 逐级数值）+ 卡面 `images/`/`images_webp/` + 品质边框 `frames/` | 141 | `cards-v{S}.{D}.0` | 种子 = v8 血统（20261005），首发待链重跑 |
| `echoes/` | `echoes.json` | 回响：184 回响 / 46 图鉴角色 / 270 词缀 + 属性分组、成长、经济、卡牌适配权重、品质光环 + 图标 `images/`/`images_webp/` | 184 | `echoes-v{S}.{D}.0` | 种子 = v8 血统（20261005），首发待链重跑 |

- 两模块**平级**，各自独立的 `(schema_version, data_version)` 轴：echoes 热更加内容不牵连 cards，反之亦然。
- 发布版本 = `{schema_version}.{data_version}.0`，tag = `cards-v{ver}` / `echoes-v{ver}`；热更加内容只动 `data_version`（YYYYMMDD），结构变更（消费端要改代码）才 +1 `schema_version`。
- 打 tag / 建远端 / 发 release 属**发布动作**，须过核对页并经用户确认。

### 版本两条轴（务必分清）

- **数据文件内嵌的 `schema_version`（数据自身契约）**：由各自 `schema/*.schema.json` 描述，与模块 manifest 的版本轴互不相干。

### 当前状态：种子（未发布）



- 本仓**首发 = 该版本原样重组**为 cards / echoes 两模块，数据零变更；**唯一差异 = 图片目录改名 `images/` 及其引发的路径字段重写**：
  - 目录改名：旧 `echo_images/` → `echoes/images/`、旧 `echo_images_webp/` → `echoes/images_webp/`（废除混居防撞名的 `echo_` 前缀，模块目录即命名空间）；图片文件本体 sha256 逐一未变。
  - `echoes.json`：内嵌图片路径 **281 处**前缀 `echo_images/` → `images/`（`characters[].icon`/`big_icon`、`echoes[].icon`/`evolve_icon`、`rarity_backgrounds`）。字节级前缀替换、可逆替换双向验证通过，除前缀外零变更；**`cards.json` 无内嵌回响路径，与源逐字节一致**（sha256 相同）。
  - `echoes/schema/echoes.schema.json`：路径 `pattern` 与描述随之改写（`^echo_images/…$` → `^images/…$`）；`cards/schema/cards.schema.json` 逐字节保留。
  - 两份 `manifest.json` 按新契约新写（见 `schema/manifest.schema.json`，两模块同一份），`coverage` 自旧 manifest 本模块子树语义原样搬运。

## 目录规范

模块内一律 `images/` + `images_webp/`，两模块布局同构：

```
hpma-spellbook/
├── README.md
├── cards/                          # 模块一：魔咒书卡牌
│   ├── cards.json                  # 141 张卡
│   ├── manifest.json               # 模块清单（版本/counts/coverage）
│   ├── checksums.txt               # 本模块 sha256 清单（sha256sum -c 可验）
│   ├── schema/
│   │   ├── cards.schema.json       # cards.json 契约
│   │   └── manifest.schema.json    # 模块 manifest 契约（与 echoes/ 同一份内容）
│   ├── images/                     # 卡面 PNG（375x500）
│   ├── images_webp/                # 卡面 WebP 副本（优先加载，失败回退 PNG）
│   └── frames/                     # 品质边框（cards 独有）
└── echoes/                         # 模块二：回响
    ├── echoes.json                 # 184 回响
    ├── manifest.json / checksums.txt
    ├── schema/
    │   ├── echoes.schema.json      # echoes.json 契约
    │   └── manifest.schema.json    # 与 cards/ 同一份内容
    ├── images/                     # 回响图标（旧 echo_images/）
    └── images_webp/                # 回响图标 WebP 副本（旧 echo_images_webp/）
```

- 图标引用统一写 `images/<文件名>`（echo id 与 card id 是独立空间，跨集引用看显式字段，勿按数值范围猜类型）。
- coverage 口径沿用旧包：echoes 模块 `coverage.icons` / `counts.icons` 指 `images/` 张数、`icons_webp` 指 `images_webp/` 张数；`missing_icons` 为游戏内寻址失败的图标**源路径**（非包内路径）。

## 校验

任一模块根目录内：`sha256sum -c checksums.txt`（清单覆盖该模块除自身外全部文件）。

## 版权

游戏衍生数据，仅供个人学习研究，**不开源、不再分发**；卡面/图标/边框素材著作权属及版权方。
