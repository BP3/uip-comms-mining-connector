# Camunda UiPath Communications Mining Custom Connector Template
This project contains a custom Camunda 8 Connector template that provides access to UiPath operations. The operations and functionality have to be encoded into the template to provide the integration.

This readme outlines how to update the template to provide additional functionality.

# How does the connector work?
The Connector is based on the `io.camunda:http-json` connector used by various OOTB connectors such as the REST API Connector.
Integration with UiPath is achieved using the UiPath Communications Mining API.  Details of the API can be found here: https://docs.uipath.com/communications-mining/automation-cloud/latest/developer-guide/introduction

# How to add a new operation to the connector template
The following steps outline how to add a new operation to the Connector.

## Add the new operation as an option
Add the new operation to the `choices` array:

```json
{
  "label": "UiPath Operation Type",
  "id": "operationType",
  "group": "operation",
  "description": "The operation in UiPath to invoke",
  "type": "Dropdown",
  "choices": [
    {
      "name": "Classify Email",
      "value": "classifyEmail"
    },
    {
      "name": "New Operation",
      "value": "newOperation"
    }
  ],
  "binding": {
    "type": "zeebe:input",
    "name": "operationType"
  }
}
```

## Configure which configurations should be available for the new operation
For each of the options required to configure the new operation, add the `value` of the new operation to the associated `condition.oneOf` property, for example:

```json
{
  "description": "This sets the HTTP method to POST based on the UiPath operation that is set",
  "id": "methodPost",
  "group": "configuration",
  "type": "Hidden",
  "value": "post",
  "binding": {
    "type": "zeebe:input",
    "name": "method"
  },
  "condition": {
    "property": "operationType",
    "oneOf": [
      "classifyEmail",
      "newOperation"
    ]
  }
}
```

## Add end point URL generation
The new operation will require an end point URL, which might be static, or dynamic based on the paramater values passed in. The example below shows the parameters plugged into the URL (this is done at runtime and not at design time). Note how the start of the `value` property has `=\"`, ends with a `"`, and all double quotes contained within are de-referenced (`\"`).  This allows the URL to be dynamically created by concatenating variables and strings as an expression.

Some operations may have a fixed URL, in which case that can just be enclosed inside outer quotes like any other string JSON property.

```json
{
  "description": "This is for putting together a URL to be used in the new operation request.",
  "type": "Hidden",
  "value": "=\"https://\" + cloudUrl + \"/\" + uipath_organisation + \"/\" + uipath_tenant + \"/reinfer_/api/v1/datasets/\" + project_name + \"/\" + dataset_name + \"/labellers/\" + model_version + \"/new-operation\"",
  "binding": {
    "type": "zeebe:input",
    "name": "url"
  },
  "condition": {
    "property": "operationType",
    "oneOf": [
      "newOperation"
    ]
  }
}
```


# Fork the repository
To contribute to the project you should fork the repository and raise a pull request.

## Raise a pull request
In the description of your pull request please supply the following details, with screen shots where possible:

* Provide a desription of the change you have made
* Submit results of testing you have done as part of the change
