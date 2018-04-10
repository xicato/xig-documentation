***API Calls for the Xicato Intelligent Gateway***
----
-----

**A Note on Authorization**
----
  All calls, except the call to get the API token and the call to check user permissions accept two forms of authorization:
  * Basic, using the HTTP "Authorization: Basic" headers and the username and password colon-separated and then base64-encoded.
  * Bearer, using the API token provided by the `/api/token` call.

  If a call is made to a URL that the anonymous user has access to, auth headers will be ignored.

  For the time being permissions are global and all permissions need to be on the 'unsecured' network.
  (The Admin Panel will only allow changes on the correct network at this time.)
  This is scheduled to change in Q3 2017.
----

**Get Token (Login Required)**
----
  Returns a JSON Web Token to be used with Bearer Authorization.
* **URL:**

  `/api/token`

* **Method:**

  `GET`

* **Permission:**

  N/A. Tokens are accessible for all non-anonymous users.

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

**Check User Permissions (Login Required)**
----
  Returns a JSON Web Token to be used with Bearer Authorization.
* **URL:**

  `/check_user`

* **Method:**

  `GET`

* **Permission:**

  N/A. Permissions checks are accessible for all non-anonymous users.

* **URL Parameters:**

  None.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200<br/>
  **Content:** `/check_user` returns a string with the list of permissions for the user that is logged in.

* **Error Responses:**
  * **Code:** 403 Forbidden<br/>
  **Meaning:** Bad credentials were used.

* **Notes:**
  Current as of 2017-7-31.
----

**Show Devices**
----
  Return a list of devices at the given gateway, along with some information about the gateway's status.
* **URL:**

  `/devices`

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

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the error log and send it over for someone to examine.

* **Notes:**

  Current as of 2017-7-12

  To see a pretty-printed version of this call, issue a `GET` request to `/devices/pretty`; this is _*NOT*_ interchangeable with the regular version as it is wrapped in HTML tags so it will render properly.

----

**Show Devices with Groups**
----
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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the error log and send it over for someone to examine.

* **Notes:**

  Current as of 2017-10-3

----

**Show Devices with Scenes**
----
  Return a list of devices at the given gateway, along with some information
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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the error log and send it over for someone to examine.

* **Notes:**

  Current as of 2017-7-12


----

**Show Devices with Groups and Scenes**
----
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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the error log and send it over for someone to examine.

* **Notes:**

  Current as of 2017-10-3

  To see a pretty-printed version of this call, issue a `GET` request to `/devices/with_everything/pretty`; this is _*NOT*_ interchangeable with the regular version as it is wrapped in HTML tags so it will render properly.

----

**Set Intensity (with Optional Fading)**
----
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
    * `network` : The network of the target device.
    * `id` : The device id of the target device or group of devices. For groups add `0xC000` to the int value of the group id before casting to a string.
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
  **Meaning** Controls haven't been enabled for this gateway. Enable controls and try again.

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server is not online.

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the error log and send it over for someone to examine.

* **Example Call:**

  ` curl <gateway address: port>/device/setintensity/unsecured/111/50/5000` sets the intensity of the device with id 111 to 50% over 5 seconds

* **Notes:**

  Current as of 2016-12-20

----

**Recall Scene (with Optional Fading)**
----
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
    * `id` : The device id of the target device or group of devices. For groups add `0xC000` to the int value of the group id before casting to a string.
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
  **Meaning** Controls haven't been enabled for this gateway. Enable controls and try again.

  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server is not online.

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the (server) error log and send it over for someone to examine.

* **Example Call:**

  `curl <gateway address: port>/device/recallscene/111/50/5000` Recall the device with id 111 to scene 50 over 5 seconds

* **Notes:**

  Current as of 2016-12-20

----

**Histograms**
----
  Return an intensity or temperature histogram for a given device.

* **URLs**

  `/device/histogram/:type/:network/:device_id`

* **Methods:**

  `GET` | `POST`

* **Permission:**

  `view`

* **URL Parameters:**

  Required:
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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the error log and send it over for someone to examine.

* **Example Call:**

  `curl <gateway address: port>/device/histogram/temperature/111` gets the temperature histogram for device 111.

* **Notes:**

  Current as of 2017-7-12.

  Occasionally, the gateway may return an empty list. This means it hasn't had the opportunity to retrieve the histogram from the device yet. Please allow some time for histograms to populate into the device list.

----

**Device Details**
----
  Retrieve more detailed information for a given device.

* **URLs**

  `/device/details/:network/:device_id`

* **Methods:**

  `GET` | `POST`

* **Permission:**

  `view`

* **URL Parameters:**

  Required:
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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the error log and send it over for someone to examine.

* **Example Call:**

  `curl <gateway address: port>/device/details/111` gets the details for device 111.

* **Notes:**

  Current as of 2017-7-31.

----

**Get Device Groups**
----
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
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.

* **Data Parameters:**

  None.

* **Success Response:**

  * **Code:** 200 <br />
  **Content:** `/device/groups` returns JSON:
  ```
  { "device_id" : String
  , "groups" : List Integer
  }
  ```

* **Error Responses:**
  * **Code:** 404 NOT FOUND <br />
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means device ID provided did not match an ID on the device list.

* **Example Call:**

  `curl <gateway address: port>/device/groups/111` gets the group membership for device 111.
* **Notes:**

  Current as of 2017-9-20.

----
**Set Device Groups**
----
  Sets the group membership for a given device (XID or XIM).

* **URLs**

  `/device/setgroups/:network/:device_id`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `manage`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.

* **Data Parameters:**

  Required:
    * In the body: A list of integers, each separated by a comma and an optional space, and surrounded by brackets e.g. `[51, 53, 55]`. *NOTE*: You must provide a "Content-Type:application/json" HTTP Header with this.

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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
     curl -X PUT
          -H "Content-Type:application/json"
          -d '[51,52,53]'
          -u <username>:<password>
          <gateway address: port>/device/setgroups/111
  ```
  sets the group membership for device 111 to 51, 52, and 53.
* **Notes:**

  Current as of 2017-9-18.

----

**Get Device Scenes**
----
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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means device ID provided did not match an ID on the device list.

* **Example Call:**

  `curl <gateway address: port>/device/scenes/111` gets the scene configuration for device 111.
* **Notes:**

  Current as of 2017-9-20.

----
**Set Device Scenes**
----
  Sets the scene configuration for a given device (XID or XIM).

* **URLs**

  `/device/setscenes/:network/:device_id`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `manage`

* **URL Parameters:**

  Required:
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.

* **Data Parameters:**

  Required:
    * In the body: A list of JSON objects, each having the following structure:
    ```
    { "sceneNumber" : Integer, 0 < n < 65535
    , "intensity" : Float, 0.0 <= m <= 100.0
    , "delayTime" : Integer, n >= 0
    , "fadeTime" : Integer, 0 <= n <= 1.44e7
    }
    ```
    , separated by a comma and an optional space, and surrounded by brackets e.g. `[{"sceneNumber":11, "intensity":34.5, "delayTime":0, "fadeTime":4000}, {"sceneNumber":12, "intensity":0, "delayTime":0, "fadeTime":0}]`. *NOTE*: You must provide a "Content-Type:application/json" HTTP Header with this.

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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
     curl -X PUT
          -H "Content-Type:application/json"
          -d '[{"sceneNumber":12, "intensity":34.5, "delayTime":0, "fadeTime":4000}, {"sceneNumber":13, "intensity":0, "delayTime":0, "fadeTime":0}]'
          -u <username>:<password>
          <gateway address: port>/device/setscenes/111
  ``` 
  sets the scene response configuration for scenes 12 and 13 on device 111.
* **Notes:**

  Current as of 2017-9-18.

----

**Set Device Communication Configuration**
----
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
    { txPower : Float, either -2.5, 3.5, or 9.5. This will only work with PA-equipped devices.
    , highRxGain : Bool
    , dynamicLightStatusConfiguration : DynamicLightStatusConfiguration
    , advertisementSettings : AdvertisementSettings
    }
    ```
    , where `dynamicLightStatusConfiguration` is structured as:
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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
    curl 'http://<gateway>:8000/device/setconfig/Unsecured/111' \
      -X POST \
      -H 'Content-Type: application/json' \
      -H 'Accept-Encoding: gzip, deflate' \
      -H 'Content-Length: 500' \
      --data-binary '{"txPower":9.5,"highRxGain":true,"dynamicLightStatusConfiguration":{"warnInterval":1000,\
        "warnIntensityMax":100,"warnIntensityMin":0,"warnTemperatureMax":90,"warnTemperatureMin":0,"warnVinMax":53,\
        "warnVinMin":43,"changeBursts":3,"changeBurstInterval":100,"changeIntervalMin":1000,"changeIntensity":0,\
        "changeTemperature":5,"changeVin":1},"advertisementSettings":{"lightStatusAdvInterval":10000,\
        "lightHistoryAdvInterval":0,"lightChangeBursts":3,"lightChangeBurstInterval":100,"alwaysConnectable":false}}'
  ``` 
  sets the communication configuration for device 111.
* **Notes:**

  Current as of 2018-4-5.

----
**Set Device Light Setup**
----
  Sets the communication configuration for a given device (XID or XIM).

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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
    curl 'http://<gateway>:8000/device/setlightsetup/Unsecured/111' \
      -X POST \
      -H 'Content-Type: application/json' \
      -H 'Accept-Encoding: gzip, deflate' \
      --data-binary '{"max_level":100,"min_level":0.1,"power_on_type":1,"power_on_level":0,"power_on_fade_time":0,"power_on_start_time":0,"dimming_curve":1,"fade_smoothing":1}'
  ``` 
  sets the communication configuration for device 111.
* **Notes:**

  Current as of 2018-4-6.

----
**Set Device Name**
----
  Sets the scene configuration for a given device.

* **URLs**

  `/device/setname/:network/:device_id/:name`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `manage`

* **URL Parameters:**

  Required:
    * `network` : The network the target device is on. For unsecured devices, use 'Unsecured'.
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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
     curl -X PUT
          -u <username>:<password>
          <gateway address: port>/device/setname/Unsecured/111/Experience%20Room%20Front
  ``` 
  sets the name for unsecured device 111 to 'Experience Room Front'.
* **Notes:**

  Current as of 2017-11-21.

----

**Set Device ID**
----
  Sets the scene configuration for a given device.

* **URLs**

  `/device/setid/:network/:device_id/:new_id`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `manage`

* **URL Parameters:**

  Required:
    * `network` : The network the target device is on. For unsecured devices, use 'Unsecured'.
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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
     curl -X PUT
          -u <username>:<password>
          <gateway address: port>/device/setid/Unsecured/111/2112
  ``` 
  sets the device ID for unsecured device 111 to 2112.

* **Notes:**

  Current as of 2017-11-21.

----

**Set Device Network**
----
  Sets the scene configuration for a given device.

* **URLs**

  `/device/setnetwork/:network/:device_id/:new_network`

* **Methods:**

  `PUT` | `POST`

* **Permission:**

  `manage`. *NOTE*: You will need permissions for both networks for this operation to proceed.

* **URL Parameters:**

  Required:
    * `network` : The network the target device is on. For unsecured devices, use 'Unsecured'.
    * `device_id` : The target device ID. This _cannot_ be a group or a sensor.
    * `new_network` : The desired network that the device will move to. To remove a secure network from a device, use 'Unsecured'.

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
  **Meaning:** The server isn't up.

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

**Get Device iBeacon Configuration**
----
  Retrieve the iBeacon configuration of a selected device.

* **URL:**

  `/device/beacon/ibeacon/get/:network/:device_id`

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
  **Content:** `/device/beacon/ibeacon/get` returns JSON:
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
**Get Device Eddystone URL Configuration**
----
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
**Get Device AltBeacon Config**
----
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
**Set Device iBeacon Configuration**
----
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
**Set Device Eddystone URL Configuration**
----
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
**Set Device AltBeacon Configuration**
----
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
  curl -u <username>:<password> <gateway address: port>/device/beacon/altbeacon/set/Xicato/1 \
    -X POST \
    -H 'Content-Type: application/json' \
    -H 'Accept-Encoding: gzip, deflate' \
    --data-binary '{"company_id":[16,16],"beacon_id":[1,2,3,4,5,6,7,8,9,0,1,2,3,4,5,6,7,8],"mfg_data":37,"measured_power":-45,"period":5000}'
  ```
    will set the AltBeacon configuration for device 1 on the network named "Xicato".

* **Notes:**

  Current as of 2018-04-10.

----
**Name Group**
----
  Name or change the name of a group. It will also save the name through software restarts.

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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means there was an issue with the parameters provided.

* **Example Call:**

  `curl -u <username>:<password> <gateway address: port>/group/setname/7/Group%20Test` will set the name of group 7 to 'Group Test'.

* **Notes:**

  Current as of 2017-09-21.

  Names are *not* shared between gateways currently, and are *not* stored on the devices in the group.

----

**Name Scene**
----
  Name or change the name of a scene. It will also save the name through software restarts.

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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means there was an issue with the parameters provided.

* **Example Call:**

  `curl -u <username>:<password> <gateway address: port>/scene/setname/7/Scene%20Test` will set the name of scene 7 to 'Scene Test'.

* **Notes:**

  Current as of 2017-09-21.

  Names are *not* shared between gateways currently, and are *not* stored on the devices configured to respond to that scene.

----

**Device Dismissal**
----
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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the error log and send it over for someone to examine.

* **Example Call:**

  `curl <gateway address: port>/device/remove/111` will remove device 111 from the device list.

* **Notes:**

  Current as of 2017-7-31.

  If a valid device is errantly dismissed then it will automatically re-appear the next time an advertising packet is received from it.

  It is _strongly recommended_ that device failures and failure to respond be investigated before dismissing a device.


----

**Get Firmware Available**
----
  Returns the available firmware for a device.

* **URLs**

  `/device/firmware/available/:network/:device_id`

* **Method:**

  `GET`

* **Permission:**

  `firmware`

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
  **Meaning:** The server isn't up.

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
**Enqueue A Firmware Update**
----
  Enqueue a firmware update for a device.

* **URLs**

  `/device/firmware/update/:network/:device_id/:version`

* **Method:**

  `POST` | `PUT`

* **Permission:**

  `firmware`

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
  **Meaning:** The server isn't up.

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

**Check Firmware Update Status**
----
  Check on the status of a firmware update.

* **URLs**

  `/device/firmware/update/check/:network/:device_id`

* **Method:**

  `GET`

* **Permission:**

  `firmware`

* **URL Parameters:**

  Required:
    * `network` : The network the target device is on. For unsecured devices, use 'Unsecured'.
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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Generally this means the data provided was improperly formatted, but if it persists there may be underlying issues.

* **Example Call:**

  ```
     curl -X PUT
          -u <username>:<password>
          <gateway address: port>/device/firmware/update/check/Unsecured/111
  ``` 
  checks on the status of a firmware update for unsecured device 111.
* **Notes:**

  Current as of 2017-11-21.

----

**Basic Gateway Info**
----
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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the error log and send it over for someone to examine.

* **Example Call:**

  `curl <gateway address: port>/gateway_info` gets the basic gateway info

* **Notes:**

  Current as of 2017-2-24

----

**Randomize Local ID**
----
  Randomize the local logical address.
  __DO NOT USE THIS UNLESS YOU KNOW WHAT YOU ARE DOING__

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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Tail the error log and send it over for someone to examine.

* **Example Call**

  `curl <gateway address: port>/reload/randomize_id` randomizes the gateway's local ID.

* **Notes**

  Current as of 2016-12-20

----

**Gateway Status**
----
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
  **Meaning:** The server had an error which it couldn't recover from. Tail the error log and send it over for someone to examine.

* **Example Call**

  `curl <gateway address: port>/status` gets the gateway's status.

* **Notes**

  Current as of 2017-2-24

----

**Reload Gateway**
----
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
  **Meaning:** The server had an error which it couldn't recover from (this is very bad in this case). Tail the error log and send it over for someone to examine.

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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Gather the logs and send them over for someone to examine.

* **Example Call**

  `curl <gateway address: port>/set_advertising_interval/0.050` sets the minimum advertising interval to 50 milliseconds.

* **Notes**

  Current as of 2017-5-10


----

**Enable/Disable Network Time Sync**
----
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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Gather the logs and send them over for someone to examine.

* **Example Call**

  `curl <gateway address: port>/enable_time_sync/True` will enable network time advertising.

* **Notes**

  Current as of 2017-5-10

**Enable/Disable Device Autoremoval**
----
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
  **Meaning:** The server isn't up.

    OR

  * **Code:** 500 Internal Server Error<br />
  **Meaning:** The server had an error. Gather the logs and send them over for someone to examine.

* **Example Call**

  `curl <gateway address: port>/enable_auto_purge/900` will set devices to automatically be removed from the device list after 15 minutes of inactivity.

* **Notes**

  Current as of 2018-1-31.


----

Change Notes:
* 2016-12-20: Document created.
* 2017-2-24: Document updated to include latest updates; largest update is temperature reporting.
* 2017-5-10: Add `/set_advertising_interval` documentation.
* 2017-7-31: Add permissions information and some new calls.
* 2018-1-31: (Many intervening changes) Documentation should be complete up to ms1.6.12.