### Switching Table
---

### What is switching table

A switching table (also called a MAC address table or CAM table) is like a memory notepad inside a network switch.

* **What it does:** It keeps a list of every device connected to the switch and remembers exactly which plug (port) each device is using.
* **How it works:** It identifies each device using its unique hardware fingerprint, known as a **MAC address**.
* **Why it is important:** When a message arrives, the switch looks at this table so it can send the message *only* to the correct device. This keeps the network fast, private, and prevents traffic jams!

**Example:** Imagine a mailroom in a large office building. The switching table is like the directory on the wall. It says, "The printer is in Room 4 (Port 4)." When a letter arrives for the printer, the mailroom sends it straight to Room 4, instead of knocking on every single door in the building to find it.

---

### How switch fill its tables?

### 1. How a Switch Fills its Table (Learning)
A switch learns by "listening" to the traffic that comes into its ports. It always looks at the **sender** of a message to build its list.

*   **Step 1:** A device (like a computer) sends a data message (called a frame) into the switch.
*   **Step 2:** The switch opens the message and looks at the **Source MAC Address** (the hardware ID of the sender).
*   **Step 3:** The switch writes down that MAC address and the exact port number the message came from into its switching table. 
*   **Step 4:** It sets a timer. If the device doesn't send another message for a while (usually 5 minutes), the switch erases it to keep the table clean and up-to-date.

### 2. Finding Unknown Devices (Flooding)
What happens if the switch receives a message for a destination it hasn't learned yet?

*   If the destination MAC address is not in the table, the switch sends the message out of **every single port** (except the one it came from). This is called "flooding."
*   When the correct device receives the message and replies, the switch looks at the sender's address on the reply and adds *that* new device to the table.

**Example:**
Imagine Computer A is plugged into Port 1, and Computer B is plugged into Port 2. 
1. Computer A says, "Hello Computer B." 
2. The switch receives this on Port 1. It immediately writes in its table: *"Computer A is on Port 1."*
3. The switch doesn't know where Computer B is yet, so it shouts the "Hello" out of all other ports.
4. Computer B hears it and replies, "I'm here!" on Port 2.
5. The switch receives the reply and writes in its table: *"Computer B is on Port 2."* 
Now, the table is filled, and they can talk directly next time!  
 
