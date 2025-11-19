🎧 # **LiveKit Interrupt Handler** ## Filler Filtering + Smart Interrupts (Hindi + English)
 
This project extends the LiveKit Agents event loop with a custom InterruptHandler, enabling intelligent interruption handling for real-time conversational agents.
The handler filters filler words, listens for stop commands, ignores low-confidence ASR noise, and supports Hindi + English mixed speech — all without modifying any LiveKit core SDK code.
This module is fully async, thread-safe, and integrates seamlessly with LiveKit’s callback system.


📌 **1. What Changed** (Overview of All Additions)

✅ New Module Added

**interrupt_handler.py**

-> Implements advanced interruption logic

-> Duck-typed to integrate with any LiveKit-style agent

✅ New Example Demo

**main.py** (offline simulation)

-> Demonstrates real-time behavior without connecting to LiveKit cloud

-> Simulates agent speaking + incoming ASR events

 ✅ New Features

|  Feature                               |   Description   |
|-----------------------------------|-----------------|
| **Filler filtering (Hindi + English)** | Ignores fillers like *uh, hmm, umm, haan, arre, acha…* only while agent speaks |
| **Real interruption detection**        | Detects stop-words like *stop, wait, pause, no, hold* |
| **Low-confidence noise filtering**     | Ignores ASR segments below threshold while agent speaks |
| **Dynamic list normalization**         | Handles stretched fillers: *“ummm”*, *“haaannn”* → normalized |
| **Runtime-safe locking**               | All shared state protected using an **asyncio.Lock** |
| **Optional HTTP config server**        | Allows modifying ignored words + threshold dynamically |


| Parameter                |  Meaning                           |
| ------------------------ | ---------------------------------- |
|  ignored_words           | Base list of filler words          |
|  confidence_threshold    | Threshold for ASR confidence       |
|  http_config_port        | Starts a config server if provided |


🌐 New Hindi Support

Extended filler list includes:

{ arre, arey, acha, achha, ohh, theek, sahi, yaar, bas}

These are added without overwriting user-provided or env-based ignored lists.

✔️ **2. What Works** (Verified Manually + via pytest)                                                                                                                  
     Manually Verified Through main.py Demo :                                                                                                                       
-> Agent ignores fillers when speaking:
   “uh”, “hmm”, “umm”


-> Agent stops instantly when user says a stop keyword:
   “stop”, “wait”, “no”


-> Agent accepts fillers when silent:
    “umm” (agent silent → treated as valid)


-> Low-confidence ASR ignored:
    Confidence < 0.6 while agent is speaking


pytest Verification
Command:
pytest examples/interrupt_handler_demo/tests -q

All 4 tests pass:
-> filler ignored

-> stop keyword triggers pause

-> filler accepted when agent silent

-> low confidence segment ignored


⚠️ **3. Known Issues / Edge Cases**
Issue Description :
No public API validation : Since this is a demo agent, no actual LiveKit server is used
Complex multilingual grammar : Only handles simple Hindi fillers, not full grammar
Mixed-token tricky phrases :Example: “haan stop” works, but “stoooop” needs normalization rules
HTTP config server optional : Only starts if aiohttp installed

🧪 **4. Steps to Test** :

A. Create Virtual Environment
python -m venv .venv
.venv\Scripts\activate

B. Install Dependencies
Inside agents/examples/interrupt_handler_demo/:

->pip install aiohttp

(Tests also require pytest):
->pip install pytest

->pip install pytest-asyncio

C. Run Demo
From: 
C:\Users\<your-username>\agents\examples\interrupt_handler_demo>

Run:
python main.py

You will see output like:

[IGNORED] reason=FILLER_ONLY ...

[VALID] reason=STOP_KEYWORD ...

>>> DEMO: Agent TTS STOPPED <<<


<img width="1024" height="808" alt="Screenshot 2025-11-19 003958" src="https://github.com/user-attachments/assets/5568d6c1-dcec-4c86-acea-f6b4c5b1b943" />


D. Run Automated Tests
From the project root:
pytest examples/interrupt_handler_demo/tests -q


<img width="712" height="81" alt="Screenshot 2025-11-19 004731" src="https://github.com/user-attachments/assets/dc8bebdf-625e-4cbf-b082-cc01e641f316" />



 🧩 5. How the Logic Works (Real World Scenarios)

| **User Speech**                          | **Agent Speaking?** | **Expected Behavior**                       | **Implemented?** |
|------------------------------------------|----------------------|----------------------------------------------|------------------|
| “uh”, “hmm”, “umm”                       | YES                  | Ignore                                       | ✅               |
| “wait one second”, “stop”, “no”          | YES                  | Stop agent immediately                       | ✅               |
| “umm” (filler)                           | NO                   | Treat as valid user speech                   | ✅               |
| “umm okay stop”                          | YES                  | Stop agent (contains stop keyword)           | ✅               |
| “hmm yeah” (confidence < threshold)      | YES                  | Ignore noise                                 | ✅               |


 🖥️ 6. Environment Details
|  Component       |  Version                                      |
| ---------------- | --------------------------------------------- |
| **Python**       | 3.10+ (tested on 3.12)                        |
| **OS**           | Windows 10+                                   |
| **Dependencies** | `aiohttp` (optional), `pytest` (testing only) |
| **Runtime**      | Asyncio event loop                            |


📁 7. Repository Structure After Changes
agents/
├── examples/
│   └── interrupt_handler_demo/
│       ├── interrupt_handler.py   ← NEW / UPDATED
│       ├── main.py                ← NEW DEMO
│       ├── README.md              ← THIS FILE
│       └── tests/
│           ├── test_basic.py
│           └── ...




🏁 Final Notes
This project demonstrates:

-> Real-Time ASR event handling

-> Async-safe state management

-> Hindi + English language flexibility

-> Plug-and-play interrupt filtering

-> Optional runtime config via HTTP

-> Fully tested behavior


