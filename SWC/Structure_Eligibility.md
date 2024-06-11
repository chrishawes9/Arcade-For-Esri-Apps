# SWC Structure Eligibility Dashboard

This dashboard provides a high level overview of structure eligibility for the project. It is focused on overall structure eligibility to participate in the project.

## Combined Elig and Inelig

This data expression pivots a two categorical variables into columns and sums structure counts for each category based on a eligible/ineligible condition

```js
// Dump out a table with elegibility types by priority groups

// Define portal and web feature layer
var uCOP = portal("https://arcportal-ucop-corps.usace.army.mil/s0portal/")

// Create a limited feature set from structure inventory
var uApps = FeatureSetByPortalItem(uCOP,
'2116175041e045ea9a4f0fdfb5036024',
0,
["priority_group", "project_eligibility_status", "project_ineligibility_type", "structure_id"],false)

// Using group by 
var elig = GroupBy(uApps,
  [{name: 'Priority Group', expression: 'priority_group'}],
  [{name: 'Eligible', expression: "CASE WHEN project_eligibility_status = 'Eligible' THEN 1 ELSE 0 END", statistic: 'SUM'},
   {name: 'Perm. Ineligible', expression: "CASE WHEN project_eligibility_status = 'Permanently Ineligible' THEN 1 ELSE 0 END", statistic: 'SUM'},
   {name: 'Temp. Ineligible', expression: "CASE WHEN project_eligibility_status = 'Temporarily Ineligible' THEN 1 ELSE 0 END", statistic: 'SUM'},
   {name: 'Total Structures', expression: "structure_id", statistic: 'COUNT'},
   {name: 'Ineligible: Elevated', expression: "CASE WHEN project_ineligibility_type = 'Structure is elevated' THEN 1 ELSE 0 END", statistic: 'SUM'},
   {name: 'Ineligible: Not A Residence', expression: "CASE WHEN project_ineligibility_type = 'Structure is not a residence' THEN 1 ELSE 0 END", statistic: 'SUM'},
   {name: 'Ineligible: No Structure On Site', expression: "CASE WHEN project_ineligibility_type = 'No structure on site' THEN 1 ELSE 0 END", statistic: 'SUM'},
   {name: 'Ineligible: Vacant', expression: "CASE WHEN project_ineligibility_type = 'Vacant' THEN 1 ELSE 0 END", statistic: 'SUM'},
   {name: 'Ineligible: Destroyed', expression: "CASE WHEN project_ineligibility_type = 'Structure destroyed' THEN 1 ELSE 0 END", statistic: 'SUM'},
   {name: 'Ineligible: Owner Not Interested', expression: "CASE WHEN project_ineligibility_type = 'Homeowner not interested' THEN 1 ELSE 0 END", statistic: 'SUM'},
   {name: 'Total Ineligible Structures', expression: "CASE WHEN project_eligibility_status <> 'Eligible' THEN 1 ELSE 0 END", statistic: 'SUM'}]
)

return elig
```
