# ChatBCI-Experiment-Logs

This repository contains the real experimental logs from the **ChatBCI** study.  
ChatBCI is a P300-based brain-computer interface (BCI) speller system that integrates large language models (LLMs) to enable **word completion** and **(multi-word) prediction**, allowing users to compose free-form sentences based on their daily intentions and activities.

These logs were collected from real-time, in-vivo user experiments.

## Folder Structure

The directory is organized by subject ID (e.g., `S01`, `S02`, ... `S07`). Each subject folder contains three online session folders, typically named as: `OnlineSession1`, `OnlineSession2`, `OnlineSession-Improvising`
Each session folder contains up to three types of log files:
- `textLog.txt`: Records the evolving sentence over time.
- `wordLog.txt`: Shows the list of word suggestions at each spelling step.
- `selectLog.txt`: Captures the character/word selected at each decision point.

```plaintext
ChatBCI-Experiment-Logs/
├── S01/                        # Subject 1's session logs
│   ├── OnlineSession1/         # First online session: using ChatBCI
│   │   ├── textLog.txt         
│   │   ├── wordLog.txt        
│   │   └── selectLog.txt       
│   ├── OnlineSession2/         # Second online session: using letter by letter approach
│   │   ├── textLog.txt
│   │   └── selectLog.txt
│   └── OnlineSession-Improvising/  # Third session with improvised content using ChatBCI
│       ├── textLog.txt
│       ├── wordLog.txt
│       └── selectLog.txt
├── S02/
├── S03/
├── S04/
├── S05/
├── S06/
├── S07/
└── README.md                   # This documentation file
```

## Main Log: `textLog.txt`

Below is a sample snippet from `textLog.txt` for Subject **S01**:
```
2023/12/20_18:50:12
2023/12/20_18:50:12,>I want to buy a new phone
2023/12/20_18:50:46,I<
2023/12/20_18:51:23,I-<
2023/12/20_18:52:00,I-CAN-<
…
2023/12/20_19:03:49,I-WANT-TO-BUY-A-NEW-PHONE-<
```
---

### Log Structure Description

| Format Example                                 | Field             | Description                                                                 |
|------------------------------------------------|--------------------|------------------------------------------------------------------------------|
| `2023/12/20_18:50:12`                          | Timestamp only     | Session start time.                                                          |
| `2023/12/20_18:50:12,>I want to buy a new...`  | Final output       | Marked by `>`, this line shows the full sentence intended by the user.      |
| `2023/12/20_18:52:00,I-CAN-<`                  | Intermediate log   | Each line shows the current composed text after a selection cycle. Ends with `<` to indicate ongoing composition. |
| `-` (dash between words)                       | Space character    | The dash symbol (`-`) represents a space between words in the log.          |

Each intermediate line reflects the **user’s current composition state** and is recorded after each keystroke selection. These logs are useful for analyzing: Spelling progression Correction behavior and Efficiency of prediction/completion usage.

---

### Predictive Spelling in ChatBCI (also in Page 5 of Paper)

ChatBCI uses GPT-3.5 to provide two types of intelligent suggestions based on the sentence context:

- **Word Completion**: If the last word is incomplete, GPT suggests completions.  
  *Example*: For `I-WANT-TO-B`, GPT might suggest `BUY`, `BE`, etc.

- **Word Prediction**: If the last word is complete but the sentence is unfinished, GPT suggests the next word.  
  *Example*: For `I-WOULD-`, GPT might suggest `LIKE`, `CALL`, etc.

#### Integration Logic

To handle both scenarios in a unified way, ChatBCI applies two simple rules:

1. **Last Word Identification**:  
   The "last word" is defined as the substring after the final dash (`-`).  
   - Examples:  
     - `I` → Last word: `I`  
     - `I-WANT-TO-B` → Last word: `B`  
     - `I-WOULD` → Last word: `WOULD`  
     - `I-WOULD-` → Last word: (empty)

2. **Candidate Word Replacement**:  
   When the user selects a suggestion:
   - It **replaces** the last word in the current composition
   - A dash (`-`) is **appended** to indicate a space after insertion

   - Examples:  
     - `I-WANT-TO-B` + `BUY` → `I-WANT-TO-BUY-` (word completion)  
     - `I-WOULD-` + `LIKE` → `I-WOULD-LIKE-` (word prediction)


## Other Log Types and Format

In addition to `textLog.txt`, two other log files provide detailed traces of the ChatBCI experiment:

### 1. `selectLog.txt`

This file captures the **actual selections** made by the participant during each spelling cycle, with timestamps. Each line is formatted as: 
| Example [TIMESTAMP],[ACTION]                         | Meaning                                                                 |
|----------------------------------|-------------------------------------------------------------------------|
| `2023/12/20_18:50:12`            | Start of session                                                        |
| `2023/12/20_18:50:46,I`          | Selected character "I"                                                  |
| `2023/12/20_18:51:23,Space`      | Selected space                                                          |
| `2023/12/20_18:52:00,W2`         | Selected **second** word from the suggestion panel                      |
| `2023/12/20_18:52:37,DelWord`    | Triggered delete **last word**                                          |
| `2023/12/20_18:53:15,W`          | Selected character "W"                                                  |
| `2023/12/20_18:53:52,W4`         | Selected **fourth** word suggestion                                     |
| `2023/12/20_18:56:58,O`          | Selected character "O"                                                  |
| `2023/12/20_18:58:13,DelWord`    | Deleted word again                                                      |
| `2023/12/20_19:03:11,W1`         | Selected **first** word suggestion                                      |
| `2023/12/20_19:03:49,W3`         | Selected **third** word suggestion                                      |

> 💡 Notes:
> - `"W1"` to `"W10"` indicate which word (1st to 10th) was selected from the prediction panel.
> - `"DelWord"` is used to remove the previously composed word.
> - `"Space"` manually inserts a space.
> - Characters like `"A"`, `"B"`, `"C"` represent manually typed letters.

---

### 2. `wordLog.txt`

This file records the **prediction word list** shown to the user at each step. Each line includes a timestamp and a comma-separated list of 10 predicted words.


| Example ([TIMESTAMP],[WORD_1],[WORD_2],…,[WORD_10])                                                                               | Meaning                                                                                      |
|--------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|
| `2023/12/20_18:50:47,AM,CAN,WILL,HAVE,WANT,...`                                             | 10 prediction candidates shown to the user at this time                                      |
| `2023/12/20_18:55:07,BE,BUY,BECOME,BRING,...`                                               | GPT-3.5 generated 10 new suggestions based on sentence context                               |
| `2023/12/20_19:00:44,NEW,NICE,NINTENDO,NAIL,NECKLACE,...`                                  | Predicted completions for word starting with "N", e.g., `NEW`, `NECKLACE`                   |
| `2023/12/20_19:01:20,ONLINE,FOR,WITH,FROM,THAT,...`                                         | GPT predicting possible next words                                  |
| `2023/12/20_19:03:50,CASE,CHARGER,CONTRACT,COVER,...`                                      | Final predicted items possibly related to "PHONE" and input context                   |

> 💡 Notes:
> - These words are generated by ChatGPT-3.5 based on the current sentence context.
> - The user chooses one using "W1"–"W10" in `selectLog.txt`.
> - You can cross-reference these logs with `textLog.txt` to reconstruct user behavior.

---

Together, `textLog.txt`, `selectLog.txt`, and `wordLog.txt` provide a comprehensive timeline of user spelling intent, interface options, and actual selections in ChatBCI.