# LoRa On Actility
This guide aims to give a general view of how to setup devices and callbacks to work with Actility network.
The good thing about actility is they send gateways with pre-configurations to ensure connection stability between gateways and their core network.

## Thing Park Dashboard
Is a dashboard where access to all configuration and documentation of Actility network is available.
### Device manager
#### Uplink callback
Before register a device on their network, we need to setup a route (callback) by registering `AS routing profiles` and `Application servers`. 
1. Choose `Application servers` and click `Create`
2. Fill `Name`, choose **HTTP Application Server (LoRaWAN)** for `Type` and click `Create`:
    * Choose **JSON** for `Content Type`.
    * Set **Active** for `Status`.
    * Click `Add` from `Add a route` and choose **Blast** (to send to all Destinations) or `Sequential` (to send one by one).
    * From `Destination` click `Add` to add a route to your server and dont forget to click `Save` to save the setting.
    [![Application server](/img/3.PNG)]
3. Choose `AS routing profiles` and click on `Create` button.
4. Fill `Name`, choose `LoRaWAN` for `Type` and click `Create`:
    * Click `Add` from `Destinations` to add a new destination. Choose **Local application server** for `Type` and your new destination created by your from the previous step. Click `Add` then `Save` to finish this step.
     [![Create AS routing profile](/img/4.PNG)]

Here we finished to set up an UL callback to our servers. We can add more callbacks in the `Application servers` if we'd like to.

#### Device registration
1. Click on `Devices` and `Create`
2. Fill in information:
    * Device name: Your device name
    * Manufacturer: Choose your board manaufacturer. If you don't know you can choose **Generic** and specify **LoRaWan version, RX2, Zone and class** setting.
    * Fill in your device information depending on `Device activiation`.
    * DevAddr: We let server decide so choose **Allocated by the network server**
    * Application server routing profile: Choose the profile we've just created in previous steps.
3. Click `Create` to register this device. 
4. Notice that you can modify this device by clicking on the `Marker` icon.
5. Turn on your device and test. (Use `Wireless Logger` from the dashboard).

#### Downlink callback
* In order to ask for a DL on Actility, we need to register a token for Authentication. Either we can create one from a [shortcut](https://dx-api.thingpark.com/getstarted/#/) of `DX API Console` or from the API. However, the token created from this shortcut will be expired in 1 week as default.
* We are able to create token in `ThingPark DX Admin API` so navigate to this page to create one. Click on `Token gernation`
    [![Create a token](/img/4.PNG)]
    * grant_type: client_credentials
    * client_id, client_secret: username & password
    * renewToken: true
    * validityPeriod: infinite (or other options)
* Click `Try it out`
* You will get response containing a token to use for APIs on Actility.
* Please take a look [here](https://dx-api.thingpark.com/platform/#token-based-authentication) to know how to use the token.
* **To learn the format of message to trigger a downlink. Please take a look [here](https://dx-api.thingpark.com/core/v141/doc/index.html#downlink-message-sending)**

#### Examples
* Example of a response from Token generation:
    ```
    Token generation
    
    Request:
    curl -X POST
    -H 'Content-Type: application/x-www-form-urlencoded'
    -H 'Accept: application/json'
    -d 'grant_type=client_credentials&client_id=dev1-api%2Fjohn%40actility.com&client_secret=1234' 
    https://dx-api.thingpark.com/admin/v141/api/oauth/token
    Response:
    {
      "expires_in" : 92762,
      "client_id" : "dev1-api/john@actility.com",
      "token_type" : "bearer",
      "access_token" : "eyJhbGciOiJSUzI1NiI...KyNftyxc18K9a7y32d1w",
      "scope" : "[SUBSCRIBER:983]",
      "jti" : "32d6a066-2fd1-4564-8915-42e810d464c1"
    }
    ```
* Format of DL message:
    ```
    https://dx-api.thingpark.com/core/latest/api/devices/0004A30B002130E5/downlinkMessages?flushDownlinkQueue=true
    
    {
      "payloadHex": "00010100180e0038",
      "targetPorts": "1"
    }
    ```
* Format of UL message
    ```
    Post Parameters
    Parameter	Values
    LrnDevEui	0004A30B002130E5
    LrnFPort	1
    LrnInfos	TWA_100115594.5807.AS-1-716634

    {
      "DevEUI_uplink": {
        "Time": "2018-10-19T15:40:24.807+02:00",
        "DevEUI": "0004A30B002130E5",
        "FPort": 1,
        "FCntUp": 6,
        "ADRbit": 1,
        "MType": 2,
        "FCntDn": 2,
        "payload_hex": "101a0162",
        "mic_hex": "8c444cbd",
        "Lrcid": "00000127",
        "LrrRSSI": -71,
        "LrrSNR": 8.5,
        "SpFact": 7,
        "SubBand": "G0",
        "Channel": "LC7",
        "DevLrrCnt": 1,
        "Lrrid": "C0001308",
        "Late": 0,
        "LrrLAT": 0,
        "LrrLON": 0,
        "Lrrs": {
          "Lrr": [
            {
              "Lrrid": "C0001308",
              "Chain": 0,
              "LrrRSSI": -71,
              "LrrSNR": 8.5,
              "LrrESP": -71.573822
            }
          ]
        },
        "CustomerID": "100115594",
        "CustomerData": {
          "alr": {
            "pro": "LORA/Generic",
            "ver": "1"
          }
        },
        "ModelCfg": "0",
        "InstantPER": 0,
        "MeanPER": 0,
        "DevAddr": "05AFF5CF",
        "AckRequested": 0,
        "rawMacCommands": "",
        "TxPower": 2,
        "NbTrans": 1
      }
    }
    ```
