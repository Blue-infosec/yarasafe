
# YARASAFE - Adding SAFE embedding to match functions in yara

SAFE is a tool developed to create Binary Functions Embedding developed by 
Massarelli L., Di Luna G.A., Petroni F., Querzoni L. and Baldoni R.
You can use SAFE to create your function embedding to use inside yara rules.
If you are using this please cite:
https://arxiv.org/abs/1811.05296

### Requirements

python3


## Quickstart

### Ubuntu

Clone the repository:

```
git clone https://github.com/lucamassarelli/yarasafe.git
```

Install yara dependencies: 

```
sudo apt-get install automake libtool make gcc flex bison 
sudo apt-get install libjansson-dev
```

Install radare2 on your system:

```
git clone https://github.com/radare/radare2.git
cd radare2
./sys/install.sh
```

Install yarasafe dependencies:

```
cd yarasafe/python_script
pip install -r requirements.txt
```

Find python 3.* library path:
```python
import sys
print(sys.path[2])
```




###What you need
Before to compile yara you need python3.* installed into your computer.
Moreover, you should create a virtualenv that uses python3.* and has the requirements
installed.
After you created and activated the virtualenv, you can install the requirements with:
```bash
cd python_script
pip install -r requirements.txt
```
You should also have radare2 installed in your system.

To compile you also need to install jansson library:

On ubuntu:
```bash
sudo apt-get install libjansson-dev
```
For different platform look at: https://jansson.readthedocs.io/en/2.11/gettingstarted.html


Finally, you need to install Radare2: https://github.com/radare/radare2

###How to compile 

* Inside the file Makefile.am change:
``` bash
libyara_la_LDFLAGS += -LPATH_TO_PYTHON3.*_LIB -lpython3.*m -ljansson 
``` 

After the line:
``` bash
libyara_la_LDFLAGS = -version-number 3:8:1
``` 

To find the path to your python libraries you can launch python and use:
```python
import sys
print(sys.path)
```

Now you are ready to Compile:
* ./bootstraph.sh
* ./configure
* make

### How to add safe to your version of yara
If you want to add safe to your yara repository:
* Copy the file libyara/modules/safe.c into your_rep/libyara/modules/safe.c
* Copy the folder libyara/include/python into your_rep/libyara/include
* At the end of libyara/modules/module_list add ``` MODULE(safe)
* Compile! `

###How to write your rule

An example of safe yara rule is:

```yara
import safe

rule example
{
    meta:
        description = "This is just an example"
        threat_level = 3
        in_the_wild = true

    strings:
        f_1 =  "[-0.02724416,0.00640265,0.01138294,-0.07013566,0.00306808,-0.09757628,0.10414989,-0.13555837,-0.07873314,-0.00725415,-0.01418876,-0.05907412,-0.12452127,0.06237456,0.02260636,-0.06013175,0.11689295,-0.00200026,-0.03594812,0.07857288,-0.00288544,0.01148411,0.00891006,0.04702956,0.1205316,0.0079077,-0.07449158,0.00653283,0.15414064,0.13021031,0.01325423,-0.35491243,-0.00992016,-0.21460094,0.0558461,-0.07761839,-0.10909985,-0.05616508,0.01800609,0.06736821,0.00308393,0.04241242,-0.08351246,0.13501632,-0.10729794,-0.10229874,0.00066896,-0.01963937,0.05516102,-0.01612499,-0.09743191,-0.0314435,-0.01470971,-0.00125769,-0.01774654,0.2332938,0.14166495,0.16998142,-0.04843156,-0.08931472,0.13102795,0.14147657,0.02275739,-0.04335862,0.05724025,0.03936686,-0.10526938,-0.11637416,-0.0112917,0.05484914,-0.06934103,0.2543144,-0.17833991,-0.00828893,0.00174531,-0.03048271,-0.04773486,0.095866,-0.14434388,0.11433239,-0.10749247,0.03952292,0.03988512,-0.11541581,-0.07812429,-0.04978319,0.32052052,-0.0497911,-0.13022986,0.02477266,-0.05968329,0.01724695,0.01577485,-0.0497415,0.24494685,0.00361651,-0.08172874,-0.07473877,-0.01046288,0.02298573]"

    condition:
        safe.similarity($f_1) > 0.95
}
```

The rule will be satisfied if inside the sample there is at least one function
whose similarity with target is more then 0.95

###How to compute embedding of your functions:
You can use the the script create_rule.py that you can found inside the folder python script
launch it as:
```bash
python create_rule.py -f target_file
```

the script will ask you the address of the function to embedd and then it will print
its embedding, that you can use for your own rule.

###How to launch yara with SAFE

Before to launch yara with SAFE you have to set two environmental variable:
```bash
export PYTHONHOME='PATH_TO_THE_VIRTUAL_ENV'
export YARAPYSCRIPT = 'PATH_TO_THE_FOLDER_PYTHON_SCRIPT'
```

Then you can launch yara as usual!

```bash
./yara safe_rule.yar  folder_to_scan/
```
