---
title: 聊聊JNI
date: 2023-10-04 19:36:23
tags: Java
---
引言：本文讲讲JNI的使用和开发过程中遇到的问题
<!--more-->
# 一，JNI是用来干什么的
**什么是JNI?**
JNI 是指 Java 原生接口。它定义了 Android 从受管理代码（使用 Java 或 Kotlin 编程语言编写）编译的字节码与原生代码（使用 C/C++ 编写）互动的方式。

**那什么场景下需要用到c++呢？**
  在Android编程中，出于硬件交互，跨平台，安全性，第三方库等方面的考虑，我们需要Java与C/C++互相调用，这就需要借助Java平台的JNI接口。
  
# 二，JNI怎么用
## 2.1 JNI具体功能及JNIEnv,JavaVM
通过通过JNI，你可以在java中调用native方法，也可以在native代码中：
* 创建、检查或者更新java对象
* 调用java方法
* 捕捉和抛出异常
* 加载class和获取class信息
* 运行时类型检查

本地代码是通过一系列JNI函数（如FindClass等）来访问JVM的，这些JNI函数是通过一系列指针来访问的，而访问这堆指针存在一个表内，由一个叫JNIEnv的指针来访问。这就有点像是c++的虚函数表。**这样设计的好处是什么呢?**可以一个VM可以提供多个版本的JNI函数表（比如同样的函数一个带非法参数检查，适合调试；一个最小检查，效率高，用来正式执行）。
JNIEnv是一个ThreadLocal类型的指针，只在本线程有效，不能跨线程传递。JNIEnv也是本地方法的一个重要参数，每个本地方法拿到的都是它所在线程的JNIEnv。
![](/img/17016947421226.jpg)
JavaVM和JNIEnv一样，两者本质上都是指向函数表的二级指针。只不过JavaVM是一个进程唯一的，函数表里是销毁VM,绑定/解绑线程之类的。
```
/*
 * C++ version.
 */
struct _JavaVM {
    const struct JNIInvokeInterface* functions;

#if defined(__cplusplus)
    jint DestroyJavaVM()
    { return functions->DestroyJavaVM(this); }
    jint AttachCurrentThread(JNIEnv** p_env, void* thr_args)
    { return functions->AttachCurrentThread(this, p_env, thr_args); }
    jint DetachCurrentThread()
    { return functions->DetachCurrentThread(this); }
    jint GetEnv(void** env, jint version)
    { return functions->GetEnv(this, env, version); }
    jint AttachCurrentThreadAsDaemon(JNIEnv** p_env, void* thr_args)
    { return functions->AttachCurrentThreadAsDaemon(this, p_env, thr_args); }
#endif /*__cplusplus*/
};

/*
 * JNI invocation interface.
 */
struct JNIInvokeInterface {
    void*       reserved0;
    void*       reserved1;
    void*       reserved2;

    jint        (*DestroyJavaVM)(JavaVM*);
    jint        (*AttachCurrentThread)(JavaVM*, JNIEnv**, void*);
    jint        (*DetachCurrentThread)(JavaVM*);
    jint        (*GetEnv)(JavaVM*, void**, jint);
    jint        (*AttachCurrentThreadAsDaemon)(JavaVM*, JNIEnv**, void*);
};
```
**官方的建议 JNI 解决方案应该尝试遵循以下准则**（按重要程度依次列出，从最重要的开始）：
* 尽可能减少跨 JNI 层编组资源的次数。跨 JNI 层进行编组的费用十分高昂。尝试设计一种接口，尽可能减少需要编组的数据量以及必须进行数据编组的频率。
* 尽可能避免在使用受管理编程语言编写的代码与使用 C++ 编写的代码之间进行异步通信。这样可使 JNI 接口更易于维护。通常，您可以采用与编写界面相同的编程语言保持异步更新，以简化异步界面更新。例如，最好使用 Java 编程语言在两个线程之间进行回调（其中一个线程发出阻塞 C++ 调用，然后在阻塞调用完成时通知界面线程），而不是通过 JNI 从使用 Java 代码的界面线程调用 C++ 函数。
* 尽可能减少需要接触 JNI 或被 JNI 接触的线程数。如果您确实需要使用 Java 和 C++ 这两种语言的线程池，请尽量保持在池所有者之间（而不是各个工作器线程之间）进行 JNI 通信。
* 将接口代码保存在少量易于识别的 C++ 和 Java 源位置，以便将来进行重构。酌情考虑使用 JNI 自动生成库。


## 2.2 一个JNI模块的代码是什么样的呢?
**Java代码调用C++方向:**
1. 首先我们需要在java代码中加载对应c++代码的库，注意库名不用加后缀，系统会根据平台类型自动转换。
   并将后续在native中实现的方法前注明native关键字。
```
package pkg;  
class Cls { 
    public native void register();
    public native String sayHello();
    public native double f(int i, String s); 
     static { 
         System.loadLibrary(“pkg_Cls”); 
     } 
} 
```
2. 加载完了之后，我们需要完成c++部分逻辑，并将它和java中我们声明的native方法link上，如此java中就能调用native方法了。
   这部分分为两种方式：静态注册和动态注册。
* 静态注册
只需要native方法名满足一定规则（可以用javah命令直接生成对应规则的头文件），如
```
//注意，c和c++在使用JNI接口的时候有点不一致，请仔细观察通过env调用接口的调用方式
//C版本
jdouble Java_pkg_Cls_f__ILjava_lang_String_2 (
     JNIEnv *env,        /* interface pointer */
     jobject obj,        /* "this" pointer */
     jint i,             /* argument #1 */
     jstring s)          /* argument #2 */
{
     /* Obtain a C-copy of the Java string */
     const char *str = (*env)->GetStringUTFChars(env, s, 0);
     /* process the string */
     ...
     /* Now we are done with str */
     (*env)->ReleaseStringUTFChars(env, s, str);
     return ...
}

//C++版本
extern "C" /* specify the C calling convention */  
jdouble Java_pkg_Cls_f__ILjava_lang_String_2 ( 
     JNIEnv *env,        /* interface pointer */ 
     jobject obj,        /* "this" pointer */ 
     jint i,             /* argument #1 */ 
     jstring s)          /* argument #2 */ 
{ 
     const char *str = env->GetStringUTFChars(s, 0); 
     ... 
     env->ReleaseStringUTFChars(s, str); 
     return ... 
} 
```
* 动态注册
通过JNI RegisterNatives()使用作为参数传递的类来注册本机方法。通过使用这种方法，我们可以根据需要命名 C++ 函数，这种方式的使用更为广泛。
动态注册的还有一个好处是可以通过只导出 JNI_OnLoad 来获得规模更小、速度更快的共享库，并避免与加载到应用中的其他库发生潜在冲突。（使用 -fvisibility=hidden 进行构建，只JNIEXPORT JNI_OnLoad一个方法）
```
static JNINativeMethod methods[] = {
  {"sayHello", "()Ljava/lang/String;", (void*) &hello },
};

JNIEXPORT jint JNICALL JNI_OnLoad(JavaVM* jvm, void* reserved) {
    jclass clazz = env->FindClass("pkg/Cls");
    (env)->RegisterNatives(clazz, methods, sizeof(methods)/sizeof(methods[0]));
}

JNIEXPORT jstring JNICALL hello (JNIEnv* env, jobject thisObject) {
    std::string hello = "Hello from registered native C++ !!";
    std::cout << hello << std::endl;
    return env->NewStringUTF(hello.c_str());
}

```
**C++代码调用Java方向:**
1. 总结起来就是通过JNIEnv来访问JNI函数们，最终反射调用Java方法或者成员。
这个就非常多了（JNINativeInterface.h中定义），详见
https://docs.oracle.com/javase/8/docs/technotes/guides/jni/spec/functions.html

## 2.3 JNI中的类型和数据结构
JNI中会将java和c的类型做一个对应，包括数据类型，引用类型，方法签名。
### 1）基本类型和引用类型对应关系
![](/img/17016993819468.jpg)
![](/img/17016929384377.jpg)

## 2）字段和方法ID
在C中，字段和方法ID是一个指向结构体的指针。
```
struct _jfieldID;                       /* opaque structure */
typedef struct _jfieldID* jfieldID;     /* field IDs */

struct _jmethodID;                      /* opaque structure */
typedef struct _jmethodID* jmethodID;   /* method IDs */
```
## 3）值类型
```
typedef union jvalue {
    jboolean    z;
    jbyte       b;
    jchar       c;
    jshort      s;
    jint        i;
    jlong       j;
    jfloat      f;
    jdouble     d;
    jobject     l;
} jvalue;
```
## 4）类型签名
![](/img/17017034232590.jpg)
 
# 三，JNI开发过程中的坑和注意事项
## 3.1 未调用DetachCurrentThread导致线程无法正常退出
首先我们知道JNIEnv是ThreadLocal的，在native代码中自己创建线程，那么这个线程不包含任何 JNIEnv，也无法调用 JNI。除非调用AttachCurrentThread() 或 AttachCurrentThreadAsDaemon() 函数附加通过 pthread_create() 或 std::thread 启动的线程。
  这里注意，通过 JNI 附加的线程在退出之前必须调用 DetachCurrentThread()，否则会导致线程无法正常退出，有类似错误日志：”thread exiting, not yet detached”，甚至导致VM abort。
  Google官方JNI指南文档建议在Android2.0以上可使用pthread_key，在线程析构时自动调用Detach以简化操作。
  不过需要注意一个进程中pthread_key的数量是有限制的，特别是三星Android4.3手机的可用pthread_key只有64个，尽量进程内复用pthread_key。下面是参考代码：
```
extern "C" {
    pthread_key_t s_threadKey;

    static void detach_current_thread_(void *env)
    {
        JAVAVM->DetachCurrentThread();
    }

    static bool getenv_(JNIEnv **env)
    {
        bool bRet = false;
        switch (JAVAVM->GetEnv((void **)env, JNI_VERSION_1_4))
        {
            case JNI_OK:
                bRet = true;
                break;
            case JNI_EDETACHED:
                if (JAVAVM->AttachCurrentThread(env, 0) < 0)
                {
                    break;
                }
                if (pthread_getspecific(s_threadKey) == NULL)
                {
                    pthread_setspecific(s_threadKey, env);
                }
                bRet = true;
                break;
            default:
                break;
        }
        return bRet;
    }

    void JniHelper::SetJavaVM(JavaVM *vm)
    {
        static bool is_init = false;
        if (is_init == false)
        {
            is_init = true;
            pthread_key_create(&s_threadKey, detach_current_thread_);
            LOG_INFO("init pthread_key");
        }
        ......
    }
}
```

## 3.2 局部引用超限
### 1）关于局部引用和全局引用
传递给原生方法的每个参数，以及 JNI 函数返回的几乎每个对象都属于“局部引用”。这意味着，局部引用在当前线程中的当前原生方法运行期间有效。在原生方法返回后，即使对象本身继续存在，该引用也无效。这适用于 jobject 的所有子类，包括 jclass、jstring 和 jarray。
获取非局部引用的唯一方法是通过 NewGlobalRef 和 NewWeakGlobalRef 函数。
请注意，jfieldID 和 jmethodID 属于不透明类型，不是对象引用，且不应传递给 NewGlobalRef。

### 2）问题
一般情况下，我们不需要处理局部引用。但是
* 如果调用过程中如果存在循环、递归等调用层次过多的情况
* 使用 AttachCurrentThread附加原生线程，那么在线程分离之前，JNI绝不会自动释放局部引用
这两种情况，很可能会导致局部引用数量超过局部引用限制导致崩溃，从而导致可能会出现内存泄漏或局部引用超限(local reference table overflow)的问题。

因此，我们定制规范，在局部引用使用完毕后，需要尽快调用DeleteLocalRef手动删除局部引用。

## 3.3 多线程场景下FindClass调用失败
在自己创建的线程(类似通过pthread_create)中调用FindClass会失败得到空的返回，从而导致调用失败。
如果在Java层调用到native层，会携带栈桢(stack frame)信息，其中包含此应用类的Class Loader，因此场景下JNI能通过此应用类加载器获取自定义类信息。 而在使用自己创建并Attach到虚拟机的线程时，因为没有栈桢(stack frame)信息，此场景下因为类加载器的双亲委派模型，虚拟机会通过系统类加载器寻找应用类信息，但此类加载器并未加载应用类，因此FindClass返回空。
注意：这里因为是系统类加载器，所以系统库的类(string,map等)是依然可以获取的。
建议通过缓存应用类的Class Loader解决此问题，下面是参考代码。另外还需注意检查类名有没有写错(格式类似于java/lang/String)，并且确认相应的类没有被混淆。
```
// java代码
public class JniAdapter {
    public static ClassLoader getClassLoader() {
        return JniAdapter.class.getClassLoader();
    }
}

// C/C++代码
JavaVM *JniHelper::java_vm_ = NULL;
jobject JniHelper::class_loader_obj_ = NULL;
jmethodID JniHelper::find_class_mid_ = NULL;

void JniHelper::SetJavaVM(JavaVM *vm)
{
    ......
    java_vm_ = vm;
    JNIEnv *env;
    if (!getenv_(&env))
    {
        return;
    }
    jclass classLoaderClass = env->FindClass("java/lang/ClassLoader");
    jclass adapterClass = env->FindClass("com/xxx/xxx/framework/JniAdapter");
    if (adapterClass)
    {
        jmethodID getClassLoader = env->GetStaticMethodID(adapterClass, "getClassLoader", "()Ljava/lang/ClassLoader;");
        jobject obj = env->CallStaticObjectMethod(adapterClass, getClassLoader);
        class_loader_obj_ = env->NewGlobalRef(obj);
        find_class_mid_ = env->GetMethodID(classLoaderClass, "loadClass", "(Ljava/lang/String;)Ljava/lang/Class;");
        env->DeleteLocalRef(classLoaderClass);
        env->DeleteLocalRef(adapterClass);
        env->DeleteLocalRef(obj);
    }
}

jclass JniHelper::GetClass(const char *className)
{
    CheckAndClearException();

    JNIEnv *p_env = 0;
    jclass ret = 0;
    do
    {
        if (!p_env)
        {
            if (!getenv_(&p_env))
            {
                break;
            }
        }
        jstring j_class_name = p_env->NewStringUTF(className);
        ret = (jclass)p_env->CallObjectMethod(
            JniHelper::class_loader_obj_, JniHelper::find_class_mid_, j_class_name);
        p_env->DeleteLocalRef(j_class_name);
    } while (0);
    if (!ret)
    {
        LOG_ERROR("Failed to find class of %s", className);
    }
    return ret;
}
```
## 3.4 使用emoji表情导致Crash或服务端解析失败
JNI中使用的是MUTF-8。通过JNI的NewStringUTF方法把C++的字符串转换为jstring时，如果入参为emoji表情或其他非Modified UTF8编码字符将导致Crash。
另外使用JNI的GetStringUTFChars方法把jstring转换为C++字符串时得到的字符串编码为MUTF-8，如果直接传递到服务端或其他使用方，emoji表情将出现解析失败的问题。
```
jstring ToJavaString(const char *buffer, int size)
{
    jclass str_class = GetClass("java/lang/String");
    jmethodID init_mid = JNIENV->GetMethodID(str_class, "<init>", "([BLjava/lang/String;)V");
    jbyteArray bytes = JNIENV->NewByteArray(size);
    JNIENV->SetByteArrayRegion(bytes, 0, size, (jbyte *)buffer);
    jstring encoding = JNIENV->NewStringUTF("utf-8");
    jstring result = (jstring)JNIENV->NewObject(str_class, init_mid, bytes, encoding);
    JNIENV->DeleteLocalRef(str_class);
    JNIENV->DeleteLocalRef(encoding);
    JNIENV->DeleteLocalRef(bytes);
    return result;
}

std::string ToStdString(jstring jstr)
{
    std::string result;
    jclass str_class = JNIENV->FindClass("java/lang/String");
    jstring encoding = JNIENV->NewStringUTF("utf-8");
    jmethodID mid = JNIENV->GetMethodID(str_class, "getBytes", "(Ljava/lang/String;)[B");
    JNIENV->DeleteLocalRef(str_class);

    jbyteArray jbytes = (jbyteArray)JNIENV->CallObjectMethod(jstr, mid, encoding);
    JNIENV->DeleteLocalRef(encoding);

    jsize str_len = JNIENV->GetArrayLength(jbytes);
    if (str_len > 0)
    {
        char *bytes = (char*)malloc(str_len);
        JNIENV->GetByteArrayRegion(jbytes, 0, str_len, (jbyte*)bytes);
        result = std::string(bytes, str_len);
        free(bytes);
    }
    JNIENV->DeleteLocalRef(jbytes);
    return result;
}
```
## 3.5 缓存常用jmethodID 和 jfieldID
C++调用java需要查找类，查找方法，查找方法ID，获取字段或者方法的调用有时候会需要在JVM中完成大量工作，因为字段和方法可能是从超类中继承而来的，这会让JVM向上遍历类层次结构来找到它们，这是个开销很大的操作。但是为特定类返回的id不会在JVM进程生存期间发生变化。
所以，缓存ID字段可以降低CPU负载，提高运行速度，节约电量。

## 3.6 异常的捕获
1）java代码po异常
native侧可以通过ExceptionCheck，ExceptionDescribe，ExceptionClear检测打印消除。
所以如果要调用java方法（使用 CallObjectMethod 等函数），则必须始终检查异常，因为如果系统抛出异常，返回值将无效。
2）如果native侧crash
Android 尚不支持 C++ 异常，抛出的异常不会展开原生堆栈帧。所以如果有打印需要，可以自行try-catch住后，调用JNI Throw/ThrowNew抛到java层打印/处理（或者native层自行处理，看需求）。 

# 四，总结
JNI属于上手不难，但容易有坑的东西。网上关于JNI的资料都零零散散，还是官网的比较全，更推荐。


# 五，参考
https://developer.android.com/training/articles/perf-jni?hl=zh-cn
https://docs.oracle.com/javase/7/docs/technotes/guides/jni/spec/intro.html