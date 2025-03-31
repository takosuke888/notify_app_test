### flutter_local_notificationsの導入
[link](https://zenn.dev/slowhand/articles/f2dc182c7f5dd7)を参考に進めた
#### 修正箇所
- パッケージバージョン: flutter_local_notifications: ^19.0.0
- android/app/build.gradle.kts
    ```
    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_1_8
        targetCompatibility = JavaVersion.VERSION_1_8
        isCoreLibraryDesugaringEnabled = true
    }
    ```
    ```
    defaultConfig {
        ...
        multiDexEnabled = true
    }
    ```
    ```
    dependencies {
        coreLibraryDesugaring("com.android.tools:desugar_jdk_libs:2.1.4")
    }
    ```
- android/build.gradle.kts
    ```
    buildscript {
        repositories {
            google()
            mavenCentral()
        }
        dependencies {
            classpath("com.android.tools.build:gradle:7.3.1")
        }
    }
    ```

### 通知テストコード
https://flutter.salon/plugin/flutter_local_notifications/#Android%E3%81%AE%E8%A8%AD%E5%AE%9A

### TimeZoneによる時間指定通知

- ```flutter pub add timezone```
- [link]()を参考にしたが、notificationの仕様が変更されているようだった。
    ```dart
    void _scheduleNotification() async {
        final now = tz.TZDateTime.now(tz.local);
        var scheduledDate = tz.TZDateTime(tz.local, now.year, now.month, now.day, 7);

        if (scheduledDate.isBefore(now)) {
        scheduledDate = scheduledDate.add(const Duration(days: 1));
        }

        const androidDetails = AndroidNotificationDetails(
        'channel_id',
        'channel_name',
        importance: Importance.high,
        priority: Priority.high,
        );
        const iosDetails = DarwinNotificationDetails();
        const notificationDetails = NotificationDetails(android: androidDetails, iOS: iosDetails);

        await flutterLocalNotificationsPlugin.zonedSchedule(
        0,
        'Notification title',
        'Notification message',
        scheduledDate,
        notificationDetails,
        androidScheduleMode: AndroidScheduleMode.exactAllowWhileIdle, // 修正
        matchDateTimeComponents: DateTimeComponents.time, // ここはそのまま
        );
    }
    ```
