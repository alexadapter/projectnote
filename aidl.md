# aidl (android interface definition language)
> android提供在不同进程之间进行数据通信接口
  只谈具体实现方法,不谈原理
  

### aidl工程 (IAgentAidl.aidl为例)
* ####应用于service
>*为什么只运用于service？你猜.. ***android四大组件里面还有谁不能跨进程访问吗？逗比***
* ####aidl文件 
 >*定义提供出去的接口..主要功能方法提供在于此,但是只支持简单数据格式int,String 等*

```java
    package cn.dream.watchagent;
    
    //import cn.dream.watchagent.model.data.ContactBean;
    //parcelable con;
    interface IAgentAidl {
        /**
        * app send loc to server
        * */
        void location();
    }
```

* ####创建重载了IAgentAidl.Stub的对象
 >*IAgentAidl.stub是aidl文件动态生成的,用于创建binder对象,并实现具体的功能操作...类似于Linux的进程binder..啪啪啪*

```java
    public class IAgentAidlStub extends IAgentAidl.Stub {
        @Override
        public void location() throws RemoteException {
            //do nothings...
        }
    }
```

* ####OnBind 里面返回创建的IAgentAidl.stub对象*
 
```java
    IAgentAidlStub mBinder = new IAgentAidlStub();
    @Override
    public IBinder onBind(Intent intent) {
        // TODO: Return the communication channel to the service.
        Log.d(TAG, "onBind");
        return mBinder;
    }
```

### 使用
* ####拷贝aidl文件到具体使用的工程路径下
 >*路径=定义aidl的packagename*
* ####创建一个重载于ServiceConnection的类
```java
private class MyServiceConnection implements ServiceConnection {
    @Override
    public void onServiceConnected(ComponentName name, IBinder service) {
        iAgentAidl = IAgentAidl.Stub.asInterface(service);

        if(iAgentAidl==null){
            LogUtils.D(TAG,"bind fail");
            return;
        }
    }

    @Override
    public void onServiceDisconnected(ComponentName name) {
        iAgentAidl = null;
    }
}
```

* ####接下来就简单了

```java
MyServiceConnection connection = new MyServiceConnection();
//使用的地方调用
Intent intent  = new Intent(actionName);
intent.setPackage("cn.dream.watchagent");
bindService(intent, connection, Context.BIND_AUTO_CREATE);
```
```java
//退出的地方
unbindService(connection);
```
```java
//使用的地方
iAgentAidl.location();
```
***一个简单的aidl工程就创建好了,so easy.. 如果创建自定义数据尼？？***

* 首先定义一个用于传递的bean(重载Parcelable接口)*不要问原因,不序列化跨进程的数据传输..我也没见过*

```java
    package cn.dream.watchagent.model.data;
    
    import android.os.Parcel;
    import android.os.Parcelable;
    
    /**
     * Created by alex on 15-12-1.
     */
    public class ContactBean implements Parcelable{
        private String uuid;
        
        public ContactBean(){
        }
    
        protected ContactBean(Parcel in) {
            uuid = in.readString();
        }
    
        public static final Creator<ContactBean> CREATOR = new Creator<ContactBean>() {
            @Override
            public ContactBean createFromParcel(Parcel in) {
                return new ContactBean(in);
            }
    
            @Override
            public ContactBean[] newArray(int size) {
                return new ContactBean[size];
            }
        };
    
        public String getUuid() {
            return uuid;
        }
    
        public void setUuid(String uuid) {
            this.uuid = uuid;
        }
    
        @Override
        public int describeContents() {
            return 0;
        }
    
        @Override
        public void writeToParcel(Parcel dest, int flags) {
            dest.writeString(uuid);
        }
    }
```

* ####开启装逼模式
 >* 创建ContactBean.aidl..设置路径和ContactBean.java一样..内容为...

```java
    // ContactBean.aidl
    package cn.dream.watchagent.model.data;
    parcelable ContactBean;
```

 **好了,最后在IAgentAidl里面**
 
 ```java
    import cn.dream.watchagent.model.data.ContactBean
 ```
 **这样IAgentAidl里面就能使用ContactBean对象了**
 
 *注意:自定义对象需要注明是in,还是out,例如:*
 ```java
    void modifyContact(String type,in ContactBean bean);
 ```


