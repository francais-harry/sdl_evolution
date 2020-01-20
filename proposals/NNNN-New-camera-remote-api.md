# New camera remote API

* Proposal: [TBD](TBD)
* Author: [Hisato Shoji](https://github.com/francais-harry/)
* Status: ****
* Impacted Platforms: [Core | HMI | Policy Server | SHAID | iOS | Java Suite | RPC]

## Introduction

This proposal is to add `Camera` to give chance for driver to see and check actual scene around the car. The API should provide overlay view of the camera on top of calling SDL app.

## Motivation

In order to provide safer driving experience especially on narrow road in Asian region, we need to provide additional API to SDL. By `Camera` API, driver can see camera view without switching from an SDL app. This will provide smoother and better UX for SDL navigation apps. The camera view can be shown on top of the SDL app by trigger from the SDL app.

## Proposed Solution

The `Camera` API should have following set of APIs.
- Query and list up available cameras on the vehicle
  - Camera type can be,
    - Front
    - Rear
    - Right
    - Left
    - Panoramic view
    - Moving view
    - See through view
- Open/close camera view
  - When the open API called, camera view should be shown on top of the SDL app as overlay layer.
    - The view should have close button, so that driver can close the overlay view by GUI.
    - The open API should return error when the vehicle drives. (Depends on regulation of each territory)
  - When the close API called, the overlay view should be closed.


We need to add `ParkingSensor` for `GetVehicleData`, `SubscribeVehicleData`, `UnsubscribeVehicleData` & `OnVehicleData` RPCs, we need to add `Camera` to remote control API. Following are the changes needed in MOBILE_API and HMI_API:

# Following part is not described yet!!!!!!


### Updates in MOBILE_API:

#### Add to enum `VehicleDataType`:

```xml
<element name="VEHICLEDATA_PARKINGSENSOR" since="X.x"/>

```
#### Add new struct `ParkingSensor`:

```xml
<struct name="ParkingSensor" since="x.x">
	<param name="parkingSensorPosition" type="ParkingSensorPosition" mandatory="true">
		<description>The position of parking sensor.</description>
	</param>
	<param name="signalStrength" type="Integer" minvalue="0" maxvalue="100" mandatory="true">
		<description>Signal strength of the parking sensor.</description>
	</param>
</struct>
```

#### Update following parameters in these function requests:
* `SubscribeVehicleData`
* `UnsubscribeVehicleData`
* `GetVehicleData`

```xml
<param name="climateData" type="Boolean" mandatory="false" since="X.x">
	<description>See ClimateData</description>
</param>
<param name="externalTemperature" type="Boolean" mandatory="false" deprecated="true" since="X.x">
	<description>The external temperature in degrees celsius. This parameter is deprecated starting RPC Spec X.x.x, please see climateData.</description>
	<history>
		<param name="externalTemperature" type="Boolean" mandatory="false" since="2.0" until="X.x" />
	</history>
</param>
```

#### Update following parameters in these function responses:
* `SubscribeVehicleData`
* `UnsubscribeVehicleData`

```xml
<param name="climateData" type="VehicleDataResult" mandatory="false" since="X.x">
	<description>See ClimateData</description>
</param>
<param name="externalTemperature" type="VehicleDataResult" mandatory="false" deprecated="true" since="X.x">
	<description>The external temperature in degrees celsius. This parameter is deprecated starting RPC Spec X.x.x, please see climateData.</description>
	<history>
		<param name="externalTemperature" type="VehicleDataResult" mandatory="false" since="2.0" until="X.x" />
	</history>
</param>
```

#### Update following parameters in these function responses:
* `GetVehicleData`
* `OnVehicleData`

```xml
<param name="climateData" type="ClimateData" mandatory="false" since="X.x">
	<description>See ClimateData</description>
</param>
<param name="externalTemperature" type="Float" minvalue="-40" maxvalue="100" mandatory="false" deprecated="true" since="X.x">
	<description>The external temperature in degrees celsius. This parameter is deprecated starting RPC Spec X.x.x, please see climateData.</description>
	<history>
		<param name="externalTemperature" type="Float" minvalue="-40" maxvalue="100" mandatory="false" since="2.0" until="X.x" />
	</history>
</param>
```

### Updates in HMI_API

#### Add to enum `VehicleDataType` in `Common` interface:

```xml
<element name="VEHICLEDATA_CLIMATEDATA"/>
```
#### Add new struct `ClimateData` in `Common` interface:

```xml
<struct name="ClimateData">
	<param name="externalTemperature" type="Common.Temperature" mandatory="false">
		<description>The external temperature in degrees celsius</description>
	</param>
	<param name="cabinTemperature" type="Common.Temperature" mandatory="false">
		<description>Internal ambient cabin temperature in degrees celsius</description>
	</param>
	<param name="atmosphericPressure" type="Float" minvalue="0" maxvalue="2000" mandatory="false">
		<description>Current atmospheric pressure in mBar</description>
	</param>
</struct>
```

#### Add the following parameter to these function requests:
* `SubscribeVehicleData`
* `UnsubscribeVehicleData`
* `GetVehicleData`

```xml
<param name="climateData" type="Boolean" mandatory="false">
	<description>See ClimateData</description>
</param>
```

#### Add the following parameter to these function responses:
* `SubscribeVehicleData`
* `UnsubscribeVehicleData`

```xml
<param name="climateData" type="Common.VehicleDataResult" mandatory="false">
	<description>See ClimateData</description>
</param>
```

#### Add the following parameter to these function responses:
* `GetVehicleData`
* `OnVehicleData`

```xml
<param name="climateData" type="Common.ClimateData" mandatory="false">
	<description>See ClimateData</description>
</param>
```

## Potential downsides

Author is not aware of any downsides to proposed solution. This proposal just enhances the SDL content.

## Impact on existing code

* SDL Core needs to be updated as per new API.
* iOS/Java Suite need to be updated to support getters/setters as per new API.
* SDL Server needs to add permissions for new vehicle data items.
* SHAID needs to add mappings for new vehicle data items as per updated spec.
* HMI needs to be updated to support new vehicle data items.

## Alternatives considered

* None
