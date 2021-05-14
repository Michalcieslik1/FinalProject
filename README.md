# FinalProject

## The Legend of Zelda (NES)
Legend of Zelda was released in 1986 for the NES. It was a critical title for Nintendo, because it sold an unprecedented amount of units. The game is an early exam-ple of a metroidvania-style game; it relied on exploration of an open area in order to find a set of keys that let you progress further and further into the game world. The original game had an Overworld that had a number of different areas, in which 9 separate dungeons, or separate areas to explore that ended in a final boss were hidden. 

![image](https://user-images.githubusercontent.com/60623457/118228099-a3e85980-b457-11eb-9cec-fb1108662af6.png)

## My Version
Since I didn't have neither the resources nor the skill to complete a remake of the entirety of the original game, I have decided to focus on implementing the absolute basics of a 2D Zelda game. I used the Unity engine to create the game. 
### Player
To implement my player, I needed to create a game object with a specific PlayerMovement, and CamperaMovement script, in order to let user input control the movement of my player, and in order to make the camera follow said player. I also needed the player sprite to change depending on the state of the player, as well as the action that the player is taking. On top of changing the sprite, the player object should also create box colliders that will serve as collision detectors with our environment, box colliders for hitboxes of displayed attacks, and a box collider for players own hitboxes. 
#### PlayerMovement
My PlayerMovement script handles the state of the player, player movement, and player animation flags. The possible states are saved in the enum outside of the class:
``C#
public enum PlayerState
{
    walk,
    attack,
    interact,
    stagger
}
``
The `Update()` function, which gets called around 30 times a second, is the place where all of the player movement, state shifts, and animations occur:
``C#
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
``
