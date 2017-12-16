# Large Systems Development Report

Top-Up: Software Development

Fall, Semester 2

December 16, 2017

**Group I**

By: William Bech, Adam Lewandowski, Lukasz Koziarski, Teodor Mihail Costica

**Introduction**

The goal of this project was to create a functional clone of a social news website called Hacker News, which is a news aggregator focusing on computer science and entrepreneurship.

The project was supposed to contain all of the core functionalities of Hacker News; displaying stories, creating comments and using karma points. The project was also supposed to simulate how it is to handle a large system in real time as well as build one.

This was done by implementing server logs, containerizing our system, monitoring system metrics and having all of our services deployed. In order to do this we also attempted to use known processes which help the development process. Every aspect of our system from architecture and design has been done in a particular way due to the value it gave us for further maintenance and scalability.

**1. Requirements, architecture, design and process**

**1.1. System requirements**

The requirements of our project are split up into two parts, the website itself and the System as a whole. In this section the functional and nonfunctional requirement of the website and System are discussed.

**Website**

In the website users should be able to view stories and interact with stories by creating them, commenting on them and modifying the &quot;point&quot; system by giving or reducing a &quot;point&quot;. The website is centered around stories posted by users. This system is a replica of &quot;Hacker News&quot; by Y Combinator. Users who are not registered are simply able to read stories and register themselves meanwhile registered users are able to upvote/downvote a story as well as create stories, view other user's past interactions on the website as well as see their own. A registered user is also able to comment on existing stories

**Functional Requirements**

- Display a set of stories and comments on the front page
- Stories and comments are posted by users, which have to be registered to and logged into the system to be able to post
- Users login to the system via a separate page
- Users are identified by a user name and a password
- New users can register to the system, via a separate page.
- Users can upvote stories and/or comments
- Design and functionality should mimic the original Hacker News
- The system should have a Karma point system
- Only registered users can upvote or downvote posts
- Only users with 500 Karma points or more can downvote posts

**Non Functional requirements** :

Legal

- Webapp has to mention the original Hackernews
- Webapp has to mention that this is a school project and it's not meant to be some kind of competition for Hackernews

Usability

- The website has to work smoothly, without any uncaught or critical errors.
- The website wait times should be short
- Error messages should help a user recover from an error
- Actions that can't be undone need to have user confirmation

Implementation

- Registered users should not be able to access some other users account and post under their name

**System**

**Functional requirements** :

- Be able to fetch the most recent stories that have been persisted in the database
- Store user data (password, username)
- Store stories and comments and be able to relate them to who created them
- Authenticate users by verifying matching credentials
- Be able to modify the data of an already created user
- Have a Karma scale per story and comment

**Non Functional requirements** :

Usability

- There should be available and complete user documentation

Reliability

- The system has to have an uptime of 95% or more.
- The system should always be able to process data

Performance

- The system has to be robust and fast
- The website should be dynamic
- Must be able to scale
- Timely delivery of processed data

Supportability

- The system's basic functionalities should be tested
- The backend and frontend should not be heavily coupled

Implementation

- The system must utilize a VCS system
- Each component of the system can be upgraded separately
- The two major components of our System (frontend and backend) need to have continuous integration.

Interface

- System requires a REST API
- The REST API has to allow external program to query its state

Packaging

- Project has to be shipped with whole data that is necessary (database scripts etc.)
- Project has to utilize virtualization in order to be software independent (docker)

**1.2. Development process**

Team responsibility

We formed 2 teams, one responsible for the frontend of the application, the other one responsible for the server and database. Each team would be self-organising choosing the most appropriate technologies to work with.

Cross functionality of roles appeared naturally in our team.

Communication and work strategy

The internet was the place where we'd usually meet and discuss about the project. This enabled us to work independently from home at any given time without time or environment constraints. Sometimes we would also meet in person and have pair programming sessions.

Branching strategy

Our team contributed to the same Git repository which was hosted on Github. Every new feature, bug fix and refactoring of code would have its own branch and after a pull request, it would be thoroughly reviewed by at least one other member group to check the quality of the change. After that, it would be successfully merged into the master branch.

Decision theory

While building the LSD Hacker News project, we were required to make various decisions regarding to all development phases. Throughout that process, we went through all 3 decision making models:

- Economic man (known problem, known solution): the knowledge for building a server, with an associated front end component was there so we knew what to do and how to do it from the beginning
- Administrative man (known problem, unknown solution): experience with the Neo4j database was present and we were able to make qualified decisions when unexpected problems arised
- Muddling through (unknown problem, unknown solution) : we had little knowledge about logging and monitoring but we had the capabilities to evaluate the right decisions to configure and use them.

- Concepts meaning methodology, scrum, how we worked
- Branching strategy, pair programming
- Basically read a bit about methodologies and pick what aspects from them we used (willing or unwillingly)

**1.3. Software architecture**

The front-end of our project is structured with having our React JS website hosted on an AWS S3 bucket. We have then set-up a Content Delivery Network (CDN) service between our S3 bucket and the end user. This service is called CloudFront which is also hosted by AWS and is part of the AWS ecosystem. The way that the front-end works is that an end user will try to access our website through the CloudFront URL, CloudFront will then return what is hosted in the S3 bucket to the end user.

The React Js project itself is following the Flux architecture. This is a unidirectional data flow architecture which goes from the View to the Action Creator to the Action Dispatchers to the Stores. The View is what the end users interact with, the Action Creators/Dispatchers are where user and server actions are filtered and where data is being dispatched to the Stores. The Stores are where the app state is kept and modified. The diagram below helps illustrate the Flux Architecture.

![](https://github.com/teutzue/LSD-Report/blob/master/Picture1.png) 

*Flux Architecture (http://fluxxor.com/images/flux-simple.png)

The way that the front-end and the back-end communicate is through the REST API that the server exposes. The website hosted on the S3 bucket sends requests to the server hosted in the DigitalOcean Droplet. The server then queries the Neo4J database which returns the appropriate results. The hosted front-end (S3) then receives the results from the server and then sends it to CloudFront which then returns the results to the end-user.

Server is implemented with Java 8 and Spring framework. Server is hosted in its own droplet on DigitalOcean. It is running on embedded Apache Tomcat server within Spring framework. Functionality of server is exposed through REST API and it is sending messages over HTTP protocol. Server can access the database through Neo4j implementation of Java Database Connectivity (JDBC) API.

We have chosen Neo4j graph database. It has direct connection to server through a [bolt](https://boltprotocol.org/)protocol that is specifically designed to support rapid and lightweight communication with database. Database design is very simple. We have two types of nodes: Users and Posts. Database is hosted in its own droplet on DigitalOcean.

**1.4. Software design**

Continuous integration on the server side has been done using Jenkins, Github, Docker, Virtualbox and DigitalOcean. Every team member worked on their local machine, collaborating and contributing to a central repository hosted on Github. The jenkins build server lives inside a VirtualBox image which runs linux (we wanted to minimize the costs of hosting).

DigitalOcean hosts our remote server which has a docker container running an artifactory instance which serves the local maven dependencies. It also hosts the server which will be automatically deployed in a docker container.

Basically, Jenkins pulls the code from Github, fetches all dependencies from Maven which are later registered in Artifactory, runs the build and test processes and pushes the docker image generated to Docker hub which is later deployed on the digital ocean server.


 ![](https://github.com/teutzue/LSD-Report/blob/master/Picture2.png) 

*Diagram of how our back-end CI chain works, the base of the arrow shows which service initiates contact

The front end component of our web application uses a different Continuous Integration tool - Travis. It was simpler to setup in comparison to Jenkins since Travis is able to automatically pull npm dependencies. The flow starts on the local machine from which code is pushed to Github; Building and testing the code is done in Travis and after, the deployables are pushed to AWS (Amazon web services) into an S3 bucket.

 ![](https://github.com/teutzue/LSD-Report/blob/master/Picture3.png) 

For this project we have chosen Neo4j graph database. This choice was made based on detailed analyze of Hackernews discussion sections. Hackernews allows to comment not only posts but also comments of other people. This results in a lot of related data that forms a directed acyclic graph. Neo4j seemed like an obvious choice since it has native support for all kinds of graph structures. Native support for graph structure means robustness and lightning fast performance compared to other databases that do not provide such support.

Before we started the project we had a big concern that Neo4j can be sometimes problematic to work with since it's a relatively young product. With this issue in mind we have chosen JDBC implementation of Neo4j as a primary way of interaction with database. It is way more verbose and a little bit harder to work with than Object Graph Mapper (OGM). The rationale behind JDBC is that it certainly empowers the developer by providing him way more control over flow over program. This control is priceless when Neo4j is causing unexpected problems.

For server we have chosen java and spring. Java was chosen because Neo4j is written in Java so it has native support for that language. Moreover some of us have already strong experience in this programming language. Java is also one of most used programming languages so there is almost unlimited amount of resources to work with. We had to implement REST API and we have chosen Spring for that matter. It is also one of most robust and popular frameworks for java. Beside that, Spring syntax is very terse so it eliminates a lot of boilerplate code and helps to combat verbosity of Java language without sacrifices in performance.

For the front-end we had planned on creating a React JS application due to Reacts quick development processes. Our concern with this though was the slow initial setup of React projects. We planned on using React as well as we knew it was easy to integrate logic with the view as opposed to a &quot;pure&quot; website. This would help us process data more in the front end rather than handling everything in the backend in which we would have very little processing power. The reason for us choosing React over another framework such as Angular was due to our familiarity with it.

**1.5. Software implementation**

Spring framework was indeed a good choice. It allowed us to quickly implement REST API operations and as a result allocate more man power on other tasks. Server structure can be decomposed into two parts - rest controller and database mapper. Communication between Rest controller and database mapper is based on contract defined by java interface. This approach reduces coupling and improves cohesion. As a result it allows to swap implementations of database mapper on demand. For example we could switch database to MySql or MongoDB with only one line of code.  Rest controller lists all possible API operations and it's handling the requests and responses. Database mapper is responsible for database calls and mapping between Neo4j nodes and Java Objects.

As expected Neo4j was very problematic due to unexpected problems with queries on aggregated data. Fortunately due to the choice of JDBC as a primary way of interaction with database, we were able to do extensive optimization of queries which resulted in major performance improvements.

Most of functional requirements are reflected in system as cypher queries. For example:

- Authenticate users by verifying matching credentials

![](https://github.com/teutzue/LSD-Report/blob/master/Picture4.png) 

- Be able to fetch the most recent stories that have been persisted in the database 

![](https://github.com/teutzue/LSD-Report/blob/master/Picture5.png) 

Unfortunately we didn't meet the requirement that only people with 500 or more karma points can downvote. This was due to the fact that we didn't have time to implement this feature. We are aware that it could be solved through a query that checks how many karma points does user have and returning false when he/she doesn't have enough points. Unfortunately this would require few small changes in database design and we didn't have time for that.

When developing the front-end of the project everything went according to plan except we did not anticipate for the need of CloudFront. The reason for which we needed CloudFront was for when an end user would try to access an endpoint which was not registered in the API the user would be redirected to a 404 error landing page. This was something that we did not want users to encounter, especially as we were redirecting using the front-end rather than the server. This meant that when a user would refresh the page on any URL other than the base url the user would then be redirected to the 404 landing page as all routes were stored in the front-end rather than the server. All CloudFront does is redirect any 404 error to the base URL and then allows the front-end to take care of the rest of the routing.

**2. Maintenance and SLA status**

**2.1. Hand-over**

Throughout the development process of the Hacker News clone, our team also operated a project of another group - the Group H. Hand-over of the operated system happened on the same day we all had a deadline for the &quot;SLA + monitoring&quot; assignment which was on the 8th of November. On that day we have received a completed service-level agreement (SLA) with metrics we have agreed upon with the group that owns the project.

In the SLA Group H described the service they were meant to provide and claimed the reliable performance level. SLA has one page, however contained all necessary information and declarations that would be needed for a successful monitoring.

Together with SLA we have received access to the monitoring tool called Grafana. It is a metric analytics and visualization suite, it was used as an graphical interface for a data collected by another tool called Prometheus, which is a monitoring and alerting toolkit.

We have not received an access to the Prometheus itself.

Unfortunately regarding documentation we have not received anything from the Group H in that matter. They have not created any relevant wiki on GitHub that would describe the project parts in details or how to use it.

However, on our own we have found some texts regarding their project, but in two separate repositories. There was a very short description about the backend and even poorer description of a frontend. When asked about documentation, the group representative said they do not have any.

**2.2. Service-level agreement**

In their service-level agreement Group H well and compactly stated the commitments about their system. The operated group has come up with only one metric that was relevant for measuring the performance of their system. That metric was the threshold of the overall uptime for the system. Both our sides have agreed on 95% of the uptime.

In the SLA Group H also stated that they were aiming for their server to be able to digest stories and comments from the simulator program without errors.

SLA also included a brief description of three monitoring processes that the group has shown in their Grafana. First of them was a &quot;System Status&quot; which was a simple metric displaying system's current status, either it was down or up and running. Second metric, &quot;Uptime&quot; was very important due to its usefulness related to meeting the agreed uptime threshold. Metrics was basically meant to display an uptime of the application in two formats, percentage and hours since last restart of the system. Last metric &quot;process\_cpu\_seconds&quot; shown the total system cpu time used in seconds.

**2.3. Maintenance and reliability**

For ensuring that the agreed SLA was upheld in the operated system our group was constantly working with Grafana. We did not get edit rights to Grafana from the operated team, so we could not set up our own alarms for any unusual system behaviour, therefore we had to make it in a different way, which worked for us as well. Our strategy was to make sure Grafana interface was constantly opened because it was providing us with an important information about either the monitored system was running, was down or had any other unexpected behaviour. Thus there was always at least one person from our team who had Grafana opened on a second screen or in a browser tab. We have experienced a few times that the Prometheus went down, due to that occurence, the system data in Grafana was not available. Due to the constant observation of the operated system we were able to catch and report those issues to the Group H in a couple of minutes. On the picture below you can see how during that event Grafana interface would look like, no data.

 ![](https://github.com/teutzue/LSD-Report/blob/master/Picture6.png) 

This event happened at least 3 times and in a beginning Group H was very fast in fixing those problems, they seemed pretty committed. On another picture you can see that they actually fixed that problem after a couple of minutes, since it was a minor fix, the only thing you would have to do was to restart Prometheus container in Docker. We have experienced the same errors in our own system so we were able to help resolve that issue to the Group H.

![](https://github.com/teutzue/LSD-Report/blob/master/Picture7.png) 

However later on, after a few weeks, the owners of the operated system seemed to lose their commitment and willingness to fix current issues. The same problem happened again and we reported that issue to the group as you can see on the picture below.

![](https://github.com/teutzue/LSD-Report/blob/master/Picture8.png) 
Unfortunately we did not get any response on that issue for 5 days. During that time we have decided to remind them again that they are losing a lot of data, and we can not monitor the system properly like that. As you can see on the picture above, the issue was created 28 days ago, then on the next one you can see how we remind them after 3 days they should look into that, and the issue was fixed 2 days later.

![](https://github.com/teutzue/LSD-Report/blob/master/Picture9.png) 

Since most of the data from the Prometheus was lost, it is relatively hard to tell either they delivered on 95% uptime as stated in SLA. However just by looking at the picture below, that presents system uptime, you can see how many times alarms were triggered and how many problems group had.

 ![](https://github.com/teutzue/LSD-Report/blob/master/Picture10.png) 

Our group have reported many issues related to violating system requirements, however only some of them were fixed. Most of the time they would just say they were working on it:

![](https://github.com/teutzue/LSD-Report/blob/master/Picture11.png) 

 ![](https://github.com/teutzue/LSD-Report/blob/master/Picture12.png) 

Finally they would have fixed some of the problems however most of the time the fix was not satisfying because was not working properly. On the next picture you can see an example of poorly implemented pagination. System had problems displaying data, since it was fetching all the available data it was talking more than 10 minutes.

 ![](https://github.com/teutzue/LSD-Report/blob/master/Picture13.png) 

Fix was in our opinion a hacky solution to the problem and was quite buggy.

 ![](https://github.com/teutzue/LSD-Report/blob/master/Picture14.png) 

To conclude this section we had a pretty hard time monitoring the others group system. We have started the cooperation quite well however over time it only got bad. In a beginning we were getting responses from Group H pretty fast, both for missing features, bugs and problems related to system being down, which was leading to violating the SLA. It seems like they were struggling a lot with their system thus the system was not very reliable.

**3. Discussion**

3.1. Technical discussion

After some reflection we have concluded that our project was handled very well and that we have done a great job at taking responsibility for our parts of the system. What made us have such a smooth ride when maintaining and creating our system was the split of responsibilities we set before actually starting the project. We split ourselves up into two major groups frontend and backend, and within those groups we set who had more responsibility of which parts of the system. That worked very well for us as there were group members that felt responsible for making their part of the system work as they would let the rest of the team down if their part didn't work. Even though we were good at handling our parts of the system one thing that did lack was solid communication between each other. Overall we knew what we each had to do and how to do it but we did not communicate between each other about the status of our work or the problems we were facing. This could have helped us find some solutions faster as someone in the team would probably be able to help out. We were also not very good at discussing how exactly we would integrate our system, this was due to the lack of documentation, even the simplest form of it. This led to us having to look through source code in order to understand how to integrate. This was fine as we all knew the languages and technologies we were working with but if one of us didn't then our process would not have worked.

In terms of the technologies we used we were very satisfied with our choices and the wide use of different techs. This made us gain experience with different technologies and made us learn a lot more than we would have expected. The only downside was the choice of our database. We did not look at other peoples experience with databases but rather made our own interpretation of what we thought would be the best database for the user case. This put us behind as we faced many problems with Neo4J, as it was not good at handling large sets of aggregated data. Other than that our stack seemed to be near flawless.

Unfortunately we did not comply with our SLA.

Our service will have a maximum response time of 60 milliseconds.

We totally failed to meet this requirement. This is due to the fact that we didn't think about growing complexity and data of system. By the time of writing that SLA we were complying with it but data was increasing too rapidly for us to keep up with quality. We believe that it could be solved by making a direct relationship between response time and size of database. For example we could promise 60 ms with 100k nodes and 2s with 1m of nodes etc.

Our services minimum expected up time is set at 95% per month.

We failed to meet this metric too. Our uptime by the last time that we took a look at it was 91,73%. This is due to the fact that as much as we want we can't monitor the system 24/7. We have different courses that also require our attention and we all have part time jobs too. For example if system will go down on Monday 3 A.M there is no way for any of us to do anything about it till 6 P.M. We are sure that if it would be a real system that we were working full time on, we would be able to meet 95% availability threshold.

3.2. Group work reflection &amp; Lessons learned

The three most important things we learnt from having done this project are: when you have a large system you must have monitoring, a way of handling updates and continuous integration if you want integrity in the system. How to actually deploy a system from end to end and the full extent of what is needed. Lastly that it is a bad idea to only use neo4j for large data sets.

The three most important things we learnt from the project all come down to handling a large system. This makes sense as we have never had to handle such a large system from end to end on our own. This has shown us what it means to handle large systems and all of the work behind them.

The most important thing we learnt; when you have a large system you must have monitoring, a way of handling updates and continuous integration if you want integrity in the system. This is important as monitoring allows us to know the status of our system at all times. A proper way of updating the system would allow us to not lose any data when updating the system and continuous integration would stop any system crashing updates to be pushed to production and increase the efficiency of a team working on the same system.

The second most important thing we learnt; how to actually deploy a system from end to end and the full extent of what is needed. This was a big lesson as we did not have the notion of how many services and components we actually need to create a stable system. This put perspective as to what it means to develop a large system and how much work and consideration is needed when making one.

Lastly, that it is a bad idea to only use neo4j for large data sets. We found out the hard way that Neo4J is not a good database to have as a standalone database managing massive amounts of data. This is due to its scalability issues and the niche problems it solves, easily retrieving data that is strongly related.

**Conclusion**

Summing up we think that this project was a major success. It was a very challenging but also rewarding experience. We think that we delivered big functional system that could be used and maintained in real life scenario. It was a very fun project that has shown us how many things are necessary to do, after product is released.

We are aware that we did not comply with SLA and we miss few features here and there (down votes from 500 karma points or more, lack of buffer which results in losing data between updates, bad performance of Neo4j in some cases). We have learned a lot on those mistakes and we think that those mistakes are actually the most valuable things that we done during this project period. In school environment they have little to none impact and if we didn't make them here, we would do them in &quot;real life&quot; industry which would have way bigger consequences.
