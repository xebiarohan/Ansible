1. if condition

```
-
  name: if condition in jinja2 templating
  hosts: all
  tasks:
    - name: printing message for ubuntu-c
      debug:
        msg: >
            {# This is a comment bracket -#}
            {% if ansible_hostname === "ubuntu-c" -%}
                This is ubuntu-c
            {% elif ansible_hostname === "centos1" -%}    
                This is a centos machine
            {% else -%}
                This is ubuntu system
            {% endif %}
```

2. We can put condition to check if a variable is defined or not
```
-
  name: is variable defined
  hosts: all
  tasks:
    - name: variable check
      debug:
        msg: >
            {# This is a comment bracket -#}
            {% if example_hostname is defined -%}
                example_hostname is defined
            {% else -%}
                example_hostname is not defined
            {% endif %}
```

3. We can set variables using Jinja2 templating

```
-
  name: is variable defined
  hosts: all
  tasks:
    - name: variable check
      debug:
        msg: >
            {# This is a comment bracket -#}
            {% set example_hostname='abc' -%}
            {% if example_hostname is defined -%}
                example_hostname is defined
            {% else -%}
                example_hostname is not defined
            {% endif %}
```

4. For loop in Jinja2 templating

```
-
  name: For loop in jinja2 templating
  hosts: all
  tasks:
    - name: for loop example
      debug:
        msg: >
             {# This is a comment section -#}
             {% for entry in ansible_interface -%}
                Interface entry {{loop.index}} = {{entry}}
             {% endfor %}
```

5. Ranges in for loop
    - for example if we want to do for 1 to 10 
    - exclusive last value

```
-
  name: For loop in jinja2 templating
  hosts: all
  tasks:
    - name: for loop example
      debug:
        msg: >
             {# This is a comment section -#}
             {% for entry in range(1,11) -%}
                {{entry}}
             {% endfor %}
```

6. Break and continue
    - need to update the ansible config as well
    - continue works the same way

```
-
  name: Break and continue
  hosts: all
  tasks:
    - name: Break and continue example
      debug:
        msg: >
             {# This is a comment section -#}
             {% for entry in range(10,0, -1) -%}
               {% if entry === 5-%}
                 {% break -%}
               {% endif %} 
               {{entry}}
             {% endfor %}
```

```
[defaults]
inventory = hosts
host_key_checking = False
jinja2_extensions = jinja2.ext.loopcontrols
```

```
-
  name: Break and continue
  hosts: all
  tasks:
    - name: Break and continue example
      debug:
        msg: >
             {# This is a comment section -#}
             {% for entry in range(10,0, -1) -%}
               {% if entry is odd -%}
                 {% continue -%}
               {% endif %} 
               {{entry}}
             {% endfor %}
```

7. Filters in jinja2 templating

```
    {{[1,2,3,4,5] | min }}
    {{[1,2,3,4,5] | max }}
    {{[1,2,3,4,5,5,4,3,2,1] | unique }}
    {{[1,2,3,4,5] | random }}

```

8. External template file

```
  tasks:
    - name: Jinja2 template
      template:
        src: template.j2
        dest: "/tmp/{{ ansible_hostname }}_template.out"
        trim_blocks: true
        mode: 0644

```

```template.j2

{# If the hostname is ubuntu-c, include a message -#}
{% if ansible_hostname == "ubuntu-c" -%}
      This is ubuntu-c
{% endif %}

--== Ansible Jinja2 if elif statement ==--

{% if ansible_hostname == "ubuntu-c" -%}
   This is ubuntu-c
{% elif ansible_hostname == "centos1" -%}
   This is centos1 with it's modified SSH Port
{% endif %}

--== Ansible Jinja2 if elif else statement ==--

{% if ansible_hostname == "ubuntu-c" -%}
   This is ubuntu-c
{% elif ansible_hostname == "centos1" -%}
   This is centos1 with it's modified SSH Port
{% else -%}
   This is good old {{ ansible_hostname }}
{% endif %}

```