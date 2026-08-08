# Wasteland 3 Ranger Build Console — session handoff

State as of the first session. `index.html` is a complete, working, single-file
character builder. What remains is **data verification**, which was blocked by
network egress policy.

---

## What exists

`index.html` — standalone, no dependencies, opens directly in Chrome.

- 7 attributes (base 2, max 10) with +/- steppers and per-attribute gear modifiers
- 22 skills in 4 groups with +/- steppers, rank pips, live next-rank cost, gear modifiers
- Perks gated on **trained** skill rank (gear bonuses deliberately do not qualify)
- Level stepper 1–35 driving three live budgets: attribute / skill / perk points
- Lowering the level surfaces an over-budget alert rather than silently discarding spend
- Live combat readout (AP, combat speed, evasion, initiative, CON, crit, penetration…)
- Full derived-statistics table
- Backgrounds and quirks, with Poindexter wired into the skill-point budget
- Gear/stat modifier grid for worn items that hit derived stats directly
- Custom perk entry, named build save/load to localStorage, JSON export/import
- Self-documenting "Data & Assumptions" panel marking every value verified vs. approximate

All game data lives in the `DATA` block at the top of the `<script>`: `RULES`,
`ATTRS`, `SKILL_GROUPS`, `PERKS`, `BACKGROUNDS`, `QUIRKS`, `GEAR_FIELDS`.
The UI is generic over that data — correcting a number is a one-line edit.

---

## Research already confirmed — do not redo

**Progression**
- 7 attributes, base 2 each, +7 free points at creation (21 total at level 1); max rank 10
- +1 attribute point per level starting at level 3
- 3 skill points per level
- Intelligence grants a one-time skill-point bonus per rank
- First perk point at level 4, then one every 2 levels
- Skills run rank 0–10; cost rises from 1 point to 6 at rank 10; **28 points total** to max one
- Level cap 35 widely reported for the base game

**Attribute effects (rank 1 → rank 10 endpoints)**
- **Coordination** — AP: base 6 / max 10, +1 per two ranks, reaching **12 AP / 16 max AP** at rank 10. Status effect resistance +3% → +35%
- **Luck** — per point: +1% lucky action, +2% luck crit, +2% mega crit, +2% lucky evade; +1 penetration per 2 points
- **Awareness** — +2% hit chance, +1 perception (→ +6), +3% ranged damage (→ +35%)
- **Strength** — CON +5 → +75; CON/level +3 → +15; melee damage +3% → +35%; throwing range +0.1 → +1.1m
- **Speed** — combat speed +0.1 → +1.4 (base 1.4 m/AP, so 1.5 → 2.8 m/AP); evasion +3% → +33%; initiative +4% → +45%
- **Intelligence** — crit chance → +25%; crit damage +0.1x → +1.1x; crit heal → +25%; +1 skill point per rank
- **Charisma** — strike rate +2%/pt (base 6%, → +22%); XP +2%/pt (→ +30%); mission reward +1%/pt (→ +10%)

**Skills (22, four groups)** — Combat: Small Arms, Sniper Rifles, Automatic Weapons,
Big Guns, Brawling, Melee Combat, Explosives. General: First Aid, Weird Science,
Survival, Animal Whisperer, Sneaky Shit, Leadership. Exploration: Lockpicking,
Mechanics, Nerd Stuff, Toaster Repair, Weapon Modding, Armor Modding.
Social: Barter, Hard Ass, Kiss Ass.

**Perks confirmed by name + required rank**
- Small Arms: Shredder Shot 2, Opportunist 3, Trick Shot 5, Clear Cover 6, Draw! 7, Devastation 8, Counter-Offensive 10
- Sniper Rifles: Mark Target 2, Masterful Precision 5, Concentration 7, Chain Ambush 10
- Automatic Weapons: Puncturing Shot 2, Gopher Hunter 3, Spray 'N' Pray 4, Reckless 6, Double Tap 7, Stormer 8, Trigger Happy 10
- Lockpicking has no perks; Armor Modding has exactly one, at rank 10; Small Arms reportedly has 8 (only 7 recovered)

**Quirks (17)** — Blunderer, Bop Bag, Circus Freak, Death Wish, Doomsday Prepper,
Lone Wolf, Medical Marvel, Mime, Poindexter, Prospector, Pyromaniac, Sadomasochist,
Serial Killer, Two-Pump Chump, Varangian Blood, Waste Roamer, Way of the Squeezins.
Confirmed effects: Serial Killer +3 AP per kill (once/turn); Two-Pump Chump +2 AP for
3 turns; Way of the Squeezins +50% damage drunk / −20% sober; Doomsday Prepper −35%
status effect damage; Circus Freak +combat speed and +25% crit resistance;
Poindexter +1 skill point every 2 levels.

**Backgrounds confirmed** — Bookworm +5% XP; Desert Cat +1 Perception;
Disciple of the Metal +15% fire damage; Explodomaniac +15% explosive damage;
Goat Killer +5% crit chance; Grease Monkey +15% vs robots and vehicles;
Mannerite +1 Kiss Ass. Also seen: The Boss, Lethal Weapon, Mopey Poet,
Vicious Avenger, and loose effects (+1 Barter, +5% evasion, +15% crit resist,
+10% vs humans, +0.2 combat speed).

---

## What is still unverified — the actual remaining work

Each is flagged in the app's Data & Assumptions panel.

1. **Per-rank attribute tables.** Only rank-1 and rank-10 endpoints are sourced;
   ranks 2–9 are interpolated. Several stats clearly take an extra jump at rank 10.
2. **Skill cost curve middles.** `[1,1,1,2,2,3,3,4,5,6]` matches the confirmed
   endpoints and the 28 total, but the middle values are inferred.
3. **Intelligence skill-point rule.** Sources conflict three ways: +1 per rank above 1
   (currently used, +9 at rank 10), +1 per even rank (+5), or +1 per rank (+10).
4. **Attribute points per level.** "+1 per level from level 3" is stated directly, but
   one source's "47 points at max level" total implies a different schedule
   (21 + 26 = levels 10–35). Needs confirming against the game.
5. **Level cap with DLC.** 35 confirmed for base game; whether Steeltown or Cult of the
   Holy Detonation raises it could not be confirmed.
6. **Base CON, base crit chance, base crit damage.** Currently 10 / 5% / 2.0x — placeholders.
7. **Perks for 19 of 22 skills.** Only the three gun trees are recovered. Brawling's
   Shaolin Surprise (rank 10) is confirmed; the rest are missing.
8. **Quirk and background effects** that are descriptive text rather than numbers.

---

## Why it stalled

Egress policy returned 403 for every game wiki: `wasteland.fandom.com`,
`wasteland-archive.fandom.com`, `wasteland3.wiki.fextralife.com`, `game-maps.com`,
`guides.gamepressure.com`, `breezewiki.com`, `en.namu.wiki`, `neoseeker.com`,
`techraptor.net`, `brightrockmedia.com`, `pcinvasion.com`, `beforeiplay.com`.
`WebSearch` still worked (it runs server-side) and produced everything above, but its
summariser reads snippets and cannot reproduce full tables — and it contradicted itself
on several numbers, which is why items 1–6 remain open.

**Fix:** run the next session with unrestricted network access, or allowlist those
domains, then `WebFetch` the per-rank tables directly.
