# Onboarding Application - ServiceNow

## Project Overview

The **Onboarding Application** is a ServiceNow Service Catalog project designed to automate and manage the employee onboarding process.

The application allows users to submit an onboarding request through the Service Portal. The request collects employee information, validates the entered data, provides software and hardware requirements, creates incidents where required, manages approvals, and automatically generates catalog tasks for different teams.

The project uses ServiceNow features such as:

- Service Catalog
- Catalog Items
- Variable Sets
- Catalog Client Scripts
- Catalog UI Policies
- Order Guides
- Record Producers
- SLA Definitions
- Flow Designer
- Approvals
- Catalog Tasks
- Service Portal

---

## Project Objectives

The main objectives of the project are:

1. Create an employee onboarding catalog application.
2. Collect employee and onboarding details through catalog variables.
3. Validate user input using Catalog Client Scripts.
4. Dynamically display and make fields mandatory based on user selections.
5. Create an order guide containing multiple onboarding-related catalog items.
6. Create an SLA for onboarding requests.
7. Create an incident automatically when a service-related issue is selected.
8. Route requests to the appropriate teams for approval.
9. Generate multiple catalog tasks automatically after approval.
10. Provide the complete onboarding process through the Service Portal.

---

## Application Structure

The project contains the following major components:

```text
Onboarding Application
│
├── Employee Onboarding Catalog Item
│   ├── Personal Details
│   ├── Employee Details
│   └── Validations
│
├── Software Catalog Item
│   └── Software Update
│
├── Hardware Catalog Item
│   └── IP Address Validation
│
├── Incident Record Producer
│   ├── Affected Service
│   ├── Contact Type
│   ├── Description
│   └── Urgency
│
├── Order Guide
│   ├── Onboarding Application
│   ├── Software
│   ├── Hardware
│   └── Incident Record Producer
│
├── SLA
│   └── 4 Days 8 Hours
│
└── Flow Designer
    ├── Approval Routing
    └── Parallel Catalog Tasks
```
## 1. Employee Onboarding Catalog Item

The primary catalog item is used to collect employee onboarding information.

The catalog item contains variable sets such as:

* Personal Details
* Employee Details

The project document shows the catalog item being created under:
```
All > Maintain Items > New
```
The catalog item is configured to be submitted through the Service Catalog.

### Personal Details

The Personal Details variable set contains employee-related information.

Examples include:

* Employee Name
* Date of Birth
* Phone Number
* Country
* City
### Employee Details

The Employee Details variable set contains employee-specific information.

One of the main validations is the Employee ID.

### Employee ID Validation

The Employee ID must contain exactly six digits.

Example validation:
```javascript
function onChange(control, oldValue, newValue, isLoading) {

    if (isLoading || newValue == '') {
        return;
    }

    var regex = /^\d{6}$/;

    if (!regex.test(newValue)) {

        g_form.showFieldMsg(
            'employee_id',
            'Invalid input. Please enter exactly 6 digits with no spaces or letters.',
            'error',
            true
        );

        g_form.setValue('employee_id', '');
    }
}
```
## 2. Phone Number Validation

The employee phone number must contain 10 digits.

The Catalog Client Script validates the entered phone number and displays an error when the input is invalid.

Example:
```javascript
function onChange(control, oldValue, newValue, isLoading) {

    if (isLoading || newValue == '') {
        return;
    }

    var regex = /^\d{10}$/;

    if (!regex.test(newValue)) {

        g_form.showFieldMsg(
            'phone_number',
            'Please enter a valid 10 digit phone number.',
            'error',
            true
        );

        g_form.setValue('phone_number', '');
    }
}
```
## 3. Employee Name Validation

The employee name should contain only alphabets and spaces.

The maximum length is 30 characters.
```javascript
function onChange(control, oldValue, newValue, isLoading) {

    if (isLoading || newValue == '') {
        return;
    }

    var regex = /^[A-Za-z\s]{1,30}$/;

    if (!regex.test(newValue)) {

        g_form.showFieldMsg(
            'name',
            'Name should contain only alphabets and spaces and be maximum 30 characters.',
            'error',
            true
        );

    } else {

        g_form.hideFieldMsg('name', true);
    }
}
```
## 4. Date of Birth Validation

The Date of Birth field is mandatory.

A Catalog OnSubmit Client Script checks whether the field has been populated before the catalog item is submitted.
```javascript
function onSubmit() {

    var dob = g_form.getValue('dob');

    if (g_form.isMandatory('dob') && dob == '') {

        g_form.showFieldMsg(
            'dob',
            'Please select date of birth',
            'error',
            true
        );

        return false;
    }
}
```
## 5. Dynamic Country and City Selection

The project contains dynamic behavior for Country and City.

When a country is selected:

- The City field becomes visible.
- The City field becomes mandatory.
- City options are dynamically populated based on the selected country.

Example:
```javascript
function onChange(control, oldValue, newValue, isLoading) {

    g_form.clearOptions('city');

    g_form.addOption(
        'city',
        '',
        '-- Select City --'
    );

    if (newValue == 'india') {

        g_form.addOption('city', 'hyderabad', 'Hyderabad');
        g_form.addOption('city', 'banglore', 'Banglore');
        g_form.addOption('city', 'chennai', 'Chennai');

    } else if (newValue == 'germany') {

        g_form.addOption(
            'city',
            'germany_city_1',
            'Germany City 1'
        );

        g_form.addOption(
            'city',
            'germany_city_2',
            'Germany City 2'
        );

        g_form.addOption(
            'city',
            'germany_city_3',
            'Germany City 3'
        );

    } else if (newValue == 'japan') {

        g_form.addOption(
            'city',
            'japan_city_1',
            'Japan City 1'
        );

        g_form.addOption(
            'city',
            'japan_city_2',
            'Japan City 2'
        );

        g_form.addOption(
            'city',
            'japan_city_3',
            'Japan City 3'
        );
    }
}
```
## 6. SLA Configuration

An SLA is configured for onboarding requests.

#### SLA Configuration
```
Duration: 4 Days 8 Hours
Schedule: Weekdays
Working Hours: 8 AM - 5 PM
Weekends: Excluded
```
The SLA starts when a request is created in the request table.

#### SLA Conditions

Start condition:
```
State is Work in Progress
```
Pause condition:
```
State is Pending
```
Stop condition:
```
State is Closed Complete
```
The SLA is created from:
```
All > SLA > SLA Definition > New
```
## 7. Software Catalog Item

A Software catalog item is created as part of the onboarding process.

The requirement is to provide a checkbox called:
```
Software Update
```
If the checkbox is selected, a field should be displayed asking the user to provide the software version.

#### Variables
```
Software Update
    Type: Checkbox

Version Number
    Type: String
```
The software catalog item is included in the onboarding Order Guide.

## 8. Hardware Catalog Item

A Hardware catalog item is also included in the onboarding process.

The requirement is to collect and validate an IP address.

Only a valid IP address should be accepted.

Example validation:
```javascript
function onChange(control, oldValue, newValue, isLoading) {

    if (isLoading || newValue == '') {
        return;
    }

    var regex =
        /^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.
        (25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.
        (25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.
        (25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/;

    if (!regex.test(newValue)) {

        g_form.showFieldMsg(
            'ip_address',
            'Please enter a valid IP address.',
            'error',
            true
        );

        g_form.setValue('ip_address', '');
    }
}
```
## 9. Record Producer

A Record Producer is created for service-related issues.

The Record Producer creates a record in the Incident table.

Navigation:
```
All > Record Producer > New
```
The Record Producer collects information from the user and creates an incident when the appropriate service-related option is selected in the Service Portal.

The project document specifically describes using the Incident table as the target table for this Record Producer.

### Record Producer Variables

The Record Producer contains four main variables:
```
Affected Service
Contact Type
Description of the Service
Urgency
```
The variables belong to the Record Producer rather than directly to the Incident table.

## 10. Mapping Record Producer Variables to Incident

The Record Producer uses a script to map the collected variables to the Incident record.
```javascript
var serviceDisplay =
    producer.affected_service.getDisplayValue();

current.description =
    producer.description_of_the_service;

current.contact_type =
    producer.contact_method;

current.urgency =
    producer.urgency;

current.short_description =
    "issue is:" + serviceDisplay;

current.caller_id =
    gs.getUserID();

current.category =
    "inquiry";

current.subcategory =
    "service";

current.assignment_group.setDisplayValue(
    'Service Desk'
);
```
## 11. Order Guide

An Order Guide is used to combine multiple related catalog items into a single onboarding request.

The project creates an Order Guide for the employee onboarding process.

The Order Guide contains:
```
Employee Onboarding
Software
Hardware
Incident Record Producer
```
The user can submit the onboarding request and select the required catalog items through the Service Portal.

## 12. Order Guide General Variables

General variables are created for the Order Guide.
```
Email
Name
Phone
```
These variables are validated using Catalog Client Scripts.

## 13. Email Validation

The email address is validated using a regular expression.
```javascript
function onChange(control, oldValue, newValue, isLoading) {

    if (newValue == '') {
        return;
    }

    var regex =
        /^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$/;

    if (!regex.test(newValue)) {

        g_form.showFieldMsg(
            'email',
            'Entered invalid email text',
            'error',
            true
        );

    } else {

        g_form.hideFieldMsg('email', true);
    }
}
```
## 14. Order Guide Name Validation

The name field is validated to allow alphabetic characters and supported name characters.
```javascript
function onChange(control, oldValue, newValue, isLoading) {

    if (newValue == '') {
        return;
    }

    var regex = /^[a-zA-ZÀ-ÿ '.-]+$/;

    if (!regex.test(newValue)) {

        g_form.showFieldMsg(
            'name',
            "You've entered incorrect characters",
            'error',
            true
        );

    } else {

        g_form.hideFieldMsg('name', true);
    }
}
```
## 15. Order Guide Phone Validation

The phone number is validated using a country-code-based format.
```javascript
function onChange(control, oldValue, newValue, isLoading) {

    if (isLoading || newValue == '') {
        return;
    }

    var regex = /^\+[0-9]{1,3}[0-9]{10}$/;

    if (!regex.test(newValue)) {

        g_form.showFieldMsg(
            'number',
            'Please enter correct phone number',
            'error',
            true
        );

    } else {

        g_form.hideFieldMsg('number', true);
    }
}
```
## 16. Service Portal

After configuring the catalog items and Order Guide, the onboarding application can be accessed through the Service Portal.

The user can:

- Search for the onboarding application.
- Open the onboarding Order Guide.
- Enter employee details.
- Select the required catalog items.
- Submit the request.
- View the request summary.

After submission, the corresponding Requested Items are created.

## 17. Flow Designer

Flow Designer is used to automate the approval and task creation process.

The main flow is named:
```
onboarding application
```
Navigation:
```
All > Flow Designer > New > Flow
```
The trigger used is:
```
Service Catalog
```
The flow then retrieves catalog variables using:
```
Get Catalog Variables
```
## 18. Approval Routing

The onboarding request must be routed to the appropriate team based on the selected area of specialization.

####  Software

If:
```
Area of Expertise = Software
```
The request is routed for approval to:
```
Software Team
```
#### Core

If:
```
Area of Expertise = Core
```
The request is routed for approval to:
```
Core Team
```
The project documentation describes this conditional approval routing as Requirement 1 of the Flow Designer implementation.

## 19. Catalog Variable Retrieval

The Flow Designer retrieves the required variables from the onboarding catalog item.

The variables used by the flow include information such as:
```
Area of Expertise
Employee Details
Personal Details
```
If the expected Software option is not available dynamically, the project demonstrates using an exact/hardcoded value for the condition.

## 20. Parallel Catalog Tasks

After the approval process is completed, the flow creates three Catalog Tasks.

The three tasks are generated in parallel rather than sequentially.

The project uses the Catalog Task table:
```
sc_task
```
The required fields are mapped from the request/requested item information.

Flow structure:
```
Service Catalog Trigger
        |
        v
Get Catalog Variables
        |
        v
Approval
        |
        v
Approval Completed
        |
        +----------------------+
        |          |           |
        v          v           v
     Task 1     Task 2      Task 3
```
The project documentation explicitly specifies three parallel Catalog Tasks after approval and describes copying the task action twice and placing the three actions in parallel.

## 21. Overall Workflow

The complete onboarding process can be represented as:
```
User
 |
 v
Service Portal
 |
 v
Onboarding Order Guide
 |
 +--------------------+
 |                    |
 v                    v
Employee Details   Catalog Items
 |                    |
 |              +-----+-----+
 |              |           |
 |              v           v
 |          Software      Hardware
 |              |           |
 |              v           v
 |        Validation    IP Validation
 |              |           |
 +--------------+-----------+
                |
                v
          Submit Request
                |
                v
       Requested Item Created
                |
                v
          Flow Designer
                |
                v
        Get Catalog Variables
                |
                v
       Determine Specialization
                |
        +-------+-------+
        |               |
        v               v
     Software          Core
        |               |
        v               v
 Software Approval   Core Approval
        |               |
        +-------+-------+
                |
                v
        Approval Completed
                |
                v
      Create 3 Parallel Tasks
                |
        +-------+-------+
        |       |       |
        v       v       v
      Task 1  Task 2  Task 3
```
## 22. Technologies and ServiceNow Features
### Platform
- ServiceNow
### ServiceNow Modules
- Service Catalog
- Service Portal
- Flow Designer
- SLA
- Incident Management
### Configuration
- Catalog Items
- Variable Sets
- Catalog Variables
- Catalog Client Scripts
- Catalog UI Policies
- Order Guides
- Record Producers
- SLA Definitions
- Flow Designer Actions
- Approvals
- Catalog Tasks
### Scripting
- JavaScript
- ServiceNow Glide APIs
- Regular Expressions
- g_form
- producer
- current
- gs
## 23. Key Skills Demonstrated

This project demonstrates practical experience with:
- ServiceNow Service Catalog configuration
- Catalog item creation
- Variable and variable-set configuration
- Client-side validation
- JavaScript scripting
- Regular expressions
- Dynamic catalog fields
- Mandatory field handling
- Service Portal configuration
- Order Guide configuration
- Record Producer development
- Incident creation
- SLA configuration
- Flow Designer automation
- Conditional approval routing
- Parallel task creation
- Request and Requested Item management
## 24. Project Outcome

The completed application provides an automated onboarding workflow where users can submit employee onboarding requirements through the Service Portal.

The system:

- Collects employee information.
- Validates user inputs.
- Dynamically displays required fields.
- Provides software and hardware requirements.
- Creates incidents for service-related issues.
- Creates onboarding requests and requested items.
- Applies SLA tracking.
- Routes requests for appropriate approval.
- Generates multiple catalog tasks in parallel after approval.
<!-- for pull shark badge bruhhh -->
