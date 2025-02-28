# Plugin, giao tiếp với native cách hiệu quả để bảo trì các ứng dụng Flutter. 

Có nhiều trường hợp cần truy cập vào các API gốc của hệ điều hành để tận dụng các chức năng không có sẵn trong Flutter. Để làm điều này, chúng ta có thể tạo một plugin giao tiếp với native bằng cách sử dụng MethodChannel. Plugin này cho phép Flutter gửi yêu cầu đến mã native và nhận kết quả trả về từ iOS và Android.
Tất nhiên chúng ta hoàn toàn có thể tạo nó ngay trong native của project, nhưng việc tác biệt nó ra thành 1 plugin sẽ giúp quá trình maintain sử chữa khi ứng dụng gặp lỗi sẽ trở nên khó khăn hơn. 
Sau đây tôi sẽ tình bày về cách tôi tạo 1 plugin cho các ứng dụng của tôi. 

## 1. Tạo tổng quan plugin
### 1.1. Tạo thư viện plugin Flutter
Chạy lệnh sau để tạo plugin mới:
```bash
flutter create --template=plugin your_plugin
```
Hãy đảm bảo rằng plugin của bạn không dùng hoặc hạn chế thấp nhất có thể khi dùng thư viện của bên thứ 3. 

### 1.2. Cấu trúc của 1 plugin

Cấu trúc thư mục của plugin như sau:
```
|--lib
    |-- src
        |-- message_entity.dart
        |-- your_plugin_function.dart
    |-- your_plugin.dart
 ```
Trong file `src/your_plugin_function.dart` định nghĩa giao tiếp giữa Dart và native:
Hãy đảm bảo tất cả các file sử dụng đều được export vào trong `your_plugin.dart`, để bạn có thể import thư viện 1 lần duy nhất khi gọi đến nó. 

## 2. Tạo method cho plugin
Để gọi native code, cần sử dụng chanel, thông qua chanel ta sẽ gọi hàm native và nhận kết quả từ đó

### 2.1. Set up method cho dart
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
### 2.2. Tạo Code Native Cho Android

create `TalkingWithFlutter.java`: Xử lý việc truyền dữ liệu giữa Flutter và native iOS thông qua MethodChannel.
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
Hàm này sử dụng để gửi dữ liệu từ native lên flutter. Nó sẽ hữu lích khi lắng nghe dự liệu qua bluetooth. 
```java
    public void onListenStateChanged(int state) {
        HashMap<String, Object> map = new HashMap<>();
        map.put("state", state);
        invokeMethodUIThread("OnListenStateChanged", map);
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
Phần code này sẽ nhận dữ liệu được truyền từ flutter xuống thực thi nó và trả kết quả lại cho flutter.
```java
  private void exTalk(@NonNull MethodCall call, @NonNull Result result) {
    HashMap<String, Object> data = call.arguments();
    int copies = (int) data.get("ex_message");
    utils.log(copies + "");

    talkingWithFlutter.onListenStateChanged(TalkingWithFlutter.FREE);

    result.success(TalkingWithFlutter.WORKING);
  }
```

create `android/src/main/kotlin/com/example/your_plugin/Utils.java` file
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
Hàm này sử dụng để gửi dữ liệu từ native lên flutter. Nó sẽ hữu lích khi lắng nghe dự liệu qua bluetooth.
```Swift
    func onListenStateChanged(state: Int){
        let response = [
            "state": state,
        ]
        if methodChannel != nil {
            methodChannel!.invokeMethod("OnListenStateChanged", arguments: response)
            Utils.log("OnListenStateChanged \(response)")
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
Phần code này sẽ nhận dữ liệu được truyền từ flutter xuống thực thi nó và trả kết quả lại cho flutter.
```Swift
  func exTalk(call: FlutterMethodCall ,result: @escaping FlutterResult) {
        let args = call.arguments as? NSDictionary
        let copies = args?["ex_message"] as? Int
        Utils.log( "copies \(copies) ")

        talking.onListenStateChanged(state: talking.FREE)

        result(NSNumber(value: talking.WORKING))
    }
```

## 3. Truyền dữ liệu
### 3.1. Truyền dữ liệu chủ động, ra lệnh cho native
Giao tiếp chủ động với native sẽ có 2 cách để truyền dữ liệu, cách đầu tiên là chủ động từ flutter gửi xuống native và sau đó native sẽ trả lại kết quả, đây là cách phổ biến. 
chúng ta sẽ giao tiếp với native với method có tên là 'exTalk'. 
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
```


### 3.2. Truyền dữ liệu thụ động, lắng nghe native
Nhưng trong 1 vài trường hợp, chúng ta phải lắng nghe kết quả từ native sau đấy sẽ gửi lại lên flutter. VD 1 câu lệnh từ máy in truyền sang thiết bị, khi đó native sẽ gửi nó lên trên flutter để giải mã câu lệnh đó. 
Chúng ta sẽ gọi đây là cách giao thiếp thụ động. Chúng ta sẽ sử dụng 1 stream để lắng nghe method có tên là "OnListenStateChanged".

```dart
  /// 2.2 | Type 2 Native -> Flutter: Listen native
  Stream<ListenState> get listenState async* {
  yield* _methodStream.stream
      .where((m) => m.method == "OnListenStateChanged")
      .map((m) => m.arguments)
      .map((args) => ListenState.fromMap(args));
  }
```

2 ngôn ngữ khác nhau sẽ có những kiểu dữ liệu khác nhau, chúng ta sẽ sử dụng json để giao tiếp chúng, tức là native sẽ biến kiểu dữ liệu thành json và gửi nó lên flutter, flutter sẽ đọc mã json đó là chuyển thành kiểu dữ liệu mong muốn.
#### Biên dịch dữ liệu từ native thành dữ liệu của Flutter
Trong file `lib/src/message_entity.dart`, giả sử tôi có ListenStateEnum quản lý trạng thái trên flutter, và nó sẽ lắng nghe dữ liệu trong json với key là 'state'.
Giả sử chúng ta gửi từ native lên với dữ liệu của state là 0 thì flutter sẽ nhân được và biên dịch ra kết quả là ListenStateEnum.working
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

## 4. Triển khai trong thực tế

### 4.1. Tạo code example và test các chức năng trong flutter. 
```dart
import 'dart:async';

import 'package:flutter/material.dart';
import 'package:your_plugin/your_plugin.dart';

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
### 4.2. Khi giao tiếp bằng cách giao tiếp chủ động 

Tôi sẽ gửi 1 lệnh từ flutter xuống native và nó sẽ trả về ngay sau đó, tôi sẽ in nó ra màn hình ngay khi có kết quả. 
```dart
 final a = await _yourPlugin.exTalk(exMessage: 3);
 print(a);
```
### 4.3. Khi giao tiếp bằng cách giao tiếp thụ động, lắng nghe từ native. 
Ý tưởng của tôi là sử dụng Stream để lắng nghe dữ liệu, tôi sẽ trình bày 2 cách viết để nhận dữ liệu từ Stream, 
+ Đơn giản nhất là chúng ta sẽ sử dụng StreamBuilder để hiển thị kết quả ra màn hình. 
```dart
 StreamBuilder(
    stream: _yourPlugin.listenState,
    builder: (context, s) {
    return Text("Way 1 - listenState: ${s.data?.state.toString()}");
    }
  ),
```
+ Nếu bạn không cần hiển thị chúng ra màn hình mà sử lý những tác vụ khác phức tạp hơn thì hãy sử dụng StreamSubscription. 
Tôi sẽ khi báo và triển khai nó trong 1 Statefull như sau:
```dart  
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
```
Việc hiển thị kết quả chỉ đơn giản như thế này: 
```dart  
Text("Way 2 - listenState: $_listenState"),
```

## 5. Thiết lập nhanh hơn với mason
Nếu bạn chưa biết mason, hãy tham bảo bài viết này của tôi về mason *LINK mason*. 
Trên đây tôi đã trình bày về cách thiết lập 1 plugin mà tôi đang sử dụng, và 2 cách giao tiếp giữa native và flutter. Bạn có thể thấy nó khá là nhiều công đoạn đúng không. 
Nếu mà mỗi khi tạo 1 plugin mới, chúng ta phải làm đi làm lại những bước trên thì mất rất nhiều thời gian. 
Giải pháp của tôi là sử dụng mason, tôi sẽ tạo những file mẫu code và sau khi phải tạo 1 plugin mới, tôi sẽ chỉ cần sử dụng mason là đã có tất cả các mẫu code trên trong dự án. 
Bạn có thể tham khảo brick dr_plugin của tôi tại https://brickhub.dev/bricks/dr_plugin 


