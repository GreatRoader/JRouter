# JRouter
路由框架


此框架当前设计共有八大功能
1、跳转（此功能一般的路由框架都具备）
2、自动装载分参数装载与view装载（此功能部分路由框架具备）
3、获取节点IP功能（此功能一般路由框架都不具备）
4、注册自定义服务供其他界面使用（此功能部分路由框架具备）
5、路由表（此功能部分路由框架具备）               开发中
6、观察者模式，仿eventbus(主要对内存方面进行优化，是一个可控的观察者)   开发中
7、进程间通信（从繁杂的aidl文件编写中解放出来）    开发中
8、拦截器（可以拦截工作的每一个步骤，是每一个步骤都清晰可见）  开发中




一、跳转
      无论是同module跳转还是跨module跳转，都可以方便快捷的发起跳转请求，路由内部对URL地址、参数等都会进行校验，
  可轻松实现应用内跳转、唤起系统服务、跨应用跳转等功能。
      分批按需装载节点activity，按module进行第一次分组，在同一个module内部按节点activity所设置的group信息进行第二次分组，
  两次分组保证了内存的合理使用，另外在应用内存不足时，已装载的节点会自动释放，来保证应用的安全运行。路由在装载节点的过程中，
  会将当前目标节点所属最小分组的全部节点同时装载，开发者可以通过合理设置group信息来调整装载的范围，既能保证跳转的最大流畅性
  又能保证保证内存的最优使用。
      URL校验可以规避因URL拼写错误造成的错误跳转，又能根据URL信息分别调用不同逻辑实现应用内跳转、唤起系统服务、跨应用跳转
  等功能
      参数校验可以规避因少传参数或参数类型错误等等原因造成跳转后界面的空指针、类型转换错误等问题，而参数又分为了刚需与非刚需
  参数校验主要校验的是刚需参数，保证刚需参数的完整性，通过注解可以设置任意刚需参数缺失时的提示信息，以及参数对应的key值等等，
  因此框架理念module间应该绝对隔离完全解耦，因此开发者在发起跳转的过程中很可能需要询问目标界面的开发所需传递的参数信息，有时
  因多个团队组合开发，很难快速得到准确的信息，这里就体现了参数校验的合理性，当发生参数错误时，可以依靠跳转回执，轻松了解目标
  节点情况，同时可以依靠跳转回执做一些数据的缓存，临时或占用大量内存对象的回收等等。


二、自动装载
      自动分为跳转后参数的装载与目标界面view的装载（类似butterknife）.
      此框架在自动装载方面的最大的优势把此功能的开关真正的交给了开发者，类似的带有自动装载的框架无论你是否需要这个功能，他们
  的内部逻辑都会在你编译项目时生成，并创建大量的逻辑文件，这些不需要的文件会给apk体积带来巨大的负担，并严重影响工程编译的速
  度，开发者能决定的仅仅是要不要调用这些文件，而不是需不需要创建这些文件。此框架通过gradle配置可以灵活选择哪个module开启，
  哪个module不开启，而在开启的module中又可以选择哪个界面需要这个功能，哪个界面不需要，真正实现了由开发者来决定自己需要
  什么不需要什么。
      1、参数装载
         省去了跳转后的getIntent.getXXXX等繁琐的代码，完全可以直接拿来用，当然仅仅针对于刚需参数，而那些非刚需的仍然需要
      判空，毕竟这个不在参数校验的范围(^～^)。
      2、view装载
         相信用过butterknife的都知道



三、获取节点IP
      此框架会自动对开发者所设置的IP地址path与group进行校验，在多个团队协作开发中，IP地址冲突不可避免，或者因手误没能
  按照正确IP地址规则设置的应有尽有，这种事情本来就无可厚非，人非圣贤孰能无过，总不能有一点错误就得修改-编译-运行重新来一次吧，
  因此当框架发现错误时候会尝试对错误进行修复，并打印日志供开发者查看并决定是否需要立刻修改或者下次编译前修改，只有遇见无法修复的
  情况时才会打断编译过程，并将问题抛出由用户来处理。
      由于依靠路由进行跳转，所以发起跳转方需要知道目标IP，如果将IP用硬编码的方式写进代码中，会非常的不友好，假如某一天因项
  目原因需要修改一个节点的IP地址，恐怕没人能确定修改后都会影响哪些界面吧，问题日积月累，直到最后爆发，而获取节点IP的功能就是
  为解决这个问题而来，由于IP是动态获取的，所以会随着目标节点IP地址的改变而改变，让你想改就改，从此再也没有后顾之忧，妈妈
  再也不用担心我会跳转错误了。
  


四、自定义服务
      没有任何框架敢说自己是完美的，能满足开发者的一切需求，因此作为优秀的框架最大的一个特点就是扩展性，优秀的框架并不会提供
  所有的服务，而是把特殊服务的设计交给开发者，由开发者来自定义他们需要的服务，框架的作用在于对这些具有不同特色的服务的管理，开发者可
  以随心所欲的注册以及解注册自身需要的服务，并在正确的地方调用这些服务，框架负责对自定义服务进行统一管理、调配。
  
  
  
五、路由表
      用来标识每一个节点的IP、参数、包名、注释等信息的文件，方便新加入团队的开发能够迅速融入项目中来，更方便与同事间的协作开发，
  可以很轻松就能看懂各个模块的具体信息，以及开发进度等等
  
  
六、观察者模式（暂时没想好是依靠OttoBus来做，还是自己实现一套管理类）
      是一个额外功能，大家使用eventbus的最大原因应该都是看上了跨界面通信吧，至少我是因为这个才使用eventbus的，毕竟eventbus
  确实非常的简洁，可以用最少的代码实现自己需要的功能。
  
  
七、进程间通信
      大家在实际开发中避免不了的需要用到多进程，而跨进程通信又需要编写aidl文件，很多开发都为此略感头疼，而此功能的出现就是专为
  解决头疼而来（恩，最近头腾大战也挺火的），大致设计思路：开发者与路由中的代理中心进行交互，由代理中心负责与service进行binder
  交互，曲线实现进程间的通信。
  
  
八、拦截器
      相信很多人都用过阿里的ARouter，此功能就是仿造ARouter而来，让路由工作中的每一步都变得清晰明朗，并允许开发者通过拦截器超
  出路由的限制实现各种不可思议的功能












