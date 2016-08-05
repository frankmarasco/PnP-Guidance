# Sandbox solution transformation guidance 
Transform or convert your code-based sandbox solutions to the SharePoint add-in model. Learn about the options and strategies on converting existing code-based functionalities to SharePoint add-in model or alternative solutions.

_**Applies to:** add-ins for SharePoint | SharePoint 2013 | SharePoint Online_

Code-behind sandbox solutions [were deprecated](https://blogs.msdn.microsoft.com/sharepointdev/2014/01/14/deprecation-of-custom-code-in-sandboxed-solutions/) back in 2014 and SharePoint online has started the process to completely remove this capability. Code-behind sandox solutions are also deprecated in SharePoint 2013 and in SharePoint 2016.

Transforming your sandbox solutions to the SharePoint add-in model involves analyzing your existing extensions, designing and developing your new add-in(s) for SharePoint, and then testing and deploying your add-in in your production environment. 

## What is a code-based sandbox solution in SharePoint
<a name="sectionSection0"> </a>
Sandbox solutions are customization packages, which can be used to deploy customizations to SharePoint in site collection level. If sandbox solution contains code, it has been executed in special isolated process with limited set of APIs to access SharePoint services and content.

There are two types of Sandbox solutions
- Code-based sandbox solutions, which contain a custom assembly in the package
- Declarative sandbox solutions, which only contain xml based configurations and related assets 

Declarative (xml based) sandbox solutions can be further divided to following types based on their use case.
- Site template – Generated using the “Save site as a template” functionality from existing sites
- Design package – Generated using Design Manager from publishing site

Code-based sandbox solutions can be further divided to following types based on the use cases. 
- Declarative sandbox solution with empty assembly
- Sandbox solution containing InfoPath form with code 
- Code-based sandbox solutions with customizations like web parts, event receivers and/or feature receivers
- Sandbox solutions with custom workflow action

When you are planning to move away from the sandbox solutions, you should be evaluating the functional and business requirements of specific solution and conclude the future design direction based on those. 


## Steps to perform transformation
<a name="sectionSection1"> </a>

When you transform your sandbox solutions to the SharePoint add-in model, you want to ensure that the impact on your users is minimal. Carefully analyze your current sandbox solutions, and then design your new add-in for SharePoint to meet the needs of your organization. We recommend the following process to ensure a successful transformation.


1. Readiness. Learn about:
    
	- The SharePoint add-in model, different kinds of add-ins, and hosting options. For more information, see  [Overview of add-ins for SharePoint](https://msdn.microsoft.com/library/office/fp179930.aspx) and [SharePoint Patterns and Practices guidance](https://msdn.microsoft.com/en-us/pnp_articles/office-365-development-patterns-and-practices-solution-guidance).

2. Solution assessment. Analyze the functional and business requirements by: 
    
	- Identifying deployed sandbox solutions in your current environment. 
	
	- You can use [specific sandbox solution inventory script](https://github.com/OfficeDev/PnP-Tools/tree/master/Scripts/SharePoint.Sandbox.ListSolutionsFromTenant) provided by the PnP initiative for getting list of installed sandbox solutions in your tenant. Script also indicates if sandbox solution contains an assembly or not.
    
	- Reviewing requirements with your users. Consider asking your users to demonstrate how they use the existing sandbox solutions to perform their daily work.
    
	- Identifying, documenting, and designing new functionality to include in the new add-in for SharePoint. Consider reviewing your list of new feature requests from your users for additional ideas.
    
	- Identifying unused features, and agreeing with your users to omit this functionality from the new add-in for SharePoint. 
    
	- For each solution, determining whether to replace it with an add-in for SharePoint or implement that either using with some out of the box capabilities or using some alternative solution.
    
3.  Solution planning. Design the new application using the SharePoint add-in model based on:
    
	- The requirements gathered in step 2.
    
	- Your analysis of the existing code. During your code analysis, consider identifying portions of the code that can be dropped (for example, the code is no longer being used, or the requirements have changed).
    
4. Develop and test the SharePoint add-in model version of your application. 
    
5. Deploy your new add-in. 

## Replacing sandbox solution customizations
<a name="sectionSection2"> </a>

Here's typical customizations which are included in the sandbox solutions and potential transformation options. We are looking on adding further information for each of the customization types, so that you can will have real-world examples on the transformation options. 

|**Customization**|**Transformation options**|
|:-----|:-----|
|Declarative solution with empty assembly|You can control assembly creation from Visual Studio solution project properties. See following KB article for detais - [Remove assembly reference from your Sandbox solution created in Visual Studio](https://support.microsoft.com/en-us/kb/3183084).|
|InfoPath form with code|<p>If you have published an InfoPath form from the InfoPath client which contains code, it’s actually published to the SharePoint as a sandbox solution. This means that the form code-behind is actually executed by the sandbox engine in SharePoint.</p> <p>Moving away from the code-behind InfoPath forms is dependent on the actual business use case. There are multiple different options from generating custom UI as add-in or to utilize other form techniques.</p><p>See more details on the options from [InfoPath transformation guidance](https://github.com/OfficeDev/PnP-Transformation/tree/master/InfoPath) from PnP initiative</p>|
|Web Part|<p>Web parts are typically converted either to add-in parts or they are implemented with fully client side technologies by using so called JavaScript embed pattern. </p><p>See following resources for additional information <lu><li>[Customize your SharePoint site UI using JavaScript](https://msdn.microsoft.com/en-us/pnp_articles/customize-your-sharepoint-site-ui-by-using-javascript)</li><li>[Create add-in parts to install with your SharePoint Add-in](https://msdn.microsoft.com/en-us/library/office/fp179921.aspx)</li><li>[How to update your SharePoint pages via the embedding of JavaScript](https://channel9.msdn.com/blogs/OfficeDevPnP/JavaScript-embedding-demo)</li><li>[Cross site collection navigation](https://channel9.msdn.com/blogs/OfficeDevPnP/Cross-site-collection-navigation)</li></lu></p>|
|Visual Web Part|<p>Visual web parts are transformed in similar ways as normal web parts. User control used in visual web part will need to be also replaced, since in sandbox solution cases, it's included inside of the assembly.</p>|
|Event Receiver|<p>Event receivers can in many cases be replaced with the remote event receiver implementation. Remote event receivers do however need to hosted in some platform, typically on specific provider hosted add-in.</p><p>See following resources for additional information <lu><li>[Use remote event receivers in SharePoint](https://msdn.microsoft.com/en-us/pnp_articles/use-remote-event-receivers-in-sharepoint)</li><li>[How to use remote event receivers for your SharePoint add-ins](https://channel9.msdn.com/blogs/OfficeDevPnP/How-to-use-remote-event-receivers-for-your-SharePoint-add-ins)</li></lu></p>|
|Feature Receiver|<p>Are typically replaced with a remote API based operation, like using CSOM or REST for applying the needed customization or configuration to site level. If needed API is missing from the remote APIs (CSOM/REST), report this gap using [SharePoint UserVoice](https://sharepoint.uservoice.com/).</p><p>Feature receivers are used for example to set a custom master page or theme to site, when they are activated. These kind of operations can be easily replaced with remote code based solutions or using [PnP PowerShell](https://github.com/OfficeDev/PnP-PowerShell/blob/master/README.md), which provides easy commands for controlling site configuration.</p>|
|Custom workflow action|<p>Typical code migration path for these kind of customizations is to start using either SharePoint 2013 workflows, move using alternative solutions, like Microsoft Flow or using third party solutions.</p>|

We are looking for adding specific articles on the transformation techniques for specific technical scenarios. 

## Removing your sandbox code  from your site
<a name="sectionSection3"> </a>
When you deactivate your existing sandbox solution from sites, any assets or files deployed using declarative options will not be removed. If you have used sandbox solutions to introduce new code-behind web parts, those functionalities will be disabled from the sites, but pages are still rendering normally, so there's no direct end user impact when solution is deactivated.

## Additional resources
<a name="bk_addresources"> </a>

-  [Removing Code-Based Sandbox Solutions in SharePoint Online](http://dev.office.com/blogs/removing-code-based-sandbox-solutions-in-sharepoint-online)

-  [Remove assembly reference from your Sandbox solution created in Visual Studio](https://support.microsoft.com/en-us/kb/3183084)

-  [Office 365 development patterns and practices solution guidance](Office-365-development-patterns-and-practices-solution-guidance.md)
    
-  [Build add-ins for SharePoint](https://msdn.microsoft.com/library/jj163230.aspx)