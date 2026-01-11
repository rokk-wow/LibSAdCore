# SAd - Simple Addons

SAdCore is a lightweight, embeddable framework for rapidly building simple addons with consistent options and controls. Define your checkboxes, dropdowns, sliders, and buttons, and the settings UI is automatically generated with values persisted to SavedVariables. Built-in features include settings import/export, profile management, and optional zone detection for addons that need zone-based behavior.

**Note:** SAdCore is a library, not a standalone addon. It's embedded within other addons (similar to Ace3, LibStub, etc.).

## The SAd Creed
SAddons are:
- **Easy.** If a configuration setting is more complicated than a single control, it's probably wrong
- **Simple.** If a user doesn't understand your configuration without additional instructions, it's probably wrong
- **Intuitive.** If a user can't remember where they saw a particular configuration setting, it's probably wrong
- **Relevant.** If a configuration setting doesn't directly relate to the name of the addon, it's probably wrong
- **Respectful.** If a users game setting is changed just by installing the addon, it's probably wrong
- **Affirmative.** Logic should always enable, not disable (a checkbox should "Show Map", not "Hide Map")
- **Robust.** All code exists inside scoped functions. No global code exists
- **On Time.** All code should run based on events, not timers
- **Exceptional.** All rules have exceptions, especially when writting addons for WoW 😂

## Installation

### Embedding SAdCore in Your Addon

1. **Clone or download this repository** into your addon's `Libs/LibSAdCore` folder:
   ```bash
   cd MyAddon/Libs
   git clone https://github.com/rokk-wow/LibSAdCore.git LibSAdCore
   ```

   Your addon structure should look like:
   ```
   MyAddon/
   ├── Libs/
   │   └── LibSAdCore/
   │       ├── LibSAdCore.lua
   │       ├── LibSAdCore.toc
   │       ├── Addon_Example.lua
   │       ├── README.md
   │       ├── STYLEGUIDE.md
   │       └── LICENSE
   ├── MyAddon.lua
   └── MyAddon.toc
   ```

2. **Update your `.toc` file** to load SAdCore:
   
   ```toc
   ## Interface: 120000
   ## Title: MyAddon
   ## Author: Your Name
   ## Version: 1.0
   ## SavedVariables: MyAddon_Settings_Global
   ## SavedVariablesPerCharacter: MyAddon_Settings_Char
   ## AddonCompartmentFunc: MyAddon_Compartment_Func

   Libs\LibSAdCore\LibSAdCore.lua

   MyAddon.lua
   ```

3. **Initialize the addon** in `MyAddon.lua`:
   ```lua
   local addonName = ...
   local SAdCore = LibStub("SAdCore-1")
   local addon = SAdCore:GetAddon(addonName)
   
   addon.savedVarsGlobalName = "MyAddon_Settings_Global"
   addon.savedVarsPerCharName = "MyAddon_Settings_Char"
   addon.compartmentFuncName = "MyAddon_Compartment_Func"
   
   function addon:LoadConfig()
       self.config.version = "1.0"
       self.author = "Your Name"
       
       -- Example - Add Settings to Main Settings Panel
       self.config.settings.main = {
           title = "exampleTitle",
           controls = {
               {
                   type = "header",
                   name = "exampleHeader"
               },
               {
                   type = "checkbox",
                   name = "exampleCheckbox",
                   default = true,
                   persistent = true,
                   onValueChange = self.exampleCheckbox
               }
           }
       }
       
       -- Example - Add a New Child Settings Panel
       self.config.settings.examplePanel = {
           title = "examplePanelTitle",
           controls = {
               {
                   type = "header",
                   name = "examplePanelHeader"
               },
               {
                   type = "checkbox",
                   name = "examplePanelCheckbox",
                   default = true,
                   persistent = true,
                   onValueChange = self.examplePanelCheckbox
               }
           }
       }
   end
   
   function addon:exampleCheckbox(isChecked)
       self:debug(addonName .. ": " .. tostring(isChecked))
   end
   
   function addon:examplePanelCheckbox(isChecked)
       self:info("Checkbox changed to: " .. tostring(isChecked))
   end
   
   -- Localization
   addon.locale = {}
   
   addon.locale.enEN = {
       -- Main settings localization strings
       exampleTitle = "My Addon Settings",
       exampleHeader = "Example Settings",
       exampleCheckbox = "Example Checkbox on Main Settings",
       exampleCheckboxTooltip = "Tooltip for example checkbox control",
       
       -- Custom panel localization strings
       examplePanelTitle = "Example Panel Settings",
       examplePanelHeader = "Example Panel Header",
       examplePanelCheckbox = "Example Checkbox on Custom Panel",
       examplePanelCheckboxTooltip = "Tooltip for checkbox on the custom panel"
   }
   ```

## Example Controls

This example shows all available control types using the same naming convention and structure as `Addon_Example.lua`:

```lua
function addon:LoadConfig()
    self.config.version = "1.0"
    self.author = "Your Name"
    
    -- Main Settings Panel
    self.config.settings.main = {
        title = "mainTitle",
        controls = {
            -- Header
            {
                type = "header",
                name = "mainHeader"
            },
            
            -- Checkbox (persisted)
            {
                type = "checkbox",
                name = "mainCheckbox",
                default = true,
                persistent = true,
                onValueChange = self.mainCheckbox
            },
            
            -- Dropdown (persisted)
            {
                type = "dropdown",
                name = "mainDropdown",
                default = "option2",
                persistent = true,
                options = {
                    {value = "option1", label = "dropdownOption1"},
                    {value = "option2", label = "dropdownOption2"},
                    {value = "option3", label = "dropdownOption3"}
                },
                onValueChange = self.mainDropdown
            },
            
            -- Slider (persisted)
            {
                type = "slider",
                name = "mainSlider",
                default = 50,
                min = 0,
                max = 100,
                step = 5,
                persistent = true,
                onValueChange = self.mainSlider
            },
            
            -- Color Picker (persisted)
            {
                type = "colorPicker",
                name = "mainColor",
                default = "#FFFFFF",
                persistent = true,
                onValueChange = self.mainColor
            },
            
            -- Input Box with Button
            {
                type = "inputBox",
                name = "mainInput",
                default = "Enter text",
                buttonText = "mainInputButton",
                onClick = self.mainInputButton,
                persistent = true
            },
            
            -- Button
            {
                type = "button",
                name = "mainButton",
                onClick = self.mainButton
            },
            
            -- Description
            {
                type = "description",
                name = "mainDescription"
            }
        }
    }
    
    -- Child Settings Panel
    self.config.settings.advancedPanel = {
        title = "advancedTitle",
        controls = {
            {
                type = "header",
                name = "advancedHeader"
            },
            {
                type = "checkbox",
                name = "advancedCheckbox",
                default = false,
                persistent = true,
                onValueChange = self.advancedCheckbox
            }
        }
    }
end

-- Callback Functions
function addon:mainCheckbox(isChecked)
    self:debug("Main checkbox: " .. tostring(isChecked))
end

function addon:mainDropdown(selectedValue)
    self:info("Dropdown selected: " .. selectedValue)
end

function addon:mainSlider(value)
    self:info("Slider value: " .. value)
end

function addon:mainColor(hexColor)
    self:info("Color changed: " .. hexColor)
end

function addon:mainInputButton(inputText, editBox)
    self:info("Input value: " .. inputText)
    editBox:SetText("")
end

function addon:mainButton()
    self:info("Button clicked!")
end

function addon:advancedCheckbox(isChecked)
    self:info("Advanced checkbox: " .. tostring(isChecked))
end

-- Localization
addon.locale = {}

addon.locale.enEN = {
    -- Main panel
    mainTitle = "My Addon Settings",
    mainHeader = "Main Settings",
    mainCheckbox = "Enable Main Feature",
    mainCheckboxTooltip = "Enable or disable the main feature",
    mainDropdown = "Select Option",
    mainDropdownTooltip = "Choose from available options",
    dropdownOption1 = "Option One",
    dropdownOption2 = "Option Two",
    dropdownOption3 = "Option Three",
    mainSlider = "Adjust Value",
    mainSliderTooltip = "Set the value between 0 and 100",
    mainColor = "Choose Color",
    mainColorTooltip = "Select a color with optional transparency",
    mainInput = "Enter Text",
    mainInputTooltip = "Type your text here",
    mainInputButton = "Apply",
    mainButton = "Click Me",
    mainButtonTooltip = "Perform an action",
    mainDescription = "This is a description that provides additional information to the user.",
    
    -- Advanced panel
    advancedTitle = "Advanced Settings",
    advancedHeader = "Advanced Options",
    advancedCheckbox = "Enable Advanced Mode",
    advancedCheckboxTooltip = "Enable advanced features"
}
```

### Persistent vs Session-Only Controls

Set `persistent = true` to save settings permanently (survives logout/exit). If the user has selected the Global profile, it will be saved to the global saved variables. If the user has selected the Character only profile, it will be saved to the character saved variables.

### Automatic Tooltips

To add a tooltip, define a localization key with the control's name + `"Tooltip"` (e.g., `"exampleCheckboxTooltip"` for `name = "exampleCheckbox"`).

## Accessing Saved Settings

If a control is persisted, it can be later accessed through 'self.settings' organized by panel key. Access control values using `self.settings.panelKey.controlName`.

**Example:**
```lua
function addon:PrintDebuggingStatus()
    if self.settings.main.enableDebugging then
        self:info("Debugging is currently ENABLED")
    else
        self:info("Debugging is currently DISABLED")
    end
end
```

## Adding New Lua Files

To add a new Lua file to the addon: add it to your `.toc` file, then include these three lines at the top:

```lua
local addonName = ...
local SAdCore = LibStub("SAdCore-1")
local addon = SAdCore:GetAddon(addonName)
```

## Localization

All user-facing text uses localization keys. This allows you to easily translate into other languages.
When you use `name = "exampleHeader"` in your control config, SAdCore automatically displays "My Settings". If a key is missing, it displays as `[keyName]` to alert you during development.

**Adding localized text:**

```lua
addon.locale.enEN = {
    keyName = "Your Text Here",
    exampleHeader = "My Settings",
    exampleCheckbox = "Enable Feature"
}
```

## Slash Commands

By default, every addon automatically gets a slash command based on the addon name. For example, if your addon is named `MyAddon`, the slash command `/myaddon` is automatically registered.

**Default behavior:** Typing the slash command alone (e.g., `/myaddon`) opens the addon's settings panel.

### Registering Custom Commands

You can register additional commands as **parameters** to the main slash command using `self:RegisterSlashCommand(command, callback)`. These custom commands are accessed by typing the main slash command followed by the command name.

**Example:**

**Example:**
```lua
function addon:RegisterFunctions()
    self:RegisterSlashCommand("hello", self.HelloCommand)
    self:RegisterSlashCommand("debug", self.DebugCommand)
end

function addon:HelloCommand()
    self:info("Hello, World!")
end

function addon:DebugCommand(enabled)
    if enabled == "on" then
        self.settings.main.enableDebugging = true
        self:info("Debugging enabled")
    end
end
```

**Usage:**
- `/myaddon` - Opens the settings panel (default behavior)
- `/myaddon hello` - Calls the `HelloCommand` function, displays "Hello, World!"
- `/myaddon debug on` - Calls the `DebugCommand` function with parameter "on"

**Note:** Command names are case-insensitive.

## Event Registration

Register WoW events with `self:RegisterEvent(eventName, callback)`:

```lua
function addon:RegisterFunctions()
    self:RegisterEvent("PLAYER_ENTERING_WORLD", self.OnPlayerEnteringWorld)
    self:RegisterEvent("PLAYER_REGEN_DISABLED", self.OnEnterCombat)
    self:RegisterEvent("UNIT_HEALTH", self.OnUnitHealth)
end

function addon:OnPlayerEnteringWorld(event)
    self:info("Player entered world")
end

function addon:OnEnterCombat(event)
    self:info("Entering combat")
end

function addon:OnUnitHealth(event, unitID)
    if unitID == "player" then
        local health = UnitHealth("player")
        self:debug("Health: " .. health)
    end
end
```

## Zone Management

SAdCore provides automatic zone detection and callbacks. This feature is designed for addons that want to take advantage of zone-based behavior (like chat filters or UI visibility in different zones).

### Supported Zones
- `"arena"` - Arena instances
- `"battleground"` - Battleground instances
- `"dungeon"` - Dungeon instances (5-player)
- `"raid"` - Raid instances
- `"world"` - Open world (default)

### Registering Zone Callbacks

Register a callback for when the player enters a zone:

```lua
function addon:RegisterFunctions()
    self:RegisterZone("ARENA", self.enteringArena)
    self:RegisterZone("BATTLEGROUND", self.enteringBattleground)
    self:RegisterZone("WORLD", self.enteringWorld)
end

function addon:enteringArena()
    self:info("Entered arena - applying arena settings")
    -- Apply your arena-specific settings
end

function addon:enteringWorld()
    self:info("Entered world - restoring default settings")
    -- Restore default settings
end
```

### Getting Current Zone

```lua
local currentZone = self:GetCurrentZone()
-- Returns: "ARENA", "BATTLEGROUND", "DUNGEON", "RAID", or "WORLD"
```

## Common API Functions

These are the most commonly used functions available on the `self` object within the addon methods:

### Logging
- **`self:debug(text)`** - Only displays when "Enable Debugging" is enabled in settings
- **`self:info(text)`** - Always displays (informational messages)
- **`self:error(text)`** - Always displays (error messages)

### Localization
- **`self:L(key)`** - Returns the localized string for the given key based on client language

### Events & Commands
- **`self:RegisterEvent(eventName, callback)`** - Register a WoW event with a callback function
- **`self:RegisterZone(zoneName, callback)`** - Register a callback when player enters a zone
- **`self:RegisterSlashCommand(command, callback)`** - Register a custom slash command as a parameter to the main addon command

### Zone Detection
- **`self:GetCurrentZone()`** - Returns current zone: "ARENA", "BATTLEGROUND", "DUNGEON", "RAID", or "WORLD"

### Settings
- **`self:OpenSettings()`** - Opens the addon settings panel

### Utilities
- **`self:ShowDialog(title, message, onAccept, onCancel)`** - Display a confirmation dialog


## Hooks

Every framework function has Before/After hooks for extending functionality.

**Before hooks** receive and must return parameters (can modify them):
```lua
function addon:BeforeL(key)
    return key:upper()  -- Modify parameter
end
```

**After hooks** receive return values (observation only):
```lua
function addon:AfterAddCheckbox(checkbox, newYOffset)
    checkbox:SetAlpha(0.9)  -- Customize the checkbox
end
```

All available hooks follow the pattern: `Before[FunctionName]` and `After[FunctionName]`. See `LibSAdCore.lua` for the complete list.

