# Lab 5: ArcGIS Collector

## TGIS 504, Winter 2022, Dr. Emma Slager

### Introduction

While Lab 4 introduced you to Esri's form-centric data collection tool--Survey123--this part of the lab will introduce you to Esri's map-centric data collection tool, Collector. Like Survey123, Collector is available as a mobile app. Field teams can download surveys to the Collector app on their mobile devices to use in the field and use them to update information about existing features or collect data about new features. If a data connection is available in the field, data can be uploaded immediately, or it can be stored to be uploaded when a connection to the Internet is restored. Collector supports any kind of vector feature--point, line, or polygon--and is integrated with ArcGIS Online services to support data storage and map display/interactivity.

Set up for Collector is a bit more complicated than for Survey123, and involves creating a geodatabase and an editable feature service with desktop GIS software rather than using a web interface. The general steps for this lab are as follows:

- Plan data to be collected
- Build a geodatabase and feature classes for your Collector instance
- Build and share a map to be used in the Collector instance
- Download the Collector app and your Collector instance
- Collect sample data
- Write a brief reflective essay

*Technology stack for this lab:*

- ArcMap Desktop (or ArcPro, though directions here are written for ArcMap)
  - ArcMap and ArcPro are both available through the Remote Lab if you do not have this installed on your personal computer. Instructions for accessing the remote lab are posted to the Canvas weekly page. 
  - If you prefer to use ArcPro, I suggest using [these instructions](https://doc.arcgis.com/en/collector/android-phone/help/prepare-layer.htm) rather than the ones linked in Parts 2 & 3 below. The instructions are structured slightly differently, so I will leave it to you to navigate them using the sidebar menu.
- ArcGIS Online, accessed by computer
- ArcGIS Collector, downloaded to and accessed by mobile device
- Submission on Canvas

### Part 1: Identifying a data collection scenario and planning your data entry

For this lab, you'll be building a Collector instance that could be used by Metro Parks Tacoma staff to create and maintain an inventory of parks, their features, and their maintenance needs. This will include:

- a polygon layer for each park with associated attributes like name, area, and the date of its last inventory survey
- a point layer for bathroom facilities with associated attributes about whether the bathroom needs to be cleaned or repaired
- a line layer for paths and associated attributes such as whether the path is paved or not
- three additional layers of your choosing and design

As you'll see in part 2 of the instructions, creating the geodatabase that will hold these layers is made much easier by having planned your data organization in schema tables ahead of time. See below for examples of the feature layers we'll create for the first three layers, then use the blank table template (available on GitHub as a Word doc) to plan out the additional layers you will create.

**Notes about schemas**

- The *feature class* can be point, line, or polygon and you should give it a descriptive name.
- The *field name* should have no spaces or special characters (underscores are OK). Each question in your survey represents a field. Each field will eventually be column in the feature class's attribute table.
- The *alias* should be a human readable version of the field name.
- The *type* can include:
  - Date—Date (and time)
  - Float – Numbers with fractional values (decimals), precise to six decimal places
  - Double—Numbers with fractional values (decimals), precise beyond six decimal places but requiring larger storage capacity than float fields
  - Short Integer—Whole numbers from -32,768 to 32,767
  - Long Integer—Whole numbers from -2,147,483,648 to 2,147,483,647, requiring larger storage capacity than short integer fields
  - Text/String—Any sequence of characters
  - Blob—Handles photos
- *Length* is the number of allowed characters in the field's value. This must be specified for any string/text field.
- *Allow Null* must be either true or false. If true, it means this field can be left blank.
- *Domain* is ArcMap's term for a list of choices for users to input data. Providing dropdown choices can be very useful for limiting user error and aiding standardization, but it can also be limiting, so you need to ensure you provide choices for the full range of possible answers. Not every field will utilize a domain, but those that do should be specified in the second Domain table. Domains can be re-used; for instance if you have multiple yes/no questions, they can all use the same YesNo domain.
- *Notes* are any additional information about the question you want to provide to the surveyor.
- NA stands for "Not Applicable"; ADA stands for "Americans with Disabilities Act"

| **Feature Class: ParkBoundaries, Feature type: polygon** |                     |          |            |            |                     |                |
| -------------------------------------------------------- | ------------------- | -------- | ---------- | ---------- | ------------------- | -------------- |
| **Field Name**                                           | **Alias**           | **Type** | **Length** | **Domain** | **Notes**           | **Allow Null** |
| ParkName                                                 | Name of the Park    | String   | 100        | NA         |                     | False          |
| ParkArea                                                 | Area of the park    | Float    | NA         | NA         | Calculated in acres | False          |
| LastSurvey                                               | Date of last survey | Date     | NA         | NA         |                     | True           |

| Feature Class: Bathrooms, Feature Type: point |                                             |          |            |               |                                                              |                |
| --------------------------------------------- | ------------------------------------------- | -------- | ---------- | ------------- | ------------------------------------------------------------ | -------------- |
| **Field Name**                                | **Alias**                                   | **Type** | **Length** | **Domain**    | **Notes**                                                    | **Allow Null** |
| GenderType                                    | Is the bathroom limited to certain genders? | String   | 50         | GenderOptions |                                                              | false          |
| StallType                                     | Is the bathroom multi-stall or single stall | String   | 50         | StallOptions  |                                                              | false          |
| ADA_Accessible                                | Is the bathroom ADA accessible?             | String   | 50         | YesNo         |                                                              | false          |
| NeedsCleaning                                 | Does the bathroom need to be cleaned?       | String   | 50         | YesNo         |                                                              | false          |
| NeedsSupplies                                 | Does the bathroom need supply refills?      | String   | 50         | YesNo         | Supplies include toilet paper, hand soap, or paper towels    | false          |
| NeedsRepairs                                  | Does the bathroom need repairs?             | String   | 50         | YesNo         | May include plumbing, electrical, or structural repairs      | false          |
| Photo                                         | NA                                          | Blob     | NA         | NA            | Attach a photo of any maintenance issues that need to be addressed | true           |

| **Feature Class: Paths, Feature Type: line** |                                 |          |            |                |                                                              |                |
| -------------------------------------------- | ------------------------------- | -------- | ---------- | -------------- | ------------------------------------------------------------ | -------------- |
| **Field Name**                               | **Alias**                       | **Type** | **Length** | **Domain**     | **Notes**                                                    | **Allow Null** |
| SurfaceType                                  | Type of path surface            | String   | 50         | SurfaceOptions |                                                              | false          |
| NeedsMaintenance                             | Does the path need maintenance? | String   | 50         | YesNo          | May include snow or debris removal or repaving.              | false          |
| ADA_Accessible                               | Is the path ADA accessible      | String   | 50         | YesNo          | Consult ADA guidelines regarding width, slope, etc.          | false          |
| Photo                                        | NA                              | Blob     | NA         | NA             | Attach a photo of any maintenance issues that need to be addressed | true           |

Domain Table:

| **Domain Name** | **Option List**                | Option Aliases                 |
| --------------- | ------------------------------ | ------------------------------ |
| GenderOptions   | AllGender, Men, Women          | All Gender, Men, Women         |
| StallOptions    | MultiStall, SingleStall        | Multi-Stall, Single Stall      |
| YesNo           | Yes, No, Unknown               | Yes, No, Unknown               |
| SurfaceOptions  | Paved, Dirt, Gravel, Woodchips | Paved, Dirt, Gravel, Woodchips |

Using the template provided, produce similar tables for the three additional feature layers you will include in your survey. Be sure to specify a feature type (point, line, or polygon). Your additional feature layers may be for features such as park benches, playgrounds, etc. You must decide what attributes to collect for each feature class. Add any dropdown options you will need to a domain in the Domain Table.

### Part 2: Build a geodatabase to store the data your users will gather with Collector

Before we can start gathering data with Collector, we need to build the geodatabase that will hold the data once it is collected. Your geodatabase should have a structure based on the data schema you mapped out in Part 1. It needs to include a feature class for every class of feature your user will collect (point, line, or polygon) as well as fields for all of the tabular information you want to collect about each feature.

Using your data schema, build your geodatabase, define your domains, and create your feature classes in ArcMap Desktop. Reference the directions found in this tutorial: https://doc.arcgis.com/en/collector/android-phone/help/prepare-layer.htm. (We will also have gone through this tutorial together in class.) 

To import the existing data we have for the ParkBoundaries layer, follow these steps, which are not covered in the tutorial above: 

1. Open Catalog and navigate to the location where your geodatabase is stored. 
2. Right-click on the geodatabase >> Import >> Feature class (single)
3. In the window that appears enter the following: 
   * Input features: navigate to the location where the Parks layer you downloaded from GitHub is stored. You will need to extract this from the Zip you downloaded before it is available to access. 
   * Output location: this should by default already be set to the Geodatabase you set up for this lab, but confirm that is true and correct it if not. 
   * Output Feature Class: name this ParkBoundaries
   * Expression and Field Map can be left unchanged
   * Click OK
4. After a moment of processing, the ParkBoundaries layer should appear in your TOC. Right-click and select 'Zoom to Layer' to ensure it appears as expected. Its datum should already be set to WGS84, but you can confirm this by examining the Source tab in the Layer Properties. 

After you've created all of the feature classes you need (this should be 6 total: the ParkBoundaries layer, the Bathrooms layer, the Paths layer, and 3 layers of your choosing and design), symbolize your data with colors and symbols that are appropriate for the data that will be collected. Using the instructions in the tutorial linked above, share your feature service online on your UWT ArcGIS Online account. Make sure your service is editable and that you know the URL by which you can reference it.

***Some important notes***

- For the ParkBoundaries feature class, we will import existing data rather than build the feature class from scratch. If you are not present in class to do this with the instructor, please view the relevant portion of the class recording before continuing. You can find a zip folder containing the shapefile we will use for the import on GitHub.
- You must define your domains before you create any feature classes that use those domains for drop-down options. This is counter-intuitive to many users, but it is important.
- Photos are not actually included as fields (i.e. not columns in an attribute table) but as attachments. See the class recording and linked instructions from ArcGIS.com for a reminder of how to enable attachments on a given feature class.
- Before publishing your service, make sure that your service is shared with this year's MSGT cohort on the 'Sharing' tab of the 'Service Editor' window.

### Part 3: Create a map for data collection

The next step to implementing a Collector instance is to build a map to customize the user’s experience of data collection further. ArcGIS Online is in a period of technology transition (bless their hearts), so there are two interfaces and two sets of instructions for this, and you get to choose one. 

* For the older interface (likely easier to follow if you used Desktop to create your feature service), [follow these directions](https://doc.arcgis.com/en/collector-classic/windows/create-maps/create-and-share-a-collector-map.htm). If you use this interface, be sure to click 'Open in map Viewer Classic' after signing in and entering the Map Viewer. Use your own file geodatabase in place of the Damage Assessment layer referenced in the directions and make choices specific to your scenario as suggested below.
* For the newer interface (likely easier to follow if you used Pro to create your feature service), [follow these directions](https://doc.arcgis.com/en/collector/android-phone/help/make-map.htm). 

Whichever interface you use, *include your name in your map’s title, as I will need this information when I grade.*

Customize your map in the following ways, and document the customizations you made to include in your lab write-up:

- Use a basemap that is appropriate for your data collection scenario and purpose. Will your user likely collect data in bright light? Use a high contrast base map. Will your user need street-level detail or will a generalized basemap work? Would aerial imagery be useful to your user? Will the symbolization you created for your feature classes appear clearly on the basemap you selected? Make your choice based on these and other factors specific to your project.
- Configure popup windows in ways that are appropriate for your application. Most simply, you can customize how each feature’s title appears, change the alias for any of the data fields, and select and de-select fields to include in the popup window. For more advanced customizations (not necessary for this lab, but for future reference): might your user want to do math based on attribute information collected (for example, calculate how many fire hazards out of all possible hazards are present at the location to draw attention to how much preparedness improvement needs to be undertaken)? Will your user collect data that can be visualized in a chart or graph on the fly? This is possible!
- Customize application settings. Set an appropriate default extent. Make sure that the option to ‘Use in Collector for ArcGIS’ is checked.

Once you have built your map and customized it appropriately, follow the instructions at the link above and share it with the cohort via the UWT ArcGIS organization. Again, *please include your name in your map’s title, as I will need this information when I grade.*

### Part 4: Test your app and collect sample data

In the Google Play Store or Apple App Store, download the ArcGIS Collector app on your mobile device. Log in using your UWT credentials, and navigate to your map. Download it, open it and test out data collection in Collector using your app.

First, collect some new data that doesn't currently exist in the database. You should collect at least one sample feature from each of your feature classes, except the ParkBoundaries layer. You may collect these sample data in "the lab" (your home), or, if you are located near any Tacoma Metro Parks locations, you can collect them in the field. If you collect your samples from the lab/your home, still be sure to place your sample points inside the boundary of one of the existing parks. If you opt for field collection, please be safe and follow all the usual precautions. For the purposes of this lab, these do not need to be actually existing features, since, if you are collecting data from your home you may or may not know where a bathroom, path, etc. actually exists in a given park.  

For the ParkBoundaries layer, instead of collecting *new* data, update at least one existing feature. To do this, click on a Park to update, click the 'Edit' icon (looks like a pencil, should be in the bottom left corner of the screen), click the LastSurvey field and update the date the to current date and time. Click 'Submit' in the upper right corner without making any other changes.

To learn more about configuring Collector as a user visit this link: https://doc.arcgis.com/en/collector/android-phone/help/configure-collector.htm. For guidance on using Collector, see the Quick Reference Guide at https://doc.arcgis.com/en/collector/android-phone/help/quick-reference.htm

Any edits you submit should be automatically uploaded. Log on to ArcGIS Online and view your map there to ensure that your edits were saved and submitted.

### Part 5: Brief reflection

Please provide responses to the following:

1. What is the name of your Collector map?
2. Please describe the customization choices you made in designing the map you made for use in Collector. How did you choose to symbolize the layers and why? What base map, pop-up window, and application setting choices did you make, and why did you make them?
3. In the previous lab and this one, you used both ArcGIS Collector and Survey123 for ArcGIS. As the developer, describe your experience of working with these tools. How easy and difficult were the two systems to set up? What were their advantages and disadvantages? Repeat this process to compare the two systems from the perspective of the user/data collector. This should be a ~2 paragraph narrative to reflect on the process of both developing and using Collector and Survey123. I encourage you to use vocabulary about design and evaluation that we've used in class and in readings.

### Deliverables

- Your completed data schema tables from Part 1 (MS Word template available on GitHub)
- Reflection responses to the questions in Part 5, including the name of your Collector map, which I will access via the app and use to conduct my assessment.