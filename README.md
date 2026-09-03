# Growtopia Farming Calculator

An open-source, client-side simulation engine and yield calculator designed for Growtopia players. This application computes multi-cycle agricultural trajectories, estimating seed gain, block-break yields, gem gain, experience gain (XP), Comet Dust chances, and World Lock calculations.

---

## Overview

The Growtopia Farming Calculator models the complete lifecycle of farming operations in Growtopia. By incorporating base drop mechanics, recursive block-smashing loops, item effects, role bonuses, and Ances, the calculator provides deterministic projections over 1 to 100 farming cycles.

The project is distributed as a self-contained, single-file web application requiring no backend infrastructure or external build toolchains.

---

## Features

### 1. Common Farmables Presets
* Integrated database containing 26 standard farmable items (e.g., Fish Tank, Pepper Tree, Pinball Bumper, Laser Grid, Chandelier, Magic Bell, Clouds Background, and more!).
* Automatic population of item rarity and baseline statistics upon selection.
* Dedicated modal interface with search and reset capabilities.

### 2. Stock and Farmability Configuration
* **Initial Inventory:** Set initial seed reserves and starting block counts.
* **Capacity Bounds:** Configure maximum tree capacities per world (default: 2,500).
* **Multi-Cycle Iteration:** Simulate up to 100 consecutive plant-grow-harvest-smash cycles.
* **Harvest Yield Tiers (Maxblock):** Selectable farmability tiers adjusting base block yields per tree:
  * Not Farmable (1.0x / 2.50 blocks per tree)
  * Farmable (1.5x / 3.75 blocks per tree)
  * Very Farmable (2.0x / 5.00 blocks per tree)
  * Super Farmable (2.5x / 6.25 blocks per tree)

### 3. Role Up! Bonus Calculation
* **Farmer Role (Levels 1–10):** Adds scaling bonus gem probabilities from harvested trees (+0.5% at Level 2 up to +3.0% at Level 10).
* **Builder Role (Levels 1–10):** Adds double-block drop probabilities from breaking blocks (+0.5% at Level 2 up to +3.0% at Level 10).

### 4. Hidden Riches (Ancestral Item Effects)
Supports level 1 through 6 for ancestral items with dynamic UI mutual exclusion:
* **Blue Ances (Ancestral Tesseract of Riches):** +5% to +10% block drop chance when smashing.
* **Green Ances (Ancestral Seed of Life):** -2% to -12% seed growth duration reduction.
* **Red Ances (Ancestral Lens of Riches):** +5% to +10% bonus gem drop rate when smashing. **Mutually exclusive with American Sports Ball Jersey** (dynamically disables and grays out the Jersey when active).
* **Yellow Ances (Ancestral Totem of Wisdom):** +5% to +10% chance of receiving double XP per farming action.

### 5. Item Effects, Consumables & Drop Caps
* **American Sports Ball Jersey:** 10% bonus gems on smashing. **Mutually exclusive with Red Ances** (dynamically disables and grays out the Red Ances option when equipped).
* **Buddy Block Head:** 2% bonus block drop chance.
* **Cosmic Cape:** 0.001% (1 in 100,000) Comet Dust drop rate per block destroyed.
* **Dreamcatcher Staff:** 2% bonus harvest blocks for items with Rarity < 100.
* **Galaxy Skin:** 10% bonus block drop chance.
* **Harvester / Harvester of Sorrows:** 10% bonus harvest blocks for items with Rarity < 100.
* **Winter Wishing Star:** 2% bonus block drop chance.
* **Emerald Lock:** 10% chance of adding an additional flat gem drop (+0.10 gems/block) upon breaking blocks.
* **Lucky Clover / Songpyeon:** 10% bonus block chance and 10% chance to multiply gem drops by up to 5x (+200% average net gain on trigger).
* **30% Bonus Block Hard Cap:** Enforces the in-game ~30% hard ceiling on cumulative bonus block drop probabilities across all equipped items and roles.

### 6. Analytics, UI Transparency & Data Export
* **Conservative Floor Rounding:** All primary KPI metrics—including Total Profit (WLs), Net Seed Gain, Total Gems Farmed, and Comet Dust WL value—are strictly rounded down (`Math.floor`) to whole integers to provide conservative, guaranteed baseline projections.
* **Consolidated Gem Breakdown:** Displays a unified **Bonus Gems** aggregate row under Harvest & Smashing Multipliers that combines all active item, role, and consumable modifiers additively.
* **Interactive Tooltip:** Features an interactive `?` popover next to the **Compound Smash Multiplier** explaining recursive block-break cycles without visual clutter.
* **Interactive Views:** Tabbed navigation between Calculation Mechanics, Cycle-by-Cycle Data Tables, and Visual Growth Progress Bars.
* **Export Utilities:** Direct CSV export functionality and formatted clipboard summary copying.

---

## Mathematical Specifications & Engine Logic

All calculations simulate estimated values derived from community research, verified Discord developer statements, and documentation from the Growtopia Wiki.

### 1. Tree Growth Duration
Growth time in seconds ($T$) as a function of item rarity ($R$):

$$T = R \times (R^2 + 30)$$

When the Green Ancestor artifact is active with reduction factor $k_{\text{green}} \in [0.02, 0.12]$:

$$T_{\text{effective}} = T \times (1 - k_{\text{green}})$$

### 2. Base Harvest Seed Drop Rate
The probability ($P_{\text{seed}}$) of a seed dropping directly from a harvested tree:

$$P_{\text{seed}} = \frac{4}{R + 12}$$

### 3. Base Gem Yields
Base gem yield per action ($G_{\text{base}}$) depends on item rarity:

$$G_{\text{base}} = \begin{cases} \frac{R}{9}, & \text{if } R > 30 \\ \frac{R}{13.5}, & \text{if } R \le 30 \end{cases}$$

### 4. Experience Points (XP)
Base XP earned per action ($XP_{\text{base}}$):

$$XP_{\text{base}} = 1 + \left\lfloor \frac{R}{5} \right\rfloor$$

### 5. Recursive Smashing Multiplier & Drop Capping
Breaking a block yields seeds, gems, and occasionally additional blocks. Because bonus blocks can themselves be broken recursively, the total effective blocks smashed ($B_{\text{effective}}$) is modeled as a geometric series.

Active bonus block probabilities from equipment and roles ($M_i$) are capped at a 30% maximum:

$$P_{\text{bonus\_capped}} = \min\left(\sum M_i, 0.30\right)$$

The compound smash denominator ($D_{\text{smash}}$) accounts for the standard base return rate ($B_{\text{base}} = \frac{1}{12}$) and capped bonus rolls:

$$D_{\text{smash}} = (1 - B_{\text{base}}) \times (1 - P_{\text{bonus\_capped}})$$

The compound block break multiplier is:

$$M_{\text{compound}} = \frac{1}{D_{\text{smash}}}$$

Total seeds from breaking blocks:

$$S_{\text{smash}} = B_{\text{effective}} \times 0.25$$

---

## Technical Stack

* **Core Language:** HTML5 / Modern ECMAScript (ES6+)
* **UI Framework:** React 18 (Production UMD)
* **Rendering & Compilation:** Babel Standalone for in-browser JSX parsing
* **Styling Framework:** Tailwind CSS (CDN distribution)
* **Icons:** Inline SVGs (zero external icon library dependencies)

---

*Note: The code used for this calculator was completely written, designed, and coded by Google Gemini.*
