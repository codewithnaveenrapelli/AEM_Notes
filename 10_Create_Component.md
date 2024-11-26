

### Objective

- After reading this Article, You should have an Understanding of 
    - [What is a component in AEM ?](#what-is-a-component-in-aem)
        - [How to Create a Component without sling model ?](#how-to-create-a-component)
        - [Listeners](#listeners)
    

### What is a component in AEM
- AEM component is a block of code or a specific functionality to show on the web page.

### How to Create a Component 

- Open CRXDE and move to your project under /apps

    - eg: /apps/AEM-DeveloperResource/components


![Folder](./Images/component/component%201.png)

- Right-Click on the folder, then Create…, then Create Node…

![Folder](./Images/component/component%202.png)

- Type your component name and in the Type field select/type “cq:Component”

![Folder](./Images/component/component%203.png)

- Once you create node if you want to add any properties to that node check below screenshot.

![Folder](./Images/component/component%205.png)

- jcr:title is to declare component title.

- componentGroup is use to group component under one category.

- Once you created cq:dialog, go to folder --> http://localhost:4502/editor.html/content/AEM-DeveloperResource/us/en/search.html add the component on the page to check weather component dialog is created or not.

![Folder](./Images/component/component%206.png)

- create other nodes inside the cq:dialog

![Folder](./Images/component/component%207.png)


- once cq:dialog is created in crx/de now we need to move this code to folder structure before moving that we need to create a package in crx/de

![Folder](./Images/component/component%208.png)
- while creating package you need follow the steps which is mention in the screenshot.
    * packageName : button
    * version : 1.0 (optional)
    * Group : AEM-DeveloperResource( optional)

![Folder](./Images/component/component%209.png)

- Now we need to select the filter option and select the path of the component which we created under crx/de.

![Folder](./Images/component/component%2010.png)

- Once you select the filter option just click on done and save button --> click on build so it will create package once created just download the package.

- Go to Download folder and Just Unzip the folder and go inside the folder - 

    - C:\Users\dell\Downloads\button-1.0\jcr_root\apps\AEM-DeveloperResource\components

- copy the button component and add in your folder structure. 

    - C:\AEM\AEM-DeveloperResource\ui.apps\src\main\content\jcr_root\apps\AEM-DeveloperResource\components

- inside _cq_dialog folder you will find .content.xml file and paste below component.

Here is the cq:dialog --> content.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<jcr:root xmlns:jcr="http://www.jcp.org/jcr/1.0" xmlns:nt="http://www.jcp.org/jcr/nt/1.0" xmlns:cq="http://www.day.com/jcr/cq/1.0" xmlns:sling="http://sling.apache.org/jcr/sling/1.0"
    jcr:primaryType="nt:unstructured"
    jcr:title="Button"
    sling:resourceType="cq/gui/components/authoring/dialog">
    <content
        jcr:primaryType="nt:unstructured"
        sling:resourceType="granite/ui/components/coral/foundation/fixedcolumns">
        <items jcr:primaryType="nt:unstructured">
            <content
                jcr:primaryType="nt:unstructured"
                sling:resourceType="granite/ui/components/coral/foundation/container">
                <items jcr:primaryType="nt:unstructured">
                    <buttonText
                        jcr:primaryType="nt:unstructured"
                        sling:resourceType="granite/ui/components/coral/foundation/form/textfield"
                        fieldLabel="Button Text"
                        name="./buttonText"/>
                    <buttonPath
                        jcr:primaryType="nt:unstructured"
                        sling:resourceType="granite/ui/components/coral/foundation/form/pathfield"
                        fieldLabel="Button Path"
                        name="./buttonPath"/>
                </items>
            </content>
        </items>
    </content>
</jcr:root>
```

- Create button.html file having same name we have given to folder as button and add below code inside button.html.

![Folder](./Images/component/component%2012.png)

```html
<sly data-sly-use.placeholderTemplate="core/wcm/components/commons/v1/templates.html">
    <div>Button Text: ${properties.buttonText} </div>
    <div>Button Path: ${properties.buttonPath} </div>
</sly>

<sly data-sly-call="${placeholderTemplate.placeholder @ isEmpty=!hasContent}"></sly>
```
- Access current component or node properties with the help of out of the box properties object.

- Create _cq_editConfig.xml file to declare or allow actions(allow to edit, copy, move, delete, etc.) and listeners to enhance component experience.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<jcr:root xmlns:cq="http://www.day.com/jcr/cq/1.0" xmlns:jcr="http://www.jcp.org/jcr/1.0" xmlns:nt="http://www.jcp.org/jcr/nt/1.0"
  cq:actions="[DELETE,INSERT,COPYMOVE,EDITANNOTATE]"
  jcr:primaryType="cq:EditConfig"
  cq:disableTargeting="{Boolean}true">
    <cq:listeners
        jcr:primaryType="nt:unstructured"
        afterchildinsert="REFRESH_SELF"
        afterdelete="REFRESH_SELF"
        afteredit="REFRESH_SELF"
        afterinsert="REFRESH_SELF"/>
</jcr:root>
```

- cq:disableTargeting is to disable targeting for profile component.

- cq:dialogMode as floating to show dialog on top of the component and can be move around.

### Listeners
- cq:listeners tag allows us to refresh component, page or parent component after authoring as soon as we click on ok.

    -  REFRESH_SELF listener will refresh current component after edit, delete, inserted, etc.

    -  REFRESH_PARENT listener will refresh parent component after edit, delete, inserted, etc.

    -  REFRESH_PAGE listener will refresh current page after edit, delete, inserted, etc.

    - REFRESH_INSERTED listener will refresh current component as soon as insert in page.

- Go to project root folder and run below command to deploy code on AEM instance.

    - **mvn clean install -PautoInstallPackage -DskipTests=true**

- Verify below highlighted structure after build in crx de http://localhost:4502/crx/de/index.jsp

![Folder](./Images/component/component%2012.png)


- Create page, drag and drop profile component on the page. Click on Drag components here option as shown below
Select button component option as highlighted below:


![Folder](./Images/component/Screenshot%202024-04-28%20174910.png)

- Select button component and click on below highlighted button to author buttoncomponent

![Folder](./Images/component/component%2013.png)

- Author button component, fill buttonText and buttonPath. Click on done button..

![Folder](./Images/component/component%2014.png)

- To see where the authored values stored, Go to [CRX/DE](http://localhost:4502/crx/de/index.jsp) and /content/<your-project-folder>/us/en/<page> you will find button component.

![Folder](./Images/component/component%2015.png)













