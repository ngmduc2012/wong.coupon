# Cách Xử Lý Video với FFmpeg Flutter

![img.png](img.png)

Xử lý video là một trong những công việc tốn nhiều tài nguyên, đặc biệt là RAM và CPU của thiết bị. Nếu không được tối ưu hóa, ứng dụng của bạn dễ gặp sự cố crash. Trong quá trình phát triển, tôi đã gặp vấn đề khi sử dụng các thư viện bên thứ ba từ Pub.dev để:

- Chuyển video thành GIF.
- Chuyển đổi danh sách hình ảnh thành 1 video.

Những thư viện này thường mất nhiều thời gian xử lý và cho ra kết quả không đạt chất lượng mong muốn. Tuy nhiên, với **FFmpeg**, bạn có thể giải quyết những vấn đề trên một cách hiệu quả.


## 1. Giới Thiệu FFmpeg

[FFmpeg](https://www.ffmpeg.org/) là thư viện mã nguồn mở mạnh mẽ chuyên dùng để xử lý âm thanh và video. FFmpeg hỗ trợ rất nhiều tác vụ như:

- Chuyển đổi định dạng (MP4, MKV, MP3, v.v.).
- Xử lý video và âm thanh: Thêm watermark, cắt ghép, nén chất lượng.
- Tạo GIF từ video.
- Streaming và tối ưu hóa media.

Bộ công cụ [ffmpeg_kit_flutter_full_gpl](https://pub.dev/packages/ffmpeg_kit_flutter_full_gpl) mang lại đầy đủ tính năng của FFmpeg trên Flutter. Ngoài ra, bạn có thể tham khảo các gói khác tại [FFmpeg Kit Packages](https://github.com/arthenica/ffmpeg-kit/wiki/Packages).


## 2. Cài Đặt Bộ Công Cụ FFmpeg
Chú ý: Bài viết này xây dựng với phiên bản flutter 3.24.5 và thư viện ffmpeg_kit_flutter_full_gpl 6.0.3, có vẻ như các phiên bản mới của Flutter, thư viện này đang không hoặt động tốt.

### Cài Đặt
Thêm dependency vào tệp `pubspec.yaml`:

```yaml
ffmpeg_kit_flutter_full_gpl: ^latest_version
```

Chạy lệnh:

```bash
flutter pub get
```

### Cấu Hình Android
Thêm vào tệp `android/app/build.gradle`:

```gradle
defaultConfig {
    ...
    minSdk = 24
    ...
}
```

Cập nhật quyền trong `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.READ_MEDIA_IMAGES"/>
```

### Cấu Hình iOS
Thêm vào tệp `Info.plist`:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>We need access to your photo library to pick files.</string>
<key>NSDocumentDirectoryUsageDescription</key>
<string>We need access to your documents to pick files.</string>
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Đảm bảo tệp `ios/Podfile` có phiên bản nền tảng:

```ruby
platform :ios, '13.0'
```



## 3. Chuyển Loạt Ảnh Thành Video

Dưới đây là một hàm chuyển đổi danh sách hình ảnh thành video:
Ý tưởng của tôi là lưu tất cả hình ảnh vào 1 thư mục và công cụ ffmpegKit sẽ đọc nó lên tạo thành video.

```dart
Future<({bool isSuccess, String message})> convertImageDirectoryToVideo({
  required String imagesPath,
  required String outputVideoPath,
  int framerate = 24,
  int? fps,
  int? quality,
}) async {
  var isSuccess = false;
  var message = "";

  final command = '-framerate $framerate -i $imagesPath/%d.png '
      '${fps != null ? "-r $fps" : ""} '
      '${quality != null ? "-crf $quality -preset slow" : ""} '
      '-vf "scale=trunc(iw/2)*2:trunc(ih/2)*2" '
      '-c:v libx264 -pix_fmt yuv420p -movflags +faststart $outputVideoPath';

  await FFmpegKit.execute(command).then((session) async {
    final returnCode = await session.getReturnCode();

    if (returnCode?.isValueSuccess() ?? false) {
      message = 'Video conversion successful!';
      isSuccess = true;
    } else {
      final output = await session.getOutput();
      message = 'Video conversion failed: $output';
    }
  });

  return (isSuccess: isSuccess, message: message);
}
```

Ví dụ sử dụng:

```dart
final result = convertImageDirectoryToVideo(
  imagesPath: "path/to/images",
  outputVideoPath: "path/to/videoOutput.mp4",
);
```

Đảm bảo rằng thư mục `path/to/images` chứa các hình ảnh được đặt tên bằng số tự nhiên và có định dạng `.png`, như minh họa trong ví dụ:

![Hình minh họa](example/assets/image.jpeg)

Tệp `path/to/videoOutput.mp4` phải có tên duy nhất để tránh sảy ra lỗi trong quá trình xử lý của Ffmpeg.
Trong câu lệnh trên, với bất kỳ kích thước nào nào hình ảnh, tôi sẽ chia trung bình chúng và tạo ra kích thước của video. Bạn có thể sự sửa câu lệnh theo ý muốn của mình 

## 4. Thêm Watermark Vào Video

Hàm thêm watermark:

```dart
Future<({bool isSuccess, String message})> addWatermarkToVideo({
  required String videoPath,
  required String watermarkPath,
  required String outputPath,
  required int x,
  required int y,
  int? width,
  int? height,
}) async {
  var isSuccess = false;
  var message = "";

  final scaleFilter = (width != null && height != null)
      ? "[1:v]scale=$width:$height[wm];[0:v][wm]overlay=$x:$y"
      : "overlay=$x:$y";
  final command =
      '-i $videoPath -i $watermarkPath -filter_complex "$scaleFilter" -codec:a copy $outputPath';

  await FFmpegKit.execute(command).then((session) async {
    final returnCode = await session.getReturnCode();
    if (returnCode?.isValueSuccess() ?? false) {
      message = "Watermark added successfully!";
      isSuccess = true;
    } else {
      message = "Failed to add watermark.";
    }
  });

  return (isSuccess: isSuccess, message: message);
}
```

Ví dụ sử dụng:

```dart
final result = addWatermarkToVideo(
  videoPath: videoPath!,
  watermarkPath: watermarkPath!,
  outputPath: outputPath,
  x: 20,
  y: 30,
  width: 200,
  height: 200,
);
```
Trong đó x, y là toạ độ của watermark. width và height là kích thước của watermark. 


## 5. Giảm Chất Lượng Video

Hàm giảm chất lượng:

```dart
Future<({bool isSuccess, String message})> reduceVideoQualityByPercentage({
  required String inputPath,
  required String outputPath,
  required double qualityPercentage
}) async {
  final crfValue = (51 - 18) * (1 - qualityPercentage / 100) + 18;
  final command =
      '-i $inputPath -crf ${crfValue.toInt()} -preset fast -codec:a copy $outputPath';

  var isSuccess = false;
  var message = "";
  await FFmpegKit.execute(command).then((session) async {
    final returnCode = await session.getReturnCode();
    if (returnCode?.isValueSuccess() ?? false) {
      message = "Quality reduced successfully!";
      isSuccess = true;
    } else {
      message = "Failed to reduce quality.";
    }
  });

  return (isSuccess: isSuccess, message: message);
}
```

Ví dụ:

```dart
final result = reduceVideoQualityByPercentage(
  inputPath: videoPath!,
  outputPath: outputPath,
  qualityPercentage: 50,
);
```



## 6. Tạo GIF Từ Video

Hàm tạo GIF:

```dart
Future<({bool isSuccess, String message})> createGifFromVideo({
  required String inputPath,
  required String outputPath,
  required double fps,
  required int quality,
  int scale = 320,
}) async {
  final command =
      '-i $inputPath -vf "fps=$fps,scale=$scale:-1:flags=lanczos" -q:v $quality $outputPath';

  var isSuccess = false;
  var message = "";
  await FFmpegKit.execute(command).then((session) async {
    final returnCode = await session.getReturnCode();
    if (returnCode?.isValueSuccess() ?? false) {
      message = "GIF created successfully!";
      isSuccess = true;
    } else {
      message = "Failed to create GIF.";
    }
  });

  return (isSuccess: isSuccess, message: message);
}
```

Ví dụ sử dụng:
```dart
final result = createGifFromVideo(
  inputPath: videoPath!,
  outputPath: pathGif,
  fps: 10,
  quality: 10,
  scale: 500,
);
```
Kích thước scale càng lớn thì chất lượng gif càng sát với chất lượng gốc của video.

Hãy chú ý đến RAM khi sử dụng thư viện này.
Nếu bạn muốn sử dụng luôn, hãy sử dụng plugin của tôi tại [https://pub.dev/packages/my_maker_video](https://pub.dev/packages/my_maker_video).

[Buy Me a Coffee](https://buymeacoffee.com/ducmng12g) | [Support Me on Ko-fi](https://ko-fi.com/I2I81AEJG8)

