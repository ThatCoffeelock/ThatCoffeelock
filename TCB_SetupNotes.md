# TCB_SetupNotes.md
# ThatCoffeeLock Buy Businesses — Construction Set Wiring Guide

This document walks through every step needed to wire up the
TCB_BusinessQuest quest, the three NPC dialogue trees, and the
income script in the Oblivion Construction Set (CS).

---

## Naming Conventions Used Throughout

| Identifier               | Type             | Purpose                                   |
|--------------------------|------------------|-------------------------------------------|
| `TCB_BusinessQuest`      | Quest            | Persistent quest; carries all tier vars   |
| `TCB_MainQuestScript`    | Quest Script     | Daily income logic                        |
| `TCB_InnBuy`             | Dialogue Topic   | Buy the inn (Tier 0 → 1)                  |
| `TCB_InnUpgrade2`        | Dialogue Topic   | Upgrade inn Tier 1 → 2                    |
| `TCB_InnUpgrade3`        | Dialogue Topic   | Upgrade inn Tier 2 → 3                    |
| `TCB_ShopBuy`            | Dialogue Topic   | Buy the shop (Tier 0 → 1)                 |
| `TCB_ShopUpgrade2`       | Dialogue Topic   | Upgrade shop Tier 1 → 2                   |
| `TCB_ShopUpgrade3`       | Dialogue Topic   | Upgrade shop Tier 2 → 3                   |
| `TCB_FarmBuy`            | Dialogue Topic   | Buy the farm (Tier 0 → 1)                 |
| `TCB_FarmUpgrade2`       | Dialogue Topic   | Upgrade farm Tier 1 → 2                   |
| `TCB_FarmUpgrade3`       | Dialogue Topic   | Upgrade farm Tier 2 → 3                   |
| `TCB_Innkeeper`          | NPC (example)    | The innkeeper who sells the inn           |
| `TCB_Shopkeeper`         | NPC (example)    | The shopkeeper who sells the shop         |
| `TCB_Farmer`             | NPC (example)    | The farmer who sells the farm             |

The `TCB_` prefix keeps all mod objects grouped together in the CS
object lists and avoids conflicts with vanilla or other mod records.

---

## Part 1 — Create the Quest

### 1.1  Open the Quest window
1. In the CS, go to **Gameplay → Quests**.
2. Click **New**.
3. Set **Quest ID** to `TCB_BusinessQuest`.
4. Set **Quest Name** (player-facing) to `Business Holdings`.
5. Check **Start Game Enabled** — this makes the quest active from
   the very first frame without requiring a quest stage to start it.
6. Leave **Priority** at default (50).

### 1.2  Add Quest Variables
Switch to the **Script** sub-tab of the Quest window.

Click the **Script** drop-down and choose **[New Script]**.

In the script editor that opens, enter the full contents of
`TCB_MainQuest.obscript` (copy-paste the entire file).

Save the script with **Ctrl+S**. The CS will compile it; fix any
errors before closing.

Back in the Quest window the script is now attached.

The variables declared in that script (`iInnTier`, `iShopTier`,
`iFarmTier`, `iLastPaidDay`, `iCurrentDay`, `bInitialized`,
`iDailyGold`) automatically become **quest variables** accessible
from any other script as `TCB_BusinessQuest.<varName>`.

### 1.3  Close and save the Quest
Click **OK** in the Quest window.
Save your plugin file (**File → Save** or Ctrl+S).

---

## Part 2 — Create the NPC Owners

If you are adding dialogue to existing vanilla NPCs, skip to Part 3
and substitute their Editor IDs for `TCB_Innkeeper` etc.

### 2.1  Create each NPC
1. Go to **Actors → NPC**.
2. Click **New** for each owner.
3. Assign the Editor IDs `TCB_Innkeeper`, `TCB_Shopkeeper`,
   `TCB_Farmer` (or any IDs you prefer — just use them consistently
   in the dialogue conditions).
4. Configure race, stats, faction membership, and AI packages as
   needed for your mod.
5. Place each NPC in a cell using the Render window.

---

## Part 3 — Create the Dialogue Topics

### 3.1  Open the Dialogue window
Go to **Gameplay → Dialogue**.

### 3.2  Create each Topic
For every topic in the table below:

1. Click **New Topic**.
2. Set the **Topic ID** exactly as shown.
3. Set the **Topic Text** (what appears in the player's conversation
   menu) as shown.

| Topic ID          | Topic Text                                     |
|-------------------|------------------------------------------------|
| `TCB_InnBuy`      | I would like to buy this inn.                  |
| `TCB_InnUpgrade2` | I would like to expand the inn.                |
| `TCB_InnUpgrade3` | I would like to upgrade the inn further.       |
| `TCB_ShopBuy`     | I would like to buy this shop.                 |
| `TCB_ShopUpgrade2`| I would like to expand the shop.               |
| `TCB_ShopUpgrade3`| I would like to renovate the shop.             |
| `TCB_FarmBuy`     | I would like to buy this farm.                 |
| `TCB_FarmUpgrade2`| I would like to expand the farm.               |
| `TCB_FarmUpgrade3`| I would like to develop the farm.              |

### 3.3  Add Dialogue Responses
Each topic needs at least one **Info** (response) entry.

1. Select the topic.
2. Click **New** in the Info list.
3. In the **Speaker** field: select the NPC who will say this line
   (e.g. `TCB_Innkeeper` for the inn topics).
4. In the **Response Text** field: enter the NPC's spoken line.
   Examples:
   - TCB_InnBuy: *"Aye, I've been wanting to retire. Five thousand
     gold and she's all yours."*
   - TCB_InnUpgrade2: *"For ten thousand gold, I can help you bring
     in builders and furnish new rooms."*
   - TCB_InnUpgrade3: *"Twenty thousand would turn this place into
     a proper grand inn."*
   - TCB_ShopBuy: *"Three thousand and the stock and the name are
     yours."*
   - TCB_ShopUpgrade2: *"Six thousand more and we could double the
     floor space."*
   - TCB_ShopUpgrade3: *"Twelve thousand for a full renovation —
     marble counters, the works."*
   - TCB_FarmBuy: *"Two thousand gold, fair and simple."*
   - TCB_FarmUpgrade2: *"Four thousand would clear the back fields
     and build a second barn."*
   - TCB_FarmUpgrade3: *"Eight thousand for orchards, livestock pens,
     and a proper manor house."*

---

## Part 4 — Set Conditions on Each Dialogue Info

Conditions control whether a topic is visible in the player's
conversation menu. Each Info entry has a **Conditions** list.

### How to add a condition
1. Select the Info entry.
2. In the Conditions panel (bottom half of the Dialogue window),
   click **New**.
3. Fill in Function, Subject, and Value as described below.

### Inn topics

**TCB_InnBuy** — show when inn is unowned AND player can afford it:
```
Condition 1:
  Function:  GetQuestVariable
  Quest:     TCB_BusinessQuest
  Variable:  iInnTier
  Operator:  ==
  Value:     0.00

Condition 2:
  Function:  GetGold        (subject: Player is automatic)
  Operator:  >=
  Value:     5000.00
```

**TCB_InnUpgrade2** — show when inn is at Tier 1 AND player can afford it:
```
Condition 1:
  Function:  GetQuestVariable
  Quest:     TCB_BusinessQuest
  Variable:  iInnTier
  Operator:  ==
  Value:     1.00

Condition 2:
  Function:  GetGold
  Operator:  >=
  Value:     10000.00
```

**TCB_InnUpgrade3** — show when inn is at Tier 2 AND player can afford it:
```
Condition 1:
  Function:  GetQuestVariable
  Quest:     TCB_BusinessQuest
  Variable:  iInnTier
  Operator:  ==
  Value:     2.00

Condition 2:
  Function:  GetGold
  Operator:  >=
  Value:     20000.00
```

### Shop topics

**TCB_ShopBuy**:
```
GetQuestVariable TCB_BusinessQuest iShopTier == 0
GetGold >= 3000
```

**TCB_ShopUpgrade2**:
```
GetQuestVariable TCB_BusinessQuest iShopTier == 1
GetGold >= 6000
```

**TCB_ShopUpgrade3**:
```
GetQuestVariable TCB_BusinessQuest iShopTier == 2
GetGold >= 12000
```

### Farm topics

**TCB_FarmBuy**:
```
GetQuestVariable TCB_BusinessQuest iFarmTier == 0
GetGold >= 2000
```

**TCB_FarmUpgrade2**:
```
GetQuestVariable TCB_BusinessQuest iFarmTier == 1
GetGold >= 4000
```

**TCB_FarmUpgrade3**:
```
GetQuestVariable TCB_BusinessQuest iFarmTier == 2
GetGold >= 8000
```

### Important: also restrict by NPC speaker
To prevent the inn dialogue appearing when talking to the farmer,
add a third condition to every Info entry:

```
Condition 3:
  Function:  GetIsID
  Reference: <NPC Editor ID, e.g. TCB_Innkeeper>
  Operator:  ==
  Value:     1.00
```

This ensures each topic only appears when talking to the correct NPC.

---

## Part 5 — Add Result Scripts to Each Dialogue Info

The Result Script field is where the purchase/upgrade code runs
after the player selects the topic.

### 5.1  Where to find the Result Script field
In the Dialogue window, select an Info entry. At the bottom of the
window you will see a **Result Script** text box (multi-line).

### 5.2  What to paste
Do **NOT** paste the `ScriptName` line or the `Begin`/`End`
block headers from the `.obscript` files. Paste only the lines
between `Begin ScriptEffectStart` and `End`.

**TCB_InnBuy Result Script:**
```
if ( TCB_BusinessQuest.iInnTier != 0 )
    MessageBox "You already own this inn."
    return
endif
if ( player.GetItemCount Gold001 < 5000 )
    MessageBox "You cannot afford this inn. You need 5,000 gold."
    return
endif
player.RemoveItem Gold001 5000
set TCB_BusinessQuest.iInnTier to 1
MessageBox "Congratulations! You are now the proud owner of this inn. You will receive 50 gold each day."
```

**TCB_InnUpgrade2 Result Script:**
```
if ( TCB_BusinessQuest.iInnTier != 1 )
    MessageBox "You must own the inn at Tier 1 before you can expand it."
    return
endif
if ( player.GetItemCount Gold001 < 10000 )
    MessageBox "You cannot afford this expansion. You need 10,000 gold."
    return
endif
player.RemoveItem Gold001 10000
set TCB_BusinessQuest.iInnTier to 2
MessageBox "The inn has been expanded with new rooms and a larger common hall. You will now receive 100 gold each day."
```

**TCB_InnUpgrade3 Result Script:**
```
if ( TCB_BusinessQuest.iInnTier != 2 )
    MessageBox "The inn must already be at Tier 2 before this final upgrade."
    return
endif
if ( player.GetItemCount Gold001 < 20000 )
    MessageBox "You cannot afford this upgrade. You need 20,000 gold."
    return
endif
player.RemoveItem Gold001 20000
set TCB_BusinessQuest.iInnTier to 3
MessageBox "The inn is now a grand establishment, the finest in the region. You will receive 150 gold each day."
```

**TCB_ShopBuy Result Script:**
```
if ( TCB_BusinessQuest.iShopTier != 0 )
    MessageBox "You already own this shop."
    return
endif
if ( player.GetItemCount Gold001 < 3000 )
    MessageBox "You cannot afford this shop. You need 3,000 gold."
    return
endif
player.RemoveItem Gold001 3000
set TCB_BusinessQuest.iShopTier to 1
MessageBox "Excellent! The shop is yours. You will receive 75 gold each day from its profits."
```

**TCB_ShopUpgrade2 Result Script:**
```
if ( TCB_BusinessQuest.iShopTier != 1 )
    MessageBox "You must own the shop at Tier 1 before you can expand it."
    return
endif
if ( player.GetItemCount Gold001 < 6000 )
    MessageBox "You cannot afford this expansion. You need 6,000 gold."
    return
endif
player.RemoveItem Gold001 6000
set TCB_BusinessQuest.iShopTier to 2
MessageBox "The shop has been expanded with new stock and additional stalls. You will now receive 150 gold each day."
```

**TCB_ShopUpgrade3 Result Script:**
```
if ( TCB_BusinessQuest.iShopTier != 2 )
    MessageBox "The shop must already be at Tier 2 before this final renovation."
    return
endif
if ( player.GetItemCount Gold001 < 12000 )
    MessageBox "You cannot afford this renovation. You need 12,000 gold."
    return
endif
player.RemoveItem Gold001 12000
set TCB_BusinessQuest.iShopTier to 3
MessageBox "The shop is now one of the finest establishments in the region. You will receive 225 gold each day."
```

**TCB_FarmBuy Result Script:**
```
if ( TCB_BusinessQuest.iFarmTier != 0 )
    MessageBox "You already own this farm."
    return
endif
if ( player.GetItemCount Gold001 < 2000 )
    MessageBox "You cannot afford this farm. You need 2,000 gold."
    return
endif
player.RemoveItem Gold001 2000
set TCB_BusinessQuest.iFarmTier to 1
MessageBox "The farm is now yours. You will receive 60 gold each day from the harvest."
```

**TCB_FarmUpgrade2 Result Script:**
```
if ( TCB_BusinessQuest.iFarmTier != 1 )
    MessageBox "You must own the farm at Tier 1 before you can expand it."
    return
endif
if ( player.GetItemCount Gold001 < 4000 )
    MessageBox "You cannot afford this expansion. You need 4,000 gold."
    return
endif
player.RemoveItem Gold001 4000
set TCB_BusinessQuest.iFarmTier to 2
MessageBox "New fields have been cleared and a second barn raised. You will now receive 120 gold each day."
```

**TCB_FarmUpgrade3 Result Script:**
```
if ( TCB_BusinessQuest.iFarmTier != 2 )
    MessageBox "The farm must already be at Tier 2 before this final development."
    return
endif
if ( player.GetItemCount Gold001 < 8000 )
    MessageBox "You cannot afford this development. You need 8,000 gold."
    return
endif
player.RemoveItem Gold001 8000
set TCB_BusinessQuest.iFarmTier to 3
MessageBox "The farm is now a thriving estate with orchards and livestock. You will receive 180 gold each day."
```

---

## Part 6 — Add Topics to NPC Conversation Menus

Topics must be linked to a greeting or made available via the
standard conversation menu.

### Option A — Conversation Menu (Greeting route)
1. Create a new Dialogue topic with Type **Greeting** for each NPC
   (e.g. `TCB_InnGreeting`).
2. In the greeting's Info entry, add a **Choice** link to each
   business topic:
   - For `TCB_Innkeeper`: link to `TCB_InnBuy`, `TCB_InnUpgrade2`,
     `TCB_InnUpgrade3`.
   - For `TCB_Shopkeeper`: link to `TCB_ShopBuy`, etc.
   - For `TCB_Farmer`: link to `TCB_FarmBuy`, etc.
3. The conditions on each individual Info entry (see Part 4) will
   automatically hide topics the player is not yet eligible for.

### Option B — Force Topic via Script on the NPC
Attach a script to the NPC actor and call:
```
ForceGreeting
```
inside an `OnActivate` block, then set the greeting to offer the
business topics as choices.  This is useful if the NPC is not
flagged as a dialogue NPC by default.

---

## Part 7 — Final Checks and Testing

### 7.1  Compile and save
Press **F7** (or **Gameplay → Compile All Scripts**) to recompile
all scripts in the plugin. Resolve any errors shown in the output.

Save your plugin.

### 7.2  In-game testing checklist
- [ ] Load a save with enough gold (use `player.additem Gold001 30000`
      in the console if needed for testing).
- [ ] Talk to the innkeeper. The **Buy Inn** option appears.
      Select it; verify gold is deducted and a confirmation message
      appears.
- [ ] Save and wait one in-game day (`wait 24` at a bed, or use
      `set GameDaysPassed to <current+1>` in the console).
      Verify the income message appears and gold is added.
- [ ] Talk to the innkeeper again. The **Buy Inn** option is gone;
      **Expand Inn** appears. Upgrade and re-test income.
- [ ] Repeat for Tier 3, and repeat the full cycle for shop and farm.
- [ ] Verify that buying the shop does not accidentally change inn
      tier, and vice versa.
- [ ] Test with no businesses owned: no income message should appear.
- [ ] Test fast-travel across multiple in-game days: income should
      be paid exactly once per day, not once per elapsed day.

### 7.3  Console commands for quick testing
```
; Check current tier values
getqv TCB_BusinessQuest iInnTier
getqv TCB_BusinessQuest iShopTier
getqv TCB_BusinessQuest iFarmTier

; Manually set a tier (useful for skipping to upgrade tests)
setqv TCB_BusinessQuest iInnTier 2

; Force the day counter forward by one
set GameDaysPassed to (GameDaysPassed + 1)
```

---

## Summary of Income Rates

| Business | Tier 1 | Tier 2 | Tier 3 | Buy Cost | T2 Cost | T3 Cost |
|----------|--------|--------|--------|----------|---------|---------|
| Inn      |  50/day| 100/day| 150/day|  5,000g  | 10,000g | 20,000g |
| Shop     |  75/day| 150/day| 225/day|  3,000g  |  6,000g | 12,000g |
| Farm     |  60/day| 120/day| 180/day|  2,000g  |  4,000g |  8,000g |

Maximum combined income at Tier 3: **555 gold per in-game day**.
