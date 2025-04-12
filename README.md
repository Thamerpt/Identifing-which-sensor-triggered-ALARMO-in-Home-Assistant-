Implementation Guide for Alarmo Sensor Alert Dashboard CardThis guide will help you implement a dashboard card that displays which Aeotec multi-contact sensor triggered your ALARMO integration in Home Assistant.Prerequisites•Home Assistant with ALARMO integration set up•Aeotec multi-contact sensors configured and working with ALARMO•Access to your Home Assistant dashboard configurationTesting NotesThe provided YAML configurations assume your ALARMO integration entity is named alarm_control_panel.alarmo. If your entity has a different name, you'll need to replace this in the configurations.Implementation OptionsOption 1: Basic Markdown CardThis card shows the triggered sensor when the alarm is triggered, and shows the current alarm state otherwise.Copytype: markdown
title: Alarm Sensor Alert
content: >
  {% if is_state('alarm_control_panel.alarmo', 'triggered') %}
    {% set open_sensors = state_attr('alarm_control_panel.alarmo', 'open_sensors') %}
    {% if open_sensors %}
      {% set sensor_entity_id = open_sensors.keys() | list | first %}
      {% set friendly_name = state_attr(sensor_entity_id, 'friendly_name') or sensor_entity_id.split('.')[1] | replace('_', ' ') | title %}
      ## ⚠️ Alert! Security Breach Detected
      
      It seems that someone is tampering with your **{{ friendly_name }}** Sensor
    {% else %}
      ## ⚠️ Alert! Alarm Triggered
      
      The alarm has been triggered, but the specific sensor could not be identified.
    {% endif %}
  {% else %}
    ## 🔒 Security System Status
    
    The security system is currently {{ states('alarm_control_panel.alarmo') }}.
    
    No tampering detected.
  {% endif %}Option 2: Conditional Card (Only Shows When Triggered)This card only appears when the alarm is triggered, making it ideal for alert dashboards.Copytype: conditional
conditions:
  - entity: alarm_control_panel.alarmo
    state: triggered
card:
  type: markdown
  title: Alarm Sensor Alert
  content: >
    {% set open_sensors = state_attr('alarm_control_panel.alarmo', 'open_sensors') %}
    {% if open_sensors %}
      {% set sensor_entity_id = open_sensors.keys() | list | first %}
      {% set friendly_name = state_attr(sensor_entity_id, 'friendly_name') or sensor_entity_id.split('.')[1] | replace('_', ' ') | title %}
      ## ⚠️ Alert! Security Breach Detected
      
      It seems that someone is tampering with your **{{ friendly_name }}** Sensor
    {% else %}
      ## ⚠️ Alert! Alarm Triggered
      
      The alarm has been triggered, but the specific sensor could not be identified.
    {% endif %}Option 3: Complete Solution with DetailsThis card combines the alert message with additional alarm system details.Copytype: vertical-stack
cards:
  - type: markdown
    title: Alarm Sensor Alert
    content: >
      {% if is_state('alarm_control_panel.alarmo', 'triggered') %}
        {% set open_sensors = state_attr('alarm_control_panel.alarmo', 'open_sensors') %}
        {% if open_sensors %}
          {% set sensor_entity_id = open_sensors.keys() | list | first %}
          {% set friendly_name = state_attr(sensor_entity_id, 'friendly_name') or sensor_entity_id.split('.')[1] | replace('_', ' ') | title %}
          ## ⚠️ Alert! Security Breach Detected
          
          It seems that someone is tampering with your **{{ friendly_name }}** Sensor
        {% else %}
          ## ⚠️ Alert! Alarm Triggered
          
          The alarm has been triggered, but the specific sensor could not be identified.
        {% endif %}
      {% else %}
        ## 🔒 Security System Status
        
        The security system is currently {{ states('alarm_control_panel.alarmo') }}.
        
        No tampering detected.
      {% endif %}
  
  - type: entities
    title: Alarm System Details
    show_header_toggle: false
    entities:
      - entity: alarm_control_panel.alarmo
        name: Alarm Status
      - type: attribute
        entity: alarm_control_panel.alarmo
        attribute: open_sensors
        name: Triggered SensorsHow It Works1.The card checks if the ALARMO integration is in a "triggered" state2.If triggered, it accesses the open_sensors attribute which contains information about which sensor triggered the alarm3.It extracts the entity ID of the first triggered sensor4.It attempts to get the friendly name of the sensor, or formats the entity ID if no friendly name is available5.It displays a message indicating which sensor was triggeredTroubleshooting•If the card doesn't show the correct sensor name, check if your ALARMO entity is named correctly in the configuration•Verify that your Aeotec sensors are properly integrated with ALARMO•Use the Home Assistant Developer Tools > States to check the attributes of your ALARMO entity when triggered•Ensure
