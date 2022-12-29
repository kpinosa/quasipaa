通过自定义视频源，我们可以输入视频帧到`WebRTC MediaStreamTrack`中，这样可以将自定义的视频源发送到对端.  
要实现自定义视频源，需要实现的类就是`VideoSourceInterface`和`VideoTrackSource`.

#### VideoSourceInterface:
> 主要需要实现的就是`AddOrUpdateSink`和`RemoveSink`

```cpp
class VideoSourceInterface {
 public:
  virtual ~VideoSourceInterface() = default;
  
  // `AddOrUpdateSink`在`WebRTC`向这个类中添加`VideoSink`的时候触发,
  // 添加之后你就可以通过它向内部推送视频帧.
  // 一个视频源可以有同时存在多个`VideoSink`.
  virtual void AddOrUpdateSink(VideoSinkInterface<VideoFrameT>* sink,
                               const VideoSinkWants& wants) = 0;
  
  // `RemoveSink`是需要删除`VideoSink`的时候调用.
  virtual void RemoveSink(VideoSinkInterface<VideoFrameT>* sink) = 0;

  virtual void RequestRefreshFrame() {}
};
```

```urlpreview
https://github.com/colourful-rtc/batrachiatc/blob/master/src/media_stream_track.h#L35
```

#### VideoTrackSource
> 不需要实现其中任何方法，直接继承即可，这个类的主要作用就是提供`VideoSource`的实现

```cpp
class RTC_EXPORT VideoTrackSource : public Notifier<VideoTrackSourceInterface> {
 public:
  explicit VideoTrackSource(bool remote);
  void SetState(SourceState new_state);

  SourceState state() const override { return state_; }

  // 视频源是否来自远端，如果是自己实现的，始终为`false`
  bool remote() const override { return remote_; }

  // 视频源是否为桌面捕获，如果来自桌面，则覆盖实现返回`true`即可
  bool is_screencast() const override { return false; }
  absl::optional<bool> needs_denoising() const override {
    return absl::nullopt;
  }

  // 获取源状态
  bool GetStats(Stats* stats) override { return false; }

  void AddOrUpdateSink(rtc::VideoSinkInterface<VideoFrame>* sink,
                       const rtc::VideoSinkWants& wants) override;
  void RemoveSink(rtc::VideoSinkInterface<VideoFrame>* sink) override;

  bool SupportsEncodedOutput() const override { return false; }
  void GenerateKeyFrame() override {}
  void AddEncodedSink(
      rtc::VideoSinkInterface<RecordableEncodedFrame>* sink) override {}
  void RemoveEncodedSink(
      rtc::VideoSinkInterface<RecordableEncodedFrame>* sink) override {}

 protected:
  virtual rtc::VideoSourceInterface<VideoFrame>* source() = 0;

 private:
  SequenceChecker worker_thread_checker_;
  SourceState state_;
  const bool remote_;
};
```

```urlpreview
https://github.com/colourful-rtc/batrachiatc/blob/master/src/media_stream_track.h#L53
```