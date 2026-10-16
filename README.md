# Acientcuisine

基于 **Unreal Engine 5.4** 的古代饮食主题第三人称交互项目。现有内容围绕村落场景中的任务、对话、食材与器具交互展开，包含灶台烹饪、烧烤、古籍阅读及数独等相关蓝图与界面资产。

项目目前采用蓝图开发，业务逻辑主要位于 `Content/Code/`，没有独立的 `Source/` C++ 模块。工程文件名沿用仓库中的 `Acientcuisine` 拼写。



## 当前进度

目前完成了线性主体交互逻辑，未搭建场景，仍需优化细节。


项目基于UE第三人称模板制作

可以立即测试的线性任务结构：玩家醒来->与灶台交互->与芹菜交互->与柴火堆交互->与灶台交互->完成做饭游戏->......

线性逻辑:TaskManager线性任务框架的核心，玩家靠近与物体交互，物体判断自身是否为当前TaskManager任务状态下需要交互的物体，交互后更新状态任务。所有任务状态用枚举保存在E_TaskStage中。添加新的任务状态只需要在E_TaskStage中添加枚举并在TaskManager中添加任务对应的显示内容即可。

对话系统:DialogueManager负责处理对话框等

交互系统:BP_InteractableBase作为所有可交互物体的父类定义物体的任务状态变量，IBP_Interact作为接口定义物体的行为，即所有可交互物体都时BP_InteractableBase的子类并实现IBP_Interact。 BP_Pig除外。

### 操作绑定

项目使用 Enhanced Input，主要映射资源为 `Content/ThirdPerson/Input/IMC_Default.uasset`。

| 输入资产 | 对应操作入口 |
| --- | --- |
| `IA_Move` | 角色移动 |
| `IA_Look` | 视角控制 |
| `IA_Jump` | 跳跃 |
| `IA_Interact` | 交互 |
| `IA_CookJudge` | 烹饪判定 |
| `IA_RotateMeat` | 烤肉旋转 |

具体按键请在编辑器中查看 `IMC_Default`，并结合 `BP_ThirdPersonCharacter` 检查绑定与启用条件。本文不将模板默认按键当作本项目已确认的操作说明。

## 文件结构

```text
Acientcuisine/
├── Acientcuisine.uproject       # UE 5.4 工程入口
├── Config/                     # 地图、渲染、输入等项目配置
├── Content/
│   ├── Code/                   # 项目业务蓝图（不是 C++ 源码）
│   │   ├── Character/          # 可交互角色基类、猪角色与动画蓝图
│   │   ├── Dialogue/           # 对话管理器
│   │   ├── Interact/           # 交互接口、交互基类、测试蓝图
│   │   ├── Task/               # 任务管理器与阶段枚举
│   │   └── TaskObject/         # 食材、器具、场景交互物和交互摄像机
│   ├── ThirdPerson/            # 主地图、玩家角色、GameMode、输入资源
│   ├── buildings/		#存储主要的搭建地图的模型素材
│   ├── UI/                     # 对话、任务、交互提示、书籍、烹饪、数独界面
│   ├── Pictures/               # 界面图片资源
│   ├── Font/                   # 字体资产
│   ├── Prefabs/                # 食材、器具、古籍等模型、材质和贴图资源
│   ├── Mesh/                   # 灶台、柴火、墙体等模型与材质
│   ├── Japanese_Village/       # 村落环境资源包
│   ├── LChineseWarrior/        # 人物资源包
│   ├── AnimalVarietyPack/      # 动物模型与动画资源
│   ├── Fire_VDB/               # 火焰体积效果、材质及示例地图
│   ├── Fab/                    # 其他导入道具资源
│   ├── Characters/             # 模板角色、骨骼、动画与绑定资源
│   ├── StarterContent/         # UE 基础示例资源
│   ├── LevelPrototyping/       # 关卡原型资源
│   ├── __ExternalActors__/     # 地图的外部 Actor 资产
│   └── __ExternalObjects__/    # 地图的外部对象资产
├── .gitattributes              # LFS 与文本规则
└── .gitignore                  # 本地缓存及生成文件的忽略规则
```

1. **主地图与玩家入口**：打开 `ThirdPersonMap`，查看关卡中的对象；再阅读 `BP_ThirdPersonGameMode` 和 `BP_ThirdPersonCharacter`。
2. **通用交互规则**：查看 `Content/Code/Interact/BPI_Interact`、`BP_InteractableBase`，以及 `Content/Code/Character/BP_InteractableCharacterBase`，了解对象如何响应交互。
3. **任务推进**：查看 `Content/Code/Task/E_TaskStage` 和 `BP_TaskManager`，结合 `Content/UI/WBP_TaskUI` 理解阶段与提示的关系。
4. **对话呈现**：查看 `Content/Code/Dialogue/BP_DialogueManager` 与 `Content/UI/WBP_Dialogue`。
5. **具体玩法**：从 `TaskObject` 中选择一个对象，追踪它的交互逻辑、任务条件、界面及场景引用。

常见任务的定位入口：

| 灶台与烹饪判定 | `BP_Stove`、`BP_StoveCamera`、`WBP_CookBar`、`IA_CookJudge` |
| 烧烤交互 | `BP_Barbecue`、`BP_BarbecueCamera`、`BP_Meat`、`IA_RotateMeat` |
| 食材与器具 | `BP_Celery`、`BP_Carrots`、`BP_Bucket`、`BP_Pot`、`BP_Bowl`、`BP_Plate` 等 |
| 调料相关逻辑 | `BP_Salt`、`BP_Sesame`、`BP_Spice`、`BP_Vinegar` 及 `BP_TaskManager` |
| 场景人物或动物交互 | `BP_Merchant`、`BP_PigCircle`、`BP_Vegetableplot`，以及 `Code/Character/BP_Pig` |
| 古籍与阅读界面 | `BP_AcientBook`、`WBP_Book1`、`WBP_Book2`、`WBP_Book3` |
| 数独界面 | `Content/UI/数独/WBP_Sudoku`、`WBP_SudokuCell` |
| 开场与界面表现 | `WBP_Draw`、`WBP_Blink`、`WBP_Flicker`、`Content/Pictures/` |

上表中的物体蓝图位于 `Content/Code/TaskObject/`，界面蓝图位于 `Content/UI/`，输入动作位于 `Content/ThirdPerson/Input/Actions/`，另行标注的路径除外。

