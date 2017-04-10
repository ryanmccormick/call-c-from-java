# Call Native C Program From Java on OSX
---
Java is a pretty awesome language with almost unlimited use cases, but when it comes to anything computationally intensive, `C` is better. I had a very specific use case where calling C from Java made more sense performance-wise. Too advanced to go into the details here, but I wanted to post a tutorial for anyone looking to implement. At a very high level, this tutorial calls native C (NOT c++) from Java to print "Hello World!!".

## 1: Create HelloWorld.java
---
```
class HelloWorld {
    private native void print();

    public static void main(String[] args) {
        new HelloWorld().print();
    }
    
    static {
        System.loadLibrary("hello");
    }
}
```

## 2: Compile HelloWorld class and create native header file
---
1. Compile with `javac HelloWorld.java`
2. Create Header `javah -jni HelloWorld`

## 3: Create HelloWorld.c file
---
```
#include <jni.h>
#include <stdio.h>
#include "HelloWorld.h"

JNIEXPORT void JNICALL
Java_HelloWorld_print(JNIEnv *env, jobject obj) {
    printf("Hello World!!\n");
    return;
}
```
## 4: Compile HelloWorld.c, create dynamic library and bundle
---
1. Compile `cc -c -I/System/Library/Frameworks/JavaVM.framework/Headers HelloWorld.c`
2. Create dynamic library `cc -dynamiclib -o libhello.jnilib HelloWorld.o -framework JavaVM`
3. Compile JNI Library `cc -g -I/System/Library/Frameworks/JavaVM.framework/Headers -c -o HelloWorld.o HelloWorld.c`

## 5. Moment of truth, run the app
---
1. Run `java HelloWorld`
2. You should see `Hello World!!` in your terminal.

## References
---
1. Old Article from online Java book. Sun Microsystems.
http://web.archive.org/web/20120419230023/http://java.sun.com/docs/books/jni/html/start.html
2. Core Java APIs and the Java Runtime on OSX.
https://developer.apple.com/library/content/documentation/Java/Conceptual/Java14Development/05-CoreJavaAPIs/CoreJavaAPIs.html
3. Using OS X Native Application Environments.
https://developer.apple.com/library/content/documentation/Porting/Conceptual/PortingUnix/environments/environments.html
4. Getting Started with JNI and C++ on OSX Lion
http://mrjoelkemp.com/2012/01/getting-started-with-jni-and-c-on-osx-lion/
