# Cocos示例学习笔记 #


----------
2020/6/21 2:13:52 
## 一、图集Atlas ##

图集文件可以通过 TexturePacker 等图集工具生成。

图集文件有两个，一个是plist后缀文件，查看内容猜测是关于图片格式信息等相关内容；一个是meta文件，查看内容和plist有重合部分，但内容更详细（包含图片的uuid，目测和单个文件的meta似乎没有区别）.

Cocos中加载图集文件中的图要写入Sprite中的atlas和sprite frame两栏内容.单纯的图片则不需要写入atlas.

*来自[https://github.com/cocos-creator/example-cases/blob/master/assets/resources/readme/AtlasSprite.md](https://github.com/cocos-creator/example-cases/blob/master/assets/resources/readme/AtlasSprite.md)*

> 如果你是游戏程序员，你应该知道openGL载入纹理图片的时候，所用内存是会自动扩张到2的n次方的。比如，如果你的资源素材是10×10px，载入后，将会按16×16规格载入图片。如果资源素材是64×65，那么就会按照64×128载入了。假设资源是RGBA8888的图片格式来算，（255，255，255，255）代表（r, g, b, a）,一个点就使用了8位×4=32位。相当于4个byte。按刚才最后一种情况，你浪费的空间是64×（128-65）×4 /1024 = 15K，别说我的内存你无所谓。。。移动设备上的游戏，内存还是吃紧。理论上，如果每一张图的长宽都恰好是2的n次方数值，那很完美，没有任何浪费。但是，你懂的。所以我们需要把很多小图拼接到一张大图里，比如（2048×2048）然后到代码中取其所需。这样，浪费掉的空间就很小了。
> 
> OK，你说你不懂openGL，那你想想网页设计师前台制作css的时候，为了提高载入速度，往往把很多小图拼接成一张大图，一次载入，减少I/O，提高速度。这个好懂吧。
> 
> 所以不管怎么样，“拼大图”这个流程不可少。
> 
> *来自[https://blog.csdn.net/azhou_hui/article/details/9113907?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.nonecase](https://blog.csdn.net/azhou_hui/article/details/9113907?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.nonecase)*


----------

## 二、Sliced Sprite ##

Type指定为Sliced类型的图片，在其meta文件中的borderTop/Bottom/Left/Right属性不全为0，这些值决定了保持不拉伸和拉伸填充的区域的边界.

----------

## 三、Anchor Point ##

**1、convertToWorldSpace**<br>
将一个相对于节点左下角的坐标位置转换到世界空间坐标系。 这个 API 的设计是为了和 cocos2d-x 中行为一致，更多情况下你可能需要使用 convertToWorldSpaceAR

|  meta   | description  |
|  ----  | ----  |
| 返回  | Vec2 |
| 定义于  | cocos2d/core/CCNode.js:3367 |
| 废弃（Deprecated） | since v2.1.3 |

**参数列表** <br>
* nodePoint Vec2

**示例** <br>
```javascript
var newVec2 = node.convertToWorldSpace(cc.v2(100, 100));
```

**2、convertToWorldSpaceAR**<br>
将节点坐标系下的一个点转换到世界空间坐标系

|  meta   | description  |
|  ----  | ----  |
| 返回  | Vec3 \| Vec2 |
| 定义于  | cocos2d/core/CCNode.js:3315 |

**参数列表** <br>
* nodePoint Vec3 | Vec2
* out Vec3 | Vec2

**示例** <br>
```javascript
var newVec2 = node.convertToWorldSpaceAR(cc.v2(100, 100));
var newVec3 = node.convertToWorldSpaceAR(cc.v3(100, 100, 100));
```

来自 [<https://docs.cocos.com/creator/api/zh/classes/Node.html#converttoworldspace> ](https://docs.cocos.com/creator/api/zh/classes/Node.html#converttoworldspace )

在浏览器中测试时，发现其坐标显示和编辑器中设置的position等数值完全不一致，经过网页开发者工具debug发现在网页中获取的canvas的宽高和编辑器中设置的宽高并不一致。然后发现编辑器中在层级管理器Canvas节点特有的Canvas属性中的Fit Height默认为勾选，如果Fit Height/Width中有任何一个勾选就会导致最终呈现的height和width是经过适配屏幕后的数值，就会导致和实际数值和编辑器中的数值不一致.<br>
将这两个选项全部不勾选即可获得预期数值.<br>
并且发现这个实际值会受到（parent）旋转的影响（合乎情理）.
