Setting Up NPCs &  Quest Lines
---

Talking NPC Vendors allows you to create permissions unlockable through their conversation with your players.

To get started you will want at least two NPCs already made with their own Conversation files.

It's easiest to do this both in game as admin and with access to server files.

1️⃣ Creating the NPCs
---
Stand where you want the NPC to be, including the way you want them to face

type /talking_npc add NameThem - Using the name you want them to be associated with. You will be able to change all this in the data file.


2️⃣  Creating their Conversation Files
---

In Data/TalkingNPC open TalkerSpawns.json

Find your newly created NPCs

It will start by listing the name you gave them, this is the file name.

then in "Conversation File": "" you can put their Name again, and this will Create Their Conversation File

ie "doc brown": {

  "Conversation File": "Doc",

This is also where you can further customize the NPC

If you plan on giving them their own Kit, go ahead and put their name again in there.

Then when you make the kit, name it the same thing and it will already be linked

Create the conversation file for all the NPCs in your Quest Line


3️⃣ Starting the Permission Chain
---

In Data/TalkingNPC/Conversations, begin with the NPC that will REQUIRE a permission to speak with.

We will use Jax as our Example. Jax requires you to have talked to Doc Brown first.

 If you are doing more than two NPCs, Its easiest to start with the LAST one in the chain and work to the initial NPC who does not require anything to speak with,

(Else when you try to load the NPC you are working on, it will say the Permission doesnt exist.

Which you could just ingore if youd rather build the quest logically lol.) 

Open the Last NPC in the chains Conversation file.

For the Example, this would be Jax's


 
In the Response that you want the requirement for, defines its permission

"Needs Permission (null = No)": "talkingnpc.NewPermission", 

Include talkingnpc. 

ie: "Needs Permission (null = No)": "Talkingnpc.TalkedToDoc"

Save it, then open the next person that will give them the permission

This would be Doc Browns in our Example


4️⃣ Moving up the Chain
---

For the NPC that will GRANT the permission:

Find the point in the conversation you want them to Grant the player the Perm.

Ie

 "6": 

    "Message": "Well, take care partner, go and tell em Doc sent ya!",

    "Message Display Time (seconds) - Only used if there are no responses": 2.0,

    "Responses": [

      {

        "Message": "I will! See you later.",

        "Needs Permission (null = No)": null,

        "Player Commands": [],

        "Server Commands": [

          "o.grant user $userID Talkingnpc.TalkedToDoc" ]...}

In Server Commands use the command o.grant user $userID Talkingnpc.NewPermission you defined in 3️⃣

Save and Reload and your NPCs are now linked!


5️⃣ If you are using multiple NPCS
---

In the same file you would then find the point you would want to require speaking to the next NPC towards the initial before unlocking

and repeat from 3️⃣

