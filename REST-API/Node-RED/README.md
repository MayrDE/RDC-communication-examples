# ROBA®-drive-checker Node-RED examples

## Table of contents

- [ROBA®-drive-checker Node-RED examples](#roba-drive-checker-node-red-examples)
  - [Table of contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Flow](#flow)
  - [Dashboard](#dashboard)
    - [Example Flow](#example-flow)

## Introduction

Node-RED is a powerful visual programming tool specifically designed for integrating hardware devices,  
APIs and online services. It allows you to create flows by connecting nodes, each of which performs a specific function.

In this flow, some commands from the REST service of the ROBA-Drive-Checker are executed as examples,   
and a small dashboard is created for visualization.   
Node-Red can be downloaded here [https://nodered.org/](https://nodered.org/)   
For the example to work properly, the module library [node-red-dashboard](https://flows.nodered.org/node/node-red-dashboard) is required!

the flow includes a readme, please note the instructions included here!   
![Open readme](Assets/readme.png)    
![Open readme](Assets/readme_text.png)

Please update the init function node with the correct hostname.   
![Open readme](Assets/init.png)

Enter the correct password in the http request node.   
![Open readme](Assets/http-reqest_node.png)

now press deploy!   
![Open readme](Assets/deploy.png)

Now the example should run 💪   
![Open readme](Assets/run.png)

## Important information

All rotor commands, that ***change the rotor configuration***, will only take effect if rotor is ***not*** in `run mode` AND after the rotor has been restarted to load the new configuration.

All rotor commands like `Tara`, `Reset Min/Max` etc. are only temporary, until the next restart of the rotor (or gateway).

## Flow
![Open flow](Assets/flow.png)   
[Flows.json](Assets/flows.json "open flows.json")

## Dashboard
![Open dashboard](Assets/Dashboard.png)

### Example Flow
[Flows.json](Assets/flows.json "open flows.json")
``` json
[
    {
        "id": "9727d69723ecfd0b",
        "type": "tab",
        "label": "REST",
        "disabled": false,
        "info": ""
    },
    {
        "id": "b3cc2bfa395c8b27",
        "type": "comment",
        "z": "9727d69723ecfd0b",
        "name": "rest service for RDC Stator",
        "info": "",
        "x": 490,
        "y": 60,
        "wires": []
    },
    {
        "id": "39f6c9ab46f92a5d",
        "type": "inject",
        "z": "9727d69723ecfd0b",
        "name": "GET Id",
        "props": [
            {
                "p": "topic",
                "vt": "str"
            },
            {
                "p": "method",
                "v": "GET",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "/RdcStator/Id",
        "x": 450,
        "y": 120,
        "wires": [
            [
                "a7a4c91a978d91d8"
            ]
        ]
    },
    {
        "id": "2873110682ec484e",
        "type": "inject",
        "z": "9727d69723ecfd0b",
        "name": "GET Version",
        "props": [
            {
                "p": "topic",
                "vt": "str"
            },
            {
                "p": "method",
                "v": "GET",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "/RdcStator/Version",
        "x": 470,
        "y": 160,
        "wires": [
            [
                "a7a4c91a978d91d8"
            ]
        ]
    },
    {
        "id": "9e71db48545ea125",
        "type": "inject",
        "z": "9727d69723ecfd0b",
        "name": "GET RotorState",
        "props": [
            {
                "p": "topic",
                "vt": "str"
            },
            {
                "p": "method",
                "v": "GET",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "/RdcStator/RotorState",
        "x": 480,
        "y": 240,
        "wires": [
            [
                "a7a4c91a978d91d8"
            ]
        ]
    },
    {
        "id": "53b693bdf9283908",
        "type": "inject",
        "z": "9727d69723ecfd0b",
        "name": "2s",
        "props": [
            {
                "p": "payload"
            },
            {
                "p": "topic",
                "vt": "str"
            }
        ],
        "repeat": "2",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "/RdcRotor/ProcessData",
        "payload": "",
        "payloadType": "date",
        "x": 110,
        "y": 900,
        "wires": [
            [
                "d6d166dcfcc1e920"
            ]
        ]
    },
    {
        "id": "0c92c0778f64fe38",
        "type": "function",
        "z": "9727d69723ecfd0b",
        "name": "clear",
        "func": "\nvar newMsg1  = { payload: new Array(0) };\nvar newMsg2  = {payload: 0};\n\nvar oMinMax = {\n        digit_min: +40000,\n        digit_max: -40000,\n        Nm_min: +4000,\n        Nm_max: -4000,\n};\n\nflow.set (\"MINMAX\", oMinMax);\n\n\nreturn [newMsg1, newMsg2];\n",
        "outputs": 2,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 670,
        "y": 980,
        "wires": [
            [
                "037f91292a234b89"
            ],
            [
                "572c110bcae8981a",
                "c8fc75fd4ba46df7",
                "b046e516f4e20aff"
            ]
        ]
    },
    {
        "id": "318644f53ba22325",
        "type": "function",
        "z": "9727d69723ecfd0b",
        "name": "torque",
        "func": "//MSG's\nvar newMsg1 = { topic: \"Torque [Nm]\", payload: msg.payload[\"Torque\"], timestemp: msg.payload[\"Timestamp\"]};\nvar newMsg2  = {payload: msg.payload[\"Torque\"]};\nvar newMsg4 = { topic: \"Temp [°C]\", payload: msg.payload[\"Temp\"], timestemp: msg.payload[\"Timestamp\"]};\nvar newMsg5 = { topic: \"Speed [U/m]\", payload: msg.payload[\"Speed\"], timestemp: msg.payload[\"Timestamp\"]};\n\n//Object's\nvar MinMax = {  Minimum: msg.payload[\"Minimum\"],\n                Maximum: msg.payload[\"Maximum\"]};\n\nvar newMsg3 = { payload: MinMax};\n\nreturn [newMsg1, newMsg2, newMsg3, newMsg4, newMsg5];",
        "outputs": 5,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 670,
        "y": 900,
        "wires": [
            [
                "037f91292a234b89"
            ],
            [
                "572c110bcae8981a"
            ],
            [
                "8638f946738f9cc9",
                "c55db24f70b06460"
            ],
            [
                "b046e516f4e20aff"
            ],
            [
                "c8fc75fd4ba46df7"
            ]
        ]
    },
    {
        "id": "e46a2845bae6de5a",
        "type": "catch",
        "z": "9727d69723ecfd0b",
        "name": "",
        "scope": null,
        "x": 100,
        "y": 1280,
        "wires": [
            [
                "6f47cb40200d4cd2"
            ]
        ]
    },
    {
        "id": "6f47cb40200d4cd2",
        "type": "debug",
        "z": "9727d69723ecfd0b",
        "name": "",
        "active": false,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "true",
        "x": 250,
        "y": 1280,
        "wires": []
    },
    {
        "id": "f8c1f85193b3757d",
        "type": "debug",
        "z": "9727d69723ecfd0b",
        "name": "",
        "active": false,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "payload",
        "targetType": "msg",
        "x": 690,
        "y": 800,
        "wires": []
    },
    {
        "id": "324469735ada581d",
        "type": "inject",
        "z": "9727d69723ecfd0b",
        "name": "",
        "props": [
            {
                "p": "payload"
            },
            {
                "p": "topic",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": true,
        "onceDelay": "0.1",
        "topic": "",
        "payload": "true",
        "payloadType": "bool",
        "x": 110,
        "y": 120,
        "wires": [
            [
                "226bfcaca8d373ce"
            ]
        ]
    },
    {
        "id": "ce90c4ce29cd7c77",
        "type": "comment",
        "z": "9727d69723ecfd0b",
        "name": "init list's",
        "info": "",
        "x": 90,
        "y": 60,
        "wires": []
    },
    {
        "id": "226bfcaca8d373ce",
        "type": "function",
        "z": "9727d69723ecfd0b",
        "name": "init",
        "func": "//Dashboard init values\nflow.set (\"Run\", false);\n\n//Drive Checker intt valus\n//the correct hostname must be entered here!\nflow.set(\"Hostname\", \"http://rdc-2426d00331\");\n\n/*\nFor the PUT commands to work correctly, \nbasic authentication must be selected in the \"http request\" node.\ne.g.:\nUser:     Customer\nPassword: kx2426d00331!\n*/",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 250,
        "y": 120,
        "wires": [
            []
        ]
    },
    {
        "id": "d6d166dcfcc1e920",
        "type": "function",
        "z": "9727d69723ecfd0b",
        "name": "check run",
        "func": "if(flow.get(\"Run\"))\n{\n    var newMsg = { payload: 0, topic: \"/RdcRotor/ProcessData\", method: \"GET\"};\n    return newMsg;\n}",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 260,
        "y": 900,
        "wires": [
            [
                "eb2f147f325cd204"
            ]
        ]
    },
    {
        "id": "f5202bd328651eb6",
        "type": "function",
        "z": "9727d69723ecfd0b",
        "name": "run",
        "func": "flow.set (\"Run\", true);\n",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 670,
        "y": 1020,
        "wires": [
            []
        ]
    },
    {
        "id": "97831e11bd215c46",
        "type": "function",
        "z": "9727d69723ecfd0b",
        "name": "stop",
        "func": "flow.set (\"Run\", false);\n",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 670,
        "y": 1060,
        "wires": [
            []
        ]
    },
    {
        "id": "924bbf11682e73b8",
        "type": "delay",
        "z": "9727d69723ecfd0b",
        "name": "",
        "pauseType": "delay",
        "timeout": "100",
        "timeoutUnits": "milliseconds",
        "rate": "1",
        "nbRateUnits": "1",
        "rateUnits": "second",
        "randomFirst": "1",
        "randomLast": "5",
        "randomUnits": "seconds",
        "drop": false,
        "allowrate": false,
        "outputs": 1,
        "x": 480,
        "y": 1020,
        "wires": [
            [
                "f5202bd328651eb6"
            ]
        ]
    },
    {
        "id": "7dd459dd2da7e4c4",
        "type": "ui_button",
        "z": "9727d69723ecfd0b",
        "name": "",
        "group": "781b6815d353fd77",
        "order": 7,
        "width": "2",
        "height": "1",
        "passthru": false,
        "label": "clear",
        "tooltip": "",
        "color": "",
        "bgcolor": "",
        "className": "",
        "icon": "",
        "payload": "true",
        "payloadType": "bool",
        "topic": "reset",
        "topicType": "str",
        "x": 250,
        "y": 980,
        "wires": [
            [
                "0c92c0778f64fe38"
            ]
        ]
    },
    {
        "id": "9401addbb3ad36b7",
        "type": "ui_button",
        "z": "9727d69723ecfd0b",
        "name": "",
        "group": "781b6815d353fd77",
        "order": 10,
        "width": "2",
        "height": "1",
        "passthru": false,
        "label": "READ",
        "tooltip": "",
        "color": "",
        "bgcolor": "",
        "className": "",
        "icon": "",
        "payload": "true",
        "payloadType": "bool",
        "topic": "/RdcRotor/ProcessData",
        "topicType": "str",
        "x": 250,
        "y": 940,
        "wires": [
            [
                "eb2f147f325cd204"
            ]
        ]
    },
    {
        "id": "0da73fe56932b03e",
        "type": "ui_button",
        "z": "9727d69723ecfd0b",
        "name": "",
        "group": "781b6815d353fd77",
        "order": 11,
        "width": "2",
        "height": "1",
        "passthru": false,
        "label": "RUN",
        "tooltip": "",
        "color": "",
        "bgcolor": "",
        "className": "",
        "icon": "",
        "payload": "true",
        "payloadType": "bool",
        "topic": "",
        "topicType": "str",
        "x": 250,
        "y": 1020,
        "wires": [
            [
                "924bbf11682e73b8"
            ]
        ]
    },
    {
        "id": "8e5a785f24a0dae4",
        "type": "ui_button",
        "z": "9727d69723ecfd0b",
        "name": "",
        "group": "781b6815d353fd77",
        "order": 12,
        "width": "2",
        "height": "1",
        "passthru": false,
        "label": "STOP",
        "tooltip": "",
        "color": "",
        "bgcolor": "",
        "icon": "",
        "payload": "true",
        "payloadType": "bool",
        "topic": "reset",
        "x": 250,
        "y": 1060,
        "wires": [
            [
                "97831e11bd215c46"
            ]
        ]
    },
    {
        "id": "8638f946738f9cc9",
        "type": "ui_text",
        "z": "9727d69723ecfd0b",
        "group": "781b6815d353fd77",
        "order": 2,
        "width": "6",
        "height": "1",
        "name": "",
        "label": "MaximumValue [Nm]:",
        "format": "{{msg.payload.Maximum}}",
        "layout": "row-left",
        "className": "",
        "x": 920,
        "y": 980,
        "wires": []
    },
    {
        "id": "c55db24f70b06460",
        "type": "ui_text",
        "z": "9727d69723ecfd0b",
        "group": "781b6815d353fd77",
        "order": 3,
        "width": "6",
        "height": "1",
        "name": "",
        "label": "Minimum Value [Nm]:",
        "format": "{{msg.payload.Minimum}}",
        "layout": "row-right",
        "className": "",
        "x": 920,
        "y": 1020,
        "wires": []
    },
    {
        "id": "572c110bcae8981a",
        "type": "ui_gauge",
        "z": "9727d69723ecfd0b",
        "name": "Torque",
        "group": "781b6815d353fd77",
        "order": 4,
        "width": "4",
        "height": "4",
        "gtype": "gage",
        "title": "Torque",
        "label": "[Nm]",
        "format": "{{payload}}",
        "min": "-250",
        "max": "+250",
        "colors": [
            "#ca3838",
            "#00e62e",
            "#ca3838"
        ],
        "seg1": "-190",
        "seg2": "+190",
        "diff": false,
        "className": "",
        "x": 870,
        "y": 1100,
        "wires": []
    },
    {
        "id": "c8fc75fd4ba46df7",
        "type": "ui_gauge",
        "z": "9727d69723ecfd0b",
        "name": "Speed",
        "group": "781b6815d353fd77",
        "order": 5,
        "width": "4",
        "height": "4",
        "gtype": "gage",
        "title": "Speed",
        "label": "[U/m]",
        "format": "{{payload}}",
        "min": "-14000",
        "max": "+14000",
        "colors": [
            "#ca3838",
            "#00e62e",
            "#ca3838"
        ],
        "seg1": "-190",
        "seg2": "+190",
        "diff": false,
        "className": "",
        "x": 870,
        "y": 1140,
        "wires": []
    },
    {
        "id": "037f91292a234b89",
        "type": "ui_chart",
        "z": "9727d69723ecfd0b",
        "name": "",
        "group": "781b6815d353fd77",
        "order": 1,
        "width": "0",
        "height": "0",
        "label": "",
        "chartType": "line",
        "legend": "true",
        "xformat": "HH:mm:ss",
        "interpolate": "linear",
        "nodata": "",
        "dot": false,
        "ymin": "",
        "ymax": "",
        "removeOlder": "10",
        "removeOlderPoints": "400",
        "removeOlderUnit": "604800",
        "cutout": 0,
        "useOneColor": false,
        "useUTC": false,
        "colors": [
            "#1f77b4",
            "#aec7e8",
            "#ff7f0e",
            "#2ca02c",
            "#98df8a",
            "#d62728",
            "#ff9896",
            "#9467bd",
            "#c5b0d5"
        ],
        "outputs": 1,
        "useDifferentColor": false,
        "className": "",
        "x": 870,
        "y": 900,
        "wires": [
            []
        ]
    },
    {
        "id": "7972da5c33dc4d47",
        "type": "function",
        "z": "9727d69723ecfd0b",
        "name": "code to text",
        "func": "var newMsg = msg;\n\nswitch (msg.payload.Value) {\n    case 0:\n        newMsg.payload = \"Initialize_RF\";\n        break;\n    case 1:\n        newMsg.payload = \"Initialize_Self\";\n        break;\n    case 2:\n        newMsg.payload = \"Init_Failed\";\n        break;\n    case 3:\n        newMsg.payload = \"NotPaired\";\n        break;\n    case 4:\n        newMsg.payload = \"Pairing\";\n        break;\n    case 5:\n        newMsg.payload = \"PairingFailed\";\n        break;\n    case 6:\n        newMsg.payload = \"NotConnected\";\n        break;\n    case 7:\n        newMsg.payload = \"Connected\";\n        break;\n    case 8:\n        newMsg.payload = \"RunMode\";\n        break;\n    case 9:\n        newMsg.payload = \"RotorUpdate\";\n        break;\n    case 10:\n        newMsg.payload = \"RotorUpdateFailed\";\n        break;\n    case 11:\n        newMsg.payload = \"Wait4CommandResponse\";\n        break;\n    default:\n        newMsg.payload = \"Undefined Statuscode\";       \n}\nreturn newMsg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 930,
        "y": 660,
        "wires": [
            [
                "042716afe2805ece"
            ]
        ]
    },
    {
        "id": "06fe2d95cd51662f",
        "type": "comment",
        "z": "9727d69723ecfd0b",
        "name": "rest service for RDC Rotor",
        "info": "",
        "x": 930,
        "y": 60,
        "wires": []
    },
    {
        "id": "e961257450612933",
        "type": "http request",
        "z": "9727d69723ecfd0b",
        "name": "http request",
        "method": "use",
        "ret": "txt",
        "paytoqs": "ignore",
        "url": "",
        "tls": "",
        "persist": false,
        "proxy": "",
        "insecureHTTPParser": false,
        "authType": "basic",
        "senderr": false,
        "headers": [],
        "credentials": {},
        "x": 370,
        "y": 580,
        "wires": [
            [
                "1145ba5ffe14edd0",
                "729c8a6fb792634f"
            ]
        ]
    },
    {
        "id": "ca3fa37da8ca24b0",
        "type": "inject",
        "z": "9727d69723ecfd0b",
        "name": "GET Id",
        "props": [
            {
                "p": "topic",
                "vt": "str"
            },
            {
                "p": "method",
                "v": "GET",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "/RdcRotor/Id",
        "x": 890,
        "y": 120,
        "wires": [
            [
                "097716d0ef7d081f"
            ]
        ]
    },
    {
        "id": "1145ba5ffe14edd0",
        "type": "json",
        "z": "9727d69723ecfd0b",
        "name": "",
        "property": "payload",
        "action": "",
        "pretty": false,
        "x": 570,
        "y": 580,
        "wires": [
            [
                "2d6a3ba30ab509a8",
                "59df41a3919c6f72"
            ]
        ]
    },
    {
        "id": "2d6a3ba30ab509a8",
        "type": "debug",
        "z": "9727d69723ecfd0b",
        "name": "",
        "active": false,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "payload",
        "targetType": "jsonata",
        "statusVal": "",
        "statusType": "auto",
        "x": 760,
        "y": 480,
        "wires": []
    },
    {
        "id": "729c8a6fb792634f",
        "type": "debug",
        "z": "9727d69723ecfd0b",
        "name": "",
        "active": true,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "true",
        "targetType": "full",
        "statusVal": "",
        "statusType": "auto",
        "x": 570,
        "y": 480,
        "wires": []
    },
    {
        "id": "b1e5f0342f416b6e",
        "type": "inject",
        "z": "9727d69723ecfd0b",
        "name": "GET Version",
        "props": [
            {
                "p": "topic",
                "vt": "str"
            },
            {
                "p": "method",
                "v": "GET",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "/RdcRotor/Version",
        "x": 910,
        "y": 160,
        "wires": [
            [
                "097716d0ef7d081f"
            ]
        ]
    },
    {
        "id": "597071aff78dcf75",
        "type": "inject",
        "z": "9727d69723ecfd0b",
        "name": "GET ProcessData",
        "props": [
            {
                "p": "topic",
                "vt": "str"
            },
            {
                "p": "method",
                "v": "GET",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "/RdcRotor/ProcessData",
        "x": 930,
        "y": 200,
        "wires": [
            [
                "097716d0ef7d081f"
            ]
        ]
    },
    {
        "id": "675eed9aa05362d5",
        "type": "function",
        "z": "9727d69723ecfd0b",
        "name": "Modify URL",
        "func": "var newMsg = msg;\nvar Hostname = flow.get(\"Hostname\");\nnewMsg.url = Hostname + msg.topic;\n\nif ((newMsg.payload === true) && (newMsg.topic === \"/RdcRotor/ProcessData\"))\n    newMsg.method = \"GET\";\n\nreturn newMsg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 190,
        "y": 580,
        "wires": [
            [
                "e961257450612933",
                "bb6a0a5add36bfaf"
            ]
        ]
    },
    {
        "id": "59df41a3919c6f72",
        "type": "switch",
        "z": "9727d69723ecfd0b",
        "name": "",
        "property": "topic",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "/RdcRotor/Id",
                "vt": "str"
            },
            {
                "t": "eq",
                "v": "/RdcRotor/Version",
                "vt": "str"
            },
            {
                "t": "eq",
                "v": "/RdcRotor/ProcessData",
                "vt": "str"
            },
            {
                "t": "eq",
                "v": "/RdcRotor/FilterSelection",
                "vt": "str"
            },
            {
                "t": "eq",
                "v": "/RdcStator/Id",
                "vt": "str"
            },
            {
                "t": "eq",
                "v": "/RdcStator/Version",
                "vt": "str"
            },
            {
                "t": "eq",
                "v": "/RdcStator/Ping",
                "vt": "str"
            },
            {
                "t": "eq",
                "v": "/RdcStator/RotorState",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 8,
        "x": 750,
        "y": 580,
        "wires": [
            [
                "042716afe2805ece"
            ],
            [
                "042716afe2805ece"
            ],
            [
                "042716afe2805ece",
                "5b54d87eea327336"
            ],
            [
                "7269cbf9acfb8d6d"
            ],
            [
                "042716afe2805ece"
            ],
            [
                "042716afe2805ece"
            ],
            [
                "042716afe2805ece"
            ],
            [
                "7972da5c33dc4d47"
            ]
        ]
    },
    {
        "id": "7269cbf9acfb8d6d",
        "type": "function",
        "z": "9727d69723ecfd0b",
        "name": "code to text",
        "func": "var newMsg = msg;\n\nswitch (msg.payload.Value) {\n    case \"0\":\n        newMsg.payload = \"1Hz\";\n        break;\n    case \"1\":\n        newMsg.payload = \"10Hz\";\n        break;\n    case \"2\":\n        newMsg.payload = \"100Hz\";\n        break;\n    case \"3\":\n        newMsg.payload = \"200Hz\";\n        break;\n    case \"4\":\n        newMsg.payload = \"500Hz\";\n        break;\n    case \"5\":\n        newMsg.payload = \"1000Hz\";\n        break;\n    case \"6\":\n        newMsg.payload = \"1500Hz\";\n        break;\n    default:\n        newMsg.payload = \"Undefined filter selection\";       \n}\nreturn newMsg;",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 930,
        "y": 620,
        "wires": [
            [
                "042716afe2805ece"
            ]
        ]
    },
    {
        "id": "042716afe2805ece",
        "type": "debug",
        "z": "9727d69723ecfd0b",
        "name": "",
        "active": true,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "payload",
        "targetType": "msg",
        "statusVal": "",
        "statusType": "auto",
        "x": 1110,
        "y": 480,
        "wires": []
    },
    {
        "id": "049df9678ec9193c",
        "type": "inject",
        "z": "9727d69723ecfd0b",
        "name": "GET Ping",
        "props": [
            {
                "p": "topic",
                "vt": "str"
            },
            {
                "p": "method",
                "v": "GET",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "/RdcStator/Ping",
        "x": 460,
        "y": 200,
        "wires": [
            [
                "a7a4c91a978d91d8"
            ]
        ]
    },
    {
        "id": "be9f4ededa40b1cd",
        "type": "inject",
        "z": "9727d69723ecfd0b",
        "name": "GET FilterSelection",
        "props": [
            {
                "p": "topic",
                "vt": "str"
            },
            {
                "p": "method",
                "v": "GET",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "/RdcRotor/FilterSelection",
        "x": 930,
        "y": 240,
        "wires": [
            [
                "097716d0ef7d081f"
            ]
        ]
    },
    {
        "id": "eddc45236547d633",
        "type": "comment",
        "z": "9727d69723ecfd0b",
        "name": "Dashboard example",
        "info": "",
        "x": 130,
        "y": 780,
        "wires": []
    },
    {
        "id": "b046e516f4e20aff",
        "type": "ui_gauge",
        "z": "9727d69723ecfd0b",
        "name": "Temp",
        "group": "781b6815d353fd77",
        "order": 6,
        "width": "4",
        "height": "4",
        "gtype": "gage",
        "title": "Temperature",
        "label": "[°C]",
        "format": "{{payload}}",
        "min": "-30",
        "max": "+70",
        "colors": [
            "#ca3838",
            "#00e62e",
            "#ca3838"
        ],
        "seg1": "-20",
        "seg2": "+60",
        "diff": false,
        "className": "",
        "x": 870,
        "y": 1180,
        "wires": []
    },
    {
        "id": "5b54d87eea327336",
        "type": "link out",
        "z": "9727d69723ecfd0b",
        "name": "ProcessData OUT",
        "mode": "link",
        "links": [
            "0a5d113a6e5c82a4"
        ],
        "x": 875,
        "y": 700,
        "wires": []
    },
    {
        "id": "0a5d113a6e5c82a4",
        "type": "link in",
        "z": "9727d69723ecfd0b",
        "name": "ProcessData IN",
        "links": [
            "5b54d87eea327336"
        ],
        "x": 505,
        "y": 900,
        "wires": [
            [
                "318644f53ba22325",
                "f8c1f85193b3757d"
            ]
        ]
    },
    {
        "id": "eb2f147f325cd204",
        "type": "link out",
        "z": "9727d69723ecfd0b",
        "name": "Get ProcessData",
        "mode": "link",
        "links": [
            "599e9084409c85d6"
        ],
        "x": 395,
        "y": 900,
        "wires": []
    },
    {
        "id": "599e9084409c85d6",
        "type": "link in",
        "z": "9727d69723ecfd0b",
        "name": "request in",
        "links": [
            "097716d0ef7d081f",
            "103eae957d48552a",
            "a7a4c91a978d91d8",
            "eb2f147f325cd204"
        ],
        "x": 55,
        "y": 580,
        "wires": [
            [
                "675eed9aa05362d5"
            ]
        ]
    },
    {
        "id": "a9cfa9a39e34ca4a",
        "type": "comment",
        "z": "9727d69723ecfd0b",
        "name": "ERROR catcher",
        "info": "",
        "x": 120,
        "y": 1240,
        "wires": []
    },
    {
        "id": "87143520124f5ca5",
        "type": "inject",
        "z": "9727d69723ecfd0b",
        "name": "PUT Reset MIN/MAX",
        "props": [
            {
                "p": "topic",
                "vt": "str"
            },
            {
                "p": "payload"
            },
            {
                "p": "method",
                "v": "PUT",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "/RdcRotor/Reset",
        "payload": "{\"Value\":17}",
        "payloadType": "json",
        "x": 940,
        "y": 300,
        "wires": [
            [
                "097716d0ef7d081f"
            ]
        ]
    },
    {
        "id": "dc613f613741ed3b",
        "type": "ui_button",
        "z": "9727d69723ecfd0b",
        "name": "",
        "group": "781b6815d353fd77",
        "order": 8,
        "width": "2",
        "height": "1",
        "passthru": false,
        "label": "RST min/max",
        "tooltip": "",
        "color": "",
        "bgcolor": "",
        "className": "",
        "icon": "",
        "payload": "true",
        "payloadType": "bool",
        "topic": "reset min/max",
        "topicType": "str",
        "x": 280,
        "y": 1100,
        "wires": [
            [
                "9098e62bec13320f"
            ]
        ]
    },
    {
        "id": "9098e62bec13320f",
        "type": "function",
        "z": "9727d69723ecfd0b",
        "name": "create reset-code 0x11",
        "func": "var obj = {Value: 0x11};\nvar newMsg1 = { topic: \"/RdcRotor/Reset\", payload: obj, method: \"PUT\"};\n\nreturn newMsg1;\n",
        "outputs": 1,
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 620,
        "y": 1100,
        "wires": [
            [
                "103eae957d48552a",
                "c5cf20cc9bc893e2"
            ]
        ]
    },
    {
        "id": "103eae957d48552a",
        "type": "link out",
        "z": "9727d69723ecfd0b",
        "name": "PUT",
        "mode": "link",
        "links": [
            "599e9084409c85d6"
        ],
        "x": 835,
        "y": 1240,
        "wires": []
    },
    {
        "id": "c5cf20cc9bc893e2",
        "type": "debug",
        "z": "9727d69723ecfd0b",
        "name": "",
        "active": false,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "true",
        "targetType": "full",
        "statusVal": "",
        "statusType": "auto",
        "x": 870,
        "y": 1280,
        "wires": []
    },
    {
        "id": "a7a4c91a978d91d8",
        "type": "link out",
        "z": "9727d69723ecfd0b",
        "name": "request OUT 1",
        "mode": "link",
        "links": [
            "599e9084409c85d6"
        ],
        "x": 715,
        "y": 120,
        "wires": []
    },
    {
        "id": "097716d0ef7d081f",
        "type": "link out",
        "z": "9727d69723ecfd0b",
        "name": "request OUT 2",
        "mode": "link",
        "links": [
            "599e9084409c85d6"
        ],
        "x": 1155,
        "y": 120,
        "wires": []
    },
    {
        "id": "a48d7c2b4bceec7d",
        "type": "comment",
        "z": "9727d69723ecfd0b",
        "name": "http request flow",
        "info": "",
        "x": 120,
        "y": 460,
        "wires": []
    },
    {
        "id": "b65e95628f71083c",
        "type": "inject",
        "z": "9727d69723ecfd0b",
        "name": "GET RunMode",
        "props": [
            {
                "p": "topic",
                "vt": "str"
            },
            {
                "p": "method",
                "v": "GET",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "/RdcStator/RunMode",
        "x": 480,
        "y": 280,
        "wires": [
            [
                "a7a4c91a978d91d8"
            ]
        ]
    },
    {
        "id": "0ee3ad5fc0b32d70",
        "type": "inject",
        "z": "9727d69723ecfd0b",
        "name": "PUT RunMode OFF",
        "props": [
            {
                "p": "topic",
                "vt": "str"
            },
            {
                "p": "payload"
            },
            {
                "p": "method",
                "v": "PUT",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "/RdcStator/RunMode",
        "payload": "{\"Value\":0}",
        "payloadType": "json",
        "x": 490,
        "y": 340,
        "wires": [
            [
                "a7a4c91a978d91d8"
            ]
        ]
    },
    {
        "id": "bb6a0a5add36bfaf",
        "type": "debug",
        "z": "9727d69723ecfd0b",
        "name": "",
        "active": false,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "true",
        "targetType": "full",
        "statusVal": "",
        "statusType": "auto",
        "x": 350,
        "y": 480,
        "wires": []
    },
    {
        "id": "6f726e0293aafd42",
        "type": "inject",
        "z": "9727d69723ecfd0b",
        "name": "PUT RunMode ON",
        "props": [
            {
                "p": "topic",
                "vt": "str"
            },
            {
                "p": "payload"
            },
            {
                "p": "method",
                "v": "PUT",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "/RdcStator/RunMode",
        "payload": "{\"Value\":1}",
        "payloadType": "json",
        "x": 490,
        "y": 380,
        "wires": [
            [
                "a7a4c91a978d91d8"
            ]
        ]
    },
    {
        "id": "05a0b3ac66fbb92f",
        "type": "comment",
        "z": "9727d69723ecfd0b",
        "name": "README",
        "info": "For the PUT commands to work correctly,    \nbasic authentication must be selected in the \"http request\" node.   \ne.g.:    \nUser:     Customer    \nPassword: kx2426d00331!    \n\nThe host name must be entered in the function node \"init\"!   \ne.g.:   \nHostname: http://rdc-2426d00331",
        "x": 100,
        "y": 200,
        "wires": []
    },
    {
        "id": "22c3de8d9091945a",
        "type": "ui_spacer",
        "z": "9727d69723ecfd0b",
        "name": "spacer",
        "group": "781b6815d353fd77",
        "order": 9,
        "width": "2",
        "height": "1"
    },
    {
        "id": "781b6815d353fd77",
        "type": "ui_group",
        "name": "live Chart",
        "tab": "2ca03eff3ffc7e09",
        "order": 3,
        "disp": true,
        "width": "12",
        "collapse": false
    },
    {
        "id": "2ca03eff3ffc7e09",
        "type": "ui_tab",
        "name": "ROBA-drive-checker",
        "icon": "dashboard",
        "order": 1,
        "disabled": false,
        "hidden": false
    }
]
```
[Back to REST-API overview](../README.md)


[def]: #roba-drive-checker-node-red-examples