# Arr_Package
AndroidStudio 多层级 Module 对 aar 引用问题
http://www.cnblogs.com/bellkosmos/p/6146349.html

问题：有个arr文件被放到Module A中引用，现在Module B又依赖了Module A，则在编译过程中会发生错误，Module B找不到aar文件。（同时如果又有Module C 依赖了Module B，C也会出同样的问题）



解法：
1、正常给一个module加入aar文件的步骤是：
把aar文件放入module文件夹下libs包下
然后在项目的build.gradle文件的android节点下加入
repositories {
    flatDir {
        dirs 'libs'
    }
}
然后继续在dependencies节点中加入：compile(name: 'svprogresshud-release', ext: 'aar')
 
2、就是这样会引发上面的问题，所以你要做的是，给所有依赖或间接依赖Module A 的Module的build.gradle的android节点下加入
 
repositories {
    flatDir {
        dirs '../common/libs','libs'
    }
}
其中dirs前面一段路径是Module A的文件夹
 
问题原因：
对于Module B和Module C来说，他们依赖了Module A，不管用不用，他们都会去把Module A的依赖走一遍，当走到你添加的aar本地依赖时，他们去找aar的路径也需要你给出，而且给的方式如果是libs这样的路径，它会去找自己包下的libs，里边自然没有，所以你要给出另一个相对路径：../common/libs这样才能找到，而为了不影响自己这个Module对自己Module下的libs的引用，就使用逗号再添加一个路径
