IOS Config Replace
=======

This script includes functions to template IOS device configs and execute a config replace using jinja2, nornir, nornsible, and napalm.
- Jinja2 is used as the templating language
- Nornir is used for inventory management and to paralell execution.
- Nornsible is utilized to provide ansible-like host/group limits and tagging functionality so that the script can be limited to only those hosts/groups against which it should executed at time of execution. 
- Napalm is used for config replace

# How it works

This script utilizes a few environment variables and nornir data elements.

- The 'NORNIR_CONFIG_FILE' environment variable is used to set the nornir config file. The full path of this file needs be specified. e.g. '/home/cool_user/nornir/inventory/config.yaml'
- The 'IOS_TEMPLATES_DIR' environment variable should link to the directory in which IOS templates exist
  - IOS templates are accessed in a specific directory structuire - '$role/$model/'.
  - The 'role' attribute should be specified as a nornir data element (e.g. 'role: some_cool_role' as a data element in nornir inventory)
  - The 'model' attribute should, likewise, be specified as a nornir data element.
- The 'IOS_FILLED_TEMPLATES_DIR' variable denotes the location to which filled templates should be written after constructed, and likewise the directory from which configs should be scp'd to the device

# Banner Replace

Cisco IOS uses escape and carriage return characters in its banner config. Jinja2 doesn't parse a cisco config file correctly as it doesn't read from disk using utf-8. Because of this, the escape and carriage return characters are not preserved after the template has been rendered and Cisco IOS sees the banner as a diff when a config replace operation is executed. 

To make it so the banner isn't always flagged as a diff when a config replace is executed, I included some code which will read the "incorrect_banner.txt" and "correct_banner.txt" files from memory and replace the incorrect banner (without escape and carraige return characters) with the correct one (with escape and carriage return characters) before writing the file to disk using the correct format. To utilize this function, one would simply need take the banner config from their rendered template and copy it to the "incorrect_banner.txt" file, then generate a banner with the escape and carriage return characters and add it to the "correct_banner.txt" file.