<!-- markdownlint-configure-file {
  "MD013": {
    "code_blocks": true,
    "tables": false
  },
  "MD033": false,
  "MD041": false,
  "MD013": false
} -->

# Accessing Echoview Through `COM` in `Python`

???+ info "Requirements"
    To access Echoview via Python, your machine must have the following:

    * Windows operating system
    * Echoview software installed with an active license (including the Automation Module)
    * Python 3.10+
    * `pywin32` package (see below for installation)

## Step 1: Install `pywin32`
`pywin32` is the Python package for Windows Extensions, including Component Object Model (COM) support. It can easily by installed via pip:

```python
pip install pywin32
```

## Step 2: Open an Echoview instance on your machine
This establishes a COM connection between Python and Echoview. This is the first step in any Echoview COM script, whether you are [creating a new .EV file](#step-3-create-a-new-ev-file-and-load-raw-data) or [opening/editing an existing .EV file](#step-4-open-an-existing-ev-file).
```python
import win32com.client
import os

# Open Echoview COM connection
EvApp = win32com.client.Dispatch("EchoviewCom.EvApplication")
```

## Step 3: Create a new .EV file and load .raw data
Once you've established a COM connection, you can create a new .EV file.
```python
# Create a new EV file
EvFile = EvApp.NewFile()
```

### Step 3.1: Add .raw data to your new .EV file
Add .raw files to `Fileset 1` in Echoview by pointing Python to a user-specified directory where the .raw files that you would like to add are stored or adding all the .raw files in your current directory.

=== "User-Specified Directory"

    ```python
    from glob import glob
    
    # Set the directory where you want to add .raw files from
    target_dir = r"InsertPathHere" # replace this with the path to your directory
    raw_files = sorted(glob(os.path.join(target_dir, '*.raw')))

    # Add the .raw files to `Fileset 1` in Echoview to create a new .EV file
    for file_path in raw_files:
      EvFile.Filesets.Item(0).Datafiles.Add(file_path)
    ```

=== "Current Directory"

    ```python
    from glob import glob
    
    # Add all the .raw files in your current directory
    raw_files = sorted(glob(os.path.join(os.getcwd(), '*.raw')))
    
    # Add the .raw files to `Fileset 1` in Echoview to create a new .EV file
    for file_path in raw_files:
      EvFile.Filesets.Item(0).Datafiles.Add(file_path)
    ```

## Step 4: Open an existing .EV file
To open an existing .EV file, simply point Python to the path where it is stored.
```python
EvFile = EvApp.OpenFile(r"InsertPathHere.EV") # replace this with the path to your .EV file
```

## Step 5: Save your .EV file
To save your new .EV file or to change the name of your existing .EV file, use `SaveAs()`. To simply save changes made to your existing .EV file, use `Save()`.

=== "SaveAs()"

    ```python
    # Save your .EV file as "New.EV"
    EvFile.SaveAs(r"New.EV") # replace this with the name you want to assign to your new .EV file, including the path to the directory you want to save it to (if different from the current directory)
    ```

=== "Save()"

    ```python
    # Save changes to your existing .EV file without changing the name
    EvFile.Save()
    ```

## Step 6: Close your .EV file
Run this after saving your .EV file to close Echoview, or run it on its own to close Echoview without saving any changes made to your .EV file.
```python
# Close Echoview
EvFile.Close()
```

## Step 7: Close your `COM` connection with Echoview
After you are finished accessing Echoview through `COM`, close the connection.
```python
# Close Echoview COM connection
EvApp.Quit()
```
