# Python Module Hijacking lead to Code Execution - POC

Install the requirements running `./install.sh` or just run `python3 -m pip install -r requirements.txt` in the project directory. Then.. you will see ;)

**Note: This repository does not contain harmful code, it exists as a proof of concept. I used the well known [impacket](https://github.com/SecureAuthCorp/impacket) project source files to masquerade the attack**


# Explanation

I noticed that when a user runs a python module (e.g., python3 -m pip install something) it is possible to perform a module hijacking leading to code execution.
The problem is related to the search path that has the current working directory as its first entry. In details, this happens if the user runs the module in a working directory that contains a python script named as one of the imported modules.
As an example, if a directory contains a python script named "enum.py" this will be executed if the user runs pip with the command "python3 -m pip install something". The script "enum.py" is executed because pip imports enum module, but the local enum.py is loaded instead of the enum.py located in the library path (/usr/lib/python3.9/enum.py).

The fact that python looks first the modules in the current directory lead the possibility for an attacker to hijack python modules and run stealth code against unaware users. In this POC python throws an exception after the code execution, but it is possible to make it work without any error.

**This issue is already well known**, I made this repo to warn users about this issue that could be dangerous if the user clones a public repository containing this tricky code execution. *Do not run python modules using a working directory containing code that you don't trust*.

## Possible Scenario
An attacker creates a python project on github with several python files. Among these files he can put a file that overwrites a common module (e.g. enum.py). The content of the script could be a little bit obfuscated/masqueraded to be less suspicious.
In the "readme" file he can write to install the dependencies first launching "python3 -m pip install -r requirements.txt". If the user follows the instructions in the readme, or if he runs an installer script with this command, the result will be that the attacker's code is executed on the user machine even if the user didn't mean to execute the project or any related script.

## Examples
Other commands to run in order to trigger the poc are the following:
- python3 -m http.server
- python3 -m compileall .


I noticed this behavior in some Linux distributions as Manjaro Linux 21.2.0 and Debian 11.2.0.
On other distributions like Kali Linux 2021.2 the hijack doesn't work except for "python3 -m compileall ." that loads token.py.
This suggest that probably the issue depends also on some system configurations.



