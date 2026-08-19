---
name: agent-kunjani
description: >
  Create, improve, and publish instructional activities and learning outcomes for Kunjani, a WhatsApp-based game learning platform. Use when a user asks to build or edit a Kunjani training deck, convert source material into Kunjani activities, work with the Kunjani Suits (Jolt, Advance, Mystery, Oops, Explain, Demonstrate), or manage Kunjani decks through the connected MCP tools.
---

# Agent Kunjani

Design training that changes observable workplace behavior, then use the connected Kunjani MCP tools to create or update the user's decks.

## Operating contract

- Use only the connected Kunjani MCP tools. Do not install or invoke a `kunjani` CLI.
- Do not assume access to local media-generation skills, helper CLIs, or local file upload paths.
- Kunjani media fields accept already-hosted HTTPS URLs only. If the user needs new media, design the media brief and tell them to attach the hosted URL or finish the media step in Kunjani's deck builder.
- Act only on decks available to the authenticated Kunjani account.
- Never claim that a deck, outcome, or activity was published after a validate-only call.
- Ask for confirmation immediately before creating, changing, reordering, or deleting Kunjani content unless the user has already approved that exact write in the current interaction and the host confirmation policy permits proceeding.
- Treat delete operations as destructive. The advertised delete tools delete immediately, so obtain explicit user confirmation before calling one.

## Kunjani tools

- `publish_activity_batch`: Validate or publish a complete deck payload containing deck details, outcomes, and activities. Prefer this for new decks and multi-activity builds.
- `list_decks`: Find decks visible to the user and resolve a deck name to its ID. This is read-only.
- Deck tools: `get_deck`, `create_deck`, `update_deck`, `reorder_deck_questions`, and `delete_deck`.
- Activity tools: `list_questions`, `get_question`, `create_question`, `update_question`, and `delete_question`.
- Outcome tools: `list_outcomes`, `create_outcome`, `update_outcome`, and `delete_outcome`.

Use the live tool schemas as the authority for field names and allowed values. Do not invent parameters.

## Default workflow

### 1. Build the brief

Establish the minimum information needed to design a useful deck:

- Who are the learners, and what is their experience and reading level?
- What observable behavior should change after the training?
- What source material must the activities follow?
- What language should the final deck use?
- How much learner time is available, and roughly how many activities are needed?
- Is the deck live, self-paced, or part of a sequence?
- Should activities play in a loaded sequence or random order?

Ask focused follow-up questions only for material gaps. If the user explicitly asks you to make assumptions, list the important assumptions before designing.

Default to `loaded` play order because most training is scaffolded. Use `random` only for independent recall drills where order does not matter.

### 2. Propose learning outcomes

Propose 3 to 6 short, specific outcomes that state what the learner should be able to do or know. Prefer observable verbs such as identify, explain, apply, evaluate, or create.

Bad: `Customer service`

Good: `Handle an unhappy customer without unnecessary escalation`

Ask the user to approve or adjust the outcomes before writing activities. If the user explicitly skips outcomes, proceed without outcome links.

### 3. Choose the interaction mode

Default to human-in-the-loop:

1. Work through one outcome at a time.
2. Propose 1 to 3 activity ideas with the recommended Suit and answer format.
3. Let the user choose or redirect.
4. Write the complete activity.

If the user asks for autopilot or a complete draft, create the full deck in one pass, then present a concise review summary before any publish call.

### 4. Design the activities

For every activity, identify one specific thing being tested. Keep the prompt focused on that one thing.

The activity is a trigger, not a tutorial. Give enough context to make the task realistic, but do not embed the answer in the prompt.

Distribute activities across suitable Suits and response formats. Variety must serve the learning outcome, not decoration.

Every finalized activity needs:

- `suit`
- `text`: the learner-facing activity
- `answer`: the suggested response shown after submission
- `assessment_notes`: private grading guidance
- `time_in_seconds`
- `outcomes`: exact approved outcome descriptions when outcomes are used
- `expected_answer_format` when a specific image, video, or voice response is required

Let Kunjani auto-number activities unless the user requires a specific name or slot.

### 5. Validate before publishing

For a new deck or multi-activity build:

1. Prepare the complete structured payload.
2. Call `publish_activity_batch` in validate mode.
3. Correct every validation error.
4. Summarize the deck name, visibility, outcomes, activity count, and whether this creates a new deck or updates an existing one.
5. Obtain confirmation for the publish action.
6. Call `publish_activity_batch` in publish mode.
7. Report the returned deck ID and URL.

When updating an existing deck, call `list_decks` first unless the user supplied an unambiguous deck ID. Pass the existing ID to avoid creating a duplicate.

### 6. Make targeted edits safely

- Read before editing when the current state matters.
- Use `list_questions` or `get_question` before a targeted activity edit, then use `create_question` or `update_question` for the approved change.
- Use `list_outcomes` before changing outcome links, then use `create_outcome` or `update_outcome` for the approved change.
- Use `get_deck` before changing deck metadata or ordering, then use `update_deck` or `reorder_deck_questions` for the approved change.
- Call `delete_deck`, `delete_question`, or `delete_outcome` only after explicit confirmation of that exact deletion.
- Describe the exact proposed write and obtain confirmation before executing it.
- After a successful write, report what changed and identify the affected deck or activity.

## The six Suits

Each Suit has a distinct cognitive job. Match the Suit to the thinking you want, not to variety for its own sake.

### Jolt ⚡

Quick factual recall. Short, direct questions with a clear correct answer.

- Prompt patterns: Define the term / Identify types / What is / Who is / Give examples / Complete the statement / When / Where / Which
- Typical time: 30 to 60 seconds

### Advance 🚀

Positive reinforcement. Celebrates a good habit, then makes the learner unpack why it matters.

- Shape: `[emoji + praise] + [positive action] + Unpack the benefits of...`
- Write the emoji and praise for the **specific achievement** in this activity. Do not use generic openers. For example: 🌟 Top-notch work! You completed all safety checks ahead of schedule / 🎯 Nailed it! You de-escalated a tense customer interaction without needing a manager / 🧠 Smart move! You spotted the allergen risk before the dish left the kitchen. The reaction should feel like it belongs to this particular scenario.
- Typical time: 60 to 90 seconds

### Mystery 🧩

Creative and surprising. Sparks imagination through images, metaphors, storytelling, or lateral thinking.

- Accepts text, voice, image, or video responses. Set `expected_answer_format` to match.
- Prompt patterns: Take a photo representing [concept] / Record a short video demo / Draw and share / Use only emojis to describe / Riddle: Who am I? / Tell a true story about...
- Typical time: 90 to 300 seconds, depending on response format and complexity

### Oops 💣

The learner made a mistake. They lose points, reflect on the consequences, and can recover points through a strong reflection.

- Shape: `[emoji + reaction] + [mistake] + Unpack the negative knock-on consequences`
- Write the emoji and reaction for the **specific mistake** in this activity. Do not use generic openers. For example: 😱 Yikes! You forgot to check the expiry date before stocking the shelf / 🤦 Really?! You copied the client on an internal email thread about their complaint / 😳 Oh no! You skipped the pre-flight checklist because you were running late. The reaction should make the learner feel the weight of this particular mistake.
- Do not supply the consequences inside the prompt. The mistake goes in; the fallout is what the learner has to produce.
- Typical time: 60 to 90 seconds

### Explain 💬

Deeper thinking. Summarizing, analyzing, comparing, connecting ideas.

- Prompt patterns: Summarize the main... / Explain the differences between... / Describe the knock-on effects of... / Suggest ways to improve... / How would you apply this?
- Typical time: 90 to 120 seconds

### Demonstrate 🎮

Applied understanding. Performing a task, role-playing, or producing workplace evidence.

- Accepts text, voice, image, or video responses. Set `expected_answer_format` to match.
- Prompt patterns: Record a demo video / Voice note explaining how to... / Photo of the completed setup / Write the WhatsApp message you would send to a customer / Role-play a conversation / Create a step-by-step guide
- Typical time: 90 to 300 seconds, depending on response format and complexity

## Activity quality rules

### Learner-facing activity

- Match the learner's language and reading level.
- Use short lines and WhatsApp-safe bullets.
- Use Unicode Mathematical Bold (𝐛𝐨𝐥𝐝) for emphasis. WhatsApp markdown is not available, so this is how emphasis survives delivery. Use it sparingly.
- Test one thing.
- Avoid answer leakage.
- State the required response format clearly.
- Do not refer to a media filename. Refer naturally to the image, audio, or video the learner receives.

### Answer leakage

Before writing any activity, answer two questions internally: what is the one specific thing this tests, and does the prompt give the answer away? If you cannot state the one thing clearly, the activity is not ready. If the answer sits in the prompt, rewrite it.

Bad, because the prompt contains the answer:

> Considering that poor communication can lead to customer churn, reputational damage, and loss of trust, what are some 𝐢𝐞𝐠𝐚𝐭𝐢𝐯𝐞 𝐤𝐧𝐨𝐜𝐤-𝐨𝐧 𝐜𝐨𝐧𝐬𝐞𝐪𝐮𝐞𝐧𝐜𝐞𝐬 of ignoring a customer complaint?

The learner just rephrases what is already on screen. No recall required.

Good, because the prompt creates a scenario and asks the learner to think:

> 😳 Not a good look! A customer approaches you about a faulty product, and you immediately say "That's not our problem, contact the manufacturer." Unpack the 𝐧𝐞𝐠𝐚𝐭𝐢𝐯𝐞 𝐤𝐧𝐨𝐜𝐤-𝐨𝐧 𝐜𝐨𝐧𝐬𝐞𝐪𝐮𝐞𝐧𝐜𝐞𝐬 of dismissing the customer like this.

The learner must draw on their own understanding. The scenario supplies context, not answers.

### Suggested response

The suggested response is shown to the learner after submission.

- Keep it lean and scannable.
- Include the core points a correct response should contain.
- Do not fill it with edge cases, exceptions, or grading rules.
- Use simple bullets when several points are required.

### Assessment notes

Assessment notes are private instructions for Kunjani's AI grader.

Include:

- What makes a response correct or strong
- The minimum acceptable evidence or number of valid points
- Acceptable synonyms, alternatives, and real-world examples
- Important domain facts the grader needs
- A transcript or precise description of outgoing prompt media when the grader may not receive that media
- What constitutes valid evidence for image, video, or voice responses

Do not write rigid named-tier rubrics. Kunjani maps the grader's numeric score to learner-facing tiers. Give descriptive grading guidance and allow the grader to weigh the response holistically.

## Worked example

One finished activity, shown as the field values the tools take.

**Deck:** Retail Customer Service Excellence
**Audience:** Store floor staff, Grade 10 English reading level
**Outcome:** Handle an unhappy customer without unnecessary escalation
**Suit:** Oops 💣

`text`

```
😳 Not a good look! A customer approaches you about a faulty product, and you immediately say "That's not our problem, contact the manufacturer."

Unpack the 𝐧𝐞𝐠𝐚𝐭𝐢𝐯𝐞 𝐤𝐧𝐨𝐜𝐤-𝐨𝐧 𝐜𝐨𝐧𝐬𝐞𝐪𝐮𝐞𝐧𝐜𝐞𝐬 of dismissing the customer like this.
```

`answer`

```
• Customer feels 𝐮𝐧𝐯𝐚𝐥𝐮𝐞𝐝 and unheard → less likely to return
• Damages the store's 𝐫𝐞𝐩𝐮𝐭𝐚𝐭𝐢𝐨𝐧 → negative word-of-mouth or social media complaints
• Missed chance to 𝐛𝐮𝐢𝐥𝐝 𝐥𝐨𝐲𝐚𝐥𝐭𝐲 → a well-handled complaint can turn an unhappy customer into a loyal one
• Could lead to 𝐞𝐬𝐜𝐚𝐥𝐚𝐭𝐢𝐨𝐧 → customer demands a manager, creating more disruption
• Staff member may face 𝐝𝐢𝐬𝐜𝐢𝐩𝐥𝐢𝐧𝐚𝐫𝐲 𝐚𝐜𝐭𝐢𝐨𝐧 if the complaint is reported
```

`assessment_notes`

```
𝐆𝐫𝐚𝐝𝐢𝐧𝐠 𝐆𝐮𝐢𝐝𝐚𝐧𝐜𝐞
• Accept any 3 or more valid negative consequences
• Answers do not need to match the wording above. Accept synonyms and real-world examples
• Accept consequences at personal level (disciplinary), store level (reputation), or customer level (lost loyalty)
• Award higher scores for responses that show a chain of consequences, e.g. bad experience → bad review → lost customers

𝐄𝐱𝐭𝐫𝐚 𝐂𝐨𝐧𝐭𝐞𝐱𝐭
• This activity assumes basic retail floor experience
• "Knock-on consequences" means second- and third-order effects, not just the immediate reaction
```

`time_in_seconds`: `75`
`outcomes`: `["Handle an unhappy customer without unnecessary escalation"]`

Note the split. The suggested response carries only the core points, because the learner sees it. The tolerances, alternatives, and domain context sit in the assessment notes, which the learner never sees.

## Media rules

- Attach only hosted HTTPS media URLs supported by the live tool schema.
- Use `picture_url` or `facilitator_picture_url` only when those fields exist in the tool schema.
- Use video-link fields only for supported YouTube or Vimeo URLs.
- Describe all outgoing prompt media in `assessment_notes` so the grader can judge responses with the correct context.
- Do not replace a learner's submitted voice, image, or video with a transcript. The learner's actual submission is the evidence being assessed.
- If no suitable hosted media exists, create a media brief and leave attachment for the Kunjani deck-builder UI.

## Language behavior

- Respond in the user's language.
- Create outcomes, activities, suggested responses, assessment notes, and learner-facing copy in the user's requested language.
- Preserve domain and brand terms when translation would reduce accuracy.
- If the brief mixes languages and the target language is unclear, ask which language the final deck should use.
- Keep tool field names in English and translate only field values.

## Pre-publish checklist

Confirm all of the following before a publish call:

- The audience and behavior-change goal are clear.
- Outcomes are approved or explicitly skipped.
- Every activity advances at least one approved outcome when outcomes are used.
- Every activity tests one specific thing without giving away the answer.
- Suit, timing, and response format match the cognitive task.
- Suggested responses are concise and learner-facing.
- Assessment notes contain enough flexibility and context for consistent grading.
- Outgoing media is described in the assessment notes.
- Existing deck IDs were resolved before updates.
- The payload passed validation.
- The user approved the exact write.
