---
title: Gradle for Android读书笔记
categories: 
- Android
---

##### Gradle的一些基础概念

1. **project和task**:gradle每次构建都至少有一个project参与，每个project都包含若干个task。每一个build.gradle文件都代表着一个project,
2. **生命周期**:



apk=>runtimeOnly

provided=>compileOnly

compile=>implementation

testCompile=>testImplementation

androidTestCompile=>androidTestImplementation