# NFFMC Division Overview Dashboard

The NFFMC Division Overview Dashboard provides summary totals of materiel at the USACE and Division level. Primary functionality is through use of a category selector that filters a number of list and indicator widgets. Two data expressions are used to support customer requirements.

## Additional Pump Breakdown Categories

PTO and non-PTO pump counts were added by the data expression below, this expression is used in the Division Summary list widget

```js
// uCOP Prod, ArcGIS Enterprise 11.2, Feb. 2024
// Create two additional pump statistics (PTO, non-PTO) 
// For use in ArcGIS Dashboard list widget
// Returns feature set

// Define portal and web feature layer
var uCOP = portal("https://arcportal-ucop-corps.usace.army.mil/s0portal");

var uDIV = FeatureSetByPortalItem(uCOP, 
'8a16765777c44e8f9263323ddd463028', 
0, 
["*"], false);

// Set up feature object and array to store multiple feature objects
var features = [];
var feat;

// Loop through rows in feature set, keeping required columns and creating 
// new columns and values for PTO and non-PTO pump categories
for (var p in uDIV) {
    var divID = p["USACE_DIVISION_CODE"]
    var oNAME = p["USACE_OFFICE_NAME"]
    var sBAG = p["SANDBAG_CT"]
    var ssBAG = p["SUPER_SANDBAG_CT"]
    var aBAG = p["AIRLIFT_SANDBAG_CT"]
    var gBAS = p["GABION_BASKET_LEN"]
    var pSHT = p["POLY_SHEETING_LEN"]
    var tPMP = p["PUMP_TOTAL_CT"]
    var pCFD = p["PORT_COFFER_DAM_LEN"]
    feat = {
        attributes: {
            Division: divID,
	    Office_Name: oNAME,
	    Sandbag_CT: sBAG,
	    Airlift_Sandbag_CT: aBAG,
	    Super_Sandbag_CT: ssBAG,
	    Gabion_Basket_LNFT: gBAS,
	    Poly_Sheeting_LNFT: pSHT,
	    Portable_Coffer_Dam_LNFT: pCFD,
	    Total_Pumps_CT: tPMP,
            PTO_Pumps_CT: sum(p["PUMP_8_IN_PTO_CT"], 
                               p["PUMP_10_IN_PTO_CT"], 
                               p["PUMP_12_IN_PTO_CT"], 
                               p["PUMP_16_IN_PTO_CT"],
    			       p["PUMP_16_IN_PTO_DIESEL_CT"],
    			       p["PUMP_24_IN_PTO_DIESEL_CT"]),
            Non_PTO_Pumps_CT: sum(p["PUMP_3_IN_GAS_CT"],
                                  P["PUMP_4_IN_DIESEL_CT"],
                                  p["PUMP_4_IN_SUB_ELECTRIC_CT"],
                                  p["PUMP_4_IN_SUB_HYDRAULIC_CT"],
    			          p["PUMP_6_IN_DIESEL_CT"],
    			          p["PUMP_6_IN_FLOAT_GAS_CT"],
    			          p["PUMP_6_IN_SUB_DIESEL_CT"],
    			          p["PUMP_8_IN_DIESEL_CT"],
    			          p["PUMP_8_IN_SUB_ELECTRIC_CT"],
    			          p["PUMP_12_IN_DIESEL_CT"],
    			          p["PUMP_18_IN_DIESEL_CT"])
            }
        }
    Push(features, feat)
};

// create dictionary from array of feature objects 
var outDict = {
    fields: [
    {name: "Division", type: "esriFieldTypeString"},
	{name: "Office_Name", type: "esriFieldTypeString"},
	{name: "Sandbag_CT", type: "esriFieldTypeInteger"},
	{name: "Airlift_Sandbag_CT", type: "esriFieldTypeInteger"},
	{name: "Super_Sandbag_CT", type: "esriFieldTypeInteger"},
	{name: "Gabion_Basket_LNFT", type: "esriFieldTypeInteger"},
	{name: "Poly_Sheeting_LNFT", type: "esriFieldTypeInteger"},
	{name: "Portable_Coffer_Dam_LNFT", type: "esriFieldTypeInteger"},
	{name: "Total_Pumps_CT", type: "esriFieldTypeInteger"},
    {name: "PTO_Pumps_CT", type:"esriFieldTypeInteger"},
    {name: "Non_PTO_Pumps_CT", type:"esriFieldTypeInteger"}],
    'geometryType': '',
    'features':features
};

// Convert dict to feature set
return FeatureSet(Text(outDict))
```

The FeatureSet returns a table where each row is a Division and associated materiel category counts and lengths.

## USACE Total Materiels

To complement the Division totals, a USACE-wide data expression was created. It includes the addition of the PTO and non-PTO counts.

```js
// uCOP Prod, ArcGIS Enterprise 11.2, Feb. 2024
// Create USACE NFFMC Material Totals from Division totals
// For use in ArcGIS Dashboard list widget
// Returns Feature Set

// Define portal and web feature layer
var uCOP = portal("https://arcportal-ucop-corps.usace.army.mil/s0portal");

var uDIV = FeatureSetByPortalItem(uCOP, 
'8a16765777c44e8f9263323ddd463028', 
0, 
["*"], false);

// Set up feature object and array to store multiple feature objects
var features = [];
var feat;

// Loop through rows in feature set, keeping required columns and creating 
// new columns and values for PTO and non-PTO pump categories
for (var p in uDIV) {
    var divID = p["USACE_DIVISION_CODE"]
    var oNAME = p["USACE_OFFICE_NAME"]
    var oTYPE = p["USACE_OFFICE_TYPE"]
    var sBAG = p["SANDBAG_CT"]
    var ssBAG = p["SUPER_SANDBAG_CT"]
    var aBAG = p["AIRLIFT_SANDBAG_CT"]
    var gBAS = p["GABION_BASKET_LEN"]
    var pSHT = p["POLY_SHEETING_LEN"]
    var tPMP = p["PUMP_TOTAL_CT"]
    var pCFD = p["PORT_COFFER_DAM_LEN"]
    feat = {
        attributes: {
            Division: divID,
	    Office_Name: oNAME,
	    Office_Type: oTYPE,
	    Sandbag_CT: sBAG,
	    Airlift_Sandbag_CT: aBAG,
	    Super_Sandbag_CT: ssBAG,
	    Gabion_Basket_LNFT: gBAS,
	    Poly_Sheeting_LNFT: pSHT,
	    Portable_Coffer_Dam_LNFT: pCFD,
	    Total_Pumps_CT: tPMP,
            PTO_Pumps_CT: sum(p["PUMP_8_IN_PTO_CT"], 
                               p["PUMP_10_IN_PTO_CT"], 
                               p["PUMP_12_IN_PTO_CT"], 
                               p["PUMP_16_IN_PTO_CT"],
    			       p["PUMP_16_IN_PTO_DIESEL_CT"],
    			       p["PUMP_24_IN_PTO_DIESEL_CT"]),
            Non_PTO_Pumps_CT: sum(p["PUMP_3_IN_GAS_CT"],
                                  P["PUMP_4_IN_DIESEL_CT"],
                                  p["PUMP_4_IN_SUB_ELECTRIC_CT"],
                                  p["PUMP_4_IN_SUB_HYDRAULIC_CT"],
    			          p["PUMP_6_IN_DIESEL_CT"],
    			          p["PUMP_6_IN_FLOAT_GAS_CT"],
    			          p["PUMP_6_IN_SUB_DIESEL_CT"],
    			          p["PUMP_8_IN_DIESEL_CT"],
    			          p["PUMP_8_IN_SUB_ELECTRIC_CT"],
    			          p["PUMP_12_IN_DIESEL_CT"],
    			          p["PUMP_18_IN_DIESEL_CT"])
            }
        }
    Push(features, feat)
};

// create dictionary from array of feature objects 
var outDict = {
    fields: [
    {name: "Division", type: "esriFieldTypeString"},
	{name: "Office_Name", type: "esriFieldTypeString"},
	{name: "Office_Type", type: 'esriFieldTypeString"},
	{name: "Sandbag_CT", type: "esriFieldTypeInteger"},
	{name: "Airlift_Sandbag_CT", type: "esriFieldTypeInteger"},
	{name: "Super_Sandbag_CT", type: "esriFieldTypeInteger"},
	{name: "Gabion_Basket_LNFT", type: "esriFieldTypeInteger"},
	{name: "Poly_Sheeting_LNFT", type: "esriFieldTypeInteger"},
	{name: "Portable_Coffer_Dam_LNFT", type: "esriFieldTypeInteger"},
	{name: "Total_Pumps_CT", type: "esriFieldTypeInteger"},
    {name: "PTO_Pumps_CT", type:"esriFieldTypeInteger"},
    {name: "Non_PTO_Pumps_CT", type:"esriFieldTypeInteger"}],
    'geometryType': '',
    'features':features
};

// Convert dict to feature set
var pumpType = FeatureSet(Text(outDict));

// Using group by, generate totals of materiel, return feature set
return GroupBy(pumpType, ['Office_Type'],
[{name: "Sandbag_CT", expression: "Sandbag_CT", statistic: "SUM"},
{name: "Airlift_Sandbag_CT", expression: "Airlift_Sandbag_CT", statistic: "SUM"},
{name: "Super_Sandbag_CT", expression: "Super_Sandbag_CT", statistic: "SUM"},
{name: "Gabion_Basket_LNFT", expression: "Gabion_Basket_LNFT", statistic: "SUM"},
{name: "Poly_Sheeting_LNFT", expression: "Poly_Sheeting_LNFT", statistic: "SUM"},
{name: "Portable_Coffer_Dam_LNFT", expression: "Portable_Coffer_Dam_LNFT", statistic: "SUM"},
{name: "PTO_Pumps_CT", expression: "PTO_Pumps_CT", statistic: "SUM"},
{name: "Non_PTO_Pumps_CT", expression: "Non_PTO_Pumps_CT", statistic: "SUM"},
{name: "Total_Pumps_CT", expression: "Total_Pumps_CT", statistic: "SUM"}])
```

The feature set returns a single row table with USACE wide materiel category counts and lengths totals.
