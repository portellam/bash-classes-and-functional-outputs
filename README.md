# Bash "Classes" and Functional "Outputs"
### Status: Work-in-progress

Bash script programming is useful. A developer can automate tasks, install software, make changes to a system, etc.
However, Bash is not without it's challenges and shortcomings.

Two features missing in the Bash language are **classes**, and functional **outputs**. These may be implemented in an unconventional way.

Bash supports functions with input variables (**arguments**), and variable-types **reference** and **inherited**. The following documentation is an overview of how to implement a **class** and functional **outputs**.

## Table of Contents
- [**1. Functions**]
  - [**1.a. Arguments**]
  - [**1.b. Differences in Bash**]
  - [**1.c. References**]
  - [**1.d. Inherited Variables**]
  - [**1.e. Functional Output Example**]
- [**2. Classes**]
  - [**2.a. Definition**]
  - [**2.b. Differences in Bash**]
  - [**2.c. "Class" Example**]
  - [**2.d. Concerns**]

## Contents

### 1. Functions
#### 1.a. Arguments
#### 1.b. Differences in Bash
#### 1.c. References
#### 1.d. Inherited Variables
#### 1.e. Functional Output Example

### 2. Classes
#### 2.a. Definition
#### 2.b. Differences in Bash
#### 2.c. "Class" Example
#### 2.d. Concerns

## Old Contents

### Classes (or lack thereof):
#### Definition:
**Classes** is a feature in both compiled (ex: C++, Java) and scripted languages (ex: JavaScript, Python). Classes are used to organize logic in chunks more read-able by a developer, and for greater security. 

Functions are available in Classes as logic. Class logic is organized - purposeful and privileged.

#### Use:
Purpose: a program uses Classes to reduce redundancy. 

Privilege: a program uses Classes to prevent unintended behavior, secure protected information, and restrict access.

Bash is limited to privileges with **read-only** as a variable attribute. Unlike other languages, a Bash script process has access to most or everything anything previously declared within the current scope. In other words, any function can call previously declared variables and functions.

Privileges will not be a focus of this overview.

You may be familiar with **Libraries** or Dependencies, which is another feature. A **Library** is not unlike a Class. A library contains logic, but it is not necessarily made by the developer of the current program. It has logic which may allow the developer to make his/her own logic to be more higher-level (ex: the famous quote "standing on the shoulders of giants").

#### Why:
If a variable is declared more than once, a variable is updated; each consecutive declaration updates a variable.

In Bash, the concept most similar to a Class is known as a **Source**. Whenever a Source is first called, any declarations within that script file or Source are also run. In this case, a Source is no different than executing the script file.

This can be an issue. For example, several script Sources contain variables. Each script Source depends on the next, and first some at the "top" also depend on the "last" Source. Here is an example:

- `A` requires `B` and `C` and `F`.
- `B` requires `C` and `D`.
- `C` requires `E` and `F`.
- `D` requires `F`.

Given the example, `C` is called two times, and `F` is called three times! If a variable in `F` were to be made read-only at any-point before another Source call, then an error would occur in the Bash interpreter. This would likely stop execution.

**Warning:** Going further, if two Source files called each other (ex: `F` calls `A`), then the Bash interpreter would hang and eventually cause a *Stack overflow*.

In conclusion, if a developer  wishes to create Source files like Classes in Bash, you must become creative with how declarations are called. See the [example above](#class-example).

### Functions
#### Definition:
A **function** is a a code block defined by a variable, with a relationship between input and output. In Bash, it is by default input-less (unless with the use of arguments).

**Arguments** are input variables of the function which can be passed. In Bash, by default the data-type of an argument (or variable) may be undefined.

For example C#, is a **strongly-typed** language. Strongly-typed means a variables data-types are declared ahead of time.

Another example, JavaScript, a **weakly-typed** language. Weakly-typed means variable data-types are flexible, and are interpreted at run-time.

**Bash is a weakly-typed language.**



### References
#### Definition:
**References** are a variable which copies the value of another variable. Also, any consecutive change after will update the other (from a previous scope) variable.

#### Use:
References are useful to make Bash functions act similar to functions from other languages. In Bash, functions do not have outputs, except for return codes (ex: `0` for success, `1` for general failure, up to `255`).

#### Why:
**References** are useful for scenarios where a variable can be declared in one Source file. From there, other source files (without variables) can contain functions with arguments (ex: references), allowing for class-like behavior. This is illustrated in the [code example above](#example).

### Inherited
#### Definition:
**Inherited** variables are like References. However, they have one difference: a consecutive updates will **not** update the other variable (of a previous scope).

#### Use:
Limited.

Given a reference's use-case, an inherited variable can be used as a placeholder, until a condition is met, and reference can be used to update the previous scope variable.

### Reference and Inherited Example
The function below is an (uninspiring) example of the operation of **References** and **Inherited** variables. 

#### 1. Here is the function we wish to run:
```
  #
  # DESC:   Is variable an int.
  # $1:     the name of the variable.
  # RETURN: Always return 0.
  #
    function is_int_var
    {
      echo -e '${1}'"\t= '${1}'"

      local -n ref="${1}"
      local -I inh="${1}"

      echo -e '${ref}'"\t= '${ref}'"
      echo -e '${inh}'"\t= '${inh}'"
      echo

      echo "Set "'${ref}'" to '2'"
      ref=2
      echo -e '${ref}'"\t= '${ref}'"
      echo -e '${inh}'"\t= '${inh}'"
      echo

      echo "Set "'${inh}'" to '3'"
      inh=3
      echo -e '${inh}'"\t= '${inh}'"
      echo -e '${ref}'"\t= '${ref}'"
      echo

      return 0
    }
```

#### 2.a. Here is the input variable:
```
  declare -i my_new_int=0
```
#### 2.b. and the terminal output before code execution:
`echo -e '${my_new_int}'"\t= '${my_new_int}'"`

will return

`${my_new_int}   = '0'`

#### 3. Here is the code output:
```
  is_int_var "my_new_int"
  ${1}    = 'my_new_int'
  ${ref}  = '0'
  ${inh}  = 'my_new_int'

  Set ${ref} to '2'
  ${ref}  = '2'
  ${inh}  = 'my_new_int'

  Set ${inh} to '3'
  ${inh}  = '3'
  ${ref}  = '2'
```

#### 4. Lastly, here is the terminal output after code execution:
`echo -e '${my_new_int}'"\t= '${my_new_int}'"`

will return

`${my_new_int}   = '2'`

#####
lorem ipsum
Given this, more responsibility is placed upon the developer to make Bash code more robust. Familiarize youself with Bash syntax and you will understand ([Bash scripting cheatsheet](https://devhints.io/bash)).
