---
layout: post
title: "How To Use Spring RESTTemplate To Post Data to a Web Service"
description: "How To Use Spring RESTTemplate To Post Data to a Web Service"
categories: [ Spring, Java, JavaConfig, Programming, Spring Java Configuration, Spring MVC, Web, REST, RESTTemplate ]
tags: [ Spring, Java, JavaConfig, Programming, Spring Java Configuration, Spring MVC, Web, REST, RESTTemplate ]
comments: false
---

###  Using Spring RESTTemplate to Post Objects to RESTful web services with Spring's Java Configuration (JavaConfig) style with Maven, JUnit, Log4J


In this example I am going to show you how to post data to a RESTful web service in Java using Spring, Spring Java Configuration and more


### Web Service Code

Let's take a quick look at the Spring MVC Web Service code on the server:

    @Controller
    @RequestMapping("/api")
    class JSonController
    {

        private static final Logger logger = LoggerFactory.getLogger(JSonController.class);



        @RequestMapping(value = "/{id}", method = RequestMethod.POST)
        @ResponseBody
        public User updateCustomer(@PathVariable("id") String id, @RequestBody User user) {

            logger.debug("I am in the controller and got ID: " + id.toString());
            logger.debug("I am in the controller and got user name: " + user.toString());

            return new User("NEW123", "NEW SMITH");
        }


As you can see from the code above the web service is goign to what for a ID and user object to be passed in and then its going to create a new User Object and send it back to the client.

Lets take a quick look inside the User Object

    public class User
    {
        private String user;
        private String name;


        public User()
        {
        }

        public User(String user, String name)
        {
            this.user = user;
            this.name = name;
        }

        public String getUser()
        {
            return user;
        }

        public void setUser(String user)
        {
            this.user = user;
        }

        public String getName()
        {
            return name;
        }

        public void setName(String name)
        {
            this.name = name;
        }
    }

So you can see from the above code that the user object has to fields user and name.



### Time For The Client Code

You can see from the client code below is that we are using Spring RESTTemaple and going to post an User Object to a web server and get one back.


    @PropertySource("classpath:application.properties")
    public class Main
    {

        /**
         * Setting up logger
         */
        private static final Logger LOGGER = getLogger(Main.class);


        public static void main(String[] args) throws IOException
        {
            LOGGER.debug("Starting REST Client!!!!");

            /**
             *
             * This is going to setup the REST server configuration in the applicationContext
             * you can see that I am using the new Spring's Java Configuration style and not some OLD XML file
             *
             */
            ApplicationContext context = new AnnotationConfigApplicationContext(RESTConfiguration.class);

            /**
             *
             * We now get a RESTServer bean from the ApplicationContext which has all the data we need to
             * log into the REST service with.
             *
             */
            RESTServer mRESTServer = context.getBean(RESTServer.class);



            /**
             *
             * Setting up data to be sent to REST service
             *
             */
            Map<String, String> vars = new HashMap<String, String>();
            vars.put("id", "JS01");




            /**
             *
             * Doing the REST call and then displaying the data/user object
             *
             */


            try
            {

                /*

                    This is code to post and return a user object

                 */

                RestTemplate rt = new RestTemplate();
                rt.getMessageConverters().add(new MappingJacksonHttpMessageConverter());
                rt.getMessageConverters().add(new StringHttpMessageConverter());

                String uri = new String("http://" + mRESTServer.getHost() + ":8080/springmvc-resttemplate-test/api/{id}");

                User u = new User();
                u.setName("Johnathan M Smith");
                u.setUser("JS01");

                User returns = rt.postForObject(uri, u, User.class, vars);

                LOGGER.debug("User:  " + u.toString());

            }
            catch (HttpClientErrorException e)
            {
                /**
                 *
                 * If we get a HTTP Exception display the error message
                 */

                LOGGER.error("error:  " + e.getResponseBodyAsString());

                ObjectMapper mapper = new ObjectMapper();
                ErrorHolder eh = mapper.readValue(e.getResponseBodyAsString(), ErrorHolder.class);

                LOGGER.error("error:  " + eh.getErrorMessage());

            }
            catch(Exception e)
            {
                LOGGER.error("error:  " + e.getMessage());

            }
        }

    }


You can see from the above code how easy it is to use RESTTeample to post data to a web service.


### Download The Source

You can checkout the project from github.

    git clone git@github.com:JohnathanMarkSmith/springmvc-resttemplate-test.git
    cd springmvc-resttemplate-test.git


Here is the video:

<object width="640" height="480"><param name="movie" value="http://www.youtube.com/v/uSP-_aQjXBI?version=3&amp;hl=en_US"></param><param name="allowFullScreen" value="true"></param><param name="allowscriptaccess" value="always"></param><embed src="http://www.youtube.com/v/uSP-_aQjXBI?version=3&amp;hl=en_US" type="application/x-shockwave-flash" width="640" height="480" allowscriptaccess="always" allowfullscreen="true"></embed></object>


If you have any questions or comments please email me at <a href="mailto:john@johnathanmarksmith.com">john@johnathanmarksmith.com</a>

{% include JB/setup %}
