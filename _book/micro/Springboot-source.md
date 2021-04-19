第三节:springboot源码解析(王炸篇)

今天内容

1:spring注解 热身

2:springboot自动装配原理

3:springboot启动原理(jar包启动)

4:springboot启动原理(War包启动)

5:作业:springboot的自定义启动器



一:spring注解之如何导入Bean的几种方式

1）@Bean注解，不做讲解

2. 包扫描来加载Bean比如标识@Controller @Service  @Repository @Compent不做讲解
3. @Import几种取值来注册bean

①：实现ImportSelector接口的类

②：实现ImportBeanDefinitionRegistrar接口來注冊bean

4）实现factoryBean的方式来导入组件(不做讲解)



1.1)通过@Import注解来导入ImportSelector组件

①:写一个配置类在配置类上标注一个@Import的注解，



@Configuration

@Import(value = {TulingSelector.class})

public class TulingConfig {

}


②：在@Import注解的value值 写自己需要导入的组件

在selectImports方法中 就是你需要导入组件的全类名




public class TulingSelector implements ImportSelector {

@Override

public String[] selectImports(AnnotationMetadata annotationMetadata) { return new String[]{"com.tuling.service.TulingServiceImpl"};

}

}


核心代码:




@RestController

public class TulingController {



//自动注入tulingServiceImpl

@Autowired

private TulingServiceImpl tulingServiceImpl;

@RequestMapping("testTuling")

public String testTuling() {

tulingServiceImpl.testService();

return "tulingOk";

}

}

这里是没有标注其他注解提供给spring包扫描的

public class TulingServiceImpl {

public void testService() {

System.out.println("我是通过importSelector导入进来的service");

}

}

![图片](https://uploader.shimo.im/f/wQOH7aTVrHrDxVZz.jpeg!thumbnail?fileGuid=nVxuNijHlDkBFI64)































1.2）通过@Import导入ImportBeanDefinitionRegistrar从而进来导入组件

![图片](https://uploader.shimo.im/f/NZw5JoFNuyjb6OK5.jpeg!thumbnail?fileGuid=nVxuNijHlDkBFI64)












核心代码:



public class TulingImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar { @Override

public void registerBeanDefinitions(AnnotationMetadata annotationMetadata, BeanDefinitionRegistry beanDefinitionReg //定义一个BeanDefinition

RootBeanDefinition rootBeanDefinition = new RootBeanDefinition(TulingDao.class);



//把自定义的bean定义导入到容器中

beanDefinitionRegistry.registerBeanDefinition("tulingDao",rootBeanDefinition);

}

}

通过ImportSelector功能导入进来的

public class TulingServiceImpl {

@Autowired

private TulingDao tulingDao;

public void testService() {

tulingDao.testTulingDao();

System.out.println("我是通过importSelector导入进来的service");

}

}

通过ImportBeanDefinitionRegistar导入进来的

public class TulingDao {

public void testTulingDao() {

System.out.println("我是通过ImportBeanDefinitionRegistrar导入进来tulingDao组件");

}

}

测试结果:

![图片](https://uploader.shimo.im/f/tUbPXbVRwx3E6tVP.jpeg!thumbnail?fileGuid=nVxuNijHlDkBFI64)
























1.3)spring底层条件装配的原理@Conditional

应用要求:比如我有二个组件,一个是TulingLog一个是TulingAspect

而TulingLog是依赖TulingAspect的 只有容器中有TulingAspect组件才会加载TulingLog



tulingLog组件 依赖TulingAspect组件

public class TulingLog {

}

tulingAspect组件

public class TulingAspect {

}

①:自定义条件组件条件




public class TulingConditional implements Condition {

@Override

public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {

//容器中包含tulingAspect组件才返回Ture

if(conditionContext.getBeanFactory().containsBean("tulingAspect")){

return true;

}else{

return false;

}

}

}

-------------------------------------该情况下会加载二个组件--------------------------------------------

@Bean

public TulingAspect tulingAspect() {

System.out.println("TulingAspect组件自动装配到容器中");

return new TulingAspect();

}


@Bean

@Conditional(value = TulingConditional.class)

public TulingLog tulingLog() {

System.out.println("TulingLog组件自动装配到容器中");

return new TulingLog();

}

-------------------------------------二个组件都不会被加载----------------------------------------

/*@Bean**/

public TulingAspect tulingAspect() {

System.out.println("TulingAspect组件自动装配到容器中");

return new TulingAspect();

}


@Bean

@Conditional(value = TulingConditional.class)

public TulingLog tulingLog() {

System.out.println("TulingLog组件自动装配到容器中");

return new TulingLog();

}




==================================到此结束spring自层注解

==============================

二:springboot自动装配原理

2.1)@Springboot注解组合图

![图片](https://uploader.shimo.im/f/sDcyKL7zEiiSOcWn.jpeg!thumbnail?fileGuid=nVxuNijHlDkBFI64)












根据上面的@SpringBootApplication注解 我们来着重分析如下二个类

①：AutoConfigurationImportSelector.class

②：AutoConfigurationPackages.Registrar.class



先分析AutoConfigurationImportSelector为我们干了什么活 ？？




public String[] selectImports(AnnotationMetadata annotationMetadata) { if (!isEnabled(annotationMetadata)) {

return NO_IMPORTS;

}

AutoConfigurationMetadata autoConfigurationMetadata = AutoConfigurationMetadataLoader

.loadMetadata(this.beanClassLoader); AnnotationAttributes attributes = getAttributes(annotationMetadata); //获取候选的配置类

List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);

//移除重复的

configurations = removeDuplicates(configurations);

Set<String> exclusions = getExclusions(annotationMetadata, attributes); checkExcludedClasses(configurations, exclusions); configurations.removeAll(exclusions);

configurations = filter(configurations, autoConfigurationMetadata); fireAutoConfigurationImportEvents(configurations, exclusions); //返回出去

return StringUtils.toStringArray(configurations);

}

//获取候选的配置类

protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {

List<String> configurations = SpringFactoriesLoader.loadFactoryNames( getSpringFactoriesLoaderFactoryClass(), getBeanClassLoader());

Assert.notEmpty(configurations,

"No auto configuration classes found in META-INF/spring.factories. If you "

* "are using a custom packaging, make sure that file is correct.");

return configurations;

}

//加载配置类

public static List<String> loadFactoryNames(Class<?> factoryClass, @Nullable ClassLoader classLoader) { String factoryClassName = factoryClass.getName();

return loadSpringFactories(classLoader).getOrDefault(factoryClassName, Collections.emptyList());

}

private static Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) { MultiValueMap<String, String> result = cache.get(classLoader);

if (result != null) {

return result;

}

try {

//"META-INF/spring.factories"去类路径下该文件中加载EnableAutoConfiguration.class Enumeration<URL> urls = (classLoader != null ?

classLoader.getResources(FACTORIES_RESOURCE_LOCATION) :

ClassLoader.getSystemResources(FACTORIES_RESOURCE_LOCATION));

result = new LinkedMultiValueMap<>();

//遍历解析出来的集合

while (urls.hasMoreElements()) {

URL url = urls.nextElement();

UrlResource resource = new UrlResource(url);

//放在Properties中

Properties properties = PropertiesLoaderUtils.loadProperties(resource); for (Map.Entry<?, ?> entry : properties.entrySet()) {

String factoryClassName = ((String) entry.getKey()).trim();

for (String factoryName : StringUtils.commaDelimitedListToStringArray((String) entry.getValue())

result.add(factoryClassName, factoryName.trim());

}

}

}

cache.put(classLoader, result);

//返回

return result;

}

catch (IOException ex) {

throw new IllegalArgumentException("Unable to load factories from location [" + FACTORIES_RESOURCE_LOCATION + "]", ex);

}

}





主要是扫描spring-boot-autoconfigure\2.0.8.RELEASE\spring-boot-autoconfigure-

2.0.8.RELEASE.jar!\META-INF\spring.factories中EnableAutoConfiguration对应的全类名



org.springframework.boot.autoconfigure.EnableAutoConfiguration=\ org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\ org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\ org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\ org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\ org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\ org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\ org.springframework.boot.autoconfigure.cloud.CloudAutoConfiguration,\ org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\ org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\ org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\ org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\ org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration,\ org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration,\ org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveDataAutoConfiguration,\ org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveRepositoriesAutoConfiguration,\

org.springframework.boot.autoconfigure.data.cassandra.CassandraRepositoriesAutoConfiguration,\ org.springframework.boot.autoconfigure.data.couchbase.CouchbaseDataAutoConfiguration,\ org.springframework.boot.autoconfigure.data.couchbase.CouchbaseReactiveDataAutoConfiguration,\ org.springframework.boot.autoconfigure.data.couchbase.CouchbaseReactiveRepositoriesAutoConfiguration,\ org.springframework.boot.autoconfigure.data.couchbase.CouchbaseRepositoriesAutoConfiguration,\ org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchAutoConfiguration,\ org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchDataAutoConfiguration,\ org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchRepositoriesAutoConfiguration,\ org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration,\ org.springframework.boot.autoconfigure.data.ldap.LdapDataAutoConfiguration,\ org.springframework.boot.autoconfigure.data.ldap.LdapRepositoriesAutoConfiguration,\ org.springframework.boot.autoconfigure.data.mongo.MongoDataAutoConfiguration,\ org.springframework.boot.autoconfigure.data.mongo.MongoReactiveDataAutoConfiguration,\ org.springframework.boot.autoconfigure.data.mongo.MongoReactiveRepositoriesAutoConfiguration,\ org.springframework.boot.autoconfigure.data.mongo.MongoRepositoriesAutoConfiguration,\ org.springframework.boot.autoconfigure.data.neo4j.Neo4jDataAutoConfiguration,\ org.springframework.boot.autoconfigure.data.neo4j.Neo4jRepositoriesAutoConfiguration,\ org.springframework.boot.autoconfigure.data.solr.SolrRepositoriesAutoConfiguration,\ org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration,\ org.springframework.boot.autoconfigure.data.redis.RedisReactiveAutoConfiguration,\ org.springframework.boot.autoconfigure.data.redis.RedisRepositoriesAutoConfiguration,\ org.springframework.boot.autoconfigure.data.rest.RepositoryRestMvcAutoConfiguration,\ org.springframework.boot.autoconfigure.data.web.SpringDataWebAutoConfiguration,\ org.springframework.boot.autoconfigure.elasticsearch.jest.JestAutoConfiguration,\ org.springframework.boot.autoconfigure.flyway.FlywayAutoConfiguration,\ org.springframework.boot.autoconfigure.freemarker.FreeMarkerAutoConfiguration,\ org.springframework.boot.autoconfigure.gson.GsonAutoConfiguration,\ org.springframework.boot.autoconfigure.h2.H2ConsoleAutoConfiguration,\ org.springframework.boot.autoconfigure.hateoas.HypermediaAutoConfiguration,\ org.springframework.boot.autoconfigure.hazelcast.HazelcastAutoConfiguration,\ org.springframework.boot.autoconfigure.hazelcast.HazelcastJpaDependencyAutoConfiguration,\ org.springframework.boot.autoconfigure.http.HttpMessageConvertersAutoConfiguration,\ org.springframework.boot.autoconfigure.http.codec.CodecsAutoConfiguration,\ org.springframework.boot.autoconfigure.influx.InfluxDbAutoConfiguration,\ org.springframework.boot.autoconfigure.info.ProjectInfoAutoConfiguration,\ org.springframework.boot.autoconfigure.integration.IntegrationAutoConfiguration,\ org.springframework.boot.autoconfigure.jackson.JacksonAutoConfiguration,\ org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\ org.springframework.boot.autoconfigure.jdbc.JdbcTemplateAutoConfiguration,\ org.springframework.boot.autoconfigure.jdbc.JndiDataSourceAutoConfiguration,\ org.springframework.boot.autoconfigure.jdbc.XADataSourceAutoConfiguration,\ org.springframework.boot.autoconfigure.jdbc.DataSourceTransactionManagerAutoConfiguration,\ org.springframework.boot.autoconfigure.jms.JmsAutoConfiguration,\ org.springframework.boot.autoconfigure.jmx.JmxAutoConfiguration,\ org.springframework.boot.autoconfigure.jms.JndiConnectionFactoryAutoConfiguration,\ org.springframework.boot.autoconfigure.jms.activemq.ActiveMQAutoConfiguration,\ org.springframework.boot.autoconfigure.jms.artemis.ArtemisAutoConfiguration,\ org.springframework.boot.autoconfigure.groovy.template.GroovyTemplateAutoConfiguration,\ org.springframework.boot.autoconfigure.jersey.JerseyAutoConfiguration,\ org.springframework.boot.autoconfigure.jooq.JooqAutoConfiguration,\ org.springframework.boot.autoconfigure.jsonb.JsonbAutoConfiguration,\ org.springframework.boot.autoconfigure.kafka.KafkaAutoConfiguration,\ org.springframework.boot.autoconfigure.ldap.embedded.EmbeddedLdapAutoConfiguration,\ org.springframework.boot.autoconfigure.ldap.LdapAutoConfiguration,\ org.springframework.boot.autoconfigure.liquibase.LiquibaseAutoConfiguration,\ org.springframework.boot.autoconfigure.mail.MailSenderAutoConfiguration,\ org.springframework.boot.autoconfigure.mail.MailSenderValidatorAutoConfiguration,\ org.springframework.boot.autoconfigure.mongo.embedded.EmbeddedMongoAutoConfiguration,\ org.springframework.boot.autoconfigure.mongo.MongoAutoConfiguration,\ org.springframework.boot.autoconfigure.mongo.MongoReactiveAutoConfiguration,\ org.springframework.boot.autoconfigure.mustache.MustacheAutoConfiguration,\ org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration,\

org.springframework.boot.autoconfigure.quartz.QuartzAutoConfiguration,\ org.springframework.boot.autoconfigure.reactor.core.ReactorCoreAutoConfiguration,\ org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration,\ org.springframework.boot.autoconfigure.security.servlet.SecurityRequestMatcherProviderAutoConfiguration,\ org.springframework.boot.autoconfigure.security.servlet.UserDetailsServiceAutoConfiguration,\ org.springframework.boot.autoconfigure.security.servlet.SecurityFilterAutoConfiguration,\ org.springframework.boot.autoconfigure.security.reactive.ReactiveSecurityAutoConfiguration,\ org.springframework.boot.autoconfigure.security.reactive.ReactiveUserDetailsServiceAutoConfiguration,\ org.springframework.boot.autoconfigure.sendgrid.SendGridAutoConfiguration,\ org.springframework.boot.autoconfigure.session.SessionAutoConfiguration,\ org.springframework.boot.autoconfigure.security.oauth2.client.OAuth2ClientAutoConfiguration,\ org.springframework.boot.autoconfigure.solr.SolrAutoConfiguration,\ org.springframework.boot.autoconfigure.thymeleaf.ThymeleafAutoConfiguration,\ org.springframework.boot.autoconfigure.transaction.TransactionAutoConfiguration,\ org.springframework.boot.autoconfigure.transaction.jta.JtaAutoConfiguration,\ org.springframework.boot.autoconfigure.validation.ValidationAutoConfiguration,\ org.springframework.boot.autoconfigure.web.client.RestTemplateAutoConfiguration,\ org.springframework.boot.autoconfigure.web.embedded.EmbeddedWebServerFactoryCustomizerAutoConfiguration,\ org.springframework.boot.autoconfigure.web.reactive.HttpHandlerAutoConfiguration,\ org.springframework.boot.autoconfigure.web.reactive.ReactiveWebServerFactoryAutoConfiguration,\ org.springframework.boot.autoconfigure.web.reactive.WebFluxAutoConfiguration,\ org.springframework.boot.autoconfigure.web.reactive.error.ErrorWebFluxAutoConfiguration,\ org.springframework.boot.autoconfigure.web.reactive.function.client.WebClientAutoConfiguration,\ org.springframework.boot.autoconfigure.web.servlet.DispatcherServletAutoConfiguration,\ org.springframework.boot.autoconfigure.web.servlet.ServletWebServerFactoryAutoConfiguration,\ org.springframework.boot.autoconfigure.web.servlet.error.ErrorMvcAutoConfiguration,\ org.springframework.boot.autoconfigure.web.servlet.HttpEncodingAutoConfiguration,\ org.springframework.boot.autoconfigure.web.servlet.MultipartAutoConfiguration,\ org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration,\ org.springframework.boot.autoconfigure.websocket.reactive.WebSocketReactiveAutoConfiguration,\ org.springframework.boot.autoconfigure.websocket.servlet.WebSocketServletAutoConfiguration,\ org.springframework.boot.autoconfigure.websocket.servlet.WebSocketMessagingAutoConfiguration,\ org.springframework.boot.autoconfigure.webservices.WebServicesAutoConfiguration


上面的一些个XXXAutoConfiguration都是一个个自动配置类

我们就拿二个来分析一下 这些自动配置类是如何工作的???

分析源码1:  org.springframework.boot.autoconfigure.web.servlet.HttpEncodingAutoConfiguration



@Configuration//标识是一个自动配置类

@EnableConfigurationProperties(HttpEncodingProperties.class)启动指定类的配置功能，并且把配置文件中的属性和HttpEncodi @ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET) //spring底层的@Conditional注解的变@ConditionalOnClass(CharacterEncodingFilter.class) ,判断环境中是否没有这个类

判断配置文件中是否存在某个配置spring.http.encoding.enabled；如果不存在，判断也是成立的//即使我们配置文件中不配置pring.http.encoding.enabled=true，也是默认生效的@ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing = true) public class HttpEncodingAutoConfiguration {

自动配置类的属性映射

private final HttpEncodingProperties properties;

public HttpEncodingAutoConfiguration(HttpEncodingProperties properties) { this.properties = properties;

}

//配置一个CharacterEncodingFilter是springmvc解决乱码的，若容器中没有该组件，那么就会创建该组件

@Bean

@ConditionalOnMissingBean

public CharacterEncodingFilter characterEncodingFilter() { CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();

filter.setEncoding(this.properties.getCharset().name()); filter.setForceRequestEncoding(this.properties.shouldForce(Type.REQUEST)); filter.setForceResponseEncoding(this.properties.shouldForce(Type.RESPONSE)); return filter;



}

@Bean

public LocaleCharsetMappingsCustomizer localeCharsetMappingsCustomizer() { return new LocaleCharsetMappingsCustomizer(this.properties);

}

private static class LocaleCharsetMappingsCustomizer implements WebServerFactoryCustomizer<ConfigurableServletWebServerFactory>, Ordered {

private final HttpEncodingProperties properties;

LocaleCharsetMappingsCustomizer(HttpEncodingProperties properties) { this.properties = properties;

}

@Override

public void customize(ConfigurableServletWebServerFactory factory) { if (this.properties.getMapping() != null) {

factory.setLocaleCharsetMappings(this.properties.getMapping());

}

}

@Override

public int getOrder() {

return 0;

}

}

}



我们来看下HttpEncodingProperties，这个类是用来什么的？ 就是我们yml中能配置什么类，在这个类中都会有一个属性一一对应



@ConfigurationProperties(prefix = "spring.http.encoding") //从配置文件中获取指定的值和bean的属

性进行绑定

public class HttpEncodingProperties {

public static final Charset DEFAULT_CHARSET = Charset.forName("UTF‐8");

我们对应的配置文件(yml)中就会有对应属性来配置

![图片](https://uploader.shimo.im/f/AiYA7w4ipzBU5XxG.jpeg!thumbnail?fileGuid=nVxuNijHlDkBFI64)

![图片](https://uploader.shimo.im/f/aTTefTc9iSYF1J8f.jpeg!thumbnail?fileGuid=nVxuNijHlDkBFI64)

















以上 就是AutoConfigurationImportSelector为我们容器中注册了那些组件，然后根据maven依赖导入的jar包，根据条件装配来指定哪些组件

起作用 哪些组件不起作用。



三:上面我们分析了springboot自动装配原理，接下来我们依靠 自动装配原理来分析出spring Boot的jar包的启动流程.

![图片](https://uploader.shimo.im/f/N446hhEQq8gup4Mi.jpeg!thumbnail?fileGuid=nVxuNijHlDkBFI64)




















3.1)我们先来看springboot怎么来自动装配tomcat相关的组件

EmbeddedWebServerFactoryCustomizerAutoConfiguration(内嵌web容器工厂自定义定制器装配类)?

疑问1？：定制器是用来干什么的？


疑问2？:定制器何时工作？

类的继承关系

![图片](https://uploader.shimo.im/f/Kexc0xBVs4pILI1I.jpeg!thumbnail?fileGuid=nVxuNijHlDkBFI64)









我们就以tomcat作为内嵌容器来分析



@Configuration

@ConditionalOnWebApplication

@EnableConfigurationProperties(ServerProperties.class)

public class EmbeddedWebServerFactoryCustomizerAutoConfiguration {

//配置tomcat的

@Configuration

@ConditionalOnClass({ Tomcat.class, UpgradeProtocol.class })

public static class TomcatWebServerFactoryCustomizerConfiguration {

@Bean

public TomcatWebServerFactoryCustomizer tomcatWebServerFactoryCustomizer( Environment environment, ServerProperties serverProperties) {

return new TomcatWebServerFactoryCustomizer(environment, serverProperties);

}

}

//配置jetty

@Configuration

@ConditionalOnClass({ Server.class, Loader.class, WebAppContext.class })

public static class JettyWebServerFactoryCustomizerConfiguration {

@Bean

public JettyWebServerFactoryCustomizer jettyWebServerFactoryCustomizer( Environment environment, ServerProperties serverProperties) {

return new JettyWebServerFactoryCustomizer(environment, serverProperties);

}

}

配置undertow的

@Configuration

@ConditionalOnClass({ Undertow.class, SslClientAuthMode.class }) public static class UndertowWebServerFactoryCustomizerConfiguration {

@Bean

public UndertowWebServerFactoryCustomizer undertowWebServerFactoryCustomizer( Environment environment, ServerProperties serverProperties) {

return new UndertowWebServerFactoryCustomizer(environment, serverProperties);

}

}

}

我们来看下tomat工厂定制器 是用来修改设置容器的内容的(把serverProperties的属性设置到tomcat的创建工厂中)

public class TomcatWebServerFactoryCustomizer implements WebServerFactoryCustomizer<ConfigurableTomcatWebServ

..........................其他代码省略。。。。。。。。。。。。。。

@Override

public void customize(ConfigurableTomcatWebServerFactory factory) { ServerProperties properties = this.serverProperties; ServerProperties.Tomcat tomcatProperties = properties.getTomcat(); PropertyMapper propertyMapper = PropertyMapper.get(); propertyMapper.from(tomcatProperties::getBasedir).whenNonNull()

.to(factory::setBaseDirectory);

propertyMapper.from(tomcatProperties::getBackgroundProcessorDelay).whenNonNull()

.as(Duration::getSeconds).as(Long::intValue)

.to(factory::setBackgroundProcessorDelay);

customizeRemoteIpValve(factory);

propertyMapper.from(tomcatProperties::getMaxThreads).when(this::isPositive)

.to((maxThreads) -> customizeMaxThreads(factory,

tomcatProperties.getMaxThreads()));

propertyMapper.from(tomcatProperties::getMinSpareThreads).when(this::isPositive)

.to((minSpareThreads) -> customizeMinThreads(factory, minSpareThreads)); propertyMapper.from(() -> determineMaxHttpHeaderSize()).when(this::isPositive)

.to((maxHttpHeaderSize) -> customizeMaxHttpHeaderSize(factory, maxHttpHeaderSize));

propertyMapper.from(tomcatProperties::getMaxHttpPostSize)

.when((maxHttpPostSize) -> maxHttpPostSize != 0)

.to((maxHttpPostSize) -> customizeMaxHttpPostSize(factory,

maxHttpPostSize));

propertyMapper.from(tomcatProperties::getAccesslog)

.when(ServerProperties.Tomcat.Accesslog::isEnabled)

.to((enabled) -> customizeAccessLog(factory)); propertyMapper.from(tomcatProperties::getUriEncoding).whenNonNull()

.to(factory::setUriEncoding);

propertyMapper.from(properties::getConnectionTimeout).whenNonNull()

.to((connectionTimeout) -> customizeConnectionTimeout(factory, connectionTimeout));

propertyMapper.from(tomcatProperties::getMaxConnections).when(this::isPositive)

.to((maxConnections) -> customizeMaxConnections(factory, maxConnections)); propertyMapper.from(tomcatProperties::getAcceptCount).when(this::isPositive)

.to((acceptCount) -> customizeAcceptCount(factory, acceptCount));

customizeStaticResources(factory);

customizeErrorReportValve(properties.getError(), factory);

}




ServletWebServerFactoryAutoConfigurationServletweb工厂自动配置类

很重要*********************:@Import({

ServletWebServerFactoryAutoConfiguration.BeanPostProcessorsRegistrar.class}******************



@Configuration

@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE)

@ConditionalOnClass(ServletRequest.class)

@ConditionalOnWebApplication(type = Type.SERVLET)

@EnableConfigurationProperties(ServerProperties.class)

@Import({ ServletWebServerFactoryAutoConfiguration.BeanPostProcessorsRegistrar.class, ServletWebServerFactoryConfiguration.EmbeddedTomcat.class, ServletWebServerFactoryConfiguration.EmbeddedJetty.class, ServletWebServerFactoryConfiguration.EmbeddedUndertow.class })

public class ServletWebServerFactoryAutoConfiguration {

@Bean



public ServletWebServerFactoryCustomizer servletWebServerFactoryCustomizer( ServerProperties serverProperties) {

return new ServletWebServerFactoryCustomizer(serverProperties);

}

@Bean

@ConditionalOnClass(name = "org.apache.catalina.startup.Tomcat")

public TomcatServletWebServerFactoryCustomizer tomcatServletWebServerFactoryCustomizer( ServerProperties serverProperties) {

return new TomcatServletWebServerFactoryCustomizer(serverProperties);

}

}


.....................................................ServletWebServerFactoryCustomizer核心代码...........................................

public void customize(ConfigurableServletWebServerFactory factory) { PropertyMapper map = PropertyMapper.get().alwaysApplyingWhenNonNull(); map.from(this.serverProperties::getPort).to(factory::setPort); map.from(this.serverProperties::getAddress).to(factory::setAddress); map.from(this.serverProperties.getServlet()::getContextPath)

.to(factory::setContextPath);

map.from(this.serverProperties.getServlet()::getApplicationDisplayName)

.to(factory::setDisplayName);

map.from(this.serverProperties.getServlet()::getSession).to(factory::setSession);

map.from(this.serverProperties::getSsl).to(factory::setSsl);

map.from(this.serverProperties.getServlet()::getJsp).to(factory::setJsp);

map.from(this.serverProperties::getCompression).to(factory::setCompression);

map.from(this.serverProperties::getHttp2).to(factory::setHttp2);

map.from(this.serverProperties::getServerHeader).to(factory::setServerHeader);

map.from(this.serverProperties.getServlet()::getContextParameters)

.to(factory::setInitParameters);

}


---------------------------------------------TomcatServletWebServerFactoryCustomizer核心定制代码----------

public void customize(TomcatServletWebServerFactory factory) {

ServerProperties.Tomcat tomcatProperties = this.serverProperties.getTomcat();

if (!ObjectUtils.isEmpty(tomcatProperties.getAdditionalTldSkipPatterns())) {

factory.getTldSkipPatterns()

.addAll(tomcatProperties.getAdditionalTldSkipPatterns());

}

if (tomcatProperties.getRedirectContextRoot() != null) {

customizeRedirectContextRoot(factory,

tomcatProperties.getRedirectContextRoot());

}

if (tomcatProperties.getUseRelativeRedirects() != null) {

customizeUseRelativeRedirects(factory,

tomcatProperties.getUseRelativeRedirects());

}

}




ServletWebServerFactoryConfiguration容器工厂配置类



@Configuration

class ServletWebServerFactoryConfiguration {

@Configuration

@ConditionalOnClass({ Servlet.class, Tomcat.class, UpgradeProtocol.class }) @ConditionalOnMissingBean(value = ServletWebServerFactory.class, search = SearchStrategy.CURRENT) public static class EmbeddedTomcat {



//配置tomcat容器工厂

@Bean

public TomcatServletWebServerFactory tomcatServletWebServerFactory() { return new TomcatServletWebServerFactory();

}

}




现在我们来分析一下启动流程。。。。。。。。。。。。。。。。。。。。

1)com.tuling.TulingvipSpringbootAutoconfigPrincipleApplication#main运行main方法

2)org.springframework.boot.SpringApplication#run(java.lang.Class<?>, java.lang.String...)

2.1）传入主配置类，以及命令行参数

2.2)创建SpringApplication对象

①:保存主配置类

②：保存web应用的配置类型

③：去mate-info/spring.factories文件中获取ApplicationContextInitializer(容器初始

化器)保存到springapplication对象中

④：去mate-info/spring.factories文件中获取ApplicationListener(容器监听器)保存到springapplication对象中

⑤：保存选取 主配置类



public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) { this.resourceLoader = resourceLoader;

Assert.notNull(primarySources, "PrimarySources must not be null"); //保存主配置类

this.primarySources = new LinkedHashSet<>(Arrays.asList(primarySources)); //保存web应用的类型

this.webApplicationType = WebApplicationType.deduceFromClasspath(); //保存容器初始化器(ApplicationContextInitializer类型的) setInitializers((Collection) getSpringFactoriesInstances(

ApplicationContextInitializer.class));

//把监听器保存到SpringApplication中[ApplicationListener]

setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));

//保存主配置类

this.mainApplicationClass = deduceMainApplicationClass();

}

//还是去META-INFO/spring.factories中获取ApplicationContextInitializer类型，用于初始化容器private <T> Collection<T> getSpringFactoriesInstances(Class<T> type,

Class<?>[] parameterTypes, Object... args) {

ClassLoader classLoader = Thread.currentThread().getContextClassLoader();

* Use names and ensure unique to protect against duplicates Set<String> names = new LinkedHashSet<>(

SpringFactoriesLoader.loadFactoryNames(type, classLoader)); List<T> instances = createSpringFactoriesInstances(type, parameterTypes,

classLoader, args, names); AnnotationAwareOrderComparator.sort(instances); return instances;

}



//查找主配置类查询的依据就是看哪个方法是否有main方法

private Class<?> deduceMainApplicationClass() {

try {

StackTraceElement[] stackTrace = new RuntimeException().getStackTrace(); for (StackTraceElement stackTraceElement : stackTrace) {

if ("main".equals(stackTraceElement.getMethodName())) { return Class.forName(stackTraceElement.getClassName());

}

}

}

catch (ClassNotFoundException ex) {

// Swallow and continue

}

return null;

}

![图片](https://uploader.shimo.im/f/yCX2PcnUYIQYe7gf.png!thumbnail?fileGuid=nVxuNijHlDkBFI64)



































3)运行SpringbootApplication的run方法



public ConfigurableApplicationContext run(String... args) { StopWatch stopWatch = new StopWatch(); stopWatch.start();

//创建一个容器对象

ConfigurableApplicationContext context = null;

Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList<>();

configureHeadlessProperty();

//去meta-info/spring.factories中获取SpringApplicationRunListener监听器(事件发布监听器)

SpringApplicationRunListeners listeners = getRunListeners(args);

//发布容器starting事件(通过spring的事件多播器)

listeners.starting();

try {

//封装命令行参数

ApplicationArguments applicationArguments = new DefaultApplicationArguments(

args);



//准备容器环境

1:获取或者创建环境

2：把命令行参数设置到环境中

3：通过监听器发布环境准备事件

ConfigurableEnvironment environment = prepareEnvironment(listeners, applicationArguments);

configureIgnoreBeanInfo(environment);

//打印springboot的图标

Banner printedBanner = printBanner(environment);

//创建容器根据webApplicationType来创建容器通过反射创建context = createApplicationContext(); //去meta-info类中获取异常报告exceptionReporters = getSpringFactoriesInstances(

SpringBootExceptionReporter.class,

new Class[] { ConfigurableApplicationContext.class }, context);

//准备环境

1：把环境设置到容器中

2. 循环调用AppplicationInitnazlier进行容器初始化工作

3:发布容器上下文准备完成事件

4:注册关于springboot特性的相关单例Bean

5:发布容器上下文加载完毕事件

prepareContext(context, environment, listeners, applicationArguments,printedBanner); refreshContext(context);

//运行ApplicationRunner和CommandLineRunner afterRefresh(context, applicationArguments); stopWatch.stop();

if (this.logStartupInfo) {

new StartupInfoLogger(this.mainApplicationClass)

.logStarted(getApplicationLog(), stopWatch);

}

//发布容器启动事件listeners.started(context);

//运行ApplicationRunner和CommandLineRunner callRunners(context, applicationArguments);

}

catch (Throwable ex) {

//出现异常；调用异常分析保护类进行分析

handleRunFailure(context, ex, exceptionReporters, listeners); throw new IllegalStateException(ex);

}

try {

//发布容器运行事件

listeners.running(context);

}

catch (Throwable ex) {

handleRunFailure(context, ex, exceptionReporters, null);

throw new IllegalStateException(ex);

}

return context;

}




5)org.springframework.boot.SpringApplication#refreshContext

6)org.springframework.context.support.AbstractApplicationContext#refresh

7)org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext#onRefresh

8)org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext#createWebServer

8.1)org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext#getWebServer获取web服务器工厂

以下是springioc容器启动的核心流程，在这里不做详细解释,大概步骤为如下:

![图片](https://uploader.shimo.im/f/tO436NTDA1CBuiZD.jpeg!thumbnail?fileGuid=nVxuNijHlDkBFI64)

















..................................


8.2)org.springframework.boot.web.server.WebServerFactoryCustomizerBeanPostProcessor#postProces

![图片](https://uploader.shimo.im/f/8jnu4mgPRUMVHS0X.jpeg!thumbnail?fileGuid=nVxuNijHlDkBFI64)

















8.3)org.springframework.boot.web.server.WebServerFactoryCustomizerBeanPostProcessor#postProcessBeforeInitializati

![图片](https://uploader.shimo.im/f/8qOeGBcUwYlDEzsK.jpeg!thumbnail?fileGuid=nVxuNijHlDkBFI64)














8.3.1)WebServerFactoryCustomizerBeanPostProcessor是一个什么东西？ 在哪里注册到容器中的???

我们往容器中导入了BeanPostProcessorsRegistrar他实现了ImportBeanDefinitionRegistrar在他的registerBeanDefinitions注册Bean定义的时候 注册

* webServerFactoryCustomizerBeanPostProcessor

想知道webServerFactoryCustomizerBeanPostProcessor何时在容器中注册的么？？？？？



public static class BeanPostProcessorsRegistrar implements ImportBeanDefinitionRegistrar, BeanFactoryAware {

private ConfigurableListableBeanFactory beanFactory;

@Override

public void setBeanFactory(BeanFactory beanFactory) throws BeansException { if (beanFactory instanceof ConfigurableListableBeanFactory) {

this.beanFactory = (ConfigurableListableBeanFactory) beanFactory;

}

}

@Override

public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {

if (this.beanFactory == null) {

return;

}

registerSyntheticBeanIfMissing(registry,

"webServerFactoryCustomizerBeanPostProcessor",

WebServerFactoryCustomizerBeanPostProcessor.class);

registerSyntheticBeanIfMissing(registry,

"errorPageRegistrarBeanPostProcessor",

ErrorPageRegistrarBeanPostProcessor.class);

}

9. org.springframework.boot.web.embedded.tomcat.TomcatServletWebServerFactory#getWebServer

创建tomcat并且容器启动



public WebServer getWebServer(ServletContextInitializer... initializers) { Tomcat tomcat = new Tomcat();

File baseDir = (this.baseDirectory != null) ? this.baseDirectory

* createTempDir("tomcat");

tomcat.setBaseDir(baseDir.getAbsolutePath()); Connector connector = new Connector(this.protocol); tomcat.getService().addConnector(connector); customizeConnector(connector); tomcat.setConnector(connector); tomcat.getHost().setAutoDeploy(false); configureEngine(tomcat.getEngine());

for (Connector additionalConnector : this.additionalTomcatConnectors) { tomcat.getService().addConnector(additionalConnector);

}

prepareContext(tomcat.getHost(), initializers); return getTomcatWebServer(tomcat);

}

protected TomcatWebServer getTomcatWebServer(Tomcat tomcat) { //端口大于0启动启动

return new TomcatWebServer(tomcat, getPort() >= 0);

}

public TomcatWebServer(Tomcat tomcat, boolean autoStart) { Assert.notNull(tomcat, "Tomcat Server must not be null"); this.tomcat = tomcat;

this.autoStart = autoStart;

initialize();

}

tomcat启动流程

private void initialize() throws WebServerException {

TomcatWebServer.logger

.info("Tomcat initialized with port(s): " + getPortsDescription(false));

synchronized (this.monitor) {



try {

addInstanceIdToEngineName();

Context context = findContext();

context.addLifecycleListener((event) -> {

if (context.equals(event.getSource())

    * Lifecycle.START_EVENT.equals(event.getType())) {
* Remove service connectors so that protocol binding doesn't
* happen when the service is started. removeServiceConnectors();

}

});

* Start the server to trigger initialization listeners this.tomcat.start();
* We can re-throw failure exception directly in the main thread rethrowDeferredStartupExceptions();

try {

ContextBindings.bindClassLoader(context, context.getNamingToken(), getClass().getClassLoader());

}

catch (NamingException ex) {

// Naming is not enabled. Continue

}

* Unlike Jetty, all Tomcat threads are daemon threads. We create a
* blocking non-daemon to stop immediate shutdown startDaemonAwaitThread();

}

catch (Exception ex) {

stopSilently();

throw new WebServerException("Unable to start embedded Tomcat", ex);

}

}

}

10. 在IOC容器中的org.springframework.context.support.AbstractApplicationContext#refresh的

onReFresh（）带动tomcat启动

然后在接着执行ioc容器的其他步骤。

![图片](https://uploader.shimo.im/f/oYSDbLVYVDYEW7pr.jpeg!thumbnail?fileGuid=nVxuNijHlDkBFI64)






























疑问？？？？？

1）AutoConfigurationImportSelector#selectImports的方法是怎么触发

的？

![图片](https://uploader.shimo.im/f/BCjs4ZttLPxBI0GZ.jpeg!thumbnail?fileGuid=nVxuNijHlDkBFI64)



















2）我们自己定义的一些@Controller @Service怎么到容器中去的？？？

接下来 我们就一一解答你们的疑问?还是以debug的方式来为大家解答

1>AbstractApplicationContext#refresh(容器的刷新)

2>AbstractApplicationContext#invokeBeanFactoryPostProcessors调用bean工厂的后置处理器

3>PostProcessorRegistrationDelegate#invokeBeanDefinitionRegistryPostProcessors

4>ConfigurationClassPostProcessor#postProcessBeanDefinitionRegistry配置类的后置处理器5>ConfigurationClassPostProcessor#processConfigBeanDefinitions处理配置的bean定义

5.1）找到候选的配置类(tulingvipSpringbootAutoconfigPrincipleApplication)我们自己项目中的配

置类

5.2 )创建配置类解析器

6>ConfigurationClassParser#parse解析我们自己的配置类

(tulingvipSpringbootAutoconfigPrincipleApplication)

7>ConfigurationClassParser#processConfigurationClass处理配置类

7.1)处理配置类上的@PropertySource注解

ConfigurationClassParser#processPropertySource

7.2）处理@ComponentScan注解的ComponentScanAnnotationParser#parse

①:创建 类路径下的bean定义扫描器ClassPathBeanDefinitionScanner

..多个步骤 解析@ComponentScan注解的属性

②:ClassPathBeanDefinitionScanner#doScan真正的扫描

(tulingvipSpringbootAutoconfigPrincipleApplication所在的包)

③:返回我们标志了@Controller @Service @Response  @compent注解的bean定

义

7.3)处理@Import注解ConfigurationClassParser#processImports

7.4）处理@ImportSource注解

7.5)处理@Bean注解的

8>ConfigurationClassParser#processDeferredImportSelectors处理实现了

ImportSelectors接口的

9>AutoConfigurationGroup#process (获取 容器中的所有ImportSelector包含了AutoConfigurationImportSelector)

10>ImportSelector#selectImports回

* AutoConfigurationImportSelector.selectImports
    11. ConfigurationClassBeanDefinitionReader#loadBeanDefinitions把解析出来的类的bean定

义 注册到容器中

![图片](https://uploader.shimo.im/f/06u23qeA1kWEIGJd.jpeg!thumbnail?fileGuid=nVxuNijHlDkBFI64)

