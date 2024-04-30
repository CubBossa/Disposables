# Disposables

The library allows you to link the lifespan of different objects.
Objects may only exist while another parent object exists, which results in a tree structure of logical dependency.
With this library, you may link the object to its parent and execute code as soon as the parent
gets disposed.

The graphic below illustrates lifetime dependencies and how a crashing plugin
will no longer claim resources beyond its lifetime if combined with this library.

![lifetimes](docs/lifetime_overview.svg)

```Java
public class MyPlugin extends JavaPlugin {
  BukkitDisposer disposer;
  public void onEnable() {
    disposer = BukkitDisposer.disposer(this);
    
    World minigameWorld = createMyPluginWorldForMinigame();
    // disable world when plugin shuts down
    disposer.register(MyPlugin.this, minigameWorld);
    
    Player player = ...;
    ChestMenu menu = createMyLobbyGUI(player);
    
    // close menu when plugin crashes
    disposer.register(MyPlugin.this, menu);
    
    // run menu close code when player quits (like save settings)
    disposer.register(player, menu);
    
    // unregister inventory listeners when not needed
    disposer.register(menu, mySpecificInventoryListener);
    disposer.register(player, mySpecificInventoryListener);
  }
  
  class ChestMenu implements Disposable {
    @Override
    public void dispose() {
      // save player settings from lobby menu here
    }
  }
}
```

## Maven

```xml
<repositories>
  <repository>
    <id>CubBossa</id>
    <url>https://nexus.leonardbausenwein.de/repository/maven-public/</url>
  </repository>
</repositories>

<dependencies>
  <dependency>
    <groupId>de.cubbossa</groupId>
    <artifactId>disposables-api</artifactId>
    <version>1.2</version>
  </dependency>
  <dependency>
    <groupId>de.cubbossa</groupId>
    <artifactId>disposables-bukkit</artifactId>
    <version>1.2</version>
  </dependency>
</dependencies>
```

