# Sponge Config Manager
A config manager for sponge plugins.
### The Goal:
Sponge Config Manager arouse from difficulties encountered on my various projects with properly configuring and updating
default configurations.  Sponge Config Manager is my attempt to simplify this processes by providing one convenient 
class to handle all my configuration needs.
### How it Works:
Sponge Config Manager was designed to be very simple to use.  You grab an instance of it with ```ConfigMangaer.getInstance();```
then you set it up by calling ```setup()``` and passing in a ```Path``` to the file you want to save/modify,
a ```ConfigurationLoader<CommentedConfigurationNode>``` to manage loading and modifying nodes, a ```Logger``` to handle
logging any error messages originating from the file I/O, and finally an implementation of the ```ConfigManager.DefaultConfigBuilder```
interface, which handles formatting the default configuration format.  `Important:` the default configuration format
will only be used if the config file in question does not already exist, otherwise the file as it exists will be loaded.
Finally you can get a reference to the config file loaded by calling ```getConfig()```.  That's it three simple method calls
and you have yourself a config file!  You can see an example of Sponge Config Manager being used below.
 
## Example Usage

```
@Plugin(id = "ConfigExample", name = "ConfigExample", version = "1.0.0")
public class ConfigExample {
   
    @Inject
    private Logger logger;
    
    public Logger getLogger() {
        return logger;
    }
    
    
    @Inject
    @DefaultConfig(sharedRoot = true)
    private Path defaultConfig;

    @Inject
    @DefaultConfig(sharedRoot = true)
    private ConfigurationLoader<CommentedConfigurationNode> configLoader;

    private ConfigManager configManager;

    private Map<String, Boolean> disabledMap;

    @Listener
    public void onServerStart(GameStartedServerEvent event) {
        
        //Grab Instance of Config Manager
        configManager = ConfigManager.getInstance();
        
        //Setup the config, passing in the default config format
        configManager.setup(defaultConfig, configLoader, getLogger(), new ConfigManager.DefaultConfigBuilder() {
            @Override
            public void build(CommentedConfigurationNode config) {
                config.getNode("sampleNode").setComment("This is a sample boolean node.").setValue(true);
                config.getNode("samples", "sampleNode2").setComment("This is a sample string node.").setValue("Hello World!");
            }
        });
        
        //Alternative method of setup using Lambda Expressions
        configManager.setup(defaultConfig, configLoader, getLogger(), config -> {
            config.getNode("sampleNode").setComment("This is a sample boolean node.").setValue(true);
            config.getNode("samples", "sampleNode2").setComment("This is a sample string node.").setValue("Hello World!");
        });
                
        //Get the config file!
        CommentedConfigurationNode config = configManager.getConfig();
    }
}
```

### Output Config File
```
# This is a sample boolean node.
sampleNode=true
samples {
    # This is a sample string node.
    sampleNode2="Hello World!"
}
```

