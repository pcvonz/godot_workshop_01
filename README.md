#Godot Workshop

##The editor

###Creating a project

1. Click new project on the side, create a new folder and put your project in
there.

2. Click on your project

###The editor interface

##Create a new node

Before we create a new node, let's learn some vocab. A node is the basic element for creating games in Godot. Scenes are compositions of Nodes. 

![scene](http://docs.godotengine.org/en/stable/_images/tree.png)

 Let's add a node to the scene! Click the plus button on the scene outliner and search for <code>Node2D</code> and add it to the scene. 

![scene outliner](http://i.imgur.com/KznrVDO.png)

Now let's add a sprite to the scene. Click the plus button again and add look for the <code>Sprite</code> node. Notice that when you add the sprite node to the scene it becomes the child of Node2D. Every scene you create is going to be a tree of nodes. 

Below the scene outliner there is a panel labeled <code>Inspector</code>. With the sprite node selected, navigate to the row labeled texture and click where it says "<\null>".  Load the image "wall.png". 

##Navigating in the editor window

Some useful useful shortcuts:

* With an object selected, ctrl + drag rotates an object
* Arrow keys can move objects (useful if there is a lot of stuff in the editor window). 
* Shift + arrow keys moves an object 10 pixels. 
* For real precision, use the inspector to set scale, position, rotation, etc. 
* Locking objects is also useful for cluttered scenes. 

##Creating a tilemap

Select the <code> Sprite </code> node again and add another node. This time we'll add a <code>StaticBody2D</code>. There are two other types of physics bodies in Godot, we'll go over them later.

Now, with the <code>StaticBody2D</code> selected add a <code>CollisionObject2D</code> to the scene. Something slightly confusing about this object is that it's really only present in the editor. It's used as a helper object for editing and positioning collision shapes. 

Select the <code>CollisionShape2D</code> and click assign a rectangle shape to the object in the Inspector. Edit the bounds in the main editor window, make sure it's tight on the sprite. 

We have our Tileset ready, so let's export it. 

1. Go to scene > Convert To... > TileSet
2. Name the object: tileset.res
3. Save this scene: scene > save scene as > tileset.tscn

Now we can build a bit of the stage in. Open a new scene > scene new scene and a Node2D as the base again. Underneath Node2D add a <code>TileMap</code>. In the inspector set the Tile Set to the file you just exported. Set the size to 16x16. Then, with the TileSet selected start drawing in the editor!

##Kinematic Character

So, for brevity sake we will be using the Kinematic character from the Godot Demo. If you'd like to learn more about how to construct one of these yourself, then reference [this page in the documentation.](http://docs.godotengine.org/en/stable/tutorials/2d/kinematic_character_2d.html)

Create a new scene. Scene > new scene. Add a <code>KinematicBody2D</code> node to the scene and name it player (double click the node name). Underneath it add a <code>Sprite</code> and a <code>CollisionShape2D</code>. Make sure all the nodes are direct children of player. Load the godot Icon as the player, in the inspector scale it down to a fourth of it's size.

Now we're going to attach a script to the player. With the player node selected, scroll all the way down the inspector pane. At the bottom there is a script field, click it and load the player.gd script. Now, if you click the play scene button: ![play scene](http://i.imgur.com/ji58XpB.png), the character should fall. 

##Instancing scenes

One of the most powerful concepts about Godot are these modular scenes. Navigate back to our level scene where we drawing with the TileMap. Click <code>Node2D</code> and then click this button in the scene outliner: ![instance scene](http://i.imgur.com/lFGf7Tv.png) and load the player scene. 

Lock the TileMap so we wont accidentally click it in the editor window and then move the player to position of the scene where they will fall safely to the ground. Now click the play scene button.

The player falls down and nothing else happens. The cool thing about this is that this scene is still editable, if you navigate back to the player scene and make the player double the size, those changes will reflect on all scenes where the player is instanced. This is really great for working collaboratively and since Godot was made to be used with version control it makes using services like github very easy.

##Setting up controls

You'll notice that your player doesn't move when you use the arrow keys. Let's investigate this a little bit. Navigate to the player screen. Click the script icon next to Player node. on line 36-38 we have our problem. Godot is looking for the actions "move_left", "move_right", and "jump", but we haven't added those to the input list yet. 

Open up the project settings. Scene > Project Settings. Navigate to the Input Map tab. At the top type in and hit enter:

* move_right
* move_left
* jump

Scroll down where are new actions have been added. Click the plus button on the right and assign some controls to them. Now try playing the level scene. Navigate to the level scene and press the play scene button. We have a moving character!

##Animating a door

Let's create a simple door animation. First create a new scene and then add a `Node2D` and name it "Wall". Underneath the Wall add a `Sprite` and a `KinematicBody2D`. Underneath the `KinematicBody2D` add a `CollisionShape2D`  with a rectangle shape to it. Load the same wall image into the `Sprite`. 

Scale the sprite in the y direction to 4 times it's height. Scale the CollisionShape2D to cover the door. 

Add an `AnimationPlayer` node as a child of Wall. Create a new animation by clicking the blank page button in the AnimationPlayer pane (below the main editor window). Once you create a new animation, you'll notice that everything in the inspector now has a key next to it. This means that those properties are able to be animated. 

Select the Wall node and click the key symbol next to position in the inspector. You'll notice that a blue dot has been added to the animation. Drag the play head to the end of the animation and then set the door's ending position to something like -64. Click the key next to the position again. We now have an animated door!

##Scripting

So, now is the part where some of you might get lost. Until this point, we were able to mostly avoid scripting. 

In Godot we use a built in scripting language called gdscript which is syntactically similar to Python. The Godot developers implemented Lua, Python, and Squirrel in the past but found that building their own custom language was faster and more efficient. [More on that in the docs.](http://docs.godotengine.org/en/stable/reference/gdscript.html) 

###The Class docs

In the scripting view you can bring up the class documentation at anytime. Let's bring up the documentation for an Area2D. In the top pane of the scripting view click "Classes"

###Signals

Before we connect our signals let's add an `Area2D` as the child of our Wall. Add a `CollisionShape2D` underneath the `Area2D` and make it look like so:

![Wall](http://i.imgur.com/i9FUfmx.png)

Now right click the Wall node and add a script to it. Add to the ready function:

```python
func _ready():
	get_node("Area2D").connect("body_enter", self, "open_door")
	
func 	open_door(body):
	get_node("AnimationPlayer").play("anim")
```

Now let's navigate back to the level scene and instance our new door. Play the scene and test it out. You might notice a few oddities, if the player leaves the area and enters it again the door restarts the animation from the top. Let's fix that.

```python
# For convenience
var anim

func _ready():
	get_node("Area2D").connect("body_enter", self, "open_door")
	get_node("Area2D").connect("body_exit", self, "close_door")
	anim = get_node("AnimationPlayer")
	
func open_door(body):
	if not anim.is_playing() and anim.get_current_animation_pos() == 0:
		anim.play("anim")

func close_door(body):
	if not anim.is_playing():
		anim.play_backwards("anim", .2) #the .2 is for blend time, makes it blend with the open animation
```

That's a pretty solid door!

##Rays, RigidBodies, and groups oh my!

Now, I think it'd be great if our player got pelted with something right as they went through the door we just made.

By now you should know the drill. Create a new scene add a `Node2D` rename it to arrow trap. Add a `RayCast2D` and a `Position2D` as children. 

In the inspector of the ray set the `Cast To` variable to (200, 0), untick "rigid body" in the `Type Mask`, and turn the ray on by ticking "enable". Save this scene as "arrow_trap". 

Now create another new scene and add a `RigidBody2D` and a `Sprite` and `CollisionShape2D` as children. In the sprite, load the arrow image. For the collision shape, add a rectangle shape and match it up to the arrow.  Save this scene as "arrow"

Now the code: 

```python


extends Node2D

var ray
var arrow
var arrow_pos
var time_elapsed = 0
var arrow_speed = 500
var arrow_rot = 0

func _ready():
	set_process(true)
	#Loading the arrow scene we just creeated
	arrow = preload("res://arrow.tscn").instance()	
	#set up our convenience variables
	ray = get_node("RayCast2D")
	arrow_pos = get_node("Position2D").get_global_pos()
	arrow_rot = arrow.get_rot()

func _process(delta):
	if(ray.is_colliding() and not ray.get_collider() == null):
		#We need to duplicate the arrow object, we'll be firing 
multiple 
arrows from this
		var temp_arrow = arrow.duplicate()
		#Add the new arrow as a child to the to our arrow trap scene
		add_child(temp_arrow)
		#Orient our arrow in the world
		temp_arrow.set_global_pos(arrow_pos)
		temp_arrow.set_rot(arrow_rot)
		#Fire in the correct direction
		temp_arrow.set_linear_velocity(Vector2(cos(get_rot()), 
sin(-get_rot()))*arrow_speed)
```

Now to test it! Instance the arrow_trap into our level and place it in a position where our unwitting protagonist will encounter it. 

That didn't work as expected did it? There are a few problems we need to address:

* Limit the amount of arrows that are fired. Right now it's firing every frame draw.
* Make it so the arrow only fires at our character. 

Let's adjust our code! (changes are commented)

```python

extends Node2D
var ray
var arrow
var arrow_pos
var arrow_speed = 500
var arrow_rot = 0

var shoot_speed = 1 #The delay between arrows
var time_elapsed = 0 # Adding variable for total time elapsed

func _ready():
	set_process(true)
	arrow = preload("res://arrow.tscn").instance()	
	ray = get_node("RayCast2D")
	arrow_pos = get_node("Position2D").get_global_pos()
	arrow_rot = arrow.get_rot()

func _process(delta):
	time_elapsed += delta #Delta is the change in time between every frame, if we add these up we get the total time elapsed
	if(ray.is_colliding() and not ray.get_collider() == null and 
time_elapsed > shoot_speed): #Add a test to see if it's time shoot another arrow
arrows from this
		var temp_arrow = arrow.duplicate()
		add_child(temp_arrow)
		temp_arrow.set_global_pos(arrow_pos)
		temp_arrow.set_rot(arrow_rot)
		temp_arrow.set_linear_velocity(Vector2(cos(get_rot()), 
sin(-get_rot()))*arrow_speed)
		time_elapsed = 0 #reset the timer
```


It's getting there, but we still haven't addressed the issue of when to fire the arrow. This is where groups come in. Navigate to your player scene. Next to the inspector tab you'll notice another tab called "groups". Add the parent node (named player) to the group "players".

Now, on the line where we checked if time_elapsed was greater than shoot\_speed, add: `and ray.get_collider().is_in_group("players")` . So the whole statment should look like this:

```python
	if(ray.is_colliding() and not ray.get_collider() == null and 
time_elapsed > shoot_speed and ray.get_collider().is_in_group("players")): #add 
a check for group
```

There! The arrows now only fire when the ray detects the player.