# Developer Guide

## 1. Introduction
Cent Wise Dollar Wise is a desktop application for money management, optimised for use via a Command Line Interface (CLI). This application aims to help NUS hostel students manage their financial situation and track their spending efficiently. 
This developer guide provides information on the architecture and design of the application. It will not only help you get started as a Cent Wise Dollar Wise contributer, but that you will find useful to refer to even if you are already a contributer of this project. 

## 2. Setting up
### 2.1 Prerequisites
- JDK 1.8.0_60 or later <br>
🛈 Having any Java 8 version is not enough. <br>
🛈 This app will not work with earlier versions of Java 8.
- IntelliJ IDE <br>
🛈 IntelliJ by default has Gradle and JavaFx plugins installed. <br>
🛈 Do not disable them. If you have disabled them, go to File > Settings > Plugins to re-enable them.

### 2.2 Setting up the project in your computer
The following are the steps to set up the project in your computer:
1. Fork this repo, and clone the fork to your computer.
2. Open IntelliJ. If you are not in the welcome screen, click File > Close Project to close the existing project dialog first.
3. Set up the correct JDK version for Gradle.
4. Click Configure > Project Defaults > Project Structure.
5. Click New…​ and find the directory of the JDK.
6. Click Import Project.
7. Locate the build.gradle file and select it. Click OK
8. Click Open as Project
9. Click OK to accept the default settings
10. Open a console and run the command gradlew processResources (Mac/Linux: ./gradlew processResources). It should finish with the BUILD SUCCESSFUL message. This will generate all resources required by the application and tests.

### 2.3 Verifying the setup
The following are the steps to verify your setup:
1. Run the seedu.duke.Duke and try a few commands.
2. Run the tests and ensure all the tests pass.

## 3. Design & implementation
### 3.1 Architecture
![image](https://user-images.githubusercontent.com/45732128/97735431-4886f780-1b15-11eb-920c-24e5bf7a76fe.png) <br>

The architecture diagram above explains the high-level design of the application. Given below is a quick overview of each component:

Main: Initializes spending list at app launch and coordinates the interaction between other components

DukeException: Issues exceptions if there are errors in storage or wrong format of user input is detected

SpendingList: Stores the expenditure of the user as individual entries

Storage: Reads data from, and writes data to, the hard disk

Ui: Interacts with the user

Command: Executes the user command or system-issued command

Parser: Analyzes the user command

**How the architecture components interact with each other** <br>

The sequence diagram below shows how the components interact with each other when the user issues a general command. <br>

![image](https://user-images.githubusercontent.com/45732128/97735507-62c0d580-1b15-11eb-9f14-aab54bf5b29d.png) <br>

### 3.2 Add Feature
`SpendingList`, `SpendingListCategoriser` and `Ui` facilitate this feature. The Add feature is able to add a new item into the spending list. It implements the following operations:
* `AddCommand#updateAmount` → converts amount to SGD
* `AddCommand#updateCurrency` → updates currency to SGD
* `SpendingList#addItem` → adds item into the spending list
* `Ui#printAdd` → prints the message that the item is successfully added into the spending list
* `SpendingListCategoriser#execute`→ categorises the item in the spending list
* `WarnCommand#execute` → prints warning message if total spending amount approaches the threshold of 90% of the budget limit or when the total spending amount exceeds the budget limit

Below shows an example of usage:
1. User types `add -c food -d beer -s USD 10` to add chicken rice into the spending list
2. The `add` command calls `AddCommand#execute`
3. `AddCommand#execute` calls the following operations in order:
   1. AddCommand#updateAmount to convert the amount “10” to SGD
   2. AddCommand#updateCurrency to update the currency from “USD” to “SGD”
   3. SpendingList#addItem to add beer into the spending list
   4. Ui#printAdd to print a message if beer is successfully added into the spending list
   5. SpendingListCategoriser#execute to categorise beer under the food category of the spending list
   6. WarnCommand#execute to print a warning message if required
   
The following sequence diagram illustrates how this feature works.

![image](https://user-images.githubusercontent.com/45732128/97770676-60d83000-1b70-11eb-95c1-63c2b300af05.png) <br>

### 3.3 Clear Feature
`SpendingList`, `RepaymentList` and `Budget` facilitate this feature. The Clear feature is able to clear existing data (either budget, repayment entries, spending entries, or all), according to the user’s command. It implements the following operations:
* `ClearBudgetCommand#execute` → clears budget and prints message output
* `ClearSpendingListCommand#execute` → clears spending list and prints message output
* `ClearRepaymentListCommand#execute` → clears repayment list and prints message output
* `PurgeDataCommand#execute` → clears all data from budget, spending list and repayment list, and prints message output
* `Budget#clearBudget` → clears budget
* `RepaymentList#deleteRepaymentEntry` → deletes a particular entry of the repayment list
* `RepaymentList#clearAllEntries` → clears all entries of the repayment list
* `Ui#printClearAllRepaymentList` → prints the message that all entries in the repayment list are cleared
* `SpendingList#deleteItem` → deletes a particular entry of the spending list
* `SpendingList#clearAllItems` → clears all entries of the spending list
* `Ui#printClearAllSpendingList` → prints the message that all entries in the spending list are cleared
* `Ui#printClearIndex` → prints the message that an entry is cleared from either the repayment list or the spending list
* `Ui#printPurgeData` → prints the message that all data is cleared

Below shows an example of usage:
1. User types `clear -s 1` to clear the first entry in the spending list.
2. ClearSpendingListCommand calls `ClearSpendingListCommand#execute`, which calls the following operations in order:
   1. SpendingList#clearAllItems to clear all items in the spending list and saves the updated spending list in storage.
   2. Ui#printClearAllSpendingList to print a message if the spending list is cleared successfully
   
The following sequence diagram illustrates how clearing a spending list works.

![image](https://user-images.githubusercontent.com/45732128/97770858-08099700-1b72-11eb-86e4-80e07c416ea6.png) <br>

### 3.4 Edit Feature
`SpendingList` and `Ui` facilitate this feature. The Edit feature is able to edit the existing items in the spending list. 
It implements the following operations:
* `EditCommand#execute` → edit the spending list accordingly and calls the ui to print message output
* `SpendingList#editItemDescription` → updates the description of the item in the spending list
* `SpendingList#editItemCategory` → updates the category of the item in the spending list
* `SpendingList#editItemAmount` → updates the amount of the item in the spending list
* `Ui#printEdit` → prints the message to show the successful completion of the edition process

Below shows an example of the usage:
1. User executes `edit 1 bubble tea` command to edit the first item in the spending list to bubble tea
2. The `edit` command calls the `EditCommand#execute` to complete the edition process

The following sequence diagram illustrates how this feature works.

![image](https://user-images.githubusercontent.com/45732128/97735682-97cd2800-1b15-11eb-9a72-dd0b0dee7b43.png) <br>

### 3.5 Convert Feature
`SpendingList`, `Ui` and `Item` facilitate this feature. The Convert feature is able to convert the currency of the items 
stored in the spending list. It implements the following operations:
* `ConvertCommand#execute` → converts currency and calls the ui to print message output
* `SpendingList#getSpendingList` → retrieves the current spending list
* `ConvertCommand#identifyCurrency` → identifies the currency after conversion
* `ConvertCommand#findExchangeRate` → finds the corresponding exchange rate
* `Item#editAmount` → updates the new amount after conversion
* `Item#editSymbol` → updates the new currency after conversion
* `SpendingList#updateSpendingList` → updates the spending list after conversion 
* `Ui#printConvertCurrency` → prints the message to show the successful completion of the conversion 
process

Below shows an example of the usage:
1. User executes `convert SGD USD` command to convert the currency from SGD to USD
2. The `convert` command calls the `ConvertCommand#execute` to complete the conversion process

The following sequence diagram illustrates how this feature works.
![image](https://user-images.githubusercontent.com/45732128/97736543-d2839000-1b16-11eb-8ecd-ced57fe20466.png) <br>

### 3.6 Set Budget Feature
`Budget` and `Ui` facilitate this feature. The Set Budget feature is able to set the budget limit for the spending. 
It implements the following operations:
* `SetBudgetCommand#identifyBudgetLimit` → identifies the budget limit input by the user
* `Budget#addBudget` → stores the budget limit with its corresponding currency
* `Ui#printBudgetLimit` → prints the message to show the successful completion of the setting 
budget process

Below shows an example of usage:
1. User executes `set SGD 100.0` to set the budget limit to SGD 100.0
2. The `set` command calls `SetBudgetCommand#execute` to complete the setting budget process

Figure below shows the sequence diagram of `SetBudgetCommand` class.

![image](https://user-images.githubusercontent.com/45732128/97735896-ee3a6680-1b15-11eb-82a1-df893ea95675.png) <br>
Figure 5: Sequence diagram of `SetBudgetCommand` class

### 3.7 Warn Feature
`Budget`, `SpendingList` and `Ui` facilitate this feature. The Warn feature is able to warn the user when the total 
spending amount approaches the threshold of 90% of the budget limit or when the total spending amount exceeds 
the budget limit. It implements the following operations:
* `Budget#getCurrency` → retrieves the corresponding currency for the budget
* `Budget#getBudgetLimit` → retrieves the budget limit
* `SpendingList#getCurrentAmount` → gets the total amount of spending in the spending list
* `Ui#printApproachingWarningMessage` → prints the warning message that the spending 
is approaching budget limit (i.e. exceeds the threshold value of 90% of the budget limit but not yet exceed it), with 
the amount remaining
* `Ui#printExceedingWarningMessage` → prints the warning message that the spending has exceeded the budget limit

Below shows an example of usage:
1. User adds the spending to the spending list after setting the budget limit
2. The `add` command calls `WarnCommand#execute` to check if the user approaches or exceeds budget 
limit 

The following sequence diagram illustrates how this feature works.

![image](https://user-images.githubusercontent.com/45732128/97735970-feeadc80-1b15-11eb-9a72-f37dc73ea7e9.png) <br>

### 3.8 Repay Feature
`RepaymentList` and `Ui` facilitate this feature. The Repay feature is able to store the repayment information as a 
remainder to the user. It implements the following operations:
* `RepaymentList#storeCurrentString` → stores the repayment information to the repayment list
* `Ui#printRepay(repaymentList.returnCurrentString` → prints the repayment information which has been successfully stored

Below shows an example of usage:
1. User executes `repay Johnny SGD 5.0 2020-12-20` command to add the repayment information that one has to repay 
SGD 5.0 to Johnny before 2020-12-20
2. The `repay` command calls the `RepayCommand#execute` to complete the adding process

The following sequence diagram illustrates how this feature works.

![image](https://user-images.githubusercontent.com/45732128/97736043-14f89d00-1b16-11eb-85c4-73a3e52112c2.png) <br>

### 3.9 Summary Feature
`SpendingList` and `Item` facilitate this feature. The Summary feature is able to summarise the total amount spent 
given a date. It implements the following operations:
* `Item#getDate` → gets the date when user spent on the item
* `Item#getAmount` → gets the amount spent on the item
* `SpendingList#getSpendingAmountTime` → gets the total amount spent during a period
* `SpendingList#getSpendingAmountCategory` → gets the total amount spent of a given category

Below shows an example of usage:

1. User executes the `summary 2020` command to get the amount spent during year 2020.
2. The `summary` command calls the `SpendinList#getSpendingAmount` which checks the spending date of every items
stored in the memory.
3. If the item is spent during year 2020, the amount spent will be summed up.

The following sequence diagram illustrates how this feature works.

![image](https://user-images.githubusercontent.com/45732128/97736098-26da4000-1b16-11eb-8e03-37486b55303d.png) <br>

### 3.10 Reminder Feature
`SpendingList` and `WarnCommand` facilitate this feature. The Reminder feature is able to provide the user about the 
total expenditure of the current week, starting on Monday. It implements the following operations:
* `SpendingList#getSpendingAmountTime` 
* `WarnCommand#execute`

Below shows an example of usage:

1. User starts the application.
2. The `Reminder` will be instantiated. The dates of the current week (starting from Monday) will be saved to a list.
3. In the `Reminder#execute(spendinglist, ui)`, a check will be done to see if there is any budget being set by the user.
    * If no budget is being set, the total expenditure of current week will be tallied up. 
    * If there is, `WarnCommand#execute(spendingList, ui)` will be called first before tallying up the expenditure. 

The following sequence diagram illustrates how this feature works.

![image](https://user-images.githubusercontent.com/45732128/97736156-39ed1000-1b16-11eb-93d4-062a41d9657b.png) <br>

### 3.11 Encouragement Feature
The Ui facilitate this feature. The Encouragement feature regularly encourages the user to keep up his effort in using the application. It implements the following operation:
Ui#printEncouragementMessage() → prints an encouragement message for the user

This is a hidden feature that will be executed automatically by the application when the application is launched. Figure below shows the sequence diagram of `EncouragementCommand` class.

The following sequence diagram illustrates how this feature works.

![image](https://user-images.githubusercontent.com/45732128/97768300-0386b380-1b5d-11eb-9438-1c328ab10139.png) <br>

### 3.12 Export Feature
`Workbook`, `FileOutputStream`, `SpendingList` and `Ui` facilitate this feature. The export data feature could extract the current data and export to an Excel file. It implements the following operations:
* `Workbook#createSheet` → creates a sheet in the workbook
* `SpendingList#getItem` → gets the item waiting to be added to the workbook
* `Workbook#write` → writes the data to an Excel file
* `Ui#printExportMessage` → prints the message indicating that the Excel file has been created successfully

Below shows an example of usage:

1. User executes the `export F:\` command to export the data to the location `F:\`.
2. The `export` command calls the `Workbook#createSheet()` to create an Excel workbook sheet.
3. `SpendingList#getItem` will be called repeatedly to get items and set the cells' values accordingly.
3. After writing to an Excel file at the location `F:\` using `Workbook#write`, a prompt message will be shown by `Ui`.

The following sequence diagram illustrates how this feature works.

![image](https://user-images.githubusercontent.com/45732128/97736203-4a9d8600-1b16-11eb-9661-ce9c27ccb6a9.png) <br>

## 4. Product scope
### 4.1 Target user profile

The target user is NUS hostel students including both local students and international students.

### 4.2 Value proposition

We observed that hostel students are encountering tight budgets due to expensive hostel fees, on top of their school 
tuition fees. Hence, we are developing a money manager targeted at NUS hostel students. This money manager   
Cent Wise Dollar Wise aims to help hostel students to have better financial management with limited budget. 

## 5. User Stories

|Version| As a ... | I want to ... | So that I can ...|
|--------|----------|---------------|------------------|
|v1.0|international student|convert local currency to foreign currency or the reverse way|trace my spending easily|
|v1.0|careless user|view and reset the previous input|ensure the data input is correct|
|v1.0|user|have a monthly/yearly summary|know how much I had spent|
|v2.0|user|set a budget limit|be less likely to overspend my money|
|v2.0|user|be warned when my spending approaches the budget limit or exceeds the budget limit|be less likely to overspend my money|
|v2.0|big spender|tag purchases according to categories|for an easier overview|
|v2.0|user|have weekly reminders on how much has spent|take note of future spending|
|v2.0|forgetful user|include a list to summarise the repayment to others|repay the debt on time|

## 6. Non-Functional Requirements

1. The system should respond within two seconds.
2. The system should be easy to use, do not require much effort to learn.
3. The system should work on any mainstream OS that has Java 11 or above installed.
4. Users with faster typing speed than average should be able to use this program with ease.

## 7. Glossary 

* *glossary item* - Definition

## 8. Instructions for manual testing

{Give instructions on how to do a manual product testing e.g., how to load sample data to be used for testing}
