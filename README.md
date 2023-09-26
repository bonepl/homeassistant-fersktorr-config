# Home Assistant's Fersk Torr configuration
Tutorial on how to add Fersk Torr 20 and Fersk Torr 30 to Home Assistant

> Note: I made this using Fersk Torr 20, keep in mind that your device can slightly differ (if you let me know I will update this instruction).

## Enable and configure local tuya integration

Follow the instructions from https://github.com/rospogrigio/localtuya to enable the integration and configure your local tuya account.

## Configure Fersk Torr entities

When your device is discovered you will need to configure entities for it (use default protocol version).

Use the table below to decipher the tuya api entities and configure your device.

| Entity type | ID  | Friendly name               | Description                                              | Inputs                                               | Comments                            |
|-------------|-----|-----------------------------|----------------------------------------------------------|------------------------------------------------------|-------------------------------------|
| Switch      | 1   | fersk_torr_power            | Turns on/off the device                                  |                                                      |                                     |
| Select      | 2   | fersk_torr_mode             | Working mode of the device                               | 0: Auto (humidity based)<br>1: Laundry (continuous)  | Could be<br>handled as switch       |
| Sensor      | 3   | fersk_torr_current_humidity | Current humidity %                                       |                                                      |                                     |
| Number      | 4   | fersk_torr_target_humidity  | Target humidity % <br>(for Auto mode)                    | Manual suggests setting this<br>30 - 80% with step 5 | Could be <br>handled as select      |
| Switch      | 5   | fersk_torr_ionizer          | Turns on/off the ionizer                                 |                                                      |                                     |
| Select      | 6   | fersk_torr_fan_speed        | Fan speed                                                | 0: High<br>1: Low                                    | Could be<br>handled as switch       |
| Switch      | 7   | fersk_torr_child_lock       | Turns on/off the child lock                              |                                                      |                                     |
| Switch      | 8   | fersk_torr_fan_swing        | Turns on/off the fan swing                               |                                                      |                                     |
| Sensor      | 11  | fersk_torr_status           | The status of the device                                 | 0: ok<br>8: tank full                                | These are the ones<br>I am aware of |
| Switch      | 12  | fersk_torr_timer            | Turns timer off or sets<br>timer's minutes               | 0: off<br>1-24: number of hours                      |                                     |
| Sensor      | 13  | fersk_torr_timer_countdown  | Minutes left until device will<br>be turned off by timer |                                                      |                                     |
| Switch      | 101 | fersk_torr_inside_drying    | Turns on/off the inside drying                           |                                                      |                                     |

## Set up a template for status

Status only reports numbers, to give it a little meaning you can set up a template that will map its number to descriptions.

You can use following template as an example (I named it `fersk_torr_status_text` so I can distinguish it from original sensor:
```
{%if states.sensor.fersk_torr_status.state == '0' %}
  ok
{%elif states.sensor.fersk_torr_status.state == '8'%}
  tank full
{%else%}
  unknown
{%endif%}
```

## Set up a dashboard

Once you've got everything configured you can set up a dashboard, here is an example:
![Fersk Torr lovelace](/img/fersk_torr.jpg "Fersk Torr lovelace")

And a JSON for it:
```
type: vertical-stack
title: Fersk Torr
cards:
  - type: horizontal-stack
    cards:
      - show_name: true
        show_icon: true
        type: button
        tap_action:
          action: toggle
        entity: switch.fersk_torr_power
      - show_name: true
        show_icon: true
        type: button
        tap_action:
          action: toggle
        entity: switch.fersk_torr_child_lock
      - show_name: true
        show_icon: true
        type: button
        tap_action:
          action: toggle
        entity: switch.fersk_torr_fan_swing
      - show_name: true
        show_icon: true
        type: button
        tap_action:
          action: toggle
        entity: switch.fersk_torr_inside_drying
      - show_name: true
        show_icon: true
        type: button
        tap_action:
          action: toggle
        entity: switch.fersk_torr_ionizer
  - type: entities
    entities:
      - entity: sensor.fersk_torr_status_text
        name: Status
      - entity: select.fersk_torr_mode
      - entity: number.fersk_torr_target_humidity
      - entity: sensor.fersk_torr_current_humidity
      - entity: select.fersk_torr_fan_speed
      - entity: select.fersk_torr_timer
      - entity: sensor.fersk_torr_timer_minutes
```
