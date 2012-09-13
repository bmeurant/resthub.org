Resthub Spring Tutorial
=======================

Content for resthub-spring-stack tutorial.

This tutorial will help you to get an overview of resthub-spring-stack and its components and to take advantage of this framework and its tools.

Problem description
-------------------

During this tutorial we'll illustrate resthub usage with a sample and simple REST interface to manage tasks. Its components are:

.. image:: http://www.yuml.me/c57b8a61

Our REST interface will be mainly able to expose services to:

   - get all tasks
   - get all tasks & users paginated (with a page id and a page size)
   - get one task or user from its id
   - update one task or user from an updated object parameter
   - remove a task or user from its id
   
Each step of this tutorial is proposed as a git branch. You could checkout a step branch to get step instructions and solutions.
  
Step 1: Initialization
----------------------

**Prerequisites** :

   - Git installed : `<http://git-scm.com/downloads>`_
   - Maven installed : `<http://maven.apache.org/download.html>`_
   
**Solution** : you can find solution at `<http://resthub.org/training/spring/solution.html#step-1-initialization>`_

Find:
+++++

1. **Resthub2 getting started guide**

    see `<http://resthub.org/2/getting-started.html>`_

2. **Resthub2 documentation for Spring stack**

    see `<http://resthub.org/2/spring-stack.html>`_

3. **Resthub2 javadoc site**

    see `<http://jenkins.pullrequest.org/job/resthub-spring-stack-master/javadoc>`_
    
4. **List of Resthub2 underlying frameworks and corresponding documentation**

    - Maven: `complete reference <http://www.sonatype.com/books/mvnref-book/reference/public-book.html>`_
    - Spring 3.1: `reference manual <http://static.springsource.org/spring/docs/3.1.x/spring-framework-reference/html>`_ and `Javadoc <http://static.springsource.org/spring/docs/3.1.x/javadoc-api/>`_
    - Spring Data: `reference <http://www.springsource.org/spring-data>`_
        - Spring Data JPA: `reference <http://static.springsource.org/spring-data/data-jpa/docs/current/reference/html/>`_ and `Javadoc <http://static.springsource.org/spring-data/data-jpa/docs/current/api/>`_
        - Spring Data MongoDB: `reference <http://static.springsource.org/spring-data/data-mongodb/docs/current/reference/html/>`_ and `Javadoc <http://static.springsource.org/spring-data/data-mongodb/docs/current/api/>`_
    - Hibernate ORM and JPA : `reference <http://docs.jboss.org/hibernate/orm/4.1/manual/en-US/html_single/>`_ and `Javadoc <http://docs.jboss.org/hibernate/orm/4.1/javadocs/>`_
    - Spring MVC 3.1: `reference <http://static.springsource.org/spring-data/data-mongodb/docs/current/reference/html/>`_
    - Spring MVC Router: `reference <https://github.com/resthub/springmvc-router>`_
    - Jackson 2.0: `reference <http://wiki.fasterxml.com/JacksonDocumentation>`_ and `Javadoc <http://wiki.fasterxml.com/JacksonJavaDocs>`_
    - AsyncHttpClient: `reference <https://github.com/sonatype/async-http-client>`_ and `Javadoc <http://sonatype.github.com/async-http-client/apidocs/reference/packages.html>`_
    - SLF4J: `reference <http://www.slf4j.org/manual.html>`_
    - Logback: `reference <http://logback.qos.ch/manual/index.html>`_
    
Do:
+++

1. **Generate a Resthub2 template project structure**

   You can choose which template to use : pure Java Spring server template or Server + Client template if you plan to provide a RIA client
   for your app based on `Resthub Spring Stack`
   
   Choose groupId `org.resthub.training`, artifactId `jpa-webservice`, package `org.resthub.training` and version `1.0-SNAPSHOT`.
   
       As described in `Resthub documentation <http://resthub.org/2/getting-started.html>`_, create your local project by executing 
       ``mvn archetype:generate -DarchetypeCatalog=http://nexus.pullrequest.org/content/repositories/releases/`` in your `training` directory.

       - When **archetype** prompt, choose `1`: `org.resthub:resthub-jpa-webservice-archetype`. or 2 if you want also that a basic resthub-backbone-stack project
         will be generated. Enter
       - When **groupId** prompt, choose your `groupId`: `org.resthub.training`. Enter
       - When **artifactId** prompt, choose your `artifactId`: `jpa-webservice`. Enter
       - When **version** and **package** prompt, Enter.
       - Confirm by typing 'Y'. Enter

   You now have a `ready-to-code` sample resthub-spring project. Congrats !

2. **Run your project with mvn**

    Run ``mvn jetty:run`` from your `training/jpa-webservice` directory. Jetty should launch your application
    and says: 

    .. code-block:: script

       [INFO] Started Jetty Server

3. **Check on your browser that your project works that the response is an XML serialization of a Sample object with id 1**.

    Check `<http://localhost:8080/api/sample>`_

Let's take a look at the generated project. Its structure is:

.. code-block:: text

   |--- src
   |   |--- main
   |   |    | --- java
   |   |    |     | --- org
   |   |    |           | --- resthub
   |   |    |                 | --- training
   |   |    |                       | --- controller
   |   |    |                       |     | --- SampleController.java
   |   |    |                       | --- model
   |   |    |                       |     | --- Sample.java
   |   |    |                       | --- repository
   |   |    |                       |     | --- SampleRepository.java
   |   |    |                       | --- SampleInitializer.java
   |   |    |                       | --- WebAppInitializer.java
   |   |    | --- resources
   |   |          | --- applicationContext.xml
   |   |          | --- database.properties
   |   |          | --- logback.xml
   |   |--- test
   |        | --- java
   |              | --- org
   |                    | --- resthub
   |                          | --- training
   | --- pom.xml
   
``src/main/java`` contains all java sources under the package ``org.resthub.training`` as specified during archetype generation.
This package contains the following sub packages and files: 

- **controller**: This package contains all your application controllers, i.e. your web API. In the generated sample, the archetype provided
  you a SampleController that simply extend ``RepositoryBasedRestController`` and apply its behaviour to the *Sample* model and
  *SampleRepository*: ``SampleController extends RepositoryBasedRestController<Sample, Long, SampleRepository>``. This generic ``RepositoryBasedRestController``
  provides basic CRUD functionalities: see Resthub2 documentation for details.
- **model**: This package contains all you domain models.
- **repository**: This package contains your repositories, i.e. classes that provide methods to manipulate, persist and retrieve your objects from your JPA
  manager (and so your database). In the generated sample, the archetype provided you a SampleRepository that simply extend Spring-Data ``JpaRepository``.
  for behaviour, see Spring-Data JPA documentation for details.
- **initializers**: Initializers are special classes executed at application startup to setup your webapp. ``WebappInitializer`` load your spring application contexts,
  setup filters, etc. (all actions that you previously configured in your web.xml). The archetype provided you a ``SampleInitializer`` to setup sepcific domain model
  initializations such as data creation.
  
``src/main/resources`` contains all non java source files and, in particular, your spring application context, your database configuration file and you logging configuration.

``src/test/`` contains, obviously, all you test related files and has the same structure as src/main (i.e. *java* and *resources*).


Step 2: Customize Model
-----------------------

**Solution** : you can find solution at `<http://resthub.org/training/spring/solution.html#step-2-customize-model>`_

Let's start to customize the project generated by our archetype.

We are going to create Contoller, Repository and, obviously Model for our Task object. We'll also adapt our Initializer in order to provide
some sample data at application startup. 

Do:
+++

1. **Replace the generated `Sample` related objects with `Task`**

    - rename ``org.resthub.training.model.Sample`` class to ``org.resthub.training.model.Task``
    - replace ``name`` attribut by ``title``
    - add a ``description`` attribute and corresponding getter and setter

2. **Modify all others components considering this modification**

    - rename ``org.resthub.training.repository.SampleRepository`` class to ``org.resthub.training.repository.TaskRepository``
    - rename ``org.resthub.training.controller.SampleController`` class to ``org.resthub.training.controller.TaskController``
    - rename ``org.resthub.training.controller.SampleInitializer`` class to ``org.resthub.training.controller.TaskInitializer``
    - in ``TaskController`` and  ``TaskInitializer`` rename ``@RequestMapping`` & ``@Named`` annotation string values from sample to task  
    - check that all references to older Sample classes have been replaced

3. **Check that your new API works**

    re-run ``mvn jetty:run`` from your `training/jpa-webservice` directory. 

    Check on your browser that `<http://localhost:8080/api/task>`_ works and display XML representation for a sample object with id 1.

Answer:
+++++++

1. **How is wrapped the list of all existing tasks ?**
    
    A ``GET`` request on `<http://localhost:8080/api/task?page=all>`_ shows that the list of all existing tasks is **wrapped into a Pagination object** `PageImpl`.
    
2. **How to get a single task ?**
    
    A ``GET`` request on `<http://localhost:8080/api/task/1>`_ **returns a single Task** object with id 1, 
    
3. **How to update an existing task ? Update task 1 to add a description** ``new description``
    
    A ``PUT`` request on `<http://localhost:8080/api/task/1>`_ with ContentType ``application/json`` and body : 

    .. code-block:: javascript

       {
          "id": 1,
          "title": "testTask1",
          "description": "new description"
       }

4. **How to delete a task ?**       
  
    A ``DELETE`` request on `<http://localhost:8080/api/task/1>`_ **delete the Task** (check with a GET on `<http://localhost:8080/api/task>`_).
    
5. **How to create a task ?**  
    
    A ``POST`` request on `<http://localhost:8080/api/task>`_ with ContentType ``application/json`` and body: 

    .. code-block:: javascript

       {
          "title": "new test Task",
          "description": "new description"
       }

Step 3: Customize Controller
----------------------------

**Solution** : you can find solution at `<http://resthub.org/training/spring/solution#step-3-customize-controller>`_

We now have a basic REST interface uppon our Task model object providing default methods and behaviour implemented by resthub.

Let's suppose that the current findall : `<http://localhost:8080/api/task?page=all>`_ does not match our needs: the current implementation
returns a paginated list containing all elements in order to provide a consistent API between a *find all* and a *find paginated*.

In our case, we want a ``findAll`` implementation that returns a simple non paginated list of tasks: 

Do:
+++

1. **Modify** ``TaskController.java`` **to add a new method called** ``findAllNonPaginated``  **with no parameter mapped to** ``/api/task?page=no``.

   Implement this using existing repository method (see `Spring Data JPA documentation <http://static.springsource.org/spring-data/data-jpa/docs/current/api/>`_).
   Check on your browser that `<http://localhost:8080/api/task?page=no>`_ works and display a simple list of tasks, without pagination.

.. code-block:: javascript

   [{
       "id": 1,
       "name": "testTask1",
       "description": null
   }, {
       "id": 2,
       "name": "testTask2",
       "description": null
   }, {
       "id": 3,
       "name": "testTask3",
       "description": null
   }]


Implementation is done with using repository findAll method (see `<http://static.springsource.org/spring-data/data-jpa/docs/current/api/org/springframework/data/jpa/repository/JpaRepository.html#findAll()>`_).
    
    .. code-block:: Java
    
        @RequestMapping(method = RequestMethod.GET, params = "page=no")
        @ResponseBody
        public List<Task> findAllNonPaginated() {
            return this.repository.findAll();
        }


    **Note**: We cannot simply override ``/api/task?page=all`` method because mappings are currently defined in interface ``RestController`` 
    (see `documentation <http://jenkins.pullrequest.org/job/resthub-spring-stack-master/javadoc/org/resthub/web/controller/RestController.html>`_)
    and *Spring MVC* does not accept that a path appears twice.
    
    see `<https://github.com/resthub/resthub-spring-training/tree/step3-solution>`_ for complete solution.

Test your controller
++++++++++++++++++++

1. **Add dependency to use Resthub2 testing tools**

    .. code-block:: xml

       <dependency>
          <groupId>org.resthub</groupId>
          <artifactId>resthub-test</artifactId>
          <version>${resthub.spring.stack.version}</version>
          <scope>test</scope>
       </dependency>
   
2. In ``src/test/org/resthub/training``, add a ``controller`` directory and create a ``TaskControllerTest`` inside. 
   We first want to make an **integration test** of our controller. i.e. a test that need to run and embedded servlet container.
   **Implement a new** ``findAllNonPaginated`` **test method that creates some tasks and call controller.** 
   
   Verify that the new controller returns a response that is not empty, does not contain pagination and contains the created tasks.

    Our test ``TaskControllerTest`` should extend resthub ``AbstractWebTest`` 
    (see `documentation <http://jenkins.pullrequest.org/job/resthub-spring-stack-master/javadoc/org/resthub/test/common/AbstractWebTest.html>`_)
    
    .. code-block:: Java
    
        public class TaskControllerTest extends AbstractWebTest {
            protected String rootUrl() {
                return "http://localhost:8080/api/task";
            }


            @Test
            public void testCreateResource() throws IllegalArgumentException, InterruptedException, 
                                                    ExecutionException, IOException {
                Client httpClient = new Client();
                httpClient.url(rootUrl()).xmlPost(new Task("task1")).get();
                httpClient.url(rootUrl()).xmlPost(new Task("task2")).get();
                String responseBody = httpClient.url(rootUrl()).setQueryParameter("page", "no")
                        .getJson().get().getBody();
                Assertions.assertThat(responseBody).isNotEmpty();
                Assertions.assertThat(responseBody).doesNotContain("\"content\":2");
                Assertions.assertThat(responseBody).contains("task1");
                Assertions.assertThat(responseBody).contains("task2");
            }
        }
       
    see `<https://github.com/resthub/resthub-spring-training/tree/step3-solution>`_ for complete solution.

3. **Run test and check it passes**

   .. code-block:: script

        mvn -Dtest=TaskControllerTest#testCreateResource test
        
        -------------------------------------------------------
         T E S T S
        -------------------------------------------------------
        Running org.resthub.training.controller.TaskControllerTest
        
        ....
        
        Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 15.046 sec

        Results :

        Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

        [INFO] ------------------------------------------------------------------------
        [INFO] BUILD SUCCESS
        [INFO] ------------------------------------------------------------------------
        [INFO] Total time: 24.281s
        [INFO] Finished at: Thu Sep 13 14:27:44 CEST 2012
        [INFO] Final Memory: 13M/31M
        [INFO] ------------------------------------------------------------------------

Step 4: Users own tasks
-----------------------

**Prerequisites** : you can find some prerequisites and reference implementation of ``NotificationService`` and ``MockConfiguration`` at
`<http://github.com/resthub/resthub-spring-training/tree/step4-prerequisites>`_

**Solution** : you can find solution at `<http://resthub.org/training/spring/solution#step-4-users-own-tasks>`_

Find:
+++++

1. **Hibernate & JPA mapping documentation**

    see `reference <http://docs.jboss.org/hibernate/orm/4.1/manual/en-US/html_single/>`_ and `Javadoc <http://docs.jboss.org/hibernate/orm/4.1/javadocs/>`_
    
2. **Jackson annotations documentation**

    see `reference <http://wiki.fasterxml.com/JacksonAnnotations>`_
    
3. **Resthub2 Crud Services documentation**

    see `Crud Services <http://resthub.org/2/spring-stack.html#crud-services>`_ and 
    `Javadoc <http://jenkins.pullrequest.org/job/resthub-spring-stack-master/javadoc/org/resthub/common/service/CrudService.html>`_
    
4. **Resthub2 Different kind of controllers documentation**

    see `Web server<http://resthub.org/2/spring-stack.html#web-server>`_
    
5. **Spring assertions documentation**

    see `documentation <http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/util/Assert.html>`_
    
6. **Spring transactions documentation**

    see `documentation <http://static.springsource.org/spring/docs/3.1.x/spring-framework-reference/html/transaction.html#transaction-declarative-annotations>`_

Do:
+++

1. **Implement a new domain model** ``User`` **containing a name and an email and owning tasks:**
   User owns 0 or n tasks and Task is owned by 0 or 1 user
   
   Each domain object should contain relation to the other. Relations should be **mapped with JPA** in order to be saved and retrieved from database.
   Be caution with potential infinite JSON serialization
   
    .. code-block:: java
        
        // User
        @Entity
        public class User {

            private Long id;
            private String name;
            private String email;
            private List<Task> tasks;

            ...

            @Id
            @GeneratedValue
            public Long getId() {
                return id;
            }

            ...

            @JsonIgnore
            @OneToMany(mappedBy = "user")
            public List<Task> getTasks() {
                return tasks;
            }
            
            ...
        }
        
        // Task
        @Entity
        public class Task {

            private Long id;
            private String title;
            private String description;
            private User user;

            ...

            @Id
            @GeneratedValue
            public Long getId() {
                return id;
            }

            ...

            @ManyToOne
            public User getUser() {
                return user;
            }
            
            ...
        }
            
    see complete solution for `User <https://github.com/resthub/resthub-spring-training/blob/step4-solution/jpa-webservice/src/main/java/org/resthub/training/model/User.java>`_
    and `Task <https://github.com/resthub/resthub-spring-training/blob/step4-solution/jpa-webservice/src/main/java/org/resthub/training/model/Task.java>`_

2. **Provide dedicated Repository and Controller for user**

    .. code-block:: java
    
        // Repository
        public interface UserRepository extends JpaRepository<User, Long> {
            // that's all !
        }
        
        // Controller
        @Controller
        @RequestMapping(value = "/api/user")
        public class UserController extends RepositoryBasedRestController<User, Long, UserRepository> {

            @Inject
            @Named("userRepository")
            @Override
            public void setRepository(UserRepository repository) {
                this.repository = repository;
            }

            @Override
            public Long getIdFromResource(User resource) {
                return resource.getId();
            }
        }
        
    see complete solution for `controller <https://github.com/resthub/resthub-spring-training/blob/step4-solution/jpa-webservice/src/main/java/org/resthub/training/controller/UserController.java>`_
    and `repository <https://github.com/resthub/resthub-spring-training/blob/step4-solution/jpa-webservice/src/main/java/org/resthub/training/repository/UserRepository.java>`_

3. **Modify** ``TaskInitializer`` **in order to provide some sample users associated to tasks at startup**
   
    .. code-block:: java
   
        @Named("taskInitializer")
        public class TaskInitializer {

            @Inject
            @Named("taskRepository")
            private TaskRepository taskRepository;

            @Inject
            @Named("userRepository")
            private UserRepository userRepository;

            @PostInitialize
            @Transactional(readOnly = false)
            public void init() {
                User user1 = userRepository.save(new User("testUser1"));
                User user2 = userRepository.save(new User("testUser2"));
                taskRepository.save(new Task("testTask1", user1));
                taskRepository.save(new Task("testTask2", user1));
                taskRepository.save(new Task("testTask3", user2));
                taskRepository.save(new Task("testTask4"));
            }
        }
        
    see complete solution for `TaskInitializer <https://github.com/resthub/resthub-spring-training/blob/step4-solution/jpa-webservice/src/main/java/org/resthub/training/TaskInitializer.java>`_

   
4. **Check on your browser that User API** `<http://localhost:8080/api/user>`_ **works and provides simple CRUD and that** `<http://localhost:8080/api/task>`_ **still works**.

You can thus add domain models and provide for each one a simple CRUD API whithout doing nothing but defining empty repositories and controllers.
But if you have more than simple CRUD needs, resthub provides also a generic **Service layer** that could be extended to fit your business needs: 

5. **Create a new dedicated service (** ``TaskService``/``TaskServiceImpl`` **) for business user management** 
    - The new service should beneficiate of all CRUD Resthub services and work uppon TaskRepository.
    - Update your controller to manager this new 3 layers architecture
    
        .. code-block:: java
        
            // Interface
            public interface TaskService extends CrudService<Task, Long> {

            }
            
            // Implementation
            @Transactional
            @Named("taskService")
            public class TaskServiceImpl extends CrudServiceImpl<Task, Long, TaskRepository> implements TaskService {

                @Override
                @Inject
                public void setRepository(TaskRepository taskRepository) {
                    super.setRepository(taskRepository);
                }
            }
            
            // Controller
            @Controller
            @RequestMapping(value = "/api/task")
            public class TaskController extends ServiceBasedRestController<Task, Long, TaskService> {

                @Inject
                @Named("taskService")
                @Override
                public void setService(TaskService service) {
                    this.service = service;
                }

                @Override
                public Long getIdFromResource(Task resource) {
                    return resource.getId();
                }

                @RequestMapping(method = RequestMethod.GET, params = "page=no")
                @ResponseBody
                public List<Task> findAllNonPaginated() {
                    return this.service.findAll();
                }

            }
    
6. **Check that your REST interface is still working**

The idea is now to **add a method that affects a user to a task** based on user and task ids. During affectation, the user should be notified that a new task 
has been affected and, if exists, the old affected user should be notified that his affectation was removed. 
These business operations should be implemented in service layer: 

7. **Declare and implement method** ``affectTaskToUser`` **in (**``TaskService`` / ``TaskServiceImpl`` **)**
   
   Notification simulation should be performed by implementing a custom ``NotificationService`` that simply
   logs the event (you can also get the implementation from our repo in step4 solution). It is important to have an independant service (for mocking - see below - purposes)
   and you should not simply log in your new method. 
  
   **Signatures:**
    
   .. code-block:: java

      // NotificationService
      void send(String email, String message);
       
      // TaskService
      Task affectTask(Long taskId, Long userId);
  
   - In ``affectTask`` implementation, validate parameters to ensure that both userId and taskId are not null and correspond to existing objects
   - Tip : You will need to manipulate userRepository in TaskService ...
   - Tip 2 : You don't even have to call ``repository.save()`` due to Transactional behaviour of your service
   - Tip 3 : Maybe you should consider to implement ``equals()`` and ``hashCode()`` methods for User & Task   
   
    .. code-block:: java

        // TaskService
        public interface TaskService extends CrudService<Task, Long> {
            Task affectTaskToUser(Long taskId, Long userId);
        }
        
        // TaskServiceImpl
        @Transactional
        @Named("taskService")
        public class TaskServiceImpl extends CrudServiceImpl<Task, Long, TaskRepository> implements TaskService {


            private UserRepository userRepository;
            private NotificationService notificationService;

            @Override
            @Inject
            public void setRepository(TaskRepository taskRepository) {
                super.setRepository(taskRepository);
            }

            @Inject
            @Named("userRepository")
            public void setUserRepository(UserRepository userRepository) {
                this.userRepository = userRepository;
            }

            @Inject
            @Named("notificationService")
            public void setNotificationService(NotificationService notificationService) {
                this.notificationService = notificationService;
            }

            @Transactional(readOnly = false)
            @Override
            public Task affectTaskToUser(Long taskId, Long userId) {

                Assert.notNull(userId, "userId should not be null");
                Assert.notNull(taskId, "taskId should not be null");

                User user = this.userRepository.findOne(userId);
                Assert.notNull(user, "userId should correspond to a valid user");

                Task task = this.repository.findOne(taskId);
                Assert.notNull(task, "taskId should correspond to a valid task");

                if (task.getUser() != null && task.getUser() != user) {
                    if (task.getUser().getEmail() != null) {
                        this.notificationService.send(task.getUser().getEmail(), "The task " + task.getTitle() + " has been reaffected");
                    }
                }

                if (user.getEmail() != null) {
                    this.notificationService.send(user.getEmail(), "The task " + task.getTitle() + " has been affected to you");
                }

                task.setUser(user);

                return task;
            }
    
    see complete solution for `TaskService <https://github.com/resthub/resthub-spring-training/blob/step4-solution/jpa-webservice/src/main/java/org/resthub/training/service/TaskService.java>`_,
    `TaskServiceImpl <https://github.com/resthub/resthub-spring-training/blob/step4-solution/jpa-webservice/src/main/java/org/resthub/training/service/impl/TaskServiceImpl.java>`_,
    `TaskController <https://github.com/resthub/resthub-spring-training/blob/step4-solution/jpa-webservice/src/main/java/org/resthub/training/controller/TaskController.java>`_,
    `NotificationService <https://github.com/resthub/resthub-spring-training/blob/step4-solution/jpa-webservice/src/main/java/org/resthub/training/service/NotificationService.java>`_,
    `NotificationServiceImpl <https://github.com/resthub/resthub-spring-training/blob/step4-solution/jpa-webservice/src/main/java/org/resthub/training/service/impl/NotificationServiceImpl.java>`_
   
Test your new service
+++++++++++++++++++++

We will now write an integration test for our new service:

Find:
#####

1. **Resthub2 testing tooling documentation**

   see `<http://resthub.org/2/spring-stack.html#testing>`_

Do:
###  

1. **Create a new** ``TaskServiceIntegrationTest`` **integration test in** ``src/test/org/resthub/training/service/integration``
   This test should be **aware of spring context but non transactional** because testing a service should be done in a non transactional way. This is indeed the
   way in which the service will be called (e.g. by controller). The repository test should extend ``org.resthub.test.common.AbstractTransactionalTest`` to be run
   in a transactional context, as done by service.

    This test should perform an unique operation:

    - Create user and task and affect task to user.
    - Refresh the task by calling service.findById and check the retrived task contains the affected user
    
    .. code-block:: java
    
        public class TaskServiceIntegrationTest extends AbstractTest {

            @Inject
            @Named("taskService")
            private TaskService taskService;

            @Inject
            @Named("userRepository")
            private UserRepository userRepository;

            @Test
            public void testAffectTask() {
                User user = this.userRepository.save(new User("userName", "user.email@test.org"));
                Task task = this.taskService.create(new Task("taskName"));
                this.taskService.affectTaskToUser(task.getId(), user.getId());

                task = this.taskService.findById(task.getId());
                Assertions.assertThat(task.getUser()).isNotNull();
                Assertions.assertThat(task.getUser()).isEqualTo(user);

                User newUser = this.userRepository.save(new User("userName2", "user2.email@test.org"));

                this.taskService.affectTaskToUser(task.getId(), newUser.getId());

                task = this.taskService.findById(task.getId());
                Assertions.assertThat(task.getUser()).isNotNull();
                Assertions.assertThat(task.getUser()).isEqualTo(newUser);
            }
        }
    

2. **Run test and check it passes**

       .. code-block:: script

            mvn -Dtest=StandaloneEntityRepositoryTest#testFindByNameWithExplicitQuery test
            
            -------------------------------------------------------
             T E S T S
            -------------------------------------------------------
            Running org.resthub.training.service.integration.TaskServiceIntegrationTest
            
            ....
            
            Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

            Results :

            Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

            [INFO] ------------------------------------------------------------------------
            [INFO] BUILD SUCCESS
            [INFO] ------------------------------------------------------------------------
            [INFO] Total time: 6.951s
            [INFO] Finished at: Thu Sep 13 15:42:27 CEST 2012
            [INFO] Final Memory: 7M/17M
            [INFO] ------------------------------------------------------------------------

Mock notification service
+++++++++++++++++++++++++

If you didn't do anything else, you can see that we didn't manage notification service calls. In our case, this is not a real problem because
our implementation simply perform a log. But in a real sample, this will lead our unit tests to send a mail to a user (and thus will need for us to
be able to send a mail in tests, etc.). So **we need to mock**.

Find:
#####

1. **Mockito documentation**

   see `documentation <http://docs.mockito.googlecode.com/hg/latest/org/mockito/Mockito.html>`_

Do:
###  

1. **Add in** ``src/test/java/org/resthub/training`` **a new** ``MockConfiguration`` **class** 

.. code-block:: java

   @Configuration
   @ImportResource("classpath*:applicationContext.xml")
   @Profile("test")
   public class MocksConfiguration {
       @Bean(name = "notificationService")
       public NotificationService mockedNotificationService() {
           return mock(NotificationService.class);
       }
   }
   
This class allows to define a mocked alias bean to notificationService bean for test purposes. Its is scoped as **test profile** 
(see `documentation <http://blog.springsource.com/2011/02/14/spring-3-1-m1-introducing-profile/>`_).

2. **Modify your** ``TaskServiceIntegrationTest`` **to load our configuration**

.. code-block:: java

   @ContextConfiguration(loader = AnnotationConfigContextLoader.class, classes = MocksConfiguration.class)
   @ActiveProfiles("test")
   public class TaskServiceIntegrationTest extends AbstractTest {
      ...
   }
   
3. **Modify your test to check that** ``NotificationService.send()`` **method is called once when a user is affected to a task and twice if there was
   already a user affected to this task. Check the values of parameters passed to send method.**

       .. code-block:: java
       
          @ContextConfiguration(loader = AnnotationConfigContextLoader.class, classes = MocksConfiguration.class)
          @ActiveProfiles("test")
          public class TaskServiceIntegrationTest extends AbstractTest {
          
              @Inject
              @Named("taskService")
              private TaskService taskService;
          
              @Inject
              @Named("userRepository")
              private UserRepository userRepository;
          
              @Inject
              @Named("notificationService")
              private NotificationService mockedNotificationService;
          
          
              @Test
              public void testAffectTask() {
                  User user = this.userRepository.save(new User("userName", "user.email@test.org"));
                  Task task = this.taskService.create(new Task("taskName"));
                  this.taskService.affectTaskToUser(task.getId(), user.getId());
          
                  task = this.taskService.findById(task.getId());
                  Assertions.assertThat(task.getUser()).isNotNull();
                  Assertions.assertThat(task.getUser()).isEqualTo(user);
          
                  User newUser = this.userRepository.save(new User("userName2", "user2.email@test.org"));
          
                  this.taskService.affectTaskToUser(task.getId(), newUser.getId());
          
                  task = this.taskService.findById(task.getId());
                  Assertions.assertThat(task.getUser()).isNotNull();
                  Assertions.assertThat(task.getUser()).isEqualTo(newUser);
          
                  verify(mockedNotificationService, times(3)).send(anyString(), anyString());
                  verify(mockedNotificationService, times(1)).send("user.email@test.org", "The task " + task.getTitle() + " has been affected to you");
                  verify(mockedNotificationService, times(1)).send("user.email@test.org", "The task " + task.getTitle() + " has been reaffected");
                  verify(mockedNotificationService, times(1)).send("user2.email@test.org", "The task " + task.getTitle() + " has been affected to you");
              }
          }
          
       see complete solution for `TaskServiceIntegrationTest <https://github.com/resthub/resthub-spring-training/blob/step4-solution/jpa-webservice/src/test/java/org/resthub/training/service/integration/TaskServiceIntegrationTest.java>`_

  
This mock allows us to verify integration with others services and API whitout testing all these external tools.

This integration test is really usefull to validate your the complete chain i.e. service -> repository -> database (and, thus, your JPA mapping)
but, it is not necessary to write integration tests to test only your business and the logic of a given method.

It is really more performant and efficient to write *real unit tests* by using mocks.

Unit test with mocks
++++++++++++++++++++

Do:
###  

1. **Create a new** ``TaskServiceTest`` **class in** ``src/test/java/org/resthub/training/service``

   - Declare and mock ``userRepository``, ``taskRepository`` and ``notificationService``. Find a way to inject userRepository and notificationService in
     ``TaskServiceImpl``
   - Define that when call in ``userRepository.findOne()`` with parameter equal to 1L, the mock will return a valid user instance, null otherwise.
   - Define that when call in ``taskRepository.findOne()`` with parameter equal to 1L, the mock will return a valid task instance, null otherwise.
   - Provide these mocks to a new TaskServiceImpl instance (note that this test is a real unit test so we fon't use spring at all).
   - This should be done once for all tests in file.
   
    .. code-block:: java
    
        public class TaskServiceTest {

        private UserRepository userRepository = mock(UserRepository.class);
        private TaskRepository taskRepository = mock(TaskRepository.class);
        private NotificationService notificationService = mock(NotificationService.class);

        private TaskServiceImpl taskService;

        private User user;
        private Task task;

        @BeforeClass
        public void setup() {
            this.task = new Task("task1");
            this.task.setId(1L);
            this.user = new User("user1");
            this.user.setId(1L);

            when(this.userRepository.findOne(1L)).thenReturn(user);
            when(this.taskRepository.findOne(1L)).thenReturn(task);

            this.taskService = new TaskServiceImpl();
            this.taskService.setRepository(this.taskRepository);
            this.taskService.setUserRepository(this.userRepository);
            this.taskService.setNotificationService(this.notificationService);
        }

        @Inject
        @Named("notificationService")
        private NotificationService mockedNotificationService;
        
        ...

2. **Implement tests**
   
   - Check that the expected exception is thrown when userId or taskId are null   
   - Check that the expected exception is thrown when userId or taskId does not match any object.
   - Check that the returned task contains the affected user.
 
    .. code-block:: java   
    
        ...
        
        @Test(expectedExceptions = {IllegalArgumentException.class})
        public void testAffectTaskNullTaskId() {
            this.taskService.affectTaskToUser(null, this.user.getId());
        }

        @Test(expectedExceptions = {IllegalArgumentException.class})
        public void testAffectTaskNullUserId() {
            this.taskService.affectTaskToUser(this.task.getId(), null);
        }

        @Test(expectedExceptions = {IllegalArgumentException.class})
        public void testAffectUserInvalidTaskId() {
            this.taskService.affectTaskToUser(2L, this.user.getId());
        }

        @Test(expectedExceptions = {IllegalArgumentException.class})
        public void testAffectTaskInvalidUserId() {
            this.taskService.affectTaskToUser(this.task.getId(), 2L);
        }

        @Test
        public void testAffectTask() {
            Task returnedTask = this.taskService.affectTaskToUser(this.task.getId(), this.user.getId());

            Assertions.assertThat(returnedTask).isNotNull();
            Assertions.assertThat(returnedTask).isEqualTo(this.task);
            Assertions.assertThat(returnedTask.getUser()).isNotNull();
            Assertions.assertThat(returnedTask.getUser()).isEqualTo(this.user);
        }
        
    see complete solution for `TaskServiceTest <https://github.com/resthub/resthub-spring-training/blob/step4-solution/jpa-webservice/src/test/java/org/resthub/training/service/TaskServiceTest.java>`_,

    
Working mainly with unit tests (whithout launching spring context, etc.) is really more efficient to write and run and should be preffered to
systematic complete integration tests. Note that you still have to provide, at least, one integration test in order to verify mappings and complete
chain.
  
Create correponding method in controller to call this new service layer
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Do:
###  

- Implement a new method API in controller to affect a task to a user that call ``taskService.affectTaskToUser`` method. This API could be reached at ``/api/task/1/user/1`` on a 
  ``PUT`` request in order to affect user 1 to task 1.

    .. code-block:: java
    
        // TaskController
        @Controller
        @RequestMapping(value = "/api/task")
        public class TaskController extends ServiceBasedRestController<Task, Long, TaskService> {

            @Inject
            @Named("taskService")
            @Override
            public void setService(TaskService service) {
                this.service = service;
            }

            @Override
            public Long getIdFromResource(Task resource) {
                return resource.getId();
            }

            @RequestMapping(method = RequestMethod.GET, params = "page=no")
            @ResponseBody
            public List<Task> findAllNonPaginated() {
                return this.service.findAll();
            }

            @RequestMapping(value = "{taskId}/user/{userId}", method = RequestMethod.PUT)
            @ResponseBody
            public Task affectTaskToUser(@PathVariable Long taskId, @PathVariable Long userId) {
                return this.service.affectTaskToUser(taskId, userId);
            }
        }
        
    see complete solution for `TaskController <https://github.com/resthub/resthub-spring-training/blob/step4-solution/jpa-webservice/src/main/java/org/resthub/training/controller/TaskController.java>`_,


You can test in your browser (or, better, add a test in ``TaskControllerTest``) that the new API is operational.

    .. code-block:: java
    
         @Test
         public void testAffectTaskToUser() throws IllegalArgumentException, InterruptedException, ExecutionException, IOException {
             Client httpClient = new Client();
             Response resp = httpClient.url(rootUrl()).xmlPost(new Task("task1")).get();
             Task task = XmlHelper.deserialize(resp.getBody(), Task.class);
             resp = httpClient.url(userRootUrl()).xmlPost(new User("user1")).get();
             User user = XmlHelper.deserialize(resp.getBody(), User.class);
             String responseBody = httpClient.url(rootUrl() + "/" + task.getId() + "/user/" + user.getId()).put("").get().getBody();
             Assertions.assertThat(responseBody).isNotEmpty();
             Assertions.assertThat(responseBody).contains("task1");
             Assertions.assertThat(responseBody).contains("user1");
         }


Step 5: Validate your beans and embed entities
----------------------------------------------

**Solution** : you can find solution at `<http://resthub.org/training/spring/solution#step-5-validate-your-beans-and-embed-entities>`_

Finally, we want to add validation constraints to our model. This could be done by using BeanValidation (JSR303 Spec) and its reference
implementation: Hibernate Validator. see `documentation <http://docs.jboss.org/hibernate/validator/4.1/reference/en-US/html_single/>`_

Find:
+++++

1. **Bean Validation and Hibernate Validators documentation**

    see `reference <http://docs.jboss.org/hibernate/validator/4.0.1/reference/en/html_single/>`_

2. **JPA / Hibernate embedded entities documentation**

    see `reference <http://docs.jboss.org/hibernate/orm/4.1/manual/en-US/html_single/#mapping-declaration-component>`_
    
Do:
+++ 

1. **Modify User and Task to add validation**

    - User name and email are mandatory and not empty
    - User email should match regexp ``.+@.+\\.[a-z]+``
    - Task title is mandatory and not empty
    
        .. code-block:: java
        
            // User
            @Entity
            public class User {

                ...

                @NotNull
                @NotEmpty
                public String getName() {
                    return name;
                }

                public void setName(String name) {
                    this.name = name;
                }

                @NotNull
                @Pattern(regexp = ".+@.+\\.[a-z]+")
                public String getEmail() {
                    return email;
                }
                
                ...
            }
            
            // Task
            @Entity
            public class Task {

                ...

                @NotNull
                @NotEmpty
                public String getTitle() {
                    return title;
                }

                ...
            }

2. **If your integration tests (and initializer) fail. Make it pass**

    .. code-block:: java
    
        // TaskControllerTest
        public class TaskControllerTest extends AbstractWebTest {
            protected String rootUrl() {
                return "http://localhost:9797/api/task";
            }

            protected String userRootUrl() {
                return "http://localhost:9797/api/user";
            }

            @Test
            public void testCreateResource() throws IllegalArgumentException, InterruptedException, ExecutionException, IOException {
                Client httpClient = new Client();
                httpClient.url(rootUrl()).xmlPost(new Task("task1")).get();
                httpClient.url(rootUrl()).xmlPost(new Task("task2")).get();
                String responseBody = httpClient.url(rootUrl()).setQueryParameter("page", "no")
                        .getJson().get().getBody();
                Assertions.assertThat(responseBody).isNotEmpty();
                Assertions.assertThat(responseBody).doesNotContain("\"content\":2");
                Assertions.assertThat(responseBody).contains("task1");
                Assertions.assertThat(responseBody).contains("task2");
            }

            @Test
            public void testAffectTaskToUser() throws IllegalArgumentException, InterruptedException, ExecutionException, IOException {
                Client httpClient = new Client();
                Response resp = httpClient.url(rootUrl()).xmlPost(new Task("task1")).get();
                Task task = XmlHelper.deserialize(resp.getBody(), Task.class);
                resp = httpClient.url(userRootUrl()).xmlPost(new User("user1", "user1@test.org")).get();
                User user = XmlHelper.deserialize(resp.getBody(), User.class);
                String responseBody = httpClient.url(rootUrl() + "/" + task.getId() + "/user/" + user.getId()).put("").get().getBody();
                Assertions.assertThat(responseBody).isNotEmpty();
                Assertions.assertThat(responseBody).contains("task1");
                Assertions.assertThat(responseBody).contains("user1");
            }
        }
    
        // TaskInitializer
        @Named("taskInitializer")
        public class TaskInitializer {

            @Inject
            @Named("taskRepository")
            private TaskRepository taskRepository;

            @Inject
            @Named("userRepository")
            private UserRepository userRepository;

            @PostInitialize
            @Transactional(readOnly = false)
            public void init() {
                User user1 = new User("testUser1", "user1@test.org");
                user1 = userRepository.save(user1);
                User user2 = userRepository.save(new User("testUser2", "user2@test.org"));
                taskRepository.save(new Task("testTask1", user1));
                taskRepository.save(new Task("testTask2", user1));
                taskRepository.save(new Task("testTask3", user2));
                taskRepository.save(new Task("testTask4"));
            }
        }

3. **Add embedded address to users : Modify User model to add an embedded entity address to store user address (city, country)**

    .. code-block:: java
    
        // User
        @Entity
        public class User {

            ...
            
            private Address address;

            ...

            @Embedded
            public Address getAddress() {
                return address;
            }

            public void setAddress(Address address) {
                this.address = address;
            }
            
            ...
        }
        
        // Address
        @Embeddable
        public class Address implements Serializable {
            private String city;
            private String country;

            public Address() {
            }

            public Address(String city, String country) {
                this.city = city;
                this.country = country;
            }

            @NotNull
            @NotEmpty
            public String getCity() {
                return city;
            }

            public void setCity(String city) {
                this.city = city;
            }

            @NotNull
            @NotEmpty
            public String getCountry() {
                return country;
            }

            public void setCountry(String country) {
                this.country = country;
            }
        }

4. **Add a** ``UserRepositoryIntegrationTest`` **class in** ``src/test/java/org/resthub/training/repository/integration`` **and implement
   a test that try to create a user with an embedded address**. 
   
   Check that you can then call a findOne of this user and that the return object contains address object.
   
    .. code-block:: java
    
        @ActiveProfiles("test")
        public class UserRepositoryIntegrationTest extends AbstractTest {

            @Inject
            @Named("userRepository")
            private UserRepository repository;

            @Test
            public void testCreateValidAddress() {
                User user = new User("userName", "user.email@test.org");
                Address address = new Address();
                address.setCity("city1");
                address.setCountry("country1");
                user.setAddress(address);

                user = this.repository.save(user);
                Assertions.assertThat(user).isNotNull();
                Assertions.assertThat(user.getId()).isNotNull();
                Assertions.assertThat(user.getAddress()).isNotNull();
                Assertions.assertThat(user.getAddress().getCity()).isEqualTo("city1");
            }
        }
  
5. **Add nested validation for embedded address. city and country should not be null and non empty**

    .. code-block:: java

        // User
        @Entity
        public class User {

            ...
            
            private Address address;

            ...

            @Valid
            @Embedded
            public Address getAddress() {
                return address;
            }

            public void setAddress(Address address) {
                this.address = address;
            }
            
            ...
        }
        
        // Address
        @Embeddable
        public class Address implements Serializable {
            
            ...

            @NotNull
            @NotEmpty
            public String getCity() {
                return city;
            }

            ...

            @NotNull
            @NotEmpty
            public String getCountry() {
                return country;
            }

            ...
        }

   see complete solution for `User <https://github.com/resthub/resthub-spring-training/blob/step5-solution/jpa-webservice/src/main/java/org/resthub/training/model/User.java>`_,
   `Address <https://github.com/resthub/resthub-spring-training/blob/step5-solution/jpa-webservice/src/main/java/org/resthub/training/model/Address.java>`_,
   and `Task <https://github.com/resthub/resthub-spring-training/blob/step5-solution/jpa-webservice/src/main/java/org/resthub/training/model/Task.java>`_

6. **Modify** ``UserRepositoryIntegrationTest`` **to test that a user can be created with a null address but exception is thrown when 
   address is incomplete (e.g. country is null or empty)**
   
       .. code-block:: java
    
        @ActiveProfiles("test")
        public class UserRepositoryIntegrationTest extends AbstractTest {

            @Inject
            @Named("userRepository")
            private UserRepository repository;

            @Test
            public void testCreateNullAddress() {
                User user = new User("userName", "user.email@test.org");

                user = this.repository.save(user);

                user = this.repository.findOne(user.getId());
                Assertions.assertThat(user).isNotNull();
                Assertions.assertThat(user.getId()).isNotNull();
                Assertions.assertThat(user.getAddress()).isNull();
            }

            @Test(expectedExceptions = {TransactionSystemException.class})
            public void testCreateInvalidAddress() {
                User user = new User("userName", "user.email@test.org");
                Address address = new Address();
                address.setCity("city1");
                user.setAddress(address);

                this.repository.save(user);
            }

            ...
        }
