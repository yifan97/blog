---
layout: post
current: post
cover: assets/images/cover-img/c17.jpg
navigation: True
title: Path manipulation and File Handling
date: 2019-08-06
comments: true
---


**This post serves as a self-practice and introduction of common path manipulation and file handling, such as how to write a file, read a file, serialize a file to json format as well as some pitfalls and tricks. I will use Python as examples, but other languages are essentially the same.**

------------------

A lot of times when we want to extract information from places into our programs, we need to do many path and files operations to store the information in our local in a reasonable format so that we can save that standardized information for future use. Usually thsoe operations include path searching/declaration, files writing or reading, format serialization, etc. So I am gonna break down each piece step by stemp in details using an example of my recent project.

```python
import os

# when the file is changed, do something
def do_something():
    cache_stamp = 0

    stamp = os.stat(file_path).st_mtime
    #check whether file is changed or not
    if stamp != cache_stamp:
        cache_stamp = stamp

        # file has changed, so do something
        ...
```

```python
import os
import json
import datetime
from pathlib import Path

def logger(base_filename):
    src_path = os.path.realpath(base_filename)
    dir_path = os.path.dirname(src.path)

    # open() will return a file object, and 
    # read() will return a string containing all chars in the file
    with open(src_path, 'r') as checkpoint:
        checkpoint = json.loads(checkpoint.read())

        # create the path to store the json format into a file in target path
        log_path = Path(os.path.join(dir_path, base_filename.split('.')[0]+'_log.json'))

        # when log_path is created, log_path.is_file return False since the file itself hasn't been created yet
        if log_path.is_file():
            old = ''
            with open(log_path, 'r') as f:
                try:
                    old = json.loads(f.read())
                except json.decoder.JSONDecodeError:
                    print('There is an error decoding log. Log file may be corrupt')
                    return

            should_update = old['checkpoints'][-1]['checkpoint'] != checkpoint

            if should_update:
                with open(log, 'w') as f:
                    old["checkpoints"].append({"time":str(datetime.datetime.now()),"checkpoint":checkpoint})
                    f.write(json.dumps(old))

        else:
            # actually create log file in the log_path defined above
            with open(log_path, 'w') as f:
                new = {"checkpoints":[{"time":str(datetime.datetime.now()),"checkpoint":checkpoint}]}                    
                f.write(json.dumps(new))

```

This two snippets of code kind of explain how to handle files. Let's recap some key takeaways:

- ```os.stat(path)```: I used os.stat(path) to get some basic data from the file in the argument *path*. Notice that the argument can be either a string representation or a path object. ```os.stat()``` returns several further information. One of them, and the one I used in the first snippet is ```.st_mtime()```. This will return the time of the most recent modification. I want to leverage this method to check whether the file has been changed and do something about it. 

- ```open(path, mode)```: This function opens a file, and returns it as a file object. It takes two arguments: the first one being path of file that you want to open, the second one being the mode, and there are some options:
<img src="assets/images/FH/FH-1.jpg" alt="FH-1" style="width: 90%;">
Again, the path argument can be either a string representation or a path object.

- ```.read()```: This is a method owned by the file object from ```open()```. It reads the content of the file. By default, ```.read()``` returns the whole text, but you can also specify how many characters you want to return by ```.read(5)```, for example, to return first 5 chars.

- ```Path```: It is a function in ```pathlib``` module. Here I use it to convert a string(in a form of path) to a path object.

- ```json.loads()```: This method returns an object from a string representing a json object. It is usually used in reading mode.

- ```json.dumps()```: This method takes an object and produces a string:. It is usually used in writing mode. Notice that ```.loads()``` and ```.dumps()``` convert betweeen objects and strings.