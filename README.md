# ps6-shiny-final
This repo is for ps6 which includes the code for an application on shiny. 
library(shiny)
library(tidyverse)
library(dplyr)
library(ggplot2)


bacteria_1 <- read_delim("pathogen.csv")
bacteria_2 <- select(bacteria_1, -sex_id, -sex_name, -metric_id, -metric_name, -upper, -lower)
bacteria_2
nrow(bacteria_2)
ncol(bacteria_2)
bacteria_final <- bacteria_2[!(bacteria_2$location_name %in% c("Global",
                                                               "Southeast Asia, East Asia, and Oceania", 
                                                               "East Asia", "Democratic People's Republic of Korea",
                                                               "Southeast Asia", "Lao People's Democratic Republic", "Marshall Islands",
                                                               "Central Europe, Eastern Europe, and Central Asia", 
                                                               "Central Asia", "Central Europe", "Eastern Europe", "Republic of Moldova", 
                                                               "High-income", "High-income Asia Pacific", "Western Europe",
                                                               "Southern Sub-Saharan Africa", "Southern Latin America", 
                                                               "High-income North America", "Latin America and Caribbean")), ]
bacteria_final
nrow(bacteria_final)   
ncol(bacteria_final)

bacteria_final <- bacteria_2[!(bacteria_2$age_group_name %in% c("Age-standardized")), ]


# Define UI
ui <- fluidPage(
  titlePanel("Estimates of Global Mortality from Bacterial Pathogens in 2019"),
  tabsetPanel(
    tabPanel("About",
             p("This dataset provides the",
               strong("estimates of deaths and years of life lost"), 
               p("due to various bacterial infections, caused by",
                 em("33 pathogens across 204 locations in 2019.")
               ),
               p("This dataset contains 664516 rows and 12 columns/variables.
                 A sample of the first few rows of the dataset is shown below."),
               tableOutput("bacteria_table")
             )
    ),
    tabPanel("Plots",
             fluidRow(
               column(
                 width = 4,
                 selectInput(inputId = "location", 
                             label = "Select location", 
                             choices = unique(bacteria_final$location_name)),
                 checkboxGroupInput(inputId = "pathogen_checkbox",
                                    label = "Select pathogens to display:",
                                    choices = unique(bacteria_final$pathogen),
                                    selected = unique(bacteria_final$pathogen))
               ),
               column(
                 width = 8,
                 radioButtons(inputId = "plot_type",
                              label = "Select plot type:",
                              choices = c("Bar Graph", "Scatterplot"),
                              selected = "Bar Graph"),
                 plotOutput(outputId = "pathogen_plot", width = "800px"),
                 fluidRow(
                   column(
                     width = 12,
                     textOutput(outputId = "pathogen_max_deaths")
                   )
                 )
               )
             )
    ),
    tabPanel("Table",
             fluidRow(
               column(
                 width = 4,
                 p("Table for bacterial infections in various locations, grouped by pathogen and age group."),
                 selectInput(inputId = "age_group", 
                             label = "Select age group", 
                             choices = c("All Ages", unique(bacteria_final$age_group_name))),
                 textOutput(outputId = "death_range")
               ),
               column(
                 width = 8,
                 tableOutput(outputId = "pathogen_table")
               )
             )
    )
  )
)

# Define server logic
server <- function(input, output) {
  
  # Render the table
  output$bacteria_table <- renderTable({
    head(bacteria_final)
  })
  
  # Filter the data based on user inputs
  filtered_data <- reactive({
    bacteria_final %>%
      filter(location_name == input$location,
             pathogen %in% input$pathogen_checkbox,
             age_group_name == input$age_group)
  })
  
  # Render the plot
  output$pathogen_plot <- renderPlot({
    data <- filtered_data() %>%
      filter(pathogen %in% input$pathogen_checkbox) %>%
      group_by(pathogen) %>%
      summarize(deaths = sum(val)) %>%
      arrange(desc(deaths))
    
    if (input$plot_type == "Bar Graph") {
      ggplot(data, aes(x = reorder(pathogen, deaths), y = deaths, fill = pathogen)) +
        geom_col() +
        ggtitle(paste("Pathogens Responsible for Deaths in", input$location)) +
        xlab("Pathogen") +
        ylab("Number of Deaths") +
        theme(plot.title = element_text(hjust = 0.5),
              axis.text.x = element_text(angle = 45, hjust = 1))
    } else {
      ggplot(data, aes(x = pathogen, y = deaths, color = pathogen)) +
        geom_point() +
        ggtitle(paste("Pathogens Responsible for Deaths in", input$location)) +
        xlab("Pathogen") +
        ylab("Number of Deaths") +
        theme(plot.title = element_text(hjust = 0.5),
              axis.text.x = element_text(angle = 45, hjust = 1))
    }
  })
  
  # Render the max deaths text
  output$pathogen_max_deaths <- renderText({
    data <- bacteria_final %>%
      filter(location_name == input$location,
             pathogen %in% input$pathogen_checkbox) %>%
      group_by(pathogen) %>%
      summarize(deaths = sum(val)) %>%
      arrange(desc(deaths))
    
    max_deaths <- max(data$deaths)
    max_pathogen <- data %>%
      filter(deaths == max_deaths) %>%
      pull(pathogen)
    
    paste("The pathogen that causes the most deaths is", max_pathogen, "with", 
          round(max_deaths, 2), "deaths.")
  })
  
  # Render the table
  output$pathogen_table <- renderTable({
    data <- filtered_data() %>%
      filter(age_group_name == input$age_group) %>%
      group_by(pathogen) %>%
      summarize(deaths = sum(val)) %>%
      arrange(desc(deaths))
    
  })
  
  # Render the death range text
  output$death_range <- renderText({
    data <- filtered_data() %>%
      group_by(pathogen) %>%
      summarize(deaths = sum(val)) %>%
      arrange(desc(deaths))
    
    min_deaths <- round(min(data$deaths), 2)
    max_deaths <- round(max(data$deaths), 2)
    
    if (input$age_group == "All Ages") {
      paste("The minimum deaths across all age groups for the selected location and pathogens is", 
            round(min_deaths, 2), "and the maximum is", round(max_deaths, 2), ".")
    } else {
      age_data <- filtered_data() %>%
        group_by(pathogen, age_group_name) %>%
        summarize(deaths = sum(val)) %>%
        arrange(desc(deaths))
      
      min_age_deaths <- round(min(age_data$deaths), 2)
      max_age_deaths <- round(max(age_data$deaths), 2)
      
      paste("The minimum deaths for the selected age group, location, and pathogens is", 
            round(min_age_deaths, 2), "and the maximum is", round(max_age_deaths, 2),".")
    }
  })
}

# Run the app
shinyApp(ui, server)
