# GuideBot

This is a telegram bot to guide users where they want to drive within a limited region.

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes.

### Prerequisites

To install this project you should have installed `pip` in order to install all the packages needed.

### Installing

The `.zip` file contains the following files:

* `bot.py` - The module that interacts with telegram to guide the user.

* `guide.py`- The module that contains everything related to route calculations and maps.

* `requirements.txt` - The file containing all the packages needed.

* `icon-flag.png` - An icon for the `guide.py` that indicates the destiny.

* `icon-location.png` - An icon for the `guide.py` that indicates the user location.

* `README.md` - The documentation file.

Now let's see how to install all the packages. The `osmnx` library usually gives problems, so here you can install it other ways:

* [install-osmnx-conda](https://anaconda.org/conda-forge/osmnx). If you use anaconda.

* [install-osmnx-pip](https://pypi.org/project/osmnx/). If you have all `osmnx` dependencies.

To install the other packages needed in this project use the next command on the terminal being at the same directory of `requirements.txt`.

```
$ pip install -r requirements.txt
```

You may create a `virtualenv` to execute this project, to know more about how to install and create virtualenvs visit [virtualenv](https://docs.python.org/3/library/venv.html).


## Introducing to guide.py

This module is the core of our bot. Is responsible of calculating efficient routes and working with graphs of places and map plots.
Here you have an explanation of the main functions included in `guide.py`

##### **download_graph(place)**

This function downloads the graph of the place you enter as parameter.

```python
G = guide.download_graph("Barcelona")
```

##### **save_graph(graph, filename)**

This function saves a graph in a file in `.gpickle` format at your system.
```python
guide.save_graph(G, "Barcelona-Graph")
```

##### **load_graph(filename)**

This function loads a graph in a `.gpickle` format file from your system.
```python
G = guide.load_graph("Barcelona-Graph")
```

##### **current_location(location)**

This function generates a `.png` image containing a map with a marker on location.
And returns the name of the file.
```python
location = (41.40674136015038, 2.1738860390977446)
file = guide.current_location(location)
open(file, 'rb')
```

##### **get_directions(graph, source_location, destination_location)**

This function computes the shortest route from location to destiny on the graph, where location and destiny are tuples of coordinates (lat,lon) inside the bounds of the graph.
```python
source = (41.40674136015038, 2.1738860390977446)
destination = (41.4034789, 2.1744103330097055)
route = guide.get_directions(G, source, destination)
print(route)
```

##### **plot_directions(graph, source_location, destination_location, directions, filename, width=400, height=400)**

This function plots the directions from source to destination on a map and returns the name of a `.png` image containing it.
```python
source = (41.40674136015038, 2.1738860390977446)
destination = (41.4034789, 2.1744103330097055)
route = guide.get_directions(G, source, destination)
file = plot_directions(G, source, destination, route, "map")
open(file, 'rb')
```

## Introducing to bot.py

This module is in charge of establishing communication with the telegram user, through commands our bot interacts with the user in all the ways the user has.
By way of summary, it is responsible for carrying out guided routes at the user's request.

### Commands

Here you have an explanation of the commands that your bot will incorporate.

##### **/start**

This command is used to start the conversation with the bot, it does a simple introduction.

##### **/help**

This command is responsible for showing the user all the commands and functionalities of the bot.

##### **/where**

This command sends a photo of the current location of the user on a map to him. 

##### **/go _place_**

This command starts a new route to the mentioned place. The user will receive guiding messages as long as he has not arrived.

##### **/recompute**

This command recomputes the route to the place the user was going. It requires that there have been an active route. It's useful when the user gets lost.

##### **/cancel**

This command cancels the current route of the user. But the last place he was going to will remain saved as long as the user don't start a new different route, it has been made this way in case the user wants to recompute the previous route.

##### **/author**

This command shows the authors of this project.

### Features

##### **For whom skip nodes of the route**

The guiding bot includes this feature which is goal is to verify that the user is following the marked route despite having skipped a checkpoint, and to act accordingly.

##### **Recomputing route**

This feature is very useful if the user is lost or simply wants to recompute the route on his own whenever he wants. Just by pressing `/recompute` the bot computes a new route to the previous place the user was goingo to. Or if the bot detects that you are going far away, he will give you the option.

<center><img src='example-recompute.png'></center>

##### **Reminder**

If the bot realizes that the user has not send messages or shared the location for a few minutes while he had an active route, the bot will give the user the option to either `/cancel` the route or explain how to share location.

<center><img src='example-reminder.png'></center>

##### **User friendly**

In order to make the communication with the bot faster and less tedious to the user, there are incorporated this awesome features.

First we have adapted the commands so that they can be used without putting the counterbar in front of them. This is writting `where`instead of `/where`.

<center><img src='example-commands.png'></center>

The `/go` command is also adapted to detect if the user wants to go any place just by messaging the place (if it's a real place).

<center><img src='example-go.png'></center>

Another remarkable feature is that we have added inline buttons in some cases to know what the user wants to do in response to some situations (for example when getting lost) and execute the action the user requests (either recompute the route or cancel the route). 

<center><img src='example-buttons.png'></center>

## Customization

In this section you will get an idea of the various options you have to customize both modules `guide.py` and `bot.py`.

### guide.py

##### Icons

The icons showed at the photos on both functions `current_location`and `plot_directions` are customizable, this means you can use your own icons as long as they follow the appropriate format `.png`.

<left><img src='icon-location.png'></center>
<center><img src='icon-flag.png'></right>

##### Constants

* FIND_DST: The radius used to look for the nearest edge when optimizing the route.

* FARTHEST_NODE: The maximum distance to consider a node out of the graph.

> Note: You can modify this constants (on the top of the code) being aware of its consequences.

### bot.py

##### Map

You can use the map you like to delimit the place where the bot can guide users.
By default we use the "Barcelona one, but with the function `download_graph`of `guide.py`you can download the one you want and changing it at the end of the code.

##### Constants

* PLACE: The graph of the place the bot is guiding through.

* NEAR_DST: The distance to consider the user is near a checkpoint.

* AWAY_DST: The distance to consider the user maybe is getting lost from the checkpoint.

* FAR_AWAY_DST: The distance to consider the user is getting far away and lost.

* MAX_TIME: The time to consider the user is not sharing anymore its location with us.

* N: The number of checkpoints the user can skip to ensure the route continues.

> Note: You can modify this constants (on the top of the code) being aware of its consequences.

## Tested using

* [OpenStreetMap](https://www.openstreetmap.org/) - The web used to see routes.
* [GPS-Simulator](https://play.google.com/store/apps/details?id=com.rosteam.gpsemulator) - A tool to simulate a fake location of the phone.

## Authors

* Dani Gomez

* David Pujalte