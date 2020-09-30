# scheduler-card <!-- omit in TOC -->
[![hacs_badge](https://img.shields.io/badge/HACS-Custom-orange.svg)](https://github.com/custom-components/hacs)  

- [Introduction](#introduction)
- [Installation](#installation)
- [Updating](#updating)
- [Usage](#usage)
  - [Creating a schedule](#creating-a-schedule)
    - [Choosing an entity and action](#choosing-an-entity-and-action)
    - [Choosing the days](#choosing-the-days)
    - [Choosing the time](#choosing-the-time)
- [Configuration](#configuration)
  - [Introduction](#introduction-1)
  - [Overview](#overview)
  - [Standard configuration](#standard-configuration)
  - [Adding entities](#adding-entities)
    - [Include](#include)
    - [Exclude](#exclude)
  - [Groups](#groups)
  - [Schedule discovery](#schedule-discovery)
  - [Customization](#customization)
    - [Actions](#actions)
    - [Numeric action variable](#numeric-action-variable)
    - [List action variable](#list-action-variable)
- [Translations](#translations)
- [FAQ](#faq)
  - [*Can I make a schedule that checks a condition before executing the action?*](#can-i-make-a-schedule-that-checks-a-condition-before-executing-the-action)
  - [*How do I check which version I am using?*](#how-do-i-check-which-version-i-am-using)
  - [*How do I fix error 'Failed to call service scheduler/add. Service not found.' ?*](#how-do-i-fix-error-failed-to-call-service-scheduleradd-service-not-found-)
  - [*Why is there no such scheduler in HA yet?*](#why-is-there-no-such-scheduler-in-ha-yet)
- [Troubleshooting](#troubleshooting)
- [Say thank you](#say-thank-you)

## Introduction
This is a Lovelace card for Home Assistant that can be used to create a time schedule for your smart devices.
You can create new rules, modify existing rules and temporarily disable rules.

The card works on top of the [scheduler custom component](https://github.com/nielsfaber/scheduler-component). You *will* need it this as well.

See it in action:

![alt text](https://github.com/nielsfaber/scheduler-card/blob/master/screenshots/Demonstration.gif?raw=true "demonstration video")

## Installation
<details>
<summary>click to show installation instructions </summary>

HACS installation:
1. Add `https://github.com/nielsfaber/scheduler-card` as a custom frontend repository.
2. Click on "Install" under the new card that just popped up.

Manual installation

1. Download the latest release of `scheduler-card.js` [here](https://github.com/nielsfaber/scheduler-card/releases) and place it into `www/scheduler-card`.

2. Use the GUI to add `/local/scheduler-card/scheduler-card.js?v=0`, or add a reference to the card in the resources section of `configuration.yaml`:

```yaml
resources:
  - url: /local/scheduler-card/scheduler-card.js?v=0
    type: module
```

 3. Add the card in the view where you want it to be shown:

  ```yaml
 type: custom:scheduler-card
 domains:
   ...
 entities:
   ...
 groups:
   ...
  ```

</details>

## Updating

<details>
<summary>click to show updating instructions </summary>

Updating via HACS:
HACS should auto-remind you in the HACS tab when an update is available.

Updating manually:

Use `git pull` for manual installation updates.

Since most browsers will cache the Lovelace card code, you can force a refresh of  the browser by editing the entry in the `resources:` section in  `ui-lovelace.yaml`, by updating the version to `?v=(n+1)` (where `n` the current value).


</details>

---

## Usage

:construction: WIP More usage instructions should follow soon.



### Creating a schedule
Click the button 'add item' in the bottom of the card, to start creating a schedule.

#### Choosing an entity and action
The card scans the entities in your HA configuration and suitable candidates should automatically show up in this view.

__Groups__
Since HA may contain many entities, the card divides the entities into different groups.
Clicking a group automatically will show the entities contained in the group.

The groups that are displayed are depending on your HA configuration.
Typically the groups are based on the _domain_ of your entities.
If you want to make changes to the groups, you can do this by defining [groups](#groups) configuration.

__Entities__
The entities that you can to control with the scheduler show up here.
Clicking a entity automatically will show the actions that you can program for this entity.

Typically an entity is a device in your house, but you can also control an `automation`, `script`, `input_boolean`, etc. If you are missing one or more entities, you can add these yourself using the [entities](#entities) or [domains](#domains) configuration.

__Actions__
The actions that you can perform for the selected entity show up here.
 Typically an action is to either 'turn on' or 'turn off' a device. But some entities have more capabilities. If you are missing an action, you can add it yourself to either entity or the complete domain (group) using the [actions](#actions) configuration.


 Actions can contain a variable setting (e.g. turn on a lamp at specific brightness, or change the setpoint for a thermostat).
These can be defined in the next page.

<img src="https://github.com/nielsfaber/scheduler-card/blob/master/screenshots/instructions_select_entity.png?raw=true" width="500">

#### Choosing the days
After clicking the 'next' button, a new view appears.
This view is used for choosing _when_ the schedule should be active.

Choose the days of the week for which the schedule should be active.


__Every day__
 the default option. The schedule will perform the action every day at the specified time.

__Weekdays__
perform action only on Monday thru Friday. Typically weekdays are the same as working days, but it may depend on your country. Holidays are not taken into account as of yet.

__Custom__
choose your own days. A list with all days of the week appears. You can select one or more days by clicking them.

:construction: WIP rename _weekdays_ to _workdays_, and take into account holidays and the actual workdays in your country using the [workday sensor](https://www.home-assistant.io/integrations/workday/)

:construction: WIP add _weekends_ and make it the opposite of _workdays_

:construction: WIP add option to choose the first day of the week


#### Choosing the time

The time at which you want schedule to be activated can be set using the time picker.



The time picker shows the current time setting, and features arrow buttons to increase or decrease the hour and minutes. Note that you can infinitely loop through time.
The step size for minutes is 10 minutes by default, but can be configured to your preference.

If you have the AM/PM option enabled, 12-hour format will be used. You can click on the button to switch between AM and PM.

__Sunrise / sunset mode__

If you have the [sun](https://www.home-assistant.io/integrations/sun/) integration in HA, a button with sun/moon icon shows up on the right. This is the _mode button_, which allows you to switch from a fixed time, to time relative to sunrise or sunset.  
The card allows you to choose a time that is 2 hours around sunrise or sunset. The button will be disabled if the current time is not in this range.

In sunrise/sunset mode, the time picker will show the offset relative to sunrise/sunset. The time offset is automatically calculated from fixed time.
The _sunrise/sunrise button_ will show a sun icon when offset is relative to sunrise, or a moon icon when offset is relative sunset.
The _before/after button_ indicates whether the offset is applied in positive direction (so triggers after sunrise/sunset), or in negative direction (before sunrise/sunset).
Also here, buttons can be clicked to toggle.

:warning: **Important**: _What you see is what you get_ here. If you want to store time as relative to sunrise or sunset, make sure that you have this _mode_ activated when you click the save button.

<img src="https://github.com/nielsfaber/scheduler-card/blob/master/screenshots/instructions_timepicker.png?raw=true" width="400">

---

## Configuration


### Introduction
The configuration of the card is in YAML.
Currently there is no UI editor provided (this may change in the future).

_Configuration is not necessary_, it is only used for customization.

The card includes a _standard configuration_.
It is intended to make setting up the card easy.
The standard configuration consists of the following:
* Discovery of devices (entities) of various types in your HA config and making them available for creating schedules
* Defining actions per entity based on their capabilities
* Icons for groups, entities and actions

:warning: **Tip**: If you want to define your own configuration, provide `standard_configuration:false` to disable it completely.


### Overview

| Name                     | Type           | Default      | Available from | Description                                                                                                                                                                                                                                |
| ------------------------ | -------------- | ------------ | -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `type`                   | string         | **Required** | -              | `custom:scheduler-card`                                                                                                                                                                                                                    |
| `standard_configuration` | boolean        | *true*       | v1.2.0         | Use the [standard configuration](#about-the-standard-configuration) as a base configuration.                                                                                                                                               |
| `discover_existing`      | boolean        | *true*       | v1.2.0         | Show previously created schedules in the card, also if the related entities are not included in the configuration.<br>Set to `false` if you have multiple scheduler-cards.<br>See [schedule discovery](#schedule-discovery) for more info. |
| `include`                | list           | none         | v1.6.0         | List of filters to determine which HA entities are available for creating schedules.<br> See [include](#include) for more info.                                                                                                            |
| `exclude`                | list           | none         | v1.6.0         | List of filters to determine which HA entities are **not** available for creating schedules.<br> See [exclude](#exclude) for more info.                                                                                                    |
| `groups`                 | list           | none         | v1.0.0         | Organize the entities menu. <br>See [groups](#groups) for more info.                                                                                                                                                                       |
| `customize`              | dictionary     | none         | v1.6.0         | Customize the available actions or visualization of entities.                                                                                                                                                                              |
| `title`                  | boolean/string | *true*       | v1.2.8         | Provide a text to replace the title of the card.<br> Set to `false` to hide the title.                                                                                                                                                     |
| `am_pm`                  | boolean        | *false*      | v1.3.0         | Use AM/PM time notation (instead of 24 hours notation)                                                                                                                                                                                     |
| `time_step`              | number         | 10           | v1.3.0         | Set the time step (in minutes) for the time picker                                                                                                                                                                                         |


### Standard configuration

The card includes a _standard configuration_.
It is intended to make setting up the card easy.
The standard configuration has actions defined for most types of entities in Home assistant.

When including an entity, the standard configuration will automatically detect which actions are supported by it, and will make these available.

Also it has icons for most entity types and actions.

If you would rather set up your own configuration of entities and actions, you can provide `standard_configuration:false` to disable it.


:warning: **Note**: Not all entity types are currently supported by the standard configuration. If you are missing something, you can make a [feature request](https://github.com/nielsfaber/scheduler-card/issues/new) for it.

### Adding entities

#### Include

The `include` configuration allows you to pick entities from your HA config that you can use for creating schedules.

You can either provide the full `entity_id` of the entities, or only the domain.

The list supports wildcards (*) as well. It is recommended to use quotes ("") around your input, else it may be wrongly interpretated.

<u>Example:</u>

```yaml
include:
  - climate.my_thermostat # include an individual entity
  - light # include all light entities
  - "*garden*" # include all entities containing the word 'garden'
  ...
```
#### Exclude

The `exclude` configuration allows you to remove entities from your HA config from appearing in the scheduler.

The `exclude` list works on top of the `include` list, so it behaves like *'ADD (includes) EXCEPT (excludes)'*.

Like with `include`, you can either provide the full `entity_id` of the entities, or the domain, or use wildcards.


```yaml
include:
  - light # include all light entities
exclude:
  - light.my_light_that_i_never_use # exclude
```


### Groups
The `groups` configuration provides the capability of organizing the entities.
To be clear, they have nothing to do with the [group](https://www.home-assistant.io/integrations/group/) integration in Home Assistant.

By default, entities will be grouped based on their type (domain). 
Entities that are assigned to your own defined group will not be grouped by type.

| Name    | Type   | Default            | Description                                                                                                                                                                          |
| ------- | ------ | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| name    | string | (same as group_id) | Displayed name for group                                                                                                                                                             |
| icon    | string | none               | Displayed icon for group                                                                                                                                                             |
| include | list   | none               | List of filters to determine which of the entities belong in this group.<br>This has the same functionality as the [include](#include) filter for defining the entities in the card. |
| exclude | list   | none               | List of filters to determine which of the do not belong in this group.<br>This has the same functionality as the [exclude](#exclude) filter for defining the entities in the card.   |  |

<u>Example:</u>

*Place all `light` entities in group labelled "lighting"*
```yaml
groups:
  - name: "Lighting"
    icon: ceiling-light
    include:
      - light
```
Result:

![groups example](https://github.com/nielsfaber/scheduler-card/blob/master/screenshots/groups_example.png?raw=true)

### Schedule discovery

The card checks for the created schedules in your HA config and show them in the overview page.

The schedule discovery is a feature that will ensure that **all** your schedules will be there.

What is the benefit of this feature?

It could occur that you make changes in the configuration, resulting in previously created schedules to become hidden.
For example, you made a schedule for controlling a `fan`.
The day after you decide to remove the `fan` domain from the card. But you forgot to delete the previously created schedule.

Without the discovery, you now have a schedule that is hidden from the card, but will keep running. You cannot remove or change it anymore (unless via the HA configuration panel).

Discovery makes sure that you will always find it back in the card.
The feature can be turned on/off through the `discover_existing` option.

For your protection, it is enabled by default.

:warning: **Tip**: You should set `discover_existing:false` if you want to use multiple cards. Else you will see each created schedule in every card.


### Customization
With the `customize` configuration you can specify configuration for specific HA entities.

:warning: **Tip**: You can use entities `configuration` in combination with the standard configuration. The configurations will be merged.

| Name    | Type   | Default               | Description                                                                                                 |
| ------- | ------ | --------------------- | ----------------------------------------------------------------------------------------------------------- |
| entity  | key    | **Required**          | Entity id (or filter).<br> Filter works the same as `include` so you can also use it for multiple entities. |
| actions | list   | none                  | See [actions](#actions)                                                                                     |
| name    | string | (take from HA config) | Displayed name for entity                                                                                   |
| icon    | string | (take from HA config) | Displayed icon for entity                                                                                   |

#### Actions
An action defines **what** needs to be done when a schedule timer expires.

An action is similar to a [service call](https://www.home-assistant.io/docs/scripts/service-calls/) in HA. It requires a `service` with optionally additional parameters given by `service_data`.

Actions are linked to their entities, so the entity ID is sent together with the service call, it is not needed to add it to the `service_data`).

| Name         | Type   | Default           | Description                                             |
| ------------ | ------ | ----------------- | ------------------------------------------------------- |
| service      | string | **Required**      | Service to be executed                                  |
| service_data | map    | none              | Additional parameters to use for the service call       |
| variable     | map    | none              | Add a variable. See [action variable](#action-variable) |
| name         | string | (same as service) | Displayed name for action                               |
| icon         | string | "flash"           | Displayed icon for action                               |

:warning: **Note**: Templates (jinja code) are not supported at this point.

<u>Example:</u>

*Adding an action to turn on a light with 40% brightness*
  ```yaml
entities:
    light.my_lamp:
      name: "Dining light"
      icon: ceiling-light
      actions:
        - service: turn_on
          service_data:
            brightness: 100 # note that brightness is from 0-255 so 100 = 40%
          name: "Turn on at 40%"
          icon: lightbulb-on-outline
  ```
Result:

![customize example](https://github.com/nielsfaber/scheduler-card/blob/master/screenshots/entities_example.png?raw=true)


#### Numeric action variable

Some devices allow to operate on a variable working point. For example lights can be dimmed with a `brightness`, fans can spin at a `speed` etc.

By providing an action variable, the card allows you to choose the setting you want to apply with the action.

| Name                | Type        | Default       | Description                                                                                                                                                                                           |
| ------------------- | ----------- | ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| field               | string      | **Required**  | field name in the `service_data` that is represented by this variable                                                                                                                                 |
| name                | string      | same as field | Name under which the variable is visible in the card                                                                                                                                                  |
| unit                | string      | " "           | Displayed unit                                                                                                                                                                                        |
| min                 | number      | 0             | Minimum value that can be set. If not provided, it will be read from the entity attributes.                                                                                                           |
| max                 | number      | 255           | Maximum value that can be set. If not provided, it will be read from the entity attributes.                                                                                                           |
| step                | number      | 1             | Step size                                                                                                                                                                                             |
| optional            | boolean     | false         | Setting the variable is optional, the action can also be executed without this variable. <br>If `optional:true` is provided, a checkbox will be shown that needs to be selected to apply the variable |
| ~~show_percentage~~ | ~~boolean~~ | ~~false~~     | This option is removed in v1.5.0. Use '%' as unit instead.                                                                                                                                            |

**Example**

The Xiaomi Air Purifier can be controlled using the [Xiaomi Miio](https://www.home-assistant.io/integrations/xiaomi_miio/#service-xiaomi_miiofan_set_favorite_level-air-purifiers-only) integration.
To be able to set the speed of this device in your action, you can use:
```yaml
customize:
  fan.xiaomi_purifier:
    - service: xiaomi_miio.fan_set_favorite_level
      name: "set speed"
      variable:
        field: level
        name: "Speed"
        min: 1
        max: 16
```
You can now select the speed for this action in the schedule editor:

![action variable example](https://github.com/nielsfaber/scheduler-card/blob/master/screenshots/action_variable_example.png?raw=true)

#### List action variable

With some actions, you can provide an option from a limited list of options.
For example, setting the value of an `input_select`, but also the operation mode of a thermostat.

By providing the list variable, the card allows you to choose the option when you set up the action.

| Name                          | Type   | Default       | Description                                                                   |
| ----------------------------- | ------ | ------------- | ----------------------------------------------------------------------------- |
| field                         | string | **Required**  | field name in the `service_data` that is represented by this variable         |
| name                          | string | same as field | Name under which the variable is visible in the card                          |
| options                       | list   | **Required**  | List of options to choose from                                                |
| &nbsp;&nbsp;&nbsp;&nbsp;value | string | **Required**  | Option value (which is passed with together with the field as `service_data`) |
| &nbsp;&nbsp;&nbsp;&nbsp;name  | string | same as value | Name to show for the option                                                   |
| &nbsp;&nbsp;&nbsp;&nbsp;icon  | string | none          | Icon to show with the option                                                  |

**Example**

Setting the operation mode of a thermostat.

Note that this configuration will already be set up when using _standard configuration_.

```yaml
customize:
  climate.my_thermostat:
    name: My thermostat
    icon: thermometer
    actions:
      - service: set_hvac_mode
        name: Set mode
        icon:
        variable:
          field: hvac_mode
          name: Operation mode
          options:
            - value: heat
              icon: fire
            - value: cool
              icon: snowflake
            - value: 'off'
              icon: power
```
Now the list of options become visible when you set up the action:

![action variable example](https://github.com/nielsfaber/scheduler-card/blob/master/screenshots/action_variable_list_example.png?raw=true)


## Translations

The card is available in multiple languages. The card will automatically detect the appropriate translation based on the language setting for your user account in HA.

Currently the following languages are supported:

| Language    | Code(s)    | Available from | Status / Remarks                                                                       |
| ----------- | ---------- | -------------- | -------------------------------------------------------------------------------------- |
| Deutsch     | de         | v1.2.3         |                                                                                        |
| **English** | en         | v1.0.0         | Default language.                                                                      |
| Eesti       | et         | v1.4.0         |                                                                                        |
| Español     | es, es_419 | v1.2.8         |                                                                                        |
| Français    | fr         | v1.2.3         |                                                                                        |
| Magyar      | hu         | v1.3.0         |                                                                                        |
| Polski      | pl         | v1.2.6         |                                                                                        |
| Português   | pt, pt-br  | v1.3.0         | Translation is brazilian Portuguese, improvements may be needed for native Portuguese. |
| Русский     | ru         | v1.2.8         |                                                                                        |
| Nederlands  | nl         | v1.2.2         |                                                                                        |
| Norsk       | no, nb, nn | v1.2.8         |                                                                                        |




The translations are maintained by users.
If you are missing a translation, or a translation needs to be improved, please contribute. Take the [english](https://github.com/nielsfaber/scheduler-card/blob/master/src/localize/languages/en.json) file as a starting point.

---
## FAQ

### *Can I make a schedule that checks a condition before executing the action?*

From within the _scheduler-card_, there is unfortunately no such functionality in place.
There are two alternative ways:

__1. Use an `automation` to control when the schedule is running__

 Create an `automation` that checks for the condition, and based on this, enables or disables the schedule.
 Run service `switch.turn_off` or `switch.turn_on` with the entity_id matching your schedule.

<u>Main disadvantage</u>: the entity_id of a schedule is not easy to read: they are randomly generated, and the entity_id does not tell anything about the related entity.


__2. Use an `script` to check the condition before execution of the action__

 Create a `script` that checks for the condition, and if it passes, executes the action.
 Use the scheduler to execute this `script` (instead of the actual entity you want to control).
Currently this is considered the best option.

<u>Main disadvantage</u>: you will have to add configuration to the card to make the `script` selectable in the scheduler-card. The result will look less pretty than with other entities.

<br>

Example: _Turn on my thermostat at X degrees. but only if my window is closed_

Create the `script` (for setting up scripts see [here](https://www.home-assistant.io/integrations/script/)):
```yaml
my_script:
  fields:
    temperature: {} # allow temperature as variable field
  sequence: # sequence for multiple steps, aborts if a step fails
    - condition: state # check the condition
      entity_id: binary_sensor.my_window
      state: 'off'
    - service: climate.set_temperature # update the temperature
      data_template:
        entity_id: climate.my_thermostat
        temperature: "{{ temperature }}"

```
To add the newly created script to HA you need to restart HA (or run 'reload scripts').

Now make sure to `include` it in the scheduler-card and you should be able to schedule a *run* action for it.

### *How do I check which version I am using?*

In your browser, open the console log. This is usually accessible from the developer tools (MS Edge: F12, Chrome: ctrl+shift+i). There should be a badge titled _"SCHEDULER-CARD"_ with the version number included.

<br>

### *How do I fix error 'Failed to call service scheduler/add. Service not found.' ?*

This error suggests that the [scheduler-component](https://github.com/nielsfaber/scheduler-component) is not running. Please do NOT file an issue in this repo.

<br>

### *Why is there no such scheduler in HA yet?*

Good question, ask the developers!

But let's see if we can get convince them to adopt this scheduler, simply by making it awesome :)

<br>

## Troubleshooting

If you have an issue with this card, please report it [here](https://github.com/nielsfaber/scheduler-card/issues).


---


## Say thank you
If you want to make donation as appreciation of my work, you can buy me a coffee. Thank you!

<a href="https://www.buymeacoffee.com/vrdx7mi" target="_blank"><img src="https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png"></a>
