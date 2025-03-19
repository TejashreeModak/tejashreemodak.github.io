---
layout: post
title: RShiny 
date: 2025-03-19
---

## RShiny apps 

RShiny apps are a great way to easily *share your analysis* with stakeholders who can easily accesss your work with a web browser, interact with the data, plot data and see results very easily. 

Rshiny apps can also be used to *build tools for bioinformatics* analysis that can make mundane tasks fast and easily accessibly by yourself or shared with others. With the advantage of UI, they can be a great tool for bench scientists who may not be commandline savvy. 

Here is an example of a simple tool I built to reverse complement DNA sequence. While designing primers, checking sequence of genes on opposite strand or checking alignments I often needed to check the reverse complement sequence of a region or a gene. Of course there are online tools and functions to do that job but its more fun to build a RShiny app and have it available whenever I want it. 

Here are some resources that served as very helpful guides with examples for RShiny development. 
- [Posit](https://shiny.posit.co/)
- [Mastering shiny Book](https://mastering-shiny.org/index.html)

**My RShiny app can be accessed at: [Reverse complement DNA Tool](https://tejashreemodak.shinyapps.io/revcompDNA/)**

This R Shiny app provides a simple interface for computing the reverse complement of a given DNA sequence.

You can make 2 separate scripts ui.R and server.R to house the ui and server logic separately or make a single script app.R to house it all. I find the second option easier for simple apps. Of course as you build more functionalities it might be easier to keep them separate. 

A few things I thought of to improve on user experience and also make my life easier!: 
- It is a good idea to *limit the length* of the user input sequence to avoid performance and memory issues and sour user experience. 
- To expect spaces or lowercase letters or numbers in the user input that **needs** to be cleaned in order to avoid errors.
- Add a warning for exceeding length and invalid input.
- To give an example serves as a formatting suggestion. 


Below is a breakdown of the code:

### 1. Function: `reverse_complement()`
This function takes a DNA sequence as input and computes its reverse complement.

Converts the sequence to uppercase using `toupper(seq)`.
Uses `chartr("ATGC", "TACG", seq)` to replace each nucleotide with its complementary base:
A → T
T → A
G → C
C → G
Splits the sequence into individual characters, reverses their order, and rejoins them.

### 2. UI (User Interface)  

The `fluidPage()` function creates a simple web layout with:  

- **A title panel (`titlePanel`)**.  
- **A sidebar panel (`sidebarPanel`)** containing:  
  - A `textInput` for the user to enter a DNA sequence.  
  - An `actionButton` labeled **"Get Reverse Complement"**.  
  - Example input for guidance.  
- **A main panel (`mainPanel`)** displaying:  
  - The original sequence.  
  - The reverse complement result. 


### 3. Server Logic  

- A **reactive value** (`result`) is used to store the computed reverse complement.  
- When the **"Get Reverse Complement"** button is clicked (`observeEvent(input$compute, { ... })`):  
  - The input sequence is cleaned by removing invalid characters (`[^ATGCatgc]`).  
  - If the input is valid, the `reverse_complement` function is called.  
  - Otherwise, an **error message** is displayed.  
- The outputs are rendered dynamically:  
  - `renderText()` is used to display both the **original sequence** and the **computed reverse complement**.  

### 4. Running the App
The shinyApp(ui = ui, server = server) function initializes and runs the app.

There you go! A simple task now automated for any type of user. 
