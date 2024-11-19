
![AEM Replication](./Titleimages/Replications.png)

### Objective

- After reading this Article, You should have an Understanding of 
    - [Introduction](#Introduction)
    - [what is Replication?](#what-is-replication)
    - [How to Configure Replication Agent?](#how-to-configure-replication-agent)
    - [what is Reverse Replication?](#what-is-reverse-replication)
    - [Replication Using Custom API](#replication-using-custom-api)
    - [Workflow Process Step for Replication via Custom Replication Agent](#workflow-process-step-for-replication-via-custom-replication-agent)
    

### Introduction

In AEM, sometimes we come across the scenario of replicating the content with a specific replication agent. For example, if we want to replicate the content from one instance to another AEM instance, we can’t achieve this with the default replication agent. To achieve this, we have to create the replication agent and replicate with the help of the Replication API and Replication Options.

### what is Replication?
* Replication allows to push content from an author to publish AEM instance.

* AEM Author instance generally hosted on 4502 port. This is an internal (not for end user) server mainly used for content authoring. Content will get publish or transfer to multiple AEM instances on content replication which is also called as activation.

* We can have one or more than one publish AEM instance.

![AEM Replication Image](./Images/author.png)

### How to Configure Replication Agent?

Replication agent is a configuration which help us to publish content from author to publish instance. Replication agents needs to be created for each publish instance.

We can create the replication agent following the below steps:

-  Navigate to below URL and select Configuration tile.

- http://localhost:4502/sites.html/content

![AEM Replication Image](./Images/Replication%20Image/replication1.png)

- Traverse to Replication option which will have Agents on author and Agents on publish option.

- http://localhost:4502/etc/replication.html


![AEM Replication Image](./Images/Replication%20Image/rep.png)


- Click Default Agent to open the default replication agent 

![AEM Replication Image](./Images/Replication%20Image/replication%20Image%20Author.png)


- Then click on “Edit” after Settings to open the Agent Settings dialog box to enter the details.

![AEM Replication Edit](./Images/Replication%20Image/replication%203.png)

- Under the settings tab, update the following

- Enabled — check true

- Agent user ID — Leave this empty


![AEM Replication Edit](./Images/Replication%20Image/replication%204.png)

- Under the transport tab  update The “URI” with the host name and port number 

- Configure URI — http://localhost:4503/bin/receive?sling:authRequestLogin=1

- update the “User” and “Password“, As per your publish instance User Name and Password. (For me: User: admin, Password: admin)

![AEM Replication Edit](./Images/Replication%20Image/replication%205.png)

- Click on “OK” and then click on “Test Connection“

![AEM Replication Edit](./Images/Replication%20Image/replication%206.png)

- If all are okay then you can see a page with “Replication test succeeded“

- you can go to the author package manager, select a package and from the more option replicate the package.

![AEM Replication Edit](./Images/Replication%20Image/replicate2.png)


### what is Reverse Replication?

- AEM Publish instance generally hosted on 4503 port and available to access for end users.

- As part of reverse replication, content will get replicate or transfer from publish instance to author.

![AEM Replication Edit](./Images/Replication%20Image/AEM_Publish.png)

- Out of the box, only cq:Page is applicable for reverse replication. For other node it require to write custom code.

- Reverse replication needs to be triggered if there is any change in the content. Add cq:lastModified, cq:lastModifiedBy, cq:distribute properties as part of an event to fire reverse replication agent.

- At the time of activation please clear cq:lastModified, cq:lastModifiedBy, cq:distribute properties to avoid infinite loop.


### **Reverse Replication is not available in AEM cloud version**

### Replication Using Custom API

Use com.day.cq.replication API to publish or replicate a resource.

Follow below steps for custom resource replication:

- Use @Reference annotation to consume Replicator OSGI service

```java
@Reference
Replicator replicator;
```

- replicate() method from Replicator interface will help us to publish content or resource as soon as we provide session, replication type and content, asset or resource path.

```java
replicator.replicate(session, "Activate", resource_path);
```

### Workflow Process Step for Replication via Custom Replication Agent

AEM provides a Replication API which replicates the content from an AEM Author to the AEM Publish instance or any other author instance. Here, we will see how we can replicate content programmatically. We can accomplish this with Sling Servlet or Workflow Process Step.

So let’s see the implementation code with Workflow Process Step

CustomerReplicationStep.java
```java
package com.adobe.learning.core.workflow;

import com.day.cq.replication.*;
import com.day.cq.workflow.WorkflowException;
import com.day.cq.workflow.WorkflowSession;
import com.day.cq.workflow.exec.WorkItem;
import com.day.cq.workflow.exec.WorkflowProcess;
import com.day.cq.workflow.metadata.MetaDataMap;
import org.osgi.framework.Constants;
import org.osgi.service.component.annotations.Component;
import org.osgi.service.component.annotations.Reference;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import javax.jcr.Session;

/**
 * @author Shiv Prakash
 */

@Component(service = WorkflowProcess.class, property = {Constants.SERVICE_DESCRIPTION + "= Custom Replication Step",
        Constants.SERVICE_VENDOR + "= workflow.com",
        "process.label" + "= Custom Replication Step"})
public class CustomerReplicationStep implements WorkflowProcess {

    private static final Logger logger = LoggerFactory.getLogger(CustomerReplicationStep.class);

    @Reference
    Replicator replicator;

    @Reference
    AgentManager agentManager;

    Session session;

    //Hard Coded selected Replication Agent Name
    //(Name available in Settings Tab of Custom Replication Agent)
    String replicationAgentName = "Custom Replication Agent";

    //Hard coded payload for replication
    String payloadPath = "/content/learning/us/en/samplePage";

    @Override
    public void execute(WorkItem workItem, WorkflowSession workflowSession, MetaDataMap metaDataMap) throws WorkflowException {

        try {
            //Getting Session form Workflow Session
            session = workflowSession.getSession();

            //Getting Replication Agent with help of Agent Manager
            for (final Agent replicationAgent : agentManager.getAgents().values()) {
                //Filtering the replication agent with the help of Name
                if (replicationAgentName.equals(replicationAgent.getConfiguration().getName())) {
                    ReplicationOptions replicationOptions = new ReplicationOptions();
                    //Setting replication Options for replicating via Custom Replication Agent
                    replicationOptions.setFilter(agent -> replicationAgentName.equals(agent.getConfiguration().getName()));
                    replicationOptions.setSynchronous(false);

                    /* PayLoad Replication*/
                    replicator.replicate(session, ReplicationActionType.ACTIVATE, payloadPath, replicationOptions);
                    logger.info("Replicated via Agent : {} for Payload {}", replicationAgentName, payloadPath);
                }
            }

        } catch (ReplicationException e) {
            logger.error("Exception Occurred in Replication {}", e.getMessage());
        }
    }
}

```

When we run the workflow for a given payload It will replicate the content with the selected payload. We can see below the logger entry after successful replication:

```
12.07.2024 09:16:11.067 *INFO* [JobHandler: /var/workflow/instances/server0/2024-07-12/contentmigration:/content/learning/us/en/samplePage]
com.adobe.learning.core.workflow.PackageReplicationStep Replicated via Agent : Custom Replication Agent for Payload /content/learning/us/en/samplePage
```