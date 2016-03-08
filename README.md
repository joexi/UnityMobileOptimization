# UnityMobileOptimization
Translate of Unity documentation "PlatformSpecific/Mobile Developer Checklist/Optimazation"

## Optimizations

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

On mobiles you’re essentially fillrate bound (fillrate = screen pixels * shader complexity * overdraw), and over-complex shaders is the most common cause of problems. So use mobile shaders that come with Unity or design your own but make them as simple as possible. If possible simplify your pixel shaders by moving code to vertex shader.

If reducing the Texture Quality in Quality Settings makes the game run faster, you are probably limited by memory bandwidth. So compress textures, use mipmaps, reduce texture size, etc.

LOD (Level of Detail) - make objects simpler or eliminate them completely as they move further away.

#### Good practice

Mobile GPUs have huge constraints in how much heat they produce, how much power they use, and how large or noisy they can be. So compared to the desktop parts, mobile GPUs have way less bandwidth, low ALU performance and texturing power. The architectures of the GPUs are also tuned to use as little bandwidth & power as possible.

Unity is optimized for OpenGL ES 2.0, it uses GLSL ES (similar to HLSL) shading language. Built in shaders are most often written in HLSL (also known as Cg). This is cross compiled into GLSL ES for mobile platforms. You can also write GLSL directly if you want to, but doing that limits you to OpenGL-like platforms (e.g. mobile + Mac) since there currently are no GLSL->HLSL translation tools. When you use float/half/fixed types in HLSL, they end up highp/mediump/lowp precision qualifiers in GLSL ES.

Here is the checklist for good practice:
