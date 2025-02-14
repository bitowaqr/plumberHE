This repository was created for the [R-HTA](https://r-hta.org/) workshop, held in Oxford on Thursday 19th May 2022. 

# **Automating a living health economic evaluation with GitHub Actions & Plumber APIs**

Robert Smith<sup>1,2,3</sup>,  Paul Schneider<sup>2,3</sup> & Praveen Thokala<sup>2</sup>

<sup>1</sup> [Lumanity](https://lumanity.com/), Sheffield, UK    
<sup>2</sup> [University of Sheffield](https://www.sheffield.ac.uk/scharr), University of Sheffield, Sheffield, UK    
<sup>3</sup> [Dark Peak Analytics](https://darkpeakanalytics.com/), Sheffield, UK;

>#### **Background**
>
>The process of updating economic models is time-consuming and expensive, and often involves the transfer of sensitive data between parties.
>However, recent advances in data and computing sciences can be combined with script based health economic models to provide living health economic analysis, that is, analysis
>that is continually being updated as new evidence, and our understanding of the world, emerge.
>Allowing clients to retain control of their data, while automating much of the health economic analysis, would be a desirable step forward for HEOR.
>The following describes a simple worked example of an automated health economic evaluation which is run monthly, or as new data emerges.
>
>
>#### **Methods**
>
>The materials demonstrate how economic models can be separated from any sensitive data on which they rely, allowing for companies to retain control of their data at all times >and allowing models to be automatically updated as new data becomes available. There are three parts to achieving this:
>- An API, generated using the R package [plumber](https://www.rplumber.io/?msclkid=b4faa783bbfc11ec93ded7f5b4523880/) is hosted on a client server (on RStudio Connect). This API >hosts all sensitive data, so that data does not have to be provided to the consultant. 
>- An economic model, written in a script based programming language, is constructed by the consultant using pseudo data and hosted on GitHub. For this example we use the >sick-sicker model created as a teaching tool by the [DARTH group](http://darthworkgroup.com/). 
>- An automated workflow is created using GitHub actions. This workflow passes the model code to the client API, which runs the model within the client server and returns the >results of the economic analysis. This workflow can be scheduled to run at certain time points (e.g. monthly), or when triggered by an event (e.g. an update to the model). A >report is generated from the workflow using [RMarkdown](https://rmarkdown.rstudio.com/?msclkid=2f44ca56bbfe11eca6ec37c1951dc1f9).
>
>A diagram of the method is shown below.
>
>**< INSERT DIAGRAM HERE >**
>
>#### **Results & Discussion**
>
>The method is relatively complex, and would require a strong understanding of R, APIs, RMarkdown and GitHub Actions.
>However, the end result is a process by which a client never needs to release their sensitive data to a consultant, reducing concerns about data-security.
>The entire process is automated, such that it can be triggered by a client if data is provided ad-hoc, or run on a schedule if data is continually updated.
>As far as we are aware this is the first application of this process anywhere for a HEOR project.
>
>#### **Conclusions**
>
>This example demonstrates that it is possible to separate an algorithm (a code base for a health economic model) from the data used by the model. 
>By using new open source tools data never left the client's server, yet analysis was undertaken and updated when necessary as new data was input by the client.


## Instructions for using the API only

<!-- THE INSTRUCTIONS ARE NOT CLEAR: HOW DO I RUN THE WORKFLOW? WHERE CAN I FIND THE CONNECT_KEY ? -->

The file `scripts/run_darthAPI.R` is the file run by the automated workflow.
Within this file is a call to the API, shown below, undertaken using the `httr` package.
To run this function you will need the api key, which I have stored as an environment variable `CONNECT_KEY`.

```
# function to call API to run the model
httr::content(
  httr::POST(
    # the Server URL can also be kept confidential, but will leave here for now 
    url = "https://connect.bresmed.com",
    # path for the API within the server URL
    path = "rhta2022/runDARTHmodel",
    # code is passed to the client API from GitHub.
    query = list(model_functions = "https://raw.githubusercontent.com/BresMed/plumberHE/main/R/darth_funcs.R"),
    # set of parameters to be changed ... we are allowed to change these but not some others...
    body = jsonlite::toJSON(data.frame(parameter = c("p_HS1","p_S1H"),
                                       distribution = c("beta","beta"),
                                       v1 = c(25, 50),
                                       v2 = c(150, 100))),
    # we include a key here to access the API ... like a password protection
    config = httr::add_headers(Authorization = paste0("Key ", 
                                             Sys.getenv("CONNECT_KEY")))
  )
)

```

The example plumber code used to generate the API can be found in `darthAPI`. This is a development version of the code.

### List of contributors
- [RobertASmith](Robert.Smith@lumanity.com)
- [Paul Schneider](pschneider@darkpeakanalytics.com)
