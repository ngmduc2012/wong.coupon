# Cách xây dụng plugin với các function giao tiếp với native 

Trong quá trình phát triển ứng dụng Flutter, có nhiều trường hợp cần truy cập vào các API gốc của hệ điều hành để tận dụng các chức năng không có sẵn trong Flutter. Để làm điều này, chúng ta có thể tạo một plugin giao tiếp với native bằng cách sử dụng MethodChannel. Plugin này cho phép Flutter gửi yêu cầu đến mã native và nhận kết quả trả về từ iOS và Android.

Bài viết này sẽ hướng dẫn từng bước để xây dựng một plugin Flutter có thể giao tiếp với native, giúp bạn mở rộng khả năng của ứng dụng một cách linh hoạt.

## 1. Tạo plugin
### 1.1. Tạo thư viện plugin Flutter
Chạy lệnh sau để tạo plugin mới:
```bash
flutter create --template=plugin your_plugin
```
Lệnh này sẽ tạo một thư viện plugin với cấu trúc mặc định.
- Sử dụng tùy chọn `--platforms` để chỉ định plugin sẽ có những ngôn ngữ nào. Có các tùy chọn như: android, ios, web, linux, macos, windows
- Sử dụng tùy chọn `--org` để chỉ định tên miền cho tổ chức của bạn
- Sử dụng tùy chọn `--a` để chỉ định ngôn ngữ cho android. Bạn có thể chọn java hoặc kotlin
- Sử dụng tùy chọn `--i` để chỉ định ngôn ngữ cho ios. Bạn có thể chọn swift hoặc objc

Tham khảo: 
```bash
flutter create --org com.example --template=plugin --platforms=android,ios -a kotlin -i swift your_plugin
```
### 1.2. Cấu hình `your_plugin.dart`
Để tạo Widget hay Function để người dùng plugin để thể gọi và sử dụng, bạn cần đưa file đó vào thư mục src và export nó ra ngoài. Khi làm vậy, người dùng chỉ cần import 1 dòng duy nhất là có thể sử dụng plugin của bạn.

Cấu trúc thư mục của plugin như sau:
```
|--lib
    |-- src
        |-- message_entity.dart
        |-- your_plugin_function.dart
    |-- your_plugin.dart
 ```

Trong file `src/your_plugin_function.dart` định nghĩa giao tiếp giữa Dart và native:

Để gọi native code, cần sử dụng chanel, thông qua chanel ta sẽ gọi hàm native và nhận kết quả từ đó
#### Step 1 Set up method
```dart
part of YourPlugin;

class YourPlugin {

  /// STEP I | setup method

  static const String nameMethod = 'YourPlugin';
  static const MethodChannel _methods = MethodChannel('$nameMethod/methods');
  final StreamController<MethodCall> _methodStream = StreamController.broadcast();

  // 1.1: Create stream method
  bool _initialized = false;
    Future<dynamic> _initFlutterBluePlus() async {
    if (_initialized) {
      return;
    }
      _initialized = true;
      _methods.setMethodCallHandler((call) async {
        _updateMethodStream(call);
    });
  }

  // 1.2: invoke a platform method
  Future<dynamic> _invokeMethod(String method, [dynamic arguments]) async {
    dynamic out;
    print("_invokeMethod | $method | $arguments");
    try {
      _initFlutterBluePlus();
      out = await _methods.invokeMethod(method, arguments);
    } catch (e) {
      rethrow;
    }
    return out;
  }

  // 1.3: Update Stream _methodStream on flutter
  void _updateMethodStream(MethodCall methodCall) {
    _methodStream.add(methodCall);
  }
}
```
### Step 2
Bây gờ, tạo hàm để gửi dữ liệu và lắng nghe kết quả từ native
```dart
/// STEP II | Talk (and get) to native
  /// 2.1 | Type 1 Flutter -> Native -> Flutter
  Future<int> exTalk({
      int exMessage = 1,
    }) async {
      final Map<dynamic, dynamic> data = {};
      data['ex_message'] = exMessage;
      return await _invokeMethod('exTalk', data);
  }

  /// 2.2 | Type 2 Native -> Flutter: Listen native
  Stream<ListenState> get listenState async* {
  yield* _methodStream.stream
      .where((m) => m.method == "OnListenStateChanged")
      .map((m) => m.arguments)
      .map((args) => ListenState.fromMap(args));
  }
```
### Step 3
Tạo model lắng nghe kết quả từ native
Trong file `lib/src/message_entity.dart`
```dart
part of YourPlugin;

class ListenState {
  ListenStateEnum state;

  ListenState({
    required this.state
  });

  Map<dynamic, dynamic> toMap() {
    final Map<dynamic, dynamic> data = {};
    data['state'] = state;
    return data;
  }

  factory ListenState.fromMap(Map<dynamic, dynamic> json) {
    return ListenState(
      state: ListenStateEnum.getEnum(json['state']),
    );
  }
}

enum ListenStateEnum {
  working(0),
  free(1);

  const ListenStateEnum( this.code);
  final int code;
  static ListenStateEnum getEnum(int code){
    try {
      return ListenStateEnum.values.firstWhere((element) => element.code == code,);
    } catch (e) {
      return ListenStateEnum.working;
    }
  }

}
```

## 2. Tạo Code Native Cho Android
first, create `android/src/main/kotlin/com/example/your_plugin/Utils.java` file
```java
package com.example.your_plugin;

import android.util.Log;

public class Utils {
    private static final String TAG = "[YourPlugin-Android]";

    void log(String message ) {
        Log.d(TAG, message);
    }
}
```
And then create `TalkingWithFlutter.java`: Xử lý việc truyền dữ liệu giữa Flutter và native iOS thông qua MethodChannel.
```java
package com.example.your_plugin;

import android.os.Handler;
import android.os.Looper;
import java.util.HashMap;
import io.flutter.plugin.common.MethodChannel;


public class TalkingWithFlutter {

    TalkingWithFlutter(Utils utils){
        this.utils = utils;
    }
    public MethodChannel methodChannel;

    public void remove(){
        methodChannel.setMethodCallHandler(null);
        methodChannel = null;
    }

    private final Utils utils;

    // Send data back to Flutter plugin
    public void invokeMethodUIThread(final String method, HashMap<String, Object> data) {
        new Handler(Looper.getMainLooper()).post(() -> {
            //Could already be teared down at this moment
            if (methodChannel != null) {
                methodChannel.invokeMethod(method, data);
            } else {
                utils.log("invokeMethodUIThread: tried to call method on closed channel: " + method);
            }
        });
    }

    public static final int WORKING = 0;
    public static final int FREE = 1;
    public void onListenStateChanged(int state) {
        HashMap<String, Object> map = new HashMap<>();
        map.put("state", state);
        invokeMethodUIThread("OnListenStateChanged", map);
    }
}
```
and in `YourPlugin.java` file: Plugin chính xử lý các phương thức được gọi từ Flutter và giao tiếp với TalkingWithFlutter.
```java
package com.example.your_plugin;

public class YourPlugin implements FlutterPlugin, MethodCallHandler, PluginRegistry.ActivityResultListener {

  /**
   * I.Step 1: Setup method
   */
  private Context context;
  private ActivityPluginBinding activityBinding;
  private final Utils utils = new Utils();
  private final TalkingWithFlutter talkingWithFlutter = new TalkingWithFlutter(utils);

  @Override
  public void onAttachedToEngine(@NonNull FlutterPluginBinding flutterPluginBinding) {
    context = flutterPluginBinding.getApplicationContext();

    talkingWithFlutter.methodChannel = new MethodChannel(flutterPluginBinding.getBinaryMessenger(), "{{name.snakeCase()}}/methods");
    talkingWithFlutter.methodChannel.setMethodCallHandler(this);
  }

  @Override
  public void onDetachedFromEngine(@NonNull FlutterPluginBinding binding) {
    context = null;
    talkingWithFlutter.remove();
  }

  @Override
  public boolean onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    return false;
  }

  @Override
  public void onMethodCall(@NonNull MethodCall call, @NonNull Result result) {
    switch (call.method) {
      case "exTalk": {
        exTalk(call, result);
        break;
      }
      default:
        result.notImplemented();
        break;
    }
  }

  private void exTalk(@NonNull MethodCall call, @NonNull Result result) {
    HashMap<String, Object> data = call.arguments();
    int copies = (int) data.get("ex_message");
    utils.log(copies + "");

    talkingWithFlutter.onListenStateChanged(TalkingWithFlutter.FREE);

    result.success(TalkingWithFlutter.WORKING);
  }
}
```
## 3. Tạo Code Native Cho IOS
Tương tự như android ` ios/Classes/Utils.swift` file

And then create `TalkingWithFlutter.swift`: Xử lý việc truyền dữ liệu giữa Flutter và native iOS thông qua MethodChannel.
```swift
import Flutter
import Foundation
import CoreBluetooth

class TalkingWithFlutter : NSObject{
    
    // share func
    static let shared = TalkingWithFlutter()
    
    override private init() {
        super.init()
    }
    
    deinit {
    }
    
    init(methodChannel: FlutterMethodChannel? = nil) {
        self.methodChannel = methodChannel
    }
    
    var methodChannel : FlutterMethodChannel?

    let WORKING = 0
    let FREE = 1
    func onListenStateChanged(state: Int){
        let response = [
            "state": state,
        ]
        if methodChannel != nil {
            methodChannel!.invokeMethod("OnListenStateChanged", arguments: response)
            Utils.log("OnListenStateChanged \(response)")
        }
    }  

}
```
and in `YourPlugin.swift` file: Plugin chính xử lý các phương thức được gọi từ Flutter và giao tiếp với TalkingWithFlutter.
```Swift
import Flutter
import UIKit

public class YourPlugin: NSObject, FlutterPlugin {

    // MARK: I.Step 1: Setup method
    var talking = TalkingWithFlutter.shared

    public static func register(with registrar: FlutterPluginRegistrar) {
        let instance = YourPlugin()
        instance.talking.methodChannel = FlutterMethodChannel.init(name: "{{name.snakeCase()}}/methods", binaryMessenger: registrar.messenger())

        registrar.addMethodCallDelegate(instance, channel: instance.talking.methodChannel!)
    }

    // FlutterResult return a result.
    public func handle(_ call: FlutterMethodCall, result: @escaping FlutterResult) {
        Utils.log( call.method)
        switch call.method {

            case "exTalk":
                exTalk(call: call, result: result)

            default:
                result(FlutterMethodNotImplemented)
        }
    }

    func exTalk(call: FlutterMethodCall ,result: @escaping FlutterResult) {
        let args = call.arguments as? NSDictionary
        let copies = args?["ex_message"] as? Int
        Utils.log( "copies \(copies) ")

        talking.onListenStateChanged(state: talking.FREE)

        result(NSNumber(value: talking.WORKING))
    }
}
```
## 4. Usage
in file `main.dart` of your project:
```dart
import 'dart:async';

import 'package:flutter/material.dart';
import 'package:{{name.snakeCase()}}/{{name.snakeCase()}}.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatefulWidget {
  const MyApp({super.key});

  @override
  State<MyApp> createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {

  final _yourPlugin = YourPlugin();

  ListenStateEnum _listenState = ListenStateEnum.working;
  late StreamSubscription<ListenState> _listenStateSubscription;

  @override
  void initState() {
    super.initState();
    _listenStateSubscription = _yourPlugin.listenState.listen((value) {
      _listenState = value.state;
      if (mounted) {
        setState(() {});
      }
    });
  }

  @override
  void dispose() {
    _listenStateSubscription.cancel();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: const Text('Plugin example app | YourPlugin'),
        ),
        body: Center(
          child: SingleChildScrollView(
            child: Column(
              mainAxisSize: MainAxisSize.min,
              children: [
                const Text("TYPE 1: call to Native and get data"),
                TextButton(
                    onPressed: () async {
                      final a = await _yourPlugin.exTalk(exMessage: 3);
                      print(a);
                    },
                    child: const Text("exTalk")),

                const Text("TYPE 2: listen data from Native"),
                StreamBuilder(
                  stream: _yourPlugin.listenState,
                  builder: (context, s) {
                    return Text("Way 1 - listenState: ${s.data?.state.toString()}");
                  }
                ),
                Text("Way 2 - listenState: $_listenState"),

              ],
            ),
          ),
        ),
      ),
    );
  }
}
```
Cuối cùng run và xem thành quả
## Tổng Kết
Như vậy, chúng ta đã hoàn thành việc tạo một plugin Flutter giao tiếp với native thông qua MethodChannel. Plugin này giúp ứng dụng có thể:

Gửi dữ liệu từ Flutter đến native (iOS và Android).

Nhận phản hồi từ native.

Lắng nghe sự kiện từ native thông qua Stream.

Bằng cách tách biệt từng lớp (Utils.swift, TalkingWithFlutter.swift, và YourPlugin.swift), chúng ta đảm bảo mã nguồn có cấu trúc rõ ràng, dễ bảo trì và mở rộng.