# ps6-shiny-final #

## User Documentation ##

### Description of the Data: ###
This dataset provides the estimates of deaths and years of life lost due to various bacterial infections, caused by 33 pathogens across 204 locations in 2019. The estimates in this dataset were made by using a total of 343 million individual records and 11,361 study location years. These records analyzed data from hospital discharges, cause of death, tissue sampling, literature reviews, microbiology lab results from hospitals nationally as well as muti-national surveillance systems.

The data was collected by researchers at the Institute for Health Metrics and Evaluation (IHME) as well as University of Oxford. It is being accessed through the GHDx: Global Health Data Exchange which is a catalog of vital statistics and health related data, available to the public.


### Explanation of Widgets and Panels: ###

_Plots Page:_
In this page, the plot provides you information with the number of deaths each pathogen from the dataset causes in a certain location. This plot can be presented as either a bar graph or a scatterplot, which is shown as a widget, in which the user may select which plot they would like to see. In this way, the visual of the plot changes, but the data does not. The other widgets include selecting the location and selecting which pathogens the user would like to see displayed on the graph. The textual output states which pathogen causes the most number of deaths in the chosen location and among the selected pathogens.

_Table Page:_
In this page, the table provides you with the number of deaths each pathogen from the dataset causes among certain age groups. In this page, the widget allows the user to select which age group they would like to see, and the table shows the deaths each pathogen causes in that age group in descending order. The textual output explains what the minimum and maximum deaths for that age group. 

### Link to Shiny App: ###
https://pkesap.shinyapps.io/pathogen_app/
#### Small Note: In my app, I'm unsure why and I couldn't pinpoint it, but the bargraph/scatterplot data on the "Plots" page only shows up after you go to the "Table" page, make an age selection, view the table, then go back to the "Plots" page. Then, you can make all the selections you'd like on the "Plots" page and the bargraph/scatterplot will function. #### 

### Hours Spent on PS6: ###
I spent several hours over several days working on this PS, a total of around 20 hours. 
