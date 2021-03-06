# HomeAssistant Configuration

When i started with HomeAssistant (HA) it was difficult to find the best way to use automations.

In the newer versions of HA many things can be configured in the Web Gui.

That i do in the most ways. But for my python_scripts and there automation rules i do it with yaml files. 
That is easier for me and also i can not edit them or delete them in the webgui.
Also the data in there is static, no need to edit this over the time.

All the python script will be copied to the folder "python_scripts" in the config folder.

For the automation and all the other config files i added folders and added them as includes.
Then the content of the folders has no need to include as once - an refresh "..." in "Server Controls" is enough

after changes in python_scripts folder use RELOAD PYTHON SCRIPTS
after changes in autmation folder use RELOAD AUTOMATIONS

i hope that is enough to understand my way for configuring automations

Attention for the panel_custom integration:

my local url is https://raspy5:8123/zwave-graph

first i used https://raspy5.fritz.box:8123/zwave-graph - that was ok for the HA System, but the panel could not be loaded! 
Until now i do not understand why...

```yaml
# configuration.yaml

homeassistant:
  # Customization file
  customize: !include customize.yaml
  packages: !include_dir_named packages

  auth_providers:
    - type: trusted_networks
      trusted_networks:
        - 192.168.178.0/24
    - type: homeassistant

python_script:

frontend:
  themes: !include_dir_merge_named themes

default_config:

discovery:

ios:

config:

lovelace:

climate:  

ffmpeg:
  ffmpeg_bin: /usr/bin/ffmpeg

stream:
      
cloud: !include cloud.yaml

# Text to speech
tts:
  - platform: google_translate
media_player:
  - platform: soundtouch
    host: 192.168.178.161
    port: 8090
    name: "Lydi's Zimmer"

 
# add MDI Icon index to the sidebar
panel_iframe:
  mdiindex:
    title: MDI Icon Index
    icon: mdi:vector-square
    url: https://cdn.materialdesignicons.com/3.2.89/

# add the graphic zwave tree to the sidebar
# add this before: https://github.com/AdamNaj/ZWaveGraphHA

panel_custom:
  - name: zwave-graph-panel
    url_path: zwave-graph
    sidebar_title: Z-Wave Graph
    sidebar_icon: mdi:z-wave
    module_url: /local/zwavegraph3.js
    trust_external_script: True
    config:
      # ranker - pick one: network-simplex, tight-tree, longest-path
      ranker: network-simplex
      # edge_visibility - pick one: relevant, all
      edge_visibility: relevant
      # grouping - pick one: z-wave, ungrouped
      grouping: z-wave   
      
      
zwave:
  # find the correct id in "ls -l /dev/serial/by-id/" 
  # to use the id is much more better than to use the devicename
  # the devicename can change after reboot
  # use the id also in the docker configuration!
  usb_path: /dev/serial/by-id/usb-xxxzzzyyy333445
  
  # create a network key on your ha server
  # cat /dev/urandom | tr -dc '0-9A-F' | fold -w 32 | head -n 1 | sed -e 's/\(..\)/0x\1, /g' -e 's/, $//'
  # this key is not my original :-)
  network_key: "0xAs, 0x2s, 0x2s, 0x39, 0x15, 0x5D, 0x9E, 0x61, 0xA7, 0xFA, 0x67, 0x9E, 0x61, 0x8D, 0xAA, 0x39"
  
  # touch zwave_device_config.yaml in config folder if not present. 
  # the file is mandatory but empty
  device_config: !include zwave_device_config.yaml
  debug: true

# secure your websie with your own certificates
http:
  ssl_certificate: !secret ssl_certificate
  ssl_key: !secret ssl_key  
  ssl_profile: intermediate
  ip_ban_enabled: False 
  login_attempts_threshold: 3
  cors_allowed_origins:
    - https://cast.home-assistant.io  

# single files needed for automations editor in web ui
automation: !include automations.yaml
scene:      !include scenes.yaml
script:     !include scripts.yaml

# includes by folders for manual editing
# the tag name "xxx old" is mandatory to use both, old and new way!

# first you have to create the folders
# the files in the folders need to have the suffix .yaml
# all other files will not be used

automation old: !include_dir_merge_list  include_automations/
device_tracker: !include_dir_merge_list  include_device_trackers/
group:          !include_dir_merge_named include_groups/
input_boolean:  !include_dir_merge_named include_input_booleans/
input_datetime: !include_dir_merge_named include_input_datetime/
input_number:   !include_dir_merge_named include_input_numbers/
scene old:      !include_dir_merge_list  include_scenes/
sensor:         !include_dir_merge_list  include_sensors/
shell_command:  !include_dir_merge_named include_shell_commands/
switch:         !include_dir_merge_list  include_switches/
# noo need anymore to use this. Actual use the onvif integration
#camera:         !include_dir_merge_list  include_cameras/
homekit:        !include_dir_merge_named include_homekit/

logger: !include logger.yaml
```

```yaml
# logger.yaml

default: info
logs:
    homeassistant.components.homekit: debug
    homeassistant.components.zwave: debug
    homeassistant.components.automation: debug
    pychromecast: debug
```

