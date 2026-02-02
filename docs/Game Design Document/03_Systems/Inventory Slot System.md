_Each slot in the inventory is a container that holds a specific instance of an item._

### The Item Definition (Data Structure)

Every item in the game is defined by two parts:

1. **Static Config (The "Archetype"):** Data that never changes (e.g., "Revolver Max Ammo", "Icon", "Base Weight").
    
2. **Instance State (The "Save Data"):** Data that changes for _this specific_ item (e.g., "Current Ammo", "Durability").
    

|**Variable Name**|**Data Type**|**Description**|
|---|---|---|
|`itemID`|`string`|**Unique ID.** Links the item to its static config (e.g., "weapon_revolver_01").|
|`stackCount`|`int`|**Quantity.** How many of this item are in _this specific slot_. (Default: 1).|
|`instanceData`|`dictionary`|**Dynamic State.** A flexible container for unique item properties (e.g., `{ "ammo": 3, "durability": 85.5 }`).|

---

### Slot Logic & Rules

_How items behave when placed into a slot._

|**Rule Name**|**Description**|**Example**|
|---|---|---|
|**Max Stack Size**|defined in _Static Config_. The maximum `stackCount` allowed in a single slot.|**Pistol Ammo:** 10<br><br>  <br><br>**Revolver:** 1 (Non-stackable)|
|**Merge Logic**|If a slot contains the same `itemID`, adding more increments `stackCount` up to `Max Stack Size`. Excess overflows to a new slot.|Player has 4 ammo. Pick up 8.<br><br>  <br><br>Slot A: 10 (Full).<br><br>  <br><br>Slot B: 2 (Remainder).|
|**Unique State Constraint**|Items with `instanceData` (like weapons with specific ammo counts) generally have `Max Stack Size = 1`.|You cannot stack two revolvers because one might have 6 bullets and the other 0.|

---

### Example Scenarios (Use Cases)

**Scenario A: The Revolver (Stateful Item)**

- **Item:** `weapon_revolver`
    
- **Type:** Equipment (Non-Stackable)
    
- **Slot Data:**
    
    - `stackCount`: 1
        
    - `instanceData`: `{ "currentAmmo": 3, "maxAmmo": 6, "condition": "pristine" }`
        
- _Result:_ Takes up 1 Slot. Cannot be merged with another revolver.
    

**Scenario B: Pistol Ammo (Stackable Item)**

- **Item:** `ammo_9mm`
    
- **Type:** Consumable (Stackable)
    
- **Config:** `Max Stack Size = 10`
    
- **Slot Data:**
    
    - `stackCount`: 4
        
    - `instanceData`: `null` (All bullets are identical)
        
- _Result:_ Takes up 1 Slot. If player finds 3 more rounds, `stackCount` becomes 7.
    

---

### Updated Encumbrance Calculation

_How these complex slots affect your speed penalty._

**Slot Count**

> `currentItemCount` = **Number of Occupied Slots**.
> 
> - 100 bullets (10 stacks of 10) = 10 "Items".
>     
> - 1 Revolver = 1 "Item".
>     