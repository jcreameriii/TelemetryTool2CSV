# TelemetryTool2CSV

Learn More Here: https://cyberark-customers.force.com/s/article/Better-Together-CyberArk-Telemetry-and-Business-Intelligence

CyberArk Customer Success Programs Office member, amateur developer and Success Blog author, James Creamer, put together an example of how you can leverage simple scripting languages like PowerShell to make your life easier in the ETL process of the CyberArk Telemetry Tool, allowing you to convert key sections of the JSON file into individual CSV files, making it easy to be consumed by various to BI tools. You can find the PowerShell script on James’ GitHub under the TelemetryTool2CSV repository.

The code is structured into eight major sections: configuration parameters, finding the latest JSON file, setting the telemetry date, and creating each of the five output CSV files. The PowerShell script is structured in this way because its important to be able to specify the input and output paths for the JSONs and CSVs as well as ensuring we’re always fetching the latest JSON record. It’s much easier for a BI tool to consume data and track it over time when each record has its own date, and for certain sections we have many rows. We have each type of telemetry data broken out into its own section as a way to segregate different types of records, as well as do unique error handling for potential duplicate records in each section.

In each telemetry record section, the script is specifying which type of record we’re going to convert. It loads the JSON file and expands the relevant object details and adds the date as a value to each object record. From there, it checks to see if we had already created a CSV output for this record with the date of the JSON file it loaded. If it does, it writes an error message to the Output folder called <date>.log. If it doesn’t find the existing file, it continues on to select various relevant record properties and exports them to a CSV file called <date-recordtype>.csv.

The major limitation is that the script doesn’t currently fetch record property details in multiple nested JSON sections, which impacts the ability to see CPM-related information in the PlatformDetails output.

Setting Up the Script
The script essentially piggy-acks on the existing Telemetry Tool scheduled task, so we’ll need to get the file over to that location.

Step 1: Staging the Script
Download the PowerShell script from the TelemetryTool2CSV repository. Stage the PowerShell script on the machine that runs your CyberArk Telemetry Tool scheduled task. On that machine, navigate to the CyberArk Telemetry folder (by default it should be C:/Program Files/CyberArk/CyberArk Telemetry). In that directory, create a new folder in the directory called “ETL” and then within that new folder create another folder called “CSV”. Move the PowerShell script to the ETL folder.

Step 2: Modify the Scheduled Task
Launch task scheduler and edit the CyberArk Telemetry task by right clicking on it and selecting properties. Under the Actions tab, add a new action to launch the PowerShell script.
Program/script: powershell
Add arguments (optional): -NoProfile -NoLogo -NonInteractive -ExecutionPolicy Bypass -File "C:\Program Files\CyberArk\CyberArk Telemetry\ETL\TelemetryToolETL.ps1"
Press OK, and then right click on the task and run on-demand.


Figure 2: Adding the PowerShell Script to the Scheduled Task

Step 3: Checking the Output
Navigate to the CSV folder. Validate that the files have been created for PlatformDetails, Components, Users, AppIDs and Accounts, with the date matching the latest run of telemetry. Open the files and confirm they contain your telemetry data.

Integrating Telemetry with Your BI Tools and Data Pipeline
While not the most advanced data source, CSV files are certainly one of the most common. Modern BI tools and data pipelines should be able to ingest CSV files from source directories with minimal effort. While each data pipeline is a bit different, you’ll likely want to either point your data source configuration or setup to use that “CyberArk Telemetry/ETL/CSV” folder location. You could create some additional FTP process to move those files to another location you’ll use for your directory source instead.

Alternatively, you could build your own code or modify this existing script to suit your data pipeline best, such as changing the output of the telemetry data to a database. This approach would lend itself well to converting each section to its own dedicated table.
