---
template: main.html
title: "Functional Cards Series 2: Person Card"
description: "Example of functional card, Person Card"
hideno:
  toc
tags:
  - Example
  - Functional Card - Series 2
  - Person Card
---
<!-- GT/GL -->
!!! warning "Series 2 will be released in 2025!"

##:sak-sak-logo: Visualization

![Swiss Army Knife Functional Card Person D06 Light Home](../../assets/screenshots/sak-functional-card-s2-person-theme-d06-light-home.png#only-light){width="600"}
![Swiss Army Knife Functional Card Person D06 Light Home](../../assets/screenshots/sak-functional-card-s2-person-theme-d06-dark-home.png#only-dark){width="600"}

This card uses the [Material 3 theme D06, TealBlue][ham3-d06-url]

!!! info "This card shows you some possibilities to apply JavaScript to the animations section"
    The card seems like a standard, simple card, but isn't due to some Home Assistant functionalities and the possibilities of using either an icon or an entity picture for the person.

    The [use of JavaScript][Swiss Army Knife Javascript Snippets] to fetch the zone's Icon, to do some state dependent color changes and taking care of the "use entity_picture yes/no" setting are nice examples of the possibilities that JavaScript adds to tools. It are just a few lines, but very powerful!
    
    It also takes care of fetching the icon of additional zones (ie not the home zone).
    
| Description| Aspect Ratio| Target Size |
|-|-|-|
| A card that shows in which zone a person is, or if in no known zone as away / not home.| 1/1 | Grid with 2 columns |

##:sak-sak-logo: Interaction

| Part | Description|
|-|-|
| Card | All tools connected to an entity do show by default the "more-info" dialog once clicked |

##:sak-sak-logo: Usage
[:octicons-tag-24: 4.0.1][github-releases]

Using the default mode: an icon for the person entity:
```yaml linenums="1"
- type: 'custom:swiss-army-knife-card'
  entities:
    - entity: person.marco
      name: 'Person'
      icon: mdi:face-man
    - entity: person.marco
      name: 'Person'
      secondary_info: last_changed
      format: time-24h_date-short
  layout:
    template:
      name: sak_layout_fce2_person
      variables:
        - sak_layout_person_zone_entities:
            - zone.outside
            - zone.marco_work
```
Using an `entity_picture` for the person entity. Picture can be defined here, or (default) the `entity_picture` defined for the person is used:
```yaml linenums="1"
- type: 'custom:swiss-army-knife-card'
  entities:
    - entity: person.marco
      name: 'Tha Washer'
      icon: mdi:face-man
      entity_picture: "/local/images/clip-test.jpg"
    - entity: person.marco
      name: 'Tha Washer'
      secondary_info: last_changed
      format: time-24h_date-short

  layout:
    template:
      name: sak_layout_fce2_person
      variables:
        - sak_layout_person_use_entity_picture: true
        - sak_layout_person_zone_entities:
            - zone.outside
            - zone.marco_work
```

| Data | Default| Required | Description |
|-|-|-|-|
| entities |  | :material-check: | The person entity |
| sak_layout_person_zone_entities | | :material-check: | The list of zone entities for this person to be displayed. There is no limit, it is really a list which is used when the person is not at home! |
| sak_layout_person_use_entity_picture | false | :material-close: | If set to true, an entity picture is displayed instead of the persons icon. Default the picture configured for the person is used, but can be overridden by specifying an entity_picture in the entity configuration in the view |

##:sak-sak-logo: YAML Template Definition
[:octicons-tag-24: 4.0.1][github-releases]
??? Info "Full definition of layout template"
    ```yaml linenums="1"
    sak_layout_fce2_person:
      template:
        type: layout
        defaults: 
          - sak_layout_person_use_entity_picture: false
          - sak_layout_person_icon_color: var(--theme-sys-color-primary)
          - sak_layout_person_icon_color_on: var(--theme-sys-color-error)
          - sak_layout_person_history_disabled: false
          - sak_layout_person_history_color_on: var(--theme-sys-color-error)

      layout:
        aspectratio: 1/1
        toolsets:
          # ================================================================
          - toolset: background-icon
            position:
              cx: 50
              cy: 50
            tools:
              # ------------------------------------------------------------
              - type: 'usersvg'
                position:
                  cx: 50
                  cy: 30
                  height: 100
                  width: 100
                clip_path:
                  position:
                    cx: 50
                    cy: 50
                    height: 60
                    width: 100
                    radius:
                      all: 0
                style: 'images'
                images:
                  - default: "/local/images/backgrounds/map-background.jpg"
                styles:
                  usersvg:
                    opacity: 0.8
                  mask:
                    fill: url(#sak-sparkline-area-mask-tb-0)
          # ==============================================================================
          - toolset: circle-with-icon
            position:
              cx: 20
              cy: 20
            tools:
              # ------------------------------------------------------------
              - type: circle
                position:
                  cx: 50
                  cy: 50
                  radius: 12.5
                entity_index: 0
                animations:
                  - state: 'on'
                    styles:
                      circle:
                        stroke: var(--theme-sys-color-error)
                  - state: 'off'
                    styles:
                      circle:
                        # stroke: var(--theme-sys-elevation-surface-neutral10)
                        stroke: '[[sak_layout_person_icon_color]]'
                styles:
                  circle:
                    stroke-width: 2em
                    # stroke: var(--theme-sys-elevation-surface-neutral10)
                    stroke: '[[sak_layout_person_icon_color]]'
                    fill: var(--primary-background-color)

              # ------------------------------------------------------------
              - type: icon
                position:
                  cx: 50
                  cy: 50
                  align: center
                  icon_size: 20
                entity_index: 0
                variables:
                  sak_layout_person_use_entity_picture: '[[sak_layout_person_use_entity_picture]]'
                  sak_layout_person_icon_color:  '[[sak_layout_person_icon_color]]'
                animations:
                    # Return current state, so always a match!
                  - state: '[[[ return state; ]]]'
                    styles:
                      icon:
                        # Set fill depending on being at home!
                        fill: >
                          [[[ if (['home', 'not_home'].includes(state)) return tool_config.variables.sak_layout_person_icon_color;
                              return 'var(--theme-sys-color-secondary)';
                          ]]]
                        # Hide icon if using entity_picture!
                        display: >
                          [[[ if (tool_config.variables.sak_layout_person_use_entity_picture) return 'none';
                              return 'initial';
                          ]]]
                styles:
                  icon:
                    fill: var(--theme-sys-color-secondary)
                    opacity: 0.9

              # ------------------------------------------------------------
              - type: usersvg
                position:
                  cx: 50
                  cy: 50
                  height: 20
                  width: 20
                entity_index: 0
                variables:
                  sak_layout_person_use_entity_picture: '[[sak_layout_person_use_entity_picture]]'
                clip_path:
                  position:
                    cx: 50
                    cy: 50
                    height: 20            # Slightly crop image (from 45->40)
                    width: 20
                    radius:
                      all: 10             # Radius 20 results in full circle
                style: 'images'
                images:                   # Fetch entity_picture from config or entity itself
                  - default: >
                      [[[
                        if (tool_config.variables.sak_layout_person_use_entity_picture) {
                          return (entity_config?.entity_picture ||
                                entity.attributes?.entity_picture || 'none');
                        } else {
                          return 'none';
                        }
                      ]]]
                animations:
                    # Return current state, so always a match!
                  - state: '[[[ return state; ]]]'
                    image: default
                    styles:
                      icon:
                        # Hide usersvg tool if using icon!
                        display: >
                          [[[ if (!tool_config.variables.sak_layout_person_use_entity_picture) return 'none';
                              return 'initial';
                          ]]]
                
              # - template:
              #     name: tool_valmardav_icon

          # ==============================================================================
          - toolset: name
            position:
              cx: 7.5
              cy: 45
            tools:
              # ------------------------------------------------------------
              - type: name
                position:
                  cx: 50
                  cy: 50
                entity_index: 0
                show:
                  ellipsis: 12
                styles: 
                  name: 
                    text-anchor: start
                    font-size: 12em
                    font-weight: 700
                    opacity: 1
              # ------------------------------------------------------------
              - type: state
                position:
                  cx: 50
                  cy: 62.5
                entity_index: 0
                show:
                  uom: none
                styles:
                  state:
                    text-anchor: start
                    font-size: 10em
                    font-weight: 400
                    opacity: 0.6

          # ==============================================================================
          - toolset: alert-time-boxes
            position:
              cx: 50
              cy: 75
            tools:
              # ------------------------------------------------------------
              - type: rectex
                position:
                  cx: 20
                  cy: 50
                  width: 25
                  height: 18
                  radius:
                    left: 5
                    right: 0
                entity_index: 0
                animations:
                  - state: 'home'
                    operator: '!='
                    styles:
                      rectex:
                        fill: var(--theme-sys-color-error)
                  - state: 'home'
                    styles:
                      rectex:
                        fill: var(--theme-sys-elevation-surface-neutral10)
                styles:
                  rectex:
                    fill: var(--theme-sys-elevation-surface-neutral10)

              # ------------------------------------------------------------
              - type: icon
                position:
                  cx: 20
                  cy: 50
                  align: center
                  icon_size: 12.5
                entity_index: 0
                variables:
                  zone_ids: '[[sak_layout_person_zone_entities]]'
                animations:
                    # Return current state, so always a match!
                  - state: '[[[ return state; ]]]'
                    # Fetch icon of zone. If no match on zone, the state 'not_home' is given.
                    # Check that state, and return the 'not home' icon in that case!
                    icon: >
                      [[[ if (state == 'not_home') return 'mdi:home-off-outline';
                          if (state == 'home') return states['zone.home'].attributes.icon;
                          // For not home, we get the friendly name as input. Must find that one to retrieve
                          // the zone's id...
                          
                          for (var i=0; i<tool_config.variables.zone_ids.length; i++) {
                            var zone = states[tool_config.variables.zone_ids[i]];
                            if (zone && zone.attributes.friendly_name == state) {
                              return states[zone.entity_id].attributes.icon;
                            }
                          }
                          return 'mdi:map-marker-question';
                      ]]]
                    styles:
                      icon:
                        fill: var(--primary-background-color)
                styles:
                  icon:
                    fill: var(--primary-background-color)
              # ------------------------------------------------------------
              - type: rectex
                position:
                  cx: 64
                  cy: 50
                  width: 57
                  height: 18
                  radius:
                    left: 0
                    right: 5
                entity_index: 0
                animations:
                  - state: 'home'
                    styles:
                      rectex:
                        fill: var(--theme-sys-elevation-surface-neutral4)
                  - state: 'home'
                    operator: '!='
                    styles:
                      rectex:
                        fill: var(--theme-sys-elevation-surface-error4)
                styles:
                  rectex:
                    fill: var(--theme-sys-color-on-error)

              # ------------------------------------------------------------
              - type: state
                position:
                  cx: 64 #92
                  cy: 50
                entity_indexes:
                  - entity_index: 1
                  - entity_index: 0
                animations:
                  - state: 'home'
                    entity_index: 0
                    styles:
                      state:
                        fill: var(--theme-sys-elevation-surface-neutral10)
                  - state: 'home'
                    entity_index: 0
                    operator: '!='
                    styles:
                      state:
                        fill: var(--theme-sys-color-error)
                styles: 
                  state:
                    # fill: var(--theme-sys-color-error-container)
                    fill: var(--theme-sys-color-error)
                    font-size: 12em
                    text-anchor: middle #end
                    # alignment-baseline: middle
                    font-weight: 700
                  uom:
                    # fill: var(--theme-sys-color-error-container)
                    fill: var(--theme-sys-color-error)
                    alignment-baseline: hanging
                    font-weight: 600

          # ==============================================================================
          - toolset: alert-history
            position:
              cx: 50
              cy: 92.5
            tools:
              # ------------------------------------------------------------
              - type: sparkline
                # When disabled, SAK will not use this tool
                disabled: '[[sak_layout_person_history_disabled]]'
                entity_index: 0
                position:
                  cx: 50
                  cy: 50
                  width: 85
                  height: 3
                  margin: 0
                period:
                  calendar:
                    period: day
                    offset: 0
                    duration:
                      hour: 24
                    bins:
                      per_hour: 1
                sparkline:
                  show:
                    chart_type: barcode
                    # chart_variant: stalagmites
                  state_map:
                    template:
                      name: sak_statemap_person
                  # State value settings
                  # - set agg to max to see the binary changes
                  # - set lower_bound to -1 to offset 'off' state
                  #   the barcode will start wider now, instead of at minimum width
                  # - set upper_bound to 1 ('on') to fix upper scale
                  state_values:
                    aggregate_func: max
                    lower_bound: -1
                    upper_bound: 1
                  barcode:
                    column_spacing: 3
                    line_width: 0.1
                  colorstops_transition: hard
                  colorstops:
                    colors:
                      - value: 0
                        color: var(--theme-sys-elevation-surface-neutral10)
                      - value: 1
                        color: '[[sak_layout_person_history_color_on]]'
                  styles: 
                    tool: 
                      opacity: 1
                    barcode_graph:
                      rx: 5
                      ry: 5                
    ```

<!-- Image references -->

<!--- Internal References... --->
[Swiss Army Knife Tutorial 02]: ../../tutorials/10-step-tutorial-02-intro.md
[Swiss Army Knife Javascript Snippets]: ../../basics/templates/javascript-snippets.md

<!--- External References... --->
[ham3-d06-url]: https://material3-themes-manual.amoebelabs.com/examples/material3-example-theme-d06-tealblue/
[github-releases]: https://github.com/amoebelabs/swiss-army-knife-card/releases/
