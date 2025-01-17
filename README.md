# Power Platform and Neptune API Factory
This guide outlines the steps to get started with a simple Power App that connects to an SAP system using Neptunes API Factory. In the end the Power App will be able to look up customers in the SAP system using a Search Help function. The scenario was also shown in a joint webinar with Neptune Software, [How Microsoft Power Platform customers can connect to SAP backends](https://info.neptune-software.com/acton/media/23527/microsoft-power-plaftorm-and-neptune-dxp)

Power Platform comes with several out of the box connectors that allow you to [connect to an SAP System](https://flow.microsoft.com/en-us/blog/hyperautomation-special-video-series-for-sap-based-integration-automation-with-power-automate/). 
- The Power Automate Desktop RPA automation is a good starting point to connect via the SAP UI to your SAP system: [RPA Playbook for SAP GUI Automation with Power Automate: API flows, UI flows, and Power Automate Desktop](https://flow.microsoft.com/en-au/blog/rpa-playbook-for-sap-gui-automation-with-power-automate-api-flows-ui-flows-and-power-automate-desktop/)
- The SAP ERP Connector allows you to connect to BAPI / RFCs in your SAP system using the on-prem data Gateway and the .NET Connector: [General availability of the SAP ERP connector](https://powerapps.microsoft.com/en-us/blog/general-availability-of-the-sap-erp-connector/)
- The OData Connector (currently in private preview) enables you to connect to OData service exposed by the SAP system 

The approach outlined here uses the Neptune API Factory -- an installation that customers need to do in the SAP system -- to expose any BAPI, Function Modules, ALVs, ... via RESTful services. Using an OpenAPI / Swagger specification these REST services can easily be imported / used in the Power Platform using the Custom Connector. 

All the steps outlined below can be done using a Trial account both on the Neptune and the Microsoft side. 


## Sign up to the Netpune Developer Trial
Go to https://www.neptune-software.com/free-trial/ scroll down and click on "Download Free Trial"
![Download](Images/01-DownloadNeptune.jpg)

Click on Register
![Download](Images/02-Register.jpg)

and fill in the required information. Select "Neptune DXP SAP edition [Planet 8]" and click on Register
![Download](Images/03-Register-2.jpg)

You should get an Email with the required information to sign in to the Netpune Software Account Portal. We will log onto the portal later on in step xx

## Sign up to the Power Platform Developer Trial
Go to https://powerapps.microsoft.com/en-us/developerplan/ and sign up for the "Power Apps Developer Plan"
![Download](Images/04-PowerAppDevPlan.jpg)

Fill in the required information. 
![Download](Images/05-PP-Information.jpg)

**Note:** If you have problems signing in with your work / school account, go to https://developer.microsoft.com/en-us/microsoft-365/dev-program and sign up for the Microsoft 365 Developer Program first. 

## Download and install the required files from Neptune
After registration to the Neptune Developer Trial you will get credentials and a link to login to the Neptune Portal. From there Click on Product Download -> Download Now
![Neptune Portal](Images/06-DownloadNeptune.jpg)

From the Neptune DX Platform - SAP Edition, select *Long Term Support Release* -> *DXP 21 (2021)* and download the ZIP file. 
![Download DXP](Images/07-DownloadDXP21.jpg)

Now follow the instructions outlined in the *Neptune-DXP SAP Edition Installation-Guide.pdf* and install the transport files. 

![](Images/08-STMS-Import.jpg) 

Also make sure to active the Neptune servies in SICF 
![](Images/09-SICF.jpg)

After activation run the  /NEPTUNE/INSTALLATION_CHECK from SE38
![](Images/09a-InstallationCheck.jpg)



## Start Neptune 
From the SAP GUI and start transaction  **/NEPTUNE/COCKPIT** 

A browser will open with the Netpune DX Platofrm.  Select the *API Factory*
![Download](Images/10-OpenAPIFactory.jpg)

Select in the ObjectTypeName column select the /NEPTUNE/CL_DR_LIB_DDIC_SHLP_X object:
![Download](Images/11-SelectSearchHelp-X.jpg)

Click on Policy and click on Edit
![Download](Images/12-PolicyEdit.jpg)

Now switch to Unrestricted and click on Save. 
**Note:** This is only for our test environment. For a real implementation you would not change this to unrestricted!
![Download](Images/13-UnrestrictedSave.jpg)

Make sure to select OpenAPI 2.0 in the drop down (Power Platform currently does not support OpenAPI 3.0) and enter DEBIA for the Searchhelp value  
![Download](Images/14-SelectDEBIA.jpg)

Click on the *Swagger UI* and *Explore* to see the dynamically created specification
![Download](Images/15-SwaggerUI.jpg)

From here you can already test the function. 
![Download](Images/16-Testing.jpg)

**Note:** In my setup I am using an Azure API Management to actually expose the REST API to the internet. This allows me to protect the SAP system, but also to quickly implement features like Single Sign-On from Azure Active Directory to the SAP sytem (see also 
* [.NET speaks OData too – how to implement Azure App Service with SAP Gateway](https://blogs.sap.com/2021/08/12/.net-speaks-odata-too-how-to-implement-azure-app-service-with-sap-odata-gateway/) from **Martin Pankraz** and 
* [Principal propagation in a multi-cloud solution between Microsoft Azure and SAP Business Technology Platform (BTP), Part IV: SSO with a Power Virtual Agents Chatbot and On-Premises Data Gateway](https://blogs.sap.com/2021/04/13/principal-propagation-in-a-multi-cloud-solution-between-microsoft-azure-and-sap-business-technology-platform-btp-part-iv-sso-with-a-power-virtual-agent-chatbot-and-on-premises-data-gateway/) from **Martin Raepple**).

If your system already exposes this API to the internet, there is nothing else for you to do.  
![Download](Images/17-API-Management.jpg)

## Creating a Custom Connector in the Power Platform

With this we can switch over to the Power Platform, [https://us.flow.microsoft.com/en-us/[(https://us.flow.microsoft.com/en-us/). 

Under Data -> Customer Connectors click on "+ New custom connector" and select "Import an OpenAPI from URL". 
![Download](Images/19-PowerPlatformImport.jpg)


Enter the URL from the Swagger.ui, e.g. 

https://*yourSAPSystem*:*port*/neptune/api/dynamic/neptune/cl_dr_lib_ddic_shlp_x/SH/DEBIA/swagger.json?oas_version=2.0&sap-user=*user*&sap-password=*password*

 Make sure to replace the *user* and *password* with the username and password of your SAP system (alternatively you can also download and upload the Swagger file) and click on *Import* -> *Continue*
![Download](Images/20-ImportContinue.jpg)
  
 
Now double check the configuration. Click on *Security*,
![Download](Images/21-ConnectorWizard.jpg)
  
Definition

![Download](Images/22-PP-Wizard-Step2.jpg)

![Download](Images/23-PP-Wizard-Step3.jpg)

And finally click on "Create Connector" to create the connector the actual connector.


 Now open Power Apps, [http://make.powerapps.com/](http://make.powerapps.com/) and click on *Create* -> *Canvas app from blank*
  ![image](Images/30-PPCreateApp.jpg)
 
 Enter an App name and click on *Create*
 ![image](Images/31-PPEnterAppName.jpg)
 
 In the Apps Canvas click on the Data icon on the right, click on "Add data" Search for the Customer Connector name you previously created and select it. 
 ![image](Images/32-PPAppsCanvas.jpg)
 
 As a result you should see the new data source *SearchHelp:SH(Searchhelp)*
 ![image](Images/33-DataAdded.jpg)
 
 Now click on Insert and Drag and drop a Text Input and Button onto the canvas
 ![image](Images/34-DragElements.jpg)
 
 Search for Table, select the Data table (preview) and drag it onto the canvas as well. 
 ![image](Images/35-insertTable.jpg)
 
 Now select the button you created. Make sure that the Action for the Button is OnSelect and enter the function, 

``` ClearCollect(Customers, 'SearchHelp:SH(Searchhelp)|DEBIA-Customers(general)'.POSTexecute(800,{MAXROWS:100, MCOD1:Table({SIGN: "I",OPTION: "CP",LOW: Concatenate("*",TextInput1.Text,"*"),HIGH: ""})}).result.DATA); ```

  ![image](Images/36-ClearCollect.jpg)
 
 **Note:** 
> * ClearCollect creates a new collection that we can easily poulate and use in other items. 
> * SearchHelp:SH is the Custom Connection that we created in step xxx. 
> * The POSTexecute step is the function to lookup the customer information 
> * it requires several mandatory parameters, like SAP-Client (800), MAXROWS (a default value of 100 as an example), MCOD1 (a Table with information on what customer information we should look for)
> * TextInput1.Text is the value the user interes in the Input Field. You can find the execat property name by clicking on the Next infput field
 
 ![image](Images/37-TextInputField.jpg)
 
 Now click on the table and link the newly created Collection "Customers" to it. 
 ![image](Images/38-LinkTable.jpg)
 
 On the right hand side under Data sources -> Customers click on Fields -> Edit fields, click on "+ Add field" and select the two properties KUNNR and MCOD1. Then click on Add. 
 ![image](Images/39-ChooseFields.jpg)
 
 The result should look like this (now you can see the name of the two columns). It's time to test the Power Apps. On the upper right corner click on the "Play" button. 
 ![image](Images/40-ColumnNames.jpg)
 
 Enter some letters in the Input field and click the button. As a result you should see customer data displayed in the Power App. 
 ![image](Images/41-TryitOut.jpg)


With this the app is ready. You can save it, publish it and distribute it within the organization. Now that you have the Custom Connector it can also be used in Power Automate Flow (for example to integrate it in Teams, Excel, ...). 

 
 
 
 

 
 

  
  
