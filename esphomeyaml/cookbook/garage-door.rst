Simple Garage Doors
==================

.. seo::
    :description: Instructions for setting up a simple garage door in esphomelib.
    :image: window-open.png

The following are examples of some garage door types that can be implemented in esphomeyaml.

Dual-Relay Garage Door
----------------------

The following is a possible configuration file for garage doors that are controlled by two relays:
One for opening and another one for closing the garage door. When either one of them is turned on
for a short period of time, the close/open action begins.



.. code-block:: yaml

    switch:
      - platform: gpio
        pin: D3
        name: "Garage Door Open Switch"
        id: open_switch
      - platform: gpio
        pin: D4
        name: "Garage Door Close Switch"
        id: close_switch
    cover:
      - platform: template
        name: "Garage Door"
        open_action:
          # Cancel any previous action
          - switch.turn_off: close_switch
          # Turn the OPEN switch on briefly
          - switch.turn_on: open_switch
          - delay: 0.1s
          - switch.turn_off: open_switch
        close_action:
          - switch.turn_off: open_switch
          - switch.turn_on: close_switch
          - delay: 0.1s
          - switch.turn_off: close_switch
        stop_action:
          - switch.turn_off: close_switch
          - switch.turn_off: open_switch
        optimistic: true



Single Relay Garage Door
------------------------

The following is a possible configuration file for garage doors that are controlled by one relay:
The relay toggles the garage door action; either opening, closing, or stopping. A
:doc:`binary-sensor </esphomeyaml/components/binary_sensor/index>` (such as a magnetic reed switch)
provides the feedback for the garage door state.
Note: Although the default value for ''optimistic:'' is false, it is left explicitly defined here
only to highlight it as a difference from the dual-relay example.



.. code-block:: yaml

    switch:
      - platform: gpio
        pin: D2
        id: garage_door_toggle_relay

    binary_sensor:
      - platform: gpio
        pin: D5
        id: garage_door_state

    cover:
      - platform: template
        name: "Garage"
        open_action:
          # Toggle the door switch on briefly
          - switch.turn_on: garage_door_toggle_relay
          - delay: 0.1s
          - switch.turn_off: garage_door_toggle_relay
        close_action:
          # Toggle the door switch on briefly
          - switch.turn_on: garage_door_toggle_relay
          - delay: 0.1s
          - switch.turn_off: garage_door_toggle_relay
        stop_action:
          # Toggle the door switch on briefly
          - switch.turn_on: garage_door_toggle_relay
          - delay: 0.1s
          - switch.turn_off: garage_door_toggle_relay
        # The lambda varies the state of the cover based on the binary sensor
        lambda: 'return (id(garage_door_state).state) ? cover::COVER_OPEN : cover::COVER_CLOSED;'
        optimistic: false



See Also
--------

- :doc:`/esphomeyaml/guides/automations`
- :doc:`/esphomeyaml/components/binary_sensor/index`
- :doc:`/esphomeyaml/components/cover/template`
- `Edit this page on GitHub <https://github.com/OttoWinter/esphomedocs/blob/current/esphomeyaml/cookbook/garage-door.rst>`__

.. disqus::
