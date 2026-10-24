# hpma-spellbook

spellbook 数据集仓：cards（魔咒书卡牌）与 echoes（回响）两个平级数据模块的发布层。

## 模块一览

| 模块 | 数据文件 | 内容 | 条数 | 版本轴 | 当前版本 |
|---|---|---|---|---|---|
| `cards/` | `cards.json` | 141 张卡牌：双语 name/desc/quote/tags/stats + 逐级数值，卡面 `images/`/`images_webp/`，品质边框 `frames/` | 141 | `cards-v{S}.{D}.0` | v1.20261024.0 |
| `echoes/` | `echoes.json` | 184 回响 / 46 图鉴角色 / 270 词缀：属性分组、成长、经济、卡牌适配权重、品质光环，图标 `images/`/`images_webp/` | 184 | `echoes-v{S}.{D}.0` | v1.20261024.0 |

- 两模块平级，各自独立的 `(schema_version, data_version)` 版本轴：内容刷新只动 `data_version`（YYYYMMDD），结构变更（消费端要改代码）才 +1 `schema_version`。
- 发布 tag = `cards-v{schema}.{data}.0` / `echoes-v{schema}.{data}.0`，release 附整模块 zip（内含 `check.html` 核对页）+ `zip.sha256` + RELEASE_NOTES。
- 数据结构契约见各模块 `schema/`；数据文件内嵌的 `schema_version` 与模块 manifest 的版本轴相互独立。

## 目录结构

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
    ├── images/                     # 回响图标
    └── images_webp/                # 回响图标 WebP 副本
```

- 图标引用统一写 `images/<文件名>`（echo id 与 card id 是独立空间，跨集引用看显式字段，勿按数值范围猜类型）。
- echoes 模块 `coverage.icons` / `counts.icons` 指 `images/` 张数、`icons_webp` 指 `images_webp/` 张数；`missing_icons` 为缺失图标文件名列表（仅 basename）。

## 校验与核对

- 任一模块根目录内：`sha256sum -c checksums.txt`（清单覆盖该模块除自身外全部文件）。
- release zip 解压后用浏览器打开 `check.html`，可离线核对全部条目与图片。

## 声明

本数据集仅供个人学习研究，请勿商用或再分发素材。
