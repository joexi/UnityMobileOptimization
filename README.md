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

* Avoid Cutout (alpha test) shaders unless really necessary.

* Keep Transparent (alpha blend) screen coverage to a minimum.

* Try to avoid situations where multiple lights illuminate any given object.

* Try to reduce the overall number of shader passes (Shadows, pixel lights, reflections).

* Rendering order is critical. In general case:

	* fully opaque objects roughly front-to-back.

	* alpha tested objects roughly front-to-back.

	* skybox.
	
	* alpha blended objects (back to front if needed).

* Post Processing is expensive on mobiles, use with care.

* Particles: reduce overdraw, use the simplest possible shaders.

* Double buffer for Meshes modified every frame:
