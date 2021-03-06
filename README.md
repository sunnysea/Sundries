# Sundries
****
**SpringConfig.xml解析，当遇到自定义标签会从表头获取解析类，根据相关格式进行解析**

**BeanDefinitionParserDelegate**
![image](image/parseCustomElement.png)

```java
   public class NamespaceHandler extends NamespaceHandlerSupport {
	 @Override
	 public void init() {
		 // 注册解析器
		 registerBeanDefinitionParser(ElementNames.RAMCACHE, new CustomParser());
	 }
   }
```
**解析流程**
   ![image](image/parseElement.png)
   
```java
      CustomParser extends AbstractBeanDefinitionParser{
        @Override
        protected AbstractBeanDefinition parseInternal(Element element, ParserContext parserContext) {
        }
      }
```

**添加CustomInstantiationAwareBeanPostProcessorAdapter**
```java
class PostProcessorRegistrationDelegate {
   public static void registerBeanPostProcessors(
		ConfigurableListableBeanFactory beanFactory, AbstractApplicationContext applicationContext) {
		......
		String[] postProcessorNames = beanFactory.getBeanNamesForType(BeanPostProcessor.class, true, false);
		......
		List<String> nonOrderedPostProcessorNames = new ArrayList<String>();
		for (String ppName : postProcessorNames) {
			if (beanFactory.isTypeMatch(ppName, PriorityOrdered.class)) {
				BeanPostProcessor pp = beanFactory.getBean(ppName, BeanPostProcessor.class);
				priorityOrderedPostProcessors.add(pp);
				if (pp instanceof MergedBeanDefinitionPostProcessor) {
					internalPostProcessors.add(pp);
				}
			}
			else if (beanFactory.isTypeMatch(ppName, Ordered.class)) {
				orderedPostProcessorNames.add(ppName);
			}
			else {
				nonOrderedPostProcessorNames.add(ppName);
			}
		}
		.......
		// Now, register all regular BeanPostProcessors.
		List<BeanPostProcessor> nonOrderedPostProcessors = new ArrayList<BeanPostProcessor>();
		for (String ppName : nonOrderedPostProcessorNames) {
	             BeanPostProcessor pp = beanFactory.getBean(ppName, BeanPostProcessor.class);
		     nonOrderedPostProcessors.add(pp);
		     if (pp instanceof MergedBeanDefinitionPostProcessor) {
		         internalPostProcessors.add(pp);
		     }
		}
		......
	}
    }
```
**FactoryBean**
```java
  public interface FactoryBean<T> {
     //获得FactoryBean生产的对象
     T getObject() throws Exception;
     //返回对象的类型
     Class<?> getObjectType();
     boolean isSingleton();
  }
```
FactoryBean也是一个Bean,FactoryBean的存在就是为了解决Factory作为实例时的配置问题，让工厂模式在开发的时候配置起来更方便
