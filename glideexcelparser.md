# GlideExcelParser

&nbsp;&nbsp;&nbsp;&nbsp;Recently, we did recieve a requirement to build a catalog item for Asset managers, to auto-import asset information in the platform based on attached excel sheet. Additionally, we had to reject the request if the data is missing or does not have referenced records available yet in the system.
&nbsp;&nbsp;&nbsp;&nbsp;While exploring a bit, I found this amazing API called **GlideExcelParser** documented [here](https://developer.servicenow.com/dev.do#!/reference/api/quebec/server/GEPS-setNullToEmpty_B). If you haven't used it yet like me, Let's learn together.

## What is GlideExcelParser

&nbsp;&nbsp;&nbsp;&nbsp;`GlideExcelParser` parses `.xlsx` formatted Excel files and access file data in a script.
Although, GlideExcelParser methods can be used in both global and scoped scripts, you need to use the `sn_impex` namespace identifier to create a GlideExcelParser object.

&nbsp;&nbsp;&nbsp;&nbsp;What it means is, to creates an instance of GlideExcelParser, we need to use the following syntax:
```javascript
var parser = new sn_impex.GlideExcelParser();
```

### Creating a demo data sheet

&nbsp;&nbsp;&nbsp;&nbsp;Though, ServiceNow documentation is very clear and pretty self-explanatory (along with the awesome, easy to understand examples), what lacks for this API documentation is a demo data sheet to visualize it better. So before proceeding furthur, Let's create a demo data sheet for ourselves:

![1](./images2/2022-11-19%2010_23_47-Assets%20_%20ServiceNow.png)
![2](./images2/2022-11-19%2010_27_55-Assets%20_%20ServiceNow.png)
![3](./images2/2022-11-19%2010_29_43-Assets%20_%20ServiceNow.png)
![4](./images2/2022-11-19%2010_32_38-Assets%20_%20ServiceNow.png)
![5](./images2/2022-11-19%2010_36_34-Assets%20_%20ServiceNow.png)
![6](./images2/2022-11-19%2010_36_05-Assets%20_%20ServiceNow.png)
![7](./images2/2022-11-19%2010_39_00-Assets%20_%20ServiceNow.png)
![8](./images2/2022-11-19%2010_40_22-Assets%20_%20ServiceNow.png)
![9](./images2/2022-11-19%2010_45_04-glideexcelparser.md%20-%20You-Dont-Know-SN-APIs%20-%20Visual%20Studio%20Code.png)
![10](./images2/2022-11-19%2010_50_31-glideexcelparser.md%20-%20You-Dont-Know-SN-APIs%20-%20Visual%20Studio%20Code.png)


### Creating a fix script

&nbsp;&nbsp;&nbsp;&nbsp;It is not necessary to create a fix script for our use case. But, it is always a good idea to test if our code is working as expected. And Fix script always seems to be a better choice than Background script. Also, In order to test our APIs with the demo sheet that we did create, It needs to be in the platform as an attachment. Fix script can also solve that purpose in this case. So let's go ahead and create a fix script and attach our demo sheet to it:

![11](./images2/fix_script_1.png)
![12](./images2/fix_script_2.png)
![13](./images2/fix_script_3.png)
![14](./images2/fix_script_4.png)
![15](./images2/fix_script_5.png)
![16](./images2/fix_script_6.png)
![17](./images2/fix_script_7.png)

