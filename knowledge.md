
- OSChina Android 客户端源码
http://www.oschina.net/p/oschina-android-app


1.尽量使用object.equals（）方法来代替之前的equals方法  
2.integer 在-128至127之间可以用等号，在其他区间之内要用equals来判断  
3.好多calendar中的类都是线程不安全的，尤其是simpleDateformat（不能使用static来修饰），在使用的时候记得要加锁  

