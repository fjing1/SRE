Written by
Carl Crous, Parker Roth, and Victoria Hurd

### Introduction
Site Reliability Engineers (SRE) traditionally support products indirectly via their services, being accountable to service level objectives (SLO), and improving the reliability of these services. However, this approach has the following limitations that can impact products and the end user experience:

Services are partial solutions for user needs and business goals. Measuring the reliability of a service is only an approximation of the user need or business goal.
User interfaces (UI) are increasingly complex. There are a number of layers between UIs and the services that SREs measure, resulting in a significant gap in the coverage of the product.
Service growth can easily outpace the engineering growth of an organization, leading to inevitable neglect of services or team overload.
Service support optimizes a small part of the product's overall reliability and performance, while significant risks exist beyond the scope of these services.
Services are synchronous by nature. Asynchronous flows are often overlooked or difficult to prioritize because their success cannot be measured by a single service.
This article explores these limitations and discusses how some Google SRE teams are addressing these constraints by refocusing support on the product and end user needs, instead of concentrating on infrastructure and services. We also discuss SLO strategies, we describe how Google SREs define a product, and we explain how to decide what's critical to achieving product reliability.

### Product Engagement
To support a product, SRE needs to be familiar with how the product and its features are designed and developed. Engaging with the product also entails learning the end user's goals when using a product.

To obtain product and user information, SRE needs to work with product managers and UX researchers, who define the product and features based on frameworks like Jobs to be Done [1,5] and Google's Critical User Journeys (CUJs) [2], which identify the end user's goals and desired outcomes.

Using this information, SRE can identify what is important to the product and its users, and they can define reliability in the same language used to define the product.

### Service support model
The core responsibilities of SREs are to be "responsible for the availability, latency, performance, efficiency, change management, monitoring, emergency response, and capacity planning of their [SRE] service(s)" [3].

In this model, services are the primary unit of ownership and work for an SRE team. This focus on services is how SRE teams are traditionally established, and it drives how SREs prioritize their work, measure their performance, and expand their scope to support more services.

SREs traditionally excel at services-based work, enabling high availability of the services that they support. However, for the following reasons, service availability doesn't always result in user happiness with a product:

The product still faces outages that impact users outside of the SRE team's scope, for example, problems that occur in a web or mobile application.
The SRE team spends time responding to conditions that potentially have no user impact. For example, HTTP 404 errors might not impact users.
When SRE scope is limited to a set of services, the SRE team often doesn't have enough information to solve these issues effectively. Instead, SREs are forced to assess the services with no higher level of understanding of how the services should perform.

Rather than accepting accountability for service reliability, SRE teams can accept accountability for the product itself. This higher level commitment prioritizes the product, not the service. This approach, which we call the product support model, opens up a new way of thinking about reliability.

### Product support model
The core of this new product support model focuses on SREs being accountable for the reliability of a product's critical functionality. Where SREs were once assigned a set of services, which were deemed critical at a fixed point in time, SREs are now assigned to collections of features and user outcomes. Without the fixed boundaries of traditional service ownership, SRE teams are empowered to align their priorities to business and user outcomes and tackle wider ranging and more impactful efforts at every layer of the service stack.

"If you can't measure it, you can't improve it." — Peter Drucker

Before an engineering team can manage the reliability of a product, it must first be able to measure its reliability. Establishing a set of metrics that provide sufficient breadth and depth of coverage is critical for teams to identify the most impactful engineering efforts and avoid the temptation to over-optimize any particular system.

Before a team can measure the reliability of a product, it must first know what to measure and how to measure it. Knowing what to measure comes from modeling a product as a collection of user-visible behaviors, or features, and prioritizing the behaviors in terms of the value they bring to the user. With this prioritized list in hand, a team can begin to pivot from supporting teams and services to supporting the product and its users.

Fig. 1 illustrates how the ultimate goal of improving the reliability of a product starts with a solid foundation of stakeholder alignment, product modeling, and measurement. Together, these concepts enable the team to dedicate their resources toward the issues which are most impactful for the end user and the business.

Product Reliability bootstrapping workflow

![image](https://github.com/user-attachments/assets/be0381b9-4d6c-4ef5-a1f0-2dd37fa1cdd8)

Fig. 1: Product Reliability bootstrapping workflow

Get Started
In this section, you'll learn how to implement a product-focused reliability engagement, including the following key steps:

#### Engage your stakeholders
#### Model the product
#### Measure performance
#### Manage reliability

Each step lists intermediate deliverables that bring value to the SRE team and the opportunities they unlock. We encourage you to proceed in order, but we also highlight areas where you can leverage existing metrics and relationships.

1. Engage your Stakeholders
As with any SRE engagement, the first step is to identify the relevant stakeholders. Where service-based engagements rely on a partnership between SRE and a developer team, product-focused engagements require a more diverse set of partners. This broader set typically includes the following:

Product managers: Define product strategy and requirements.
UX designers and researchers: Turn requirements into user experiences.
Engineering teams: Develop the necessary features and infrastructure to implement user experiences.
Support specialists: Interface with the end user via direct or written communication.
To build the foundation you need to successfully manage product reliability, it's critical to identify the stakeholders responsible for each role.

Deliverable: Documented roles and responsibilities, for example, a RACI matrix [7].

After you identify all the relevant stakeholders, meet with each stakeholder to initiate SRE's partnership with them.

2. Model the Product
People use a product to achieve a real-world objective. You need to understand what the product's users are trying to accomplish.

This section introduces two key concepts that will be important to keep in mind throughout the rest of this article: **user objectives and steps**. A user objective describes a user's intent and the goal that they want to achieve. For example, an objective for a mail service might be "Communicate with people." Steps are the individual actions a user takes to reach their objective.

The Jobs to be Done (JTBD) [1,5] framework models user objectives as jobs, while Google's Critical User Journeys (CUJs) [2] models user objectives as goals with an accompanying set of tasks or steps.

Understanding the user's objective in using a product or feature is a powerful tool for software development, because it gives you a clear signal about the most important aspects of the product to focus on. The user's intent is also a powerful reliability tool that SREs can leverage.

Assuming your product is built using a framework like Jobs to be Done, you will have a curated list of user objectives that the product aims to facilitate. Product managers, UX designers, and other non-engineering disciplines likely own this list (at least in part). This list, which provides high-level descriptions of what the user wants to achieve, is based on cross-functional data that provides a common language among the many disciplines involved in the software development lifecycle. Leveraging these shared user objectives as the foundation for product support is the fundamental first step in modeling your product's reliability.

However, if you don't have a list of user objectives, then you might need to develop this list yourself, which comes at a significant engineering cost and results in a list that only SREs are invested in using. A better approach is to engage with and encourage product managers –who are accountable for ensuring the product meets the needs of end-users to adopt one of these frameworks and own the definition of the user objectives.

User objectives are broken down into a list of steps that the user takes to accomplish the overall objective. Each step is an isolated unit of work, independent of the other steps. These steps provide the following information:

An explanation of what a user is doing with a product, or the user goal.
A definition of a start condition and a number of success or failure conditions for each step.
A tangible list of actions that you can relate to the product's interface or infrastructure, for example, the RPCs called when a user is sending an email.
The objectives are just an organizational element that groups steps together.

Based on the steps, you can identify which parts of the product's interface and infrastructure are required to complete those steps. Some parts of a single interface or infrastructure service might address different objectives, and can therefore have different levels of importance.

Deliverable: A product registry of the user objectives and steps with high-level descriptions.

Mail service product definition example

Let's consider an email product that lets users send and receive emails, including a few value-added features. We'll model this product in terms of the objectives and steps detailed in Table 1 and illustrated in Fig. 2. Table 1 provides the basic product model that can be used by multiple disciplines involved in the product's development and support.

Objective	Step	Description
Compose mail	Login	The user authenticates themselves on the login page.
Open compose dialog	The user clicks the Compose button, and the Compose dialog is presented.
Lookup addresses	The user starts to type a recipient's email address. A number of matching addresses are presented for selection.
Check spelling	As the user types their message, spelling mistakes are highlighted.
Send mail	The user clicks the Send button. The message is queued for delivery in the mail service.
Read mail	Login	The user authenticates themselves on the login page.
Open inbox	The user opens their inbox page, which lists all their email messages..
Open message	The user selects an email message, and its contents are presented.
Receive mail (async)	When the mail service receives new messages, they are automatically displayed in the user's inbox.
Table 1: Example user objectives and steps

Flow diagram of user objectives and steps

![image](https://github.com/user-attachments/assets/9cbafd8f-ca26-433c-b689-59b596c8b7f5)

Fig. 2: Flow diagram of user objectives and steps (see Table 1)

Product criticality and prioritization

When you pivot to a product support model, keep in mind that SRE teams will experience a greater load as they learn this new approach. Be sure to manage the additional effort required from SRE teams by strategically choosing where and how to support the product.

For example, you might want to focus a lot of support on a core user objective and its steps, instrumenting them with high cost but accurate end-to-end SLOs. In this scenario, you might choose to support less critical objectives through cheaper and more traditional server-based SLOs.

Prioritizing SRE work is a challenging problem of its own. We solve this problem by defining importance based on the relationship between specific objectives and the product's key performance indicators (KPIs). These indicators are often already used by products to assess the severity of outages, so using these KPIs to prioritize SRE tasks ensures that SREs keep pace with evolving product needs.

At Google, we use product-specific severity guidelines (like the scenarios listed for Google Cloud major incidents [6]) to indicate the severity and impact of an outage on a product. In Google Ads, revenue is a key indicator of outage severity, while in YouTube, severity guidelines include time users spend watching videos. See the mail service severity example to learn how you can define severity guidelines for a typical mail service. While this classification is primarily used to measure the impact of outages, it also provides a clear signal of importance that you can apply to other areas.

Severity guidelines are often written in terms of the impact on users, and therefore correspond closely with the user objectives and steps. Using the severity guidelines, you can organize your user objectives and steps based on the impact that a severe outage would have on a product or feature, which is defined as the product criticality. See the mail service product criticality example to learn how you can define product criticality for a typical mail service.

This criticality definition gives clear guidelines for prioritizing SRE work. Ensure that there's a good base coverage for the entire infrastructure and develop your own set of principles for assessing the ROI for more targeted efforts. Consider what level of reliability you want to achieve for your most important objectives before focusing on the less important ones.

Often, teams fall into the trap of developing a new method to address a reliability concern, and then they try to apply that approach to all scenarios, regardless of suitability. However, initial instrumentation costs and the ongoing maintenance costs of deploying improvements everywhere are not always justified. For example, offline or batch traffic doesn't have the same availability and latency requirements as interactive, user-facing traffic, and therefore different SLOs are required for each of these traffic classes.

Gaining a deeper understanding of what's important to the product has many benefits, from knowing where test coverage is important to better incident response. If an SRE knows an issue is severe, they will react and escalate more quickly, resulting in a faster resolution.

##### Deliverable: A set of severity and criticality definitions aligned with the product's user objectives and steps.

Mail service severity example
Continuing the mail service product definition example, we define the product's incident severity guidelines as follows:

Severity	Description
Major	Any impact to receiving or sending mail.
> 20% impact to core features
Medium	> 5% impact to core features.
> 20% impact to auxiliary features
Minor	> 0% impact to any feature
Table 2: Example incident severity guidelines.

In this example, core features are those features that are required for users to be able to read and compose emails, like the authentication system or the address book. Auxiliary features, like spell checking and auto-complete, add value and are nice to have, but they aren't critical to product usage.

The definition of impact varies from product to product, based on unique user and business needs. Some products might focus purely on technical impact like percentage of failed requests or percentage of requests with latency above an acceptable threshold. Other products might focus on revenue loss, brand damage, or other concepts more directly connected with business key performance indicators (KPI). No matter which criteria you choose for a product, it's important to consider these key principles:

The resulting collection should cover all aspects of user perceived behavior and business priorities.
It should be possible to determine an incident's severity in a timely and sustainable manner.
There should be broad alignment about the incident severity guidelines across product and engineering leadership.
Mail service product criticality example
Using the severity guidelines in the mail service severity example, we define the product criticality as follows:

Criticality	Description	User Objectives > Steps
Critical	Services that are responsible for mail transport and delivery, and are critical (not optional) dependencies of core features.	- Compose mail
- Read mail
Important	Services that are non-critical dependencies (graceful failover) of core features or are dependencies of auxiliary features.	- Compose mail > Check spelling
- Read mail > Filter spam
None	All other services, typically internal or unlaunched features.	All other product features not identified as product priorities.
Table 3: Example product criticality definition.

You can see how the critical definition is modeled around the user's objectives (like composing and sending email) rather than how the system was implemented to address these needs. Some specific steps (like checking spelling and filtering spam) can have different criticalities if they aren't considered critical components of the objective. A number of aspects aren't explicitly listed, like authentication, since that's one of the steps in most of the objectives.

Additional value

While Measure performance discusses how you can use this product model as the foundation for measuring a product's performance, this is not the only value provided by the model. A clear understanding of the user goals for your product enables you to align testing strategies, product usage metrics, and other production concerns on a shared foundation of user needs.

3. Measure Performance
SLOs are a key component of any SRE team, providing tangible metrics that reflect the reliability of a system. The SRE Workbook [4] is a detailed resource that defines SLOs and service level indicators (SLI) and describes how to use them, particularly with respect to services.

Service-based SLOs generally don't provide adequate product coverage. There are classes of issues that cannot be measured from a single server, for example, issues that occur within a web or mobile application, or through an asynchronous action.

When an SRE team discovers the gaps in using service-based SLOs, they start to expand their SLOs in a number of ways. There are three main categories of SLOs that provide broader coverage for the product: service SLOs, client-side instrumentation, and end-to-end SLOs.

Service SLOs

Service SLOs are the most common type of SLO. They are instrumented through measurements taken from the service itself (like application server logs or live monitoring metrics), or through the logs of a layer above the service (like a load balancer). You can also use black box monitoring results to measure service uptime.

The following are the main characteristics of service SLOs:

Characteristic	Description
Low cost	Service SLOs are common and widely used.
High confidence	The data obtained for service SLOs is under your control and can be provided with very high availability guarantees, like an SLO on the SLO data.
Low latency	You can gather and process data with a very low latency, from seconds to a few minutes.
Narrow coverage	These SLOs only cover what the service can see, which is improved by measuring the SLOs at a higher layer in the service stack. If the returned result is considered a success to the service, it passes the SLO. However, successful results don't always guarantee that the results are useful, for example, empty responses or new incompatible data being returned to an old client.
Table 4: Characteristics of service SLOs

Client-side instrumentation

With the development of more sophisticated (and more complex) user interfaces through web and mobile applications, more SRE teams have started to support these interfaces directly. This can be achieved by retrieving telemetry directly from user interfaces.

This telemetry requires some logging or monitoring side-channel that records events which can then be processed and used to define SLOs.

This data typically must be batched, and is sent in the background, separately from the interactive RPC requests. When an application is closed or loses network connectivity, some telemetry might be lost. All of these factors impact the reliability of data; and some single digit percentage loss is expected. Furthermore, measuring signals on client devices also includes measuring the device's performance and internet connection.

However, client-side SLOs provide helpful insights into a product's performance as experienced by the users. You can use these SLOs to provide a better user experience for users who have less reliable connections. Complexities in how the interface behaves, for example, caching, retries, and asynchronous RPC requests, are transparent when you can measure start and end conditions from the user interface.

Consider all of the following characteristics when you're deciding when and how to use client-side SLOs:

Characteristic	Description
Moderate cost	Instrumenting client-side SLOs requires additional features in the user interface to retrieve the data, and a system to receive and process the data and provide the SLOs.
Low confidence	There is expected data loss. Measurements can be impacted by poorly performing client devices and internet connections.
Moderate latency	Signals typically travel from client devices in batches. These signals require additional processing before they can be used in SLOs, resulting in delays from 15 minutes to one hour.
Broad coverage	The measurements from the client devices allow for SLOs that directly measure the user's experience of the product's performance and reliability.
Table 5: Characteristics of client-side SLOs

End-to-end SLOs

Some product features and business metrics can't be directly measured through service or client-side instrumentation. These often require joining data from multiple sources and typically follow asynchronous tasks.

For example, a user might ask to generate a report. The user interface sends an RPC request which succeeds once the report is enqueued in the system, but that doesn't mean the report is successfully generated. Some backend system dequeues the report and generates it, or fails to do so. To measure if the report is successfully generated, you need to combine the user's original RPC request with the final report generation result.

An end-to-end SLO generally measures its SLIs per user interaction, and can therefore provide fairly accurate results.

The implementation of an end-to-end SLO requires a fair bit of engineering effort because you need to do the following:

Determine the set of data required to measure the SLI.
Log and record the required data.
Aggregate and join the data from multiple sources.
The main characteristics of end-to-end SLOs are as follows:

Characteristic	Description
Very high cost	Instrumenting an end-to-end SLO can require months of engineering effort. Each SLO can result in unique engineering challenges and constraints.
High confidence	You can use multiple data sources, and you can track missing data in any of those sources by cross-referencing them.
High latency	Combining signals from multiple sources can be time consuming and complex. Continuously processing new data comes at a high cost, so offline batch solutions are often favored (typically run once per day or every few hours).
Narrow coverage	An end-to-end SLO aims to cover a very specific feature or business metric, and it does so with the highest degree of confidence.
Table 6: Characteristics of end-to-end SLOs

Prioritization

Websites, mobile applications, and other internet-connected devices are becoming increasingly complex, which has driven the rise in microservice architecture that helps to simplify and scale the services that are needed to run modern applications.

The number of servers around the world has grown exponentially. Evidence of this can be seen in the growth of public cloud providers like GCP. However, the number of software engineers globally has a much smaller linear growth.

It is also important to recognize that every SLO carries an ongoing cost to maintain its underlying data and respond to SLO misses. Certain product features become less important as they age, while others may remain critical for the lifetime of the product.

It is inevitable that software engineers and site reliability engineers need to prioritize which SLOs are still relevant and where engineers should spend their time improving the reliability of a product.

Product SLOs

A product-level SLO is defined around a step that a user takes to achieve their objectives. Any SLO (like service, client-side, or end-to-end) can be transformed into a product-level SLO by annotating and expanding it with user objective step information.

The relatively simple task of linking SLOs to user objectives gives you an understanding of what the SLO means to the user and how important the SLO is to the product.

Setting the objective target or latency thresholds is notoriously challenging when the SLOs are framed around services and infrastructure. But when the SLOs have the additional context of the user's objectives, what is being measured becomes much clearer. For example, instead of asking "How reliable and fast should the AddressLookup service on the user-data-server be?" you might ask, "How many errors can users tolerate when looking up email addresses and how quickly should those email addresses be returned?"

When combined with product criticality, you can validate that SLOs are set appropriately and are not unjustifiably strict. This approach gives you a broader range of SLO targets that represent what's important to the product.

Example of how product SLOs are defined around user objectives and steps

Fig. 3: Example of how product SLOs are defined around user objectives and steps.

Deliverable: A prioritized list of product SLOs that cover different SLO types (availability, latency) and instrumentation methods (service, client-side, end-to-end). Implement the highest priority SLOs and use the lower priority SLOs to create a backlog that you can prioritize later.

Telemetry

Before you can implement the SLOs discussed in Product SLOs, metrics (also known as SLIs) must be available to help you track the relevant product functionality. To develop these metrics, you need to bridge the conceptual world of the product model with the product's real-world infrastructure. This capability is a key value driver of the product reliability model.

Whereas a team's understanding of SLO performance was once limited to "database writes availability is 98%," for example, product-focused SLOs can show that those 2% of errors are causing 100% of "save email draft" steps to fail.

The key requirement to turn aggregate availability metrics into accurate and actionable indicators of product performance is the ability to know what product functionality is supported by each request. Beyond simple observability needs, this request-level information can also be used in traffic routing and load shedding policies to ensure that lower priority functionality doesn't harm more mission critical features.

While you can associate requests with product functionality in a number of ways, there are two main methods to consider: client-side annotation and server-side annotation.

Annotation	Description	Use case
Client-side annotation	Update the user interface (web pages and mobile applications) to attach the user objective step metadata to the RPC requests sent to your backend services. This is technically challenging and requires your UI teams to help design and eventually own and maintain these annotations.	Using client-side annotation offers the most accurate method of mapping what a user is trying to achieve with the interface they're using. The annotation is done on individual RPC requests and therefore has the highest fidelity.
Server-side annotation	Analyze the RPC requests that a backend server receives and infers what the user's objective step is.	This method is necessary when it's too costly to modify the client, or when you don't own the client (like REST services). Server-side annotation is not as accurate as client-side annotation since a number of assumptions are needed for a server to infer which step a RPC request is serving.
Table 7: Comparison of client-side and server-side annotation

You likely won't be able to annotate all of your traffic. By following your product criticality guidelines, you can decide where the benefits are justified; you might annotate only the most critical user objectives.

After you annotate an RPC request, your services will need to propagate the annotation throughout your infrastructure stack. Propagation helps you understand the user's objectives and their importance in many SRE tools and processes, including the following:

Understanding issues and their impact by having the user objective steps in monitoring dashboards and application logs.
Quickly determining which user objective is impacted by an issue, and therefore the severity of an incident.
Identifying SLOs that only consider specific user objective steps and ignore less important requests like batch and test traffic.
4. Manage reliability
Now that you've identified your stakeholders, user objectives and steps, the criticality definition, and your product SLOs, you are ready to support the product.

We recommend that you start supporting a product or service on a small scale, for example, by onboarding a single objective or step with some SLOs, before iterating to expand your team's responsibility and scope. Supporting a product is an ongoing process that is repeated continually. At every stage of supporting a product, you will work with your stakeholders to determine trade-offs between the following areas of investment:

Improve the metrics for the set of supported objectives to increase confidence that the objectives are meeting expectations.
Expand the set of supported objectives to ensure all aspects of the product are meeting expectations.
Address performance gaps highlighted by metrics produced by investment areas #1 and #2.
Be sure to invest across all three areas. Not investing sufficiently in areas #1 and #2 can cause your team to miss targeting the most impactful issues in area #3. On the flipside, investing only in areas #1 and #2 won't result in any improvements that help the user and your organization's business goals. Use the information you gathered and produced during the key implementation steps to help your organization determine how much to invest in each of these areas.

#### Onboarding

Onboarding is the process in which an SRE team evaluates a product or service that they are going to assume responsibility for. This process usually involves learning about the product or service to adequately support it and ensuring that the product or service meets a set of requirements defined by the SRE team.

Before an SRE team assumes responsibility for a product's user objectives and steps, the SRE team needs to define a formal onboarding process that ensures the following:

The objective and steps are important enough.
The steps are mapped to the services that they use, and are annotated within these services.
The potential SLOs are identified and prioritized within the overall product priorities.
The SLOs are stable, and supporting them would not pose an undue burden on the SRE team or the service owner.
The SRE team drives this process, meets with the appropriate stakeholders, and works with product teams to resolve any deficiencies.

#### Iterate

After you complete the onboarding process for one objective and a subset of its steps, you can then iterate and improve your team's coverage in the following ways:

Onboard more user objectives and steps.
Refine the coverage of user objectives and steps that are already onboarded.
Improve your tooling to better support the user objectives and steps.
We recommend that, instead of trying to support every user objective, you set a threshold for the most important aspects of the product you're supporting.

Likewise, we recommend that you decide which user objectives are instrumented with the more expensive SLO methodologies. Some user objectives might only require server-based SLOs, while others might benefit from client-side or end-to-end instrumentation.

Finally, to determine where and when to invest in improved tooling, evaluate the sustainability of your support. Insufficient tooling results in scaling limits that overwhelm the SRE team with inefficient triaging, debugging, and mitigation efforts for the set of supported objectives.

#### Offboarding

To ensure that your team focuses on the right parts of the product, we recommend that your team periodically re-evaluate the user objectives, steps, and SLOs that your team supports. Some SLOs may become less important over time, and some user objectives may become obsolete.

It is important to maintain a prioritized list of all the supported user objectives, steps, and SLOs. Use this list to verify that your team is spending time and resources on the most important areas. When items on the list are no longer important (or are much less important than unsupported items), consider offboarding them so that the SRE team can re-focus on the most important aspects of the product.

#### Server support

Your focus on the product must not come at the expense of the infrastructure fleet of servers that supports it. There is always a requirement to ensure that servers, and the services they provide, remain healthy. Ensuring server health is necessary regardless of how critical servers are to the product (you are, after all, spending resources to run them).

The concept of providing baseline support emerged at Google before the idea of focusing on the product. Baseline support is essentially a set of standards and best practices that SREs define, which allow them to provide general support to any server without understanding the server's specific purpose. For example, baseline support can require certain libraries and frameworks to provide common functions like load balancing, inter-service communication (RPCs), or even minimal SLOs that are owned by the development team that's primarily responsible for the server. In this world, the platform rather than the SREs provide baseline reliability support, which frees SREs to focus on more impactful reliability improvements that aren't covered by basic policies.

Additional support might be required for the most critical infrastructure, however, this is not a requirement. If SREs are primarily responsible for the most critical user objectives, significant failures in critical infrastructure will fail the product-level SLOs. Moreover, failures at the infrastructure level that do not impact the product SLOs will not alert SREs and can be handled by the development team that owns the service (like issues with unlaunched features and batch services that are not as critical).

Focusing SREs support on product SLOs instead of focusing on services allows the SREs to support more of the product and align that support with what's most important to the product.

When is the product reliability model a good fit?
The product-focused support model isn't a good fit for all use cases. Before you implement this support model, we recommend that you assess the suitability of this model by ensuring that your project meets the following requirements.

Clear roles and responsibilities
This model requires a broader set of stakeholders than a traditional service-centric engagement. Whereas traditional, service-based SRE engagements are often scoped to engineering organizations, product-based engagements require interfacing with a larger set of roles (like PM and UX). Effectively engaging your stakeholders requires clear agreement about the ownership roles and responsibilities between these parties. Even a simple user action like loading a homepage means that someone must be able to define how slow is too slow, and how fast is fast enough. Without clear consensus about who is accountable for answering these questions, it is not possible to leverage the results as requirements to measure and manage the reliability of the product.

User objective and step definitions
As stated in Model the product, user objectives and step definitions are required to adopt the product-focused support model.

SREs can try to retrofit and derive user objectives for an existing product, but without the support of the entire organization, many of the benefits of product-focused support will be lost. If the stakeholders who are responsible for making product decisions don't adopt and own the product definition, it will be inaccurate and will quickly become out of date.

A product model that is owned and maintained by the right stakeholders is therefore a key requirement for the product support model.

Easily maintainable servers
Server support provides details about the server support that are necessary when following a product support model.

A fundamental requirement is that individual servers must be relatively easy to support. This requires sufficient standards and common frameworks that allow development teams to deploy and maintain individual servers themselves, with SREs helping when necessary.

If SREs spend most of their time supporting individual servers, they won't have the capacity to focus on product-level concerns.

Clear connection to user-facing functionality
Tracking user objectives and steps must be measured as close to the user as possible (for example, in the user interface or the servers that handle requests from the user interface) and is therefore more closely aligned with the servers at the entry points of the stack.

Infrastructure servers that provide a service API that typically solves a specific problem might not be directly related to the user's objectives. For example, a data storage service might have a low-level API with simple load and store commands that aren't associated with specific user objectives.

The following are options for applying a product support model to infrastructure servers:

Option	Description
Treat infrastructure clients as users and define user objectives and steps for the infrastructure's API.	This is the least desirable option because it is the same as having a product without user objectives. However, there might be situations in which the infrastructure itself can be viewed as a product. In these cases, though, the product decision-makers must define the product; this is not an SRE responsibility.
Configure infrastructure clients to provide the user objective information based on user input from their end users.	This option helps infrastructure teams in the following ways:
Provides valuable information about the end user's goals.
Helps infrastructure teams identify the RPC requests that are important to the end user.
Do not adopt a product focus.	Infrastructure services are defined by their service APIs, which might benefit from the traditional service support model. If the product support model offers benefits that you want to access, consider how you might achieve those benefits in other ways.
Table 8: Options for applying a product support model to infrastructure servers

### Conclusion
The product support model provides a framework for SRE teams to focus their support on what matters most to the users (a traditional SRE goal) and the business as a whole. This model aligns the SRE team with the rest of the organization and supports a common nomenclature and goals.

The model solves a number of problems with the traditional service support model by helping SREs understand what's most important to the product, and by helping SREs use that knowledge to better cover the gaps in the service support model. Ultimately, the product support model lets the SRE team focus on the user to ensure that the product meets the end user's real-world needs.

References
[1] Ulwick, A.W. and Osterwalder, A. (2016). Jobs to be done : theory to practice. [online] Idea Bite Press. Available at: https://jobs-to-be-done-book.com/

[2] chang, austin (2017). What To Do If Your Product Isn't Growing. [online] Initialized Capital. Available at: https://medium.com/initialized-capital/what-to-do-if-your-product-isnt-growing-7eb9d158fc

[3] Beyer, B., Jones, C., Petoff, J. and Murphy, N. (2016). Site reliability engineering : How Google runs production systems. Sebastopol, Ca: O'reilly Media.

[4] Beyer, B., Murphy, N., Rensin, D.K., Kawahara, K. and Thorne, S. (2018). The site reliability workbook practical ways to implement SRE. Cambridge O'reilly.

[5] Kalbach, J. (2020). The jobs to be done playbook : align your markets, organizations, and strategy around customer needs. New York: Two Waves Books.

[6] Google Cloud. (n.d.). Incidents and the Google Cloud Service Health Dashboard | Support Documentation. [online] Available at: https://cloud.google.com/support/docs/dashboard#major_incident [Accessed 20 Apr. 2023].

[7] Wikipedia Contributors (2019). Responsibility Assignment Matrix. [online] Wikipedia. Available at: https://en.wikipedia.org/wiki/Responsibility_assignment_matrix
