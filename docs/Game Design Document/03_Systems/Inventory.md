## Runtime Stats (Player State)

|**Variable Name**|**Data Type**|**Default**|**Description**|
|---|---|---|---|
|`currentItemCount`|`int`|`0`|**Current Load.** The actual number of items currently in the inventory. Updated whenever items are added/removed.|
|`bonusSafeSlots`|`int`|`0`|**Upgrade Modifier.** The number of _additional_ unencumbered slots unlocked via gameplay (skills/equipment). Added to `encumbranceThreshold`.|
|`currentSpeedMult`|`float`|`1.0`|**Resulting Penalty.** The final calculated speed multiplier applied to the player character this frame. (Read-only).|

---

## Logic & Formulas

_These formulas define how Base Configs and Runtime Stats merge to create the final gameplay values._

### 1. The Effective Soft Limit

This calculates the player's _actual_ threshold after upgrades.

> **Formula:** `EffectiveSoftLimit` = `encumbranceThreshold` + `bonusSafeSlots`
> 
> _Constraint:_ `EffectiveSoftLimit` can never exceed `maxHardLimit`. If the player unlocks too many slots, they just hit the hard cap without penalty, but cannot carry _more_ items.

### 2. The Overload Calculation

How we calculate the input `t` for the Animation Curve.

> **If** `currentItemCount` <= `EffectiveSoftLimit`:
> 
> - **Result:** No Penalty (Multiplier = 1.0).
>     
> 
> **If** `currentItemCount` > `EffectiveSoftLimit`:
> 
> - **Step A:** Calculate `ExcessItems` = `currentItemCount` - `EffectiveSoftLimit`
>     
> - **Step B:** Calculate `PenaltyRange` = `maxHardLimit` - `EffectiveSoftLimit`
>     
> - **Step C (Normalized t):** `t` = `ExcessItems` / `PenaltyRange`
>     
> - **Result:** `speedPenaltyCurve.Evaluate(t)`
>     

---

### Example Scenario

- **Config:** Hard Limit = 5, Soft Limit = 3.
    
- **Upgrade:** Player has `bonusSafeSlots = 1`. (New Effective Soft Limit = 4).
    
- **Inventory:** Player holds **5 items**.
    

**Calculation:**

1. Player is over the limit (5 > 4).
    
2. Excess is 1 item.
    
3. Range is 1 item (5 - 4).
    
4. `t` = 1 / 1 = **1.0** (Maximum Penalty).