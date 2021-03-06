# State Machine Designer

A quick and simple state machine package to create your own states machine with Python

## Installation

Install via PyPI:

```shell
$ pip install state-machine-designer
```

or clone the project and install it manually using:

```shell
$ pip install .
```

## Quick start

Bellow, you can find examples to create your State Machine as a object attribute or define sigle
states and iterate with them

### State

Before starting using the State Machine object, we are going to demonstrate how you can create your own states.

#### Decision Function

First of all, all states must has a decision function, which is going to return the name of the next state, or, if nothing changes, the name of the current state.

```python
import random

def decision_function():

    # If number is even the next state will be "state1"
    if int(random.randrange(10)) % 2 == 0:
        return "state1"

    # Otherwise "state2"
    else:
        return "state2"
```

#### Routine Function (optional)

Routine function is used to implement some verification or action every time that the State Machine is in this specific state.

```python
def decision_function():
    print("Doing my routine functions")
```

#### Exit function (optional)

Exit function is a function or object method that will be executed when the State Machine is leaving that state.

```python
def exit_function():
    print("Bye bye state1")
```

#### Entry function (optional)

You can add an entry function to each state. This functions will be performed just once, when the State Machine enter in this state.

```python
def entry_function():
    print("Hello state2")
```

### State Machine

Once you have created your states, you can add them to the State Machine by passing the State object or creating states directly on the StateMachine. You can see how to do it using the examples bellow:

```python
from state_machine import State, StateMachine

def state1_decision_function():
    # For simplicity, it will just return the next state
    return "state2"

def state2_decision_function():
    # For simplicity, it will just return the next state
    return "state1"

def state1_routine_function():
    print("State1 says: Doing my stuff")

def state1_exit_function():
    print("State1 says: See ya in the next time, StateMachine")

def state1_entry_funcion():
    print("State1 says: Hii, how is going, StateMachine?")

def state2_routine_function():
    print("State2 says: Doing my stuff")

def state2_exit_function():
    print("State2 says: See ya in the next time, StateMachine")

def state2_entry_funcion():
    print("State2 says: Hii, how is going, StateMachine?")


state_machine = StateMachine()

# Ex 1: Creating a State instance and adding to the State Machine
state1 = State(
    name='state1',
    decision_function=state1_decision_function,
    routine_function=state1_routine_function,
    exit_function=state1_exit_function,
    entry_function=state1_entry_funcion
)

state2 = State(
    name='state2',
    decision_function=state2_decision_function,
    routine_function=state2_routine_function,
    exit_function=state2_exit_function,
    entry_function=state2_entry_funcion
)

state_machine.add_states(state1, state2)

# Ex 2: Creating a state with the State Machine (uncomment to use it)
"""
state_machine.create_state(
    state_name='state1',
    decision_function=state1_decision_function,
    routine_function=state1_routine_function,
    exit_function=state1_exit_function,
    entry_function=state1_entry_funcion
)
"""

# Before running the State Machine, you shall define the initial state
state_machine.initial_state = 'state1'

# Ok, we can run it
print("\nFirst execution")
state_machine.run()

print("\nSecond execution")
state_machine.run()
```

The terminal output will be:

```
First execution
State1 says: Hii, how is going, StateMachine?
State1 says: Doing my stuff
State1 says: See ya in the next time, StateMachine

Second execution
State2 says: Hii, how is going, StateMachine?
State2 says: Doing my stuff
State2 says: See ya in the next time, StateMachine
```

## Integrating StateMachine with a Python Class

This package will be more useful when you integrate it with your python classes. The state functions
will be class methods and the State Machine will be an attribute. The sample code above show you how to do it:

```python
from state_machine import State, StateMachine
from time import time, sleep
from datetime import datetime

class TrafficLight:
    red_timer_count = 10 # 10 seconds for red light
    yellow_timer_count = 2 # 2 seconds for yellow light
    green_timer_count = 5 # 5 seconds for green light


    def __init__(self):
        self.red = True
        self.yellow = False
        self.green = False
        self.timer = 0

        # Init the state machine
        self.state_machine = StateMachine()
    
        # Create the State Machine states

        # Red light state
        self.state_machine.create_state(
            state_name='red',
            decision_function=self.red_decision,
            entry_function=self.red_entry,
            exit_function=self.red_exit
        )

        # Green light state
        self.state_machine.create_state(
            state_name='green',
            decision_function=self.green_decision,
            entry_function=self.green_entry,
            exit_function=self.green_exit
        )

        # Yellow light state
        self.state_machine.create_state(
            state_name='yellow',
            decision_function=self.yellow_decision,
            entry_function=self.yellow_entry,
            exit_function=self.yellow_exit
        )

        # Define initial state
        self.state_machine.initial_state = 'red'


    def red_entry(self):
        self.timer = time()
        self.red = True
        print(f'Timestamp: {datetime.now().isoformat()}\tTraffic light state: {self.state_machine.current_state}')

    def red_decision(self):
        if time() - self.timer >= self.red_timer_count:
            return 'green'
        else:
            return 'red'


    def red_exit(self):
        self.timer = 0
        self.red = False 


    def green_entry(self):
        self.timer = time()
        self.green = True
        print(f'Timestamp: {datetime.now().isoformat()}\tTraffic light state: {self.state_machine.current_state}')


    def green_decision(self):
        if time() - self.timer >= self.green_timer_count:
            return 'yellow'
        else:
            return 'green'


    def green_exit(self):
        self.timer = 0
        self.green = False 


    def yellow_entry(self):
        self.timer = time()
        self.yellow = True
        print(f'Timestamp: {datetime.now().isoformat()}\tTraffic light state: {self.state_machine.current_state}')


    def yellow_decision(self):
        if time() - self.timer >= self.yellow_timer_count:
            return 'red'
        else:
            return 'yellow'


    def yellow_exit(self):
        self.timer = 0
        self.yellow = False 


    def start(self):
        while True:
            self.state_machine.run()
            sleep(.1)

traffic_light = TrafficLight()
traffic_light.start()
```

Terminal output:

```
Timestamp: 2020-07-26T17:00:28.211277   Traffic light state: red
Timestamp: 2020-07-26T17:00:38.344380   Traffic light state: green
Timestamp: 2020-07-26T17:00:43.463914   Traffic light state: yellow
Timestamp: 2020-07-26T17:00:45.571166   Traffic light state: red
Timestamp: 2020-07-26T17:00:55.694119   Traffic light state: green
Timestamp: 2020-07-26T17:01:00.812396   Traffic light state: yellow
Timestamp: 2020-07-26T17:01:02.921871   Traffic light state: red
```
