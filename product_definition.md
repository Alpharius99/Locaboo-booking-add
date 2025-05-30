# Locaboo Booking Tool - Product Definition

## Overview
A tool designed to automate the process of creating Locaboo bookings from an Excel worksheet containing a standard weekly schedule. 
The tool processes a 2D matrix representation of bookings and creates corresponding entries in the Locaboo system.

## Core Functionality

### Input Processing
- Read Excel worksheet containing weekly schedule
- Starting row and column are set in the configuration
- Last column and row of the matrix are set in the configuration
- Process a 2D matrix where:
  - Columns represent weekdays (Monday through Sunday)
  - The weekday names are set in the rows 5 and 6 (merged cells)
  - The weekday names are given in German starting with Monday (Montag)
  - The weekday names are mapped to three-letter code ("mon" = Monday, 6 = Sunday)
  - Each weekday has two columns (representing two Locaboo resources)
  - The resource names are set in rows 3 and 4 (merged cells)
  - The resource names are mapped to their ID's (integer values)
  - The weekdays are separated with an empty column filled with black
  - The weekday separating column is skipped
  - Rows represent 15-minute time slots
  - The starting time for the slot/row is set in column A in format H:mm
  - Bookings are represented by colored cells with text content
  - Multiple adjacent cells with the same color represent a single booking
  - In the case of multi-cell bookings
    - The start time is taken from the first cell of the row
    - The end time is calculated by adding the booking duration to the start time
    - THe booking slot duration as calculated by multiplying the number of booking cells with 15 minutes (4 cells = 60 minutes)
  - In case of single-cell booking
    - The start time is taken from the first cell of the row
    - The end time is calculated by adding 15 minutes to the start time
  - The color to customer mapping is set in the configuration
  - The bookings are separated by borderlines
  - The cell texts of all booking cells are combined to the booking description
  - The cell texts containing times are skipped ("HH:mm-HH:mm" and "HH:mm - HH:mm")
  - Bookings with no text are skipped
  - Bookings with color fill not set in the color to customer mapping are skipped
  - The input functionality is encapsulated in one class
  - The input class has a public method to deliver a list of dictionaries, which contain booking data:
    - resource ID
    - description
    - customer ID
    - weekday ID
    - start time
    - end time
- The class logs all detection, parsing and sorting activities
- All errors and exceptions are handled by the exception handling class

### Booking Creation
- The booking creation functionality is encapsulated in one class
- The booking creation class has a public factory method to create a dictionary to fill with all relevant booking data (booking container)
- The outcome booking container contains following keys with values:
  - "booking_mode" = "direct_booking" (constant value)
  - "title" = description
  - "customer_id" = customer ID
  - "recurring" = 1 (constant value)
  - "date_from" = start day from the configuration (format YYYY-MM-DD)
  - "date_to" = end day from the configuration (format YYYY-MM-DD)
  - "recurrence_type" = "week" (constant value)
  - "weekly_type_select" = "week" (constant value)
  - an array named "weekly_recurrence" with the following key/value pairs
    - "day" = weekday code ("mon" for Monday)
    - "from" = start day ("HH:mm", 24h)
    - "to" = start day ("HH:mm", 24h)
  - "internal_comments" = "Imported at <timestamp>, cells <cells in the Excel sheet>" (format HH:mm:ss DD:MM:YYYY)
- The class logs the resulted dictionary
- All errors and exceptions are handled by the exception handling class

### Booking Adding to Locaboo
- Use Locaboo API endpoint https://app.locaboo.com/api/v2/booking_add
- The API secret key is a query parameter
- The API functionality is encapsulated in one class
- The class offers a public method to add a booking over API
- This public method accepts a booking dictionary, a secret key
- The class logs all calls and responses
- All errors and exceptions are handled by the exception handling class
- A development mode activated by a flag is provided. If this mode is active, no API call is made. The request is logged.

### Logging
- Configured the logging
  - Terminal with INFO level
  - File with DEBUG level
  - File is rewritten with every start
  - Every log entry contains
    - time stamp (YYYY-MM-DD HH:mm:ss)
    - log level
    - message
- The logging functionality is provided for all classes in the project
- The logging functionality is encapsulated in a class
  - time stamp (YYYY-MM-DD HH:mm:ss)
  - log level (ERROR)
  - calling class
  - calling method
  - line in the code
  - message

### Exception handling
- Provide general exception handling for all classes in project
- The error message is written to the log using

### Controller
- The central script to control all functionality
- Use all other classes by instantiating and calling their methods
- Logging of all activities
- All errors and exceptions are handled by the exception handling class

## Technical Requirements

### Excel Processing
- Support for standard Excel formats (.xlsx, .xls)
- Color mapping configuration for customer identification
- Text extraction from cells
- Adjacent cell detection for booking duration calculation

### Locaboo Integration
- Integration with Locaboo API
- Authentication handling with the API key
- Booking creation with proper error handling
- Support for multiple resources per weekday
- Validation of booking parameters before creation

### Configuration
- Customer color mapping configuration
- Resource ID mapping for each column
- Time slot configuration (start time, interval duration)
- Error handling and logging configuration

## Dependencies
- Python 3.x
- Locaboo API client
- Excel processing library
- Configuration management system
- Logging framework

## Success Criteria
1. Accurate booking creation from Excel data
2. Proper handling of multi-cell bookings
3. Correct customer assignment based on colors and borderlines
4. Successful resource allocation
5. Comprehensive error reporting
6. User-friendly using
7. Reliable performance with standard weekly schedules 