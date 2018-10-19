# API Calls for the Xicato Intelligent Gateway
----
-----

# A Note on Authorization
----
  All calls, except the call to get the API token and the call to check user permissions accept two forms of authorization:
  * Basic, using the HTTP "Authorization: Basic" headers and the username and password colon-separated and then base64-encoded.
  * Bearer, using the API token provided by the `/api/token` call.

  If a call is made to a URL that the anonymous user has access/permissions to, auth headers will be ignored.

  **Permissions are per network per gateway. If multiple gateways are being accessed simultaneously, care should be taken by the XIG administrator (i.e., the user logging into and configuring the gateways through the XIG Admin Panel) to ensure that the permissions for a given user are consistent across the gateways they are given access to.**

----

## Get Token (Login Required)
**Change Status:** No API call changes made in V1.7.0. 

  Returns a JSON Web Token to be used with Bearer Authorization.
* **URL:**

  `/api/token`

* **Method:**

  `GET`

* **Permission:**

  N/A. Tokens are accessible once a user has logged in (i.e., non-anonymous user).

* **URL Parameters:**

  None.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200<br/>
  **Content:** `/api/token` returns a JSON Web Token in the form of an encoded string. 
  This string should be used with the HTTP "Authorization: Bearer" header in lieu of sending out Basic Auth headers.

* **Error Responses:**
  * **Code:** 403 Forbidden<br/>
  **Meaning:** Bad credentials were used.

* **Notes:**
  Current as of 2017-7-31.
----

## Check User Permissions (Login Required)
**Change Status:** No API call changes made in V1.7.0. Documentation updated to clarify command details specifying secure network. 

  Returns the permissions that the currently logged in user has. 
* **URLs:**

    `/check_user/:network`
    
    If network is omitted, the user permissions for the unsecured network will be returned.


* **Method:**

  `GET`

* **Permission:**

  N/A. Permissions checks are accessible once a user has logged in (i.e., non-anonymous user).

* **URL Parameters:**

  /None.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200<br/>
  **Content:** `/check_user/:network` returns a string with the list of permissions on the specified secure network for the user that is logged in.

* **Error Responses:**
  * **Code:** 403 Forbidden<br/>
  **Meaning:** Bad credentials were used.

* **Notes:**
  Current as of 2018-4-9.
----
----
# View Permission API Calls
## Show Devices
 **Change Status:** No API call changes made in V1.7.0. 
 
  Return a list of devices seen by the given gateway, along with some information about the gateway's status. 

  To see a pretty-printed version of this call, use the `/pretty` modifier; this is _*NOT*_ interchangeable with the regular version as it is wrapped in HTML tags so it will render in a more human readable format.
* **URL:**

  `/devices`

  `/devices/pretty`

* **Method:**

  `GET`

* **Permission:**

  `view`

* **URL Parameters:**

  None.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/devices` returns JSON:
    ```
    { "network" : Nullable String
    , "networks" : List String
    , "connectable" : Boolean
    , "devices" : List Device
    , "sensors" : List Sensor
    , "switches" : List Switch
    , "temperature" : Float
    }
    ```
    (Nullable means that the field contains either just a value of that type, or null. This is equivalent to Haskell's, Elm's, etc. Maybe-Just-Nothing concept)

    Each Device is a JSON dict containing the following fields and their associated types:
    ```
    { "01. Device ID" : String (for various reasons, this can't be treated as an integer although it almost always is)
    , "02. Name" : Nullable String
    , "03. Device" : Nullable String
    , "04. Intensity" : Float
    , "05. Power" : Nullable Float
    , "06. Tc temperature" : Integer
    , "07. supply_voltage" : Nullable Float
    , "08. on_hours" : Nullable Integer
    , "09. signal_strength" : Nullable Integer
    , "10. status" : Nullable Integer
    , "11. Last Update" : Nullable Float
    , "12. Adv Interval" : Nullable Float
    , "NetworkName" : Nullable String
    }
    ```
    Each Sensor is a JSON dict containing the following fields and their associated types:
    ```
    { "01. Device ID" : String
    , "02. Name" : Nullable String
    , "03. Device" : Nullable String
    , "04. Lux" : List (Nullable Int)
    , "05. Motion" : List (Nullable Float)
    , "06. Temperature" : Nullable Int
    , "07. supply_voltage" : Nullable Float
    , "08. Humidity" : Nullable Float
    , "09. signal_strength" : Nullable Integer
    , "10. status" : Nullable Integer
    , "11. Last Update" : Nullable Float
    , "12. Adv Interval" : Nullable Float
    , "NetworkName" : Nullable String
    , "LuxHours" : List (Nullable Float)
    }
    ```
    Each Switch is a JSON dict containing the following fields and their associated types:
    ```
    { "01. Device ID" : String
    , "02. Name" : Nullable String
    , "03. Device" : Nullable String
    , "04. Button Data" : List 
      { "last_press": Nullable Float
      , "last_release": Nullable Float
      , "state": Boolean
      }
    , "05. PCB temperature" : Nullable Int
    , "06. supply_voltage" : Nullable Float
    , "07. signal_strength" : Nullable Integer
    , "08. status" : Nullable Integer
    , "09. Last Update" : Nullable Float
    , "10. Last Press" : Nullable Float
    , "NetworkName" : Nullable String
    }
    ```

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, a `/reload` request should be sent to the gateway to clear the issue. For further analysis, pull the logs from the gateway through the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Notes:**

  Current as of 2017-7-12

----
## Show Groups with Devices
 **Change Status:** API call added in V1.7.2. 
 
  Return a list of groups with all devices contained in each group as seen by the given gateway, along with some information about the gateway's status.

  To see a pretty-printed version of this call, use the `/pretty` modifier; this is _*NOT*_ interchangeable with the regular version as it is wrapped in HTML tags so it will render in a more human readable format.
* **URL:**

  `/groups`
  
  `/groups/pretty`

* **Method:**

  `GET`

* **Permission:**

  `view`

* **URL Parameters:**

  None.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/groups` returns JSON:
    ```
    { "network" : Nullable String
    , "networks" : List String
    , "connectable" : Boolean
    , "groups" : List Group
    , "temperature" : Float
    }
    ```
    (Nullable means that the field contains either just a value of that type, or null. This is equivalent to Haskell's, Elm's, etc. Maybe-Just-Nothing concept)

    Each Group is a JSON dict containing the following fields and their associated types:
    ```
    { "devices": List
      { "01. Device ID" : String
      , "02. Name" : Nullable String
      , "03. Device" : Nullable String
      , "04. Intensity" : Float
      , "05. Power" : Nullable Float
      , "06. Tc temperature" : Integer
      , "07. supply_voltage" : Nullable Float
      , "08. on_hours" : Nullable Integer
      , "09. signal_strength" : Nullable Integer
      , "10. status" : Nullable Integer
      , "11. Last Update" : Nullable Float
      , "12. Adv Interval" : Nullable Float
      , "NetworkName" : Nullable String
      }
    , "groupId" : Nullable Integer (null indicates devices without any group membership)
    , "groupName" : String
    }
    ```
* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, a `/reload` request should be sent to the gateway to clear the issue. For further analysis, pull the logs from the gateway through the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Notes:**

  Current as of 2017-7-12

 ----
## Show Devices with Groups
**Change Status:** No API call changes made in V1.7.0. 
 
  Return a list of devices at the given gateway, along with some information
* **URL**

  `/devices/with_groups`

* **Method:**

  `GET`

* **Permission:**

  `view`

*  **URL Parameters**

  None.

* **Data Parameters**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/devices/with_groups` returns JSON:
    ```
    { "network" : Nullable String
    , "networks" : List String
    , "connectable" : Boolean
    , "devices" : List Device
    , "sensors" : List Sensor
    , "groups" : List Group
    , "temperature" : Float
    }
    ```
    (Nullable means that the field contains either just a value of that type, or null. This is equivalent to Haskell's, Elm's, etc. Maybe-Just-Nothing concept.)

    Each Device is a JSON dict containing the following fields and their associated types:
    ```
    { "00. Index" : Integer
    , "01. Device ID" : String (for various reasons, this can't be treated as an integer although it almost always is)
    , "02. Name" : Nullable String
    , "03. Device" : Nullable String
    , "04. Intensity" : Float
    , "05. Power" : Nullable Float
    , "06. Tc temperature" : Integer
    , "07. supply_voltage" : Nullable Float
    , "08. on_hours" : Nullable Integer
    , "09. signal_strength" : Nullable Integer
    , "10. status" : Nullable Integer
    , "11. Last Update" : Nullable Float
    , "12. Adv Interval" : Nullable Float
    , "NetworkName" : Nullable String
    }
    ```

    Each Sensor is a JSON dict containing the following fields and their associated types:
    ```
    { "01. Device ID" : String
    , "02. Name" : Nullable String
    , "03. Device" : Nullable String
    , "04. Lux" : List (Nullable Int)
    , "05. Motion" : List (Nullable Float)
    , "06. Temperature" : Nullable Int
    , "07. supply_voltage" : Nullable Float
    , "08. Humidity" : Nullable Float
    , "09. signal_strength" : Nullable Integer
    , "10. status" : Nullable Integer
    , "11. Last Update" : Nullable Float
    , "12. Adv Interval" : Nullable Float
    , "NetworkName" : Nullable String
    , "LuxHours": List (Nullable Float)
    }
    ```

    Each Group is a JSON dict containing the following fields and their associated types:
    ```
    { "groupName" : String
    , "groupId" : Nullable Integer (this is always an integer, but it can be null in the case of ungrouped devices)
    , "devices" : Optional Nullable List Device
    , "count" : Optional Nullable Integer
    , "network" : Nullable String
    }
    ```
    (Optional means that the field may or may not be present. In this case, one of either devices or count will always be present, not both or neither.)

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, a `/reload` request should be sent to the gateway to clear the issue. For further analysis, pull the logs from the gateway through the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Notes:**

  Current as of 2017-10-3

----
## Show Devices with Scenes
**Change Status:** No API call changes made in V1.7.0.  
 
  Return a list of devices at the given gateway, along with scene information
* **URLs:**

  `/devices/with_scenes`

* **Method:**

  `GET`

* **Permission:**

  `view`

* **URL Parameters:**

  None.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/devices/with_scenes` returns JSON:
    ```
    { "network" : Nullable String
    , "networks" : List String
    , "connectable" : Boolean
    , "devices" : List Device
    , "sensors" : List Sensor
    , "scenes" : List Scene
    , "temperature" : Float
    }
    ```
    (Nullable means that the field contains either just a value of that type, or null. This is equivalent to Haskell's, Elm's, etc. Maybe-Just-Nothing concept.)

    Each Device is a JSON dict containing the following fields and their associated types:
    ```
    { "00. Index" : Integer
    , "01. Device ID" : String (for various reasons, this can't be treated as an integer although it almost always is)
    , "02. Name" : Nullable String
    , "03. Device" : Nullable String
    , "04. Intensity" : Float
    , "05. Power" : Nullable Float
    , "06. Tc temperature" : Integer
    , "07. supply_voltage" : Nullable Float
    , "08. on_hours" : Nullable Integer
    , "09. signal_strength" : Nullable Integer
    , "10. status" : Nullable Integer
    , "11. Last Update" : Nullable Float
    , "12. Adv Interval" : Nullable Float
    , "NetworkName" : Nullable String
    }
    ```

    Each Sensor is a JSON dict containing the following fields and their associated types:
    ```
    { "01. Device ID" : String
    , "02. Name" : Nullable String
    , "03. Device" : Nullable String
    , "04. Lux" : List (Nullable Int)
    , "05. Motion" : List (Nullable Float)
    , "06. Temperature" : Nullable Int
    , "07. supply_voltage" : Nullable Float
    , "08. Humidity" : Nullable Float
    , "09. signal_strength" : Nullable Integer
    , "10. status" : Nullable Integer
    , "11. Last Update" : Nullable Float
    , "12. Adv Interval" : Nullable Float
    , "NetworkName" : Nullable String
    , "LuxHours" : List (Nullable Float)
    }
    ```

    Each Scene is a JSON dict containing the following fields and their associated types:
    ```
    { "name" : Nullable String
    , "number" : Integer
    }
    ```

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, a `/reload` request should be sent to the gateway to clear the issue. For further analysis, pull the logs from the gateway through the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Notes:**

  Current as of 2017-7-12


----
## Show Devices with Groups and Scenes
**Change Status:** No API call changes made in V1.7.0.  
 
  Return a list of devices at the given gateway, along with some information
* **URLs**

  `/devices/with_everything`

  `/devices/with_groups_and_scenes`

  `/devices/with_scenes_and_groups`

* **Method:**

  `GET`

* **Permission:**

  `view`

* **URL Parameters**

  None.

* **Data Parameters**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** /devices returns JSON:
    ```
    { "network" : Nullable String
    , "networks" : List String
    , "connectable" : Boolean
    , "devices" : List Device
    , "sensors" : List Sensor
    , "groups" : List Group
    , "scenes" : List Scene
    , "temperature" : Float
    }
    ```
    (Nullable means that the field contains either just a value of that type, or null. This is equivalent to Haskell's, Elm's, etc. Maybe-Just-Nothing concept.)

    Each Device is a JSON dict containing the following fields and their associated types:
    ```
    { "00. Index" : Integer
    , "01. Device ID" : String (for various reasons, this can't be treated as an integer although it almost always is)
    , "02. Name" : Nullable String
    , "03. Device" : Nullable String
    , "04. Intensity" : Float
    , "05. Power" : Nullable Float
    , "06. Tc temperature" : Integer
    , "07. supply_voltage" : Nullable Float
    , "08. on_hours" : Nullable Integer
    , "09. signal_strength" : Nullable Integer
    , "10. status" : Nullable Integer
    , "11. Last Update" : Nullable Float
    , "12. Adv Interval" : Nullable Float
    , "NetworkName" : Nullable String
    }
    ```

    Each Sensor is a JSON dict containing the following fields and their associated types:
    ```
    { "01. Device ID" : String
    , "02. Name" : Nullable String
    , "03. Device" : Nullable String
    , "04. Lux" : List (Nullable Int)
    , "05. Motion" : List (Nullable Float)
    , "06. Temperature" : Nullable Int
    , "07. supply_voltage" : Nullable Float
    , "08. Humidity" : Nullable Float
    , "09. signal_strength" : Nullable Integer
    , "10. status" : Nullable Integer
    , "11. Last Update" : Nullable Float
    , "12. Adv Interval" : Nullable Float
    , "NetworkName" : Nullable String
    , "LuxHours" : List (Nullable Float)
    }
    ```

    Each Group is a JSON dict containing the following fields and their associated types:
    ```
    { "groupName" : String
    , "groupId" : Nullable Integer (this is always an integer, but it can be null in the case of ungrouped devices)
    , "devices" : Optional Nullable List Device
    , "count" : Optional Nullable Integer
    , "network" : Nullable String
    }
    ```
    (Optional means that the field may or may not be present. In this case, one of either devices or count will always be present, not both or neither.)

    Each Scene is a JSON dict containing the following fields and their associated types:
    ```
    { "name" : Nullable String
    , "number" : Integer
    }
    ```

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, a `/reload` request should be sent to the gateway to clear the issue. For further analysis, pull the logs from the gateway through the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Notes:**

  Current as of 2017-10-3

  To see a pretty-printed version of this call, issue a `GET` request to `/devices/with_everything/pretty`; this is _*NOT*_ interchangeable with the regular version as it is wrapped in HTML tags so it will render properly.

----
## Histograms
**Change Status:** No API call changes made in V1.7.0. Documentation updated to clarify command details specifying secure network. 

  Return an intensity or temperature histogram for a given device.

* **URLs**

  `/device/histogram/:type/:network/:device_id`

* **Methods:**

  `GET` | `POST`

* **Permission:**

  `view`

* **URL Parameters:**

  Required:
    * `network` : The secure network the device that the histogram data is being collected from. If a network value is not provided, the results of the command are non-deterministic. To access devices that have not been assigned to a secure network, the name unsecured should be used.
    * `type` : The type of histogram: either `temperature` or `intensity`
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/histogram` returns a list of JSON key-value pairs with the keys sorted, of the form:
    ```
    { <label> : <value>
    }
    ```
    , where `label` is a String, and `value` is a Float.

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, a `/reload` request should be sent to the gateway to clear the issue. For further analysis, pull the logs from the gateway through the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call:**

  `curl <gateway address: port>/device/histogram/temperature/Xicato/111` gets the temperature histogram for device 111 on the Xicato secure network.

* **Notes:**

  Current as of 2017-7-12.

  Occasionally, the gateway may return an empty list. This means it hasn't had the opportunity to retrieve the histogram from the device yet. Please allow some time for histograms to populate into the device list.

----
## Device Details
**Change Status:** No API call changes made in V1.7.0. Documentation updated to clarify command details specifying secure network. 

  Retrieve detailed information for a given device.

* **URLs**

  `/device/details/:network/:device_id`

* **Methods:**

  `GET` | `POST`

* **Permission:**

  `view`

* **URL Parameters:**

  Required:
    * `network` : The secure network the target device is on. 
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/details` returns JSON:
    ```
    { "color_temp": Optional String
      "BLE_FW_ver": Optional String
      "fixture_part_number": Optional Nullable String
      "power_cycles": Optional Integer
      "supply_ripple": Optional Integer
      "XIM_FW_ver": Optional String
      "LED_cycles": Optional Integer
      "Xicato_PN": Optional String
      "HW_version": Optional String
      "fixture_manufacturer": Optional Nullable String
      "fixture_flux": Optional Nullable String
      "manufacturer": Optional String
      "PCB temperature": Optional Integer
      "BLE Address": Optional String
      "flux": Optional String
      "cri": Optional String
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, a `/reload` request should be sent to the gateway to clear the issue. For further analysis, pull the logs from the gateway through the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call:**

  `curl <gateway address: port>/device/details/MyNetwork/1131` gets the details for device 1131 on the secure network MyNetwork.

* **Notes:**

  Current as of 2017-7-31.

----
## Get Device Groups
**Change Status:** No API call changes made in V1.7.0. Documentation updated to clarify command details specifying secure network. 

  Gets the group membership for a given device (XID or XIM).

* **URLs**

  *`/device/groups/:network/:device_id`

  *`/device/getgroups/:network/:device_id`

* **Methods:**

  `GET`

* **Permission:**

  `view`

* **URL Parameters:**

  Required:
    * `network` : The secure network the given device is on. If the  network value is not entered, the return value for network will be null and other data returned may be incorrect.
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/groups/:network` returns JSON:
  ```
  { "network" : String
  , "device_id" : String
  , "groups" : List Integer
  }
  ```

* **Error Responses:**
  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means device ID provided did not match an ID in the device list.

* **Example Call:**

  `curl <gateway address: port>/device/groups/unsecured/111` gets the group membership from device 111 that is not on a secure network (network name is a null value).
* **Notes:**

  Current as of 2017-9-20.

----
## Get Device Scenes
**Change Status:** No API call changes made in V1.7.0. Documentation updated to clarify command details specifying secure network. 

  Gets the scene configuration for a given device (XID or XIM).

* **URLs**

  *`/device/scenes/:network/:device_id`

  *`/device/getscenes/:network/:device_id`

* **Methods:**

  `GET`

* **Permission:**

  `view`

* **URL Parameters:**

  Required:
    * `network` : The secure network for the target device.  
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/scenes` returns JSON:
  ```
  { "device_id" : String
  , "scenes" : List Scene
  }
  ```
  where each Scene is as follows:
  ```
    { "sceneNumber" : Integer, 0 < n < 65535
    , "intensity" : Float, 0.0 <= m <= 100.0
    , "delayTime" : Integer, n >= 0
    , "fadeTime" : Integer, 0 <= n <= 1.44e7
    }
  ```

* **Error Responses:**
  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means device ID provided did not match an ID on the device list.

* **Example Call:**

  `curl <gateway address: port>/device/scenes/GalaXi/111` gets the scene configuration for device 111 on the secure network GalaXi.
* **Notes:**

  Current as of 2017-9-20.

----
## Basic Gateway Info
**Change Status:** No API call changes made in V1.7.0.  

  Returns some basic information about the gateway.

* **URLs**

  `/gateway_info/`

* **Method:**

  `GET`

* **Permission:**

  `view`

* **URL Parameters**

  None.

* **Data Parameters**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** /devices returns JSON:
    ```
    { "network" : Nullable String
    , "networks" : List String
    , "connectable" : Boolean
    , "temperature" : Float
    }
    ```
    (Nullable means that the field contains either just a value of that type, or null. This is equivalent to Haskell's, Elm's, etc. Maybe-Just-Nothing concept.)

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call:**

  `curl <gateway address: port>/gateway_info` gets the basic gateway info

* **Notes:**

  Current as of 2017-2-24

----
## Gateway Status
**Change Status:** No API call changes made in V1.7.0.  

  Get the status of the gateway, in string form (as opposed to the `/gateway_info` call).

* **URLs**

  `/status`

* **Method:**

  `GET`

* **Permission:**

  `view`

* **URL Parameters**

  None.

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** /devices returns one of two strings:
    * "Gateway running, CPU at {temperature}" -- The gateway is up, and the CPU temperature is {temperature}.
    * "Gateway not running" -- This shouldn't appear, now that the gateway auto-reloads on internal errors; if it does, restart the gateway.

* **Error Responses**
  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call**

  `curl <gateway address: port>/status` gets the gateway's status.

* **Notes**

  Current as of 2017-2-24

----
## Download Gateway Groups List
**Change Status:** The API call return data has changed in V1.7.2. The call now returns a JSON dict specifying each secure network along with all of the explicitly defined group names and their associated IDs. If a group name is not defined for a given group ID on a given network, then no information for that group ID will be returned. Previous versions of this call returned a CSV file that did not specify groups by network. For a complete list of discovered groups with networks specified use the call `/devices/with_groups`. For a complete list of groups with associated networks and devices use the call `/groups`.   

  Download `groups.txt` list from the XIG.

* **URLs**

  `/groups.txt`<br/>
  `/Groups.txt`<br/>
  `/download_groups`

* **Method:**

  `GET`

* **Permission:**

  `view`

* **URL Parameters**

  None.

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/groups.txt` will return the `groups.txt` file from the specified server.

* **Error Responses**
  * **Code:** 404 NOT FOUND<br />
  **Meaning:** The `groups.txt` file doesn't exist on the gateway or the server isn't up.

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call**

  `curl <gateway address: port>/groups.txt` gets the gateway's `groups.txt`.

* **Notes**

  Current as of 2018-5-21.

----
## Download Gateway Scenes List
**Change Status:** The API call return data has changed in V1.7.2. The call now returns a JSON dict specifying each secure network along with all of the explicitly defined scene names and their associated IDs. If a scene name is not defined for a given scene ID on a given network, then no information for that scene ID will be returned. Previous versions of this call returned a CSV file that did not specify scenes by network. For a complete list of discovered scenes with networks specified use the call `/devices/with_groups_and_scenes`.   

  Download `scenes.txt` list from the XIG.

* **URLs**

  `/scenes.txt`
  `/Scenes.txt`
  `/download_scenes`

* **Method:**

  `GET`

* **Permission:**

  `view`

* **URL Parameters**

  None.

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/scenes.txt` will return the `scenes.txt` file from the specified server.

* **Error Responses**
* **Error Responses**
  * **Code:** 404 NOT FOUND<br />
  **Meaning:** The `scenes.txt` file doesn't exist on the gateway or the server isn't up.

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call**

  `curl <gateway address: port>/scenes.txt` gets the gateway's `scenes.txt`.

* **Notes**

  Current as of 2018-5-21.

----
# Control Permission API Calls
----
----
## Set Intensity (with Optional Fading)
**Change Status:** No API call changes made in V1.7.0. Documentation updated to clarify command details specifying secure network. 
 
  Set intensity for a device or a group of devices, with optional fade time.
* **URLs:**

  `/device/setintensity/:network/:device_id/:intensity/`

  To use fading:
  `/device/setintensity/:network/:device_id/:intensity/:fading`

* **Methods:**

  `GET` | `POST`

* **Permission:**

  `control`

* **URL Parameters:**

  Required:
    * `network` : The secure network of the target device. If a network value is not provided, the results of the command are non-deterministic. To access devices that have not been assigned to a secure network, the name unsecured should be used.
    * `id` : The device id of the target device or group of devices. For groups add 49152 (`0xC000`) to the int value of the group id before casting to a string.
    * `intensity` : The target intensity, in percent. This can be 0 or any positive float or integer between 0.1 and 100.0

  Optional:
    * `fading` : The fade time, in milliseconds. This can be any positive integer but obviously very large values will not have particularly useful results.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** JSON:
    ```
    { "device_id" : String
    , "intensity" : Float
    , "fading" : Optional Integer
    }
    ```
    (Optional means that the field may or may not be present. In this case, fading will only be present if a fading value is used.)

* **Error Response:**

  * **Code:** 403 Forbidden <br />
  **Meaning** Controls haven't been enabled for the logged in used on the gateway receiving the request. Enable controls and try again.

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, a `/reload` request should be sent to the gateway to clear the issue. For further analysis, pull the logs from the gateway through the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call:**

  ` curl <gateway address: port>/device/setintensity/unsecured/111/50/5000` sets the intensity of the device with id 111 to 50% over 5 seconds

* **Notes:**

  Current as of 2016-12-20

----
## Multi-Channel Set Intensity (with Optional Fading)
**Change Status:** New in 1.7.2. 
 
  Set intensity for a device or a group of devices, with optional fade time.
* **URLs:**

  `/device/multisetintensity/:network/:device_id/:master_intensity/:intensity1/:intensity2/:intensity3/:intensity4/`<br/>
  `/device/multisetintensity/:network/:device_id/:master_intensity/:intensities`

  To use fading:
  `/device/multisetintensity/:network/:device_id/:master_intensity/:intensity1/:intensity2/:intensity3/:intensity4/:fading`<br/>
  `/device/multisetintensity/:network/:device_id/:master_intensity/:intensities/:fading`

* **Methods:**

  `GET` | `POST`

* **Permission:**

  `control`

* **URL Parameters:**

  Required:
    * `network` : The secure network of the target device. If a network value is not provided, the results of the command are non-deterministic. To access devices that have not been assigned to a secure network, the name unsecured should be used.
    * `id` : The device id of the target device (master channel).
    * `master_intensity` : The target intensity of the master channel, in percent. This can be 0 or any positive float or integer between 0.1 and 100.0
    * Channel intensities: either each one in its own field (`/1/2/3/4`), or in list form (`[1,2,3,4]`).

  Optional:
    * `fading` : The fade time, in milliseconds. This can be any positive integer but obviously very large values will not have particularly useful results.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** JSON:
    ```
    { "device_id" : String
    , "network" : String
    , "master_intensity" : Float
    , "channel_intensities" : List Float
    , "fading" : Optional Integer
    }
    ```
    (Optional means that the field may or may not be present. In this case, fading will only be present if a fading value is used.)

* **Error Response:**

  * **Code:** 403 Forbidden <br />
  **Meaning** Controls haven't been enabled for the logged in used on the gateway receiving the request.

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error.

* **Example Call:**

  ` curl <gateway address: port>/device/multisetintensity/unsecured/4445/100/[1.0,20,3.0,40]/5000` sets device 111's master channel to 100%, and its 4 individual channels to 1.0%, 20%, 3.0%, and 40% respectively, over 5 seconds.

* **Notes:**

  Current as of 2018-8-14.

----
## Recall Scene (with Optional Fading)
**Change Status:** No API call changes made in V1.7.0. Documentation updated to clarify command details specifying secure network. 

  Recall a scene on a light or a group of lights, with optional fade time.
* **URLs:**

  `/device/recallscene/:network/:device_id/:sceneNumber/`

  To use fading:
  `/device/recallscene/:network/:device_id/:sceneNumber/:fading`

* **Methods:**

  `GET` | `POST`

* **Permission:**

  `control`

* **URL Parameters:**

  Required:
    * `network` : The secure network that the recall scene command will be issued to. If a network value is not provided, the results of the command are non-deterministic. To access devices that have not been assigned to a secure network, the name unsecured should be used.
    * `device_id` : The device id of the target device or group of devices. For groups an offset value of `0xC000` (49152) must be added to the integer value of the group id before casting to a string.
    * `sceneNumber` : The target scene number (an integer).

  Optional:
    * `fading` : The fade time, in milliseconds. This can be any positive integer but obviously very large values will not have particularly useful results.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** JSON:
    ```
    { "device_id" : String
    , "sceneNumber" : Integer
    , "fading" : Optional Integer
    }
    ```
    (Optional means that the field may or may not be present. In this case, fading will only be present if a fading value is used in the call.)

* **Error Response:**

  * **Code:** 403 Forbidden <br />
  **Meaning** Controls haven't been enabled for the logged in used on the gateway receiving the request. Enable controls and try again.

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, a `/reload` request should be sent to the gateway to clear the issue. For further analysis, pull the logs from the gateway through the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call:**

  `curl <gateway address: port>/device/recallscene/unsecured/49263/50/6000` Recall scene 50 from group 111 (49263 = 49152 + 111) not on a secure network with a fade time of 6 seconds

* **Notes:**

  Current as of 2016-12-20

----
## Device Dismissal
**Change Status:** No API call changes made in V1.7.0.  

  Remove a device from the device list.

* **URLs**

  `/device/remove/:network/:device_id`

* **Methods:**

  `GET` | `POST`

* **Permission:**

  `control`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/remove` returns JSON:
    ```
    { "result": Boolean
      "device_id": String
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call:**

  `curl <gateway address: port>/device/remove/Xicato/111` will remove device 111 on secure network Xicato from the device list.

* **Notes:**

  Current as of 2017-7-31.

  If a valid device is errantly dismissed then it will automatically re-appear the next time an advertising packet is received from it.

  It is _strongly recommended_ that device failures and failure to respond be investigated before dismissing a device.


----
## Enable and Disable Sensor Response
**Change Status:** Disable calls are new in 1.7.0. Documentation was added for _all_ calls with the release of 1.7.0. 

  Enable or disable the sensor mode for a given ID.

* **URLs:**

  To enable:
  `/device/reset_response/:network/:device_id`
  `/group/reset_response/:network/:group_id`
  `/broadcast/reset_response/:network`

  To disable:
  `/device/disable_response/:network/:device_id`
  `/group/disable_response/:network/:group_id`
  `/broadcast/disable_response/:network`

* **Methods:**

  `GET` | `POST`

* **Permission:**

  `control` | `broadcast`

* **URL Parameters:**

  Required:
    * `network` (for all calls): The secure network that the command will be issued to.
    * `device_id` (for `/device` calls): The target device ID.
    * `group_id` (for `/group` calls): The target group ID.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** JSON:
    ```
    { "result" : Boolean
    , "device_id" : String
    , "network" : Nullable String
    }
    ```

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call:**

  `curl <gateway address: port>/group/reset_response/unsecured/111` Reset sensor response for group 111 not on a secure network.

* **Notes:**

  Current as of 2018-4-17

## Send and Clear Button Presses
**Change Status:** <>

  Sends or clears a button press to a device or group (add `0xC000` to the group ID to use this API).
  Currently this API only supports pressing one button at a time.

* **URLs:**

  To send:
  `/device/button/:network/:device_id/:button_id`
  `/device/sendbutton/:network/:device_id/:button_id`
  `/device/button/:network/:device_id/:button_id/:number_of_buttons`
  `/device/sendbutton/:network/:device_id/:button_id/:number_of_buttons`

  To clear:
  `/device/clearbutton/:network/:device_id`
  `/device/clearbutton/:network/:device_id/:number_of_buttons`

* **Methods:**

  `GET` | `POST` | `PUT`

* **Permission:**

  `control`

* **URL Parameters:**

  Required (for both):
    * `network` : The secure network that the command will be issued to.
    * `device_id` : The target device ID.

  Required (to send):
    * `button_id` : The button to "press". Buttons are zero-indexed.

  Optional (for both):
    * `number_of_buttons` : The number of buttons on the "switch".

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** Both APIs return JSON:
    ```
    { "device_id" : String
    , "network" : String
    , "local_id" : Int
    , "number_of_buttons" : Int
    , "button_id" : Int (send only)
    }
    ```

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call:**

  `curl <gateway address: port>/device/sendbutton/Xicato/219/4/16` will send device 219 on Xicato a virtual button press on the 5th "button" of a 16 "button" "switch".

* **Notes:**

  Current as of 2018-10-16
----
----
# Configure Permission API Calls
## Set Device Groups
**Change Status:** No API call changes made in V1.7.0. Documentation updated to correctly define Permission required and clarify command details specifying secure network. 

  Sets the group membership for a given device (XID or XIM).

* **URLs**

  `/device/setgroups/:network/:device_id`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `configure`

* **URL Parameters:**

  Required:
    * `network` : The secure network for the target device.  
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.

* **Data Parameters:**

  Required:
    * In the body: A list of up to 16 integers (Group ID values) without the group ID offset of `0xC000` added to them (i.e., Group ID `1` should be entered as `1` not `49153`), each separated by a comma and an optional space, and surrounded by brackets e.g. `[51, 53, 55]`. The list of Group IDs will replace the existing Group ID list in the target device; therefore, a read-modify-write operation must be performed to append Group IDs to any list already stored in the device.   *NOTE*: You must provide a "Content-Type:application/json" HTTP Header with this.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/setgroups` returns JSON:
    ```
    { "device_id" : String
    , "groups" : List Integer
    , "result" : Boolean
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
     curl -X PUT
          -H "Content-Type:application/json"
          -d '[51,52,53]'
          -u <username>:<password>
          <gateway address: port>/device/setgroups/Xicato2/1250
  ```
  sets the group membership for device 125 on secure network Xicato2 to 51, 52, and 53.
* **Notes:**

  Current as of 2017-9-18.

----
## Set Device Scenes
**Change Status:** No API call changes made in V1.7.0. Documentation updated to correctly define Permission required and clarify command details specifying secure network. 

  Sets the scene configuration for a given device (XID or XIM).

* **URLs**

  `/device/setscenes/:network/:device_id`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `configure`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.

* **Data Parameters:**

  Required:
    * In the body: A list of up to 32 JSON objects, each having the following structure:
    ```
    { "sceneNumber" : Integer, 0 < n < 65535
    , "intensity" : Float, 0.0 <= m <= 100.0
    , "delayTime" : Integer, n >= 0
    , "fadeTime" : Integer, 0 <= n <= 1.44e7
    }
    ```
    , separated by a comma and an optional space, and surrounded by brackets e.g. `[{"sceneNumber":11, "intensity":34.5, "delayTime":0, "fadeTime":4000}, {"sceneNumber":12, "intensity":0, "delayTime":0, "fadeTime":0}]`. The list of scene objects will replace the existing scene list in the target device; therefore, a read-modify-write operation must be performed to append scene settings to the scene list already stored in the device. *NOTE*: You must provide a "Content-Type:application/json" HTTP Header with this.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/setscenes` returns JSON:
    ```
    { "device_id": String
    , "scenes": List Scene (structured as in the Data Parameters section)
    , "result": Boolean
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.
    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
     curl -X PUT
          -H "Content-Type:application/json"
          -d '[{"sceneNumber":12, "intensity":34.5, "delayTime":0, "fadeTime":4000}, {"sceneNumber":13, "intensity":0, "delayTime":0, "fadeTime":0}]'
          -u <username>:<password>
          <gateway address: port>/device/setscenes/GalaXi/111
  ``` 
  sets the scene response configuration for scenes 12 and 13 in device 111 on the secure network GalaXi.
* **Notes:**

  Current as of 2017-9-18.

----
## Get Device Light Setup
**Change Status:** Initial release in V1.7.0. 

  Gets the light configuration for a given device (XID or XIM).

* **URLs**

  `/device/getlightsetup/:network/:device_id`

* **Methods:**

  `GET`

* **Permission:**

  `configure`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `network` : The network the target device is on. If this is not included there may be unexpected behavior.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/getlightsetup` returns JSON:
    ```
    { "setup":
        { "max_level" : Float 0.1-100.0
        , "min_level" : Float 0.1-100.0
        , "power_on_type" : Int -- 0 for 'Direct', 1 for 'Last', 2 for 'Wired'
        , "power_on_level" : Float 0.1-100.0
        , "power_on_fade_time" : Int 0-6500000
        , "power_on_start_time" : Int 0-2500
        , "dimming_curve" : Int -- 0 for logarithmic, 1 for linear
        , "fade_smoothing" : Int -- 0 for disabled, 1 for enabled
        }
    , "network": String
    , "device_id": String
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call:**

  ```
    curl 'http://<gateway>:8000/device/getlightsetup/Unsecured/111'
  ``` 
  gets the communication configuration for device 111.
* **Notes:**

  Current as of 2018-4-11.

----
## Set Device Light Setup
**Change Status:** Initial release in V1.7.0. 

  Sets the light configuration for a given device (XID or XIM).

* **URLs**

  `/device/setlightsetup/:network/:device_id`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `configure`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `network` : The network the target device is on. If this is not included there may be unexpected behavior.

* **Data Parameters:**

  Required:
    * In the body: A JSON object having the following structure:
    ```
    { max_level : Float 0.1-100.0
    , min_level : Float 0.1-100.0
    , power_on_type : Int -- 0 for 'Direct', 1 for 'Last', 2 for 'Wired'
    , power_on_level : Float 0.1-100.0
    , power_on_fade_time : Int 0-6500000
    , power_on_start_time : Int 0-2500
    , dimming_curve : Int -- 0 for logarithmic, 1 for linear (usually you will want logarithmic)
    , fade_smoothing : Int -- 0 for disabled, 1 for enabled (usually this is enabled)
    }
    ```. *NOTE*: You must provide a "Content-Type:application/json" HTTP Header with this.
    You should _probably_ include a Content-Length header as well.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/setlightsetup` returns JSON:
    ```
    { "result": Bool
    , "setup": Setup (should be what was passed in--this is a place to check for serialization errors)
    , "network": String
    , "device_id": String
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
    curl 'http://<gateway>:8000/device/setlightsetup/Unsecured/111' \
      -X POST \
      -H 'Content-Type: application/json' \
      --data '{"max_level":100,"min_level":0.1,"power_on_type":1,"power_on_level":0,"power_on_fade_time":0,"power_on_start_time":0,"dimming_curve":1,"fade_smoothing":1}'
  ``` 
  sets the communication configuration for device 111.
* **Notes:**

  Current as of 2018-4-6.

----
## Get Device Wired Light Setup
**Change Status:** Initial release in V1.7.1. 

  Gets the wired (1-10V or DALI) light configuration for a given device (XID or XIM). 

* **URLs**

  `/device/getwiredsetup/:network/:device_id`

* **Methods:**

  `GET`

* **Permission:**

  `configure`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `network` : The network the target device is on. If this is not included there may be unexpected behavior.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/getwiredsetup` returns JSON:
    ```
    { "result": Bool
    , "wired_setup": Setup (should be what was passed in--this is a place to check for serialization errors)
    , "network": String
    , "device_id": String
    }
    ```
    The setup object will be different for 1-10V and DALI devices.
    For **1-10V** devices it is as follows:
    ```
    { modes : 4-bit bitfield cast to Int (b0 is MSB)
              b0 : Dimming Curve -- 0 for linear, 1 for logarithmic
              b1 : Limit Mode -- 0 for disabled, 1 for enabled
              b2 : Control Mode -- 0 for enabled, 1 for disabled
              b3 : (Reserved, use the value that's currently set)
              b4 : Fading Curve -- 0 for logarithmic, 1 for linear
    , min_level : Float 0.1-100.0
    , warmup_time : Int 0-25000
    }
    ```
    For **DALI** devices:
    ```
    { max_level : Int 1-254
    , min_level : Int 1-254
    , power_on_level : Int 0-255, 255 for last level
    , system_failure_level : Int 0-255, 255 to ignore
    , dimming_curve : Int -- 0 for logarithmic, 1 for linear
    , fade_rate : Int 0-15
    , fade_time : Int 0-15
    , fast_fade_time : Int 0-27
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error.

* **Example Call:**

  ```
    curl http://<gateway>:8000/device/getwiredsetup/Unsecured/111
  ``` 
  gets the wired light setup for Unsecured device 111.
* **Notes:**

  Current as of 2018-5-17.

----
## Set Device Wired Light Setup
**Change Status:** Initial release in V1.7.1. 

  Sets the wired (1-10V or DALI) light configuration for a given device (XID or XIM). 
  *Currently there is not a server-side check to verify that the data provided is appropriate for the device. _Caveat usor._*

* **URLs**

  `/device/setwiredsetup/:network/:device_id`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `configure`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `network` : The network the target device is on. If this is not included there may be unexpected behavior.

* **Data Parameters:**

  Required:
    * In the body: A JSON object. \
    For **1-10V** devices this is as follows:
    ```
    { modes : 4-bit bitfield cast to Int (b0 is MSB)
              b0 : Dimming Curve -- 0 for linear, 1 for logarithmic
              b1 : Limit Mode -- 0 for disabled, 1 for enabled
              b2 : Control Mode -- 0 for enabled, 1 for disabled
              b3 : (Reserved, use the value that's currently set)
              b4 : Fading Curve -- 0 for logarithmic, 1 for linear
    , min_level : Float 0.1-100.0
    , warmup_time : Int 0-25000
    }
    ```
    For **DALI** devices:
    ```
    { max_level : Int 1-254
    , min_level : Int 1-254
    , power_on_level : Int 0-255, 255 for last level
    , system_failure_level : Int 0-255, 255 to ignore
    , dimming_curve : Int -- 0 for logarithmic, 1 for linear (usually you will want logarithmic)
    , fade_rate : Int 0-15
    , fade_time : Int 0-15
    , fast_fade_time : Int 0-27
    }
    ```
    *NOTE*: You must provide a "Content-Type:application/json" HTTP Header with this.
    You should _probably_ include a Content-Length header as well.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/setwiredsetup` returns JSON:
    ```
    { "result": Bool
    , "wired_setup": Setup (should be what was passed in--this is a place to check for serialization errors)
    , "network": String
    , "device_id": String
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
    curl 'http://<gateway>:8000/device/setwiredsetup/Unsecured/111' \
      -X POST \
      -H 'Content-Type: application/json' \
      --data '{"max_level":254,"min_level":1,"power_on_level":0,"system_failure_level":255,"dimming_curve":1,"fade_rate":7,"fade_time":4,"fast_fade_time":27}'
  ``` 
  sets the wired light setup for device 111, assuming it is a DALI device.
* **Notes:**

  Current as of 2018-5-17.

----
## Get Device Sensor Response
**Change Status:** Initial release in V1.7.2. 

  Gets the sensor response configuration for a given device. 

* **URLs**

  `/device/getsensorresponse/:network/:device_id`

* **Methods:**

  `GET`

* **Permission:**

  `configure`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `network` : The network the target device is on. If this is not included there may be unexpected behavior.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/getsensorresponse` returns a JSON-serialized representation of the sensor response state machine programmed into the device:
    ```
    { "sensor_response": List State
    , "network": String
    , "device_id": String
    }
    ```
    Each State has the following JSON structure:
    ```
    { "Action": String
    , "Conditions": String
    , "Delay/Lx-": String
    , "Fade/Lx+": String
    , "From #'s": String
    , "To #": String
    , "Value":String
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error.

* **Example Call:**

  ```
    curl http://<gateway>:8000/device/getsensorresponse/Unsecured/111
  ``` 
  gets the wired light setup for Unsecured device 111.
* **Notes:**

  Current as of 2018-9-20.

----
## Set Device Sensor Response
**Change Status:** Initial release in V1.7.2. 

  Sets the wired (1-10V or DALI) light configuration for a given device (XID or XIM). 
  *Currently there is not a server-side check to verify that the data provided is appropriate for the device. _Caveat usor._*

* **URLs**

  `/device/setsensorresponse/:network/:device_id`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `configure`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `network` : The network the target device is on. If this is not included there may be unexpected behavior.

* **Data Parameters:**

  Required:
    * In the body: A List (`[]`) of JSON objects, one for each state. \
    Each State has the following JSON structure:
    ```
    { "Action": String
    , "Conditions": String
    , "Delay/Lx-": String
    , "Fade/Lx+": String
    , "From #'s": String
    , "To #": String
    , "Value":String
    }
    ```
    *NOTE*: You must provide a "Content-Type:application/json" HTTP Header with this.
    You should _probably_ include a Content-Length header as well.
    
    * Alternatively, you may attach the sensor response file produced by the Xicato Control Panel. The `name` of the file should be `"file"`.

    _For appropriate values to use here, please consult the Sensor Programming Guide._

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/setsensorresponse` returns JSON:
    ```
    { "result": Bool
    , "sensor_response": List State (should be what was passed in--this is a place to check for serialization errors)
    , "network": String
    , "device_id": String
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
    curl 'http://<gateway>:8000/device/setsensorresponse/Unsecured/111' \
      -X POST \
      -H 'Content-Type: application/json' \
      --data '[{"Delay/Lx-": "0.0", "Fade/Lx+": "2.0", "To #": "0", "Value": "100.0", "From #'s": "0", "Action": "Direct Intensity", "Conditions": "B0.0 Press "}, {"Delay/Lx-": "0.0", "Fade/Lx+": "2.0", "To #": "0", "Value": "0.0", "From #'s": "0", "Action": "Direct Intensity", "Conditions": "B0.1 Press "}, {"Delay/Lx-": "0.0", "Fade/Lx+": "2.0", "To #": "0", "Value": "0.1", "From #'s": "0", "Action": "Direct Intensity", "Conditions": "B0.2 Press "}, {"Delay/Lx-": "0.0", "Fade/Lx+": "", "To #": "0", "Value": "", "From #'s": "0", "Action": "Stop Fading", "Conditions": "B0.3 Press "}]'
  ``` 
  sets the sensor response for device 111.
* **Notes:**

  Current as of 2018-9-20.

----
## Get Device Tracking
**Change Status:** Initial release in V1.7.2. 

  Gets the tracked ID configuration for a given device. 

* **URLs**

  `/device/gettracked/:network/:device_id`

* **Methods:**

  `GET`

* **Permission:**

  `configure`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `network` : The network the target device is on. If this is not included there may be unexpected behavior.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/gettracked` returns JSON:
    ```
    { "tracked": Tracked
    , "network": String
    , "device_id": String
    }
    ```
    Tracked has the following JSON structure:
    ```
    { "motion": List Int
    , "lux": List Int
    , "intensity": List Int
    , "buttons": List (List Int)
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error.

* **Example Call:**

  ```
    curl http://<gateway>:8000/device/gettracked/Unsecured/111
  ``` 
  gets the tracked ID setup for Unsecured device 111.
* **Notes:**

  Current as of 2018-9-27.

----
## Set Device Tracking
**Change Status:** Initial release in V1.7.2. 

  Sets the lux, intensity, buttons, and motion sensors to track on a given device. 
  *Currently there is not a server-side check to verify that the data provided is appropriate for the device. _Caveat usor._*

* **URLs**

  `/device/settracked/:network/:device_id`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `configure`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `network` : The network the target device is on. If this is not included there may be unexpected behavior.

* **Data Parameters:**

  Required:
    * In the body: A JSON objects, with the following structure:
    ```
    { "motion": List Int
    , "lux": List Int
    , "intensity": List Int
    , "buttons": List (List Int)
    }
    ```
    *NOTE*: You must provide a "Content-Type:application/json" HTTP Header with this.
    You should _probably_ include a Content-Length header as well.

    _For appropriate values to use here, please consult the Sensor Programming Guide._

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/settracked` returns JSON:
    ```
    { "results": 
      { "motion": Bool
      , "lux": Bool
      , "intensity": Bool
      , "buttons": Bool
      }
    , "tracked": (should be what was passed in--this is a place to check for serialization errors)
    , "network": String
    , "device_id": String
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
    curl 'http://<gateway>:8000/device/setsensorresponse/Unsecured/111' \
      -X POST \
      -H 'Content-Type: application/json' \
      --data '{"motion":[11,22,33,44],"lux":[55,66,77,88],"intensity":[99,1234,56],"buttons":[[24,45,67,32,41,32]]}'
  ``` 
  sets the tracked device IDs for device 111.
* **Notes:**

  Current as of 2018-9-27.

----
## Get Device Schedules
**Change Status:** Initial release in V1.7.2. 

  Gets the scheduling configuration for a given device. 

* **URLs**

  `/device/getschedules/:network/:device_id`

* **Methods:**

  `GET`

* **Permission:**

  `configure`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `network` : The network the target device is on. If this is not included there may be unexpected behavior.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/getschedules` returns JSON:
    ```
    { "schedules": List Schedules
    , "network": String
    , "device_id": String
    }
    ```
    Each Schedule has the following JSON structure:
    ```
    { "#": String
    , "Days": String
    , "End Time": String
    , "Start Time": String
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error.

* **Example Call:**

  ```
    curl http://<gateway>:8000/device/getschedules/Unsecured/111
  ``` 
  gets the scheduling setup for Unsecured device 111.
* **Notes:**

  Current as of 2018-9-27.

----
## Set Device Schedules
**Change Status:** Initial release in V1.7.2. 

  Sets the scheduling configuration on a given device. 
  *Currently there is not a server-side check to verify that the data provided is appropriate for the device. _Caveat usor._*

* **URLs**

  `/device/setschedules/:network/:device_id`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `configure`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `network` : The network the target device is on. If this is not included there may be unexpected behavior.

* **Data Parameters:**

  Required:
    * In the body:A List (`[]`) of JSON objects, one for each schedule. \
    Each Schedule has the following JSON structure:
    ```
    { "#": String
    , "Days": String
    , "End Time": String
    , "Start Time": String
    }
    ```
    *NOTE*: You must provide a "Content-Type:application/json" HTTP Header with this.
    You should _probably_ include a Content-Length header as well.
    
    * Alternatively, you may attach the sensor response file produced by the Xicato Control Panel. The `name` of the file should be `"file"`.

    _For appropriate values to use here, please consult the Sensor Programming Guide._

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/setschedules` returns JSON:
    ```
    { "result": Bool
    , "tracked": List Schedule
    , "network": String
    , "device_id": String
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
    curl 'http://<gateway>:8000/device/setsensorresponse/Unsecured/111' \
      -X POST \
      -H 'Content-Type: application/json' \
      --data '[{"#":"T0","Days":"","End Time":"00:00:00","Start Time":"00:00:00"},{"#":"T1","Days":"M,W,F","End Time":"00:00:00","Start Time":"03:30:45"},{"#":"T2","Days":"Tu,Th","End Time":"11:00:00","Start Time":"23:00:00"},{"#":"T3","Days":"Sa,Su","End Time":"00:00:00","Start Time":"00:00:00"}]'
  ``` 
  sets the schedules for device 111.
* **Notes:**

  Current as of 2018-9-27.
----
----
# Manage Permission API Calls
## Set Device Name
**Change Status:** No API call changes made in V1.7.0.  

  Sets the name value for a given device.

* **URLs**

  `/device/setname/:network/:device_id/:name`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `manage`

* **URL Parameters:**

  Required:
    * `network` : The network the target device is on. For unsecured devices, use 'unsecured'.
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `name` : The desired name (URL-encoded).

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/setname` returns JSON:
    ```
    { "device_id": String
    , "network": String
    , "name": String
    , "result": Boolean
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
     curl -X PUT
          -u <username>:<password>
          <gateway address: port>/device/setname/unsecured/111/Experience%20Room%20Front
  ``` 
  sets the name for unsecured device 111 to 'Experience Room Front'.
* **Notes:**

  Current as of 2017-11-21.

----
## Set Device ID
**Change Status:** No API call changes made in V1.7.0.  

  Sets the device (node) ID value for a given device.

* **URLs**

  `/device/setid/:network/:device_id/:new_id`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `manage`

* **URL Parameters:**

  Required:
    * `network` : The network the target device is on. For unsecured devices, use 'unsecured'.
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `new_id` : The desired ID that the device will move to.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/setid` returns JSON:
    ```
    { "device_id": String
    , "old_device_id" : String
    , "new_device_id" : String
    , "network": String
    , "result": Boolean
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
     curl -X PUT
          -u <username>:<password>
          <gateway address: port>/device/setid/unsecured/111/2112
  ``` 
  sets the device ID for unsecured device 111 to 2112.

* **Notes:**

  Current as of 2017-11-21.

----
## Set Device Network
**Change Status:** No API call changes made in V1.7.0.  

  Sets the secure network for a given device.

* **URLs**

  `/device/setnetwork/:network/:device_id/:new_network`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `manage`. *NOTE*: This permission is required for both the current and new networks for the operation to proceed.

* **URL Parameters:**

  Required:
    * `network` : The network the target device is on. For unsecured devices, use 'Unsecured'.
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `new_network` : The desired network that the device will move to. To remove a secure network from a device, use 'unsecured'. *NOTE*: A device cannot be moved directly from one secure network to a second secure network, the operation must be first secure network to unsecured to second secure network.  

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/setnetwork` returns JSON:
    ```
    { "device_id": String
    , "old_network" : String
    , "new_network" : String
    , "network": String
    , "result": Boolean
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
     curl -X PUT
          -u <username>:<password>
          <gateway address: port>/device/setnetwork/Unsecured/111/Xicato
  ``` 
  sets the secure network for unsecured device 111 to Xicato.
* **Notes:**

  Current as of 2017-11-21.

----
## Name Group
**Change Status:** No API call changes made in V1.7.0.  

  Name or change the name of a group. It will also save the name through software restarts. *NOTE* This call only creates a name list associated with the specified Group IDs on the gateway. The group names are not stored on XIMs or XIDs, and the name list is not propagated to other gateways in the space. 

* **URLs**

  `/group/setname/:group_id/:name`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `manage`

* **URL Parameters:**

  Required:
    * `group_id` : The target group ID. This can be any valid group ID.
    * `name` : The desired name of the target group. (Ensure that any special characters are properly URL-encoded.)

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/group/setname` returns JSON:
    ```
    { "id": Int
      "name": String
    }
    ```

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means there was an issue with the parameters provided.

* **Example Call:**

  `curl -u <username>:<password> <gateway address: port>/group/setname/7/Group%20Test` will set the name of group 7 to 'Group Test'.

* **Notes:**

  Current as of 2017-09-21.

  Names currently are *not* shared between gateways, and Names are *not* stored on the XIMs or XIDs in the group.

----
## Name Scene
**Change Status:** No API call changes made in V1.7.0.  

  Name or change the name of a scene. It will also save the name through software restarts. *NOTE* This call only creates a name list associated with the specified scene IDs on the gateway. The scene names are not stored on XIMs or XIDs, and the name list is not propagated to other gateways in the space. 

* **URLs**

  `/scene/setname/:scene_number/:name`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `manage`

* **URL Parameters:**

  Required:
    * `scene_number` : The target scene number. This can be any valid scene number.
    * `name` : The desired name of the target scene. (Ensure that any special characters are properly URL-encoded.)

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/scene/setname` returns JSON:
    ```
    { "number": Int
      "name": String
    }
    ```

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means there was an issue with the parameters provided.

* **Example Call:**

  `curl -u <username>:<password> <gateway address: port>/scene/setname/7/Scene%20Test` will set the name of scene 7 to 'Scene Test'.

* **Notes:**

  Current as of 2017-09-21.

  Names are currently *not* shared between gateways, and names are *not* stored on the devices configured to respond to that scene.

----
## Get Firmware Available
**Change Status:** No API call changes made in V1.7.0.  

  Returns the available firmware for a device.

* **URLs**

  `/device/firmware/available/:network/:device_id`

* **Method:**

  `GET`

* **Permission:**

  `manage`

* **URL Parameters:**

  Required:
    * `network` : The network the target device is on. For unsecured devices, use 'Unsecured'.
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/firmware/available` returns JSON:
    ```
    { "device_id": String
    , "current" : String
    , "available" : Nullable (List Float)
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
     curl -X PUT
          -u <username>:<password>
          <gateway address: port>/device/firmware/available/Unsecured/111
  ``` 
  gets the available firmware for unsecured device 111.
* **Notes:**

  Current as of 2017-11-21.

----
## Enqueue A Firmware Update
**Change Status:** Updated API call in V1.7.1 to match documented method (`PUT` | `POST`) used to perform update. Previous API versions were supporting the incorrect method (`GET`).   

  Enqueue a firmware update for a device.

* **URLs**

  `/device/firmware/update/:network/:device_id/:version`

* **Method:**

  `POST` | `PUT`

* **Permission:**


  `manage`

* **URL Parameters:**

  Required:
    * `network` : The network the target device is on. For unsecured devices, use 'Unsecured'.
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `version` : The target firmware version.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/firmware/update` returns JSON:
    ```
    { "device_id": String
    , "enqueued" : Bool
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.
    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
     curl -X PUT
          -u <username>:<password>
          <gateway address: port>/device/firmware/update/Unsecured/111/0.157
  ``` 
  enqueues a firmware update to version 0.157 for unsecured device 111.
* **Notes:**

  Current as of 2017-11-21.

----
## Check Firmware Update Status
**Change Status:** No API call changes made in V1.7.0.  

  Check on the status of a firmware update.

* **URLs**

  `/device/firmware/update/check/:network/:device_id`

* **Method:**

  `GET`

* **Permission:**

  `manage`

* **URL Parameters:**

  Required:
    * `network` : The network the target device is on. For unsecured devices, use 'unsecured'.
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/firmware/update/check` returns JSON:
    ```
    { "device_id": String
    , "update": 
      { "status" : Nullable Int
      , "pct_complete" : Float
      , "boot_mode" : Bool
      , "current" : Nullable String
      , "next" : Float
      }
    }
    ```
    where the status integer can be interpreted as:
    ```
    null -> "Null status. Please try this operation again."

    0 -> "OK."

    1 -> "Stack up to date."

    2 -> "Old Launcher."

    3 -> "Unassigned ID. Please try again after assigning an ID."

    4 -> "Firmware is up-to-date."

    5 -> "No update for sensor."

    6 -> "Wrong device type."

    7 -> "Hardware mismatch."

    8 -> "Firmware read failed."

    9 -> "Entry failed."

    10 -> "Old stack."

    11 -> "Stack updated."

    12 -> "Stack restart expired."

    13 -> "Stack restarted."

    14 -> "Waiting for stack restart."

    15 -> "Stack file not found."

    16 -> "Update in progress."

    17 -> "Update complete."

    18 -> "Update pending."

    19 -> "Launcher updated."

    anything else -> "Unknown Status."
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
     curl -u <username>:<password>
          <gateway address: port>/device/firmware/update/check/Unsecured/111
  ``` 
  checks on the status of a firmware update for unsecured device 111.
* **Notes:**

  Current as of 2017-11-21.

----
## Get Device Communication Configuration
**Change Status:** V1.7.0 API call changes: Corrected reported values of txPower and highRxGain, added JSON dict entry for device specific Tx power settings (availableTxPowers). The list of device specific Tx power settings should be used to ensure only valid values are used in the Set Device Communication Configuration call.   

  Gets the communication configuration for a given device (XID or XIM).

* **URLs**

  `/device/getconfig/:network/:device_id`

* **Methods:**

  `GET`

* **Permission:**

  `manage`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `network` : The network the target device is on. If this is not included there may be unexpected behavior.

* **Data Parameters:**

  None.


* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/getconfig` returns JSON:
    ```
    { txPower : Float
    , highRxGain : Bool
    , availableTxPowers : List Float
    , dynamicLightStatusConfiguration :
        { warnInterval : Int
        , warnIntensityMax : Float
        , warnIntensityMin : Float
        , warnTemperatureMax : Int
        , warnTemperatureMin : Int
        , warnVinMax : Float
        , warnVinMin : Float
        , changeBursts : Int
        , changeBurstInterval : Int
        , changeIntervalMin : Int
        , changeIntensity : Float
        , changeTemperature : Int
        , changeVin : Float
        }
    , advertisementSettings :
        { lightStatusAdvInterval : Int
        , lightHistoryAdvInterval : Int
        , lightChangeBursts : Int
        , lightChangeBurstInterval : Int
        , alwaysConnectable : Bool
        }
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error.

* **Example Call:**

  ```
    curl 'http://<gateway>:8000/device/getconfig/Unsecured/111'
  ``` 
  gets the communication configuration for device 111 on an unsecured network.
* **Notes:**

  Current as of 2018-4-11.

----
## Get Device Communication Configuration (New Call)
**Change Status:** V1.7.1 API call changes: This call is new; it duplicates all the existing functionality of the `/device/getconfig` call, but in a way that's more consistent with other device configuration calls.

  Gets the communication configuration for a given device (XID or XIM).

* **URLs**

  `/device/getcommconfig/:network/:device_id`

* **Methods:**

  `GET`

* **Permission:**

  `manage`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `network` : The network the target device is on. If this is not included there may be unexpected behavior.

* **Data Parameters:**

  None.


* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/getcommconfig` returns JSON:
    ```
    { "config":
        { txPower : Float
        , highRxGain : Bool
        , availableTxPowers : List Float
        , dynamicLightStatusConfiguration :
            { warnInterval : Int
            , warnIntensityMax : Float
            , warnIntensityMin : Float
            , warnTemperatureMax : Int
            , warnTemperatureMin : Int
            , warnVinMax : Float
            , warnVinMin : Float
            , changeBursts : Int
            , changeBurstInterval : Int
            , changeIntervalMin : Int
            , changeIntensity : Float
            , changeTemperature : Int
            , changeVin : Float
            }
        , advertisementSettings :
            { lightStatusAdvInterval : Int
            , lightHistoryAdvInterval : Int
            , lightChangeBursts : Int
            , lightChangeBurstInterval : Int
            , alwaysConnectable : Bool
            }
        }
    , "network": String
    , "device_id": String
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call:**

  ```
    curl 'http://<gateway>:8000/device/getcommconfig/Unsecured/111'
  ``` 
  gets the communication configuration for device 111 on an unsecured network.
* **Notes:**

  Current as of 2018-5-22.

----
## Set Device Communication Configuration
**Change Status:** Initial release in V1.7.0. 

  Sets the communication configuration for a given device (XID or XIM).

* **URLs**

  `/device/setconfig/:network/:device_id`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `manage`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `network` : The network the target device is on. If this is not included there may be unexpected behavior.

* **Data Parameters:**

  Required:
    * In the body: A JSON object having the following structure:
    ```
    { txPower : Float
    , highRxGain : Bool
    , dynamicLightStatusConfiguration : DynamicLightStatusConfiguration
    , advertisementSettings : AdvertisementSettings
    }
    ```
    * The available `txPower` settings for a given device are provided bn the Get Device Communication Configuration call. 
    * `dynamicLightStatusConfiguration` is structured as:
    ```
    { warnInterval : Int
    , warnIntensityMax : Float
    , warnIntensityMin : Float
    , warnTemperatureMax : Int
    , warnTemperatureMin : Int
    , warnVinMax : Float
    , warnVinMin : Float
    , changeBursts : Int
    , changeBurstInterval : Int
    , changeIntervalMin : Int
    , changeIntensity : Float
    , changeTemperature : Int
    , changeVin : Float
    }```
    and `advertisementSettings` is:
    ```
    { lightStatusAdvInterval : Int
    , lightHistoryAdvInterval : Int
    , lightChangeBursts : Int
    , lightChangeBurstInterval : Int
    , alwaysConnectable : Bool
    }
    ```. *NOTE*: You must provide a "Content-Type:application/json" HTTP Header with this.
    You should _probably_ include a Content-Length header as well.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/setconfig` returns JSON:
    ```
    { "results": Results for each setting
        { "txPower": Bool
        , "highRxGain": Bool
        , "dynamicLightStatusConfiguration": Bool
        , "advertisementSettings": Bool
        }
    , "config": Config (should be what was passed in)
    , "network": String
    , "device_id": String
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues. If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call:**

  ```
    curl 'http://<gateway>:8000/device/setconfig/Unsecured/111' \
      -X POST \
      -u <user>:<password>
      -H 'Content-Type: application/json' \
      --data '{"txPower":9.5,"highRxGain":true,"dynamicLightStatusConfiguration":{"warnInterval":1000,        "warnIntensityMax":100,"warnIntensityMin":0,"warnTemperatureMax":90,"warnTemperatureMin":0,"warnVinMax":53,        "warnVinMin":43,"changeBursts":3,"changeBurstInterval":100,"changeIntervalMin":1000,"changeIntensity":0,        "changeTemperature":5,"changeVin":1},"advertisementSettings":{"lightStatusAdvInterval":10000,        "lightHistoryAdvInterval":0,"lightChangeBursts":3,"lightChangeBurstInterval":100,"alwaysConnectable":false}}'
  ``` 
  sets the communication configuration for device 111 on an unsecured network. Note that the `--data` value when submitted must be on a single line.
* **Notes:**

  Current as of 2018-4-5.

----
## Upload Gateway Groups List
**Change Status:** No API call changes made in V1.7.0.  

  Upload `groups.txt` to the XIG.

* **URL:**

  `/upload_groups`

* **Method:**

  `POST`

* **Permission:**

  `manage`

* **URL Parameters**

  None.

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/upload_groups` will return the same JSON as `/groups/light`.

* **Error Responses**
  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error which it couldn't recover from. Tail the error log and send it over for someone to examine.

* **Example Call**

  ```
  var formData = new FormData();
  formData.append('file', document.getElementById('groupsUploadField').files[0]);
  var url = "http://" + address + "/upload_groups";
  var xhr = new XMLHttpRequest();
  xhr.open("POST", url, true);
  xhr.setRequestHeader("Authorization", "Bearer " + token);
  xhr.send(formData);
  ``` 
  will upload the `groups.txt` file selected in the `groupsUploadField` input (n.b. `groupsUploadField` should be type `file`) to the gateway at `address` using the token `token`.

* **Notes**

  Current as of 2018-4-25.

----
## Upload Gateway Scenes List
**Change Status:** No API call changes made in V1.7.0.  

  Upload `scenes.txt` to the XIG.

* **URL:**

  `/upload_scenes`

* **Method:**

  `POST`

* **Permission:**

  `manage`

* **URL Parameters**

  None.

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/upload_scenes` will return the same JSON as `/devices/with_scenes`.

* **Error Responses**
  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.
 
* **Example Call**

  ```
  var formData = new FormData();
  formData.append('file', document.getElementById('scenesUploadField').files[0]);
  var url = "http://" + address + "/upload_scenes";
  var xhr = new XMLHttpRequest();
  xhr.open("POST", url, true);
  xhr.setRequestHeader("Authorization", "Bearer " + token);
  xhr.send(formData);
  ``` 
  will upload the `scenes.txt` file selected in the `scenesUploadField` input (n.b. `scenesUploadField` should be type `file`) to the gateway at `address` using the token `token`.

* **Notes**

  Current as of 2018-4-25.

----
## Get Device Relay Configuration
**Change Status:** Initial release in V1.7.1. 

  Gets the light configuration for a given device (XID or XIM).

* **URLs**

  `/device/getrelayconfig/:network/:device_id`

* **Methods:**

  `GET`

* **Permission:**

  `manage`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `network` : The network the target device is on. If this is not included there may be unexpected behavior.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/getrelayconfig` returns JSON:
    ```
    { "relay_config":
        { "repeatEnabled" : Boolean
        , "ttlStart" : Int
        }
    , "network": String
    , "device_id": String
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error.

* **Example Call:**

  ```
    curl 'http://<gateway>:8000/device/getrelayconfig/Unsecured/111'
  ``` 
  gets the relay config for device 111.
* **Notes:**

  Current as of 2018-5-17.

----
## Set Device Relay Configuration
**Change Status:** Initial release in V1.7.1. 

  Gets the light configuration for a given device (XID or XIM).

* **URLs**

  `/device/setrelayconfig/:network/:device_id`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `manage`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `network` : The network the target device is on. If this is not included there may be unexpected behavior.

* **Data Parameters:**
  * In the body: A JSON object having the following structure:
      ```
      { "repeatEnabled" : Boolean
      , "ttlStart" : Int
      }
      ```

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/setrelayconfig` returns JSON:
    ```
    { "relay_config":
        { "repeatEnabled" : Boolean
        , "ttlStart" : Int
        }
    , "network": String
    , "device_id": String
    , "result": Boolean
    }
    ```

* **Error Response:**

  * **Code:** 400 Bad Request<br />
  **Meaning:** The device requested could not be found.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error.

* **Example Call:**

  ```
    curl 'http://<gateway>:8000/device/setrelayconfig/Unsecured/111' \
      -X POST \
      -H 'Content-Type: application/json' \
      --data '{"repeatEnabled":true,"ttlStart":4}'
  ```
  sets the relay config for device 111.
* **Notes:**

  Current as of 2018-5-17.

----
## Set Group Name
**Change Status:** Accepts a network parameter as of 1.7.2.  

  Sets the name for a given group.

* **URLs**

  `/device/setname/:network/:group_id/:name`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `manage`

* **URL Parameters:**

  Required:
    * `network` : The network the target device is on. For unsecured devices, use 'unsecured'.
    * `group_id` : The target group ID.
    * `name` : The desired name (URL-encoded).

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/group/setname` returns JSON:
    ```
    { "name": String
    , "id": Int
    }
    ```

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
     curl -X PUT
          -u <username>:<password>
          <gateway address: port>/group/setname/Xicato/25/Boardroom
  ``` 
  sets the name for Xicato group 25 to 'Boardroom'.
* **Notes:**

  Current as of 2018-10-19.
----
## Set Scene Name
**Change Status:** Accepts a network parameter as of 1.7.2.  

  Sets the name for a given scene.

* **URLs**

  `/scene/setname/:network/:scene_id/:name`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `manage`

* **URL Parameters:**

  Required:
    * `network` : The network the target scene is on. For scenes using unsecured devices, use 'unsecured'.
    * `scene_id` : The target scene ID.
    * `name` : The desired name (URL-encoded).

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/scene/setname` returns JSON:
    ```
    { "name": String
    , "number": Int
    }
    ```

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
     curl -X PUT
          -u <username>:<password>
          <gateway address: port>/scene/setname/Xicato/41/Track%20On
  ``` 
  sets the name for Xicato scene 41 to 'Track On'.
* **Notes:**

  Current as of 2018-10-19.
----
# Beacon Permission API Calls
----
----
## Get Device iBeacon Configuration
**Change Status:** Initial release in V1.7.0. 

  Retrieve the iBeacon configuration of a selected device.

* **URL:**

  `/device/beacon/ibeacon/get/:network/:device_id`

* **Methods:**

  `GET`

* **Permission:**

  `beacon`

* **URL Parameters:**

  Required:
    * `network`: The network the target device is on.
    * `device_id`: The target device ID.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200<br/>
  **Content:** `/device/beacon/ibeacon/get` returns JSON:
  ```
  { "device_id": String
  , "network": String
  , "ibeacon_config":
      { "uuid": List Int
      , "major": Int
      , "minor": Int
      , "measured_power": Int
      , "tx_power": Float
      , "period": Int
      , "available_tx_power": List Float
      }
  }
  ```

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error.
* **Example Call:**

  `curl -u <username>:<password> <gateway address: port>/device/beacon/ibeacon/get/Xicato/1`
  will get the iBeacon configuration for device 1 on the network named "Xicato".

* **Notes:**

  Current as of 2018-04-10.

----
## Get Device Eddystone URL Configuration
**Change Status:** Initial release in V1.7.0. 

  Retrieve the Eddystone URL configuration of a selected device.

* **URL:**

  `/device/beacon/eddystone/get/:network/:device_id`

* **Methods:**

  `GET`

* **Permission:**

  `beacon`

* **URL Parameters:**

  Required:
    * `network`: The network the target device is one.
    * `device_id`: The target device ID.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200<br/>
  **Content:** `/device/beacon/eddystone/get` returns JSON:
  ```
  { "device_id": String
  , "network": String
  , "eddystone_config":
      { "url": String
      , "flags": Int (should always be 16 if enabled)
      , "tx_power_mode": Int (0 = Lowest, 1 = Low, 2 = Medium, 3 = High)
      , "tx_power_levels": List Int
      , "period": Int
      }
  }
  ```

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error.
* **Example Call:**

  `curl -u <username>:<password> <gateway address: port>/device/beacon/eddystone/get/Xicato/1` will get the Eddystone URL configuration for device 1 on the network named "Xicato".

* **Notes:**

  Current as of 2018-04-10.

----
## Get Device AltBeacon Config
**Change Status:** Initial release in V1.7.0. 

  Retrieve the AltBeacon configuration of a selected device.

* **URL:**

  `/device/beacon/altbeacon/get/:network/:device_id`

* **Methods:**

  `GET`

* **Permission:**

  `beacon`

* **URL Parameters:**

  Required:
    * `network`: The network the target device is one.
    * `device_id`: The target device ID.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200<br/>
  **Content:** `/device/beacon/altbeacon/get` returns JSON:
  ```
  { "device_id": String
  , "network": String
  , "altbeacon_config":
      { "company_id": List Int[2]
      , "beacon_id": List Int[18]
      , "mfg_data": Int
      , "measured_power": Int
      , "period": Int
      }
  }
  ```

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error.
* **Example Call:**

  `curl -u <username>:<password> <gateway address: port>/device/beacon/altbeacon/get/Xicato/1` will get the AltBeacon configuration for device 1 on the network named "Xicato".

* **Notes:**

  Current as of 2018-04-10.

----
## Set Device iBeacon Configuration
**Change Status:** Initial release in V1.7.0. 

  Set the iBeacon configuration of a selected device.

* **URL:**

  `/device/beacon/ibeacon/set/:network/:device_id`

* **Methods:**

  `PUT`|`POST`

* **Permission:**

  `beacon`

* **URL Parameters:**

  Required:
    * `network`: The network the target device is one.
    * `device_id`: The target device ID.

* **Data Parameters:**

  Required:
  * In the body: A JSON object having the following structure:
    ```
    { "uuid": List Int
    , "major": Int
    , "minor": Int
    , "measured_power": Int
    , "tx_power": Int
    , "period": Int
    }
    ```
    *NOTE*: You must provide a "Content-Type:application/json" HTTP Header with this.
    You should _probably_ include a Content-Length header as well.

* **Success Response:**

  * **Code:** 200<br/>
  **Content:** `/device/beacon/ibeacon/set` returns JSON:
  ```
  { "device_id": String
  , "network": String
  , "ibeacon_config":
      { "uuid": List Int
      , "major": Int
      , "minor": Int
      , "measured_power": Int
      , "tx_power": Int
      , "period": Int
      }
  , "results":
      { "uuid": Bool
      , "major": Bool
      , "minor": Bool
      , "measured_power": Bool
      , "tx_power": Bool
      , "period": Bool
      }
  }
  ```

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the input data was not formatted properly
* **Example Call:**

  ```
  curl -u <username>:<password> <gateway address: port>/device/beacon/ibeacon/set/Xicato/1 \
    -X POST \
    -H 'Content-Type: application/json' \
    -H 'Accept-Encoding: gzip, deflate' \
    --data-binary '{"uuid":[0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],"major":1,"minor":1,"measured_power":-50,"tx_power":10,"period":5000}'
  ```
    will set the iBeacon configuration for device 1 on the network named "Xicato".

* **Notes:**

  Current as of 2018-04-10.

----
## Set Device Eddystone URL Configuration
**Change Status:** Initial release in V1.7.0. 

  Set the Eddystone URL configuration of a selected device.

* **URL:**

  `/device/beacon/eddystone/set/:network/:device_id`

* **Methods:**

  `PUT`|`POST`

* **Permission:**

  `beacon`

* **URL Parameters:**

  Required:
    * `network`: The network the target device is one.
    * `device_id`: The target device ID.

* **Data Parameters:**

  Required:
  * In the body: A JSON object having the following structure:
    ```
    { "url": String
    , "flags": 16
    , "tx_power_mode": Int
    , "tx_power_levels": List Int
    , "period": Int
    }
    ```
    *NOTE*: You must provide a "Content-Type:application/json" HTTP Header with this.
    You should _probably_ include a Content-Length header as well.

* **Success Response:**

  * **Code:** 200<br/>
  **Content:** `/device/beacon/eddystone/set` returns JSON:
  ```
  { "device_id": String
  , "network": String
  , "eddystone_config":
      { "url": String
      , "flags": 16
      , "tx_power_mode": Int
      , "tx_power_levels": List Int
      , "period": Int
      }
  , "results":
      { "url": Bool
      , "flags": Bool
      , "tx_power_mode": Bool
      , "tx_power_levels": Bool
      , "period": Bool
      }
  }
  ```

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the input data was not formatted properly
* **Example Call:**

  ```
  curl -u <username>:<password> <gateway address: port>/device/beacon/eddystone/set/Xicato/1 \
    -X POST \
    -H 'Content-Type: application/json' \
    -H 'Accept-Encoding: gzip, deflate' \
    --data-binary '{"url":"http://xicato.com","flags":16,"tx_power_mode":3,"tx_power_levels":[-80,-70,-60,-50],"period":5000}'
  ```
    will set the Eddystone URL configuration for device 1 on the network named "Xicato".

* **Notes:**

  Current as of 2018-04-10.

----
## Set Device AltBeacon Configuration
**Change Status:** Initial release in V1.7.0. 

  Set the AltBeacon configuration of a selected device.

* **URL:**

  `/device/beacon/altbeacon/set/:network/:device_id`

* **Methods:**

  `PUT`|`POST`

* **Permission:**

  `beacon`

* **URL Parameters:**

  Required:
    * `network`: The network the target device is one.
    * `device_id`: The target device ID.

* **Data Parameters:**

  Required:
  * In the body: A JSON object having the following structure:
    ```
    { "company_id": List Int[2]
    , "beacon_id": List Int[18]
    , "mfg_data": Int
    , "measured_power": Int
    , "period": Int
    }
    ```
    *NOTE*: You must provide a "Content-Type:application/json" HTTP Header with this.
    You should _probably_ include a Content-Length header as well.

* **Success Response:**

  * **Code:** 200<br/>
  **Content:** `/device/beacon/altbeacon/set` returns JSON:
  ```
  { "device_id": String
  , "network": String
  , "altbeacon_config":
      { "company_id": List Int[2]
      , "beacon_id": List Int[18]
      , "mfg_data": Int
      , "measured_power": Int
      , "period": Int
      }
  , "results":
      { "company_id": Bool
      , "beacon_id": Bool
      , "mfg_data": Bool
      , "measured_power": Bool
      , "period": Bool
      }
  }
  ```

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the input data was not formatted properly
* **Example Call:**

  ```
  curl -u <username>:<password> <gateway address:port>/device/beacon/altbeacon/set/Xicato/1 \
    -X POST \
    -H 'Content-Type: application/json' \
    -H 'Accept-Encoding: gzip, deflate' \
    --data-binary '{"company_id":[16,16],"beacon_id":[1,2,3,4,5,6,7,8,9,0,1,2,3,4,5,6,7,8],"mfg_data":37,"measured_power":-45,"period":5000}'
  ```
    will set the AltBeacon configuration for device 1 on the network named "Xicato".

* **Notes:**

  Current as of 2018-04-10.

----
----
# Administrate Permission API Calls
## Get Local ID
**Change Status:** No API call changes made in V1.7.0.

  Reports the local logical BLE address.

* **URL:**

  `/local_id`

* **Method:**

  `GET`

* **Permission:**

  `administrate`

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/local_id` returns JSON:
    ```
    { local_id : Integer
    }
    ```

* **Error Response**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call**

  `curl <gateway address: port>/local_id` gets the gateway's local ID.

* **Notes**

  Current as of 2017-11-30

----
## Set Local ID
**Change Status:** Initial release in V1.7.1.

  Set the local logical BLE address.
  __This command should only be used if the network logical ID structure is known to prevent potential ID conflicts.__

* **URL:**

  `/set_local_id/:local_id`

* **Method:**

  `PUT` | `POST`

* **Permission:**

  `administrate`

* **URL Parameters:**

  Required:
    * `local_id`: The desired local ID.

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/set_local_id` returns JSON:
    ```
    { local_id : Integer
      result : Boolean
    }
    ```

* **Error Response**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the error log and send it over for someone to examine.

* **Example Call**

  ```
     curl -X PUT
          -H "Authorization: Bearer <token>"
          <gateway address: port>/set_local_id/31212
  ``` 
  Sets the gateway's local ID to 31212.

* **Notes**

  Current as of 2018-5-17

----
## Randomize Local ID
**Change Status:** No API call changes made in V1.7.0.  

  Randomize the local logical BLE address.
  __THIS COMMAND SHOULD NOT BE USED. IF IT IS NECESSARY TO CHANGE THE XIG LOCAL ID, USE SET LOCAL ID__

* **URLs**

  `/reload/randomize_id`

* **Method**

  `GET`

* **Permission:**

  `administrate`

* **URL Parameters**

  None.

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/reload/randomize_id` returns JSON:
    ```
    { new_id : Integer
    }
    ```

* **Error Response**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call**

  `curl <gateway address: port>/reload/randomize_id` randomizes the gateway's local ID.

* **Notes**

  Current as of 2016-12-20

----
## Reload Gateway
**Change Status:** No API call changes made in V1.7.0.  

  Manually reload the gateway by restarting the process.

* **URLs**

  `/reload`

* **Method**

  `GET`

* **Permission:**

  `administrate`

* **URL Parameters**
  * You can either enter `http://<username>:<password>@<gateway address>/reload`, or you will be prompted with an HTTP Basic Auth request for username and password.
  * You should have created a username and password for this as part of the commissioning process; if you didn't, you should have received credentials from the person who did.

* **Data Parameters**
  * You might be able to put the Basic Auth info here, or in the headers, if you so desire.

* **Success Response**
  * `Reloading gateway process. <button>Go Back?</button>`, where the button makes a JS call to `window.history.back()`, taking you back to the previous page.

* **Error Responses**

  * **Code:** 403 Forbidden <br />
  **Meaning:** The credentials provided were not correct.

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error which it couldn't recover from (this is very bad in this case). If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

----
**Set Advertising Interval**
----
  Set the minimum advertising interval for outgoing commands. Default is 500ms.

* **URL**

  `/set_advertising_interval/<interval>`

* **Methods**

  `POST`

* **Permission:**

  `administrate`

* **URL Parameters**

  Required:
    * `interval`: The desired minimum advertising interval in seconds, with a minimum of 0.030 (30ms).

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/set_advertising_interval` returns JSON:
    ```
    { interval : Float
    , result : Boolean
    }
    ```


* **Error Response:**

  * **Code:** 400 BAD REQUEST <br />
  **Meaning:** The parameter passed in was invalid (most likely below the minimum allowed parameter.)

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call**

  `curl <gateway address: port>/set_advertising_interval/0.050` sets the minimum advertising interval to 50 milliseconds.

* **Notes**

  Current as of 2017-5-10


----
## Enable/Disable Network Time Sync
**Change Status:** No API call changes made in V1.7.0.  

  Set the minimum advertising interval for outgoing commands. Default is 500ms.

* **URLs**

  `/enable_time_sync/<state>>`
  `/enable_time_sync`

* **Methods**

  `POST`

* **Permission:**

  `administrate`

* **URL Parameters**

  Optional:
    * `state`: * A string, either `True` or `False`, representing whether network time advertising should be enabled or not for the gateway. If omitted, defaults to `True`.


* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/enable_time_sync` returns JSON:
    ```
    { sync : Boolean
    , network_time_enabled : Boolean
    }
    ```


* **Error Response:**

  * **Code:** 400 BAD REQUEST <br />
  **Meaning:** The parameter passed in was invalid.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call**

  `curl <gateway address: port>/enable_time_sync/True` will enable network time advertising.

* **Notes**

  Current as of 2017-5-10

  ----
## Enable/Disable Device Autoremoval
**Change Status:** No API call changes made in V1.7.0.  

  Enable or disable auto-purging (removal from the device list) after a specified period. If an update has not been received from a device after that period, the device will be removed. (Of course, it may reappear if it is still advertising). This function is not present in the Dashboard. *We strongly recommend that you do not enable this setting except in testing and as otherwise required.* The default timeout is 600 seconds (10 minutes).

* **URLs**

  `/enable_auto_purge/<timeout>`
  `/enable_auto_purge`
  `/disable_auto_purge`

* **Methods**

  `GET`

* **Permission:**

  `administrate`

* **URL Parameters**

  Optional:
    * `timeout`: * An integer, either 0 or a value between 60 and 3600, representing the auto-purge timeout. If omitted, defaults to 600. If outside the bounds, it will be clamped to the bounds.


* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/enable_auto_purge` returns JSON:
    ```
    { timeout : Integer
    }
    ```


* **Error Response:**

  * **Code:** 400 BAD REQUEST <br />
  **Meaning:** The parameter passed in was invalid.

    OR

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call**

  `curl <gateway address: port>/enable_auto_purge/900` will set devices to automatically be removed from the device list after 15 minutes of inactivity.

* **Notes**

  Current as of 2018-1-31.

----
## Enable/Disable Packet Logging
**Change Status:** New in V1.7.0.  

  Enable or disable packet logging. By default this feature is disabled and should only be enabled when debugging specific issues in conjuction with the Xicato technical support team. 

* **URLs**

  `/enable_packet_logging`
  `/disable_packet_logging`

* **Methods**

  `GET`

* **Permission:**

  `administrate`

* **URL Parameters**

  None.

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/enable_packet_logging` returns JSON:
    ```
    { packet_logging : Boolean
    }
    ```


* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call**

  `curl <gateway address: port>/enable_packet_logging` will enable packet logging.

* **Notes**

  Current as of 28 Germinal CCXXVI.

----
## Enable/Disable Event Logging
**Change Status:** New in V1.7.0.  

  Enable or disable event logging. By default this feature is disabled and should only be enabled when debugging specific issues in conjuction with the Xicato technical support team.

* **URLs**

  `/enable_event_logging`
  `/disable_event_logging`

* **Methods**

  `GET`

* **Permission:**

  `administrate`

* **URL Parameters**

  None.

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/enable_event_logging` returns JSON:
    ```
    { event_logging : Boolean
    }
    ```


* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. If the error is persistent, get the log files from the server using the XIG Admin Panel and send along with details of the issue being seen to support@xicato.com.

* **Example Call**

  `curl <gateway address: port>/enable_event_logging` will enable event logging.

* **Notes**

  Current as of 2018-4-17.

----
## Get Local TTL
**Change Status:** Initial release in V1.7.1.

  Get the local TTL for a given network.

* **URL:**

  `/get_local_ttl/:network/`

* **Method:**

  `GET`

* **Permission:**

  `administrate`

* **URL Parameters:**

  Required:
    * `network`: The name of the target network.

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/get_local_ttl` returns JSON:
    ```
    { network : String
      ttl : Integer
    }
    ```

* **Error Response**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the error log and send it over for someone to examine.

* **Example Call**

  `curl <gateway address: port>/get_local_ttl/Experience%20Room` gets the gateway's local TTL on the "Experience Room" network.

* **Notes**

  Current as of 2018-5-17

----
## Set Local TTL
**Change Status:** Initial release in V1.7.1.

  Set the local TTL for a given network.

* **URL:**

  `/set_local_ttl/:network/:ttl`

* **Method:**

  `PUT` | `POST`

* **Permission:**

  `administrate`

* **URL Parameters:**

  Required:
    * `network`: The name of the target network.
    * `ttl`: The desired Time-To-Live for packets coming from the gateway on the target network.

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/get_local_ttl` returns JSON:
    ```
    { network : String
      ttl : Integer
    }
    ```

* **Error Response**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the error log and send it over for someone to examine.

* **Example Call**

   ```
     curl -X PUT
          -u <username>:<password>
          <gateway address: port>/set_local_ttl/Test/4
  ``` 
  Sets the gateway's local TTL on the "Test" network to 4.  

* **Notes**

  Current as of 2018-5-17

----
## Get Local Transmission Power
**Change Status:** Initial release in V1.7.1.

  Get the local transmission (Tx) power in decibel-milliwatts (dBm).

* **URL:**

  `/get_local_tx_power`

* **Method:**

  `GET`

* **Permission:**

  `administrate`

* **URL Parameters:**

  None.

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/get_local_tx_power` returns JSON:
    ```
    { tx_power : Float
    }
    ```

* **Error Response**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the error log and send it over for someone to examine.

* **Example Call**

  `curl <gateway address: port>/get_local_tx_power` gets the gateway's local Tx power.

* **Notes**

  Current as of 2018-5-17

----
## Set Local Transmission Power
**Change Status:** Initial release in V1.7.1.

  Set the local BLE radio transmission (Tx) power in decibel-milliwatts (dBm). The default value is the maximum supported setting and it should not be modified except for specific diagnostic purposes.

* **URL:**

  `/set_local_tx_power/:power`

* **Method:**

  `PUT` | `POST`

* **Permission:**

  `administrate`

* **URL Parameters:**

  Required:
    * `power`: The desired Tx power, in dBm. 

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/set_local_tx_power` returns JSON:
    ```
    { tx_power : Float
    , result : Boolean
    }
    ```

* **Error Response**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the error log and send it over for someone to examine.

* **Example Call**

  ```
     curl -X PUT
          -H "Authorization: Bearer <token>"
          <gateway address: port>/set_local_tx_power/9.5
  ``` 
  Sets the gateway's local Tx power to 9.5 dBm.

* **Notes**

  Current as of 2018-5-17.

----
## Get Local Receiver Sensitivity
**Change Status:** Initial release in V1.7.1.

  Reports whether the radio in the XIG is configured to use high gain when receiving.

* **URL:**

  `/get_local_rx_gain`

* **Method:**

  `GET`

* **Permission:**

  `administrate`

* **URL Parameters:**

  None.

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/get_local_rx_gain` returns JSON:
    ```
    { high_gain : Boolean
    }
    ```

* **Error Response**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the error log and send it over for someone to examine.

* **Example Call**

  `curl <gateway address: port>/get_local_rx_gain` gets the gateway's local Rx gain.

* **Notes**

  Current as of 2018-5-17

----
## Enable/Disable Local Receiver High Gain
**Change Status:** New in V1.7.0.  

  Enable or disable high receiver (Rx) gain for the XIG radio.

* **URLs**

  `/enable_local_rx_gain`
  `/disable_local_rx_gain`

* **Methods**

  `PUT` | `POST`

* **Permission:**

  `administrate`

* **URL Parameters**

  None.

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/enable_local_rx_gain` and `/disable_local_rx_gain` return JSON:
    ```
    { high_gain : Boolean
    , result : Boolean
    }
    ```


* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error.

* **Example Call**

  ```
     curl -X PUT
          -H "Authorization: Bearer <token>"
          <gateway address: port>/enable_local_rx_gain
  ``` 
  Sets the local receiver to high-gain mode.

* **Notes**

  Current as of 2018-5-17.

----
## Get Local RSSI Cutoff
**Change Status:** Initial release in V1.7.1.

  Get the local RSSI (Received Signal Strength Indication) cutoff. This is a global value applied to all received packets. Any packets received with an RSSI value below this level are discarded.

* **URL:**

  `/get_rssi_filter`

* **Method:**

  `GET`

* **Permission:**

  `administrate`

* **URL Parameters:**

  None.

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/get_rssi_filter` returns JSON:
    ```
    { rssi_filter : Int
    }
    ```

* **Error Response**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the error log and send it over for someone to examine.

* **Example Call**

  `curl <gateway address: port>/get_rssi_filter` gets the gateway's local RSSI filter cutoff.

* **Notes**

  Current as of 2018-5-17

----
## Set Local RSSI Cutoff
**Change Status:** Initial release in V1.7.1.

  Set the RSSI cutoff. Below this RSSI, packets will not be decoded.

* **URL:**

  `/set_rssi_filter/:rssi_filter`

* **Method:**

  `PUT` | `POST`

* **Permission:**

  `administrate`

* **URL Parameters:**

  Required:
    * `rssi_filter`: The desired cutoff for the local RSSI filter, in dBm (integer values only).

* **Data Parameters**

  None.

* **Success Response**

  * **Code:** 200 <br />
  **Content:** `/set_rssi_filter` returns JSON:
    ```
    { rssi_filter : Int
    , result : Boolean
    }
    ```

* **Error Response**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up or an incorrect URL was requested.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the error log and send it over for someone to examine.

* **Example Call**

  ```
     curl -X PUT
          -u <username>:<password>
          <gateway address: port>/set_rssi_filter/-75
  ``` 
  Sets the gateway's RSSI cutoff to -75 dBm.

* **Notes**

  Current as of 2018-5-17.

----
***Change Notes***
----
----
* 2016-12-20: Document created.
* 2017-2-24: Document updated to include latest updates; largest update is temperature reporting.
* 2017-5-10: Add `/set_advertising_interval` documentation.
* 2017-7-31: Add permissions information and some new calls.
* 2018-1-31: (Many intervening changes) Documentation should be complete up to ms1.6.12.
* 2018-4-18: Many clarifications and ensured that the device network was properly included in the API calls, reordering calls based on permission required. New API calls added to the end of the respective permission sections.
* 2018-5-21: Add new API calls for local radio settings and device (node) configuration