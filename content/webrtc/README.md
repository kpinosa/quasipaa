### rtc_enable_symbol_export = false
> 将此设置为 `true` 将使 `RTC_EXPORT` 扩展为管理符号可见性的代码.
请参阅 `rtc_base/system/rtc_export.h`

### rtc_dlog_always_on = false
> 将其设置为 `true`，将使 `RTC_DLOG()` 扩展为日志语句，而不是被预处理器删除，这很有用，例如能够在发布版本上获取 `RTC_DLOG`.

### rtc_enable_objc_symbol_export = rtc_enable_symbol_export
> 将此设置为 `true` 将使 `RTC_OBJC_EXPORT` 扩展为管理符号可见性的代码. 默认情况下，如果是C++ 符号，则导出 `Obj-C/Obj-C++` 符号，但将此 设置为 `true` 同时保持 `rtc_enable_symbol_export=false` 将仅导出 `RTC_OBJC_EXPORT` 带注释的符号.

### build_with_chromium = rtc_exclude_field_trial_default = !is_nacl && !is_chromecast
> 将此设置为 `true` 将定义 `WEBRTC_EXCLUDE_FIELD_TRIAL_DEFAULT`，这将告诉预处理器删除使用 `field_trial`所需的符号的默认定义. 在这种情况下，需要提供新的实现.
> 当 WebRTC 作为 Chromium 的一部分构建时，它应该排除 `field_trial` 的默认实现， 除非它是为 `NACL`或 `Chromecast` 构建的.

### rtc_exclude_metrics_default = build_with_chromium
> 将此设置为 `true` 将定义 `WEBRTC_EXCLUDE_METRICS_DEFAULT`，这将告诉预处理器删除使用指标所需的符号的默认定义. 
> 在这种情况下，需要提供新的实现.

### rtc_exclude_system_time = build_with_chromium
> 将此设置为 `true` 将定义 `WEBRTC_EXCLUDE_SYSTEM_TIME`，这将告诉预处理器删除`rtc_base/system_time.cc` 中定义的 `SystemTimeNanos()` 的默认定义. 
> 在这种情况下，需要提供新的实现.

### rtc_builtin_ssl_root_certificates = true
> 将此设置为 `false` 将要求 API 用户传入他们自己的 `SSLCertificateVerifier` 以验证从 TLS-TURN 服务器提供的证书. 
> 作为回报，禁用它可以在二进制文件中节省大约 100kb.

### rtc_include_ilbc = true
> 包括 `iLBC` 音频编解码器.

### rtc_include_opus = true
> 禁用此项以避免构建 `Opus` 音频编解码器.

### rtc_opus_support_120ms_ptime = true
> 如果构建 WebRTC 的 `Opus` 版本支持对 `120ms` 数据包的直接编码，请启用此选项.

### rtc_opus_variable_complexity = false
> 启用此选项可让 `Opus` 音频编解码器动态更改复杂性.

### rtc_jsoncpp_root = "//third_party/jsoncpp/source/include"
> 用于在不编译 WebRTC 自带的库时指定外部 `Jsoncpp` 包含路径.
> 即 `rtc_build_json == 0`

### rtc_ssl_root = ""
> 用于在不编译 WebRTC 附带的库时指定外部 `OpenSSL` 包含路径.
> 即 `rtc_build_ssl == 0`

### rtc_enable_external_auth = build_with_chromium
> 当使用外部认证机制而不是 `libsrtp` 对 `RTP` 数据包执行数据包认证时启用.

### apm_debug_dump = false
> 选择是否应生成音频处理模块的调试转储.

### rtc_exclude_audio_processing_module = false
> 选择是否应排除音频处理模块.

### rtc_enable_bwe_test_logging = false
> 将此设置为 `true` 以启用 `BWE` 测试日志记录.

### rtc_build_examples = true
> 将此设置为 `false` 以跳过构建示例.

### rtc_build_tools = true
> 将此设置为 `false` 以跳过构建工具.

### rtc_use_x11 = ozone_platform_x11
> 将此设置为 `false` 以跳过构建需要 `X11` 的代码.

### rtc_use_pipewire = is_linux && use_sysroot
> 将其设置为在 `Wayland` 显示服务器上使用 `PipeWire`。 默认情况下，它仅在桌面 Linux（不包括 ChromeOS）上启用，并且仅在使用 `sysroot` 时才启用，因为 `PipeWire` 在较旧且受支持的 Ubuntu 和 Debian 发行版中不可用.

### rtc_link_pipewire = false
> 将其设置为直接链接 `PipeWire` 和所需的库，而不是使用 `dlopen`.

### build_with_mozilla = false
> 启用以使用 `Mozilla` 内部设置.

### rtc_enable_android_aaudio = false
> 实验性：启用需要 `Android SDK 26` 或更高版本和 `NDK r16` 或更高版本的 `Android AAudio`.

### rtc_sanitize_coverage = ""
> 为覆盖生成设置为`func`、`block`、`edge`，在单元测试运行时设置 `UBSAN_OPTIONS="coverage=1"`， 建议设置 `include_examples=0`， 将 `llvm` 的 `sancov -html-report` 用于人类可读的报告.
> 请参阅 [http://clang.llvm.org/docs/SanitizerCoverage.html](http://clang.llvm.org/docs/SanitizerCoverage.html)

### rtc_prefer_fixed_point = current_cpu == "arm" || current_cpu == "arm64"
> 尽可能选择定点代码.

### rtc_build_with_neon = (current_cpu == "arm" && arm_use_neon) || current_cpu == "arm64"
> 确定是否构建 `NEON` 代码.

### rtc_use_h264 = proprietary_codecs && !is_android && !is_ios && !(is_win && !is_clang)
> 启用它以构建 `OpenH264` 编码器或者`FFmpeg` 解码器。 这在除 `Android` 和 `iOS` 之外的所有平台上都受支持。 因为 `FFmpeg` 可以在有或者没有 `H.264` 支持的情况下构建，所以 `ffmpeg_branding` 必须单独设置为包含 H.264 的值，例如`Chrome`. 如果 `FFmpeg` 是在没有 `H.264` 的情况下构建的，编译成功但 `H264DecoderImpl` 无法初始化。 在构建之前检查 `openh264`、`ffmpeg` 和 `H.264` 许可证/专利. [http://www.openh264.org,](http://www.openh264.org,) [https://www.ffmpeg.org/](https://www.ffmpeg.org/)
>   当前不支持在使用 `MSVC` 构建时启用 `H264`，有关详细信息，请参阅 [bugs.webrtc.org/9213#c13](bugs.webrtc.org/9213#c13).

### rtc_use_absl_mutex = false
> 启用此标志使 `webrtc::Mutex` 由 `absl::Mutex` 实现.

### rtc_use_dummy_audio_file_devices = false
> 默认情况下，使用普通平台音频支持或虚拟音频，但不要使用基于文件的音频播放和录制.

### rtc_audio_device_plays_sinus_tone = false
> 设置为 `true` 时，将音频输出替换为 `440Hz` 的正弦音. `ADM` 将从 WebRTC 请求音频数据，但不是从 `NetEQ` 读取真实音频样本，而是生成正弦音并替换真实音频样本.

### rtc_ios_macos_use_opengl_rendering = !(is_ios && target_environment == "catalyst")
> 确定 `OpenGL` 在 `iOS/macOS` 上是否可用.

### rtc_include_builtin_audio_codecs = true
> 当设置为 `false` 时，内置音频编码器/解码器工厂及其依赖的所有音频编解码器将不会包含在 `libwebrtc`中（它们仍将包含在 `libjingle_peerconnection_so.so` 和 `WebRTC.framework` 中）

### rtc_include_builtin_video_codecs = true
> 当设置为 `false` 时，内置视频编码器/解码器工厂及其依赖的所有视频编解码器将不会包含在 `libwebrtc`中（它们仍将包含在 `libjingle_peerconnection_so.so` 和 `WebRTC.framework` 中）

### rtc_win_undef_unicode = false
> 当设置为 `true` 并在独立构建中时，它将取消定义 `UNICODE` 和 `_UNICODE`（它们始终由 `Chromium Windows` 工具链全局定义）.
> 这仅用于测试目的，WebRTC 希望确保它不假定 `/DUNICODE` 和 `/D_UNICODE`，而是明确使用宽字符函数.

### rtc_enable_win_wgc = is_win
> 当设置为 `true` 时，使用 `Windows.Graphics.Capture API` 的捕获器实现将可供使用。 这引入了对 `Win 10 SDK v10.0.17763.0` 的依赖.

### rtc_include_dav1d_in_internal_decoder_factory = true
> 设置为 `true` 时，在内部解码器工厂中包含 `dav1d` 解码器.

### rtc_enable_protobuf = !build_with_mozilla
> 允许使用`protobuf`进行调试记录.

### rtc_enable_sctp = !build_with_mozilla
> 设置此项以禁用支持 `SCTP` 数据通道的构建.

### rtc_build_json = !build_with_mozilla
### rtc_build_libsrtp = !build_with_mozilla
### rtc_build_libvpx = !build_with_mozilla
### rtc_libvpx_build_vp9 = !build_with_mozilla
### rtc_build_opus = !build_with_mozilla
### rtc_build_ssl = !build_with_mozilla
> 禁用这些以不构建可以从外部提供的组件.

### rtc_include_pulse_audio = !build_with_chromium
> 排除在 Chromium 中，因为它的先决条件不需要 `Pulse Audio`.

### rtc_include_internal_audio_device = !build_with_chromium
> Chromium 使用自己的 IO 处理，因此内部 `ADM` 仅为独立的 WebRTC 构建.

### rtc_enable_avx2 = is_clang
> 将此设置为 `true` 以在 webrtc 中启用 `avx2` 支持.
> TODO：确保 `AVX2` 也适用于非 `clang` 编译器.

### rtc_include_tests = !build_with_chromium && !build_with_mozilla
> 将此设置为 `true` 以构建单元测试。 使用 Chromium 或 Mozilla 构建时禁用.

### rtc_use_x11_extensions = rtc_use_x11
> 将此设置为 `false` 以跳过还需要 `X11` 扩展（例如 `Xdamage`、`Xfixes`）的构建代码.

### rtc_disable_logging = false
> 将此设置为 `true` 以完全从 WebRTC 中删除日志记录.

### rtc_disable_trace_events = false
> 将此设置为 `true` 以禁用堆栈跟踪事件.

### rtc_disable_check_msg = false
> 将此设置为 `true` 以禁用详细的错误消息和 `RTC_CHECKs` 的日志记录.

### rtc_disable_metrics = false
> 将此设置为 `true` 以禁用 webrtc 指标.

### rtc_exclude_transient_suppressor = false
> 将此设置为 `true` 以从构建中排除音频处理模块中的瞬态抑制器.

### rtc_build_dcsctp = !build_with_mozilla && rtc_enable_sctp
> 为数据通道和相关单元测试启用 `dcsctp` 后端.

### rtc_build_usrsctp = !build_with_mozilla && rtc_enable_sctp
> 为数据通道和相关单元测试启用 `usrsctp` 后端.

### rtc_desktop_capture_supported = (is_win && current_os != "winuwp") || is_mac || ((is_linux || is_chromeos) && (rtc_use_x11_extensions || rtc_use_pipewire))
> 桌面捕获器仅在 `Windows`、`OSX` 和 `Linux` 上受支持.
