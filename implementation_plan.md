# Penguin Hacker RPG - Implementation Plan

This document outlines the specific steps needed to implement the improvements identified in the status report, focusing on the locked door system and terminal functionality.

## 1. Fix Terminal Help Command

The terminal help command issue appears to be related to how back-ticks are used in the PHP code. In `npc_handler.php`, the `handle_terminal_command` function is using back-ticks instead of double quotes for string interpolation.

### Steps:

1. Modify `npc_handler.php` around line 303:
   ```php
   if ($cmd === 'help') {
       return "
       > AVAILABLE COMMANDS:
       > help - Display this help message
       > ls - List files in current directory
       > cat [file] - Display file contents
       > whoami - Display current user
       > ping - Test network connection
       > status - Show system status
       > override --auth=[USERNAME] - Override security for authorized users
       > search [string] - Search for files containing string
       > exit - Close terminal session
       ";
   }
   ```

2. Apply the same fix to all other return statements in the `handle_terminal_command` function that use back-ticks.

## 2. Implement Door Locking System

Currently all doors are walkable by default and display as green. We need to add a locked state.

### Steps:

1. Add locked state to gameState in `script.js`:
   ```javascript
   // At the top of script.js with other gameState properties
   const gameState = {
       // ... existing properties
       lockedDoors: {},
       // ... other properties
   };
   ```

2. Modify the exit texture creation in `createTexture()` function around line 1163:
   ```javascript
   // Exit textures
   if (type.includes('exit')) {
       const isLocked = type.includes('locked');
       
       // Set color based on lock status (red if locked, green if unlocked)
       ctx.fillStyle = isLocked ? '#aa0000' : '#00aa44';
       ctx.fillRect(16, 16, 96, 96);
       
       ctx.strokeStyle = isLocked ? '#ff0000' : '#00ff66';
       ctx.lineWidth = 4;
       ctx.strokeRect(16, 16, 96, 96);
       
       // Add lock icon if locked
       if (isLocked) {
           ctx.fillStyle = '#ffffff';
           ctx.beginPath();
           ctx.rect(48, 40, 32, 24);
           ctx.fill();
           ctx.beginPath();
           ctx.arc(64, 40, 16, Math.PI, 2 * Math.PI);
           ctx.fill();
       } else {
           // Arrow based on direction
           ctx.fillStyle = '#ffffff';
           ctx.beginPath();
           
           if (type === TileType.EXIT_RIGHT || type === TileType.EXIT_RIGHT_LOCKED) {
               ctx.moveTo(40, 40);
               ctx.lineTo(88, 64);
               ctx.lineTo(40, 88);
           } else if (type === TileType.EXIT_LEFT || type === TileType.EXIT_LEFT_LOCKED) {
               ctx.moveTo(88, 40);
               ctx.lineTo(40, 64);
               ctx.lineTo(88, 88);
           } else if (type === TileType.EXIT_TOP || type === TileType.EXIT_TOP_LOCKED) {
               ctx.moveTo(40, 88);
               ctx.lineTo(64, 40);
               ctx.lineTo(88, 88);
           } else if (type === TileType.EXIT_BOTTOM || type === TileType.EXIT_BOTTOM_LOCKED) {
               ctx.moveTo(40, 40);
               ctx.lineTo(64, 88);
               ctx.lineTo(88, 40);
           }
           
           ctx.fill();
       }
   }
   ```

3. Add locked exit tile types in the TileType enum:
   ```javascript
   const TileType = {
       // ... existing types
       EXIT_RIGHT_LOCKED: 'exit_right_locked',
       EXIT_BOTTOM_LOCKED: 'exit_bottom_locked',
       EXIT_TOP_LOCKED: 'exit_top_locked',
       EXIT_LEFT_LOCKED: 'exit_left_locked',
       // ... other types
   };
   ```

4. Update `handleExit()` function around line 1901 to check if a door is locked:
   ```javascript
   function handleExit(exitType) {
       console.log("Handling exit of type:", exitType);
       
       // Check if this is a locked exit
       if (exitType.includes('locked')) {
           showDialogue("This door is locked. Find a way to unlock it first.", 3000);
           return;
       }
       
       // Rest of the function remains the same
       // ...
   }
   ```

5. Update level generation to start with locked doors:
   ```javascript
   // When loading a level, call this function
   function setupLockedDoors() {
       // Generate unique ID for each exit in the level
       const levelKey = `${gameState.currentLevel[0]}_${gameState.currentLevel[1]}`;
       
       // Find all exit tiles in the level
       const exits = [];
       for (let z = 0; z < GRID_HEIGHT; z++) {
           for (let x = 0; x < GRID_WIDTH; x++) {
               const tileValue = gameState.levelData.layout[z][x];
               const tileType = tileMapping[tileValue];
               
               if (tileType && (
                   tileType === TileType.EXIT_RIGHT || 
                   tileType === TileType.EXIT_LEFT || 
                   tileType === TileType.EXIT_TOP || 
                   tileType === TileType.EXIT_BOTTOM)) {
                   
                   exits.push({
                       x: x,
                       z: z,
                       type: tileType,
                       id: `exit_${levelKey}_${x}_${z}`
                   });
               }
           }
       }
       
       // For each exit, check if it should be locked
       exits.forEach(exit => {
           // If this is the first level, only lock the forward exits
           if (gameState.currentLevel[0] === 0 && gameState.currentLevel[1] === 0) {
               // Keep the entrance (where we came from) unlocked
               if (exit.z < 15) { // If not the entrance from bottom
                   if (!gameState.lockedDoors[exit.id]) {
                       gameState.lockedDoors[exit.id] = true;
                   }
               }
           } 
           // Otherwise lock all exits that haven't been unlocked yet
           else if (!gameState.lockedDoors.hasOwnProperty(exit.id)) {
               gameState.lockedDoors[exit.id] = true;
           }
       });
       
       // Update visuals for locked doors
       updateDoorVisuals();
   }
   ```

6. Call `setupLockedDoors()` at the end of `loadLevel()` function.

7. Create function to update door visuals:
   ```javascript
   function updateDoorVisuals() {
       // Go through all objects and update door appearances
       for (const obj of interactableObjects) {
           if (obj.userData.type.includes('exit')) {
               const exitId = `exit_${gameState.currentLevel[0]}_${gameState.currentLevel[1]}_${obj.userData.gridX}_${obj.userData.gridZ}`;
               
               // Determine if this exit should be locked
               const isLocked = gameState.lockedDoors[exitId] === true;
               
               // Update visual based on original direction
               let baseType = obj.userData.type;
               if (baseType.includes('locked')) {
                   baseType = baseType.replace('_locked', '');
               }
               
               // Set new type
               const newType = isLocked ? `${baseType}_locked` : baseType;
               
               // Only update if changed
               if (newType !== obj.userData.type) {
                   obj.userData.type = newType;
                   
                   // Update material
                   obj.material.map = createTexture(newType);
                   obj.material.needsUpdate = true;
               }
           }
       }
   }
   ```

## 3. Connect Puzzles to Door Unlocking

Once we have the door locking system, we need to connect puzzles to door unlocking.

### Steps:

1. Add functions to assign puzzles to doors:
   ```javascript
   function assignPuzzlesToDoors() {
       // Find all puzzles in the level
       const puzzles = interactableObjects.filter(obj => obj.userData.type === TileType.PUZZLE);
       
       // Find all locked exits
       const lockedExits = [];
       for (const obj of interactableObjects) {
           if (obj.userData.type.includes('exit') && obj.userData.type.includes('locked')) {
               lockedExits.push(obj);
           }
       }
       
       // If we have both puzzles and locked exits, assign them
       if (puzzles.length > 0 && lockedExits.length > 0) {
           // For simplicity, assign one puzzle to each door
           for (let i = 0; i < Math.min(puzzles.length, lockedExits.length); i++) {
               const puzzle = puzzles[i];
               const exit = lockedExits[i];
               
               // Store the connection
               const exitId = `exit_${gameState.currentLevel[0]}_${gameState.currentLevel[1]}_${exit.userData.gridX}_${exit.userData.gridZ}`;
               puzzle.userData.unlocksExitId = exitId;
               
               console.log(`Puzzle at [${puzzle.userData.gridX}, ${puzzle.userData.gridZ}] assigned to unlock exit at [${exit.userData.gridX}, ${exit.userData.gridZ}]`);
           }
       }
   }
   ```

2. Call `assignPuzzlesToDoors()` after `setupLockedDoors()` in the `loadLevel()` function.

3. Modify the puzzle completion logic to unlock doors:
   ```javascript
   function submitPuzzleSolution() {
       // Existing code...
       
       // Add to the AJAX fetch response handling:
       .then(data => {
           // Display the message
           puzzleMessage.textContent = data.message;
           
           // If solved successfully
           if (data.correct) {
               // Check if this puzzle unlocks a door
               if (gameState.activePuzzle.userData.unlocksExitId) {
                   // Unlock the door
                   gameState.lockedDoors[gameState.activePuzzle.userData.unlocksExitId] = false;
                   
                   // Update visuals
                   updateDoorVisuals();
                   
                   // Show feedback
                   showDialogue("You hear a door unlocking in the distance.", 3000);
               }
               
               // Close puzzle interface after a delay
               setTimeout(() => {
                   puzzleInterface.style.display = 'none';
                   gameState.activePuzzle = null;
               }, 3000);
           }
       })
   }
   ```

## 4. Enhance Visual Feedback

Add additional feedback to make the puzzle-door connection more clear:

1. Modify door coloring to make locked/unlocked more obvious.
2. Add particle effects when a door unlocks.
3. Add sound effects for unlocking doors.

## 5. Testing Plan

1. Test terminal help command to ensure it works properly.
2. Test door locking system:
   - Verify doors start locked (red)
   - Verify locked doors block passage
   - Verify unlocked doors allow passage
3. Test puzzle-door connection:
   - Complete a puzzle and verify the assigned door unlocks
   - Verify visual feedback works when a door unlocks
4. Test end-to-end gameplay flow through multiple levels

## 6. Future Enhancements

Once the core functionality is working:

1. Add door complexity with different unlock requirements:
   - Some doors require multiple puzzles to open
   - Some doors require specific skill levels

2. Add progression tracking:
   - Track which puzzles have been completed
   - Show indicators for incomplete puzzles
   - Guide player toward uncompleted content

3. Improve puzzle variety:
   - Add more puzzle types
   - Increase difficulty based on level
   - Add randomized elements to puzzles 