# Multiplayer

## Sharing script
The script PlayerController is accesed by both the clients and the server so it needs to be synchronized in order to avoid multiple instances
trying to acces the same variables at the same time and in order to have all the clients seeing the same variables being in the same state.

A method to synhronize/share the state of a variable is to add a Syncvar hook:

```csharp
public class Health : NetworkBehaviour{
    [SyncVar(hook = "OnChangeHealth")]
    public int currentHealth = maxHealth;
}
```

Now the server has the authority to change the health on the player using the script. All the players also share this variable so that each instance
has it's own currentHealth variable.

Another way to do it is to make each player/client instance control its own health variable instead of letting the server do it, but then the changes to the health variable state would only be visible on the host.

## Server Client acces
When both the host/server and all the clients are sharing the same script we need to control what the clients should be able to do like shooting and what the server should be able to do like updating the health and synhronizing it on all the clients.

We can use some build in variables from the inherited NetworkBehaviour:

```csharp
public class Health : NetworkBehaviour{
    public void TakeDamage(int amount)
    {
        if (!isServer)
            return;
    }  
}
```
NetworkBehaviour.isServer is being used to make sure that i.e. take damage (which is only relevant for the players) is not being called from the host instance.

## NetworkManager
The network manager has the base elements for creating a network aplicable game. The base elements are the NetworkManager and the NetworkManagerHUD which is a simple UI menu from which you can host or connect to a server if you are a client. The NetworkManager controls the state of the game by including server and client instances that you can specify by adding NetworkIdentity and setting the LocalPlayerAuthority flag to true if you want that gameobject to become a client. You also need to add the gameobjects prefab to the network manager Spawn info in order for it to be assigned as a client instance.
