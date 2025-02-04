# Retrieving Vehicle Data
You can use the @![iOS]`SDLGetVehicleData` and `SDLSubscribeVehicleData`!@@![android, javaSE, javaEE,javascript]`GetVehicleData` and `SubscribeVehicleData`!@ RPC requests to get vehicle data. Each vehicle manufacturer decides which data it will expose and to whom they will expose it. Please check the response from Core to find out which data you will have permission to access. Additionally, be aware that the user may have the ability to disable vehicle data access through the settings menu of their head unit. It may be possible to access vehicle data when the `hmiLevel` is `NONE` (i.e. the user has not opened your SDL app) but you will have to request this permission from the vehicle manufacturer.

!!! NOTE
You will only have access to vehicle data that is allowed to your `appName` and `appId` combination. Permissions will be granted by each OEM separately. See [Understanding Permissions](Getting Started/Understanding Permissions) for more details.
!!!

| Vehicle Data | Parameter Name  |  Description | RPC Version | Deprecated |
| ------------- | ------------- | ------------- | ------------- | ------------- |
| Acceleration Pedal Position | accPedalPosition | Accelerator pedal position (percentage depressed) |||
| Airbag Status | airbagStatus | Status of each of the airbags in the vehicle: yes, no, no event, not supported, fault |||
| Belt Status | beltStatus | The status of each of the seat belts: no, yes, not supported, fault, or no event |||
| Body Information | bodyInformation | Door ajar status for each door. Roof status. Trunk & hood Status. The Ignition status. The ignition stable status. The park brake active status |||
| Climate Data | climateData | Information about cabin temperature, atmospheric pressure, and external temperature | RPC v7.1+ ||
| Cloud App Vehicle Id | cloudAppVehicleID | The id for the vehicle when connecting to cloud applications | RPC v5.1+ ||
| Cluster Mode Status | clusterModeStatus | Whether or not the power mode is active. The power mode qualification status: power mode undefined, power mode evaluation in progress, not defined, power mode ok. The car mode status: normal, factory, transport, or crash. The power mode status: key out, key recently out, key approved, post accessory, accessory, post ignition, ignition on, running, crank |||
| Device Status | deviceStatus | Contains information about the smartphone device. Is voice recognition on or off, has a bluetooth connection been established, is a call active, is the phone in roaming mode, is a text message available, the battery level, the status of the mono and stereo output channels, the signal level, the primary audio source, whether or not an emergency call is currently taking place |||
| Driver Braking | driverBraking | The status of the brake pedal: yes, no, no event, fault, not supported |||
| E-Call Information | eCallInfo | Information about the status of an emergency call |||
| Electronic Parking Brake Status | electronicParkingBrakeStatus | The status of the electronic parking brake. Available states: closed, transition, open, drive active, fault | RPC v5.0+ ||
| Emergency event | emergencyEvent | The type of emergency: frontal, side, rear, rollover, no event, not supported, fault. Fuel cutoff status: normal operation, fuel is cut off, fault. The roll over status: yes, no, no event, not supported, fault. The maximum change in velocity. Whether or not multiple emergency events have occurred |||
| Engine Oil Life | engineOilLife | The estimated percentage (0% - 100%) of remaining oil life of the engine | RPC v5.0+ ||
| Engine Torque | engineTorque | Torque value for engine (in Nm) on non-diesel variants |  ||
| External Temperature | externalTemperature | The external temperature in degrees celsius || RPC v7.1 |
| Fuel Level | fuelLevel | The fuel level in the tank (percentage) || RPC v7.0 |
| Fuel Level State | fuelLevel_State | The fuel level state: Unknown, Normal, Low, Fault, Alert, or Not Supported || RPC v7.0 |
| Fuel Range | fuelRange | The estimate range in KM the vehicle can travel based on fuel level and consumption. As of RPC 7.0, this also contains Fuel Level and Fuel Level State information. | RPC v5.0+ ||
| Gear Status | gearStatus | Includes information about the transmission, the user's selected gear, and the actual gear of the vehicle.  | RPC v7.0+ ||
| GPS | gps | Longitude and latitude, current time in UTC, degree of precision, altitude, heading, speed, satellite data vs dead reckoning, and supported dimensions of the GPS |||
| Hands Off Steering | handsOffSteering | Status of hands on steering wheels capability | RPC v7.0+ ||
| Head Lamp Status | headLampStatus | Status of the head lamps: whether or not the low and high beams are on or off. The ambient light sensor status: night, twilight 1, twilight 2, twilight 3, twilight 4, day, unknown, invalid |||
| Instant Fuel Consumption | instantFuelConsumption | The instantaneous fuel consumption in microlitres |||
| My Key | myKey | Information about whether or not the emergency 911 override has been activated |||
| Odometer | odometer | Odometer reading in km |||
| PRNDL | prndl | The selected gear the car is in: park, reverse, neutral, drive, sport, low gear, first, second, third, fourth, fifth, sixth, seventh or eighth gear, unknown, or fault ||RPC v7.0 |
| RPM | rpm | The number of revolutions per minute of the engine |||
| Seat Occupancy | seatOccupancy | The status of the seats that show whether each seat is occupied and belted or not | RPC v7.1+ ||
| Speed | speed | Speed in KPH |||
| Stability Control Status | stabilityControlsStatus | Status of the vehicle's stability control and trailer sway control | RPC v7.0+ ||
| Steering Wheel Angle | steeringWheelAngle | Current angle of the steering wheel (in degrees) |||
| Tire Pressure | tirePressure | Tire status of each wheel in the vehicle: normal, low, fault, alert, or not supported. Warning light status for the tire pressure: off, on, flash, or not used |||
| Turn Signal | turnSignal | The status of the turn signal. Available states: off, left, right, both | RPC v5.0+ ||
| VIN | vin | The Vehicle Identification Number |||
| Window Status | windowStatus | An array of window locations and approximate position | RPC v7.0+ ||
| Wiper Status | wiperStatus | The status of the wipers: off, automatic off, off moving, manual interaction off, manual interaction on, manual low, manual high, manual flick, wash, automatic low, automatic high, courtesy wipe, automatic adjust, stalled, no data exists |||

## One-Time Vehicle Data Retrieval
To get vehicle data a single time, use the @![iOS]`SDLGetVehicleData`!@@![android, javaSE, javaEE,javascript]`GetVehicleData`!@ RPC.

@![iOS]
##### Objective-C
```objc
SDLGetVehicleData *getGPSData = [[SDLGetVehicleData alloc] init];
getGPSData.gps = @YES;
[self.sdlManager sendRequest:getGPSData withResponseHandler:^(__kindof SDLRPCRequest * _Nullable request, __kindof SDLRPCResponse * _Nullable response, NSError * _Nullable error) {
    SDLGetVehicleDataResponse *vehicleDataResponse = (SDLGetVehicleDataResponse *)response;
    SDLResult resultCode = vehicleDataResponse.resultCode;

    if (!vehicleDataResponse.success.boolValue) {
        if ([resultCode isEqualToEnum:SDLResultDisallowed]) {
            <#The app does not have permission to access this vehicle data#>
        } else if ([resultCode isEqualToEnum:SDLResultRejected]) {
            <#The app does not have permission to access this vehicle data because of the app state (i.e. app is closed or in background)#>
        } else if ([resultCode isEqualToEnum:SDLResultVehicleDataNotAllowed]) {
            <#The data is not available or responding on the system#>
        } else if ([resultCode isEqualToEnum:SDLResultVehicleDataNotAvailable]) {
            <#The user has turned off access to vehicle data, and it is globally unavailable to mobile applications#>
        } else {
            <#Some other error occurred#>
        }
        return;
    }

    SDLGPSData *gpsData = vehicleDataResponse.gps;
    if (gpsData == nil) { return; }
    <#Use the GPS data#>
}];
```

##### Swift
```swift
let getGPSData = SDLGetVehicleData()
getGPSData.gps = NSNumber(true)
sdlManager.send(request: getGPSData) { (request, response, error) in
    guard let response = response as? SDLGetVehicleDataResponse else { return }
    guard response.success.boolValue == true else {
        switch response.resultCode {
        case .disallowed:
            <#The app does not have permission to access this vehicle data#>
        case .rejected:
            <#The app does not have permission to access this vehicle data because of the app state (i.e. app is closed or in background)#>
        case .vehicleDataNotAllowed:
            <#The data is not available or responding on the system#>
        case .vehicleDataNotAvailable:
            <#The user has turned off access to vehicle data, and it is globally unavailable to mobile applications#>
        default:
            <#Some other error occurred#>
        }
        return
    }

    guard let gpsData = response.gps else { return }
    <#Use the GPS data#>
}
```
!@

@![android, javaSE, javaEE]
```java
GetVehicleData vdRequest = new GetVehicleData()
    .setPrndl(true);
vdRequest.setOnRPCResponseListener(new OnRPCResponseListener() {
    @Override
    public void onResponse(int correlationId, RPCResponse response) {
        if(response.getSuccess()){
            PRNDL prndl = ((GetVehicleDataResponse) response).getPrndl();
            DebugTool.logInfo("SdlService", "PRNDL status: " + prndl.toString());
        }else{
            DebugTool.logInfo("SdlService", "GetVehicleData was rejected.");
        }
    }
});
sdlManager.sendRPC(vdRequest);
```
!@

@![javascript]
```js
// sdl_javascript_suite v1.1+
const vdRequest = new SDL.rpc.messages.GetVehicleData()
    .setPrndl(true);
const response = await sdlManager.sendRpcResolve(vdRequest);

if (response.getSuccess()) {
    const prndl = response.getPrndl();
    console.log('PRNDL status: ' + prndl);
} else {
    console.log('GetVehicleData was rejected.')
}
// thrown exceptions should be caught by a parent function via .catch()

// Pre sdl_javascript_suite v1.1
const vdRequest = new SDL.rpc.messages.GetVehicleData()
    .setPrndl(true);
const response = await sdlManager.sendRpc(vdRequest).catch(error => error);

if (response.getSuccess()) {
    const prndl = response.getPrndl();
    console.log('PRNDL status: ' + prndl);
} else {
    console.log('GetVehicleData was rejected.')
}
```
!@

## Subscribing to Vehicle Data
Subscribing to vehicle data allows you to get notifications whenever new data is available. You should not rely upon getting this data in a consistent manner. New vehicle data is available roughly every second but notification timing can vary between modules.

@![iOS]
!!! NOTE
Please note that if you are integrating an sdl_ios version less than v6.3, the following example code will not work. We recommend updating to the latest release version.
!!!
!@

@![iOS]
**First**, register to observe the `SDLDidReceiveVehicleDataNotification` notification:

##### Objective-C
```objc
[self.sdlManager subscribeToRPC:SDLDidReceiveVehicleDataNotification withObserver:self selector:@selector(vehicleDataAvailable:)];
```

##### Swift
```swift
sdlManager.subscribe(to: .SDLDidReceiveVehicleData, observer: self, selector: #selector(vehicleDataAvailable(_:)))
```

**Second**, send the `SubscribeVehicleData` request:

##### Objective-C
```objc
SDLSubscribeVehicleData *subscribeGPSData = [[SDLSubscribeVehicleData alloc] init];
subscribeGPSData.gps = @YES;

[self.sdlManager sendRequest:subscribeGPSData withResponseHandler:^(__kindof SDLRPCRequest * _Nullable request, __kindof SDLRPCResponse * _Nullable response, NSError * _Nullable error) {
    SDLSubscribeVehicleDataResponse *vehicleDataResponse = (SDLSubscribeVehicleDataResponse *)response;
    if (!vehicleDataResponse.success.boolValue) {
        if ([vehicleDataResponse.resultCode isEqualToEnum:SDLResultDisallowed]) {
            <#The app does not have permission to access this vehicle data#>
        } else if ([vehicleDataResponse.resultCode isEqualToEnum:SDLResultUserDisallowed]) {
            <#The user has not granted access to this type of vehicle data item at this time#>
        } else if ([vehicleDataResponse.resultCode isEqualToEnum:SDLResultIgnored]) {
            SDLVehicleDataResult *gpsData = vehicleDataResponse.gps;
            if ([gpsData.resultCode isEqualToEnum:SDLVehicleDataResultCodeDataAlreadySubscribed]) {
                <#Ignoring request because the app is already subscribed to GPS data#>
            } else if ([gpsData.resultCode isEqualToEnum:SDLVehicleDataResultCodeVehicleDataNotAvailable]) {
                <#The app has permission to access to the GPS data, but the vehicle does not provide it#>
            } else {
                <#Request ignored for some other reason#>
            }
        } else {
            <#Some other error occurred#>
        }
        return;
    }

     <#Successfully subscribed to GPS data#>
 }];
```

##### Swift
```swift
let subscribeGPSData = SDLSubscribeVehicleData()
subscribeGPSData.gps = NSNumber(true)

sdlManager.send(request: subscribeGPSData) { (request, response, error) in
    guard let response = response as? SDLSubscribeVehicleDataResponse else { return }
    guard response.success.boolValue == true else {
        switch response.resultCode {
        case .disallowed:
            <#The app does not have permission to access this vehicle data#>
        case .userDisallowed:
            <#The user has not granted access to this type of vehicle data item at this time#>
        case .ignored:
            guard let gpsData = response.gps else { break }
            switch gpsData.resultCode {
            case .dataAlreadySubscribed:
                <#Ignoring request because the app is already subscribed to GPS data#>
            case .vehicleDataNotAvailable:
                <#The app has permission to access to the GPS data, but the vehicle does not provide it#>
            default:
                <#Request ignored for some other reason#>
            }
        default:
            <#Some other error occurred#>
        }
        return
    }

    <#Successfully subscribed to GPS data#>
}
```

**Third**, react to the notification when new vehicle data is received:

##### Objective-C
``` objc
- (void)vehicleDataAvailable:(SDLRPCNotificationNotification *)notification {
    SDLOnVehicleData *onVehicleData = (SDLOnVehicleData *)notification.notification;
    SDLGPSData *gps = onVehicleData.gps;
    if (gps == nil) { return; }
    <#Use the GPS data#>
}
```

##### Swift
```swift
func vehicleDataAvailable(_ notification: SDLRPCNotificationNotification) {
    guard let onVehicleData = notification.notification as? SDLOnVehicleData, let gpsData = onVehicleData.gps else {
        return
    }
    <#Use the GPS data#>
}
```
!@

@![android, javaSE, javaEE]
**First**, you should add a notification listener for the `OnVehicleData` notification:

```java
sdlManager.addOnRPCNotificationListener(FunctionID.ON_VEHICLE_DATA, new OnRPCNotificationListener() {
    @Override
    public void onNotified(RPCNotification notification) {
        OnVehicleData onVehicleDataNotification = (OnVehicleData) notification;
        if (onVehicleDataNotification.getPrndl() != null) {
            DebugTool.logInfo("SdlService", "PRNDL status was updated to: " + onVehicleDataNotification.getPrndl());
        }
    }
});
```

**Second**, send the `SubscribeVehicleData` request:

```java
SubscribeVehicleData subscribeRequest = new SubscribeVehicleData()
    .setPrndl(true);
subscribeRequest.setOnRPCResponseListener(new OnRPCResponseListener() {
    @Override
    public void onResponse(int correlationId, RPCResponse response) {
        if(response.getSuccess()){
            DebugTool.logInfo("SdlService", "Successfully subscribed to vehicle data.");
        }else{
            DebugTool.logInfo("SdlService", "Request to subscribe to vehicle data was rejected.");
        }
    }
});
sdlManager.sendRPC(subscribeRequest);
```

**Third**, the `onNotified` method will be called when there is an update to the subscribed vehicle data.
!@

@![javascript]
**First**, you should add a notification listener for the `OnVehicleData` notification:

```js
sdlManager.addRpcListener(SDL.rpc.enums.FunctionID.OnVehicleData, (onVehicleDataNotification) => {
    if (onVehicleDataNotification.getPrndl() !== null) {
        console.log('PRNDL status was updated to: ' + onVehicleDataNotification.getPrndl());
    }
});
```

**Second**, send the `SubscribeVehicleData` request:

```js
// sdl_javascript_suite v1.1+
const subscribeRequest = new SDL.rpc.messages.SubscribeVehicleData()
    .setPrndl(true);
const response = await sdlManager.sendRpcResolve(subscribeRequest);
if (response.getSuccess()) {
    console.log('Successfully subscribed to vehicle data.');
} else {
    console.log('Request to subscribe to vehicle data was rejected.');
}
// thrown exceptions should be caught by a parent function via .catch()

// Pre sdl_javascript_suite v1.1
const subscribeRequest = new SDL.rpc.messages.SubscribeVehicleData()
    .setPrndl(true);
const response = await sdlManager.sendRpc(subscribeRequest).catch(error => error);
if (response.getSuccess()) {
    console.log('Successfully subscribed to vehicle data.');
} else {
    console.log('Request to subscribe to vehicle data was rejected.');
}
```

**Third**, the `addRpcListener` function passed in will be called when there is an update to the subscribed vehicle data.
!@


## Unsubscribing from Vehicle Data
We suggest that you only subscribe to vehicle data as needed. To stop listening to specific vehicle data use the @![iOS]`SDLUnsubscribeVehicleData`!@@![android, javaSE, javaEE,javascript]`UnsubscribeVehicleData`!@ RPC.

@![iOS]
##### Objective-C
```objc
SDLUnsubscribeVehicleData *unsubscribeGPSData = [[SDLUnsubscribeVehicleData alloc] init];
unsubscribeGPSData.gps = @YES;

[self.sdlManager sendRequest:unsubscribeGPSData withResponseHandler:^(__kindof SDLRPCRequest * _Nullable request, __kindof SDLRPCResponse * _Nullable response, NSError * _Nullable error) {
    SDLUnsubscribeVehicleDataResponse *vehicleDataResponse = (SDLUnsubscribeVehicleDataResponse*)response;

    if (!vehicleDataResponse.success.boolValue) {
        if ([vehicleDataResponse.resultCode isEqualToEnum:SDLResultDisallowed]) {
            <#The app does not have permission to access this vehicle data#>
        } else if ([vehicleDataResponse.resultCode isEqualToEnum:SDLResultUserDisallowed]) {
            <#The user has not granted access to this type of vehicle data item at this time#>
        } else if ([vehicleDataResponse.resultCode isEqualToEnum:SDLResultIgnored]) {
            SDLVehicleDataResult *gpsData = vehicleDataResponse.gps;
            if ([gpsData.resultCode isEqualToEnum:SDLVehicleDataResultCodeDataNotSubscribed]) {
                <#The app has access to this data item but ignoring since the app is already unsubscribed to GPS data#>
            } else {
                <#Request ignored for some other reason#>
            }
        } else {
            <#Some other error occurred#>
        }
        return;
    }

    <#Successfully unsubscribed to GPS data#>
}];
```

##### Swift
```swift
let unsubscribeGPSData = SDLUnsubscribeVehicleData()
unsubscribeGPSData.gps = NSNumber(true)

sdlManager.send(request: unsubscribeGPSData) { (request, response, error) in
    guard let response = response as? SDLUnsubscribeVehicleDataResponse else { return }

    guard response.success.boolValue == true else {
        switch response.resultCode {
        case .disallowed:
            <#The app does not have permission to access this vehicle data#>
        case .userDisallowed:
            <#The user has not granted access to this type of vehicle data item at this time#>
        case .ignored:
            guard let gpsData = response.gps else { break }
            switch gpsData.resultCode {
            case .dataNotSubscribed:
                <#The app has access to this data item but ignoring since the app is already unsubscribed to GPS data#>
            default:
                <#Request ignored for some other reason#>
            }
        default:
            <#Some other error occurred#>
        }
        return
    }

    <#Successfully unsubscribed to GPS data#>
}
```
!@

@![android, javaSE, javaEE]
```java
UnsubscribeVehicleData unsubscribeRequest = new UnsubscribeVehicleData()
    .setPrndl(true); // unsubscribe to PRNDL data
unsubscribeRequest.setOnRPCResponseListener(new OnRPCResponseListener() {
    @Override
    public void onResponse(int correlationId, RPCResponse response) {
        if(response.getSuccess()){
            DebugTool.logInfo("SdlService", "Successfully unsubscribed to vehicle data.");
        }else{
            DebugTool.logInfo("SdlService", "Request to unsubscribe to vehicle data was rejected.");
        }
    }
});
sdlManager.sendRPC(unsubscribeRequest);
```
!@

@![javascript]
```js
// sdl_javascript_suite v1.1+
const unsubscribeRequest = new SDL.rpc.messages.UnsubscribeVehicleData()
    .setPrndl(true); // unsubscribe to PRNDL data
const response = await sdlManager.sendRpcResolve(unsubscribeRequest);
if (response.getSuccess()) {
    console.log('Successfully unsubscribed to vehicle data.');
} else {
    console.log('Request to unsubscribe to vehicle data was rejected.');
}
// thrown exceptions should be caught by a parent function via .catch()

// Pre sdl_javascript_suite v1.1
const unsubscribeRequest = new SDL.rpc.messages.UnsubscribeVehicleData()
    .setPrndl(true); // unsubscribe to PRNDL data
const response = await sdlManager.sendRpc(unsubscribeRequest).catch(error => error);
if (response.getSuccess()) {
    console.log('Successfully unsubscribed to vehicle data.');
} else {
    console.log('Request to unsubscribe to vehicle data was rejected.');
}
```
!@

## OEM-Specific Vehicle Data
OEM applications can access additional vehicle data published by their systems that is not available via the SDL vehicle data APIs. This data is accessed using the same SDL vehicle data RPCs, but instead of requesting a certain type of SDL-specified data, you must request data using a custom vehicle data name. The type of object returned is up to the OEM and must be parsed manually.

!!! NOTE
This feature is only for OEM-created applications and is not permitted for 3rd-party use.
!!!

### Requesting One-Time OEM-Specific Vehicle Data
Below is an example of requesting a custom piece of vehicle data with the name `OEM-X-Vehicle-Data`. To adapt this for subscriptions instead, you must look at the section **Subscribing to Vehicle Data** above and adapt the example for subscribing to custom vehicle data based on what you see in the examples below.

@![iOS]
##### Objective-C
```objc
SDLGetVehicleData *getCustomData = [[SDLGetVehicleData alloc] init];
[getCustomData setOEMCustomVehicleData:@"OEM-X-Vehicle-Data" withVehicleDataState:YES];
[self.sdlManager sendRequest:getCustomData withResponseHandler:^(__kindof SDLRPCRequest * _Nullable request, __kindof SDLRPCResponse * _Nullable response, NSError * _Nullable error) {
    SDLGetVehicleDataResponse *vehicleDataResponse = (SDLGetVehicleDataResponse *)response;
    SDLResult resultCode = vehicleDataResponse.resultCode;

    if (!vehicleDataResponse.success.boolValue) {
        if ([resultCode isEqualToEnum:SDLResultDisallowed]) {
            <#The app does not have permission to access this vehicle data#>
        } else if ([resultCode isEqualToEnum:SDLResultRejected]) {
            <#The app does not have permission to access this vehicle data because of the app state (i.e. app is closed or in background)#>
        } else if ([resultCode isEqualToEnum:SDLResultVehicleDataNotAllowed]) {
            <#The data is not available or responding on the system#>
        } else if ([resultCode isEqualToEnum:SDLResultVehicleDataNotAvailable]) {
            <#The user has turned off access to vehicle data, and it is globally unavailable to mobile applications#>
        } else {
            <#Some other error occurred#>
        }
        return;
    }

    <#OEMCustomVehicleDataType#> *customVehicleData = [vehicleDataResponse getOEMCustomVehicleData:@"OEM-X-Vehicle-Data"];
    if (customVehicleData == nil) { return; }
    <#Use the custom data#>
}];
```

##### Swift
```swift
let getCustomData = SDLGetVehicleData()
getCustomData.setOEMCustomVehicleData(name: "OEM-X-Vehicle-Data", state: true)
sdlManager.send(request: getCustomData) { (request, response, error) in
    guard let response = response as? SDLGetVehicleDataResponse else { return }
    guard response.success.boolValue == true else {
        switch response.resultCode {
        case .disallowed:
            <#The app does not have permission to access this vehicle data#>
        case .rejected:
            <#The app does not have permission to access this vehicle data because of the app state (i.e. app is closed or in background)#>
        case .vehicleDataNotAllowed:
            <#The data is not available or responding on the system#>
        case .vehicleDataNotAvailable:
            <#The user has turned off access to vehicle data, and it is globally unavailable to mobile applications#>
        default:
            <#Some other error occurred#>
        }
        return
    }

    guard let customVehicleData = response.getOEMCustomVehicleData("OEM-X-Vehicle-Data") as? <#OEMCustomVehicleDataType#> else { return }
    <#Use the custom data#>
}
```
!@

@![android, javaSE, javaEE]

```java

GetVehicleData vdRequest = new GetVehicleData()
    .setOEMCustomVehicleData("OEM-X-Vehicle-Data", true);
vdRequest.setOnRPCResponseListener(new OnRPCResponseListener() {
    @Override
    public void onResponse(int correlationId, RPCResponse response) {
        if(response.getSuccess()){
            Object CustomData = ((GetVehicleDataResponse) response).getOEMCustomVehicleData("OEM-X-Vehicle-Data");
        }else{
            DebugTool.logInfo("SdlService", "GetVehicleData was rejected.");
        }
    }
});
sdlManager.sendRPC(vdRequest);
```
!@

@![javascript]
```js
// sdl_javascript_suite v1.1+
const vdRequest = new SDL.rpc.messages.GetVehicleData()
    .setOemCustomVehicleData('OEM-X-Vehicle-Data', true);
const response = await sdlManager.sendRpcResolve(vdRequest);
if (response.getSuccess()) {
    const CustomData = response.getOemCustomVehicleData('OEM-X-Vehicle-Data');
} else {
    console.log('GetVehicleData was rejected.')
}
// thrown exceptions should be caught by a parent function via .catch()

// Pre sdl_javascript_suite v1.1
const vdRequest = new SDL.rpc.messages.GetVehicleData()
    .setOemCustomVehicleData('OEM-X-Vehicle-Data', true);
const response = await sdlManager.sendRpc(vdRequest).catch(error => error);
if (response.getSuccess()) {
    const CustomData = response.getOemCustomVehicleData('OEM-X-Vehicle-Data');
} else {
    console.log('GetVehicleData was rejected.')
}
```
!@
