# Project Standard

This standard applies to application code written in Java using LWJGL and the Vulkan API. Apply each rule when the corresponding capability is introduced.

## Build configuration

1. **Declare the toolchain and dependencies.** Record the Java language version and LWJGL release in the build configuration. Use the same LWJGL release for all modules and their native artifacts. Select native artifacts for the operating system and architecture of the target JVM. Keep IDE builds consistent with the command-line build.

## Java and LWJGL

2. **Give native resources an owner.** For every native allocation or Vulkan object the application owns, identify which component releases it. Use Java's `AutoCloseable` for resource-owning classes and try-with-resources for scoped lifetimes. Release acquired resources on shutdown and on partial initialization failure. Borrowed handles remain the responsibility of their owner.

3. **Match LWJGL allocations to their lifetimes.** Use `try (var stack = MemoryStack.stackPush())` for small temporary buffers and structures whose last native use occurs within the block. Pointers and views into that memory expire when the block closes. For longer-lived buffers allocated with `MemoryUtil.memAlloc` or `memCalloc`, retain the original allocation and release it exactly once with `memFree`.

## Vulkan

4. **Check capabilities before use.** At initialization, verify that the selected device supports the Vulkan API version, extensions, features, and queue operations the application requires. Enable the required extensions and optional device features explicitly. If a required capability is missing, identify it in the startup error.

5. **Handle results according to the operation.** For each call returning `VkResult`, handle the statuses documented for that command. For example, an out-of-date swapchain during image acquisition requires swapchain recreation before retrying. Report an unexpected failure with the command name and result code.

6. **Account for work still running on the GPU.** Before overwriting, reusing, or destroying a resource, establish that conflicting earlier work has completed or is correctly ordered. Use the synchronization appropriate to that resource. For example, wait for a frame submission's fence before resetting its command buffer. Destroy owned child objects before destroying their Vulkan parent.

7. **Run changed Vulkan paths with validation.** Enable `VK_LAYER_KHRONOS_validation` during development and make its messages visible. Exercise the changed path, including cleanup, and resolve validation errors it produces. If the layer is unavailable, report that the validation check could not run. Release builds must be able to run without the layer.

## Maintaining this standard

The project owner approves changes to this standard. When the implementation agent identifies a recurring technical constraint or a defect that warrants a project-wide rule, it proposes the wording to the project owner. The proposal must state when the rule applies and how to verify compliance. Once approved, the implementation agent updates PROJECT STANDARD.md. Feature-specific behavior belongs in the relevant specification.

## Official references

- Java resource ownership and cleanup — [Oracle's try-with-resources guide](https://docs.oracle.com/javase/8/docs/technotes/guides/language/try-with-resources.html)
- LWJGL native memory — [MemoryStack](https://javadoc.lwjgl.org/org/lwjgl/system/MemoryStack.html) and [MemoryUtil](https://javadoc.lwjgl.org/org/lwjgl/system/MemoryUtil.html)
- Vulkan initialization and error handling — [Enabling features](https://docs.vulkan.org/guide/latest/enabling_features.html), [VkResult](https://docs.vulkan.org/refpages/latest/refpages/source/VkResult.html), and [swapchain recreation](https://docs.vulkan.org/tutorial/latest/03_Drawing_a_triangle/04_Swap_chain_recreation.html)
- Vulkan development checks — [Synchronization](https://docs.vulkan.org/guide/latest/synchronization.html) and [validation overview](https://docs.vulkan.org/guide/latest/validation_overview.html)
