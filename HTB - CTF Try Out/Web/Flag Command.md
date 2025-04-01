# Flag Command
![Image](https://github.com/user-attachments/assets/df2c8ae3-946f-4d8f-ac3c-1347a71b69ae)

## Writeup
![Image](https://github.com/user-attachments/assets/4c4899bc-de02-40b0-a722-d99c9ba18f86)

"Flag Command" is a simple text-based adventure-story web game. At the start, we are prompted with four options to continue the story:

![Image](https://github.com/user-attachments/assets/50eee9cc-4781-4bd3-90f5-353713c1cf3f)

Looks like a terminal environment. Let's try to run a custom command and see if that helps in enumeration:

![Image](https://github.com/user-attachments/assets/35f97cef-2360-4737-ae2d-cd6ec5586d3d)

This isn't working. Let's try to open up **DevTools** and look around.

![Image](https://github.com/user-attachments/assets/6aad9085-4a52-4b11-80d4-336d667a0244)

![Image](https://github.com/user-attachments/assets/d2b7a74d-5412-462a-993c-0fcdad9581fc)

In the HTML code, I can see three `JavaScript` files from which the whole setup likely works.

The three main files paths are:
- `/static/terminal/js/commands.js`
- `/static/terminal/js/game.js`
- `/static/terminal/js/main.js`

Upon inspecting them, I can tell that `command.js` and `game.js` have nothing useful, but `main.js` contains an important code segment:

![Image](https://github.com/user-attachments/assets/ef831588-0170-4960-9f68-27dfc27e14d0)

Essentially, this `if` statement verifies whether the current command is EITHER in the array of accepted commands (`head north`, `mysterious path`, `set up camp`) OR in a hidden array of commands that also meet the condition to proceed inside the loop. Our goal is to determine which commands are included in this hidden array, as they likely contain information necessary to find the flag.

Scrolling to the bottom of the file reveals a request being sent to `/api/options`, receiving a response, and then exporting it as a JSON file.

![Image](https://github.com/user-attachments/assets/761abbc5-9c63-4b13-ad19-fe711b23b2ba)

Let's see the content of the `options` file:

![Image](https://github.com/user-attachments/assets/c8d7d59c-a2b7-4abd-80c3-02ac7dc9fdd8)

Here it is! This JSON file contains all the options, and inside, there is a secret option: `Blip-blop, in a pickle with a hiccup! Shmiggity-shmack`.

![Image](https://github.com/user-attachments/assets/bbb224d6-6690-4c3d-af15-248bf5370559)

**HTB{D3v3l0p3r_t00l5_4r3_b35t_wh4t_y0u_Th1nk??!_1df693cde9e6473b2fb6650f21c82305}**
