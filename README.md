# Create a simple eSignature solution on Power Apps

By Martin Boejstrup, Microsoft 1/28/2022

### Background

Sometimes you need a signature solution in Power Apps. Out of the box there is
two methods: Use the pun input control or use the 3. Part Digital signature
solution.

This solution uses a sign in request as an electronic signature, as a no/low
code using build-in features.  
![Diagram Description automatically
generated](media/790f5688ce4c074d76dd442c12b8ca0d.png)

It uses a cloud flow to call Microsoft Graph sign in request with the username
and password.

There are 3 elements:

-   Azure Ad App Registration: is used to be able to connect to Microsoft Graph.

-   Power Automate Cloud flow: is used to get the request from the app and send
    the sign in request to Microsoft Graph, and respond the result to the app.

-   Power Apps Canvas app: is used to enter the username and password and send
    the request to the cloud flow.

# Create Application Registration in Azure AD

To make the Graph call we need an application key and a secret that have access
to sign in as the user. You need a Tenant Administrator to do that.

1.  Open Azure AD: <https://aad.portal.azure.com/>

2.  Click “App Registration” and “New application registration”  
    ![Graphical user interface, application Description automatically
    generated](media/42f16be3bb23416907fbef3a80dcd991.png)

3.  Add a name and click “Create”  
    ![Graphical user interface, text, application Description automatically
    generated](media/e01f3fada52534ed9b41dbcdf89bb677.png)

4.  Note down the Application Id and tenant id– we need that in the flow  
    ![Graphical user interface, application Description automatically
    generated](media/63a8f8ce27dbb520b02a8b7d651be1f6.png)

5.  Click “Certificates & secrets” and “+ New client secret”  
    ![Graphical user interface, application, Teams Description automatically
    generated](media/e7c51a6c55bff2401dbf6e534d2f6172.png)

6.  Add a description (can be any name), set the expirations, and click “Add”  
    ![Graphical user interface, application Description automatically
    generated](media/5839af04efd296f0a553f272899d5409.png)

7.  After it is saved the value of the key will shop up – note is down, we need
    it in the cloud flow  
    ![Graphical user interface, text, application, email Description
    automatically generated](media/8261e1586670594996823187d4621b9f.png)

8.  Click “API permisions” and “+Add a permission”  
    ![Graphical user interface, text, application, email Description
    automatically generated](media/822361bb11ff4b7cb142abd3b2a5bf8b.png)

9.  Click “Microsoft Graph”  
    ![Graphical user interface, application Description automatically
    generated](media/17c2eeddc6a6c9ebc7a7027c99bff2c0.png)

10. Click “Grand admin consent”  
    ![Graphical user interface, text, application, email Description
    automatically generated](media/a2377db9b4d88e5e6f0bfa21bf718321.png)

11. Click “Yes”  
    ![Graphical user interface, text, application Description automatically
    generated](media/3c839652dc093d937e0c498c526c05b5.png)

12. Ensure the persissions  
    ![Graphical user interface, text, application, email Description
    automatically generated](media/b902e853b280096020ab71ebd577c3fa.png)

# Create cloud flow that make the Graph call

1.  Open Power Automate – <https://flow.microsoft.com>

2.  Click “+New flow” and click “Instant cloud flow”  
    ![Graphical user interface, application Description automatically
    generated](media/0bde8f50d1787b10e9c49cf07d38c1bd.png)

3.  Add a flow name and choose the “PowerApps” trigger  
    ![Graphical user interface, text, application Description automatically
    generated](media/c6d22cdecc6d1045be99a89679e4a395.png)

4.  Add a new step – variable – Initialize variable  
    ![Graphical user interface, application Description automatically
    generated](media/d3e812fdc1499f51d9bbcee5e8edb13b.png)

5.  Rename the action to “username”  
    ![](media/207a63b9a47536e9541987ba6d65f3ff.png)

6.  Change the variable name to “username”, set the type to “String” and in
    value click “Dynamic content” and click “Ask in PowerApps”  
    ![Graphical user interface, text, application, email Description
    automatically generated](media/616e6fa89d5e8fdae11a4fd1e4e4fe15.png)

7.  Add a new variable and rename the action to “password”  
    ![](media/253a53d4837334598b4b68f9da99bbcb.png)

8.  Change the variable name to “password”, set the type to “String” and in
    value click “Dynamic content” and click “Ask in PowerApps”  
    ![Graphical user interface, application Description automatically
    generated](media/f36896f3b6fe54c8021a0174bd9fd64d.png)

9.  Add a new variable and rename the action to “TenantID”  
    ![A picture containing background pattern Description automatically
    generated](media/95e9fe26d2a154d716ca71430fcb2156.png)

10. Change the variable name to “password”, set the type to “String” and in
    value add the tenant id you noted down in the previous section.![Graphical
    user interface, application Description automatically
    generated](media/47b56e145ce3b87973cd3b278edda256.png)

11. Add a new variable and rename the action to “ClientID” (application id)  
    ![](media/2beef0eaf232d6a383c02985912f1e21.png)

12. Change the variable name to “ClientID”, set the type to “String” and in
    value add the client id you noted down in the previous section.  
    ![Graphical user interface, application Description automatically
    generated](media/f04aeaa5e2090fc1994ac8c6eac1b2c4.png)

13. Add a new variable and rename the action to “ClientSecret”  
    ![](media/14b076858c7b0ca166df14b3e6775cf1.png)

14. Change the variable name to “ClientSecret”, set the type to “String” and in
    value add the client secret you noted down in the previous section.  
    ![Graphical user interface, text, application Description automatically
    generated](media/6c695a482aad6f20d602d2120a8165a9.png)

15. Add a “HTTP” action  
    ![Graphical user interface, application, email Description automatically
    generated](media/be7735bd71913e0ba5142e0aabdd888f.png)

16. Change the Method to “POST”

17. Change the URL to
    [https://login.microsoftonline.com/@{variables('TenantID')}/oauth2/token](https://login.microsoftonline.com/@%7bvariables('TenantID')%7d/oauth2/token)

18. In Headers add;  
    key: “Content-Type”, Key:“ application/x-www-form-urlencoded”

19. In Body add:  
    [grant_type=password&client_id=@{variables('ClientID')}&username=@{variables('username')}&password=@{variables('password')}&client_secret=@{variables('ClientSecret')}&resource=https://graph.microsoft.com](mailto:grant_type=password&client_id=@%7bvariables('ClientID')%7d&username=@%7bvariables('username')%7d&password=@%7bvariables('password')%7d&client_secret=@%7bvariables('ClientSecret')%7d&resource=https://graph.microsoft.com)

20. ![Graphical user interface, application Description automatically
    generated](media/d3b68950216cff6a0b0a7c536258e210.png)

21. Add a new action “Respond to PowerApp or flow”  
    ![Graphical user interface, application Description automatically
    generated](media/de01a884845121f21143ae2b305b29d9.png)

22. Add a text output “Response” and “ok”  
    ![Graphical user interface, application, Teams Description automatically
    generated](media/d4339c4f09812b4607d9efa6f87655d4.png)

23. Add a parallel branch  
    ![Graphical user interface, application Description automatically
    generated](media/be50e968f1df216652424aa9e6794d51.png)

24. Add a the action “Respond to PowerApp or flow”  
    ![Graphical user interface, application Description automatically
    generated](media/de01a884845121f21143ae2b305b29d9.png)

25. Add a text output “Response” and “error”  
    ![Graphical user interface, text, application, email Description
    automatically generated](media/4187bc7567d4f1e773522d880090e0f8.png)

26. Open the “Configure run after” on the last action  
    ![Graphical user interface, text, application Description automatically
    generated](media/534203b747051a56e58b24bbceef3310.png)

27. Add checkmark on “has failed” and “has timed out” and click “Done”  
    ![Graphical user interface, application Description automatically
    generated](media/43bf5fed269367bcecb841098316c754.png)

28. The flow is now ready – the complete flow:  
    ![Graphical user interface, application, Teams Description automatically
    generated](media/ba9bbb2cf6448d9c20eb0819a4596c87.png)

29. You can now test the flow  
    ![Graphical user interface, application Description automatically
    generated](media/ff064e59a97ba77d4a52c29db815d34e.png)

# Create the power app

1.  Open Power Apps – <https://powerapps.microsoft.com/>

    1.  Create a new canvas app

    2.  Add 2 text input controls:  
        ![Graphical user interface, application Description automatically
        generated](media/ae454f99c4200c84a13d0ecefabd5c0d.png)

    3.  Change the name, and these properties:  
        Name: txtUsername  
        Default: ""  
        HintText: "Enter username"

    4.  Change the name, and these properties  
        Name: txtPassword  
        Default: ""  
        HintText: "Enter password"  
        Mode: TextMode.Password

    5.  Add a Button and update these properties:  
        Name: brtCheck  
        Text: "Check user"

    6.  In the “OnSelect” click “Power Automate”  
        ![Graphical user interface, application, Word Description automatically
        generated](media/c6a2c0f45ae73280e0d9c7818eb12b47.png)

    7.  In the Flow panel select the “eSignature sign in” flow  
        ![Graphical user interface, application Description automatically
        generated](media/b3402e39d7d936b572dd6acf52faa0a6.png)

    8.  Update the “OnSelect” to:  
        Set(varUsercheck,
        eSignaturesignin.Run(txtUsername,txtPassword).response)  
        ![A picture containing application Description automatically
        generated](media/b351be3a6977225b0e8911eeb9edee5a.png)

    9.  Add a “Text label” control and set the “text” to: varUsercheck  
        ![Graphical user interface, application Description automatically
        generated](media/1752593452c2c9b3b0189a4d987e97c5.png)

Now you have a working sample.
