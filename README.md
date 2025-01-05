# theme_change

import 'package:demoexample/screen/home_screen.dart';
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

import 'screen/splash_screen.dart';
import 'view_model/theme_provider.dart';
//name

void main() {
  runApp(
    MultiProvider(
      providers: [
        ChangeNotifierProvider(create: (_) => ThemeProvider()),
      ],
      child: MyApp(),
    ),
  );
}

class MyApp extends StatefulWidget {
  const MyApp({super.key});

  @override
  State<MyApp> createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  @override
  Widget build(BuildContext context) {
    final provider = Provider.of<ThemeProvider>(context);

    if (provider.loading) {
      return MaterialApp(
          debugShowCheckedModeBanner: false, home: SplashScreen());
    }
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      themeMode: provider.themeMode,
      theme: ThemeData.light(),
      darkTheme: ThemeData.dark(),
      home: HomeScreen(),
    );
  }
}


import 'package:demoexample/service/persistance_theme.dart';
import 'package:flutter/material.dart';

class ThemeProvider with ChangeNotifier {
  ThemeMode _themeMode = ThemeMode.light;
  bool _loading = true;

  bool get loading => _loading;

  ThemeMode get themeMode => _themeMode;
  ThemeProvider() {
    loadTheme();
  }

  void changeTheme(bool isDarkMode) async {
    _themeMode = isDarkMode ? ThemeMode.dark : ThemeMode.light;
    await StoreTheme.setTheme(isDarkMode);
    notifyListeners();
  }

  void loadTheme() async {
    _loading = true;
    notifyListeners();
    await Future.delayed(Duration(seconds: 2));
    bool isdarkmode = await StoreTheme.getTheme();
    _themeMode = isdarkmode ? ThemeMode.dark : ThemeMode.light;

    _loading = false;
    notifyListeners();
  }
}


import 'package:shared_preferences/shared_preferences.dart';

class StoreTheme {
  static const _keyTheme = "isDarkMode";

  static Future<void> setTheme(bool isDarkTheme) async {
    SharedPreferences sp = await SharedPreferences.getInstance();

    await sp.setBool(_keyTheme, isDarkTheme);
  }

  static Future<bool> getTheme() async {
    SharedPreferences sp = await SharedPreferences.getInstance();
    return sp.getBool(_keyTheme) ?? false;
  }
}
