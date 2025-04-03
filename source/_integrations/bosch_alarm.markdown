---
title: Bosch Alarm
description: Integrate Bosch Alarms.
ha_category:
  - Alarm
  - Binary sensor
  - Sensor
  - Switch
ha_release: 2025.4
ha_iot_class: Local Push
ha_config_flow: true
ha_codeowners:
  - '@mag1024'
  - '@sanjay900'
ha_domain: bosch_alarm
ha_platforms:
  - alarm_control_panel
ha_integration_type: integration
---

The **Bosch Alarm Panel** {% term integration %} allows you to connect your [Bosch Alarm Panel](https://www.boschsecurity.com) to Home Assistant to control and monitor your Bosch Alarm Panel.

## Data updates

The Bosch Alarm Panel {% term integration %} fetches data from the device every 30 seconds.
Newer devices have the possibility to {% term push %} data.
At the start of the integration we check if your panel supports that, and fall back to {% term polling %} if it is unsupported.

{% include integrations/config_flow.md %}

## Supported devices

- _Solution 2000/3000/4000_
- B Series: _B3512/B4512/B5512/B6512_
- G Series: _B8512G/B9512G_
- _AMAX 2100/3000/4000_
- _D7412GV4/D9412GV4_ [^1]

[^1]: Firmware 2.0+

## Provided entities

The following {% term entities %} are provided:

- [Alarm Control Panel](#alarm-control-panel)
- [Binary Sensor](#binary-sensor)
- [Sensor](#sensor)
- [Switch](#switch)
- [Lock](#lock)

### Alarm Control Panel

This integration adds an Alarm Control Panel device for each configured area, with the ability to issue arm/disarm commands.
This entity reports state (_disarmed_, _armed_away_, etc.).

## Binary Sensor

A binary sensor is added for each point configured on your alarm.

## Sensor

Two sensors are added for the panel, one containing the history events from your panel, and another containing the current faults from it.
Two additional sensors are added per area, one containing information on if the panel is ready to arm, and another containing information about current points that are faulting for this area.
 
## Switch

A switch is added for each output configured on the panel. Note that for some panels, only outputs with the type set to "remote output" can be controlled via _Mode 2_ API.
 
## Lock

A lock is added for each configured door on your panel  (_Solution 4000_, _B Series_ and _G Series_ panels only).
 
## Actions

The integration provides the following actions.

### Action: Set Panel Date & Time
 
| Data attribute         | Optional | Description                                                                     |
|------------------------|----------|---------------------------------------------------------------------------------|
| `datetime`             | Yes      | The Date & Time to set. Defaults to the current date and time if it is not set. |

## Authentication

The primary means of authentication for the _Mode 2_ API is the _Automation_ passcode. It needs to be at least 10 characters long, and it is different from the _User_ code -- a shorter numeric pin used to arm/disarm the panel.
The integration will prompt for the required passcodes, which depend on the panel type.

| Panel | Code |
| --- | --- |
| Solution | User [^2] |
| B Series | Automation |
| G Series | Automation |
| AMAX | Both |

[^2]: The user needs to have the "master code functions" authority if you wish to interact with history events.

{% important %}
Since the _Mode 2_ automation user has "superuser" privileges, it bypasses the regularly configured alarm pin: you will _not_ be prompted for a _User_ code when arming/disarming through the integration.
{% endimportant %}

## Removing the integration

This integration follows standard integration removal. No extra steps are required.

{% include integrations/remove_device_service.md %}

## Troubleshooting

### Unable to connect to the panel

Make sure your panel is on and connected to the network. Also validate that the "Automation passcode" is set to a code that at least 10 characters long, otherwise some panels don't enable the Mode 2 API.

## Examples

### Turning on lights when walking into a room

{% raw %}

```yaml
automation:
  - alias: "Turn on light when walking into room"
    triggers:
      - trigger: state
        entity_id:
          - binary_sensor.bosch_solution_3000_bedroom
        to: "on"
    conditions: []
    actions:
      - action: light.turn_on
        metadata: {}
        data: {}
        target:
          entity_id: light.bedroom_light


```

{% endraw %}

## Known limitations

The integration does not provide the ability to configure the panel, which can instead be done via the configuration utility for your panel.