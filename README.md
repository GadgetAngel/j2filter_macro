# extended_macro
---
* Allow the use of custom Python functions in your Klipper macros by defining the functions in a YAML config file
* By default includes many additional modules and functions. See below for more information.
* Functions can accept parameters and return values to the macro.
*
* Original work for this was done by @droans at https://github.com/droans/klipper_extras/tree/v0.2/extended_macro
*
* My contribution was to add the ability to turn your python code into a custom Jinja2 filter that can be used
* within the Klipper Jinja2 template engine

### WARNING:

**Do not blindly download Python scripts, both for use in your macros and this script itself.** Read the script and ensure you understand what is going on. The scripts can be powerful and useful, but they can also be dangerous. If the script looks questionable, dangerous, or too confusing, it's better to assume that it is malicious.

**Installation:**
1. Write down the location of the following directories:
    * Klipper Extras: Usually located at `/home/USER/klipper/klippy/extras`.
    * Klippy Virtual Environment `bin` directory: Usually located at `/home/USER/klippy-env/bin`
2. Clone this repository or download `extended_macro.py`, `extended_template.py`, and `requirements.txt`
3. Move `extended_macro.py` and `extended_template.py` to your Klipper Extras folder.
4. Move `requirements.txt` to your home directory. or /home/pi
5. Run the following command, substituting `${KLIPPY_ENV}` with the Klippy Virtual Environment bin directory:

## Please Be patient, the install process can take up to 15 minutes.  So let it run for a while.

```
${KLIPPY_ENV}/pip install -r ${HOME}/requirements.txt
or
~/klippy-env/bin/pip install -r /home/pi/requirements.txt
```

---
**Setup:**

1. In your Klipper config, add the following section:
```
[extended_template]
```

---
**Adding your own Python functions**

*See the example folder for more guidance.* See the example2 folder for a test case.

1. Create your Python script(s) with the function(s) you intend to use.
2. Create a function configuration file using `example/function_config.yaml` or `example2/function_config.yaml` as references. YAML is the allowed schema. The extension must be `yml` or `yaml`
3. In the `[extended_template]` section of your Kipper config, add the variable `path` with the value being the location of where you saved the config YAML file.

---
**Usage:**

See the example folder or example2 folder for more guidance.

---

### Defining the ``[extended_template]`` section for Klipper

The following items will be needed in [extended_template] of your klipper printer.cfg file:

```
[extended_template]
# Extended_template is used to define the path to your Function Config
path: /home/pi/klipper_config/ACTIVE/CONFIG/KLIPPER_FUNCTIONS/function_config.yaml
jinja2_function: True
jinja2_filter: True

```

The following parameters will be needed:

```
path:                 - the path to your yaml file (the path to the python code and the name you will be using inside klipper)
jinja2_function:      - (True,False) set to True if you want to use the python code as a function call and invoke by using two curly braces, otherwise set to False; default is True
jinja2_filter:        - (True,False) set to True if you want to use the python code as a filter and invoke with the '|' character, otherwise set to False; default is False
```
---

When defining the macro, use `extended_macro` as the config name instead of `gcode_macro`. To use your function, you will wrap the name with curly brackets (`{gcode_function_name_goes_here}`) if you want to use it as a function call.

I found that when using my function as a function call was awkward.  I prefer to use my function as a jinja2 custom filter.

The reason I found it awkward was that I needed to translate a string into a python dictionary object.

My code ended up looking like the following when using my python function as a function call:

```
{% set position = params.DICT %}
G0 X{(str_to_dict(position)).x} Y{(str_to_dict(position)).y} Z{(str_to_dict(position)).z} F{(str_to_dict(position)).f}
```

By using my python function as a custom jinja2 filter I could now write the macro by using the pipe character '|' as follows:

```
{% set position = params.LIT %}
{% set position = position|str_to_dict %}
G0 X{position.x} Y{position.y} Z{position.z} F{position.f}
```

It is easier to tell that I am using a python dictionary object where the function call is very hard to tell what is going on.

---
### **Defaults**

`extended_macro` comes with many default functions which do not need to be added or declared by the user. These include:

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