<h1 align="center">Simplify your data testing</h1>

<h2 align="center">Take away the pain and complexity of your data landscape and let Data Caterer handle it to reliably 
validate all your data changes.</h2>

<span class="center-content">
[Try now](get-started/docker.md){ .md-button .md-button--primary }[See pricing](pricing.md){ .md-button .button-spaced }
</span>

<h1 class="content-spaced" align="center"> Data testing is difficult and fragmented</h1>

- Data being sent via messages, HTTP requests or files and getting stored in databases, file systems, etc.
- Maintaining and updating tests with the latest schemas and business definitions
- Different testing tools for services, jobs or data sources
- Complex relationships between datasets and fields
- Different scenarios, permutations, combinations and edge cases to cover

<h1 class="content-spaced" align="center"> Current solutions only cover half the story </h1>

- Specific testing frameworks that support one or limited number of data sources or transport protocol
- Under utilizing metadata from data catalogs or metadata discovery services
- Testing teams having difficulties understanding when failures occur
- Integration tests relying on external teams/services
- Manually generating data, or worse, copying production data into lower environments

<h1 class="content-spaced" align="center"> What you need is a reliable tool that can handle changes to your data landscape. A clear and common 
approach that covers your whole ecosystem</h1> 

<figure markdown>
  ![High level overview of Data Caterer](diagrams/high_level_flow.drawio.png#only-light)
  ![High level overview of Data Caterer](diagrams/high_level_flow_dark.drawio.png#only-dark)
</figure>

With Data Caterer, you get:

- Ability to connect to any type of data source: files, SQL or no-SQL databases, messaging systems, HTTP
- Discover metadata from your existing infrastructure and services
- Gain confidence that potential bugs do not propagate to production
- Be proactive in ensuring changes do not affect other data producers or consumers
- Configurability to run tests the way you want

<span class="center-content">
[Try now](get-started/docker.md){ .md-button .md-button--primary }[See pricing](pricing.md){ .md-button .button-spaced }
</span>

## Tech Summary

Data Caterer is a metadata driven data generation/testing tool that aids in creating production like data across batch and event
data systems. You can then clean up the generated data or run data validations to ensure your systems have ingested it
as expected. Use the Java, Scala API, or YAML files to help with setup or customisation that are all run via Docker.

Main features include:

- :material-card-search: Metadata discovery
- :material-file: Batch or :material-circle-multiple: event data generation
- :material-vector-difference-ba: Maintain referential integrity across any dataset
- :material-projector-screen-variant-outline: Create custom data generation scenarios
- :material-delete-sweep: Clean up generated data
- :material-check: Validate data
- :material-test-tube: Suggest data validations

<span class="center-content">
[Try now](get-started/docker.md){ .md-button .md-button--primary }[See pricing](pricing.md){ .md-button .button-spaced }
</span>
