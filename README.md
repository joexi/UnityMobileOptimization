# UnityMobileOptimization
Translate of Unity documentation "PlatformSpecific/Mobile Developer Checklist/Optimazation"

## Optimizations
## 优化

Just like on PCs, mobile platforms like iOS and Android have devices of various levels of performance. You can easily find a phone that’s 10x more powerful for rendering than some other phone. Quite easy way of scaling:

就像PC那样，iOS和安卓这样的移动平台也会有不同（配置）等级的设备。你可以很容易就找到一台拥有10倍于其他手机渲染速度的手机。（优化）可以简单归纳为：

* Make sure it runs okay on baseline configuration

  确保在最基本的配置下能够正常运行

* Use more eye-candy on higher performing configurations:

  在更高的配置上展现更多的效果

	* Resolution
 
	  分辨率

	* Post-processing
  
	  后期处理

	* MSAA
  
	  抗锯齿
  
	* Anisotropy
  
	  各向异性
  
	* Shaders
  
	  着色器
  
	* Fx/particles density, on/off
  	
	  特效/粒子密度，开/关

### Focus on GPUs
### 关于GPU

Graphics performance is bound by fillrate, pixel and geometric complexity (vertex count). All three of these can be reduced if you can find a way to cull more renderers. Occlusion culling could help here as Unity will automatically cull objects outside the viewing frustum.

图像性能受制于填充率，像素以及几何复杂度（定点数）。以上3点都可以通过减少渲染器来降低。遮挡剔除就是如此，Unity会自动将视锥之外的物体（在渲染中）裁去。

On mobiles you’re essentially fillrate bound (fillrate = screen pixels * shader complexity * overdraw), and over-complex shaders is the most common cause of problems. So use mobile shaders that come with Unity or design your own but make them as simple as possible. If possible simplify your pixel shaders by moving code to vertex shader.

在移动设备上你你的填充率实质上是收到限制的（填充率 = 屏幕像素 * 着色器复杂度 * 绘制次数），并且过度复杂的着色器是最容易造成问题的。所以使用那些Unity提供或者你自己撰写但足够简单的适用于移动平台的着色器。如果可以的话，把你的像素着色器代码都迁移到定点着色器中。

If reducing the Texture Quality in Quality Settings makes the game run faster, you are probably limited by memory bandwidth. So compress textures, use mipmaps, reduce texture size, etc.

如果在Quality Settings中降低图像的品质能使得游戏运行得更流畅，那么你可能受制于内存带宽。那么压缩这些图像，使用mipmaps，或者减小图像的大小等。

LOD (Level of Detail) - make objects simpler or eliminate them completely as they move further away.
LOD (Level of Detail) - 当对象在很远处时，使得他们更为简单或者降低他们的复杂度。

#### Good practice
#### 好的习惯

Mobile GPUs have huge constraints in how much heat they produce, how much power they use, and how large or noisy they can be. So compared to the desktop parts, mobile GPUs have way less bandwidth, low ALU performance and texturing power. The architectures of the GPUs are also tuned to use as little bandwidth & power as possible.

移动设备的GPU对于他们的发热，能耗，以及大小，噪音都有很高的要求。所以相较于台式设备移动谁被拥有更低的带宽，更差计算能力和图像处理能力。GPU的架构师也偏向于尽可能选用更小的带宽和能耗。

Unity is optimized for OpenGL ES 2.0, it uses GLSL ES (similar to HLSL) shading language. Built in shaders are most often written in HLSL (also known as Cg). This is cross compiled into GLSL ES for mobile platforms. You can also write GLSL directly if you want to, but doing that limits you to OpenGL-like platforms (e.g. mobile + Mac) since there currently are no GLSL->HLSL translation tools. When you use float/half/fixed types in HLSL, they end up highp/mediump/lowp precision qualifiers in GLSL ES.

Unity针对OpenGL ES 2.0进行了优化，他使用了GLSL ES（类似于HLSL）着色器语言。内置的着色器通常是由HLSL（通常称为Cg）撰写的。GLSL ES是针对移动平台交叉编译而成。你可以直接写GLSL语言如果你愿意，不过这么做使得你局限于OpenGL类的平台（移动设备 + Mac）因为没有能够将GLSL转换为HLSL的工具。当你在HLSL中使用float/half/fixed 类型时，他们最终在GLSL ES中会使用highp/mediump/lowp。

#### Here is the checklist for good practice:
#### 这里是保持良好习惯所需注意的事项

* Keep the number of materials as low as possible. This makes it easier for Unity to batch stuff.

	尽可能保证材质的数量不会过多。这会简化Unity的批处理操作。
	
* Use texture atlases (large images containing a collection of sub-images) instead of a number of individual textures. These are faster to load, have fewer state switches, and are batching friendly.

	使用纹理图集（一张大图中包含了许多小图）而不是使用许多单张的小图。这样加载起来会更快,也更利于执行批处理。
	
* Use Renderer.sharedMaterial instead of Renderer.material if using texture atlases and shared materials.
Forward rendered pixel lights are expensive.

	使用纹理图集或者共享的材质时，使用Renderer.sharedMaterial而不是Renderer.material，正向渲染的像素光（对性能的）开销很大。
	
	* Use light mapping instead of realtime lights where ever possible.
	
	如果可能的话尽量使用光照贴图而不时实时的光照
	
	* Adjust pixel light count in quality settings. Essentially only the directional light should be per pixel, everything else - per vertex. Certainly this depends on the game.
	
	在quality settings中调整像素光的数量，确保只有方向光可能是逐像素，而其他的是逐顶点。当然这也取决于游戏（的需求）。
	
* Experiment with Render Mode of Lights in the Quality Settings to get the correct priority.

	对Quality Settings中灯光的渲染模式进行试验来获得正确的优先级。
	
* Avoid Cutout (alpha test) shaders unless really necessary.

	避免裁剪类型（alpha检测）的着色器除非他们是必须的。
	
* Keep Transparent (alpha blend) screen coverage to a minimum.
	
	确保屏幕上（包含）透明度（alpha混合）的部分尽可能的小。
	
* Try to avoid situations where multiple lights illuminate any given object.

	尽量避免多个光源同时照亮一个物体的情况。
	
* Try to reduce the overall number of shader passes (Shadows, pixel lights, reflections).

	尽量降低着色器中pass的数量（阴影，像素光，反射等）。
	
* Rendering order is critical. In general case:
	
	渲染的顺序是十分重要的，一般来说（是这样的）：
	
	* fully opaque objects roughly front-to-back.

		完全不透明的物体大致上从前往后渲染
	
	* alpha tested objects roughly front-to-back.
	
		alpha检测的物体大致上从前往后渲染
	
	* skybox.
	
		天空盒
		
	* alpha blended objects (back to front if needed).
	
		alpha混合的物体（有必要的话从后往前渲染）
		
* Post Processing is expensive on mobiles, use with care.

	移动设备上的后期处理开销非常之大，使用的时候需要谨慎。
	
* Particles: reduce overdraw, use the simplest possible shaders.

	粒子：减少那些重复绘制，尽可能使用那些最简单的着色器。

* Double buffer for Meshes modified every frame:

	对于每一帧都会被修改的Meshes实行双缓冲:

	```
	void Update (){
	  // flip between meshes
	  bufferMesh = on ? meshA : meshB;
	  on = !on;
	  bufferMesh.vertices = vertices; // modification to mesh
	  meshFilter.sharedMesh = bufferMesh;
	}
	```
	
### Shader optimizations
### 着色器优化
Checking if you are fillrate-bound is easy: does the game run faster if you decrease the display resolution? If yes, you are limited by fillrate.

检查你是不是受限于填充率的方式很简单：游戏是否在你降低分辨率后执行的更快？如果是，那么你（的性能）就受限于填充率

Try reducing shader complexity by the following methods:

尝试通过以下的方式来降低着色器的复杂度

* Avoid alpha-testing shaders; instead use alpha-blended versions.

	避免使用alpha-testing着色器，应该使用alpha-blended类型的。
	
* Use simple, optimized shader code (such as the “Mobile” shaders that ship with Unity).

	使用简单，优化过的倬测器代码（比如Unity提供的"mobile"着色器）
	
* Avoid expensive math functions in shader code (pow, exp, log, cos, sin, tan, etc). Consider using pre-calculated lookup textures instead.

	避免在着色器代码中使用消耗较大的数据运算（pow,exp,log,cos,sin,tan等）考虑使用计算好的纹理。
	
* Pick lowest possible number precision format (float, half, fixedin Cg) for best performance.
	
	选用尽可能低精度的数值类型（cg中的float，half，fixed）来提升整体的性能表现
	

## Focus on CPUs
## 关于CPU

It is often the case that games are limited by the GPU on pixel processing. So they end up having unused CPU power, especially on multicore mobile CPUs. So it is often sensible to pull some work off the GPU and put it onto the CPU instead (Unity does all of these): mesh skinning, batching of small objects, particle geometry updates.

很多游戏都是由于GPU像素处理的问题受到了限制。所以他们开始使用那些没有使用的CPU算力，尤其是在那些多核心的CPU上。所以把一些GPU上的任务迁移到CPU上进行运算是十分明智的（Unity做了这些）网格蒙皮，小物体的批处理，粒子几何体更新。

These should be used with care, not blindly. If you are not bound by draw calls, then batching is actually worse for performance, as it makes culling less efficient and makes more objects affected by lights!

应该小心谨慎而不是盲目的使用。如果你没有受制于drawcall的次数，那么批处理反而对性能有损耗，这会使得裁剪更抵消并且使更多物体收到光照的影响。

### Good practice
### 好的习惯

* FindObjectsOfType (and Unity getter properties in general) are very slow, so use them sensibly.

FindObjectsOfType（以及其他unity中泛型方式获取属性）是非常慢的，所以要小心掉使用他们。

* Set the Static property on non-moving objects to allow internal optimizations like static batching.

将那些不会移动的物体设置为“静态”属性使得他们可以像静态对象那样进行批处理优化

* Spend lots of CPU cycles to do occlusion culling and better sorting (to take advantage of Early Z-cull).

花费大量的CPU周期进行遮挡剔除的计算以及更好的渲染排序（利用Early Z架构的裁剪）

### Physics
### 物理引擎

Physics can be CPU heavy. It can be profiled via the Editor profiler. If Physics appears to take too much time on CPU:

物理引擎会大量消耗CPU资源。这可以通过编辑器下的Profiler工具进行监测。如果物理引擎出现了在CPU层面消耗大量时间，那么：

* Tweak Time.fixedDeltaTime (in Project settings -> Time) to be as high as you can get away with. If your game is slow moving, you probably need less fixed updates than games with fast action. Fast paced games will need more frequent calculations, and thus fixedDeltaTime will need to be lower or a collision may fail.

（在in Project settings -> Time中）将Time.fixedDeltaTime在能接受情况下设置的高些。如果你的游戏运行的很慢，那么你可能需要降低fixed updates的频率了。快节奏的游戏通常需要更实时的计算，这样的话fixedDeltaTime将会更低否则的话碰撞检测就可能会产生错误。


* Physics.solverIterationCount (Physics Manager).

解算器迭代次数

* Use as little Cloth objects as possible.

尽可能少使用一些“衣物”对象（注:这些对象会有更多的实时物理计算）

* Use Rigidbodies only where necessary.

只在需要的时候才使用刚体（Rigidbody）

* Use primitive colliders in preference mesh colliders.

使用基础的碰撞盒（注:例如矩形或者椭圆体）而不是基于mesh的碰撞盒

* Never ever move a static collider (ie a collider without a Rigidbody) as it causes a big performance hit. It shows up in Profiler as "Static Collider.Move’ but actual processing is in Physics.Simulate. If necessary, add a RigidBody and set isKinematic to true.

绝对不要去移动一个静态的碰撞盒（例如那些没有刚体附件的碰撞器）这会导致很大的性能开销。这会在Profiler中以“Static Collider.Move”的显现，实际处理会是在“Physics.Simulate”中。如果必须这么做的话，就在上面挂在一个刚体附件并且设置isKinematic属性为true吧。

* On Windows you can use NVidia’s AgPerfMon profiling tool set to get more details if needed.

在Windows平台上你可以使用NVidia的AgPerfMon监测工具来获取更多的详细信息。

## Android
### GPU

These are the popular mobile architectures. This is both different hardware vendors than in PC/console space, and very different GPU architectures than the “usual” GPUs.

这些都是非常常见的移动设备架构。不仅仅硬件的供应商和PC/主机不同，而且GPU架构和传统相比更是差别甚远。

* ImgTec PowerVR SGX - Tile based, deferred: render everything in small tiles (as 16x16), shade only visible pixels

TBDR架构，将所有的物体在小型的Tile（16x16）中进行渲染，仅对可视范围内的像素进行阴影的处理

* NVIDIA Tegra - Classic: Render everything

Classic经典架构，渲染所有物体

* Qualcomm Adreno - Tiled: Render everything in tile, engineered in large tiles (as 256k). Adreno 3xx can switch to traditional.

Tiled架构，在Tile中渲染所有物体，设计成大型的tile（256k）。Adreno 3xx可以切换成传统形态

* ARM Mali Tiled: Render everything in tile, engineered in small tiles (as 16x16)

Tiled架构，在Tile中渲染所有物体，设计成小型的tile（16x16）

























