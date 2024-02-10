# Atliq Technologies Human Resource Analysis Project


## Problem Statement

Atliq Technologies is an IT consulting firm that offers technology development services to various firms. Due to the cultural shift towards work-from-home employment, the company has been experimenting with allowing staff to work from home when they give reasonable cause.

The head of human resources (HR), Pinali Mandalia, has been trying to track the changes in work patterns as well as move her HR data from an Excel workbook to a business intelligence software to gain valuable insights about attendance patterns and working preferences.

The company aims to use these data-driven insights to inform decisions regarding 
- activities requiring the physical presence of employees 
- developing a hybrid work structure if need be
- planning office resources
- improved sanitation in the office due to an increase in sick leave


## Project Planning
1. Purpose: To unlock HR data insights that were previously not visible to the HR team for the purpose of better decision-making on operation, resource planning, and employee working conditions.
2. Stakeholders:
- Managing Director
- Head HR
- Data analytics team
- IT team
3. End result: An automated dashboard providing quick and up-to-date sights in order to support data-driven decision-making.
4. Success Criteria :
- Creation of a dashboard uncovering HR data insights with the latest data available
- Enable the HR team to track the attendance percentage 
- Support management to monitor the working preference of people (work from home or work from office)
- Enable management to identify key insights that can aid in the planning of office team-building activities or work activities where people have to be physically present
- Identify Key insights that can help plan a hybrid work structure if need be (e.g: 2 days WFH and 3 days office) 
- Aid office resource planning with WFH trends
- Track sick leave percent to aid better health initiative planning like flu shots, improved sanitization


## Project Steps

### Data Exploration

Real-life employee attendance data was provided by Atliq Technologies, but unique identifiers like names and IDs were altered to protect their employees.

The HR data was stored in an Excel workbook with different Excel sheets for each month. The data covered attendance records from April 1st, 2022, to June 17th, 2022.

The columns for each sheet contained the days in a month and some key variables and each row represents an employee.

Also, a data key was provided to help make sense of the abbreviations. This key is shown below:

![Attendance Key](https://github.com/Jucodez/Real-Life-HR-Data-Analysis-Project/assets/102746691/132f265f-a20a-4402-a10d-15577b269272)

### Extract, transform, and load (ETL)

- Loaded the data from the Excel workbook to power query for transformation and cleaning
- Created a dynamic data cleaning and transformation template that accounts for updates to the Excel workbook as the HR team continues to track employee attendance using relevant transformations and parameters for automation.
- Used Power Query to transform the data to a more tidy structure and format ( from having dates in the columns to having a single date column and each observation as a row instead of having all observations for an employee on a row)

  Before

  ![pivoted](https://github.com/Jucodez/Real-Life-HR-Data-Analysis-Project/assets/102746691/eb2ab8e2-6ccd-400a-9e37-ea9f1368fe44)


  After

  ![Upivoted](https://github.com/Jucodez/Real-Life-HR-Data-Analysis-Project/assets/102746691/1793ccc2-e445-4bcc-be14-01ad8d314368)

- Unnecessary data points were eliminated  
- The resulting Attendance Data table was examined for data-cleaning opportunities, and none were found
- A date table was also created to support the analysis process using DAX. The calendarauto function was used to create the date column to aid automatic updates of the date table when new data is added. 

      Dates = CALENDARAUTO()

- A Month column was added to the date table to extract the month name for each date entry

      Month = FORMAT('Dates'[Date], "MMMM")


- A Weekdays column was added to the date table to extract the weekday for each date entry

      Weekdays = format('Dates'[Date],"dddd")

- A Year column was added to the date table to extract the weekday for each date entry

      Year = FORMAT('Dates'[Date], "yyyy")
- A Day Number column was added and used to order the days of the week by calendar order. The column was created using the formula below

      Day Number = SWITCH(format('Dates'[Date],"dddd"),    
          "Sunday",1,
          "Monday",2,
          "Tuesday",3,
          "Wednesday",4,
          "Thursday",5,
          "Friday",6,
          "Saturday",7,
          BLANK())

The date table is presented below.

![date table](https://github.com/Jucodez/Atliq-Technologies-Human-Resource-Analysis-Project/assets/102746691/edf4cecd-0d2b-4312-b5d0-bef5ed7571a6)

### Data modeling

A many-to-one relationship was created between the Date column in the Attendance Data table and the Date column in the Date table. This relationship was made active and given a single cross-filter direction from the Date table to the Attendance Data table.


![Data model](https://github.com/Jucodez/Atliq-Technologies-Human-Resource-Analysis-Project/assets/102746691/da3d39fb-b3ad-4f61-af2f-087c2a8188a9)



### Measures
- Calculate the number of Working days

      Working Days = 

      var totaldays = COUNT('Combined Data'[Attendance])

      var offday =  CALCULATE(COUNT('Combined Data'[Attendance]), 'Combined Data'[Attendance] in {"WO","HO"})

      RETURN
      totaldays-offday+0

- Calculate the number of Present days

      Present Days = 

      var fullday =  CALCULATE(COUNT('Combined Data'[Attendance]), 'Combined Data'[Attendance] in {"P","WFH"})

      var halfday =  DIVIDE(CALCULATE(COUNT('Combined Data'[Attendance]), 'Combined Data'[Attendance] in {"HPL","HSL","HFFL","HLWP","HBRL","HWFH","HML"}),2,0)

      RETURN
      fullday+halfday+0


- Calculate the number of WFH Days

      WFH Days = 

      var fullday =  CALCULATE(COUNT('Combined Data'[Attendance]), 'Combined Data'[Attendance] = "WFH")

      var halfday =  DIVIDE(CALCULATE(COUNT('Combined Data'[Attendance]), 'Combined Data'[Attendance] = "HWFH"),2,0)

      return
      fullday+halfday+0

- Calculate the number of SL Days

      SL Days = 

      var fullday =  CALCULATE(COUNT('Combined Data'[Attendance]), 'Combined Data'[Attendance] = "SL")

      var halfday =  DIVIDE(CALCULATE(COUNT('Combined Data'[Attendance]), 'Combined Data'[Attendance] = "HSL"),2,0)

      return
      fullday+halfday+0

- Calculate Attendance %

      Attendance % = DIVIDE([Present Days],[Working Days],0)

- Calculate WFH % 

      WFH % = DIVIDE([WFH Days],[Present Days],0)+0

- Calculate SL %

      SL % = DIVIDE([SL Days],[Working Days],0)+0

### DASHBOARDING
A dashboard was developed to help visualize key parameters relevant to attendance, work from home, and sick leave.

The Attendance Dashboard is presented below:

****

![Atliq](https://github.com/Jucodez/Atliq-Technologies-Human-Resource-Analysis-Project/assets/102746691/18abb26f-8495-4fd7-9a5e-033e242cfe76)

****

This dashboard was published online for easy access by company management staff.


### Key Insights / Data-Driven Strategy
1. Attendance Trend Analysis: 

      ![Attendance %](https://github.com/Jucodez/Atliq-Technologies-Human-Resource-Analysis-Project/assets/102746691/b56ba61e-de88-4ad9-8b4e-c7422dd77e95)

A downward trend in attendance was observed. This might be a seasonal thing so collecting more data might help to identify more patterns and improve decision making. A better understanding of attendance trends can help the company identify the amount of projects it can take on during certain seasons if seasonal patterns are identified. 



2. Work From Home (WFH) Trend Analysis: 

      ![WFH%](https://github.com/Jucodez/Atliq-Technologies-Human-Resource-Analysis-Project/assets/102746691/dc5cdb09-9527-43be-9d85-9699daa3bb05)

An upward trend in WFH was observed this might be due to an increasing cultural shift towards remote jobs. Necessary cost-saving practices have to be undertaken regarding office resources to account for the gradual reduction in employees Utilizing the office space.

3. Sick Leave (SL) Trend Analysis: 

      ![SL%](https://github.com/Jucodez/Atliq-Technologies-Human-Resource-Analysis-Project/assets/102746691/7d256aab-9598-4eef-badd-3c8e31f2fb3f)

An upward trend in SL was observed. This might be a seasonal thing, so more data is needed for better decision-making. However, it might indicate falling sanitation and hygiene standards in the office, and this has to be looked into. Flu shots might be recommended for employees.

4. Weekday Attendance %: 

      ![Attendance table](https://github.com/Jucodez/Atliq-Technologies-Human-Resource-Analysis-Project/assets/102746691/bfc2f292-a59a-432a-a0e1-ba5b8945fea6)


Monday had the highest attendance % (94%), while Friday had the lowest (91%). This can help plan tasks and workload. The company might decide to get more done on days when more people are available and reduce the workload on days when fewer people are available.

5. Weekday WFH %: 

      ![WFH Table](https://github.com/Jucodez/Atliq-Technologies-Human-Resource-Analysis-Project/assets/102746691/7899a1ea-5697-4add-888f-588f256ea2bc)

Friday and Thursday had the highest WFH %( 12.9 and 11.4%), while the other days were considerably less, all being less than 9%. This might help inform a more structured hybrid work format where employees can work from home on Fridays and Thursdays but have to come to the office Monday through Wednesday. This insight can also aid office resource planning in the meantime to save costs.



## Conclusion 

Overall, this dashboard has helped the head of HR and other key management staff gain insight into the attendance patterns and working preferences of employees in the organization. From this, well-informed decisions can be taken with regard to operations and resource planning as well as the development of a hybrid work structure. 
