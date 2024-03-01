# February 2024 Update

<img src="https://raw.githubusercontent.com/NatickGames/natickgames.github.io/main/assets/02-24-Update/ChestInAmbassadorOffice.png" alt="Ambassador Office">

Hey internet people! I wanted to give an update for the development of Valantis, for February 2024. It’s been a very full month when it comes to development, as I’ve been trying to get as much done as I can before March (I’ll explain why at the end of the update). 

This month has a lot of updates in a few different categories, but lets start with the most eclectic one. Late January I had a small playtest where I had someone play a full session of the game for the first time. I had to track some things on pen and paper for them, but the goal was to see how the basic loop felt. I got some great feedback from that test and the theme of this month was 1. Addressing issues and improvements that were discovered or suggested during that playtest, and 2. Putting some of the mechanics I was tracking on pen and paper into the code of the game. I’m very excited to say that now, at the end of February, the game can be played in a relatively full session, with most of the work that’s left being content, polish, and metagame type things. (Not that those three things won’t be a huge undertaking)

## UI/UX Updates

### Ability Keywords

The playtest brought up a number of things that I wasn’t clearly communicating to players in the UI, one of which were the ability descriptions. They were a bit long and paragraph-y, and I needed a way to make them more readable. I did a lot of things to this end this month, creating range diagrams, adding information that was hidden onto the ability tooltips, etc. But the biggest one was adding keywords. 

<img src="https://raw.githubusercontent.com/NatickGames/natickgames.github.io/main/assets/02-24-Update/KeywordTooltips.png" alt="Keyword Tooltips for the Levitate Ability">

If you’ve played games like Hearthstone or Slay the Spire, you’ll be familiar with these. They serve a similar function informationally to what status effects do for complex mechanics. They essentially take a mechanic or family of them and distill them down into a simple label that can be used as shorthand. The cost of this easy shorthand is that now you have terminology new players have to learn before it’s helpful to them. To mitigate that, games like Hearthstone actually give your tooltips a tooltip when a keyword is referenced in them, giving you secondary information on what the keyword means if you forget. I’ve basically ripped that concept exactly and implemented it here. Now something like status conditions will include a description off to the side of the main tooltip, explaining what that condition does in a nutshell. 

### Predictive Turn Order

Some of the feedback I got was that it was hard to plan out your turns. There was no indication of who was going to go before whom next turn, and so enemies seemingly randomly would get two turns before you went again sometimes, which made it really hard to know who would die first, whether you needed to run or press your attack, etc. With permadeath characters especially, I’m very concerned with ensuring you can at least tell you’re in danger before dying without any ability to respond, so this was something I needed to come up with a good solution for. 

I think the solution came in two parts, one was a UI issue, the other a surprising mechanical one. In the moment, I said something like “Oh, well there’s a logic to the turn order, but I’m probably just not displaying enough information to make that clear, I’ll add something to the UI. But when I went to go remind myself what that logic was I found, to my chagrin, a TODO saying “Come up with some sort of turn order logic” next to some code that essentially just made the turn order completely random. Whoops.

So that had to go. It was time to come up with something. I ended up settling on a risk-reward system that I think is going to add strategic depth to your decisions. Your character has a certain number of action points that are used for moving, attacking, armor, etc. The three main types, Offensive, Utility and Defensive are given to you at a rate of 2 for every stat point you put into their stats. The other, General AP, is a wildcard that can be used in place of any of the others, and is given at a ratio of 1:1 with your stat points. This has all been in the game already, and is a key component in determining your character build and their capabilities.

What’s new, is using these action points as your initiative for turn order as well. So, barring any AP buffs from rare items, all player characters, and most NPCs have the same number of stat points. Meaning, if we keep the same AP ratios of 2:1 or 1:1 for General AP, most characters will have the same amount of “Initiative points” as the player. This is a hidden stat, but can easily be counted by looking at your AP. Turn order for the next turn is now decided by the amount of Initiative you have left over from your previous turn. So if you unleash a barrage of attacks or move a long distance and deplete your AP one round, you’ll go much later in the turn order next turn than characters who saved their AP last turn. This adds a risk/reward to using all your available actions in a turn, gives some active defense to players when they’re in trouble (save your AP and defend yourself this turn to ensure you can attack/run before the enemies next turn as well)  and it provides more importance to abilities that play with the AP economy. 

<img src="https://raw.githubusercontent.com/NatickGames/natickgames.github.io/main/assets/02-24-Update/PredictiveTurnOrder.png" alt="An example of what the predictive turn order looks like on a turn where a few characters went before you.">

So far in testing it myself this has been a valuable rule change, and has fit the style of gameplay and tactics I’m hoping for, which is that there is a lot of depth, but you aren’t sitting there every turn crunching a bunch of numbers to determine exactly what to do. In service of that, there’s a new predicted turn order UI for next round that shows up under the current round order on the UI. It only includes characters in vision range who have already taken their turns, but that means if you go near the end of a turn, you’ll have a lot of information available to you about who you are likely to go before/after next turn. I’m contemplating putting the initiative numbers up on the screen but I’m not sure if I want to do that or keep it vague.

### Character Creation Updates

I also went and spruced up character creation a bit. Adding a randomize appearance option, a visual menu for setting your appearance, and adding the new Questline selection screen (Which I'll talk about next month). With some of the other updates made this month, I’m finding myself creating a lot more characters for testing, and that led to a new character creation feature that I think will help new players as well: Default Builds. 

<img src="https://raw.githubusercontent.com/NatickGames/natickgames.github.io/main/assets/02-24-Update/AppearanceSelector.png" alt="Appearance Selection Screen">

Each combination of callings now has a default build that’s pre-populated when you create a character of that type. Players who want to customize can just swap out abilities and stat points and create a custom build like normal, but I designed the default builds to reflect the flavor and general strengths of the two combined classes, so hopefully they’ll be a decent enough build for new players to hop right in with before they’re comfortable enough to experiment.

<img src="https://raw.githubusercontent.com/NatickGames/natickgames.github.io/main/assets/02-24-Update/DefaultBuild.png" alt="The default build for the Aquasense/Deadeye Combination">
(Default build for Aquasense/Deadeye Combination)

## Death and the Loop

So up until this point, I haven’t had the core game loop implemented yet. I was working mostly on getting systems up and running. This month, I’ve started to put the final gameplay loop elements in place, notably permadeath and the escalation/egress system.

You can think of a session of Valantis in a couple different ways. In some ways, it’s like loading into an extraction game, but more PvE focused than something like Tarkov or Hunt: Showdown. You’ve got a mission, you try to get it done or at least make progress, and get out before your character dies and you have to start over with a new one. The meta progression goal is to finish character’s questlines in order to unlock new callings (classes), weapons, and appearance options. 

In other ways, it’s like a co-op roguelike, or like logging into an MMO with friends to play a single quest and log out/play another quest. I want to make individual sessions be self-contained, short adventures, but have them move towards larger character goals as well.

<img src="https://raw.githubusercontent.com/NatickGames/natickgames.github.io/main/assets/02-24-Update/DeadGuard.png" alt="Whoops, I killed a guard.">

Permadeath allows that loop to have some stakes, and encourage you to try various class combos and builds. Different questlines will encourage you to befriend factions or try content you haven’t tried on other characters, and will lend towards different playstyles.

So this month, permadeath was implemented, and I spruced up character death in general to include looting. Now tiles actually have space on them blocked by bodies, and the bodies disappear after a few turns to push you to loot while you can, as well as ensure the map doesn’t gain any permanent dead ends from bodies piling up.

<img src="https://raw.githubusercontent.com/NatickGames/natickgames.github.io/main/assets/02-24-Update/Looting.png" alt="Looting">
(Preliminary Loot dialogue. This happened to be a super rare item!)

In order to make sure you don’t just play the same map for hours, and to keep play sessions short and with some tension, the escalation system kicks in after some time (currently round 30 for most escalations) and starts to raise the stakes on the map in some way tied to your characters questline. This gives your character a story-thematic reason why they can’t just wander around this area forever, and pushes the game session to a conclusion. It also creates a very dangerous situation for you to find yourself in on a permadeath character if you try to stretch the map too far. There’s a number of other design benefits that escalations provide, specifically around making maps and content able to be more dynamic during the parts of the game before the escalation kicks in, but maybe I’ll write a separate article about the system if there’s interest. Let me know.

<img src="https://raw.githubusercontent.com/NatickGames/natickgames.github.io/main/assets/02-24-Update/EgressPortal.png" alt="A room with a player character and an Egress Portal">

Finally, in order to allow you to survive an escalating map, you need a way to extract. I’ve placed egress portals around the map during generation, that you can use to escape, much like the extraction points in various extraction games. These are fairly simple for now, but there may be space to expand on this in the future if the game needs it. I’ve tried to space them out where you’re never too many turns away from a portal, but the don’t appear on the map outside of your vision range, so you need to either keep the location of nearby portals you’ve passed in your mind as the game comes closer to escalation, or you might have a frantic last few rounds as you search for an opportunity to extract.

## What’s Coming in March?

I know I mentioned I’ve been working on the quest system this month as well, in fact that’s been a large portion of the work this month, but I’m going to be working on it in March too, and this has been a long update so I’ll save quests for next time. 

<img src="https://raw.githubusercontent.com/NatickGames/natickgames.github.io/main/assets/02-24-Update/QuestTeaser.png" alt="Quest Tracker UI Teaser">

Speaking of next time… 

My wife and I are expecting a baby this month 😊. This obviously means I’ll be pretty busy and sleep deprived for a while, so while I’m committing to still working on the game next month when I get a spare moment, it’s not going to be top priority, and it will mostly be more quest work, and various fixes and improvements on existing systems and mechanics. March’s update may be light, or it may come sometime in April, or be combined with April’s update. Overall though, I want to put out an update like this each month to catalogue where Valantis development is at over the year.

Until whenever next time is, thanks for taking a look at Valantis! You can find forums for Valantis on the Tarodev or FishNetworking discord servers if you want to chat about the game or anything else. 

- Dekori