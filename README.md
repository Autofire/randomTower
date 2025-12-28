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

To select chunks, light up the TOP light on each of the chunk layouts you wish
to use. This will "prime" them for copying.


### Chunk Copying

Once enough chunks are selected to fill the row, the copying process will
then immediately begin.

#### Elements
 * **Read Head**:
   * This is used to read a row of elements within a chunk. Its main purpose is simply to travel through the elements, digging through them and then re-adding them.
   * It also destroys itself if it hits a steel wall. This way, it automatically cleans itself up and signals to Read Spawners that a Read Head has finished.
   * On its own, this isn't especially useful - we are relying on the fact that the "change it to" clause specifically *creates* items. This creation event can be listened to by other elements. And, on that note...

 * **Write Head (Primed)** and **Write Head (Cooldown)**:
   * The primed version triggers on the creation of *ANY* element, creates a duplicate of that element in its current location.
   * It also creates a **Write Head (Cooldown)** to the right of it - using the concept of being "on cooldown." This is done to help with timing. After a brief (0 frame) delay, it will then swap back to **Write Head (Primed)**.
   * This is meant to work in tandem with the **Read Head**, copying everything that it passes through during the reading process.*
   * Notably, a special exception is made for certain elements, such as the "space" letter element. This gets treated as an empty space during writing, as digging/creating empty spaces would otherwise get overlooked.
   * There may be more acceptions made to allow for other "volatile" elements to be copied, such as enemies.


 * **Read Spawner (Idle)**:
   * This marks the start of a row of elements within a chunk.
   * Doesn't do anything, but can be swapped to a **Read Spawner (Primed)** by pressing on the top-most spawner within a chunk.
   * In the future, this will also occur when the automated chunk selection system picks a chunk.

 * **Read Spawner (Primed)**:
   * Once primed, a read spawner will wait until its CE value drops to zero. Once that happens, it will do the following:
     1. Create a **Read Head**.
     2. Prime the **Read Spawner** below itself.
     3. Become idle.
   * The CE value drops in two ways:
     1. When other Read Spawners get primed -- this is to track order to start the reads in.
     2. When a previously spawned Read Head finishes reading -- allowing the sequence to continue.


#### Variables
*Unless otherwise noted, all variables are kept within the listed elements' CE Values.*

 * **chunksPerRow** - Number of chunks in a row before the reading process begins.
   * Read Spawner (Idle)
   * Read Spawner (Primed)
   * Write Spawner Parent (Primed)

 * **elementsPerRow** - Number of elements are in a single row - that is, `chunksPerRow * chunkWidth`.
   * Write Spawner Child (Primed)

 * **elementsPerChunkRow** - Number of elements within a row of chunks. Calculated via `chunksPerRow * chunkWith * chunkHeight`.
   * Write Spawner Catalyst (Idle)

 * **elementsPerArea** - Number of elements in an entire play area. Calculated via `chunksPerRow * chunkWidth * chunksPerColumn * chunkHeight`.
   * Level Load Trigger
