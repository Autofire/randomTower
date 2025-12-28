# Random Tower

This is an experimental roguelike built within the Rocks'n'Diamons engine.
See https://www.artsoft.org/ for the engine information.

This file will serve as some high-level documentation on how the game is
built - eventually, it may be broken out into other files to make it
easier to find things.

## Level Generation
Level generation is broken into several parts. From a high level, there are
(or will be) two stages that this generation is handled in:

 1. First, a layout is determined, chunk by chunk.
 2. Next, start with the first chunk row, a suitable chunk configs are queued.
 3. Once all chunk configs are selected, they are copied into the play area.
 4. This process repeats for each chunk row until all rows are copied.

### Level Layout Generation
TODO

### Chunk Selection 
Currently not automated - this must be done manually.


### Chunk Copying
TODO

To select chunks, light up the TOP light on each of the chunk layouts you wish
to use. This will "prime" them for copying.

Once enough chunks are selected to fill the row, the copying process will
then immediately begin.

#### Elements
 * **Read Spawner (Idle)**: This marks the start of a row of elements within a
   chunk. Doesn't do anything, but can be swapped to a **Read Spawner
(Primed)** by pressing on the top-most spawner within a chunk. In the future,
this will also occur when the automated chunk selection system picks a chunk.

 * **Read Spawner (Primed)**: Once primed, a read spawner will wait until its CE value drops to zero - which happens naturally once enough other readers are primed. Once that happens, it will do the following:

   1. Create a **Read Head**.
   2. Prime the **Read Spawner** below itself.
   3. Become idle.


#### Variables
 * **chunksPerRow** - Number of chunks in a row before the reading process begins.
   * Read Spawner (Idle): CE Value
   * Read Spawner (Primed): CE Value

