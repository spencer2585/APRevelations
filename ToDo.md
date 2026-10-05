# RE Revelations AP — TODO

## 0.0 — Recon & Groundwork (no mod code yet)
### Housekeeping
- [ ] Find the CE table, commit it to the repo under /research
- [ ] Record exact game build (Steam version, exe hash); everything below is tied to it
- [ ] Set up a research/notes.md: addresses, function names, struct layouts as found

### Inventory (from the CE table)
- [ ] Turn the CE inventory entries into stable pointer chains (survive restart + ASLR)
- [ ] Map the inventory slot struct: item ID, count, any flags
- [ ] Enumerate item IDs: key items, weapons, ammo, herbs, custom parts, misc
- [ ] Test: write an item into an empty slot via CE; does the game accept it cleanly?
- [ ] Test: same for a key item (does it work on its door with no pickup flag set?)

### Pickups (future location checks)
- [ ] Find the function that runs when an item is picked up (breakpoint on inventory write → walk up the stack)
- [ ] Find how "already collected" is stored per pickup (flag array? per-room table?)
- [ ] Figure out how a pickup is uniquely identified (room ID + index? global ID?)
- [ ] Genesis scanner pickups: same path as normal pickups, or separate? [split later]
- [ ] Handprints: viable as locations? where is the count/flag stored?

### Doors & map graph
- [ ] Analyze `map_connect_list` field layout (planned next step)
- [ ] Dump the full list to a file: door ID → room A ↔ room B
- [ ] Map room IDs to human-readable room names for the ship
- [ ] Confirm what `FsmSetLoadRoomEnableLoad` actually gates (one door? a room? an area?)
- [ ] Find where key-item locks are checked (separate from FSM gating?)

### Story / FSM
- [ ] Document how episode state is stored (current episode, chapter, progress flags)
- [ ] List every story trigger that teleports you, swaps character, or forces a cutscene [split later]
- [ ] Find out what breaks when you enter a room "too early" [split later]
- [ ] Find the episode/chapter load function and its arguments
- [ ] Test: force-load an off-ship episode from the ship via CE or a debugger
- [ ] Find what runs at episode end (next-chapter transition) — future hook point
- [ ] Map how character swaps handle inventory (separate structs per character?)

### Off-Ship Episodes
- [ ] List each off-ship episode: characters, start trigger, end trigger, pickups
- [ ] Find an existing interactable type that can be repurposed as the episode entrance
- [ ] Decide which ship room hosts each episode's entrance

### Saves
- [ ] Locate save file format / save-data struct in memory
- [ ] Decide: AP state in the game's save, or a sidecar file keyed by seed + slot

## 0.0.5 — Design Decisions (writing, not code)
- [x] Off-ship episodes: linear side areas, entered from a ship interactable
- [ ] Episode gating: own key item, ship progress, or open
- [ ] Episode repeatability: one-shot or replayable
- [ ] Character inventories: per-character AP items, shared, or fixed loadouts [split later]
- [ ] Define "RE parity baseline" concretely: which feature set counts as done for Batch 1
- [ ] Item pool: which items are progression vs useful vs filler
- [ ] Goal condition for an open-world ship
- [ ] Does the mod replace the FSM entirely, or only override door gating while the FSM keeps cutscenes/bosses? [split later]

## 0.1 — Hook Framework
- [ ] Decide language + hooking lib (reuse MCC's DLL injection setup where possible)
- [ ] Injector / loader working with a "hello world" log line in-game
- [ ] Resolve game base address + pattern scan for key functions (no hardcoded addresses)
- [ ] Hook pickup function: log the pickup ID only, don't block anything yet
- [ ] Hook pickup function: suppress the vanilla item grant
- [ ] Function to grant an arbitrary item ID to inventory
- [ ] Function to unlock a door by ID
- [ ] Decide IPC between DLL and client (named pipe? shared memory? embed apclientpp?)
- [ ] Function to launch an episode by ID
- [ ] Hook episode end → return to the stored ship room instead of the next chapter

## 0.2 — APWorld Skeleton
- [ ] Data dicts: items, locations, rooms (using your existing blueprint)
- [ ] Regions = rooms, connections built from the dumped `map_connect_list`
- [ ] Logic: key items gating doors
- [ ] Generates a seed with vanilla-ish logic
- [ ] Options: starting room, item pool toggles
- [ ] Each episode = one region connected to its host ship room
- [ ] Episode-internal locations with linear logic

## 0.3 — Client & End-to-End
- [ ] Client connects, receives items, sends checks
- [ ] Received items handled while in menus, cutscenes, and loads
- [ ] Reconnect / resync: re-grant items and re-send checks after reload
- [ ] DeathLink (optional)
- [ ] First full playthrough on a test seed

## 0.4 — Open World Pass [split later]
- [ ] Every ship door controlled by AP logic, not the FSM
- [ ] Softlock audit across early-access room combinations
- [ ] Boss / scripted-encounter handling when reached out of order
- [ ] Ship state preserved across episode entry/exit

## 1.0 — Full Open-World Queen Zenobia
- [ ] All items randomized
- [ ] Polish, setup docs, release

## Post-1.0
- [ ] Entrance randomizer (door graph is already in the APWorld; mostly logic work)
- [ ] Raid Mode