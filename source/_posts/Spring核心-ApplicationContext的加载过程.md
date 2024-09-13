---
title: Spring核心-ApplicationContext的加载过程
date: 2018/11/27
updated: 2018/11/27
---

我们在使用 Spring 的时候，一般来说都会通过这个方式来实例化一个`applicationContext`

```
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("classpath*:applicationContext.xml");
```

接下来我们就一步步的来看看`applicationContext`的加载过程

#ClassPathXMLApplicationContext

```
public class ClassPathXmlApplicationContext extends AbstractXmlApplicationContext {
    private Resource[] configResources;
    //无参构造
    public ClassPathXmlApplicationContext() {
    }
    //直接传入一个ApplicationContext对象进行加载
    public ClassPathXmlApplicationContext(ApplicationContext parent) {
        //调用父类的构造
        super(parent);
    }
    //根据传入的配置文件路径进行构造
    //自动刷新上下文
    public ClassPathXmlApplicationContext(String configLocation) throws BeansException {
        //调本类的方法
        this(new String[]{configLocation}, true, (ApplicationContext)null);
    }
    //根据传入的多个配置文件进行构造
    //自动刷新上下文
    public ClassPathXmlApplicationContext(String... configLocations) throws BeansException {
        this(configLocations, true, (ApplicationContext)null);
    }
    //使用给定父类进行构建
    //自动刷新上下文
    public ClassPathXmlApplicationContext(String[] configLocations, ApplicationContext parent) throws BeansException {
        this(configLocations, true, parent);
    }
    //根据传入的配置文件们进行构建
    //自定义是否刷新
    public ClassPathXmlApplicationContext(String[] configLocations, boolean refresh) throws BeansException {
        this(configLocations, refresh, (ApplicationContext)null);
    }
    //根据传入的配置文件们以及父类进行构建
    //自定义是否刷新
    public ClassPathXmlApplicationContext(String[] configLocations, boolean refresh, ApplicationContext parent) throws BeansException {
        //调用父类构造
        super(parent);
        //设置参数
        this.setConfigLocations(configLocations);
        //如果判断成功则调用刷新方法
        if(refresh) {
            this.refresh();
        }

    }

    //下面的几个方法都是通过加载类的路径从而实现ClassPathXmlApplicationContext，这里就不一一列举了
    public ClassPathXmlApplicationContext(String path, Class<?> clazz) throws BeansException {
        this(new String[]{path}, clazz);
    }

    public ClassPathXmlApplicationContext(String[] paths, Class<?> clazz) throws BeansException {
        this(paths, clazz, (ApplicationContext)null);
    }

    public ClassPathXmlApplicationContext(String[] paths, Class<?> clazz, ApplicationContext parent) throws BeansException {
        super(parent);
        Assert.notNull(paths, "Path array must not be null");
        Assert.notNull(clazz, "Class argument must not be null");
        this.configResources = new Resource[paths.length];

        for(int i = 0; i < paths.length; ++i) {
            this.configResources[i] = new ClassPathResource(paths[i], clazz);
        }

        this.refresh();
    }

    protected Resource[] getConfigResources() {
        return this.configResources;
    }
}
```

关于`ClassPathXmlApplicationContext`这个类的话我们可以看到其主要方法还是
#ClassPathXmlApplicationContext 的构造

```
	public ClassPathXmlApplicationContext(
			String[] configLocations, boolean refresh, @Nullable ApplicationContext parent)
			throws BeansException {

		super(parent);
		setConfigLocations(configLocations);
		if (refresh) {
			refresh();
		}
	}
```

在这里仍然是去调用了父类的构造，因为调用的过程是一层层的调用，我在这就不贴上每个类的方法了，在这里了解一下调用流程
`ClassPathXmlApplicationContext` => `AbstractXmlApplicationContext` => `AbstractRefreshableConfigApplicationContext` => `AbstractRefreshableApplicationContext` =>`AbstractApplicationContext`
按照了从上至下的顺序进行调用，接下来看一下终点站`AbstractApplicationCotext`中是如何处理的
#AbstractApplicationContext 的构造

```
public AbstractApplicationContext(ApplicationContext parent) {
        //先去调用了本类的无参构造
        this();
        //接着调用了一个set方法
        this.setParent(parent);
    }
```

这个`setParent(ApplicationContext parent)`有什么作用呢
#AbstractApplicationContext.setParent(ApplicationContext parent)

```
public void setParent(ApplicationContext parent) {
        //如果是null则直接赋值
        this.parent = parent;
        //如果不为null则获取该applicationContext的环境进行修改
        if(parent != null) {
            Environment parentEnvironment = parent.getEnvironment();
            if(parentEnvironment instanceof ConfigurableEnvironment) {
                this.getEnvironment().merge((ConfigurableEnvironment)parentEnvironment);
            }
        }
    }
```

再看一下本类的无参构造
#AbstractApplicationContext 的无参构造

```
    public AbstractApplicationContext() {
        this.logger = LogFactory.getLog(this.getClass());
        this.id = ObjectUtils.identityToString(this);
        this.displayName = ObjectUtils.identityToString(this);
        this.beanFactoryPostProcessors = new ArrayList();
        this.active = new AtomicBoolean();
        this.closed = new AtomicBoolean();
        this.startupShutdownMonitor = new Object();
        this.applicationListeners = new LinkedHashSet();
        this.resourcePatternResolver = this.getResourcePatternResolver();
    }
```

很明显该无参构造只是对属性进行了初始化，在这里头两个方法我们都已经查看完毕了，接着，我们来看看核心部分`refresh()`方法
注意，此时`ClassPathXmlApplicationContext`并没有重写该方法，这里是直接调用的是`AbstractApplicationContext`中的方法

# refresh()

```
public void refresh() throws BeansException, IllegalStateException {
                //startupShutdowMonitor 一个用于同步启动和销毁的监视器
		synchronized (this.startupShutdownMonitor) {

			prepareRefresh();

			ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

			prepareBeanFactory(beanFactory);

			try {
				// Allows post-processing of the bean factory in context subclasses.
				postProcessBeanFactory(beanFactory);

				// Invoke factory processors registered as beans in the context.
				invokeBeanFactoryPostProcessors(beanFactory);

				// Register bean processors that intercept bean creation.
				registerBeanPostProcessors(beanFactory);

				// Initialize message source for this context.
				initMessageSource();

				// Initialize event multicaster for this context.
				initApplicationEventMulticaster();

				// Initialize other special beans in specific context subclasses.
				onRefresh();

				// Check for listener beans and register them.
				registerListeners();

				// Instantiate all remaining (non-lazy-init) singletons.
				finishBeanFactoryInitialization(beanFactory);

				// Last step: publish corresponding event.
				finishRefresh();
			}

			catch (BeansException ex) {
				if (logger.isWarnEnabled()) {
					logger.warn("Exception encountered during context initialization - " +
							"cancelling refresh attempt: " + ex);
				}

				// Destroy already created singletons to avoid dangling resources.
				destroyBeans();

				// Reset 'active' flag.
				cancelRefresh(ex);

				// Propagate exception to caller.
				throw ex;
			}

			finally {
				// Reset common introspection caches in Spring's core, since we
				// might not ever need metadata for singleton beans anymore...
				resetCommonCaches();
			}
		}
	}
```

接下来我们对这个核心方法解析完毕，整个 ApplicationContext 的加载过程也就完毕了
#prepareRefresh()

```
	protected void prepareRefresh() {
        //启动时间
		this.startupDate = System.currentTimeMillis();
        //将当前上下文关闭状态设置为false
		this.closed.set(false);
        //将当前上下文活动状态设置为true
		this.active.set(true);
        //日志设置
		if (logger.isDebugEnabled()) {
			if (logger.isTraceEnabled()) {
				logger.trace("Refreshing " + this);
			}
			else {
				logger.debug("Refreshing " + getDisplayName());
			}
		}

		//从环境中加载所有参数，默认情况下不执行任何操作
		initPropertySources();

		//校验所有关键参数是否可以解析
		getEnvironment().validateRequiredProperties();

		//事件存储器
		this.earlyApplicationEvents = new LinkedHashSet<>();
	}
```

可以看到该方法实际上只是将参数进行了校验以及标识了当前上下文的状态

`ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();`
#obtainFreshBeanFactory()

```
	protected ConfigurableListableBeanFactory obtainFreshBeanFactory() {
        //刷新BeanFactory，此处方法由其子类AbstractRefreshableApplicationContext实现
		refreshBeanFactory();
        //获取BeanFactory并进行返回
		return getBeanFactory();
	}
```

#refreshBeanFactory

```
protected final void refreshBeanFactory() throws BeansException {
        //是否存在BeanFactory
		if (hasBeanFactory()) {
        //销毁所有的Bean
			destroyBeans();
        //关闭BeanFactory
			closeBeanFactory();
		}
		try {
            //创建一个新的BeanFactory
			DefaultListableBeanFactory beanFactory = createBeanFactory();
            //获取一个序列化id
			beanFactory.setSerializationId(getId());
            //自定义此上下文使用的内部BeanFactory
			customizeBeanFactory(beanFactory);
            //加载BeanDefinitions
			loadBeanDefinitions(beanFactory);
            //设置BeanFactory
			synchronized (this.beanFactoryMonitor) {
				this.beanFactory = beanFactory;
			}
		}
		catch (IOException ex) {
			throw new ApplicationContextException("I/O error parsing bean definition source for " + getDisplayName(), ex);
		}
	}
```

该方法的作用是刷新以及设置 BeanFactory，因为这里创建的 BeanFactory 还是一个空的，所以下面调用了`prepareBeanFactory`对工厂进行设置

```
protected void prepareBeanFactory(ConfigurableListableBeanFactory beanFactory) {
		//告诉内部BeanFactory使用context的类加载器，Bean的表达式解析器等等.
		beanFactory.setBeanClassLoader(getClassLoader());
		beanFactory.setBeanExpressionResolver(new StandardBeanExpressionResolver(beanFactory.getBeanClassLoader()));
		beanFactory.addPropertyEditorRegistrar(new ResourceEditorRegistrar(this, getEnvironment()));

		//使用context配置BeanFactory
		beanFactory.addBeanPostProcessor(new ApplicationContextAwareProcessor(this));
		beanFactory.ignoreDependencyInterface(EnvironmentAware.class);
		beanFactory.ignoreDependencyInterface(EmbeddedValueResolverAware.class);
		beanFactory.ignoreDependencyInterface(ResourceLoaderAware.class);
		beanFactory.ignoreDependencyInterface(ApplicationEventPublisherAware.class);
		beanFactory.ignoreDependencyInterface(MessageSourceAware.class);
		beanFactory.ignoreDependencyInterface(ApplicationContextAware.class);

		beanFactory.registerResolvableDependency(BeanFactory.class, beanFactory);
		beanFactory.registerResolvableDependency(ResourceLoader.class, this);
		beanFactory.registerResolvableDependency(ApplicationEventPublisher.class, this);
		beanFactory.registerResolvableDependency(ApplicationContext.class, this);

		beanFactory.addBeanPostProcessor(new ApplicationListenerDetector(this));

		if (beanFactory.containsBean(LOAD_TIME_WEAVER_BEAN_NAME)) {
			beanFactory.addBeanPostProcessor(new LoadTimeWeaverAwareProcessor(beanFactory));
			beanFactory.setTempClassLoader(new ContextTypeMatchClassLoader(beanFactory.getBeanClassLoader()));
		}

		if (!beanFactory.containsLocalBean(ENVIRONMENT_BEAN_NAME)) {
			beanFactory.registerSingleton(ENVIRONMENT_BEAN_NAME, getEnvironment());
		}
		if (!beanFactory.containsLocalBean(SYSTEM_PROPERTIES_BEAN_NAME)) {
			beanFactory.registerSingleton(SYSTEM_PROPERTIES_BEAN_NAME, getEnvironment().getSystemProperties());
		}
		if (!beanFactory.containsLocalBean(SYSTEM_ENVIRONMENT_BEAN_NAME)) {
			beanFactory.registerSingleton(SYSTEM_ENVIRONMENT_BEAN_NAME, getEnvironment().getSystemEnvironment());
		}
	}
```

该方法主要实现了对 BeanFactory 的配置

接下来在`try`中的所有方法基本都是对`BeanFactory`的其余处理这里就不详述了，在这里我们只需要将`try`中的最关键的方法`finishBeanFactoryInitialization()`再看一看

```
	protected void finishBeanFactoryInitialization(ConfigurableListableBeanFactory beanFactory) {
		// Initialize conversion service for this context.
		if (beanFactory.containsBean(CONVERSION_SERVICE_BEAN_NAME) &&
				beanFactory.isTypeMatch(CONVERSION_SERVICE_BEAN_NAME, ConversionService.class)) {
			beanFactory.setConversionService(
					beanFactory.getBean(CONVERSION_SERVICE_BEAN_NAME, ConversionService.class));
		}

		// Register a default embedded value resolver if no bean post-processor
		// (such as a PropertyPlaceholderConfigurer bean) registered any before:
		// at this point, primarily for resolution in annotation attribute values.
		if (!beanFactory.hasEmbeddedValueResolver()) {
			beanFactory.addEmbeddedValueResolver(strVal -> getEnvironment().resolvePlaceholders(strVal));
		}

		// Initialize LoadTimeWeaverAware beans early to allow for registering their transformers early.
		String[] weaverAwareNames = beanFactory.getBeanNamesForType(LoadTimeWeaverAware.class, false, false);
		for (String weaverAwareName : weaverAwareNames) {
			getBean(weaverAwareName);
		}

		// Stop using the temporary ClassLoader for type matching.
		beanFactory.setTempClassLoader(null);

		// Allow for caching all bean definition metadata, not expecting further changes.
		beanFactory.freezeConfiguration();

		// Instantiate all remaining (non-lazy-init) singletons.
		beanFactory.preInstantiateSingletons();
	}
```

该方法又巴拉巴拉一大堆，在这里我们只观察最后这个`preInstantiateSingletons`方法 ，该方法在`ConfigurableListableBeanFactory` 接口中被定义，在其子类中`DefaultListableBeanFactory`中重写

```
public void preInstantiateSingletons() throws BeansException {
		if (logger.isTraceEnabled()) {
			logger.trace("Pre-instantiating singletons in " + this);
		}

		// Iterate over a copy to allow for init methods which in turn register new bean definitions.
		// While this may not be part of the regular factory bootstrap, it does otherwise work fine.
		List<String> beanNames = new ArrayList<>(this.beanDefinitionNames);

		// Trigger initialization of all non-lazy singleton beans...
		for (String beanName : beanNames) {
			RootBeanDefinition bd = getMergedLocalBeanDefinition(beanName);
			if (!bd.isAbstract() && bd.isSingleton() && !bd.isLazyInit()) {
				if (isFactoryBean(beanName)) {
					Object bean = getBean(FACTORY_BEAN_PREFIX + beanName);
					if (bean instanceof FactoryBean) {
						final FactoryBean<?> factory = (FactoryBean<?>) bean;
						boolean isEagerInit;
						if (System.getSecurityManager() != null && factory instanceof SmartFactoryBean) {
							isEagerInit = AccessController.doPrivileged((PrivilegedAction<Boolean>)
											((SmartFactoryBean<?>) factory)::isEagerInit,
									getAccessControlContext());
						}
						else {
							isEagerInit = (factory instanceof SmartFactoryBean &&
									((SmartFactoryBean<?>) factory).isEagerInit());
						}
						if (isEagerInit) {
                            //核心部分
							getBean(beanName);
						}
					}
				}
				else {
					getBean(beanName);
				}
			}
		}

		// Trigger post-initialization callback for all applicable beans...
		for (String beanName : beanNames) {
			Object singletonInstance = getSingleton(beanName);
			if (singletonInstance instanceof SmartInitializingSingleton) {
				final SmartInitializingSingleton smartSingleton = (SmartInitializingSingleton) singletonInstance;
				if (System.getSecurityManager() != null) {
					AccessController.doPrivileged((PrivilegedAction<Object>) () -> {
						smartSingleton.afterSingletonsInstantiated();
						return null;
					}, getAccessControlContext());
				}
				else {
					smartSingleton.afterSingletonsInstantiated();
				}
			}
		}
	}
```

这个位置上就已经将我们的 bean 创建完毕了，核心是`getBean()`这个方法，该方法由`AbstractBeanFactory`重写，如何进行`Bean`的构建也是在这里面实现的，这里就不再次讲了。

关于`Spring`的核心加载部分到这里就结束了
