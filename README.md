# Please read this before review
Here is the list of **possible inconsistencies** between your code-guide and this project
 * Used autoconfig + autowire because of test project, but ambigulos dependencies are passed correctly. In production terms I am also preferred to use container over magic.
 * Some private data (Bin) can be logged
 * Some configs can be not correctly structured because of app size 
 * There can be bugs with xdebug 3 + php 8 + PhpStorm so please use raw testing using vendor/bin/phpunit, see https://youtrack.jetbrains.com/issue/WI-56947
 * Use makefile "make" to see commands for interaction, run it locally

I don't get the meaning of next

```text

For services, we use some suffix to represent the job of that service, usually *er:

manager

...

resolver

```

So I`ve renamed everything to have prefix manager instead of service. Not standard approach...

<hr>

**Spent time on this task:**

 1) Dealing with Docker, xdebug 3, finding correct image - 3h
 2) Dealing with Xdebug 3 error + configuring it locally in phpstorm - 2h
 3) Refactoring old code to new structure - 4h
 4) Adding generator + pagination - 2h
 5) Reading the codestyle + performing format changes 2h 30m
 6) Test coverage - 2h
 7) Readme + makefile etc - 1h


**Next things to improve:**
 * Add validation for input params
 * Add some currency enum for testing correct values
 * Add application layer when it is needed =)
 * Add more accurate error handling on BL cases
 * Split reader related code in different module
 * Return some data structures from API instead of arrays

**Additional things that added to task:**
 * This project was build with latest php 8 and Symfony versions
 * File reader uses generators to process big files with pagination
 * All DTO's are immutable
 * CommissionManager configured to process transactions by batches
 * Binlist configured to use async requests to process transactions by batches
 * Exchange rates response is cached by 1h
 * Output values of commissions will be rounded with precision 2 (app.yaml)
 * Scale of math operations will be 5 (app.yaml)


# Answers for pre-interview questions

### Code quality

Q: How can you assess the quality of code using static analysis? What metrics would you use?

A: There is a lot of tools to configure and check that code quality is acceptable, like codesniffer (php-cs) mess detector (phpmd), and new form me in this year - psalm. These can be installed as a composer lib and integrated into PhpStorm as plugins.

### Coding

Q: You have a final class. Is there any difference to call new static() or new self() in one of its
methods?

A: So the final keyword means that these classes can not be extended by child classes, so there should not be any difference in practical way.

### SOLID

Q: What is wrong with this class? (image)

A: From SOLID side that class has too many responsibilities - creating and keeping the connection to db, querying the DB and creating some objects (like DataMapper?) so this should be split to:
 * DatabaseConnection, that should be created by a factory and injected via interface to MySQLClient constructor.
 * QueryBuilder that can create correct query strings with correct structure
 * EntityHydrator - that can create objects and fill object data from DB
 * ...

Q: Why it was done like this?

A: The answer is abstract, but this services may require different data from user - payroll can require an employee id but report can require an employee id and department_id, but i`ve prefer to use "MethodRequest" pattern, to have unique DOT||VO as parameter to service facade calls, and a factory, that can build those objects using user entity or whatever...

Q: What is the name of the following pattern?

A: Pattern name is "Composite". 

Q: Could you describe and explain the type of connections between elements?

A: "Car" class private attribute "engine" should be injected via "Engine" interface, that inherited both by Diesel and Hybrid classes

Q: Could you describe the open-closed principle with this pattern?

A: Because of "engine" property is injected via interface the new type of engine (like steam-powered) can be added to program without changing the "Car" class code.

Q: What would you prefer to use Dependency Injection or Service Locator for object dependency
management?

A: Dependency injection, service locator is anti-pattern that simplifies life a bit, but breaks the layering of application.

### REST

Q: We have an e-commerce application which allows us to buy and sell items. Which URL and HTTP method would you like to use to buy and sell one specific item?

A: If we want to omit the "cart" functionality and buy/sell items directly is ok thing for business, then  POST {{domain}}/item/{{item_id}}/buy and POST {{domain}}/item/{{item_id}}/sell

Q: When can we use PUT for resource creation?

A: When we need to "update" existing one

### Security

Q: How to pass variables from GET query string to SQL expression?

A: First - validate it by business rules and data types, then - sanitization of parameters logically, and the last step - do not use parameters in the query without "preparation" - https://www.php.net/manual/en/pdo.prepare.php 

### Data Sources

Q: What is the case to use a Repository pattern or DataMapper pattern? Is there any sense to have a Repository in PHP?

A: Very abstract question. PHP is not bad\good for any pattern, so if your app require splitting storage-persistent layer from domain level logic - it can be implemented. Implemented in Symfony as default one approach.


Q: What is your opinion about Active record pattern?

A: It`s a good solution for small apps or new developers - easy to use and understand, less code and fast rapid-development, but when you have to build project, that should be extendable over the years with big team of developers - it's better to have things separated. Also, AR is bad for UNIT testing, and don't look SOLID enough, so I think that is more like "compromise" solution, but not the worst.

### Software architecture

Q: What kind of architectural pattern do you usually use on your web-based projects?

A: I have experience with different ones, starting from small monolith projects with backend rendering and MVC, SOA with a monolith using Onion + DDD and some parts separated to services, to Microservice cloud with a general bus of envents, gateways and layering

### DDD


Q: What is the main idea of Domain Driven Design?

A: Keep things in code separated by domains and layers of interraction, that group functionalities by the same use cases to be able to split application. The main idea for me - keep the interaction between layers and domains documented with interfaces.

### Soft skills

Q: How do you understand the acceptance criteria?

A: This is general requirements for tasks\app results, and, also, a preparation about how code should be tested.


Q: After code review, you received 20 comments regarding your code. You disagree with
half of that, what would you do?

A: I am the person, who respects the experience of other developers, so I like to listen of the "Approved reviewers" mind, and follow the team\tech -lead decisions, and even after that if minds are separated and in some moments we didn't came to agreement - then lets decide what is the best solution on team-meeting, but to follow the deadlines I would make code as guys suggested, adn then, if some things left - it can be a refactoring task in the backlog.


Q: Could you please provide us the best example of a project, which is designed and
implemented by yourself? (It should be on a public git repository)

A: Here is a link for test task, that was recently done - https://github.com/vakafonic/test_task_comission . Task can be found in this repo
