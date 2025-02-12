---
title: Bosch Alarm
description: Integrate Bosch Alarms.
ha_category:
  - Alarm Control Panel
  - Binary sensor
  - Sensor
  - Switch
ha_release: 2025.2.3
ha_iot_class: Local Push
ha_config_flow: true
ha_codeowners:
  - '@mag1024'
  - '@sanjay900'
ha_domain: bosch_alarm
ha_platforms:
  - alarm_control_panel
  - binary_sensor
  - sensor
  - switch
ha_zeroconf: false
ha_integration_type: integration
---

The **Bosch Alarm Panel** {% term integration %} allows you to connect your [Bosch Alarm Panels](https://www.boschsecurity.com) to Home Assistant in order to control and monitor your Bosch Alarm Panels.

{% include integrations/config_flow.md %}

Supported panels:

* _Solution 2000/3000/4000_
* B Series: _B3512/B4512/B5512/B6512_
* G Series: _B8512G/B9512G_
* _AMAX 2100/3000/4000_
* _D7412GV4/D9412GV4_ [^1]

[^1]: Firmware 2.0+

### Provided entities

* [AlarmControlPanel](https://developers.home-assistant.io/docs/core/entity/alarm-control-panel/) for each configured area, with the ability to issue arm/disarm commands.
  This entity reports state (_disarmed_, _armed_away_, etc), and contains custom attributes _ready_to_arm_ (_no_|_home_|_away_), and a _faulted_points_ counter.
* [BinarySensor](https://developers.home-assistant.io/docs/core/entity/binary-sensor) for each configured alarm point.
* [Sensor](https://developers.home-assistant.io/docs/core/entity/sensor/) entities for the panel's current faults, and the panel's history.
  The history itself is stored on a `history` attribute, as there is a limit to how much text a sensor can store in its state.
* [Switch](https://developers.home-assistant.io/docs/core/entity/switch) for each configured output. Note that for some panels, only outputs with the type set to "remote output" can be controlled via _Mode 2_ API.
* [Lock](https://developers.home-assistant.io/docs/core/entity/lock) for each configured "door" (_Solution 4000_, _B Series_ and _G Series_ panels only).
* A custom Service, called `set_date_time`, that can be used to set the time and date on the panel.

### Authentication

The primary means of authentication for the _Mode 2_ API is the _Automation_ passcode. It needs to be at least 10 characters long, and it is different from the _User_ code -- a shorter numeric pin used to arm/disarm the panel.
The integration will prompt for the required passcodes, which depend on the panel type.

| Panel | Code |
| --- | --- |
| Solution | User [^2] |
| B Series | Automation |
| G Series | Automation |
| AMAX | Both |

[^2]: The user needs to have the "master code functions" authority if you wish to interact with history events.

⚠️ Since the _Mode 2_ automation user has "superuser" privileges, it bypasses the regularly-configured alarm pin: you will _not_ be prompted for a _User_ code when arming/disaming through the integration.
The integration also supports (optionally) setting a Home Assistant-local pin to protect these operations -- it does not need to match any of the codes configured on the panel.
