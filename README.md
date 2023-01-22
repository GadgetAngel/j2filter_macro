# J2FILTER_MACRO (use to be extended_macro) but the authors decided to separate the two projects.

Let's talk about terminology here. You will write python code that can be used in two different ways.

1. As a custom function call inside a Klipper macro
2. As a custom jinja2 filter inside a Klipper macro

I will call the python code you create "the python code or your python code".

If you use your python code as a function call inside a Klipper macro, let's call it a "Klipper custom function".

If you use your python code as a filter inside a Klipper macro, let's call it a "custom jinja2 filter".

---
* When `[j2filter_macro]` section appears in your printer.cfg file the ability to add your own python code to
 a Klipper macro will be available.

* Allow the use of custom Python code in your Klipper macros by defining the `filters` parameter in a YAML config file.

* By default, includes many additional modules and functions. Look at the end of this README file, for more information.

* Functions can accept parameters and return values to the macro.

* Filters will process the variable to the left of the pipe symbol `(|)`

* Original work for this was done by @droans at https://github.com/droans/klipper_extras/tree/v0.2/extended_macro

* My contribution was to add the ability to turn your python code into a custom Jinja2 filter that can be used
 within the Klipper Jinja2 template engine.

* I have also enabled the ability to use your python code (either used as a "Klipper custom function" or as a "custom
 jinja2 filter") within Gcode that runs on a timer event or `[delayed_gcode]`.

---

## **:exclamation: WARNING: :exclamation:**

**Do not blindly download Python scripts, both for use in your macros and this script itself.** Read the script and ensure you understand what is going on. The scripts can be powerful and useful, but they can also be dangerous. If the script looks questionable, dangerous, or too confusing, it's better to assume that it is malicious.

---

## **Installation:**

You have two ways you can install this `j2filter_macro` extension (manually or via an installation script)

Here is the manual install instruction:

1. Write down the location of the following directories:
    * Klipper Extras: Usually located at `/home/USER/klipper/klippy/extras`.
    * Klippy Virtual Environment `bin` directory: Usually located at `/home/USER/klippy-env/bin`
2. Download `j2filter_macro.py`, `delayed_j2filter.py`, `j2filter_template.py`, and `requirements.txt`
3. Move `j2filter_macro.py`, `delayed_j2filter.py`, and `j2filter_template.py` to your Klipper Extras folder
   (home/pi/klipper/klippy/extras).
4. Move `requirements.txt` to your home directory (or /home/pi).
5. Run the following command, substituting `${KLIPPY_ENV}` with the Klippy Virtual Environment bin directory:

:bulb: **INFO:** Please Be patient, the installation process can take up to 15 minutes.  So let it run for a while.

>:point_up: Run the following command:
```BASH
cd ~
```
```BASH
${KLIPPY_ENV}/pip install -r ${HOME}/requirements.txt
```
or
```BASH
~/klippy-env/bin/pip install -r /home/pi/requirements.txt
```

## Here is the installation script method:

### First you must install the additional software packages

Perform the following commands at your Raspberry Pi command prompt:

1. Install the additional software packages needed on the Raspberry Pi for this extension to work:
```BASH
cd ~
```

```BASH
${KLIPPY_ENV}/pip install -r ${HOME}/requirements.txt
```
or
```BASH
~/klippy-env/bin/pip install -r /home/pi/requirements.txt
```
>:point_up: NOTE: Please Be patient, the installation process can take up to 15 minutes.  So let it run for a while.

---

### Now Install the `j2filter_macro` extension for Klipper

2. Install the `j2filter_macro` extension to Klipper, please run the following commands:
```BASH
cd /home/pi
```
```BASH
git clone https://github.com/GadgetAngel/j2filter_macro.git
```
```BASH
./j2filter_macro/install-j2filter.sh
```

---
## **How to Setup and use the `j2filter_macro` extension for Klipper:**

1. In your Klipper config, add the following section:
```python
[j2filter_template]
path:                 - the path to your yaml file (the path to the python code and the name you will be using inside klipper) Please ensure you use the UNIX forward (/) in the path option. DO NOT use Window's backward slash (\) in your path parameter.
jinja2_function:      - (True,False) set to True if you want to use the python code as a function call and invoke by using two curly braces, otherwise set to False; default is True
jinja2_filter:        - (True,False) set to True if you want to use the python code as a filter and invoke with the '|' character, otherwise set to False; default is True

```
>:point_up: NOTE: The default for both `jinja2_filter` and `jinja2_function:` is `Ture`. After you install the extension
>it is recommended that you set `jinja2_function:` option and `jinja2_filter:` to the appropriate values.  Only leave them
>both set to `True` if you are truly using the python code as a Klipper custom function and as a custom jinja2 filter.

The reason for the default settings is to ensure your 3D printer will boot back up without problems.

>:bulb:
> If you install the extension after you created a Klipper macro using the custom jinja2 filter, and you have set
>`jinja2_filter:` options to `False` then the 3D printer will error out because the extension will not register the
>custom jinja2 filter due to `jinja2_filter:` being set to `False`.  By ensuring the default value is `True` should
>prevent this problem from occurring.  If you do run into this error, please check your setting for `jinja2_filter:`.

>If you're setting for `jinja2_function: ` is wrong, you will not get an error at boot. The error will occur when you try
>and execute the macro that contains the Klipper custom function.

---
**Adding your own Python functions**

*See the example1 folder for more guidance. Also, See the example2 folder for a test case.*

1. Create your Python script(s) with the function(s) you intend to use.
2. Create a filter configuration file using `example1/klipper_functions/function_config.yaml` or `example2/KLIPPER_FILTERS/filters_config.yaml` as references. YAML is the allowed schema. The extension must be `yml` or `yaml`
3. In the `[j2filter_template]` section of your Kipper config, add the option `path:` with the value being the location of where you saved the config YAML file.
4. In the `[j2filter_template]` section of your Kipper config, add the option `jinja2_function:` with the value being `True` or `False` (the case of the letters does matter here).  If `jinja2_function:` is set to `True` then the python function defined in the file is to be used as a Klipper custom function (with parameters) in your Klipper macro.  If `jinja2_function:` is set to `False` then the python function defined in the file is not to be used as a Klipper custom function.
5. In the `[j2filter_template]` section of your Kipper config, add the option `jinja2_filter:` with the value being `True` or `False` (the case of the letters does matter here).  If `jinja2_filter:` is set to `True` then the python function defined in the file is to be used as a custom JINJA2 filter in your Klipper macro.  If `jinja2_filter:` is set to `False` then the python function defined in the file is not to be used as custom JINJA2 filter.

Yes, you can have the same function used as both a Klipper custom function and a custom JINJA2 filter.

:bulb: It is the user's responsibility not to overwrite predefined Jinja2 Filters!  There is nothing to check if the user is trying to create a filter name that already exists in Jinja2 unless it is built into the Jinja2 language already.

---
**Example1 Usage: An Example of using your python code as a Klipper custom function**

First you must create the `[j2filter_template]` section in your printer.cfg file so Klipper knows to load the `j2filter_template.py` file and include it into the Klipper code running on your Raspberry Pi.

Here is the example1 folder's `[j2filter_template]` section definition (it is located in the file: `klipper_functions.cfg`):
```python
#-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-
# J2FILTER_TEMPLATE Setup
#-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-

# Define the [j2filter_template] section and define the option parameter 'path' points to
#    the path of the filters_config.yaml file (any filename but it must have the .yml or .yaml extension)
#
# j2filter_template is used to define the path to your .yml or .yaml config file
[j2filter_template]
path: /home/pi/klipper_functions/function_config.yaml
jinja2_function: True
jinja2_filter: False

```

In the example1 folder the `example.py` file contains the python function called `repeat_back(rept):`.  If you look
into `function_config.yaml` file, you will see the following:
```YAML
filters:
  repeater:                                             # The name which will be used in the GCode to call the function. Must be unique.
    path: /home/pi/klipper_functions/example.py         # Location of Python script. Must be an absolute path
    filter: repeat_back                                 # The actual name of the Python function
```
The above words in the `YAML` file **must NOT** be changed:
```YAML
filters:
  you_can_change_me:
    path:
    filter:
```
>:point_up: NOTE: `filters:`, `path:`, `filter:` **must NOT** be changed!

The contents of `example.py` are as follows:
```python
def repeat_back(rept):
    return rept + ' ' + rept
```
>:point_up: NOTE: This is the file where you place your custom python code


Now you need to use the above python code inside a Klipper macro.  Example1 shows you how to use your python code as a Klipper custom function.

Here is the example2 Klipper macro definition (located in file: `klipper_functions.cfg`):
```python
#.................................................................................................................
# REPEAT_MSG - optional paramters: MSB{string} / Usage: REPEAT_MSG MSG="Kick "
#
#   Repeat a message in one string
#
#.................................................................................................................
# Required variable(s) to be set. Add the following to your global variable dictionary block as:
#
# ---NONE---
#
#.................................................................................................................
# Required external macro(s) used by this macro set.
#
# ---NONE---
#
#.................................................................................................................

#.................................................................................................................
#
## URL Resources:
#
#.................................................................................................................
[j2filter_macro REPEAT_MSG]
description: Helper: Repeat a message twice
variable_flag: 0
gcode:
    {% set rpt = params.MSG %}
    M118 {repeater(rpt)}
```
As you can see the macro has to have the section defined with ``j2filter_macro Macro_Name``.  You use `j2filter_macro` the same way you presently use `gcode_macro`

You can have local variables defined via ``variable_name:`` and you can use `description` to have a short description of the purpose for the macro.

If you want to use this code inside a timer event you can replace `j2filter_macro` with `delayed_j2filter`.  So just like you use `[delayed_gcode macro_name]` you would use `[delayed_j2filter macro_name]`.

### Using `delayed_j2filter` section
With delayed Gcode the macro name should be in lower case lettering.  Also, you can not have a `description:` option or have local variables defined in a `delayed_gcode` or `delayed_j2filter` macro definition.  The same rules that apply for `delayed_gcode` apply for `delayed_j2filter`.  The difference between the two is that you can have a Klipper custom function or a custom jinja2 filter inside a `delayed_j2filter` section, but you can not use them inside a `delayed_gcode` section.
---

**Example2 Usage: An Example of using your python code as a Custom Jinja2 Filter**

First you must create the `[j2filter_template]` section in your printer.cfg file so Klipper knows to load the `j2filter_template.py` file and include it into the Klipper code running on your Raspberry Pi.

Here is the example2 folder's `[j2filter_template]` section definition (it is located in the file: `J2FILTER_TEMPLATE.cfg`):
```python
#-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-
# J2FILTER_TEMPLATE Setup
#-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-

# Define the [j2filter_template] section and define the option parameter 'path' points to
#    the path of the filters_config.yaml file (any filename but it must have the .yml or .yaml extension)
#
#J2FILTER_TEMPLATE
[j2filter_template]
# j2filter_template is used to define the path to your custom JINJA2 Filters
path: /home/pi/klipper_config/ACTIVE/CONFIG/KLIPPER_FILTERS/filters_config.yaml
jinja2_function: True
jinja2_filter: True

```

In the example2 folder there are two example python code files: `string_to_dict.py` and `strjson_to_dict.py`. Example2 contains my Python coded that I plan to use as custom jinja2 filters.  The python code are called: `string_to_dict(dic_string):` and `strjson_to_dict(dict_string):`.  If you look into `filters_config.yaml` file, you will see the following:
```YAML
filters:
  str_json_todict:                                                                   # The name which will be used in the GCode to call the function. Must be unique.
    path: /home/pi/klipper_config/ACTIVE/CONFIG/KLIPPER_FILTERS/strjson_to_dict.py # Location of Python script. Must be an absolute path
    filter: strjson_to_dict                                                          # The actual name of the Python function
  str_to_dict:                                                                       # The name which will be used in the GCode to call the function. Must be unique.
    path: /home/pi/klipper_config/ACTIVE/CONFIG/KLIPPER_FILTERS/string_to_dict.py  # Location of Python script. Must be an absolute path
    filter: string_to_dict                                                           # The actual name of the Python function
```
The above words in the `YAML` file **must NOT** be changed:
```YAML
filters:
  you_can_change_me:
    path:
    filter:
```
>:point_up: NOTE: `filters:`, `path:`, `filter:` **must NOT** be changed!

The contents of `string_to_dict.py` are as follows:
```python
import json

def string_to_dict(dic_string):
    """
    Custom Jinja2 filter - str_todict

    :param dic_string: string to be converted to dictionary structure
    :return: dict_out - python dictionary data type
    """

    # remove the curly braces from the string
    dic_string = dic_string.strip('{}')

    # split the string into key-value pairs
    pairs = dic_string.split(', ')

    #define a blank dict obj
    dict_out = {}

    # use a dictionary comprehension to create the dictionary,
    # converting the value to float or integer depending if a '.' is present in value
    for key,value in (pair.split(': ') for pair in pairs):
        if '"' in key:
            key = key.replace('"','',2)
        elif "'" in key:
            key = key.replace("'","",2)

        if '.' in value:
            val = float(value)
            dict_out.update({key: val})
        else:
            val = int(value)
            dict_out.update({key: val})

    return dict_out

```
>:point_up: NOTE: This is the file where you place your custom python code

The contents of `strjson_to_dict.py` are as follows:
```python
import json

def strjson_to_dict(dict_string):
    """
    Custom Jinja2 filter - json_todict

    :param dict_string: string to be converted to dictionary structure
    :return: res - python dictionary data type from a properly json string (must use double quotes around the key name! i.e. not sinqle quotes)
    """

    # using json.loads()
    # convert dictionary string to dictionary
    res = json.loads(dict_string)
    return res
```
>:point_up: NOTE: This is the file where you place your custom python code


Now you need to use the above python code files inside a Klipper macro.  Example1 showed you how to use your python code as a Klipper custom function. Example2 shows you how to use your python code as a custom jinja2 filter.

Example2 uses a regular `gcode_macro` file to call a `j2filter_macro` section.  The calling macro's name is `TEST_CALL_PARK` which calls the `j2filter_macro PARK_DICT` named `PARK_DICT`.

`PARK_DICT` will accept a string that has the X, Y, Z park coordinates in a dictionary format.  But Klipper only sends string to other macros, unless you use a global variable area.  I want to see if I could get a Klipper macro to accept a dictionary variable as a parameter on the command line.  To accomplish this I need a custom jinja2 filter, so I can convert the string back into a python dictionary object.  I could not find any other way of performing this task.

So I wrote two different ways of converting a string into a python dictionary object.  `strjson_to_dict.py` uses a JSON library call to convert the string.  The problem with this is if you use single quotes around the key name the JSON library will not consider the string a valid JSON string.

So I created `string_to_dict.py` which is little more robust than `strjson_to_dict.py`. With `string_to_dict.py` I can use either single quotes or double quotes around the key name.  I tried to make it flexible enough so that the key name was not limited to one character.  This code will only work with "{'key': integer_value}" or '{"key": float_value}'.  Basically if you pass it a string for the value it will not know how to deal with it.  But it does what I need it to do at the present time. I can update my code at anytime and just "RESTART" Klipper (as if I just made a change to a Klipper macro).

Here is the example2 `TEST_CALL_PARK` Klipper macro definition:
```python
#.................................................................................................................
# TEST_CALL_PARK - No additional options / Usage: TEST_CALL_PARK
#
#   Parks the nozzle
#
#.................................................................................................................
# Required variable(s) to be set. Add the following to your global variable dictionary block as:
#
# ---NONE---
#
#.................................................................................................................
# Required external macro(s) used by this macro set.
#
# _general_Debug
# PARK_DICT
#
#.................................................................................................................

#.................................................................................................................
#
## URL Resources: https://github.com/zellneralex/klipper_config/blob/master/park_macro.cfg#L1
#                 https://github.com/droans/klipper_extras/tree/v0.2/extended_macro
#
#.................................................................................................................

[gcode_macro TEST_CALL_PARK]
description: Helper: Park head depending on variable DICT parameter which is a python dictionary variable
gcode:
   _general_Debug msg="TEST_CALL_PARK - entering"
    {% set dict_string = {"x": 150.0, "y": 20.0, "z": 50.0, "f": 4000.0} %}
    PARK_DICT DICT='{dict_string}'

    PARK_DICT DICT='{"x": 250, "y": 50, "z": 70, "f": 4000.0}'

    {% set dict_string = {'x': 150.0, 'y': 20.0, 'z': 50.0, 'f': 4000.0} %}
    PARK_DICT DICT="{dict_string}"

    PARK_DICT DICT="{'x': 250, 'y': 50, 'z': 70, 'f': 4000.0}"

#==================

    {% set dict_string = {"x": 150.0, "y": 20.0, "z": 50.0, "f": 4000.0} %}
    PARK_DICT LIT='{dict_string}'

    PARK_DICT LIT='{"x": 250, "y": 50, "z": 70, "f": 4000.0}'

    {% set dict_string = {'x': 150.0, 'y': 20.0, 'z': 50.0, 'f': 4000.0} %}
    PARK_DICT LIT="{dict_string}"

    PARK_DICT LIT="{'x': 250, 'y': 50, 'z': 70, 'f': 4000.0}"
   _general_Debug msg="TEST_CALL_PARK - exiting"
```

Here is the example2 `PARK_DICT` Klipper macro definition (this is the j2filter_macro showing the custom jinja2 filter):
```python
#.................................................................................................................
# PARK_DICT - optional parameters: DICT{'string_in_pyhon_dict_format'} LIT{'string_in_pyhon_dict_format'}
#
#   / Usage:
#
#   PARK_DICT DICT='{"x": 150.0, "y": 20.0, "z": 50.0, "f": 4000.0}'  ---> this is typed in at the Mainsail UI console window
#   PARK_DICT DICT='{"x": 250.0, "y": 50.0, "z": 70.0, "f": 4000.0}'
#   PARK_DICT DICT="{'x': 150.0, 'y': 20.0, 'z': 50.0, 'f': 4000.0}"
#   PARK_DICT DICT='{"x": 250, "y": 50, "z": 70, "f": 4000}'
#   PARK_DICT DICT="{'x': 150, 'y': 20, 'z': 50, 'f': 4000}"
#   PARK_DICT LIT='{"x": 150.0, "y": 20.0, "z": 50.0, "f": 4000.0}'  ---> this is a string passed to the rountine
#   PARK_DICT LIT='{"x": 250.0, "y": 50.0, "z": 70.0, "f": 4000.0}'
#   PARK_DICT LIT="{'x': 150.0, 'y': 20.0, 'z': 50.0, 'f': 4000.0}"
#   PARK_DICT LIT='{"x": 250, "y": 50, "z": 70, "f": 4000}'
#   PARK_DICT LIT="{'x': 150, 'y': 20, 'z': 50, 'f': 4000}"
#
#   Parks nozzle at x y z using a feedrate of f
#
#.................................................................................................................
# Required variable(s) to be set. Add the following to your global variable dictionary block as:
#
## ---NONE---
#
#.................................................................................................................
# Required external macro(s) used by this macro set.
#
# _general_Debug
# _CG28
# G0
# str_to_dict a call to the python function string_to_dict()
#
#.................................................................................................................

#.................................................................................................................
#
## URL Resources: https://github.com/zellneralex/klipper_config/blob/master/park_macro.cfg#L1
#                 https://github.com/droans/klipper_extras/tree/v0.2/extended_macro
##
#.................................................................................................................

[j2filter_macro PARK_DICT]
description: Helper: Park head depending on variable DICT parameter which is a python dictionary variable
gcode:
   _general_Debug msg="PARK_DICT - entering"
   {% if params.DICT %}
       {% set position = params.DICT %}
       _CG28                 ; home if not already homed
       G90                   ; absolute positioning
       G0 X{(str_to_dict(position)).x} Y{(str_to_dict(position)).y} Z{(str_to_dict(position)).z} F{(str_to_dict(position)).f}    #custom python fucntion call
       {% if not printer.gcode_move.absolute_coordinates %} G91 {% endif %} ; set back to relative
   {% elif params.LIT %}
       {% set position = params.LIT %}
       {% set position = position|str_to_dict %}                  #custom jinja2 filter
       _CG28                 ; home if not already homed
       G90                   ; absolute positioning
       G0 X{position.x} Y{position.y} Z{position.z} F{position.f}
       {% if not printer.gcode_move.absolute_coordinates %} G91 {% endif %} ; set back to relative
   {% endif %}
   _general_Debug msg="PARK_DICT - exiting"
```

Here is an example from folder example2 on how to use the `[delayed_j2filter macro_name]` section.  I turned `PARK_DICT` into a delay Gcode routine by using `[delayed_j2filter macro_name]` section.  This macro will perform a G28 and park the tool head 10 seconds after Klipper hits the `ready` state:

Here is `DELAYED_PARK_DICT` macro which utilizes the `delayed_j2filter`section because I am using a custom jinja2 filter inside the Klipper macro:
```python
#.................................................................................................................
# delayed_park_dict - No additional options / Usage: NONE
#
#   Parks nozzle at x y z using a feedrate of f; Doest the park 10 seconds after Klipper reaches the ready state on boot or RESET
#
#.................................................................................................................
# Required variable(s) to be set. Add the following to your global variable dictionary block as:
#
## ---NONE---
#
#.................................................................................................................
# Required external macro(s) used by this macro set.
#
# _general_Debug
# _CG28
# G0
# str_to_dict a call to the python function string_to_dict() as either a function call or as a jinj2 custom filter
#
#.................................................................................................................

#.................................................................................................................
#
## URL Resources: https://github.com/zellneralex/klipper_config/blob/master/park_macro.cfg#L1
#                 https://github.com/droans/klipper_extras/tree/v0.2/extended_macro
#.................................................................................................................

[delayed_j2filter delayed_park_dict]
initial_duration = 10
gcode:
    _general_Debug msg="delayed_park_dict - entering"
   #{% set position = '{"x": 150.0, "y": 20.0, "z": 50.0, "f": 4000.0}' %}
   {% set position = "{'x': 250, 'y': 50, 'z': 70, 'f': 4000.0}" %}
   {% set position = position|str_to_dict%}                                                                                #custom jinja2 filter
   _CG28                 ; home if not already homed
   G90                   ; absolute positioning
   #G0 X{(str_to_dict(position)).x} Y{(str_to_dict(position)).y} Z{(str_to_dict(position)).z} F{(str_to_dict(position)).f}   #custom python function call
   G0 X{position.x} Y{position.y} Z{position.z} F{position.f}
   {% if not printer.gcode_move.absolute_coordinates %} G91 {% endif %} ; set back to relative
   _general_Debug msg="delayed_park_dict - exiting"
```

---

When defining the macro, use `[j2filter_macro <macro_name_here>]` as the section name instead of `gcode_macro`. To use your python code, you will wrap the name of your python code function between two curly brackets (`{gcode_function_name_goes_here}`) if you want to use your python code as a Klipper custom function call.

I found that when using my python code as a Klipper custom function call was awkward.  I prefer to use my python code as a jinja2 custom filter.

The reason I found it awkward was that I needed to translate a string into a python dictionary object.

My code ended up looking like the following when using my python code as a Klipper custom function call:

```python
{% set position = params.DICT %}
G0 X{(str_to_dict(position)).x} Y{(str_to_dict(position)).y} Z{(str_to_dict(position)).z} F{(str_to_dict(position)).f}
```

By using my python function as a custom jinja2 filter I could now write the macro by using the pipe character '|' as follows:

```python
{% set position = params.LIT %}
{% set position = position|str_to_dict %}
G0 X{position.x} Y{position.y} Z{position.z} F{position.f}
```

It is easier to tell that I am using a python dictionary object where the Klipper custom function call is very hard to tell what is going on.

---
### Moonraker Update Manager:

It's possible to keep this extension up to date with the Moonraker's update manager by adding this configuration block to the "moonraker.conf" of your printer:

```python

[update_manager client j2filter_macro]
type: git_repo
path: ~/j2filter_macro
origin: https://github.com/GadgetAngel/j2filter_macro.git
install_script: install-j2filter.sh
managed_services: klipper
```

This requires this repository to be cloned into your home directory (e.g. /home/pi):

```BASH
git clone https://github.com/GadgetAngel/j2filter_macro.git
```

The URL for Moonraker ``[update_manager]`` section can be found [here](https://moonraker.readthedocs.io/en/latest/configuration/#update_manager)

---

### **Defaults that are automatically loaded with the j2filter_macro extension**

`j2filter_macro` comes with many default functions which do not need to be added or declared by the user. These include:

*Types:*
```
list
dict
set
tuple
str
int
float
bool
type
```

*Built-ins:*
```
dir
getattr
setattr
locals
globals
math
itertools
```

*Additional:*
```
pandas
numpy
datetime
collections
```