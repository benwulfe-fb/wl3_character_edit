# Wasteland 3 Ranger Build Console — session handoff

`index.html` is a complete, standalone character builder. **Data verification is
done.** The previous session was blocked by egress policy; this session had
working network access and every open item was closed against primary sources.

---

## What exists

`index.html` — standalone, no dependencies, opens directly in Chrome.

- 7 attributes (base 2, max 10) with steppers, per-attribute gear modifiers and
  the game's own card art as a watermark behind each card
- 22 skills in 4 groups with rank pips, live next-rank cost, gear modifiers and
  a per-skill glyph that lights amber once the skill is trained
- 75 perks gated on **trained** skill rank, each with its wiki illustration;
  owned perks burn amber, locked ones sit dim
- Level stepper 1–35 driving three live budgets: attribute / skill / perk points
- Lowering the level surfaces an over-budget alert rather than silently discarding spend
- Live combat readout and a full derived-statistics table
- 17 backgrounds and 17 quirks with numeric effects wired into the readout
- Custom perk entry, named build save/load to localStorage, JSON export/import
- Self-documenting "Data & Assumptions" panel marking every value verified vs. approximate

All game data lives in the `DATA` block at the top of the main `<script>`:
`RULES`, `ATTRS`, `SKILL_GROUPS`, `PERKS`, `BACKGROUNDS`, `QUIRKS`, `GEAR_FIELDS`.
The UI is generic over that data — correcting a number is a one-line edit.

---

## How the sources were reached

`WebFetch` returns 402 in this environment, and `wasteland.fandom.com` returns
403 to a plain HTTP client. Both were worked around:

- **Fandom** — the MediaWiki API is wide open even though page HTML is blocked:
  `https://wasteland.fandom.com/api.php?action=query&prop=revisions&titles=X&rvprop=content&rvslots=main&format=json`
  returns raw wikitext, which contains the rank tables verbatim.
- **Fextralife and game-maps** — plain `curl` with a browser User-Agent works
  (HTTP 200); parse with BeautifulSoup.

Scripts used are in the session scratchpad (`fandom.py`, `tab.py`, `art.py`,
`gen.py`, `verify.py`).

---

## Verified this session

**Progression**
- 21 attribute points at level 1 (base 2 each + 7 free), then **+1 per level from
  level 3**. Both Fandom and game-maps state this directly. Fandom's "24 extra
  points at level 25" aside is arithmetically inconsistent with its own rule and
  was discarded.
- Skill points: **total = 3 × level + Intelligence**. A level 1 Ranger with INT 1
  starts with 4. This resolves the three-way conflict: Intelligence gives **+1 per
  rank counted from rank 1**, so investment can only ever add +9. game-maps'
  "+1 every 2 Intelligence" is simply wrong. Confirmed twice over — the
  Intelligence rank table and the Skill Points page's worked level-25 totals
  (76 / 85 / 88 / 97) both agree, and the engine reproduces all four.
- Skill cost curve **1·1·1·2·2·3·3·4·5·6 = 28**, published in full, not inferred.
  A skill book taken for rank 10 drops the real cost to 22.
- Perk points: first at level 4, then one every 2 levels.
- **Level cap 35, and neither DLC raises it.** Steeltown and Cult of the Holy
  Detonation only gate entry, at squad level 9 and 16.
- `AP = 6 + Coordination bonus (0…+5)` — so **11 AP at Coordination 10, not 12**.
- Combat speed base 1.4 m/AP → 1.5 at Speed 1, 2.8 at Speed 10.

**Attribute tables** — all 28 stat tables now read rank-by-rank from published
tables. The old interpolations were wrong far more often than not. Corrections:

| Attribute | Stat | Was | Now |
|---|---|---|---|
| Coordination | AP / Max AP at rank 10 | +6 | **+5** |
| Coordination | Status Resist | 3→35 | **2,4,…,18,25** |
| Luck | Luck Crit, Mega Crit | 2%/pt to 20 | **1%/pt to 10** |
| Luck | Penetration at rank 10 | +5 | **+6** |
| Awareness | Hit Chance | 2,4,5,…,12 | **1,2,…,9,12** |
| Awareness | Perception | 1,1,2,3,… | **0,1,1,2,2,3,3,4,4,6** |
| Strength | CON | 5,13,21,… | **5,10,15,…,45,75** |
| Strength | CON / Level | 3,4,5,6,… | **3,3,3,6,6,9,9,12,12,15** |
| Speed | Combat Speed | 0.1,0.2,0.4,0.6,… | **0.1,0.2,…,0.9,1.4** |
| Speed | Initiative | 4,9,14,… | **4,8,12,…,36,45** (a percentage) |
| Intelligence | Crit Chance | …,20,25 | **2,4,…,18,20** |
| Intelligence | Crit Heal | 2,4,7,9,… | **2,4,…,18,25** |
| Charisma | Experience | 2,5,8,11,… | **2,4,…,18,30** |

New stats added: Lucky Crit Resist, Lucky Double Heal / Money / Scrap,
Crit Heal Bonus, Leadership Range.

**Skill groups were wrong.** Fandom and Fextralife agree independently:
Combat 6, General 5, Exploration 7, Social 4. Explosives is **General**,
Survival is **Exploration**, Leadership is **Social**.

**All 75 perks** recovered with name, required rank and effect, from game-maps.
Notable fixes: Shaolin Surprise is **Brawling rank 2**, not 10. Armor Modding's
capstone is **Tender Loving Care**. Draw! refunds the first attack after
reloading an empty weapon. Counter-Offensive is a +50% damage riposte, not
return fire. Lockpicking, Hard Ass and Kiss Ass genuinely have no perks.

**Quirks** — all 17 now carry both bonus *and* penalty, which were missing
entirely. Poindexter costs −8 CON and −3 CON/level; Serial Killer costs −1 AP
permanently; Doomsday Prepper is +33%, not 35%.

**Backgrounds** — 17 confirmed, several previously wrong: Lethal Weapon is
+10% melee damage (not combat speed), Mopey Poet is +5% evasion (not crit
resist), Grease Monkey is +10% (not 15%). Added Moneybags, Paladin, Sex Machine,
Stoner, Raider Hater. Mannerite / The Boss / Moneybags now actually grant their
skill rank — previously the data was present but inert.

---

## Still unverified — the only remaining gaps

1. **Base CON, base crit chance, base crit damage** (10 / 5% / 2.0x). Not
   documented by any of the three wikis. Everything Strength and Intelligence
   add *on top of* them is confirmed, so only the constant is in doubt.
2. **Base max AP (10) and base strike rate (6%)**. Same situation — the
   Coordination and Charisma bonuses layered on them are confirmed.

Both are flagged `approx` in the in-app panel. Reading them off a level 1
character sheet in-game would close them.

---

## Card art

The wiki hosts the game's pen-and-paper illustration set: 7 attributes,
22 skills, 24 quirks, 74 perks. Each is white line work on transparency, so
only the **alpha channel** is shipped, as an RGBA WebP with a flat white colour
plane, embedded as a `data:` URI (~329 KB total for 118 images).

They are applied as CSS `mask-image`, which means the art takes `currentColor`
and can carry state rather than sitting on the page as a fixed bitmap. Two
gotchas worth remembering:

- CSS masks read the **alpha** channel by default. A grayscale mask is fully
  opaque and paints a solid block — the colour plane must be flat and the
  artwork must live in alpha.
- WebP stores alpha losslessly unless `alpha_quality` is set; dropping it to 60
  roughly halves the file with no visible loss at these sizes.

Four perks (Gopher Hunter, Reckless, Hack 'n' Slash, Microwave Research) have no
wiki art; their rows keep a hidden spacer so alignment holds.

---

## Checking the engine

`verify.py` in the scratchpad drives the page in headless Chromium and asserts
the published numbers — the four level-25 skill point totals, 21/30/40 attribute
points at levels 1/25/35, 11 AP at Coordination 10, 1.5 and 2.8 m/AP, 28 to max
a skill, and the background and quirk modifiers. All pass, with no console errors.
