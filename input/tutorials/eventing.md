Description: Events and the Client/Server model
Published: 2018/12/18
Category: " Beginning"
Order: 2
Author: Steven Blom

---

Events in the Scripting API
=
The vast majority of your scripts will generally be based around one of two concepts.

Either you'll be updating the world each tick using the `system.update` callback (a function that minecraft will call in **your** script to indicate that it is time to process your game logic for a single tick), or you will be reacting to *events* that happen in the system, an event can be anything that has happened in the game.

Reacting to the world
-----
In order to react to something happening in the world, you need to tell the system which event you want to listen to, and pass it a callback (a function that will be called when the event occurs). In practice, it looks something like this:

```javascript
//Server-side script
var system = server.registerSystem(0, 0);

system.initialize = function () {
    system.listenForEvent("minecraft:player_attacked_actor", onPlayerAttacked);
}

function onPlayerAttacked(eventData) {
    //This is where you perform events when a player has been attacked.
}
```

The `minecraft:player_attacked_actor` event is used to indicate that the player has attempted to hurt something else, and gives you the opportunity 
to react to that event or even perhaps, change the outcome of the event. In the case of this event, your callback will be invoked after the damage 
calculation has taken place and the entity will have already suffered the damage.

Just knowing that the event has taken place however isn't very useful, you will undoubtedly want to know which player caused the damage, and which 
entity received the damage. This information is provided to you via the first parameter passed to your callback. In the example above, it has been 
named `eventData`, but you are free to call the parameter whatever makes the most sense for your callback.

The contents of the parameter are thus:
| property          | type         | purpose                                                  |
| ----------------- | ------------ | -------------------------------------------------------- |
| `attacked_entity` | EntityObject | This is a reference to the entity that has been attacked |
| `player`          | EntityObject | This is a reference to the player that caused the damage |

`EntityObject` is used in the official scripting API documentation to describe a reference to an entity and it is used for getting and changing 
information about an entity, and although the EntityObject is usually used as-is, there are some properties of it that can be quite useful, especially 
for filtering.

| property         | type                      | purpose                                                                                                                                     |
| ---------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `__identifier__` | string                    | contains the minecraft identifier, such as "minecraft:player", or "minecraft:zombie"                                                        |
| `__type__`       | `entity` or `entity_item` | items that have been dropped into the world have a different type than mobs.                                                                |
| `__unique_id__ ` | uuid                      | The internal ID of an entity. It will be consistent between world restarts                                                                  |
| `id`             | number                    | This is an easier to compare identifier, but can change between restarts, and do not count on it being consistent between client and server |

So to put all of that into perspective, you could get the id of the player with `eventData.player.id` and the id of the attacked entity 
with `eventData.attacked_entity.id`

Knowing this however is useless unless you can do something with it.

Sending messages to the chat log
-----
So far, we've learned that we can use `listenForEvent` to receive events from the game, now let's enact a change in the world by sending our own event back
to the game. We can do that by using `system.broadcastEvent`. Let's send a chat message that tells everyone on the server that the attack happened.

```javascript
//Server-side script
var system = server.registerSystem(0, 0);

system.initialize = function () {
    system.listenForEvent("minecraft:player_attacked_actor", onPlayerAttacked);
}

function onPlayerAttacked(eventData) {
    const attackedType = eventData.attacked_entity.__identifier__;
    const playerId = eventData.player.id;
    system.broadcastEvent("minecraft:display_chat_event", "Player #" + playerId + " attacked a " + attackedType);
}
```

When you run this in-game, you should see a chat message in-game when an entity is attacked, but as mentioned, it will appear for every player on the server.





Per-Tick Updates
-----
Some times you may need to trigger something to happen in the world that is independent of anything that is happening in the world. For example, perhaps you would want to randomly perform an action 
