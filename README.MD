# subprocess for pyinstaller 

## pip install pyinstallersubprocess

### Based on https://github.com/pyinstaller/pyinstaller/issues/6362#issuecomment-968156787

Read the post before using this module.

Here is the most important part:

```python
"Make all your entry points available as python -m invokable submodules and change all your subprocess.run(["entry_point_foo"]) usages to subprocess.run([sys.executable, "-m", "hello_word.entry_point_foo"]).
Extend those subprocess.run() calls to:
if getattr(sys, 'frozen', False):
    subprocess.run([os.path.join(sys._MEIPASS, "entry_point_foo")])
else:
    subprocess.run([sys.executable, "-m", "hello_word.entry_point_foo"])
Run PyInstaller on each entry point submodule (e.g. hello_world/hello_world/run_hello_world.py) and make use of Merge() to put them together in one bundle."
```

```python
import subprocess
from pyinstallersubprocess import (
convert_py_file_to_module,
subprocess_console,
subprocess_console_devnull,
subprocess_console_popen,
subprocess_console_stdout_read,
subprocess_no_console_devnull,
subprocess_no_console_popen,
subprocess_no_console_stdout_read)


# The function convert_py_file_to_module turns the py file into an "python -m invokable submodule"

modulename, entry = convert_py_file_to_module(
    modulename="mysubprocess", # choose what anything you want, but make sure that the folder "mysubprocess" doesn't exist in your base folder
    pythonfile=r"C:\Users\Gamer\anaconda3\envs\dfdir\pytfi.py", 
    rename_pyfile=False, # renames r"C:\Users\Gamer\anaconda3\envs\dfdir\pytfi.py" -> r"C:\Users\Gamer\anaconda3\envs\dfdir\pytfi.old"
    overwrite_existing=True, # if the folder exists 
    add_main_entry=True, # if there is no entry, 'if __name__ == "__main__" ... will be added 
)

print((modulename, entry))
('mysubprocess', 'pytfi')

# Here are some functions that take care of: "Execute the "extended" subprocess (if getattr(sys, 'frozen', False) ...)"
p = subprocess_console(
    module=modulename,
    entry=entry,
    stdout=subprocess.PIPE,
    stderr=subprocess.PIPE,
    stdin=subprocess.PIPE,
)

print(p)
# CompletedProcess(args=['C:\\Users\\Gamer\\anaconda3\\envs\\dfdir\\python.exe', '-m', 'mysubprocess.pytfi'], returncode=0, stdout=b'oioou\r\n', stderr=b'')


# All functions:

subprocess_console(module, entry, *args, **kwargs)
subprocess_console_devnull(module, entry, *args, **kwargs)
subprocess_console_popen(module, entry, *args, **kwargs)
subprocess_console_stdout_read(module, entry, *args, **kwargs)
subprocess_no_console_devnull(module, entry, *args, **kwargs)
subprocess_no_console_popen(module, entry, *args, **kwargs)
subprocess_no_console_stdout_read(module, entry, *args, **kwargs)

# This module:
# https://github.com/hansalemaos/env2installer
# can help you with "Run PyInstaller on each entry point submodule (e.g. hello_world/hello_world/run_hello_world.py)..."


```
