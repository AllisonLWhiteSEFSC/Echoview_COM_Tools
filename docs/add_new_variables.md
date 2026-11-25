# Adding New Variables to Echoview Workflows Through `COM`

Once you've established a `COM` connection with Echoview in Python and either opened an existing .EV file or created a new one, you can build your processing workflow directly in Echoview by creating new variables and editing variable properties as needed. 

## Creating New Acoustic Variables

To create new variables, you first need to reference the parent variable in your `COM` instance. This will become `Operand 1` for the new variable you create. 

```python
# Reference an existing operand variable for the new variable you want to create.
svraw = EvFile.Variables.FindByName("Fileset 1: Sv pings T1")
```

!!! note "NOTE:"
    The variable name in `FindByName()` needs to match the exact name assigned to your operand variable in Echoview, including the transducer number (eg. T1) for raw variables. See [Accessing Information About Existing Variables](#Accessing-Information-About-Existing-Variables) for information on how to create a list of existing Echoview variables in Python for ease in automating this process.
 
Once you've referenced the operand variable, you can create the new variable directly from the Python object you created using the enum value for the new operator listed in Echoview's [EOperator](https://support.echoview.com/WebHelp/How_To/Run_Echoview_Using_Scripts/COM_Objects/EOperator.htm).

!!! example "Create a Copy Variable"

    ```python
    # Create a new "Copy" variable of the operand variable `svraw`
    svcopy = svraw.AddVariable(16) # 16 is the enum value for the "Copy" operator. Change this value to the enum value for the variable you want to create listed in EOperator.
    ```

    !!! note "NOTE:"
        Assigning a name to this action creates a Python object that you can then reference when you want to change variable properties or use your new variable as an operand in downstream processing. It **_does not_** change the name of the new variable in Echoview, which defaults to the Echoview naming conventions for new variables ("Copy 1" in this example). It's highly recommended to assign a descriptive name to your new variable in Echoview by [editing the variable properties](#Editing-Variable-Properties).

For variables that require **two or more operands**, you must 
1. create Python objects for both operand variables,
2. create a Python object for your new variable, and
3. use `SetOperand` to assign all additional operands.

!!! example "Create a Single Target Detection Variable with Two Operands"
    Create a **Single target detection - split beam (method 2)** variable from a raw TS variable and set the second operand to a raw angular position variable:

    ```python
    # 1) Create Python objects to reference the two operands.
    tsraw = EvFile.Variables.FindByName("Fileset 1: TS pings T1")
    apraw = EvFile.Variables.FindByName("Fileset 1: angular position pings T1")
    
    # 2) Create a new "single target detection - split beam (method 2)" variable and associated Python object
    sed = tsraw.AddVariable(75)

    # 3) Change the 2nd operator of your new single target detection variable the raw angular position variable `apraw`.
    sed.SetOperand(2,apraw)
    ```

*When creating variables that _require_ two or more variables (such as "Mask" variables), you may not see the new variable appear in your Echoview Dataflow window until the second operand has been added. The new variable is still created in Echoview, even without a second operand, but it will be Hidden in the Dataflow until you either Show the variable in Echoview by right-clicking in your Dataflow window or until all required operands are added.

## Editing Variable Properties


## Accessing Information About Existing Variables
