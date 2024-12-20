![Sling Model](./Titleimages/slingmodel.png)

### Objective

- After reading this Article, You should have an Understanding of 

    - [What is Sling Model ?](#what-is-sling-model)
    
    - [Features of Sling Models](#features-of-sling-models)
    
    - [How Many Ways We Can Adapt Sling Model ?](#how-many-ways-we-can-adapt-sling-model)

    - [Lets Understand Common Annotation](#lets-understand-common-annotation)

        - [@Model Annotation](#model-annotation)

        - [adaptables](#adaptables)

        - [adapter](#adapter)

        - [resourceType](#resourcetype)

        - [defaultInjectionStrategy](#defaultinjectionstrategy)

    - [Create a Component using Sling Model](#lets-create-a-component-using-sling-model)

    - [Sling Model Work Flow](#sling-model-work-flow)

    - [Best Practices for Writing Sling Models in AEM](#best-practices-for-writing-sling-models-in-aem)

    - [Best Approach for Practices](#best-approach-for-practices)

    - [How to Handle Both Scenarios?](#how-to-handle-both-scenarios)

### What is Sling Model ?

- Sling Model is the one of restful framework which is created inside the AEM Architecture which is used to map request or resource objects.

- A Sling Model is implemented as an OSGi bundle. 

### Features of Sling Models

- **Annotations-Driven**: it Utilizes annotations like @Model, @Inject, and @ValueMapValue to streamline the mapping of properties in AEM components.

- **Built-in Dependency Injection**: Enables automatic injection of properties, services, and objects from the Sling context, simplifying the code

- **Versatile Adaptability**: Designed to work with a variety of adaptable objects, including Resource and SlingHttpServletRequest, to suit diverse use cases.

- **Modular Structure**: Encourages a clear separation of concerns, keeping content retrieval and business logic distinct from the component’s HTL view.

### How Many Ways We Can Adapt Sling Model ?

- we can adapt sling model in two ways.

    - Resource.class

    - SlingHttpServletRequest

#### Syntax

```Java
@Model(adaptables = {Resource.class, SlingHttpServletRequest.class}, adapters = {Button.class}, resourceType = {ButtonImpl.RESOURCE_TYPE}, defaultInjectionStrategy = DefaultInjectionStrategy.OPTIONAL)
public class ButtonImpl extend Button{
    // logic
}
```

### Lets Understand Common Annotation

#### @Model Annotation

- This annotation define the class as a Sling Model and must be present on the top of the class.


#### adaptables.

- The adaptables attribute defines the types of objects that a model can be adapted from. There are two primary types

   - Resource.class

        - Represents the data stored within the JCR (Java Content Repository). When content is treated as a resource, you can use Resource.class to adapt it and access its properties or child resources effectively.

    - SlingHttpServletRequest

        - Useful when you need to access HTTP request-specific information, such as parameters, headers, or session attributes. Additionally, it allows access to WCM-specific objects like currentPage and currentDesign to support more dynamic use cases

#### adapter

- The adapters attribute specifies the interface(s) or class(es) that this model will be adapted to.

#### resourceType 

- The resourceType attribute specifies the resource types that this model supports.    


#### defaultInjectionStrategy

- The defaultInjectionStrategy attribute determines how Sling Models handle injection when a value is missing. There are two types of injection strategies 

    - **DefaultInjectionStrategy.OPTIONAL** If a value is unavailable for injection, it will simply be set to null instead of throwing an error.

    - **DefaultInjectionStrategy.REQUIRED** Ensures that all required values must be available for injection. If a value is missing, an error will be thrown

### Let`s Create a Component using Sling Model

- We will create a new component named “CustomComponent” with the following structure in CRX/DE.

    ![Component](./Images/component/component16.png)

- cq:dialog code here 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<jcr:root xmlns:jcr="http://www.jcp.org/jcr/1.0" xmlns:nt="http://www.jcp.org/jcr/nt/1.0" xmlns:granite="http://www.adobe.com/jcr/granite/1.0" xmlns:cq="http://www.day.com/jcr/cq/1.0" xmlns:sling="http://sling.apache.org/jcr/sling/1.0"
    jcr:primaryType="nt:unstructured"
    jcr:title="Custom Component"
    sling:resourceType="cq/gui/components/authoring/dialog">
    <content
        jcr:primaryType="nt:unstructured"
        sling:resourceType="granite/ui/components/coral/foundation/container">
        <items jcr:primaryType="nt:unstructured">
            <tabs
                jcr:primaryType="nt:unstructured"
                sling:resourceType="granite/ui/components/coral/foundation/tabs"
                maximized="{Boolean}true">
                <items jcr:primaryType="nt:unstructured">
                    <properties
                        jcr:primaryType="nt:unstructured"
                        jcr:title="Properties"
                        sling:resourceType="granite/ui/components/coral/foundation/container"
                        margin="{Boolean}true">
                        <items jcr:primaryType="nt:unstructured">
                            <columns
                                jcr:primaryType="nt:unstructured"
                                sling:resourceType="granite/ui/components/coral/foundation/fixedcolumns"
                                margin="{Boolean}true">
                                <items jcr:primaryType="nt:unstructured">
                                    <column
                                        jcr:primaryType="nt:unstructured"
                                        sling:resourceType="granite/ui/components/coral/foundation/container">
                                        <items jcr:primaryType="nt:unstructured">
                                            <firstName
                                                    jcr:primaryType="nt:unstructured"
                                                    sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
                                                    fieldLabel="First Name"
                                                    fieldDescription="Please Enter First Name"
                                                    name="./firstName"/>
                                            <lastName
                                                    jcr:primaryType="nt:unstructured"
                                                    sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
                                                    fieldLabel="Last Name"
                                                    fieldDescription="Please Enter Last Name"
                                                    name="./lastName"/>
                                            <designation
                                                    jcr:primaryType="nt:unstructured"
                                                    sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
                                                    fieldLabel="User Designation"
                                                    name="./designation"/>
                                            <contact
                                                    jcr:primaryType="nt:unstructured"
                                                    sling:resourceType="granite/ui/components/coral/foundation/form/numberfield"
                                                    fieldLabel="User Contact"
                                                    name="./contact"/>
                                            <assetPath
                                                    jcr:primaryType="nt:unstructured"
                                                    sling:resourceType="granite/ui/components/coral/foundation/form/pathfield"
                                                    fieldLabel="Asset Folder"
                                                    name="./assetPath"/>
                                        </items>
                                    </column>
                                </items>
                            </columns>
                        </items>
                    </properties>
                </items>
            </tabs>
        </items>
    </content>
</jcr:root>
```

- Once Xml is added You need to Author Component to check the field in the component.

    ![com](./Images/component/component18.png)

- Once dialog is ready, Now we need to write a sling model in Java.

- This is interface named as “CustomComponent.java

```java
package com.debug.code.core.models;

public interface CustomComponent {
    String getFirstName();
    String getLastName();
    String getDesignation();
    String getContact();
    String getAssetPath();
}

```
- This is Class named as “CustomComponentImpl.java

```java
package com.debug.code.core.models.impl;

import org.apache.sling.api.SlingHttpServletRequest;
import org.apache.sling.models.annotations.DefaultInjectionStrategy;
import org.apache.sling.models.annotations.Model;
import com.debug.code.core.models.CustomComponent;
import javax.annotation.Resource;
import javax.inject.Inject;

@Model(adaptables = Resource.class, adapters = CustomComponent.class,
        defaultInjectionStrategy = DefaultInjectionStrategy.OPTIONAL
)
public class CustomComponentImpl implements CustomComponent{

    @Inject
    String firstName;

    @Inject
    String lastName;

    @Inject
    String designation;

    @Inject
    String contact;

    @Inject
    String assetPath;


    @Override
    public String getFirstName() {
        return firstName;
    }

    @Override
    public String getLastName() {
        return lastName;
    }

    @Override
    public String getDesignation() {
        return designation;
    }

    @Override
    public String getContact() {
        return contact;
    }

    @Override
    public String getAssetPath() {
        return assetPath;
    }
}

```
- Now lets map this sling model in HTL/Sightly.

- we should Use ‘data-sly-use” for fetching the sling model in our HTL file.

```html
<sly data-sly-use.customComponent="com.debug.code.core.models.CustomComponent">

    <div>${customComponent.firstName}</div>

	 <div>${customComponent.lastName}</div>

 	<div>${customComponent.designation}</div>

 	<div>${customComponent.contact}</div>

 	<div>${customComponent.assetPath}</div>
</sly>
```
- Let`s author the component and see how the sling model to fetch data from the authoring dialog and provide to HTL.

    ![com](./Images/component/component17.png)

- Once you author the values in the dialog that are stored inside content directory of CRXDE.

    ![com](./Images/component/componenr19.png)

- and here is the output of this component.

    ![com](./Images/component/component20.png)

- In this way you can create a sling model.


### Sling Model Work Flow

![com](./Images/component/napkin-selection%20(5).png)
       
- You have authored component in this page - http://localhost:4502/content/aem-debugcode/us/en/test-page.html?wcmmode=disabled

- When this URL is accessed, AEM resolves it by checking the underlying CRX/DE path - 
/content/aem-debugcode/us/en/test-page/jcr:content/root/container/customcomponent

- AEM will check the sling:resourceType property of the customcomponent node to determine the corresponding component.

     ![com](./Images/component/component21.png)

- This property points to a path under /apps - /apps/aem-debugcode/components/customComponent

- Inside the resolved component folder, AEM looks for the HTL/Sightly file (e.g., customComponent.html) for renderin

- The HTL script references a Sling Model class using the data-sly-use attribute


```html
<sly data-sly-use.customComponent="com.debug.code.core.models.CustomComponent">
```

- AEM binds this HTL file to the Sling Model (CustomComponent class) specified by the fully qualified Java class name (com.debug.code.core.models.CustomComponent)

- The referenced Sling Model class is instantiated and its methods are executed to provide data or logic for the HTL script.

- The returned data is then rendered as part of the final HTML output

### Best Practices for Writing Sling Models in AEM

> **Note :**  Avoid Using Both Resource and SlingHttpServletRequest Together as Adaptables in a Sling Model

Ambiguity in Adaptation
- When a Sling Model specifies both Resource and SlingHttpServletRequest as adaptables, the framework may struggle to determine the context, leading to potential issues
    
    - If the model is adapted from a Resource, it will look for properties within the resource.
    
    - If adapted from SlingHttpServletRequest, it will pull in attributes, request parameters, or other injected values associated with the HTTP request.

```Java
@Model(adaptables = {Resource.class, SlingHttpServletRequest.class})
public class Button {

    @ValueMapValue
    private String property;
}

```
- Having these two adaptables can result in inconsistent behavior, as the context for instantiating the model may not be clear.

- Separation of Concerns
    
    - **Resource**: Primarily used for accessing and working with content stored in the JCR repository, making it the best choice when you need to work with properties or children of resources.

    - **SlingHttpServletRequest**: Represents the HTTP request context, providing access to request-specific data like parameters, attributes, and objects like currentPage and currentDesign.

- Combining these in a single model can create confusion because it merges the content layer (JCR) with the request layer (HTTP), making it harder to maintain a clean separation of concerns.

- Testing Challenges

    - When both adaptables are used, unit testing becomes more complex, as you need to mock both the Resource and SlingHttpServletRequest contexts. This adds extra overhead and potential for test failures.

### Best Approach for Practices

- Choose the Right Adaptable for Your Model

- Use Resource When

    - Your model is focused on content stored in the JCR.
    - You only need to access properties or children of a resource.

```java
@Model(adaptables = Resource.class)
public class Button {
    @ValueMapValue
    private String title;
}

```

- Use SlingHttpServletRequest When

    - Your model depends on request-specific data, such as parameters, request attributes, or objects like currentPage and currentResource.

```java
@Model(adaptables = SlingHttpServletRequest.class)
public class Button {

    @Inject
    private Page currentPage;

    @Inject
    private String parameter;
}

```

### How to Handle Both Scenarios?

- If you need to work with both Resource and SlingHttpServletRequest data, it’s a good practice to adapt your model from the SlingHttpServletRequest and retrieve the Resource within the model itself. This way, you avoid the complications of dual adaptables.

```java
@Model(adaptables = SlingHttpServletRequest.class)
public class Button {

    @Inject
    private Resource resource;

    @Inject
    private String requestParam;

    public String getTitle() {
        return resource.getValueMap().get("title", String.class);
    }

    public String getRequestParam() {
        return requestParam;
    }
}

```
- In this example, resource is injected from the SlingHttpServletRequest, avoiding the complexity of adapting from multiple sources.

### Prefer Specific Annotations Over @Inject

- Although @Inject is a general-purpose annotation that can inject properties or objects from various contexts, using more specific annotations like @ValueMapValue, @ChildResource, @ScriptVariable, or @OSGiService is recommended. These annotations improve clarity, maintainability, and debugging.

### Benefits of Using Specific Annotations
- @ValueMapValue: Makes it clear that the property is coming from the ValueMap of the resource.
- @OSGiService: Indicates that the value is being injected from the OSGi service registry.