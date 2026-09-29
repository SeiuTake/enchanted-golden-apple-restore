# 附魔金苹果回归（Enchanted Golden Apple Restore）

Minecraft **Java 版 26.3**（Wilderness Bound，数据包格式 **121.0**）数据包，纯服务端，客户端不需要装任何东西。

## 下载

- 打包好的数据包（推荐）：
  [enchanted-golden-apple-restore.zip](https://github.com/SeiuTake/enchanted-golden-apple-restore/releases/latest/download/enchanted-golden-apple-restore.zip)
- 或者克隆本仓库，把仓库根目录（含 `pack.mcmeta`）当成数据包文件夹直接使用。

## 它做了什么

1. **恢复附魔金苹果的合成配方**：8 个金块 + 1 个苹果（1.9 之前的老配方，之后被官方移除，一直无法合成）。
2. **补上对应的成就（进度）「过于强大」**：吃下一个附魔金苹果即可完成，挑战框架（紫色），奖励 50 点经验。
3. **配方书解锁进度**（隐藏）：拿到金块之后，配方会自动出现在合成台的配方书里，和原版配方一样可搜索。

> 说明：26.3 里附魔金苹果这个物品本身并没有被删除，仍能从刷怪房、废弃矿井、远古城市、堡垒遗迹、
> 沙漠神殿、废弃传送门、试炼密室不祥宝库、林地府邸的箱子里开出，只是**不能合成**，而且 Java 版
> 一直没有对应成就（基岩版才有成就 Overpowered／过于强大）。本数据包补的就是这两样东西。

## 目录结构

```
enchanted-golden-apple-restore/
├── pack.mcmeta                                        # 数据包描述与格式（121.0）
└── data/
    └── ega/
        ├── recipe/
        │   └── enchanted_golden_apple.json             # 8 金块 + 1 苹果 → 附魔金苹果
        └── advancement/
            ├── overpowered.json                        # 成就：吃掉附魔金苹果
            └── recipes/
                └── enchanted_golden_apple.json         # 配方书解锁（隐藏进度）
```

## 安装（服务端）

1. 把整个 `enchanted-golden-apple-restore` 文件夹放进：
   - **服务器**：`<服务器目录>/<世界名>/datapacks/`（世界名默认是 `world`）
   - **单人/局域网存档**：`<存档目录>/datapacks/`
2. 直接在控制台执行 `/reload`（单人可直接 `/reload`），或重启服务器。
3. 确认加载：`/datapack list`，应能看到 `file/enchanted-golden-apple-restore`。

也可以把文件夹内容压缩成 `.zip`（**`pack.mcmeta` 必须在压缩包的根目录，不能多一层文件夹**），
再放进同一个 `datapacks` 目录，效果一样。

数据包是纯服务端的：所有玩家自动获得配方和成就，无需安装模组、资源包或任何客户端文件。

## 验证 / 测试用命令

```mcfunction
/reload
/datapack list
/recipe give @s ega:enchanted_golden_apple   # 直接给配方，可跳过解锁
/advancement grant @s only ega:overpowered   # 直接授予成就
/give @s minecraft:enchanted_golden_apple 1  # 物品本身本来就存在
```

## 自定义

### 改配方
编辑 `data/ega/recipe/enchanted_golden_apple.json`：

```json
{
  "type": "minecraft:crafting_shaped",
  "key": { "#": "minecraft:gold_block", "X": "minecraft:apple" },
  "pattern": ["###", "#X#", "###"],
  "result": { "id": "minecraft:enchanted_golden_apple", "count": 1 }
}
```

- 想更便宜：把 `minecraft:gold_block` 换成 `minecraft:gold_ingot`。
- 想改成无序配方：`"type": "minecraft:crafting_shapeless"`，并写成 `"ingredients": [...]`（无 `pattern`/`key`）。
- 想改成 9 个金块合成多个：修改 `result.count`。

### 改成就
编辑 `data/ega/advancement/overpowered.json`：

- 名称/描述：`display.title` / `display.description`（这里用的是直接文本，所以中文对任何语言的客户端都显示中文；
  想按客户端语言切换就需要额外的资源包语言文件）。
- 图标：`display.icon.id`。
- 是否弹窗/全服公告：`display.show_toast` / `display.announce_to_chat`。
- 奖励：`rewards.experience`（默认 50，改成 0 或删掉 `rewards` 即无奖励）。
- 难度外观：`display.frame` 可为 `task`（普通）、`goal`（目标）、`challenge`（挑战）。

**想改成「合成/拿到就给成就」**：把 `criteria` 换成背包检测（同时记得改 `requirements` 里的键名）：

```json
"criteria": {
  "got_enchanted_golden_apple": {
    "conditions": {
      "items": [
        { "items": "minecraft:enchanted_golden_apple" }
      ]
    },
    "trigger": "minecraft:inventory_changed"
  }
}
```

## 版本升级提示

`pack.mcmeta` 声明的是数据包格式 121（= 26.3.x）：

```json
{
  "pack": {
    "description": "附魔金苹果回归：恢复合成配方，并添加「附魔金苹果」成就（进度）",
    "pack_format": 121,
    "min_format": [121, 0],
    "max_format": 121
  }
}
```

- 升级到 26.4 及以后：把这三处 `121` 一起改成新版本的数据包格式（26.4 快照为 122），
  或用 `/version` 命令查询当前版本支持的格式号。若显示为「不兼容」但内容没报错，游戏通常会提示确认后仍可使用。
- 如果只在 26.3 上玩，保持现状即可，无需改动。

## 文件格式依据

所有 JSON 结构均与 26.3 原版数据一致（对照 26.3 版原版数据包）：

- `recipe`：`type` / `key`（值为物品 ID 字符串）/ `pattern` / `result.id`
- `advancement`：`parent` / `criteria` / `display`（`title`、`description`、`icon.id`、`frame`）/ `requirements` / `rewards`
- 触发器：`minecraft:consume_item`（吃下）、`minecraft:inventory_changed`（获得）、`minecraft:recipe_unlocked`（配方解锁）
