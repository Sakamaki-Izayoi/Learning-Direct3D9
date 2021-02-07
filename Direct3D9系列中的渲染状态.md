# **Direct3D9系列中的渲染状态**  

本文提到的渲染状态（RenderState）仅指代`Direct3D9`系列中`SetRenderState`方法可以修改的渲染状态。  

## **单独的渲染状态 VS 状态块**  

`Direct3D9`系列中的渲染状态混杂着渲染管线各个阶段的状态，像一个个独立的开关，在`Direct3D9`系列及以前的`Direct3D`图形API中，都是以这种形式来单独控制渲染状态的，到了`Direct3D10`才开始将这些状态按照管线的阶段分开成一个个独立的、不可变的大的状态块，而到了`Direct3D12`则把不可变做得更彻底，将各个管线阶段的状态、着色器、顶点布局等信息整合成了一个庞大的`ID3D12PipelineState`对象。  

以一个大的状态块来管理渲染状态虽然会增加状态管理的复杂度，但是能让GPU更快速地切换。而`Direct3D12`把渲染状态整合为了庞大的`ID3D12PipelineState`对象后，还获得了验证整所有渲染状态合理性的优势，可以更快地发现问题，降低调试难度。  

当然，`Direct3D9`系列毕竟是一个有着18年历史的图形API了，其管理渲染状态的风格早就已经定下，我们也不指望它可以变出类似`Direct3D11`风格的状态块。  

## **固定渲染管线混杂在渲染状态中**  

`Direct3D9`系列混合了两种图形API的风格，一个是早期`Direct3D`和`OpenGL`固定渲染管线图形API的风格，及`OpenGL3.3 Core Profile`和`Direct3D10`之后的可编程渲染管线API的风格。而开发者在设置渲染状态的时候，稍有不慎就会修改到固定渲染管线的部分，导致渲染的结果出现问题，最常见的错误就是把固定渲染管线的光照功能打开，导致画面一片乌黑（因为没有设置好灯光、物体材质）。  

## **渲染状态**  

以下的渲染状态按照`Direct3D11`的风格，按照不同管线阶段分类。  

* **光栅化状态**  
    * **`D3DRS_FILLMODE`**  
    * **`D3DRS_CULLMODE`**  
    * **`D3DRS_DEPTHBIAS`**  
    * **`D3DRS_SLOPESCALEDEPTHBIAS`**  
    * **`D3DRS_SCISSORTESTENABLE`**  
    * **`D3DRS_MULTISAMPLEANTIALIAS`**  
    * **`D3DRS_MULTISAMPLEMASK`**  
    * **`D3DRS_ANTIALIASEDLINEENABLE`**  
    * _`D3DRS_DITHERENABLE` (不建议修改)_  
    * _`D3DRS_LASTPIXEL` (不建议修改)_  
* **混合状态**  
    * **`D3DRS_ALPHABLENDENABLE`**  
    * **`D3DRS_SEPARATEALPHABLENDENABLE`**  
    * **`D3DRS_SRCBLEND`**  
    * **`D3DRS_DESTBLEND`**  
    * **`D3DRS_BLENDOP`**  
    * **`D3DRS_SRCBLENDALPHA`**  
    * **`D3DRS_DESTBLENDALPHA`**  
    * **`D3DRS_BLENDOPALPHA`**  
    * **`D3DRS_BLENDFACTOR`**  
    * **`D3DRS_COLORWRITEENABLE`**  
    * **`D3DRS_COLORWRITEENABLE1`**  
    * **`D3DRS_COLORWRITEENABLE2`**  
    * **`D3DRS_COLORWRITEENABLE3`**  
    * **`D3DRS_SRGBWRITEENABLE`**  
* **深度与模板状态**  
    * **`D3DRS_ZENABLE`**  
    * **`D3DRS_ZWRITEENABLE`**  
    * **`D3DRS_ZFUNC`**  
    * **`D3DRS_STENCILENABLE`**  
    * **`D3DRS_STENCILMASK`**  
    * **`D3DRS_STENCILWRITEMASK`**  
    * **`D3DRS_TWOSIDEDSTENCILMODE`**  
    * **`D3DRS_STENCILFAIL`**  
    * **`D3DRS_STENCILZFAIL`**  
    * **`D3DRS_STENCILPASS`**  
    * **`D3DRS_STENCILFUNC`**  
    * **`D3DRS_CCW_STENCILFAIL`**  
    * **`D3DRS_CCW_STENCILZFAIL`**  
    * **`D3DRS_CCW_STENCILPASS`**  
    * **`D3DRS_CCW_STENCILFUNC`**  
    * **`D3DRS_STENCILREF`**  
* **其他**  
    * **`D3DRS_DEBUGMONITORTOKEN`**  

以下的渲染状态可能会对以后迁移到`Direct3D11`、`Direct3D12`等图形API造成一定的困难，虽然它们仍然可以使用，但是并不建议。  

* _顶点裁剪（不建议使用，请在顶点着色器中实现）_  
    * _`D3DRS_CLIPPING` (不建议使用)_  
    * _`D3DRS_CLIPPLANEENABLE` (不建议使用)_  
* _纹理缠绕（不建议使用）_  
    * _`D3DRS_WRAP0` (不建议使用)_  
    * _`D3DRS_WRAP1` (不建议使用)_  
    * _`D3DRS_WRAP2` (不建议使用)_  
    * _`D3DRS_WRAP3` (不建议使用)_  
    * _`D3DRS_WRAP4` (不建议使用)_  
    * _`D3DRS_WRAP5` (不建议使用)_  
    * _`D3DRS_WRAP6` (不建议使用)_  
    * _`D3DRS_WRAP7` (不建议使用)_  
    * _`D3DRS_WRAP8` (不建议使用)_  
    * _`D3DRS_WRAP9` (不建议使用)_  
    * _`D3DRS_WRAP10` (不建议使用)_  
    * _`D3DRS_WRAP11` (不建议使用)_  
    * _`D3DRS_WRAP12` (不建议使用)_  
    * _`D3DRS_WRAP13` (不建议使用)_  
    * _`D3DRS_WRAP14` (不建议使用)_  
    * _`D3DRS_WRAP15` (不建议使用)_  
* _高阶图元（不建议使用）_  
    * _`D3DRS_PATCHEDGESTYLE` (不建议使用)_  
    * _`D3DRS_POSITIONDEGREE` (不建议使用)_  
    * _`D3DRS_NORMALDEGREE` (不建议使用)_  
    * _`D3DRS_MINTESSELLATIONLEVEL` (不建议使用)_  
    * _`D3DRS_MAXTESSELLATIONLEVEL` (不建议使用)_  
    * _`D3DRS_ADAPTIVETESS_X` (不建议使用)_  
    * _`D3DRS_ADAPTIVETESS_Y` (不建议使用)_  
    * _`D3DRS_ADAPTIVETESS_Z` (不建议使用)_  
    * _`D3DRS_ADAPTIVETESS_W` (不建议使用)_  
    * _`D3DRS_ENABLEADAPTIVETESSELLATION` (不建议使用)_  
* _点精灵（不建议使用）_  
    * _`D3DRS_POINTSPRITEENABLE` (不建议使用)_  
    * _`D3DRS_POINTSIZE` (不建议使用)_  
    * _`D3DRS_POINTSIZE_MIN` (不建议使用)_  
    * _`D3DRS_POINTSIZE_MAX` (不建议使用)_  
    * _`D3DRS_POINTSCALEENABLE` (不建议使用)_  
    * _`D3DRS_POINTSCALE_A` (不建议使用)_  
    * _`D3DRS_POINTSCALE_B` (不建议使用)_  
    * _`D3DRS_POINTSCALE_C` (不建议使用)_  

以下均为纯固定管线的内容，如果已经在使用可编程渲染管线，下面的状态就不需要再考虑和使用了。需要注意的是，有些固定管线的渲染状态默认是启用的，比如光照`D3DRS_LIGHTING`，因此最好是在创建好`Direct3DDevice9`后马上就将这些状态设置为关闭，以防影响到可编程渲染管线的工作。  

* _光照与材质（固定渲染管线，不要使用，应由开发者自己实现）_  
    * _`D3DRS_SHADEMODE` (不要使用)_  
    * _`D3DRS_SPECULARENABLE` (不要使用)_  
    * _`D3DRS_LIGHTING` (不要使用)_  
    * _`D3DRS_AMBIENT` (不要使用)_  
    * _`D3DRS_COLORVERTEX` (不要使用)_  
    * _`D3DRS_LOCALVIEWER` (不要使用)_  
    * _`D3DRS_NORMALIZENORMALS` (不要使用)_  
    * _`D3DRS_DIFFUSEMATERIALSOURCE` (不要使用)_  
    * _`D3DRS_SPECULARMATERIALSOURCE` (不要使用)_  
    * _`D3DRS_AMBIENTMATERIALSOURCE` (不要使用)_  
    * _`D3DRS_EMISSIVEMATERIALSOURCE` (不要使用)_  
    * _`D3DRS_VERTEXBLEND` (不要使用)_  
    * _`D3DRS_INDEXEDVERTEXBLENDENABLE` (不要使用)_  
    * _`D3DRS_TWEENFACTOR` (不要使用)_  
* _透明度测试（固定渲染管线，不要使用，应在像素着色器中实现）_  
    * _`D3DRS_ALPHATESTENABLE` (不要使用)_  
    * _`D3DRS_ALPHAREF` (不要使用)_  
    * _`D3DRS_ALPHAFUNC` (不要使用)_  
* _雾效果（固定渲染管线，不要使用，应由开发者自己实现）_  
    * _`D3DRS_FOGENABLE` (不要使用)_  
    * _`D3DRS_FOGCOLOR` (不要使用)_  
    * _`D3DRS_FOGTABLEMODE` (不要使用)_  
    * _`D3DRS_FOGSTART` (不要使用)_  
    * _`D3DRS_FOGEND` (不要使用)_  
    * _`D3DRS_FOGDENSITY` (不要使用)_  
    * _`D3DRS_RANGEFOGENABLE` (不要使用)_  
    * _`D3DRS_FOGVERTEXMODE` (不要使用)_  
* _纹理颜色混合（固定渲染管线，不要使用，应在像素着色器中实现）_  
    * _`D3DRS_TEXTUREFACTOR` (不要使用)_  

## **总结**  

虽然`Direct3D9`系列的渲染状态众多且复杂，但是剔除了固定渲染管线的部分和不推荐使用的部分后，剩下的基本上能分别映射到`Direct3D11`的`ID3D11RasterizerState`、`ID3D11DepthStencilState`和`ID3D11BlendState`。开发者可以简单地按照`Direct3D11`的风格把这些渲染状态包装一下，以便于未来将项目代码移植到`Direct3D11`。  
