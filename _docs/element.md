# 元件说明

?> 在这个部分，将详细讲解编辑器的每个部件的用法。

## 素材区

素材区在展开状态下，从左到右分为若干列：

1.  `project\materials\terrains.png`：位于素材区第一列（最上面两个图块不在这张图片里，它们分别是擦除和空气墙），其中从楼梯开始往下有系统含义，请勿随意修改其图块属性。
2.  `project\materials\animates.png`：位于素材区第二列，共4帧。主要为星空、岩浆、三色墙、六色门、四向出入口箭头、四种路障。
3.  `project\materials\enemys.png`：32×32px（像素，下同）的怪物，您可以随意修改它们的任何属性。如果嫌两帧太少，还可以作为32×48px怪物画在靠下2/3部分。
4.  `project\materials\enemy48.png`：32×48px的怪物，自带只有四只。您可以随意修改它们的任何属性。
5.  `project\materials\npcs.png`：32×32px的NPC，如老人、商人、小偷、公主、仙子、木牌、魔龙和章鱼的其他8块，您可以随意修改它们的任何属性。
6.  `project\materials\npc48.png`：32×48的NPC，自带只有样板0层的小姐姐，但您也可以用它来制作32×48px的门。
7.  `project\autotiles`：自动元件，会随着在地图上的连续摆放而自动采取适当的绘制方式。
8.  `project\tilesets`：额外素材，用来突破其他素材合计不得超过1000个的限制。您可以在这个区域拖动来批量框选，再在地图区单击成片绘制或拖动平铺。

## 地图编辑（快捷键Z）

![image](img/editor.jpg)

如图所示，您可以在此对地图进行清空或删除操作，也可以新建或批量新建任意宽高的空白地图。

其中“导出并复制地图”是指显示出左侧的矩阵并复制（一般用来跨塔复制地图），您也可以直接改动其中的数字，再点击“从框中导入地图”就能将改动的结果同步到地图上。

下面的“楼层ID、中文名、状态栏名”分别对应楼层属性（快捷键V）的floorId、title和name，其中floorId也作为文件名（不能使用中文），title会显示在楼传界面和楼层切换黑屏，name也允许使用中文。

## 图块属性（快捷键C）

![image](img/mapsc.jpg)

如上图，除怪物和道具外，所有素材的图块属性都定义在`project\maps.js`中。道具和怪物属性也支持清空和批量复制，下面逐一讲解各条目的含义和用法：

1.  **图块ID：**图块的唯一标识符`core.getBlockId(x, y, floorId, showDisable)`，不允许使用中文和纯数字。请注意，额外素材`tileset`的图块ID由素材图片的顺序和图块在图片上的位置确定，无法更改，也请勿随意调换图片的顺序。样板已注册的图块中建议只修改怪物和NPC的图块ID，修改方法为上图最下方的**修改图块id为**。
2.  **图块数字：**见前面的描述，额外素材的数字由ID去掉字母X得到。
3.  **图块类别：**图块素材的类型。
4.  **图块名称：**怪物在手册中、道具在道具栏中、其他图块在剧情对话中的默认名称，可以随意修改。但原则上不推荐不同的怪物和道具有重复的名称，否则会影响事件编辑器的中文替换功能。

你可以随时使用 `core.getBlockId(x, y, floorId, showDisable)` 获得地图上任何一个点的图块ID；`core.getBlockCls(x, y, floorId, showDisable)` 或的地图上任何一个点的图块类别；详见[API列表](api)。

在讲解其他属性之前，这里简单介绍一下素材的注册机制：

* 除自动元件和额外素材外，其余图块只有在注册后才拥有上面所说的ID和数字。
* 未注册的图块则只有“索引”，索引为n表示该图块在图片的第（n+1）行，
* ID和索引的对应关系定义在`project\icons.js`中。
* 尝试用未注册的图块（如利用便捷PS工具新追加的图块）在地图上绘制，就会出现红色的问号方框。
* 此时请在数据区手动注册此图块，只需填写一个新ID和数字（1000以内）即可。
* 也可以点击“自动注册”按钮批量注册该图片的所有未注册素材，自动注册出的ID无任何意义，建议手动修改成有意义的内容。

自动元件的注册与此不同，除了替换样板现有的几个外，如果还需要追加新的，请在地图区下方的下拉框中切换到“追加素材”（快捷键M），然后导入文件到画板autotile，再点击“追加”按钮即可。非Windows系统追加其他素材也主要依靠这种方式，具体用法请自行探索。

### 非怪物非道具属性

1. **触发器：**当碰触到地图上此图块时触发的系统事件，详见[事件](event)。
  * **battle**: （未列出）战斗；当撞上一个怪物且没有覆盖触发器时（参见[事件](event)）将自动调用此触发器产生战斗，并此战后事件。
  * **getItem**: （未列出）获得道具；当撞上一个道具且没有覆盖触发器时（参见[事件](event)）将自动调用此触发器获得它，并触发道具后事件。
  * **changeFloor**: （未列出）楼层切换；对于地图上绑定的绿点将自动调用此触发器产生楼层切换事件。
  * **openDoor**: 用于制作门效果，当撞上此图块时将尝试开门（仅对`animates`和`npc48`生效）；具体开门动画参见下面的门信息。
  * **pushBox**: 推箱子信息；请勿对非箱子使用此触发器。
  * **ski**: 冰信息；拥有此触发器的图块放置在背景层时，走上去将触发滑冰效果。
  * **custom**: 自定义系统触发器；你可以使用 `core.registerSystemEvent` 来自己定义一个系统触发器，参见[API列表](api)。
2. **可通行性：**勾选后勇士才可以踏入此图块，否则只能撞击此图块。（怪物被锁定为不可通行，道具被锁定为可通行，如有需要可以修改点上的不可通行性）
3. **碰触脚本：**勇士踏入或撞击此图块时执行的脚本，该项会被eval，相当于一种自定义的触发器，您可以参考踩灯和四种路障去填写它。
4. **不可出入方向：**对三个图层的图块都有效。不可出方向指的是勇士站在这种图块上不能向哪个方向走（包括撞击），不可入方向指的是勇士不能从哪个方向走向这种图块（包括撞击）。例如，勾选了“上”则不能从上方向下走向这个图块。请参考素材区第一列最下面的四个箭头。
5. **可破震：**勾选后，此图块将成为破墙镐（pickaxe）和地震卷轴（earthquake）这两个道具的目标。
6. **动画帧数：**您可以修改此帧数来让本来有4帧的图块只用前3帧循环播放，另外制作门时请务必将此帧数改为1，表示门在打开前静止在第1帧。
7. **门信息：**只对`animates`和`npc48`有效，您可以点击“编辑按钮”来填写此图块作为门的开关耗时、开关音效以及需要哪些钥匙各多少把（可以填写任何消耗类道具，也可以选择某些道具只需持有一定数量而不消耗）。修改此信息后，您需要将上面的“动画帧数”改为1，并可能需要将“触发器”改为openDoor（不改的话将无法通过撞击来开门，但可以像三色墙一样用来制作暗墙）。
8. **行走图朝向：**只对npc48有效。设置后，当勇士撞击该npc时npc会尝试转身面向勇士（对话事件结束前请使用“事件转向”指令将其转回去）。走动时也会尝试自动转向，请参考样板0层使用的小姐姐。

### 道具属性

样板自带的道具都在样板0层摆好了，您可以直接进入游戏捡起它们，就会看到该道具的注意事项，这里不再赘述。

1. **道具类别：**虽然和图块类别的英文缩写都是cls，但有本质区别，请注意区分。道具的图块类别都是items，而道具类别分为以下几种：
    * items：是的你没看错，又是`items`这个词，请注意和图块类别的`items`相区分。它表示即捡即用类不进背包的道具，如四种血瓶、三种宝石等。这类道具需要用到的其他属性有“即捡即用效果”、“即捡即用提示”、“碰触或使用事件”。
    * tools：进背包的消耗类道具，如钥匙和解药瓶、便携式血瓶蓝瓶（生命魔杖）、破震炸飞和跳鞋等。这类道具需要用到的其他属性有“道具描述”、“不显示在道具栏”、“回放不绘制道具栏”、“碰触或使用事件”、“使用效果”、“能否使用”。
    * constants：进背包的永久道具（每件在背包的数量要么为1要么为0），如手册、楼传、幸运金币、十字架、护符、二倍斩等，这类道具需要用到的其他属性和tools一致。
    * equips：装备，它需要用到的其他属性有“道具描述”、“道具的装备属性”、“能否装备”。
    * 例如：如果您想把四种血瓶和三种宝石改为便携式，就只需把其道具类别改为tools（当然，楼层属性中的宝石血瓶效果一项也就失效了）。
    * 如果想把大黄门钥匙变为钥匙盒（红黄蓝钥匙各一把），只需把其道具类别从tools改为items
    * 如果想把剑盾变成装备，只需把其道具类别改为`equips`，等等
2. **道具描述：**对除即捡即用类外的道具都有效。一个字符串，为道具在道具栏里的描述，也作为首次捡到时的提示信息的一部分（如果全塔属性中开启了这一提示功能的话）。支持使用`${表达式计算}`语法（如四种血瓶和三种宝石那样，但不支持中文替换），此语法的详细规则见“显示文章正文的转义序列”，和“值块和冒号缩写量”。
3. **不显示在道具栏：**对tools和constants有效，勾选此项后，该道具在背包中将不显示出来。常用于不能主动使用或已有专门的使用按钮的道具来节省显示篇幅，如手册、楼传、幸运金币、十字架、护符和钥匙等。
4. **回放不绘制道具栏：**勾选此项后，录像回放中使用此道具将不显示黑黑的道具栏。常用于频繁使用的道具，如楼传、技能和冰冻徽章等。
5. **即捡即用效果：**如题，该项会被eval，一般为一行下述的代码： `core.status.hero.xxx += yyy * core.status.thisMap.ratio`
    * 其中xxx为勇士的某种状态（如生命hp、生命上限hpmax、魔力mana、魔力上限manamax、护盾mef、攻防和金经）
    * yyy为此道具的基础效果（如四种血瓶和三种宝石的基础效果定义在了全塔属性中）
    * `core.status.thisMap.ratio`则是指该道具所在楼层的“楼层属性”最下面的“宝石血瓶效果”。
6. **即捡即用提示：**实际显示时会被接在“获得xxx”后面，所以该项总是一个以逗号开头的字符串，同样支持`${表达式计算}`语法。
7. **碰触或使用事件：**对除equips外都有效。该项用于代替“即捡即用效果”（但会使勇士停下脚步，且会晚于afterGetItem事件被执行）和“使用效果”，如样板中的黄宝石和生命魔杖。如果您的js语法基础薄弱，那么它将是您的不二之选。
8. **使用效果：**对tools和constants有效。该项会被eval，一般为一个js函数，较为简单的使用效果（如解药瓶）也可能是一行代码，破炸冰的目标个数请直接在该项中修改。总的来说因为事件比起脚本更容易实现异步特效且录像安全性更好，所以如非必要，不建议用此项。
9. **能否使用：**对tools和constants有效。该项也会被eval，一般为一个js函数，较为简单的使用条件（如解药瓶）也可能是一行形如`core.hasFlag('xxx')`的代码。如果该道具在任何情况下都不能主动使用，请留null（并最好勾选“不显示在道具栏”以节约显示篇幅），如果该道具在任何情况下都可以使用，请填“true”，如果使用条件较为复杂，也推荐直接填“true”先斩后奏，在使用效果中再行判定，并在使用失败的场合使用`core.addItem('xxx')`静默返还一件该道具，如样板中的破墙镐和炸弹。
10. **能否装备：**对equips有效，请注意在装上装备后如果条件不再满足也不会自动卸下。和“能否使用”不同，此项留null表示任何情况下都可以装备。
11. **道具的装备属性：**在介绍此项之前，请先留意一下“全塔属性”中的“装备孔”一项。该项为一个字符串数组，最多允许6项（13×13样板）或8项（15×15样板）。每一项为装备的类型名称，建议是两个汉字（如“武器”、“防具”）。类型允许重复，如可以让勇士最多同时装备两块盾牌。
    *  装备类型：一个自然数，和前面的“装备孔”对应，如0表示武器，1表示防具。如果装备孔有重复的名称则这里也直接写名称（不用加引号），穿戴时会自动尝试寻找第一个同类型的空闲装备位，没有空闲的话会提示玩家先卸下一件。
    * 普攻动画：`project\animates`文件夹中任何一个文件的名称（不带后缀，但需要在全塔属性中注册过），只对第一个装备孔有效。普攻动画会播放在和勇士战斗的怪物位置处，如果是强制战斗的天降怪物，则会播放在勇士身上并跟随，请自行注意。详见“文件注册”使用动画。
    * 数值提升项：若干个键值对，表示该装备增加属性的常数值（支持负数）。7个常用属性可以通过下拉框选取，自定义的新属性也可以手动输入。
    * 百分比提升项：若干个键值对，表示该装备增加属性的百分比（支持负数，如填-10就表示减少10%），修改方法同上。
    * 装备对属性的影响原理：在穿脱装备时，会根据数值提升项和百分比提升项，分别调用`core.status.hero.xxx += yyy和core.addBuff('xxx', yyy)`这两个API（衰弱的附加和解除同理），而状态栏的显示值和战斗中的使用值则是`core.getStatus('xxx')`和buff值相乘再向下取整所得。
    * PC端按下Ctrl+0~9可以保存当前套装，Alt+0~9快速更换套装。

道具相关API请阅读[API列表](api)。

### 怪物属性

1.  **手册映射ID：**如前所述，npc48提供了“行走图朝向”来实现多朝向，而怪物则没有这么好的待遇。如果您制作了四种面朝不同方向但属性完全一致的怪物，那么您可以将其中三种怪物的“手册映射ID”填写为剩下一种怪物的ID，这样手册中（确切地说，`core.getCurrentEnemys()`函数的因变量）就会统一显示为剩下那一种怪物。
2.  **生命、攻防、金经：**如题，注意金经必须在“全塔属性”（快捷键B）中的“状态栏显示项”中也勾选才能真正被启用。持有幸运金币时打怪获得的金币翻倍，附加诅咒状态时打怪不获得金经。
3.  **加点：**若全塔属性勾选了“加点”，则此项为正数时将作为与该怪物每场战斗胜利后传递给“公共事件——加点事件”的参数（即那里的flag:arg1，默认表示加点的倍率），您可以自行修改该事件。
4.  **不可炸：**勾选后该怪物不会成为炸弹的目标，有阻击怪在场的情况下请务必给有战后事件的怪物（如机关门守卫和boss）勾选此项，否则玩家可能会偷梁换柱地炸掉该怪物并把阻击怪推过去打死来触发战后事件。
5.  **特殊属性：**一个由正整数组成的一维数组，您可以点击“勾选框编辑”按钮来修改它。所有特殊属性都定义在“脚本编辑——怪物特殊属性”，您可以在那里追加新的。它们大体分为四类：
    1.  手册中属性值的修正：（按照结算顺序）模仿、坚固、光环，修正后的属性也将被用于战斗，详见“脚本编辑——怪物真实属性”。
    2.  战损的修正：这类最多，先攻、魔攻、连击（次数为n）、破甲（比例为defValue）、反击（比例为atkValue，回合数为勇士的攻击回合数）、净化（倍数为n，1表示单纯无视护盾）、吸血（比例为value，是否加到自身为add）、仇恨（每场战斗的仇恨增值由全塔属性指定）、无敌、固伤（数值为damage）、支援。其中又以仇恨和固伤不能被护盾直接抵消而和无敌较为特殊，详见“脚本编辑——战斗伤害信息”。
    3.  战后的影响：中毒、衰弱、诅咒、仇恨（的累加和减半）、自爆、退化（扣减值分别为atkValue和defValue）、重生，详见“脚本编辑——战后脚本”和“公共事件——毒衰咒处理”。
    4.  阻激夹域捕捉：即对主角行走的妨害，详见“脚本编辑——阻激夹域伤害”，该函数也负责了血网（图块ID为lavaNet，请勿修改）的伤害。

阻激域的伤害都为value且在夹击之前结算，领域的形状和半径与光环一致。

如果需要更复杂的形状（如米字形激光），请自行研究该函数。

请勿给同一种怪物设置阻击和捕捉，否则其阻击只会变得类似固伤。

可以看到，怪物属性中有很多值是彼此互相冲突的。请自行注意，比如分散给各项而不要都吊死在三个value上。最后介绍一些和怪物相关的API：
``` js
    core.status.hero.flags.no_repulse = true; // 禁用阻击，包括伤害和后退效果
    core.status.hero.flags.no_laser = true; // 禁用激光
    core.status.hero.flags.no_betweenAttack = true; // 禁用夹击
    core.status.hero.flags.no_zone = true; // 禁用领域
    core.status.hero.flags.no_ambush = true; // 禁用捕捉
    core.getItem('amulet'); // 禁用血网等路障
    core.setEnemy('greenSlime', 'atk', 100); // 设置怪物属性，并计入存档
    core.getDamageString(enemy, x, y, floorId); // 获取某只怪的地图显伤字符串和颜色
    core.getCurrentEnemys(floorId); // 获取某层楼的（映射后）怪物列表，按战损递增排列
    core.hasEnemyLeft(enemyId, floorId); // 漏怪检测，两个参数都允许使用一维数组
    core.hasSpecial(special, test); // 检测special是否有test这一个特殊属性
```

如果您想在数据区的表格中追加新的属性项，或修改已有项的格式、范围和长短注释，请点击数据区顶部的“配置表格”按钮，并参照已有的项去追加和修改，具体可查阅[修改编辑器](editor)。

## 楼层属性（快捷键V）

![image](img/floorsv.jpg)

1. **楼层ID：**`project/floors`中的文件名，不允许使用中文也不能直接修改。修改方法见上图底部，修改后必须立即刷新浏览器页面。
2. **楼层名：**楼层在楼传、上下楼黑屏和浏览地图界面的名称。
3. **状态栏显示：**勇士在此楼层时状态栏左上角上楼梯图标右边的文字，允许使用中文，但请注意控制字数。
4. **地图宽度和高度：**可在表格最下方修改。
    * 如果地图被加宽或加高，则“偏移”表示右移或下移的格子数（左边缘或上边缘用空格子填补）。
    * 如果地图被减窄或减矮，则“偏移”表示左移或上移的格子数（被移出左边缘或上边缘的图块将丢失）。
5. **几个勾选框：**
    * 可楼传：如果不勾选，则此楼层禁止成为楼传的目标楼层，勇士在此楼层也禁止使用楼传。
    * 快捷商店：如果不勾选，则勇士在此楼层禁止快捷使用商店。事件中的“启用全局商店同时打开”不受影响，详见“插件编写——全局商店”。
    * 不可浏览：如果勾选，则此楼层无法通过PageUp/PageDown键浏览，也不会计入B键数据统计。
    * 不可瞬移：如果勾选，则勇士在此楼层无法用E键和单击瞬移，常用于用自动事件去监听勇士坐标时。
    * 是否是地下层：如果勾选，则非平面模式下勇士在此楼层原地使用楼传会传送到上楼点，详见“脚本编辑——楼层飞行”。
6. **首次到达事件、每次到达事件：**如题，详见“脚本编辑——切换楼层后”。
7. **并行处理脚本：**一个字符串，为勇士在此楼层时浏览器每帧都会执行一次（eval）的脚本，最快每秒60次。一般用来制作一些定时特效如bgs、bgv，详见“脚本编辑——并行脚本”。
8. **上下楼点：**两个自然数构成的一维数组，将作为“楼层转换”事件（在地图上以图块左下角出现绿色小方块作为标记）和“楼层切换”指令中“上下楼梯”以及非平面楼传的目标坐标。
    * 如果不设置，则在传送时会尝试从地图中搜索上下楼梯图块。因此当某个楼层没有楼梯或有多个楼梯时（如《[新新魔塔](http://ckcz123.com/games/xinxin/editor.html)》），请务必设置这个属性。点击“编辑”按钮从地图选点即可。
9.  **楼传落点：**格式和设置方法同上。如果设置了此项，则楼传在此层的落点将强制变为该点，无视平面模式下的离开点和上面的上下楼点以及该层实际的楼梯位置。
10. **地面图块：**可以填写任何一个图块ID，此项也会作为手册和剧情对话中的帧动画背景。
11. **色调：**一行四列的数组，前三项为小于256的自然数（分别表示红、绿、蓝），最后一项为0到1的浮点数表示不透明度，可以点击“编辑”按钮用取色器调色（见样板的主塔40层）。
    * 值得一提的是，很多事件也以颜色作为参数，这些都是可以使用取色器调色的。
12. **天气：**一行两列的数组，第一项为字符串“rain”、“snow”或“fog”，第二项为不大于10的正整数，分别表示1—10级的雨天（见样板的主塔40层）、雪天（由大小不一的白色实心圆组成，详见样板1层）和雾天（由`project/materials/fog.png`放大后随时间加入不规则扰动得到）。
    * 色调层在天气层上方、UI层下方（如不透明色调会遮盖天气，浏览地图看不到色调），关于图层的详细说明，参见“个性化”
13. **背景音乐：**如题，当在游戏中触发楼层切换时（包括读档），如果`flag:__color__、flag:__weather__、flag:__bgm__`没有值，游戏当时的画面色调、天气、背景音乐就会变为楼层属性中的这三个设置项，详见“脚本编辑——切换楼层中”。
14. **宝石血瓶效果：**如题，必须填写且必须为非负数。此项的用法为core.status.thisMap.ratio，请参考四种血瓶和三种宝石的捡拾效果。
    * 您还可以将其用于其他各种场合作为系数，如血网的伤害、中毒后每步的损血等。
15. **楼层贴图：**
    * 由于样板提供的图块只有32×32px和32×48px两种尺寸，且后者只能画在事件层，每个图块最多只能有4帧，因此对于一些大图块十分不便。
    * 你可以使用“楼层贴图”，该项允许您使用任何尺寸、任何帧数的素材，唯一的缺点是不支持伸缩、移动跳跃和淡入淡出效果。
    * 点击“编辑”按钮进入事件编辑器，每张图片的写法为（可从入口方块拖出，然后双击预览第一帧的效果）：
        1.  图片名（name）：如题，图片需要放在`project/images`文件夹并注册。
        2.  翻转（:x/:y/:o）：您可以对贴图的每帧进行三种翻转，当然，帧顺序在原图中依然是从左到右的。
        3.  图层（bg/fg/auto）：此项决定贴图绘制在哪个图层，您可以全部画在背景层或前景层。也可以选择“自适配”让贴图的上半部分画在前景层，下半部分画在背景层，比如树木等。如果选择了自适配，最好让下面的绘制坐标和宽高都是32的倍数。
        4.  绘制坐标（x，y）：贴图在地图中的左上角像素坐标，譬如x和y都填32则表示贴图左上角和“地图左上角格子的右下角”重合。
        5.  初始禁用（Y/N）：如果勾选了此项，则此贴图初始时不显示，您可以在事件中再将其显示出来。
        6.  裁剪起点坐标（x，y）和宽高（w，h）：此项规定了贴图在按帧切分前从原图中取哪一部分，x和y为所取部分在原图中的左上角坐标（不填视为两个0），w和h为所取部分的宽高（不填表示一直取到右下角）。
        7.  帧数（frame）：不填视为1，如果填写了大于1的整数，就会把上述裁剪得到的结果再从左到右等分为若干份，并在实际绘制时从左到右逐帧（可能还带有翻转）循环绘制，每帧的持续时间和其他图块一致。
    * 贴图本身只具有观赏性，您仍然需要使用空气墙等手段去控制其绘制区域各个点的通行性。
    * 在使用贴图来表现魔龙和章鱼这类大型怪物时，可以预先注册两种一样属性的怪物。一种透明，另一种为缩小到32×32px且只有两到四帧的版本。将前者画在地图上，怪物属性的“手册映射ID”填后者，最后在该点的战后事件中隐藏贴图即可。
    * 你可以在插件重复写`drawBg`和`drawFg`函数以控制贴图和图块的绘制顺序，详见[脚本](script)。
    ``` js
    ////// 绘制背景层 //////
    core.maps.drawBg = function (floorId, ctx) {
        var onMap = ctx == null;
        if (onMap) {
            ctx = core.canvas.bg;
            core.clearMap(ctx);
        }
        this._drawBg_drawBackground(floorId, ctx);
        // ------ 调整这两行的顺序来控制是先绘制贴图还是先绘制背景图块
        // 后绘制的覆盖先绘制的。
        this._drawFloorImages(floorId, ctx, 'bg');
        this._drawBgFgMap(floorId, ctx, 'bg', onMap);
    }
    ```

## 全塔属性（快捷键B）

全塔属性共分为四部分：文件注册、初始勇士、全局数值、系统开关，您可以随时折叠其中任何一个部分。

![image](img/firstdatab.jpg)

### 文件注册

这部分基本上都是经由多选框半自动完成的，下面逐一讲解：
1. **楼层列表：**`project/floors`文件夹中的文件名（不含后缀），此数组的顺序决定了楼传和上下楼器（fly、upFly、downFly）的顺序。
    * 如果您不慎将勇士的出生楼层注销了或不慎删除了某些楼层的js文件，导致编辑器页面打开后一片白屏，请手动打开`project/data.js`去小心地修改floorIds以和实际的文件名相匹配，并将出生楼层改为一个存在的楼层。
    * 其他更复杂的白屏请在控制台根据报错信息（安卓手机则使用ES文件浏览器查看日志）去小心地修改文件（如某个楼层文件有问题则可以注销它），如果难以独立解决，欢迎加QQ群959329661寻求帮助。
2. **使用图片：**`project/images`文件夹中的文件名（需要后缀，必须全英数），单击“编辑”按钮，编辑器会自动扫描文件系统中格式合适的图片（如jpg、png和gif）。
    * 您可以预览并将需要的图片勾选。请注意，勇士的初始行走图必须在这里注册。另外，`winskin.png`只许替换为相同规格的图片而不要注销，否则道具商店插件无法正常绘制。
3. **额外素材：**`project/tilesets`中的文件名（需要后缀，只支持png）。
    * 注册方法同上，最大的区别在于这个数组的顺序必须保持好。如果随意调换其中的顺序，或注销不在数组末尾的图片，就会导致地图上最终呈现的素材发生错位。因此，新勾选的图片总会被自动追加到数组的末尾。
    * 比起常规素材，额外素材最大的好处有几点：
        1. 图片没有数量限制。常规素材的总数量最多只允许不到一千个，而额外素材每张图片上的图块数量最多允许一千个。
        2. 查看和绘制更为方便。常规素材每个图块独占一行，每列为一帧，导致不方便观察，且用多个图块拼图必须逐图块绘制。额外素材都是静止的，所以每个图块只占一格，多个图块可以在准备素材时就直接以相邻的样子绘制在同一张图片上，绘制地图时直接从素材区拖动来批量框选，再在地图区单击成片绘制或拖动平铺。
        3. 批量替换也更为方便。譬如您如果想制作形如“一场大战/天灾过后/多年以后，村庄/城镇化为了一片废墟”的效果，可以预先准备两张甚至更多张相同规格的额外素材图片，然后在适当的时候遍历某个/某些楼层的图块ID，将以“X1”开头的图块统一替换为“X2”开头等。发布单机版游戏时，您也可以提供多张相同规格的额外素材图片供玩家直接替换。当然，使用这类做法的话就没法再使用“额外素材合并”了。
    * 当您即将发布您的游戏时，可以使用“额外素材合并”工具将用到的额外素材图块重新拼成更少的图片，以减少游戏体积和加快在线游戏的加载。执行此操作后，额外素材的图块ID和数字（包括第一位）都将被打乱，因此请勿在制作中以任何方式直接引用其ID和数字。如确有此需求，请间接引用、等量代换。
4. **使用动画：**`project/animates`文件夹中的文件名（不含后缀，请注意与`animates.png`相区分）。
    * 要使用动画，您可以使用“RM动画导出”工具从RPG Maker XP 1.03及其制作的游戏中导出动画，也可以用动画编辑器修改已有的动画或用图片新建。但这些办法都只适用于Windows系统，非Windows系统建议直接从我们的官网下载他人的作品取用其中的动画。
    * 每个动画将其用到的各张图片直接以base64硬编码进同一个animate文件，每个动画为一个animate文件。这样做的缺点是如果多个动画使用了相同的图片那么图片会被重复存储，优点则是跨作品迁移动画更加方便。animate文件为文本文件，文件末尾记录了动画的帧信息，文件开头则记录了动画的伸缩比和唯一音效（会和动画同时开播）。
    * 在导出动画时，会出现一个输入框并提示动画的唯一音效名。不管该文件名是什么语种，请直接点击下一步。音效文件会被尝试自动复制，随后您只需手动注册该动画和音效并将音效文件名改为全英数。
    * 可以在编辑器中预览动画和修改音效。
    * 可以使用如下动画相关的脚本对动画进行处理，或在事件中使用「播放动画」事件。
    ``` js
        core.drawAnimate(name, x, y, alignWindow, callback);
        // 播放一个动画，name为不带后缀的动画文件名，x和y为播放的格子坐标。
        // alignWindow表示该坐标在大地图中是绝对坐标还是相对坐标，填true表示相对坐标。
        // 相对坐标模式下，坐标应填写为小于13或15的自然数，譬如对13×13样板来说，
        // 填两个6就会强制播放在视野中心。
        // callback为动画播放完毕后(和音效无关)的回调函数，因为动画播放本身是异步的。
        core.drawHeroAnimate(name, callback); // 和上面类似，但该动画会跟随勇士移动。
        // 每场战斗后，都会根据怪物坐标尝试用前者播放普攻动画。若坐标不存在
        // （天降强制战斗），则会尝试用后者播放。看上去就像勇士在打自己，请自行注意。
        core.stopAnimate(id, doCallback); // 停止一段动画
    ```
5. **使用音乐：**`project/bgms`文件夹中的文件名（需要后缀，默认只支持wav、mp3和ogg）。
    * 如果玩家使用的是手机且没有连接WiFi（iOS和部分浏览器无法获知网络状态，将始终视为流量党），那么背景音乐默认不会开启，可以在标题画面点击右下角的圆形按钮来开启。
    * 发布到官网的作品还可以从<https://h5mota.com/music/>远程加载背景音乐，您可以点击此链接试听和下载其他作品的背景音乐。
    * 是否启用远程加载、以及启用时远程加载的根目录，由main.js指定。因此从官网下载其他作品的离线版本后请先关闭远程加载功能，才能正常加载本地注册的音乐。
    * 使用core.material.bgms可以查看所有的背景音乐，如果需要同时播放多个背景音乐并独立控制时刻和音量，请直接对它们使用play()方法并修改其currentTime（可读）和volume属性。
6. **使用音效：**`project/sounds`文件夹中的文件名（写法同上）。和动画不同，音效的播放不支持回调，只支持提前停止，而且一停就会停止所有的。
    * 音效的音量是固定的，无法调节。此外，样板没有背景音效（bgs、bgv）的默认实现。如有需求，请使用并行脚本处理。
    * 音乐和音效在使用多选框注册时都支持试听，您可以看到它们的总时长和已播时长（精确到秒），从而指定音乐的开播秒数或配合使用“等待n毫秒”事件或并行脚本处理。
7. **使用字体：**project\fonts文件夹中的文件名（只支持ttf格式，不写后缀）。不建议给在线游戏版本添加中文字体，因为文件真的很大……
8. **文件映射：**如前所述，样板所有需要加载的文件名都必须全部是英文或数字。这一项允许你给文件取别名，别名可以使用任何语言的文字。
9. **装备孔：**见“道具的装备属性”。
10. **标题音乐：**如题，请注意部分浏览器不会在刚打开某个页面时就播放音频，必须用户操作一下才行。
11. **主样式：**一些css设置项，单击“编辑”按钮可以看到具体含义和用法，这里不再赘述（横竖屏标题画面背景支持gif动图）。
12. **游戏名：**标题画面和网页选项卡上显示的名字，可以和官网别的作品重名。
13. **唯一英文标识符：** **必须修改，且不得和官网别的作品重名**。只能使用字母数字下划线，如果您不确定一个标识符是否已被别的作品使用，请输入[https://h5mota.com/games/xxx](https://h5mota.com/games/xxx)，如出现404就说明此项未被使用。
14. **游戏版本：**当您的游戏发生版本更迭后，旧版本的存档直接读取可能会出现bug.因此届时您可以修改此项来让旧存档无法直接读取，只能回放其录像。
15. **难度分歧：**单击“编辑”按钮进入事件编辑器，每个难度分为以下几项。
    * 名称：标题界面单击“开始游戏”后出现的二级菜单中的文字。一般为该难度的最简要的介绍，如减伤比例、初始福利等。
    * 简写：横屏状态栏左下角（竖屏右下角，也作为数字键切换按钮）和存读档界面缩略图上的文字，也会出现在在线游戏排行榜和旧版官网的作品卡片上。允许使用中文但请注意控制字数，用core.status.hard表示。
    * 变量hard值：若同一结局有多个难度有人通关，则上述排行榜只统计此值最高的难度。
    * 颜色：上述“简写”的颜色，用`core.status.hero.flags.__hardColor__`表示，默认为红色。详见“脚本编辑——更新状态栏”。
    * 事件：此事件比下述的“开场剧情”更早执行，一般用来设置初始福利。
    * 如果将难度分歧数组留空，那么标题界面单击“开始游戏”就会直接进入开场剧情。

### 初始勇士

1. **初始楼层、朝向和坐标：**如题，请注意初始楼层必须在上述的“楼层列表”中。初始坐标一般通过右击地图上的空地快速绑定，但您也可以手动在这里填写负数或超出初始楼层宽高的值。然后使用“无视地形移动勇士”或“跳跃勇士”等改变勇士位置的事件指令，做出“勇士从地图外进入”的演出效果。
    * 如需根据难度分歧或用户选项来改变它们，请在“开场剧情”中修改core.firstData.floorId和core.firstData.hero.loc
2. **行走图：**如题，必须在“使用图片”中注册过。宽高必须为4的倍数，宽度至少为128px（即每帧32px）。高度不限，剧情对话中和状态栏中会尝试保持比例压缩到每帧32px宽。
    * 在游戏中，勇士当前的行走图文件名用core.status.hero.image表示（只读）。
3. **帧动画：**勾选此项后，勇士在剧情对话中（朝上视为朝下）和原地不动时会循环播放四帧踏步动画，一般用于长翅膀的勇士。
4. **勇士名：**如题，也会作为剧情对话中`\t[hero]`的默认标题。
5. **初始等级：**如果开启了自动进阶功能，请不要修改此项。
6. **生命上限、魔力上限、初始生命/魔力/攻防/护盾/金经：**如题。注意生命上限和金经需要在系统开关中勾选后才会启用，魔力上限填负数代表没有上限。
7. **初始装备、游戏变量：**建议留空（事件中的变量初始时都会视为0，脚本中也有办法做到）。
8. **永久道具、消耗道具、初始拥有装备个数：**点击“注释”按钮，按照提示进行修改。
9. **标题事件：**需要配合系统开关中勾选“标题开启事件化”来使用，可以在“开始游戏”、“读取存档”之余添加一些额外的功能。如成就系统、音像和回想鉴赏，但这并不是唯一的方法，请自行研究。
10. **开场剧情：**会在难度分歧事件之后执行，可以在这里设置各种变量的初始值、穿上初始拥有的装备、隐藏勇士和一些初始不希望显示的图层块、追加跟随者等。
11. **全局商店：**详见“QTE与全局商店”。
12. **等级提升：**需要配合系统开关中勾选“等级”、“经验”和“升级”来使用，每个等级分为以下几项：
    * 需求：刷新状态栏时，如果勇士当前等级是此等级的前一级，且经验值大于等于此需求，就会触发升级。
    * 称号：状态栏显示的等级默认是个正整数，会尝试替换为这里的称号（调用core.getLvName()函数），请注意控制字数。
    * 是否扣除经验：如果勾选了此项，则触发升级时经验值会扣除需求值。
    * 事件：触发升级时执行的事件，如全面提升属性。

### 全局数值

![image](img/values_flagsb.jpg)

这个类型的数值会保存在core.values中，可以直接在游戏中修改。

1. **血网伤害和中毒伤害：**如题，如果不想用常数，请修改“脚本编辑”的“阻激夹域伤害”和“每步后操作”。
2. **衰弱效果：**填小于1的正数代表扣减的比例，否则为扣减的常数。扣减和恢复的原理和装备相同，详见“公共事件——毒衰咒处理”、解衰药水和万能药水的使用效果。
3. **三种宝石和四种血瓶的值：**如题，此值为基础值。实际效果还需乘以楼层属性最下面的“宝石血瓶效果”（限即捡即用类，详见这七种道具的属性。此外，B键数据统计是按照此值乘以“宝石血瓶效果”统计的，与道具的实际效果无关，请自行注意。
4. **反击、破甲、净化比例：**如果反击、破甲、净化怪物没有指定atkValue、defValue和n，就会用这三个值。请注意反击的总回合数是勇士的攻击回合数，净化比例填1表示单纯无视护盾。
5. **仇恨增值：**每进行一场战斗，core.status.hero.flags.hatred的增加量。如果不想用常数，请修改“脚本编辑——战后脚本”。
6. **全局帧动画时间：**即怪物和NPC的振动速度，建议改为300毫秒。
7. **竖状态栏自绘行数：**需要配合系统开关“开启自绘状态栏”使用，建议改为4.

### 系统开关

这个类型的开关会保存在core.flags中（只读），请注意和core.status.hero.flags相区分。如需在游戏中修改，请使用“设置系统开关”事件。

1. **状态栏显示项：**如题，总个数请控制在12个以内，否则竖屏可能塞不下。
    * 这些项的图标都在`project\materials\icons.png`中。该文件夹下也提供了一个`icons_old.png`可供替换。
    * “血限”、“金币”和“经验”必须勾选才会启用（指会处理生命溢出、金经会显示在手册、打怪也会掉落），
    * 必须勾选“升级”才会启用自动进阶，“升级扣除模式”如果不勾选就会同时显示下一级的需求（NEXT）和当前经验（EXP，如果勾选了的话），否则会只显示两者的差（依然用NEXT图标）。
    * 如果同时勾选了“钥匙”和“绿钥”，则每种钥匙的数量都会被缩小尺寸显示，因此如非必要请不要勾选“绿钥”。
2. **楼传需在楼梯边：**如果勾选了此项，则只有勇士在楼梯旁边（如果开启了下述的平面模式则还包括四种三帧箭头旁边）时才允许使用楼传。
    * 请注意，此项是在楼传道具使用条件之外额外进行的判定，目的是给出不同的提示信息。
    * 因此如果您要修改楼传的使用条件（指总的使用条件，具体能否飞到某层的条件则在“脚本编辑——楼层飞行”），则可能需要一并取消此勾选。
3. **楼传开平面模式：**如果勾选了此项，则勇士在使用楼传飞往某层时会落在上次离开该层的位置。此外，上述“在楼梯边”也会包括“在四种三帧箭头旁边”。
4. **铁门不消耗钥匙：**如果勾选了此项，则铁门无需钥匙也能撞开。
5. **首次道具进行提示：**勾选后，首次捡到非即捡即用类道具都会弹窗提示（晚于afterGetItem事件被执行）。
6. **状态栏装备按钮：**勾选后，状态栏的楼传按钮会变为装备栏按钮，但玩家仍然可以双击道具栏来呼出装备栏。
7. **加点：**勾选后怪物的加点值会在“脚本编辑——战后脚本”中作为参数core.status.hero.flags.arg1被传递给“公共事件——加点事件”。
8. **负伤：**勾选后，战斗结束时如果勇士的护盾没有完全被打破，则剩余的护盾值会加到其生命上。所以勾选此项后，护盾可以这样“间接”抵消掉仇恨伤害和固伤。
9. **夹击不超伤害值：**勾选此项后，夹击伤害将封顶至夹击怪的战损。同时被四只怪夹击时，取两个战损中较小的。
10. **二分临界：**我们知道，打败怪物所需的回合数，取决于勇士的攻击减去怪物的防御。这个值并非每增大1都能使回合数减少，因而有了“临界”的说法，即“再至少增加多少攻击力，才能减少回合数”。然而，当您修改“脚本编辑——战斗伤害信息”函数后，攻击力的增加可能反而导致回合数也增加，于是临界值计算出错。您可以勾选此开关来修复，代价是游戏可能较卡，请自行权衡。
11. **标题开启事件化：**勾选此项后，标题画面将改为执行前述的“标题事件”，请自行研究。
12. **开启自绘状态栏：**勾选此项后，状态栏将改用“脚本编辑——自绘状态栏”来绘制，同时“脚本编辑——点击状态栏”也将启用，请自行研究。
13. **三个显伤：**略，玩家依然可以在设置菜单中开关之。
14. **允许轻按：**勾选此项后，玩家可以按下空格/大键盘数字7/双击勇士来拾取相邻的唯一道具，或面前的道具。
15. **允许穿透楼梯：**在狭窄的区域拦路放置一个可通行的“楼层转换”事件时（图块左下角出现绿色标记），玩家可能希望勇士能直接走过去。您可以逐个去设置其能否被这样走过，或者让其依据本勾选项。
    * 值得注意的是，当玩家从允许穿透的楼梯向一个不可走的方向（如旁边是墙，或不勾选下一个开关时的致命领域）手动寻路时，可以停在楼梯上（进而再轻按拾取周围物品等）。不建议您利用这类极端情况去迫使玩家进行非常规操作，毕竟穿透楼梯和不能踏入致命领域的本意是方便玩家，不是么？
16. **允许将死领域：**“脚本编辑——阻激夹域伤害”会将地图中每个点的阻激夹域和血网伤害加总，如果不勾选此开关，则当勇士生命小于等于相邻空格子的总伤害（没有则视为0）时，勇士无法走向该格子。
    * 值得注意的是，这种判定方式并没有考虑“走这一步后、结算该点伤害前”可能的加血或该点伤害变化，因此如有必要可根据“脚本编辑——每步后操作”去修改core.canMoveHero()函数。
17. **允许瞬移：**若不勾选此开关，将全程禁用瞬移功能。一般只建议在需要的楼层逐层勾选禁止瞬移。
18. **伤害禁用商店：**勾选此项后，每当勇士踩到阻激夹域和血网并受到伤害时，所有全局商店都将被禁用，需要重新去启用（譬如勇士去撞击该商店的实体NPC）。
19. **虚化前景层：**前景层会遮挡事件层，这对魔塔来说有时可能不太友好。勾选此项后，事件层有东西（如道具）时将虚化该格子的前景层，使得该东西以半透明状态可见。

上面就是整个样板中的各个元件说明。通过这种方式，你就已经可以做出一部没有任何事件的塔了。

尝试着做一个两到三层的塔吧！

==========================================================================================

[继续阅读下一章：事件](event)
