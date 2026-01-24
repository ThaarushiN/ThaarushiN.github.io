---
title: "Chart Generation"
date: 2026-01-22
categories: [AI, Engineering, Visualization]
tags: [LLM, Tool Calling, Data Visualization, JSON, Python]
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true

# SEO Metadata
excerpt: "Discussing how we got our document analyzer to generate a chart using tool interfaces and LLM invocation."
header:
  overlay_image: /assets/images/chart-gen-header.png
  og_image: /assets/images/chart-gen-header.png
  teaser: /assets/images/chart-gen-header.png
  caption: ""
---

## Chart Generation Using an LLM


In the previous article, we talked about how we read spreadsheets without compromising accuracy or performance. But data is not really impactful if it is not visualized so today we will be discussing how we got our document analyzer to generate a chart.

The first step was defining a tool interface the LLM could reliably invoke. A tool call is when an LLM recognizes that it needs to perform some action outside of its current capability to answer the user query and needs to call an outside API or service. An LLM can only generate text by itself so a tool call will greatly improve its capabilities. In our case, the LLM has to generate a tool call to call on a function that generates charts. 



To define a tool call, we need to look at the function (the tool). There are different types of chart that we need to generate based on the nature of our data. This function should be able to handle the basic chart types needed to visualize data. Then it needs x data, y data and names for x and y axis. 

Here’s the pseudo-code for the function that I designed. It requires helper functions for each chart type. It can visualize multiple y data as well, depending on the chart type

```text
FUNCTION draw_chart(chart_type, x_data, y_series_data, y_series_names, axis_x, axis_y, title):

    CREATE a new chart object of type chart_type

    FOR each index i from 0 to length of y_series_data - 1 DO
        series_data ← y_series_data[i]
        series_name ← y_series_names[i]
        ADD series_data to chart using x_data as X values
        LABEL the series with series_name
    END FOR

    SET X-axis label to axis_x
    SET Y-axis label to axis_y
    SET chart title to title

    DISPLAY the chart on screen

END FUNCTION
```

In addition to the helper functions generating each supported chart_type, there are two other helper functions. First one ensures that both x_data and elements of y_series_data (y_series_data is a list of lists) have the same length. The other helper function is to check whether x_data is safe to convert to numerical values, and if so convert.

Now that the tool is done, let’s design the tool call. I used JSON to design it as it is a format that the LLM can infer easily.

```json
tool_func = [
    {
        "name": "draw_chart",
        "description": (
            "Generate a chart (bar, line, scatter, pie) given x-axis values, "
            "multiple y-series, qualitative categories, and axis labels/title."
        ),
        "parameters": {
            "type": "object",
            "properties": {
                "chart_type": {
                    "type": "string",
                    "enum": ["bar", "line", "scatter", "pie"],
                    "description": "The type of chart to create."
                },
                "x_data": {
                    "type": "array",
                    "items": {"type": "string"},
                    "description": "List of values for the x-axis (qualitative or quantitative)."
                },
                "y_series_data": {
                    "type": "array",
                    "items": {"type": "array", "items": {"type": "number"}},
                    "description": "A list of lists, where each inner list is a numeric data series corresponding to x_data. Use a single inner list for one series."
                },
                "y_series_names": {
                    "type": "array",
                    "items": {"type": "string"},
                    "description": "List of names for each numeric series in y_series_data (e.g., ['Sales', 'Profit'])."
                },
                "axis_x": {
                    "type": "string",
                    "description": "Label for the x-axis."
                },
                "axis_y": {
                    "type": "string",
                    "description": "Label for the y-axis."
                },
                "title": {
                    "type": "string",
                    "description": "Title of the chart."
                }
            },
            "required": [
                "chart_type",
                "x_data",
                "y_series_data",
                "y_series_names",
                "axis_x",
                "axis_y",
                "title"
            ],
            "additionalProperties": False
        }
    }
]
```


Here, the “name” property is the same as the name of our tool function and the description is a comprehensive description of what the tool call is for. Then the most important part, parameters come in.

Parameters are what we are passing to the function. The LLM should follow the format of the tool call and organize our data the way the function requires. The chart types parameter explicitly states what types of charts we are supporting. Then the x_data parameter is an array of Strings as x_data can be either quantitative or qualitative depending on the chart type. Our tool handles this by checking whether it is safe to convert x_data to numeric data by using the helper function we talked about earlier. Other than these, the LLM also generates x-axis and y-axis names, the chart title and a description before sending these out as a tool call.

The parent function (LLM orchestration function) determines whether the response is a tool call or a regular response and acts accordingly.