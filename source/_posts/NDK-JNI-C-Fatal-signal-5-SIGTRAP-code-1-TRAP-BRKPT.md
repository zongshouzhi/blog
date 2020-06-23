---
title: NDK JNI C++  Fatal signal 5 (SIGTRAP) code 1 (TRAP_BRKPT)
tags: jni
categories: BUGLIST
date: 2020-06-23 14:46:58
---


 Fatal signal 5 (SIGTRAP) code 1 (TRAP_BRKPT)

这个错误是说JNI函数或者使用到的C++函数缺少指定的返回值。

```
extern "C"
JNIEXPORT bool JNICALL
Java_com_yang_cmakedemo_NativeRender_native_1OnUnInit(JNIEnv *env, jobject thiz) {
    MyGLRenderContext::destroyInstance();
    //fixme 缺少返回值，运行时会报错
}
```

或者是c++函数缺少返回值，运行时也会报错

```
GLuint GLUtils::CreateProgram(const char *pVertexShaderSource, const char *pFragShaderSource,
                              GLuint &vertexShaderHandle, GLuint &fragShaderHandle) {
    GLuint program = 0;
    //fixme 缺少返回值，运行时会报错
}
```

遇到这个问题，要仔细排查一下调用链条上的方法是否缺少return语句，祝幸运。