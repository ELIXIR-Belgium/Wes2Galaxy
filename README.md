# Galaxy workflow execution using proWES schemas

![](https://raw.githubusercontent.com/elixir-europe/elixir-cloud-outline/master/images/schematic_overview.png)

This is a proof of concept to show the possibility to invoke a workflow using the WES API.



## Building a Galaxy image supporting the workflow

A Galaxy 19.01 container is decorated with the necessary tools to run the workflow. 


```sh
docker build -t containername -f Dockerfile .
```

## Workflow Execution Service (WES) API

To mimic the WES `/runs` POST call, following example json is used:

```json
{
    "workflow_params": {
        "inputs": [
            {
                "step": "0",
                "file": "https://raw.githubusercontent.com/bedroesb/WES2Galaxy/master/example_data/UCSC_input.bed"
            }
        ]
    },
    "workflow_url": "https://raw.githubusercontent.com/bedroesb/WES2Galaxy/Dev/example_data/Galaxy-Workflow-galaxy-intro-strands-2.ga",
    "workflow_version": "0.1",
    "workflow_type": "ga"
}

```
In the future, this WES endpoint will create a new workflow run and returns a RunId to monitor its progress.

The workflow_url is an absolute URL to a workflow file.

The workflow_params JSON object specifies input parameters, such as input files. The exact format of the JSON object depends on the conventions of the workflow language being used. Input files should either be absolute URLs.

The workflow_type is the type of workflow language.

The workflow_type_version is the version of the workflow language submitted and must be one supported by this WES instance.

See the RunRequest documentation for details about other fields.
The attribute `inputs` contains a list of all the input files with their corresponding `file` and workflow `step`. Note that the step number is in string format.

## Building the docker image
A Galaxy `19.01` container will be decorated with the necessary tools to run the workflow. 

To build the docker image use following command in the root directory:

```sh
docker build -t containername -f Dockerfile .
```

## Starting the container

To run the container interactively use following command:

```sh
docker run -t containername
```

## TO DO

* Using a WES flask client to invoke the workflow in the galaxy container
* Implementing the possibility to check the workflow status
* kill te container after specific amount of time (when user has data)
* Give ID back to the user for retrieving status information

## Problems

* How to supply the output-data in the container back to the user?\
    -> Available on a server for a limited amount of time. \
    -> Downloadable through links
* When the `startingWorkflow.py` script is manually started in the container, BioBlend is not installed. No problem when the script is started automatically with `ENTRYPOINT [ "/bin/starter-service.sh" ]` in the Dockerfile ?


## Documentation

[WES schemas](https://github.com/ga4gh/workflow-execution-service-schemas) 

[WES-ELIXIR](https://github.com/elixir-europe/WES-ELIXIR)

[WES endpoint example](http://193.167.189.73:7777/ga4gh/wes/v1/ui/#!/WorkflowExecutionService/RunWorkflow)

