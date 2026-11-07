# Specification for the web-based game "Where Do I Belong at Slipstream University?"

This document describes the design and implementation of an interactive game that allows players to have LLM-driven conversations with various NPCs. It is meant to be fun and comedic, yet also to explore interesting questions about whether it is best to find "your group" or to gain new experience interacting with people who are different from you.

## Overview of Player Experience

The player's avatar in this game is a newly arrived freshman at Slipstream University, an imaginary institution of higher learning where students from various alternate universes and genres matriculate. The style is sort of a visual novel, but driven by text-based conversations with various virtual characters. The player can have as many conversations with the available characters as they want, then end the game by choosing which dorm they want to stay in for their freshman year. Optionally, they can end the game by having the game pick for them based on an analysis of their reactions to the various NPCs.

After advancing from a title screen, the player creates a character (name and description). They are then shown some brief instructions explaining the premise of the game. After that, the core gameplay loop begins: the player can pick from a menu of characters to talk to and interact with them in a chat-based interface. They can also choose to end the game by picking their dorm and getting a brief ending sequence with some unique text based on the interactions they had.

## Game World Lore

Slipstream University sits at a junction where a lot of universes overlap, so nothing on campus quite matches anything else and nobody there finds that strange. The lore that actually matters for gameplay is the dorms, since that is what the player is choosing between, and the students, since that is who the player talks to.

### The Dorms

Rustwater Hall is a converted water tower with a wraparound porch, somewhere between post-apocalyptic salvage and an old western town. It draws scrappy, self-reliant people who fix things instead of replacing them. Its signature feature is a jukebox wired to a bank of scavenged car batteries and one very tired luck charm, which only plays songs from timelines that never happened.

Cogwright Spire is a brass observatory full of tinkerers, theorists, and anyone who enjoys a good problem more than a good party. The telescope on the top floor is pointed at other universes rather than at distant stars, and the lift is run by a bound wind spirit who will strand you between floors if you forget to say hello.

Blossom Court is loud, warm, dramatic, and relentlessly social, in the way of a slice-of-life anime with a little magical-girl sparkle on top. The rooftop garden blooms according to the moods of the residents, which makes finals week very interesting, and the vending machine in the lobby is restocked nightly by a polite ghost who takes requests.

The Deep Stacks is a gothic library dorm for the quiet, the curious, and the slightly nocturnal. The basement grows another floor whenever somebody's research is going well, and the reading candles burn all night without ever getting shorter.

Vector Keep is a castle keep run like a training academy, equal parts space opera and sports anime. The main hall is floored with gravity runes the residents can crank up or down for practice, and the trophy case is full of trophies that argue about who actually earned them.

### The Characters

Claude, please invent twelve students for the player to talk to, spread across the five dorms so that every dorm has at least two people making its case. I want good coverage of personality types, genders, and backgrounds, and I want them drawn from a wide spread of universes -- fantasy, sci-fi, post-apocalyptic, western, anime, noir, fairy tale, and whatever else fits -- using common genre tropes and archetypes rather than anyone's actual intellectual property. For each character, write a short bio, a couple of notes on how they talk, and a clear opinion about why their dorm is the right place to be and what kind of person would be miserable there. Some of them should be great advocates for their dorm and some should be terrible at it. This is the material the endings draw on, so give each one something distinct for the player to react to.

## Game Screens

The UX layout basically defines the gameplay. Each screen will have very brief instructions about the controls or about what the player is supposed to do on that particular screen. A consistent style makes all the screens feel like part of a whole (favor simplicity and readability, and encourage interaction).

### Title Screen

When the game first starts, there is a title screen that shows the name of the game plus a clever subtitle that sells the premise and the gameplay, along with an instruction to "Click to continue" (which we can also use to grab input focus if needed). Have a bit of fanfare here, such as a fade-in or something like that.

### Instructions Screen: "The Quad"

After advancing from the title screen, let's get some scrolling text that very briefly explains the goal of the game and the core game loop: talk to other students, pick your dorm! There's a button labeled "Let's go to campus!" that is always available, and the player can click it to skip the rest of the instruction scroll if they choose. The fictional wrapping here is that this is "The Quad," a central hub for going off to visit with students or, in the end, picking a dorm.

### Interaction Menu

This is the core screen of the game. Players see buttons for the available characters they can interact with. Each "button" features a character portrait, the character's name, and their dorm. There is also a non-editable checkbox (initially unchecked) that appears checked after the player has had at least one conversation with that character. Four more cards close out the list, each with a picture borrowed from a scene: "Tour a Dorm," "Pick a Dorm," "Choose My Dorm For Me," and "Back to Title Screen." If the player selects a character, they go to the Conversation Screen with that character. If they select one of the ending buttons (choose a dorm, or choose for me), they go to the ending that matches the option they chose.

### Conversation Screen

This mode is basically a chat interface. We see a scrollable vertical list of character portraits and speech bubbles. First, the NPC gives the player a little greeting: if they have never met before, the NPC welcomes them to campus; if they have talked previously, the greeting includes some context from that previous conversation. Using the `conversation.html` file in the project directory as a reference for how to implement this, the player can type what they say to the NPC. The NPC answers in very short, flavorful replies that help the player's character pick their dorm. The agents are aligned to refuse questions not related to campus life, the dorms, and (non-invasive!) personal questions about the NPC. The NPC can use the interdimensional nature of Slipstream University to phrase the refusal as a joke ("I don't think that exists in my universe," or similar). There is a button in this mode labeled "Return to the Quad"; if pressed, the NPC wraps up the conversation with a flavored "Glad you're coming to Slipstream University. Good luck picking your dorm!" and the game returns to the Interaction Menu (a.k.a. The Quad).

### Ending Screen One: Player Chooses Their Dorm

There are buttons, one for each dorm. The one they click is the one their character chooses. There is also a "Wait, I changed my mind. Back to the Quad." option. Once the player picks their dorm, they get an ending screen with a very short, scrollable story about their first few weeks on campus and how much they like their dorm (this should always be positive and never too edgy). A "Quit to title screen" button lets the player go back to the title screen and start a new game. An LLM query generates the story based on the conversations they had through the game.

### Ending Screen Two: The Game Picks the Dorm for the Player

Just like Ending Screen One, except there are no dorm buttons and no "Wait, I changed my mind. Back to the Quad." button. The game just picks the dorm for them (doing at least some inference of their personality and preferences). The ending story scroll actually has two parts that appear to be one continuous text from the POV of the player: first, an explanation of why the chosen dorm is the best option for that character, and second, a little wrap-up story about the character's first year on campus.

## Implementation

This should be a game that can be served over HTTPS and played in a browser: HTML, CSS, and JavaScript. It needs LLM queries to function, so reference the attached HTML example and Markdown document for how that works. This will be used for a game jam, so make sure you ask the user for their "team ID" — the attached example uses `team-owl`, but the user will likely have a different identifier they need for access.

Each NPC needs to have its own stored context. Use a message history buffer management strategy to retain crucial context without letting the conversation run on without bound. (One idea: after 10 turns of the conversation, the NPC says a flavored version of "Great to talk to you, but I gotta run to class. Good luck picking your dorm!")

## Deliverables

Create all needed assets for the content and presentation of the game in a single subdirectory of our project directory. I will need to supply portraits of each character, and probably some images for the title screen, the Quad, and so forth to give the player an impression of what the game world looks like. I know you can't really make great images, Claude, so one of the deliverables will be a text file that gives me a list of images to make. Please supply the file name I should use for each image, a brief description of where it will be used, and a good image generation prompt I can easily copy and paste into a chat with another agent to create the image for me. ### Image Prompt Generation

All of the image prompts need to share one art style so the finished game doesn't look like a collage. The line below is the style seed. I will edit it, and you weave whatever it says into every prompt you write for me:

ART STYLE: Detailed line work, inked illustration over matte watercolors.

Keep the rest of each prompt consistent too. Character portraits should be framed the same way every time (head and shoulders, simple background that hints at the character's home universe), and location art should share a shape and mood so the screens feel like one place. Each entry in the image list file gets a file name, one line about where the image is used, and then the full prompt with the style already baked in, so I can copy and paste it without editing anything.

Likewise, I will make some little WAV files for sounds we need for the menu — very short, non-irritating sounds for clicking a button, or anything else you need. Give me the required file names and what each sound is used for, and I will make those myself. Please make it simple for me to drop those images and WAV files into a folder somewhere in the output project folder so I can't possibly mess up what I'm supposed to do here. Thanks!
