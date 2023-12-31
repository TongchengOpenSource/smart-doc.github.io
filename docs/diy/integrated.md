# UI integration

## `swagger UI` integration

smart-doc supports the generation of openapi 3.0+ standard interface documents, so you can use a document management system or ui interface that supports openapi 3.0+ to display the documents generated by smart-doc.
This article talks about how to quickly integrate swagger ui to test your interface in development.

### Add dependency

```
<!--swagger ui -->
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-ui</artifactId>
    <version>1.5.0</version>
</dependency>
```

The openapi specification version supported by smart-doc is relatively high, so it needs to integrate 1.5.0 or higher.
### Configure swagger ui
Add the following configuration in the application configuration file
```
# custom path for swagger-ui
springdoc:
  swagger-ui:
    path: /swagger-ui-custom.html
    operations-sorter: method
   #custom path for api docs
    url: /doc/openapi.json
```

- The url is the key to configuration, and it points to the openapi.json file generated by smart-doc. And you need to generate openapi under `src/main/resources/static/doc`.

After generating the api file, start your application and visit `http://localhost:8080/swagger-ui-custom.html` to see the document.
Then you can use this ui interface to self-test during development.

**Note:** Just study the other configurations of swagger ui by yourself, and we won't.


## `smart-doc` debug page

Starting from smart-doc version 2.0.0, in the allInOne mode of html. You can add the configuration of `"createDebugPage": true`. smart-doc will create a debug.html page.
When the smart-doc is generated, it is directly placed under `static/doc/`, so that you can directly start the program and visit the page `localhost:8080/doc/debug.html` for development and debugging.
Starting from smart-doc 2.0.1, for html documents, both allInOne and non-allInOne modes can generate debug pages. The current debug page of smart-doc supports file upload and file download testing.

### Configuration

```json
{
  "serverUrl": "http://localhost:8080",
  "isStrict": false,
  "allInOne": true,
  "outPath": "src/main/resources/static/doc",
  "coverOld": true,
  "style":"xt256",//Like json highlighting can be set
  "createDebugPage": true, //Enable build debug
  "md5EncryptedHtmlName": false,
  "projectName": "SpringBoot2-Open-Api"
}
```
### Cross-domain configuration
Some developers directly use [Open In Browser] in the idea to open the debug page generated by smart-doc. If you have to do this, cross-domain occurs when the front-end js requests the back-end interface. Therefore, you need to configure cross-domain in the backend.
Here is an example of SpringBoot:

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    /**
     * Cross-domain configuration will override the default configuration,
     * Therefore, it is necessary to implement the addResourceHandlers method to increase the default configuration static path
     * @param registry
     */
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/**")
                .addResourceLocations("classpath:/resources/")
                .addResourceLocations("classpath:/static/");
    }
    
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOrigins("*")
                .allowedMethods("*")
                .allowCredentials(true);
    }
}
```
> If the server is used to access the page, no configuration is required.

### Interface effect

![](https://gitee.com/smart-doc-team/smart-doc/raw/master/screen/mock.png "1.png")
### debug page debugging
When using the html debugging page generated by smart-doc for interface debugging, you may encounter a problem. Usually when you click the `Send Request` button and the button turns red, it means that there is an interface error or an error in the debug page. At this time, please open the debug console of the browser to view the problem or debug. The pages created by smart-doc are only developed with jquery and native js. `debug.js` is used to process interface test requests, and `search.js` is used to process document directory title searches. The source code is not compressed, you can directly debug the js source code of the page. Refer to the following figure for debugging operation:

![](https://gitee.com/smart-doc-team/smart-doc/raw/master/screen/debug-console.png "2.png")


## torna integration


### Introduction
After 3 years of continuous open source development of smart-doc, we have received a lot of user needs, and many enterprise users really need a convenient centralized API management platform. In the past, Yapi can be said to be an open source product with a relatively large number of long-term users in the domestic open source market. But in the author's observation, Yapi has many problems. Therefore, in 2020, I have been looking for suitable open source partners in the community to focus on building an enterprise-level API document management platform. Fortunately, I found @tanghc in the open source community. @tanghc is an author of multiple open source projects and is very keen on open source. I described to @tanghc my personal project and philosophy of API management platform. In the end, we reached a consensus to do Torna. Provide a good document generation and management solution for the open source community. Of course we will explore commercial products in the future. But the maven and gradle plugins of smart-doc and smart-doc are free. The basic functions of Torna currently provided are also free and open source for community use. The commercial version of torna is mainly an advanced function version for enterprises.

### Full document automation
smart-doc + [Torna](http://torna.cn) composes an industry-leading document generation and management solution, using smart-doc to complete Java source code analysis and extract annotations without intrusion to generate API documents, and automatically push the documents to Torna enterprise-level interface document management platform.

![smart-doc+torna](../../_images/smart-doc-en.png)

> You need to start from smart-doc 2.0.9 to support pushing documents to Torna. Of course, it is recommended to use smart-doc to pay attention to the release of the new version. Recommend smart-doc to use 2.1.7 and above, torna 1.6.2 and above
### How to custom push documents to Torna
The first is to integrate smart-doc in the java spring project. For smart-doc integration, see other official smart-doc documents. In fact, smart-doc's philosophy has always been to make it easier to use. Therefore, it is very simple to push the document to smart-doc, just add a few lines in the smart-doc.json file to push to the configuration of torna

```json
{
   "serverUrl": "http://127.0.0.1", //Server address, not required. It is recommended to set the export postman to http://{{server}} to facilitate the setting of environment variables directly in the postman
   "isStrict": false, //Whether to enable strict mode
   "packageFilters": "",//controller package filtering, multiple packages separated by English commas
   "projectName": "smart-doc",//Configure your own project name
   "appKey": "20201216788835306945118208", // torna platform docking appKey,, @since 2.0.9
   "appToken": "c16931fa6590483fb7a4e85340fcbfef", //torna platform appToken,@since 2.0.9
   "secret": "W.ZyGMOB9Q0UqujVxnfi@.I#V&tUUYZR",//torna platform secret, @since 2.0.9
   "openUrl": "http://localhost:7700/api",//torna platform address, fill in your privatization deployment address @since 2.0.9
   "debugEnvName":"Test environment", //torna test environment
   "debugEnvUrl":"http://127.0.0.1",//torna
}
```
 **Note:**  `appKey`, `appToken`, `secret` If you are not an administrator, you need to ask the administrator about the specific information about the project you pushed.

If you are an administrator, you can view it in Torna's space management.

View space
![输入图片说明](../../_images/224047_69a6870d_144669.png "屏幕截图.png")
View the appKey, secret and other information of the space
![输入图片说明](../../_images/224207_830d5b06_144669.png "屏幕截图.png")
View the tokens of related items in the space
![输入图片说明](../../_images/0313/224356_2bc8c3b7_144669.png "屏幕截图.png")

### Push operation
After integrating smart-doc and completing the push configuration, you can use smart-doc's maven or gradle plug-in to directly push the document to Torna.
![输入图片说明](../../_images/224947_853e59e3_144669.png "屏幕截图.png")
> If you want to use the command line or gradle, please check the documentation used by the smart-doc official maven and gradle plugins, which will not be repeated here.




