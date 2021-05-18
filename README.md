# FinalProject

## The Legend of Zelda (NES)
Legend of Zelda was released in 1986 for the NES. It was a critical title for Nintendo, because the game sold an unprecedented amount of units. The game is an early example of a metroidvania-style game; it relied on exploration of an open area in order to find a set of keys that let you progress further and further into the game world. The original game had an Overworld that had a number of different areas, in which 9 separate dungeons, or separate areas to explore that ended in a final boss were hidden. 

![image](https://user-images.githubusercontent.com/60623457/118228099-a3e85980-b457-11eb-9cec-fb1108662af6.png)

## My Version
Since I didn't have neither the resources nor the skill to complete a remake of the entirety of the original game, I have decided to focus on implementing the absolute basics of a 2D Zelda game. I used the Unity engine to create the game, and because of this project I managed to get a grasp on said platform. The biggest issues I encountered were all specific to Unity itself, although finding a proper smaller-scale version of Legend of Zelda to implement was somewhat a problem as well. I will attempt to explain most of my implementation tactics through screenshots of the Unity editor, and some of the C# scripts that are used.
### Player
To implement my player, I needed to create a game object with a specific PlayerMovement, and CamperaMovement script, in order to let user input control the movement of my player, and in order to make the camera follow said player. I also needed the player sprite to change depending on the state of the player, as well as the action that the player is taking. On top of changing the sprite, the player object should also create box colliders that will serve as collision detectors with our environment, box colliders for hitboxes of displayed attacks, and a box collider for players own hitboxes. For this purpose, I created an emtpy Unity object called Player, and attached the following attributes to it:

<img width="268" alt="Zrzut ekranu 2021-05-18 o 8 13 06 AM" src="https://user-images.githubusercontent.com/60623457/118648931-e8a22680-b7b0-11eb-97f0-81093b0ead54.png">

The player object, on top of the required transform and sprite renderer attributes, also has the Rigidbody 2D and Box Collider 2D components. These create a collidable area that can either act as a physical body or a trigger. In this case, the Box Collider acts as the feet of the player, colliding with any object it encounters. An example of the trigger functionality of a Box Collider can be seen in the objects that are the children of the Player object:

<img width="262" alt="Zrzut ekranu 2021-05-18 o 8 12 22 AM" src="https://user-images.githubusercontent.com/60623457/118649334-636b4180-b7b1-11eb-87bb-094a7f256794.png">

Here, the 4 Box Colliders act as triggers for player attacks. The colliders are currently grayed out because they are inactive, but they become active when the player attacks.

### PlayerMovement
My PlayerMovement script handles the state of the player, player movement, and player animation flags. The possible states are saved in the enum outside of the class:
```C#
public enum PlayerState
{
    walk,
    attack,
    interact,
    stagger
}
```
The `Update()` function, which gets called around 30 times a second, is the place where all of the player movement, state shifts, and animations occur:
```C#
void Update()
{
    //Reset Player Change
    change = Vector3.zero;
    change.x = Input.GetAxisRaw("Horizontal");
    change.y = Input.GetAxisRaw("Vertical");

    //Handle Attack
    if (Input.GetButtonDown("attack") && currentState != PlayerState.attack
        && currentState!=PlayerState.stagger)
    {
        StartCoroutine(AttackCo());
    }

    //Handle Walking
    else if (currentState == PlayerState.walk) {
        UpdateAnimation();
    }
}
```
The coroutine that's started when an attack is supposed to be performed is the timed animation and handling of the attack itself. It is executed along other code that runs simultaneously, so that the world keeps moving when the player attacks. Similarly, the knockback effect a player experienced when hit by the enemy is also handled though a coroutine.

##### Camera Movement and Animations
The script that controls the camera movement moves the position of the camera object if the player moves. The edges of said movement are controlled by preset variables that are later altered through box colliders that correspond to area transitions, in which case the max and min points of the camera are modified.

The animations are handled through in-unity functionality called Animation, which lets you craft the animations to be displayed, and the Animator, which acts as a controller of which specific animations should be displayed. This is controlled through blend trees that display different sprites based on the values of the change variable in the player object.

### Player and Enemy Health
Player Health is managed through a type of Observer pattern, which separates the call of a function from its execution. The function sends an alert to the Observer that a certain action happened, and then the Observer executes any of the code that has "subscribed" to said event happening. The values of both Player and Enemy health are stored in special C# scripts that inheret from ScriptableObject, which save the state of said script between executions, which means both Player and Enemy health data can be stored between multiple scenes and certain data can be even saved between executions of said program.
The actual value of the player's health gets stored in scriptable objects, or separate objects that don't appear in the scene and can easily store useful information that gets retained through different scenes, or even through different instances of the program running.

### Environments
Each level environment is split into two separate layers: the Ground and the Collisions, a pair of Tilesets, or canvases onto which one can paint with preset assets called Tiles. The Ground is a Tileset that does not collide with the player, while the Collisions Tileset does. Both Tilesets get covered by the Player sprite.

<img width="257" alt="Zrzut ekranu 2021-05-18 o 8 25 03 AM" src="https://user-images.githubusercontent.com/60623457/118650586-a24dc700-b7b2-11eb-99f2-14ac4a3dcc57.png">

The Objects GameObject houses all of the items that need to have the 3D effect, as well as any of the premade game items like signs and enemies. The 3D effect is created through a script that switches whether the sprite covers the player, so that when the player is in front of the object, the player covers the bush, while if the player is behind the object, the object covers the player. 
