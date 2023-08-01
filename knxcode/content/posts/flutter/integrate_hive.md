---
title: "[Flutter] Integrate Hive in 6 steps"
date: 2023-08-01T11:38:01+07:00
draft: false
tags: ["hive", "dart"]
categories: ["flutter"]
---

## Hive introduction

You can find everything here https://pub.dev/packages/hive

## Steps

1. From Visual Studio Code
   `Cmd + Shift + P`
   to `Add dependency` then search for `hive_flutter`
2. From Visual Studio Code
   `Cmd + Shift + P`
   to `Add Dev dependency` then search for `build_runner` and `hive_generator`
3. Conforms model to `HiveType` and import `part [model_name].g.dart` to generate the adapter in the next step

   ```Dart
   import 'package:hive_flutter/hive_flutter.dart';
   part 'model.g.dart';

    @HiveType(typeId: 0)
        class YourModel {
        @HiveField(0)
        final String property1;
        @HiveField(1)
        final String property2;

        YourModel(this.property1, this.property2);
    }
   ```

4. From the terminal, run `dart run build_runner build` to generate the HiveAdapter
5. Initialize HiveBox, register adapters(if any), and open the box with its name.
   ```Dart
   await Hive.initFlutter();
   Hive.registerAdapter(YourModelAdapter());
   await Hive.openBox('BoxName');
   ```
6. Get the box by its name and use it from there.
   ```Dart
   final box = Hive.box('BoxName');
   ```
