# Godot Engine notes

These are notes regarding doing basic boilerplate stuff with the Godot Engine.

## GDScript snippets

### Check a function exists
```
if node.has_method(nameString):
	# do stuff
```

### Check node exists without error
```
node.get_node_or_null(path)
```

### Instance a Prefab in code
```
# Import scene type
var my_prefab_type = preload("res://prefabs/my_prefab.tscn")

# create an instance
func foo:
    # 'new' instance
    var obj = my_prefab_type.instance()
    # object must be placed in the node tree
    add_child(obj)
```

### Teleport Node2D

```
# eg
func _process(delta):
	var mPos := get_viewport().get_mouse_position()
	set_global_position(mPos)
```

### Toggling Node states
```
# visibility
node.show()
node.hide()

# script calls
set_process(false)
set_physics_process(false)
set_process_input(false)
```

### Array
```
# declare
var _arr = []
# push
_arr.push_back(obj)
# pop
var i:int = _arr.find(obj)
_arr_.remove(i)
# reset
_arr.clear()
# length
_arr.size()
# count - number of times an entry appears in the array
_arr.count()
```

### Dictionary

```
var my_dir = {} # Creates an empty dictionary.
var points_dir = {"White": 50, "Yellow": 75, "Orange": 100}
var another_dir = {
    key1 = value1,
    key2 = value2,
    key3 = value3,
}
```
Access:
```
var x = dictionary[x];
var y = dictionary.y
```
To add extra keys just assign directly

Compare
```
dir1 = {"a": 1, "b": 2, "c": 3}
dir2 = {"a": 1, "b": 2, "c": 3}

func compare_dictionaries():
    print(dir1.hash() == dir2.hash()) # Will print true.
```

### Node Tree traversal/usage

Get Root of current:
```instance()```

Get current scene root
```var current_scene = get_tree().get_current_scene()```

Load new scene as level
```get_tree().change_scene("res://path/to/scene.tscn")```

### Groups

Add node to any number of groups via node -> signals/groups panel.

Via code:
```add_to_group("group_name")```

Call a function on all group members:
```get_tree().call_group("group_name", "function_name")```

Get an array of all nodes in group
```var nodes = get_tree().get_nodes_in_group("group_name")```

### Misc snippets

#### Get window and screen sizes
```
var real: Vector2 = OS.get_real_window_size() # window size
var scr: Vector2 = OS.get_screen_size() # actual monitor res
```

### Collision

#### Get object layer

Returns the NUMBER of the layer, not the name!
```object.get_collision_layer()```

#### Gotchas

If using is_on_floor():
gravity must always be applied even when on ground
otherwise is_on_floor will fail if move_and_slide
has no y component into the floor!

#### Raycast

```
var space = get_world().direct_space_state
var origin:Vector3 = self.global_transform.origin
var dest:Vector3 = Vector3()

# all bits on - I prefer to register what I DON'T want
# to detect
var mask = -1
# ignore a layer
mask &= ~IGNORE_BIT
var ignoreList = [self]

var result = space.intersect_ray(origin, dest, ignoreList, mask)
if result:
	# use
	# result.position
	# result.normal
```	

## GDScript - Language elements

### Absent features

These are common language features not present in gdscript (no comment on whether this is a good or bad thing):
* ++ or --
* switch
* private (everything is public)
* static variables. const instead of var is supported though eg
```
const DEG2RAD = 0.017453292519
const RAD2DEG = 57.2958
```
Does it need to be added to an auto-load node to access from other scripts?
functions CAN be static however? Presumably all data must be passed in via arguments however?

### Concrete typing

Concrete class references cannot access their super class:
```
func _on_body_enter_a(_body:PhysicsBody2D):
	# will error on reading .name, only sees functions
	# on PhysicsBody2D and not Node or Node2D
	print("Body " + _body.name)

func _on_body_enter_b(_body):
	# no concrete class, will work fine
	print("Body " + _body.name)
```

### Useful extra keywords

* $ can be used as shorthand for get_node
eg ```get_node("NodePath").foo``` becomes ```$NodePath.foo```
* PI, TAU, INF (infinity), NAN
* Base types null, bool, int, float, String. Take that javascript. A proper int type.
* Looks like Bitwise ops fully supported

### ToString
str(variable)
eg
```var txt = "Count is: " + str(count)```

### Globals

Globals are handled via an Auto load singleton.
Once a global script is setup in Project Settings -> Autoload you can use its name to access it
eg if you create a 'globals' auto load script, you can do this:
```var degrees = radians * com.RAG2DEG```

Thought it would be a faff but really isn't.
http://docs.godotengine.org/en/latest/getting_started/step_by_step/singletons_autoload.html

* Project menu -> Project Settings
* Autoload tab
* Add script (click singleton)

### Signals

#### Custom signal
```signal my_signal(arg1, arg2)```

#### Connect Signal
```targetNode.connect("signalName", self, "methodName")```

#### Emit
```self.emit_signal("signalName", arg1, arg2)```

## Application Node layouts

root
	- globals (autoload singleton)
		- children of globals scene
		- main menu etc
		- Screens that shared by game scenes, eg score screen.
	- game scene (the primary scene to load as set in project settings)
		- world
		- ents
		- ...etc

## Example Application Node layout

root (base type Node)
    autoload scene
    autoload scene
    autoload scene
    current level scene
        level node
        level node
        level node


## GUI

Types
Button
    CheckBox
    CheckButton
    ColorPickerButton
    MenuButton
    OptionButton
    ToolButton
LinkButton
TextureButton

HScrollBar
VScrollBar
ScrollContainer

## Links

### Scripting

#### General
https://docs.godotengine.org/en/3.2/getting_started/scripting/gdscript/gdscript_basics.html

Info on exports to editor
https://docs.godotengine.org/en/3.1/getting_started/scripting/gdscript/gdscript_basics.html#exports

#### API Classes
https://docs.godotengine.org/en/3.2/classes/index.html

#### Explanation of 'Servers' Architecture
https://docs.godotengine.org/en/stable/tutorials/optimization/using_servers.html


#### Error Codes
http://docs.godotengine.org/en/3.2/classes/class_@globalscope.html#enum-globalscope-error

#### Input
https://docs.godotengine.org/en/3.2/tutorials/inputs/mouse_and_input_coordinates.html

### Collision detection
http://docs.godotengine.org/en/latest/tutorials/physics/physics_introduction.html
http://docs.godotengine.org/en/latest/classes/class_kinematiccollision2d.html

### Pausing
https://docs.godotengine.org/en/3.2/tutorials/misc/pausing_games.html

### Singleton/Autoload (with example of a static scene loader)
https://docs.godotengine.org/en/3.2/getting_started/step_by_step/singletons_autoload.html

### Data Saving
https://docs.godotengine.org/en/3.2/classes/class_configfile.html#class-configfile
https://docs.godotengine.org/en/3.2/tutorials/io/saving_games.html

### Procedural Geometry Generation
https://docs.godotengine.org/en/3.1/tutorials/content/procedural_geometry.html
https://docs.godotengine.org/en/stable/tutorials/content/procedural_geometry/index.html
https://godotengine.org/qa/26722/how-do-i-generate-a-mesh-from-code-in-godot

### GDNative
https://stackedboxes.org/2017/08/20/trying-out-gdnative/
https://www.josephcatrambone.com/?p=1056#setup
https://github.com/godotengine/godot/issues/14856
https://godotengine.org/qa/57254/gdnative-vs-godot-engine-module-whats-the-best-for-codebase

> GDNative works on desktop and mobile. Web isn't supported but it's being worked on. It is easier to share and update separately (so it's nice for plugins). By its nature and state, GDNative is a bit more unsafe than modules, some issues were reported about crashes and memory leaks so if it works it doesn't necessarily mean it works correctly. Watch out for these and report/fix if you can. Also, GDNative behaves like scripts, so the same limitations apply. Watch out for the version of Godot as well, your bindings must always be up to date, even for minor versions (they are said to be forward compatible but sometimes an ABI incompatibility can sneak through, while API remains compatible).

> A module, on the other hand, is much simpler in terms of setup (no bindings, no C++to-C-to-C++ backend), and works everywhere as long as your C++ code doesn't call into APIs not found on some platforms. It also allows you to define your own built-in classes and benefit from tighter access to the engine. Although, for a module you have to compile the entire engine together. The simplicity makes it a more stable option but it can be a burden to share or waiting for it to compile, and you have to use SCons.

> In both cases you have to compile for all platforms you want to support, and you have to interface with the engine while it's possible to make your logic part not depend too much on it.

> I can't give a personal preference because I'd be biased, I haven't used GDNative for a very long time, only worked using modules. If GDNative works for you so far I'd say keep going, and if you have problems it may help improving the bindings.


### Godot Engine Internals
https://godotengine.org/article/godot-3-renderer-design-explained

### Tutorials

Reddit post listening written and video tutorials
https://www.reddit.com/r/godot/comments/an0iq5/godot_tutorials_list_of_video_and_written/


Third party gdnative tutorials:
https://gamedevadventures.posthaven.com/using-c-plus-plus-and-gdnative-in-godot-part-1

More comprehensive "first game"
https://godot.readthedocs.io/en/latest/getting_started/step_by_step/your_first_game.html
FPS - pretty feature complete as far as fundamentals are concerned
https://docs.godotengine.org/en/3.2/tutorials/3d/fps_tutorial/index.html
Ultra basics - Flappy bird clone
https://generalistprogrammer.com/tag/godot-3-2d-tutorial/


### Editor

Cheatsheet of shortcuts
https://github.com/godotengine/godot/issues/4875

https://docs.godotengine.org/en/3.2/tutorials/3d/environment_and_post_processing.html
https://docs.godotengine.org/en/3.2/tutorials/3d/high_dynamic_range.html
https://docs.godotengine.org/en/3.2/tutorials/3d/baked_lightmaps.html


# Custom Builds

## overview docs

[https://docs.godotengine.org/en/stable/contributing/development/compiling/introduction_to_the_buildsystem.html]

## Targets

editor
template_debug
template_release

## other options

dev_build=yes
defines 'DEV_ENABLED', disables optimisations, generates debug symbols. does not define 'NDEBUG'

debug_symbols=yes

-j4
specifies thread count. By default will use max - 1 so will affect anything demanding whilst compiling.

## platforms

```scons platform=list```

android
javascript
linuxbsd
server
windows

## examples

### Install scons

```python -m pip install scons```

### Basic compile

```scons platform=windows```

### build templates

```scons platform=windows target=template_release arch=x86_64```



