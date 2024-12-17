![Sling Model](./Titleimages/Slingmodelannotation.png)

### Objective

- After reading this Article, You should have an Understanding of

    - [What is Annotation in Sling Model?](#what-is-annotation-in-sling-model)
    - [Types of Annotations](#types-of-annotations)
        - [@Inject](#inject)
        - [@ValueMapValue](#valuemapvalue)
        - [Comparison @@Inject and @ValueMapValue](#comparison-inject-and-valuemapvalue)
        - [@Named](#named)
        - [@default](#default)
        - [@Self](#self)
        - [@Via](#via)
        - [@PostConstruct](#postconstruct)
        - [@ChildResource](#childresource)
        - [@OSGiService](#osgiservice)
        - [@SlingObject](#slingobject)
        - [@ScriptVariable](#scriptvariable)
        - [@ResourcePath](#resourcepath)
        - [@RequestAttribute](#requestattribute)


### What is Annotation in Sling Model?

- Annotations in Sling Models are used to map AEM content to Java objects.

### Types of Annotations

###  @Inject

- @Inject is a generic annotation that can inject properties or objects from various contexts

- @Inject annotation does not offer type safety for the injected dependency.

- By default, @Inject fields are optional. If the value is not available, the field will be null unless explicitly marked as required.

- @Inject is used for dependency injection

```java
@Model(adaptables = Resource.class, adapters = CustomComponent.class,
        defaultInjectionStrategy = DefaultInjectionStrategy.OPTIONAL
)
public class CustomComponentImpl implements CustomComponent{

    @Inject
    String firstName;
```  

### @ValueMapValue

- It is used to inject properties directly from the resource. It is useful when you need to map resource properties to model fields directly.

- Supports default values if the property is not present in the ValueMap.

- Automatically converts the property to the fields type if necessary.

- @ValueMapValue annotation provides type safety since you can specify the type of the Java field that is being mapped to the JCR property.

### Comparison @@Inject and @ValueMapValue

- Both the @Inject and @ValueMapValue annotations are used in AEM Sling Models to map content in the AEM JCR (Java Content Repository) to Java fields in the Sling Model

| Comparison   | @Inject | @ValueMapValue |
| -------- | ----------- | -----------|
| Dependency vs direct property mapping |  @Inject is used to inject OSGi services and other dependencies into the Sling Model | @ValueMapValue is used to map JCR properties directly to Java fields in the Sling Model.
| Usage context | @Inject annotation is typically used to inject services and other dependencies that are not directly related to the content being mapped, but are needed for the Sling Model to function properly | @ValueMapValue annotation is used specifically to map content properties to Java fields in the Sling Model |
| Type safety |   @Inject annotation does not provide type safety for the injected dependency, which means that you need to be careful to ensure that the correct type of object is injected into the Sling Model |  @ValueMapValue annotation provides type safety, since you can specify the type of the Java field that is being mapped to the JCR property. |    

### @Named

- This is particularly useful when the field name in the model does not match the property name in the source, or when you want to inject a specific value from a multi-field or a map.

- Example: we want to fetch “jcr:lastModifiedBy” from JCR for the specific component

- we can use @named annotation this way:
```Java
@ValueMapValue
@Named("jcr:lastModifiedBy")
private String createdBy;

public String getCreatedBy() {
    return createdBy;
    }
```   

### @default
- Specifies a default value to use if the injection is unsuccessful..

- whe you used default value Null pointer exceptions are avoided.

```Java
@ValueMapValue
@Default(values = "Default Title") // Provides a default value for the title field
private String title;

@ValueMapValue
@Default(intValues = 0) // Provides a default value of 0 for the viewCount field
private int viewCount;
``` 

### @Self

- @Self annotation is used in Sling Models to inject the adaptable object itself into a model. This is particularly useful when you need direct access to the original resource, request, or other adaptable object from which the model is created.

- Provides direct access to the original adaptable object, enabling additional operations or property retrievals.

```java
@Self
private Resource resource; // Inject the adaptable object itself
```
- Injects the adaptable Resource object into the resource field. This allows the model to access the resource directly for additional operations or property retrievals that are not mapped to specific fields.

```Java
@Model(adaptables = SlingHttpServletRequest.class)
public class RequestModel {

    @Self
    private SlingHttpServletRequest request;

    public String getRequestPath() {
        return request.getRequestURI();
    }
}
```

```Java
@Model(adaptables = Resource.class)
public class CustomModel {

    @Self
    private Resource resource;

    public boolean hasChildNodes() {
        return resource.hasChildren();
    }
}
```

###  @Via     
- This annotation is used to specify an alternative path or mechanism to access a value.

- When the requested value may be accessible through another resource or object but is not directly available on the adaptable, this is helpful.

- @Via: Specifies the intermediary adapter through which to run the adaptables object.

- Common Use Cases for @Via

    - Accessing a Child Resource: When the value is available in a child resource rather than the current resource.

    - Accessing a Parent Resource: When the value is available in a parent resource.

    - Accessing a Different Model: When the value is part of a different Sling Model or object.

-  Enhances the flexibility of Sling Models by allowing access to values not directly available on the adaptable.

```java
JCR Structure-------------------------------------------------

/content/mysite/jcr:content
  + myComponent
    + details
      - title: "Hello World"
      - description: "This is a description."
```
```java
@Model(
    adaptables = Resource.class,
    defaultInjectionStrategy = DefaultInjectionStrategy.OPTIONAL
)
public class MyComponentModel {

    @ValueMapValue
    @Via("details") // Specifies that the value should be taken from the "details" child resource
    private String title;

    @ValueMapValue
    @Via("details")
    private String description;

}
```
Example 2:

```Java
import org.apache.sling.api.resource.Resource;
import org.apache.sling.models.annotations.DefaultInjectionStrategy;
import org.apache.sling.models.annotations.Model;
import org.apache.sling.models.annotations.injectorspecific.Self;
import org.apache.sling.models.annotations.injectorspecific.ValueMapValue;
import org.apache.sling.models.annotations.Via;

@Model(
    adaptables = Resource.class,
    defaultInjectionStrategy = DefaultInjectionStrategy.OPTIONAL
)
public class ParentModel {

    @ValueMapValue
    private String title;

    public String getTitle() {
        return title;
    }
}

@Model(
    adaptables = Resource.class,
    defaultInjectionStrategy = DefaultInjectionStrategy.OPTIONAL
)
public class ChildModel {

    @Self
    @Via("parentResource") // Accessing the ParentModel through the parentResource
    private ParentModel parentModel;

    public String getTitleFromParent() {
        return parentModel.getTitle();
    }
}
```
### @PostConstruct

- @PostConstruct annotation is used to mark a method in a Sling Model that should be executed after the model's dependencies have been injected and the model has been fully initialized.

- The method annotated @PostConstruct must be void and take no arguments.

```java
import org.apache.sling.api.resource.Resource;
import org.apache.sling.models.annotations.DefaultInjectionStrategy;
import org.apache.sling.models.annotations.Model;
import org.apache.sling.models.annotations.injectorspecific.ValueMapValue;

import javax.annotation.PostConstruct;

@Model(
    adaptables = Resource.class,
    defaultInjectionStrategy = DefaultInjectionStrategy.OPTIONAL
)
public class ValidatedModel {

    @ValueMapValue
    private String title;

    @ValueMapValue
    private String description;

    @PostConstruct
    protected void init() {
        // Validate and set default values
        if (title == null || title.isEmpty()) {
            title = "Default Title";
        }
        if (description == null || description.isEmpty()) {
            description = "Default Description";
        }
    }

    public String getTitle() {
        return title;
    }

    public String getDescription() {
        return description;
    }
}
```
- The init method checks if title and description are null or empty, and if so, assign default values.

### @OSGiService
- @OSGiService annotation is used to inject OSGi services directly into a Sling Model.

```Java
@Model(adaptables = SlingHttpServletRequest.class,
        defaultInjectionStrategy = DefaultInjectionStrategy.OPTIONAL )
public class StudentConfigurationModelImpl {
    
    @OSGiService
    StudentConfigurationMethods studentConfigurationMethods;


    public String getStudentName() {
        return studentConfigurationMethods.getStudentName();
    }


    public int getRollNumber() {
        return studentConfigurationMethods.getRollNumber();
        }
}
```        
### @SlingObject

- @SlingObject annotation is used to inject common Sling objects directly into the model.

- Global Objects with @SlingObject
    - Resource: Represents the resource being adopted.

    - ResourceResolver: Provides methods to resolve resources.

    - SlingHttpServletRequest: Represents the current request.

    - SlingHttpServletResponse: Represents the current response.

    - SlingScriptHelper: Provides access to various scripting utilities and services.

```java
    @SlingObject
    private Resource resource; // Injects the current Resource

    @SlingObject
    private ResourceResolver resourceResolver; // Injects the ResourceResolver

    @SlingObject
    private SlingScriptHelper scriptHelper; // Injects the SlingScriptHelper
```

### @ScriptVariable

-  @ScriptVariable annotation in AEM is used in Sling Models to inject values from the script context, such as the current page, resource, request, etc.

-  Global Objects with @ScriptVariable Injections

- Page: Represents the current page.

    - Resource: Represents the current resource.

    - SlingHttpServletRequest: Represents the current request.

    - SlingHttpServletResponse: Represents the current response.
    - ValueMap: Represents the properties of the current resource.
    - SlingScriptHelper: Provides access to various AEM services.
    - ResourceResolver: Provides access to resources in the JCR.
```java
import com.day.cq.wcm.api.Page;
import org.apache.sling.api.resource.Resource;
import org.apache.sling.api.resource.ResourceResolver;
import org.apache.sling.api.scripting.SlingScriptHelper;
import org.apache.sling.api.scripting.SlingBindings;
import org.apache.sling.models.annotations.DefaultInjectionStrategy;
import org.apache.sling.models.annotations.Model;
import org.apache.sling.models.annotations.ScriptVariable;
import org.apache.sling.models.annotations.injectorspecific.ValueMapValue;

@Model(
    adaptables = Resource.class,
    defaultInjectionStrategy = DefaultInjectionStrategy.OPTIONAL
)
public class AdvancedExampleModel {

    @ValueMapValue
    private String title;

    @ScriptVariable
    private Page currentPage;

    @ScriptVariable
    private Resource resource;

    @ScriptVariable
    private ResourceResolver resourceResolver;

    @ScriptVariable
    private SlingScriptHelper sling;

    public String getTitle() {
        return title;
    }

    public Page getCurrentPage() {
        return currentPage;
    }

    public Resource getResource() {
        return resource;
    }

    public ResourceResolver getResourceResolver() {
        return resourceResolver;
    }

    public SlingScriptHelper getSling() {
        return sling;
    }
}
```
-  we inject several objects from the script context, including the current Page, Resource, ResourceResolver, and SlingScriptHelper.

### @ChildResource
- A child resource of the current resource can be immediately injected into the model using the @ChildResource annotation

- Allows specifying the path relative to the current resource to identify the child resource.

- Can inject single resources or collections (e.g., lists of resources).

```java
JCR Structure-------------------------------------------------

/content/mysite/jcr:content
  + myComponent
    - title: "Main Title"
    + details
      - subtitle: "Sub Title"
      - description: "Detailed description."
    + items
      + item1
        - name: "Item 1"
      + item2
        - name: "Item 2"

```
```java
import org.apache.sling.api.resource.Resource;
import org.apache.sling.models.annotations.DefaultInjectionStrategy;
import org.apache.sling.models.annotations.Model;
import org.apache.sling.models.annotations.injectorspecific.ChildResource;
import org.apache.sling.models.annotations.injectorspecific.ValueMapValue;

import java.util.List;

@Model(
    adaptables = Resource.class,
    defaultInjectionStrategy = DefaultInjectionStrategy.OPTIONAL
)
public class ExampleModel {

    @ValueMapValue
    private String title;

    @ChildResource(name = "details")
    private Resource details; // Injects the 'details' child resource

    @ChildResource(name = "items")
    private List<Resource> items; // Injects the 'items' child resources

    public String getTitle() {
        return title;
    }

    public String getSubtitle() {
        return details != null ? details.getValueMap().get("subtitle", String.class) : null;
    }

    public String getDescription() {
        return details != null ? details.getValueMap().get("description", String.class) : null;
    }

    public List<Resource> getItems() {
        return items;
    }

    public String getItemName(int index) {
        if (items != null && items.size() > index) {
            return items.get(index).getValueMap().get("name", String.class);
        }
        return null;
    }
}
```

- @ChildResource(name = "details"): Injects the details child resource.
- @ChildResource(name = "items"): Injects a list of child resources from the items node

### @ResourcePath
- This annotation is used to inject a Resource based on a given path.
- This annotation is useful when you need to work with resources that are not directly part of the current request or resource tree, allowing you to inject resources from any location in the JCR repository.

```java
JCR Structure-------------------------------------------------

/content/mysite
  + jcr:content
    - title: "Main Content"
  + referencedContent
    - title: "Referenced Content"
```
```java
import org.apache.sling.api.resource.Resource;
import org.apache.sling.api.resource.ValueMap;
import org.apache.sling.models.annotations.DefaultInjectionStrategy;
import org.apache.sling.models.annotations.Model;
import org.apache.sling.models.annotations.injectorspecific.ResourcePath;
import org.apache.sling.models.annotations.injectorspecific.ValueMapValue;

@Model(
    adaptables = Resource.class,
    defaultInjectionStrategy = DefaultInjectionStrategy.OPTIONAL
)
public class ExampleModel {

    @ValueMapValue
    private String title;

    @ResourcePath(path = "/content/mysite/referencedContent")
    private Resource referencedResource; // Injects the resource at the specified path

    public String getTitle() {
        return title;
    }

    public String getReferencedTitle() {
        if (referencedResource != null) {
            ValueMap valueMap = referencedResource.getValueMap();
            return valueMap.get("title", String.class);
        }
        return null;
    }
}
```
### @RequestAttribute
- Request attributes are data that are passed along with the HTTP request, and this annotation allows you to access these attributes directly within your model.

- This is particularly useful when you need to process or utilize data that is provided dynamically during the request.

Step 1: Set Up the Request Attribute
Servlet or Component: In your servlet or any other part of your code that handles the request, set the request attribute:
```java
request.setAttribute("myAttribute", "This is a request attribute");
```

```java
import org.apache.sling.api.SlingHttpServletRequest;
import org.apache.sling.models.annotations.DefaultInjectionStrategy;
import org.apache.sling.models.annotations.Model;
import org.apache.sling.models.annotations.injectorspecific.RequestAttribute;

@Model(
    adaptables = SlingHttpServletRequest.class,
    defaultInjectionStrategy = DefaultInjectionStrategy.OPTIONAL
)
public class ExampleModel {

    @RequestAttribute(name = "myAttribute")
    private String myAttribute; // Injects the request attribute

    public String getMyAttribute() {
        return myAttribute;
    }
}
```

- @RequestAttribute(name = "myAttribute"): Injects the request attribute named myAttribute into the Sling Model.