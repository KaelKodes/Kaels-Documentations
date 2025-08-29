
Table of Contents
---

1 Features

2 Install

3 Permissions

4 Quick Start

5 Admin Commands

6 Data & File Layout

7 Conversation Files

8 Message & Response Schema

9 Command Placeholders

20 GiveItem helper

11 Currency & Pricing

12 Cooldowns

13 Optional Integrations

14 Invisible Vending (CustomVendingSetup)

15 Map Markers (MarkerManager)

16 Kits

17 Console Test Command

18 Config Options

19 Troubleshooting

20 Developer Hooks




1. Features
---

Spawn persistent, talkable NPCs (shopkeeper-conversationalist prefab).

Flexible conversation trees that show messages and clickable responses.

Each response can:

Run player and/or server commands.

Charge currency (ServerRewards RP, Economics $, or specific items).

Jump to another message (or close the UI).

Apply per-player or global cooldowns.

Optional: open invisible vending UIs, apply kits, show map markers.

Auto-closes the dialog if the player moves too far (configurable).




2. Install
---

Place TalkingNpc.cs in oxide/plugins/.

Restart the server or run oxide.reload TalkingNpc.

(Optional) Install companion plugins to unlock features:

CustomVendingSetup – invisible vending machine UIs.

Economics – money currency.

ServerRewards – RP currency.

Kits – outfit NPC inventories.

MarkerManager – map marker support.




3. Permissions
---

Grant your admins the manage permission (configurable):

oxide.grant group admin talkingnpc.admin


Default permission: talkingnpc.admin (configurable; see Config Options).




4. Quick Start
---

Create a conversation file at
oxide/data/TalkingNpc/Conversations/village_shop:

{
  "0": {
    "Message": "Welcome, traveler!",
    "Responses": [
      {
        "Message": "Let me see your wares.",
        "Player Commands": ["OpenVending village_shop_vm"],
        "Next Message": null
      },
      {
        "Message": "Buy a bandage (5 RP).",
        "Price": 5,
        "Currency": { "Item ID": 0, "Skin ID": 0 },
        "Server Commands": ["GiveItem 1796682209 1 0 Bandage"],
        "Next Message": 1,
        "Cooldown": 60
      }
    ]
  },
  "1": {
    "Message": "Anything else?",
    "Message Display Time (seconds) - Only used if there are no responses": 2.0,
    "Responses": []
  }
}


(Optional) If you use CustomVendingSetup, create
oxide/data/TalkingNpc/VendingMachines/village_shop_vm.

Spawn an NPC at your feet (in chat, as an admin):

/talking_npc add Bob village_shop


(Optional) Bind NPC to the nearest monument so it survives map-coordinate shifts after wipes:

/talking_npc add Bob village_shop true


Remove later:

/talking_npc remove Bob




5. Admin Commands
---

Requires the admin permission.

Add NPC at your position

/talking_npc add <Name> <ConversationFile>


Example:

/talking_npc add Bob village_shop


Add NPC bound to nearest monument

/talking_npc add <Name> <ConversationFile> true


Remove NPC

/talking_npc remove <Name>




6. Data & File Layout
---

The plugin stores data under oxide/data/TalkingNpc/:

TalkerSpawns.json — all saved NPC spawn records.

PlayerCooldown.json — per-player cooldown states.

GlobalCooldown.json — global cooldown states.

Conversations/<filename> — your conversation trees.

VendingMachines/<filename> — invisible vending setups (if using CustomVendingSetup).

Conversation & vending filenames don’t need an extension. The plugin reads/writes JSON internally.




7. Conversation Files
---

A conversation file is a JSON object keyed by Message IDs (uint, e.g., "0", "1").
Each message has a Message string, optional Message Display Time … (used when there are no responses), and an array of Responses.




8. Message & Response Schema
---


Message (string) — text shown to the player.

Message Display Time (seconds) - Only used if there are no responses) (float, optional) — auto-closes after X seconds if Responses is empty.

Responses (List<Response>)

Message (string) — button text.

Needs Permission (null = No) (string, optional) — permission required to see/click this.

Player Commands (List<string>, optional) — run as the player.

Server Commands (List<string>, optional) — run on server (via rust.RunServerCommand).

Next Message (null = Close UI) (uint?, optional) — where to go next; null closes.

Price (int, optional) — amount to charge before executing.

Currency (object, optional):

Item ID (int)

Skin ID (ulong)

Insufficient Funds Message (null = Close UI) (uint?, optional)

Cooldown (int seconds, optional) — per-player cooldown after clicking.

Server Wide Cooldown (bool, optional) — if true, cooldown applies globally for everyone.




9. Command Placeholders
---

These tokens will be replaced in Player Commands and Server Commands:

playerID or $userID → player SteamID

$displayName → player name

$npcName → current conversation’s NPC name (conversation file name)




10. GiveItem helper
---

You can directly grant items from a response command using:

GiveItem <itemId> <amount> <skinId> <custom name...>


The custom name is everything after the first 3 numbers.

Works in both Player Commands and Server Commands.




11. Currency & Pricing
---

If a response has Price > 0, the plugin checks Currency.Item ID:

0 → ServerRewards RP (requires ServerRewards)

1 → Economics balance (requires Economics)

Any other item ID → looks for that item (and skin) in the player’s inventory

If the player can’t afford it, the response jumps to Insufficient Funds Message (if set), otherwise the dialog closes.




12. Cooldowns
---

Per-player cooldown: set Cooldown to N seconds.

Global cooldown: also set Server Wide Cooldown: true.
The button shows a timer overlay and is disabled until the cooldown expires.




13. Optional Integrations
---
I find Timed Permissions to be useful.
Ive also linked Talking_NPCs with Epic Loot successfully.




14. Invisible Vending (CustomVendingSetup)
---

Add vending configs under oxide/data/TalkingNpc/VendingMachines/<file>.

List vending files for an NPC spawn (see Talker options below).

In a response, call:

OpenVending <VendingFile>


This opens the invisible vending machine that matches <VendingFile>.

If a listed vending file exists but CustomVendingSetup isn’t installed, vending for that NPC is disabled and a warning is logged.




15. Map Markers (MarkerManager)
---

Per-NPC marker settings:

"Umod Marker Manager": {
  "enabled": true,
  "displayName": "Bob’s Shop",
  "radius": 0.4,
  "colorMarker": "00FFFF",
  "colorOutline": "00FFFFFF"
}


When enabled, the plugin creates/removes a persistent map marker for the NPC.




16. Kits
---

If you set "Kit": "<kitname>" on an NPC, the plugin strips the NPC’s inventory and applies the kit (requires Kits).




17. Console Test Command
---

Preview conversations without spawning an NPC:

talkingnpctest <ConversationFile> <NpcDisplayName>


If you run it from the server console, you can optionally pass a player ID first to open it for someone else:

talkingnpctest <playerID> <ConversationFile> <NpcDisplayName>




18. Config Options
---

oxide/config/TalkingNpc.json:

{
  "Settings": {
    "Admin Permission": "talkingnpc.admin",
    "Auto-Close Dialog Distance": 2.6
  },
  "Version": "1.4.1"
}


Admin Permission — the permission string required for manage commands.

Auto-Close Dialog Distance — distance (in meters) beyond which the talk UI closes automatically (default 2.6).

Talker (NPC) Options (saved per spawn)

Each saved NPC (in TalkerSpawns.json) can include:

{
  "Name": "Bob",
  "Conversation File": "village_shop",
  "Position": { "x": 0.0, "y": 0.0, "z": 0.0 },
  "Rotation": { "x": 0.0, "y": 180.0, "z": 0.0 },
  "Monument": "Outpost (or nearest binding name)",
  "Kit": "npc_vendor",
  "NpcUserID": 0,
  "CallOnUseNpc": false,
  "UseCommand on dialog open closes dialog": false,
  "UseCommand on dialog open": ["say Hey $displayName!"],
  "Vending Machine Configuration Files": ["village_shop_vm"],
  "Umod Marker Manager": {
    "enabled": true,
    "displayName": "Bob’s Shop",
    "radius": 0.4,
    "colorMarker": "00FFFF",
    "colorOutline": "00FFFFFF"
  }
}


Prefab used: assets/prefabs/npc/bandit/shopkeepers/bandit_conversationalist.prefab
NPCs are given a stable userID and auto-respawn on server boot.




19. Troubleshooting
---

“You can not use this command” → Your account lacks the admin permission; grant talkingnpc.admin (or whatever you set in config).

Dialog closes when walking away → Increase Auto-Close Dialog Distance in config (default 2.6).

Vending doesn’t open → Ensure CustomVendingSetup is installed, the vending filename exists, and your response uses OpenVending <same name>.

Buttons show a timer / greyed out → A per-player or global cooldown is active.

“Invalid conversation file …” → Ensure the filename exists under data/TalkingNpc/Conversations/ and loads as valid JSON.




20. Developer Hooks
---

OnTalkingNpcCommand(BasePlayer player, string[] args)
Called when a command string containing talking_npc is executed from a response.

OnTalkingNpcSpawnCheck(BasePlayer player, string[] args)
Used by internal “vehicle spawn” helper if you integrate spawning logic elsewhere.

UI action: TalkingNpcUIHandler.OnResponseClicked (internal button callback)
