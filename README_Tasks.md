# Tasks to Complete
# Overview
At a high-level, we'll break down this project into a few manageable tasks.

* Task 0: Inspect the data. (`data/neos.csv` and `data/cad.json`). You already did this! 
* Task 1: Build models to represent the data. (`models.py`)
* Task 2: Extract the data into a custom database (`extract.py` and `database.py`)
* Task 3: Create filters to query the database to generate a stream of matching CloseApproach objects, and limit the result size. (filters.py and database.py)
* Task 4: Save the data to a file. (`write.py`)
As you implement these tasks, you'll unlock more and more functionality. When Task 2 is complete, you'll be able to run the `inspect` subcommand. When Task 3 is complete, you'll be able to run the query subcommand without the `--outfile` argument. When Task 4 is complete, you'll be able to run everything.

Remember, in this project you won't need to write any code that prompts the user for input - the `main.py` script will accept arguments from the command line or the interactive session and pass that information to the appropriate Python classes and functions that you create.

## Task 1: Design the objects that will store our data.
Well done! Now that we understand the project overview and our data set, it's time to start coding. The first thing we'll do is create Python objects to represent our data. In particular, we're going to create two classes in the `models.py` file:

* A `NearEarthObject` class, to represent the data for a single near-Earth object.
* A `CloseApproach` class, to represent the data for a single close approach of an NEO.
In doing so, we'll have to decide how to construct new instances of this class, which attributes from our dataset belong to each object, how to build a human-readable representation this object, and which additional methods or properties, if any, we want to include. We'll also have to plan for how these objects will interact with each other.

### Designing the NearEarthObject class
The `models.py` file contains a starting template for the `NearEarthObject` class. This class object will represent a single near-Earth object.

```javascript
class NearEarthObject:
    def __init__(self, ...):
        ...

    def __str__(self):
        ...
```
The `__init__` method is the constructor for the class. You will need to decide what arguments it should accept. If you make changes, you should also update the surrounding comments.

The `__str__` method will return a human-readable string that captures the contents of the class for a human audience. In contrast, the prewritten `__repr__` method is stylized to be machine-readable.

Each `NearEarthObject` must have attributes (or gettable properties) for the following names:

* `designation`: The primary designation for this `NearEarthObject`.
* `name`: The IAU name for this `NearEarthObject`.
* `diameter`: The diameter, in kilometers, of this `NearEarthObject`. 
* `hazardous`: Whether or not this `NearEarthObject` is potentially hazardous.
* `approaches`: A collection of this `NearEarthObject`s close approaches to Earth.
The starter code contains default values for some of these attributes - you should decide how, and if, to replace that code.

Recall that, even though every NEO in the data set has a nonempty primary designation, some NEOs have no name, and some NEOs have no diameter (it's unknown to NASA).

The designation should resolve to a string, the name should resolve to either a nonempty string or the value None, the diameter should resolve to a float (you should use float('nan') to represent an undefined diameter), and the hazardous flag should resolve to a boolean.

The `approaches` attribute, for now, can be an empty collection. In Task 2, you'll use the real data set to populate this collection with the real `CloseApproach` data.

The `__str__` method that you write is up to you - it'll determine how this object is printed, and should be human-readable. For inspiration, we adopted the following format:

```python
>>> neo = ...
>>> print(neo)
NEO {fullname} has a diameter of {diameter:.3f} km and [is/is not] potentially hazardous.
>>> halley = ...
>>> print(halley)
NEO 433 (Eros) has a diameter of 16.840 km and is not potentially hazardous.
```
In the above, `{fullname}` is either `{designation} ({name})` if the `name` exists or simply `{designation}` otherwise. As a hint, this is a great opportunity for a property named `fullname`!

#### Designing the `CloseApproach` class
The `models.py` file also contains a starting template for the `CloseApproach` class. This class object will represent a single close approach to Earth by a near-Earth object.

```python
class CloseApproach:
    def __init__(self, ...):
        ...

    def __str__(self):
        ...
```

The `__init__` method is the constructor for the class. You will need to decide what arguments it should accept. If you make changes, you should also update the surrounding comments.

The `__str__` method will return a human-readable string that captures the contents of the class for a human audience. In contrast, the prewritten `__repr__` method is stylized to be machine-readable.

Each `CloseApproach` must have attributes (or gettable properties) for the following names:

* `time`: The date and time, in UTC, at which the NEO passes closest to Earth.
* `distance`: The nominal approach distance, in astronomical units, of the NEO to Earth at the closest point.
* `velocity`: The velocity, in kilometers per second, of the NEO relative to Earth at the closest point.
* `neo`: `The NearEarthObject` that is making a close approach to Earth.
The `date` should resolve to a Python datetime, the `distance` should resolve to a float, and the `velocity` should resolve to a float.

The `neo` attribute, for now, can be `None`. In its absence, you should include a `_designation` attribute with the primary designation of the close approach's NEO. In Task 2, you'll use the real data set and this `_designation` attribute to connect the `neo` attribute to a real `NearEarthObject` instance.

You can use the `cd_to_datetime` function in the `helpers` module to convert a calendar date from the format provided in `cad.json` (e.g. "1900-Jan-01 00:00") into a Python datetime object.

The `__str__` method that you write is up to you - it'll determine how this object is printed, and should be human-readable. For inspiration, we adopted the following format:

```python
>>> ca = ...
>>> print(ca)
At {time_str}, '{neo.fullname}' approaches Earth at a distance of {distance:.2f} au and a velocity of {velocity:.2f} km/s.
>>> halley_approach = ...
>>> print(halley_approach)
On 1910-05-20 12:49, '1P (Halley)' approaches Earth at a distance of 0.15 au and a velocity of 70.56 km/s.
```

You should use the `datetime_to_str` function from the `helpers` module to format the `time` attribute to a string without seconds. This is another great opportunity for a property!

#### Testing
Make sure to manually test your implementation at an interactive interpreter. Your interactive session might look something like:

```python
$ python3 -q
>>> from models import NearEarthObject, CloseApproach
>>> neo = NearEarthObject(...)  # Use any sample data here.
>>> print(neo.designation)
2020 FK
>>> print(neo.name)
One REALLY BIG fake asteroid
>>> print(neo.diameter)
12.345
>>> print(neo.hazardous)
True
>>> print(neo)
NEO 2020 FK (One REALLY BIG fake asteroid) has a diameter of 12.345 km and is potentially hazardous.
>>> ca = CloseApproach(...)  # Use any sample data here.
>>> print(type(ca.time))
datetime.datetime
>>> print(ca.time_str)
2020-01-01 12:30
>>> print(ca.distance)
0.25
>>> print(ca.velocity)
56.78
>>> print(ca)
On 2020-01-01 12:30, '2020 FK (One REALLY BIG fake asteroid)' approaches Earth at a distance of 0.25 au and a velocity of 56.78 km/s.
```
As you progress the remaining tasks, you may have to revisit this file to adapt your implementation - that's expected!

Rubric Tip:

Check if you have error-handling code for the case in which an NEO has no `name` or no `diameter`. If there’s no name, the name attribute should be None. If there’s no diameter, the diameter attribute should probably be `float(‘nan’)`.
