# HR ANALYTICS
### Understanding the Requirements:
Arrow Key is a startup company handling software and customer service operations. There fiscal year starts from April to March. They have attendance details of their employees. They want to understand the working preference of people between work from home(WFH) & work from office(WFO) and on which days most of them are opting WFH. They also want to know the presence, Sick leave percentage and percentage of people present on a given week or given month.

### Understanding the data:
The dataset is consisting the day wise data of the month of employee attendence. Along with that there a sheet with attendence key - ATTENDANCE KEY	
1. P - Present 
1. PL - Paid Leave 
1. SL - Sick Leave 
1. HPL - Half day PL 
1. HSL - Half day SL
1. WFH - Work from home 
1. FFL - Floting festival leave 
1. HFFL - Half Day Floting festival leave 
1. BL - Birthday Leave 
1. LWP - Leave without pay
1. HLWP - Half day Leave without pay
1. BRL - Bereavement Leave
1. HBRL - Half Bereavement Leave
1. HWFH - Half Work From Home
1. WO - Weekly Off
1. HO - Holiday Off
1. ML - Menstrual Leave
1. HML - Half Day ML
### Features:
To import the data, multiple sources are present. As our dataset is in excel so used excel source for connecting. We have uncleaned dataset so to manipulate the data we have to transform it. But in our case there are 3 sheets present with different column names. So here we have to follow different procedure import the data.
## Data Gathering & Transformation:
- We have multiple sheets data with different column names so we cannot go with regular procedure. So here i am following different approach to import the data.
1.  In Power BI connect to your Excel file as normal, then in the Navigator pane right-click on the name of the Excel workbook and select Transform data rather than selecting any of the individual worksheets:
2.  Create your template query
- Duplicate the query above and call the new query Template.
- Now, in the Template query, select one of the worksheets to use to build the query whose logic will be applied to all the other worksheets, and filter the table above so it only contains the row for that worksheet. In this case I’m using the worksheet called April.
- remove all the other columns in the table except the Data column.
- After that click the Table link inside the cell, and you’ll see the contents of the worksheet and There will probably be a Changed Type step in the query that sets the data types for each of the columns, and you will need to delete it.(We need to apply this modifications to the other sheets as well. There are sheets with different column names so it will try to find the reference with column names. So in order avoid that we are deleting change type step from applied steps)
### Data Preprocessing:
1. Promoted Headers
1. Removed top 1 row
1. Unpivoted coumns other than first two columns
1. Changed column names and data types
-  In the date column along with the dates string values are also present. We can filter them directly but instead of doing it manually for upcoming sheets we are trying to make it dynamic(Automation). So for that change data type of date column to date and then click on remove errors from right click.
- It means in future if we get a text value in the date column it will be automatically removed.
### Create a function:
1. You need to create a new parameter by clicking the Manage Parameters/New Parameter button, call the parameter Worksheet, set the data type to text and have it return the name of the worksheet you chose in the previous step.
1. Now, go back to the Template query, find the step called Filtered Rows towards the beginning where you filtered down to a single worksheet, and click the gear icon next to the step to edit it.
1. Then, edit the step so it uses the value returned by the parameter to filter by instead of the hard-coded value you entered earlier. To do this, click on the button shown below, select Parameter and then select the Monthsheet parameter in the next dropdown box along.
1. In order to apply all this transformations to all the other sheets, go to the Queries pane on the left-hand side of the screen and right-click on the Template query and select Create Function.
1. You’ll be prompted to give the new function a name; call it GetData.
### Invoke the function and combine the data:
Finally, go back to the duplicate copy of the original query created at the beginning of step 2. Then go to the Add Column tab on the ribbon and click the Invoke Custom Function button and invoke the GetData function, passing in the contents of the Name column to the function’s only parameter.
- Last of all, click the Expand/Aggregate button on the new column and expand the nested tables.
- After removing any unnecessary columns, you’ll see the data from all the worksheets combined into a single table as desired.
- Before loading the data, Disable the Template and rename the original sheet name in the left side of the panel.
## Data Visualization:
### Created Dax Expresions -
- Created new table with enter data option for putting all created measures in a seperate table.
### Calculated measures.
1. Total Working Days =

   var totaldays = COUNT('Final Data'[Value])

   var nonworkdays = CALCULATE(COUNT('Final Data'[Value]),'Final Data'[Value] in {"WO","HO"})

   return

   totaldays-nonworkdays
1. Present days =

   var Presentdays = CALCULATE(COUNT('Final Data'[Value]),'Final Data'[Value]="P")

   RETURN 

   Presentdays + [WFH Count]

1. WFH Count = SUM('Final Data'[WFH Count])
1. Presence % = DIVIDE([Present days],'Measure Table'[Total working days],0)
1. WFH % = DIVIDE([WFH Count],[Present Days],0)
1. SL % = DIVIDE([SL Count],[Total Working Days],0)
## Calculated columns.
1. WFH Count = SWITCH(TRUE(),

      'Final Data'[Value] = "WFH",1,

      'Final Data'[Value] = "HWFH",0.5,
      0)
1. Month = STARTOFMONTH('Final Data'[Date])
1. SL Count = SWITCH(TRUE(),
      'Final Data'[Value] = "SL", 1,
      'Final Data'[Value] = "HSL",0.5,
    0)
### Conclusion:
- Presence % is slightly declining by month.
- WFH % is increasing by month.
- SL % is also increasing by month due to some seasonal issues.