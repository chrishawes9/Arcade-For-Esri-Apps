# NFFMC Division Overview Dashboard

The NFFMC District Overview Dashboard provides summary totals of materiel at the Division and District level. Primary functionality is through use of a category selector that filters a number of list and indicator widgets. One data expression is used to support customer requirements.

## District Materiel & Supplies as a percent of Division Materiel & Supplies
```js
// Write an expression that returns a FeatureSet.
// Documentation: https://arcg.is/3c419TD
// Samples: https://arcg.is/38SEWWz

var uCOP = portal("https://arcportal-ucop-corps.usace.army.mil/s0portal");

var uDIS = FeatureSetByPortalItem(uCOP, 
'8a16765777c44e8f9263323ddd463028', 
1, 
["*"], false);

// Create FeatureSet with just Divisions to iterate through
var udiv = Distinct(uDIS, 'USACE_DIVISION_CODE')

// Set up feature object and array to hold multiple feature objects
var features = []
var feat

// Iterate through FeatureSet created above
for (var d in udiv) {
    // Create string variable with one Division Code
    var divID = d['USACE_DIVISION_CODE']
    
    // Using the Division code filter the District FeatureSet
    var fdis = Filter(uDIS, 'USACE_DIVISION_CODE = @divID')
    
    // Create a feature set that is a division total
    var distot = GroupBy(fdis, ['USACE_OFFICE_TYPE'], [
        {name: 'totsbag', expression: 'SANDBAG_CT', statistic: 'SUM'},
        {name: 'totalbag', expression: 'AIRLIFT_SANDBAG_CT', statistic: 'SUM'},
        {name: 'totssbag', expression: 'SUPER_SANDBAG_CT', statistic: 'SUM'},
        {name: 'totsbfill', expression: 'SANDBAG_FILL_MACHINE_CT', statistic: 'SUM'},
        {name: 'totpsht', expression: 'POLY_SHEETING_LEN', statistic: 'SUM'},
        {name: 'totgbkt', expression: 'GABION_BASKET_LEN', statistic: 'SUM'},
        {name: 'totpcfd', expression: 'PORT_COFFER_DAM_LEN', statistic: 'SUM'},
        {name: 'totpmps', expression: 'PUMP_TOTAL_CT', statistic: 'SUM'}])
    
    // Extract single feature from divsion total featureset
    var divfeat = First(distot)
    
    // Create a bunch of single value variables for math
    var sbag = divfeat.totsbag
    var albag = divfeat.totalbag
    var ssbag = divfeat.totssbag
    var sbfill = divfeat.totsbfill
    var psht = divfeat.totpsht
    var gbkt = divfeat.totgbkt
    var pcfd = divfeat.totpcfd
    var pmps = divfeat.totpmps
    
    for (var s in fdis) {
        feat = {
            attributes: {
                DISTRICT: s['USACE_DISTRICT_CODE'],
                DIVISION: s['USACE_DIVISION_CODE'],
                SANDBAG_CT: s['SANDBAG_CT'],
                SANDBAG_PCT: round((s['SANDBAG_CT']/sbag)*100,1),
                AIRLIFT_SANDBAG_CT: s['AIRLIFT_SANDBAG_CT'],
                AIRLIFT_SANDBAG_PCT: round((s['AIRLIFT_SANDBAG_CT']/albag)*100,1),
                SUPER_SANDBAG_CT: s['SUPER_SANDBAG_CT'],
                SUPER_SANDBAG_PCT: round((s['SUPER_SANDBAG_CT']/ssbag)*100,1),
                SANDBAG_FILL_MACHINE_CT: s['SANDBAG_FILL_MACHINE_CT'],
                SANDBAG_FILL_MACHINE_PCT: round((s['SANDBAG_FILL_MACHINE_CT']/sbfill)*100,1),
                POLY_SHEETING_LEN: s['POLY_SHEETING_LEN'],
                POLY_SHEETING_PCT: round((s['POLY_SHEETING_LEN']/psht)*100,1),
                GABION_BASKET_LEN: s['GABION_BASKET_LEN'],
                GABION_BASKET_PCT: round((s['GABION_BASKET_LEN']/gbkt)*100,1),
                PORT_COFFER_DAM_LEN: s['PORT_COFFER_DAM_LEN'],
                PORT_COFFER_DAM_PCT: round((s['PORT_COFFER_DAM_LEN']/pcfd)*100,1),
                PUMP_TOTAL_CT: s['PUMP_TOTAL_CT'],
                PUMP_TOTAL_PCT: round((s['PUMP_TOTAL_CT']/pmps)*100,1)
            }
        }
        push(features, feat)
    }
}

var combinedDict = {
    fields: [
        {name: 'DISTRICT', type: 'esriFieldTypeString'},
        {name: 'DIVISION', type: 'esriFieldTypeString'},
        {name: 'SANDBAG_CT', type: 'esriFieldTypeInteger'},
        {name: 'SANDBAG_PCT', type: 'esriFieldTypeDouble'},
        {name: 'AIRLIFT_SANDBAG_CT', type: 'esriFieldTypeInteger'},
        {name: 'AIRLIFT_SANDBAG_PCT', type: 'esriFieldTypeDouble'},
        {name: 'SUPER_SANDBAG_CT', type: 'esriFieldTypeInteger'},
        {name: 'SUPER_SANDBAG_PCT', type: 'esriFieldTypeDouble'},
        {name: 'SANDBAG_FILL_MACHINE_CT', type: 'esriFieldTypeInteger'},
        {name: 'SANDBAG_FILL_MACHINE_PCT', type: 'esriFieldTypeDouble'},
        {name: 'POLY_SHEETING_LEN', type: 'esriFieldTypeInteger'},
        {name: 'POLY_SHEETING_PCT', type: 'esriFieldTypeDouble'},
        {name: 'GABION_BASKET_LEN', type: 'esriFieldTypeInteger'},
        {name: 'GABION_BASKET_PCT', type: 'esriFieldTypeDouble'},
        {name: 'PORT_COFFER_DAM_LEN', type: 'esriFieldTypeInteger'},
        {name: 'PORT_COFFER_DAM_PCT', type: 'esriFieldTypeDouble'},
        {name: 'PUMP_TOTAL_CT', type: 'esriFieldTypeInteger'},
        {name: 'PUMP_TOTAL_PCT', type: 'esriFieldTypeDouble'}
    ],
    geometryType: '',
    features: features,
}

return FeatureSet(Text(combinedDict))
```