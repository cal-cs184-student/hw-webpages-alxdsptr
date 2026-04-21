For the project, we have been rewriting the original HW3 path tracer into a new general purpose, GPU-oriented framework. Our primary goal has been rendering complex scenes much faster than the CPU-based implementation,  hopefully real-time. So far, this framework supports:

Different types of model file through Open Asset Import Library (assimp)
A modern display output pipeline (Vulkan) with interactive navigation
Path tracing via CUDA kernels with basic acceleration data structures (ex. the BVH) rewritten
Texture loading, including basic albedo textures, normal maps, and emissive textures
Cook-Torrance BRDF for most materials, pure lambertian fallback for .dae files, and transmission for glass
To support direct light sampling for emissive triangles, we assign each triangle a weight proportional to its area and average emissive luminance.  These weights build a global CDF that allows for $O(\log N)$ importance sampling.
To balance the strengths and weaknesses of NEE and BSDF sampling, we employ MIS using the power heuristic.
For each light-sampled direction, we weight the contribution by $w_{\text{light}} = p_{\text{light}}^2 / (p_{\text{light}}^2 + p_{\text{bsdf}}^2)$; conversely, when a BSDF-sampled ray hits a registered emitter, we use $w_{\text{bsdf}} = p_{\text{bsdf}}^2 / (p_{\text{bsdf}}^2 + p_{\text{light}}^2)$.
Denoising & Upsampling for Real-Time: DLSS support and Nvidia Real-Time Denoisers (NRD) (working with CUDA but not OptiX yet)
OptiX backend implementation, ported from our CUDA path-tracing kernels to leverage RT-core hardware acceleration
	
Compared to our plan, we are pretty far along in many aspects as our framework is rendering fairly large, complex scenes with both CUDA and OptiX acceleration. Here is what we are considering next:

Light BVH: organizing light sources into a hierarchical structure, enabling efficient importance sampling that significantly reduces noise and computational overhead.
Importance sampling for environment light
Volumetric rendering
ReSTIR support
Other attempts to reduce noise


Preliminary Results


Fps @ 1280*720
| **Scene**                    | **CUDA** | **CUDA + DLSS** | **OptiX** | **OptiX + DLSS** |
| ---------------------------- | -------- | --------------- | --------- | ---------------- |
| BistroInterior               | 8.5      | 23              | 96        | 186              |
| MEASURE_SEVEN_COLORED_LIGHTS | 14.4     | 38              | 126       | 209              |
| BistroExterior               | 13.8     | 37              | 149       | 240              |


