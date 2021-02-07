# **`Direct3D9Ex`与可编程渲染管线**  

虽然`Direct3D12`已经推出5年多了，但是还有不少项目在继续使用`Direct3D9`系列，下文所有内容针对还在使用`Direct3D9`系列的项目。  

## **抛弃`Direct3D9`，走向`Direct3D9Ex`**

不要再继续调用`Direct3DCreate9`、`CreateDevice`、`Reset`了。  

从`Windows Vista`开始，微软推出了`Direct3D9Ex`，同时引入了`WDDM`驱动模型取代以前的`XPDM`驱动模型，新的驱动模型更加健壮，且最重要的一点是，在**修改交换链尺寸的时候不会造成设备丢失，也就不需要重新创建包括纹理资源在内的大多数资源，也不会导致渲染状态重置**，这点对于资源和状态管理来说是非常重要的。在`Direct3D9Ex`中，设备丢失产生的原因只有：显示设备驱动安装、卸载、崩溃，显示设备出错、被移除。而到了这种情况下已经没有什么办法补救了，只能从头开始创建Direct3D9相关的所有资源以恢复渲染。  

`Direct3D9Ex`的引入也带来了托管池`D3DPOOL_MANAGE`的移除，由于设备丢失只会发生在不可恢复的情况下，托管池变得可有可无。在`Direct3D9`中，只要调用了`Reset`方法就会导致默认池`D3DPOOL_DEFAULT`的资源丢失，因此为了管理像纹理资源这类的大型资源，微软提供了托管池来管理资源，在显存和内存中分别存放一份相同的资源，在需要的时候就从内存上传到显存中。这造成了两倍的硬件资源的消耗和两倍的心智负担，而`Direct3D9Ex`的出现很好地解决了这个问题。  

当然，即使是`Direct3D9Ex`也已经是过时了（`微软`官方把`Direct3D9`系列归类为遗留图形API），如果还在使用`Direct3D9`系列，应该尽快升级到`Direct3D11`乃至现代图形API。

## **抛弃固定渲染管线，使用可编程渲染管线**  

> 注：不要混淆“固定渲染管线”和“固定渲染阶段”，后者同时存在于两种渲染管线中  

Direct3D9中混杂着固定渲染管线和可编程渲染管线，而这也导致了使用上的混乱。从`微软`的`Direct3D11`和`Khronos`的`OpenGL3.3 Core Profile`开始，这些图形API不再提供固定渲染管线，而到了`Direct3D12`和`Vulkan`这些现代图形API，开发人员甚至能获得更多的硬件控制权。当然，现代图形API的入门曲线是相当陡峭的，在未来很长一段时间内传统图形API都还会继续存在，且被广泛使用。虽然可以继续混合使用两种渲染管线，但是在现代图形API越来越流行的今天，我们不应该继续使用传统图形API的固定渲染管线部分，我们应该积极使用可编程渲染管线。  

## **`Direct3D9Ex`与可编程渲染管线的API集合**  

在这里只列出主要的2个函数和基本的4个接口，也主要对比这些内容，其他的内容变化不大。  

### **函数**  

* _`Direct3DCreate9` (不要使用)_  
* **`Direct3DCreate9Ex`**  

### **接口与方法**  

* _`IDirect3D9` (不建议使用，请使用``IDirect3D9Ex`)_  
* **`IDirect3D9Ex`**  
    * **创建设备**  
        * _`CreateDevice` (不要使用)_  
        * **`CreateDeviceEx`**  
        * _`RegisterSoftwareDevice` (不要使用，除非你知道要做什么)_  
    * **显示适配器**  
        * **`GetAdapterCount`**  
        * **`GetAdapterIdentifier`**  
        * **`GetAdapterLUID`**  
    * **检查特性**  
        * **`CheckDeviceType`**  
        * **`GetDeviceCaps`**  
        * **`CheckDeviceFormat`**  
        * **`CheckDeviceFormatConversion`**  
        * **`CheckDepthStencilMatch`**  
        * **`CheckDeviceMultiSampleType`**  
    * **显示器**  
        * **`GetAdapterMonitor`**  
        * **`GetAdapterDisplayMode`**  
        * **`GetAdapterDisplayModeEx`**  
        * **`GetAdapterModeCount`**  
        * **`GetAdapterModeCountEx`**  
        * **`EnumAdapterModes`**  
        * **`EnumAdapterModesEx`**  
* _`IDirect3DDevice9` (不建议使用，请使用`IDirect3DDevice9Ex`)_  
* **`IDirect3DDevice9Ex`**  
    * **获取信息**  
        * **`GetDirect3D`**  
        * **`GetCreationParameters`**  
        * **`GetDeviceCaps`**  
        * **`ValidateDevice`**  
        * **`CreateQuery`**  
        * **`GetRasterStatus`**  
        * _`TestCooperativeLevel` (不要使用，应使用`CheckDeviceState`)_  
        * **`CheckDeviceState`**  
    * **渲染资源创建**  
        * **`CreateVertexDeclaration`**  
        * **`CreateVertexBuffer`**  
        * **`CreateIndexBuffer`**  
        * **`CreateVertexShader`**  
        * **`CreatePixelShader`**  
        * **`CreateTexture`**  
        * **`CreateCubeTexture`**  
        * **`CreateVolumeTexture`**  
        * **`CreateRenderTarget`**  
        * **`CreateRenderTargetEx`**  
        * **`CreateDepthStencilSurface`**  
        * **`CreateDepthStencilSurfaceEx`**  
        * **`CreateOffscreenPlainSurface`**  
        * **`CreateOffscreenPlainSurfaceEx`**  
    * **资源与性能管理**  
        * **`EvictManagedResources`**  
        * **`GetAvailableTextureMem`**  
        * **`CheckResourceResidency`**  
        * **`GetGPUThreadPriority`**  
        * **`SetGPUThreadPriority`**  
    * **纹理资源读写**  
        * **`Clear`**  
        * **`ColorFill`**  
        * **`GetRenderTargetData`**  
        * **`GetFrontBufferData`**  
        * **`StretchRect`**  
        * **`UpdateSurface`**  
        * **`UpdateTexture`**  
    * **交换链方法**  
        * _`Reset` (不要使用)_  
        * **`ResetEx`**  
        * _`Present` (不建议使用，请使用`PresentEx`)_  
        * **`PresentEx`**  
        * **`GetSwapChain`**  
        * **`GetDisplayMode`**  
        * **`GetDisplayModeEx`**  
        * **`GetBackBuffer`**  
        * **`SetMaximumFrameLatency`**  
        * **`GetMaximumFrameLatency`**  
        * **`WaitForVBlank`**  
        * **`GetNumberOfSwapChains`**  
        * **`CreateAdditionalSwapChain`**  
        * _`SetGammaRamp` (不建议使用)_  
        * _`GetGammaRamp` (不建议使用)_  
    * **渲染起止**  
        * **`BeginScene`**  
        * **`EndScene`**  
    * **管线设置**  
        * **输入装配阶段**  
            * **`SetVertexDeclaration`**  
            * **`GetVertexDeclaration`**  
            * **`SetStreamSource`**  
            * **`GetStreamSource`**  
            * **`SetStreamSourceFreq`**  
            * **`GetStreamSourceFreq`**  
            * **`SetIndices`**  
            * **`GetIndices`**  
        * **顶点着色器阶段**  
            * **`SetVertexShader`**  
            * **`GetVertexShader`**  
            * **`SetVertexShaderConstantB`**  
            * **`SetVertexShaderConstantF`**  
            * **`SetVertexShaderConstantI`**  
            * _`GetVertexShaderConstantB` (不建议使用)_  
            * _`GetVertexShaderConstantF` (不建议使用)_  
            * _`GetVertexShaderConstantI` (不建议使用)_  
        * **光栅化阶段**  
            * **`SetViewport`**  
            * **`GetViewport`**  
            * **`SetScissorRect`**  
            * **`GetScissorRect`**  
        * **像素着色器阶段**  
            * **`SetPixelShader`**  
            * **`GetPixelShader`**  
            * **`SetPixelShaderConstantB`**  
            * **`SetPixelShaderConstantF`**  
            * **`SetPixelShaderConstantI`**  
            * _`GetPixelShaderConstantB` (不建议使用)_  
            * _`GetPixelShaderConstantF` (不建议使用)_  
            * _`GetPixelShaderConstantI` (不建议使用)_  
        * **输出合并阶段**  
            * **`SetRenderTarget`**  
            * **`GetRenderTarget`**  
            * **`SetDepthStencilSurface`**  
            * **`GetDepthStencilSurface`**  
        * **着色器资源设置**  
            * **`SetSamplerState`**  
            * **`GetSamplerState`**  
            * **`SetTexture`**  
            * **`GetTexture`**  
        * **各类状态设置**  
            * **`SetRenderState`**  
            * **`GetRenderState`**  
    * **绘制方法**  
        * **`DrawPrimitive`**  
        * **`DrawIndexedPrimitive`**  
        * _`DrawPrimitiveUP` (不建议使用)_  
        * _`DrawIndexedPrimitiveUP` (不建议使用)_  
    * _索引颜色和单色纹理（不建议使用）_  
        * _`SetCurrentTexturePalette` (不建议使用)_  
        * _`GetCurrentTexturePalette` (不建议使用)_  
        * _`SetPaletteEntries` (不建议使用)_  
        * _`GetPaletteEntries` (不建议使用)_  
        * **`SetConvolutionMonoKernel`**  
    * _状态管理（不建议使用）_  
        * _`CreateStateBlock` (不建议使用)_  
        * _`BeginStateBlock` (不建议使用)_  
        * _`EndStateBlock` (不建议使用)_  
    * _固定管线方法（不要使用）_  
        * _`SetFVF` (不要使用)_  
        * _`GetFVF` (不要使用)_  
        * _`SetTransform` (不要使用)_  
        * _`GetTransform` (不要使用)_  
        * _`MultiplyTransform` (不要使用)_  
        * _`ProcessVertices` (不要使用)_  
        * _`SetSoftwareVertexProcessing` (不要使用)_  
        * _`GetSoftwareVertexProcessing` (不要使用)_  
        * _`LightEnable` (不要使用)_  
        * _`GetLightEnable` (不要使用)_  
        * _`GetLight` (不要使用)_  
        * _`SetLight` (不要使用)_  
        * _`SetMaterial` (不要使用)_  
        * _`GetMaterial` (不要使用)_  
        * _`SetClipPlane` (不要使用)_  
        * _`GetClipPlane` (不要使用)_  
        * _`SetClipStatus` (不要使用)_  
        * _`GetClipStatus` (不要使用)_  
        * _`SetTextureStageState` (不要使用)_  
        * _`GetTextureStageState` (不要使用)_  
        * _`ComposeRects` (不建议使用)_  
    * _高阶图元绘制（不要使用）_  
        * _`DeletePatch` (不要使用)_  
        * _`DrawTriPatch` (不要使用)_  
        * _`DrawRectPatch` (不要使用)_  
        * _`SetNPatchMode` (不要使用)_  
        * _`GetNPatchMode` (不要使用)_  
    * _系统交互（不要使用）_  
        * _`SetCursorPosition` (不要使用)_  
        * _`SetCursorProperties` (不要使用)_  
        * _`SetDialogBoxMode` (不要使用)_  
        * _`ShowCursor` (不要使用)_  
