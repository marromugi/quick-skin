---
name: phrasing
description: "The principles for what a design must do for the person using it: twelve principles in four groups — see, choose, respond, protect. Use it when setting a design direction, when designing a screen or a flow, when implementing a screen, and when reviewing a UI PR — whenever work of any kind touches UI. Also use it when the developer says 「デザインの方針を決めて」「UX を見て」「導線を整理して」「使いにくい」「この画面どう思う」. Values for colour, corner radius, and motion are left to the code's tokens; where code goes is left to composer. Not for terminal output or logs."
---

# phrasing

phrasing decides what a design must do for the person using it: what it
shows and what it lets them choose, how it responds and what it protects
them from.

Visual values live in the code's tokens. Where code goes is composer's
call. phrasing comes before both: it decides the role the design plays.

## When to use it

- When setting a design direction.
- When implementing a screen.
- When reviewing a UI PR, checking against `references/checklist.md`.

## Principles

Every principle is written in the same form: one rule in bold, the reason
after it, and one question at the end. If the question cannot be answered
"yes", the design is not playing its role.

Principles can pull against each other. When they do, weigh which one to
follow, decide, and record why.

### See

The design lets people grasp what is where without hesitating.

#### Regions and space

**The design divides the screen into regions and shows what each region is
for.** Space makes the boundary between regions. Without enough space, a
region blurs into its neighbour. Separate with space before reaching for
lines or borders.

Question: Can someone seeing it for the first time point at each region and
say what it is for?

#### Amount of information and disclosure

**The design keeps only the information that helps the person.** The more
there is, the harder it is to find anything. Consider showing secondary
actions only when they are needed; popovers and tooltips are ways to do
that.

Text is the last resort. When text overflows, nobody knows which part to
read. Express what you want to say through the look of the state first:
dim what is disabled, change the colour of what is selected. Put into words
only what the look cannot express.

Hide an action only when all three hold:

- It is used rarely.
- The goal can be reached without it.
- Once found, it can be found again.

Question: Can you give a reason for each element and each piece of text on
the screen?

#### Hierarchy and current location

**The design has a hierarchy and shows where in it the person is now.** A
sidebar or a navigation bar shows the current location. When the level
above is visible, the way back is clear too. Modals and popovers are
temporary layers, not positions in the hierarchy.

Question: From the screen alone, can you say where you are and how to go up
one level?

### Choose

The design makes it clear, without hesitation, what to do now.

#### The primary action follows the state

**The design leads by the shortest path to the primary action for the role
of the place.** The primary action changes with the state: right after a
save fails, it is retry. Keep to one primary action and put it where it
stands out.

Question: Can someone seeing it for the first time point at the primary
action within three seconds?

#### Only guidance that fits the context

**The design shows the current state and the next move,** but only guidance
that fits what this person is doing. Guidance not everyone needs is noise to
those who do not. Before showing guidance, ask whether this person needs it
now.

Question: Does the person doing this right now need the guidance being
shown?

### Respond

The design responds when touched and when pressed, while people wait and
when they try to leave. Without a response, people assume it is broken.

#### Sense of agency

**The design responds where it was touched, the moment it was touched.** A
late response breaks the sense of "I did that"; so does one that happens
somewhere else. Hover and press both respond on the element that was
touched.

Question: The moment something is touched, does that very spot change?

#### Layer and origin

**What an action brings up sits above everything else.** Right after acting,
the person is looking at what the action produced. Squeezed into the same
layer as the rest of the page, it hides what happened. So it goes one layer
up, and the difference in layer gives it emphasis.

How it appears follows the metaphor of where it came from. A popover from a
button scales up and fades in from the button's position at the same time,
so the metaphor holds that something large came out of a small button. This
extends the sense of agency.

Sometimes the origin cannot be followed. A modal appears across the whole
screen rather than out of the button, because the space, the amount of text,
and the weight its content needs outweigh belonging to its origin. That is a
constraint the app's structure imposes. Weigh belonging to the origin
against the weight of the content, and decide which to follow.

Question: Does what an action brings up sit on a higher layer, and has it
been decided whether it follows its origin or the structure?

#### 200 ms or less

**A response finishes within 200 ms.** Anything longer reads as a delay, not
a response. Where states change often, switch instantly with no transition;
a transition leaves an afterimage.

The only exception is a deliberate effect, such as a message celebrating
completion.

Question: If any response takes longer than 200 ms, is it a deliberate
effect?

#### Never leave people stuck

**The design does not block what the person is doing. When it must, it says
why.** Blocking is any state where the person cannot move: a loading state
that disables everything, a prompt that stops them leaving, a button that
cannot be pressed, guidance that cannot be closed. A block with no reason
looks broken.

Give the reason through the look of the state first. A button that cannot be
pressed or a field that cannot be filled is enough when it looks disabled;
do not line up a sentence of explanation beside it. Why text is kept short
is in "Amount of information and disclosure". If a reason is added, keep it
to a tooltip.

Question: Does every state where the person cannot act show a reason?

#### The state is readable

**The design shows whether something is in progress, pending, succeeded, or
failed.** If nothing changes after a press, nobody knows whether it
registered. Show in-progress as in-progress, and the result when it ends. On
failure, add what to do next.

Show the state on the look of the pressed element first. Add text only when
the look cannot express it, such as the next move after a failure.

Question: At each stage after a press, can you read which state it is in?

### Protect

The design protects people from mistakes and keeps their predictions right.

#### A way back, or a confirmation

**The design gives a way to undo what can be undone, and a confirmation for
what cannot.** A dangerous action always passes through a confirmation. Too
many confirmations teach people to press without reading, and then they fail
where it matters. So confirmations are kept to what cannot be undone.

Question: Does every action that cannot be undone pass through a
confirmation, and can everything else be undone?

#### The same action, the same form

**The same action looks the same and responds the same way everywhere.**
Predictions that come true build the sense of agency; each one that fails
forces relearning. Where possible, cut it out as a shared component and
reuse it as an asset of the whole software.

Question: Where the same action appears elsewhere, does it look and respond
the same?

## How to use it

When deciding a design:

1. Ask each question in order: See, Choose, Respond, Protect.
2. Fix any principle answered "no" before moving on.
3. If there is a reason it cannot be fixed, write that reason in the record
   of the work.

When reviewing:

1. Ask the questions in `references/checklist.md` from the top.
2. Point out every "no". Each finding says three things:
   - which principle it breaks;
   - where it happens;
   - what that does to the person.

## Out of scope

- Values for colour, corner radius, shadow, and motion live in the code's
  tokens.
- Where code goes and how components are cut is composer's call.
- Terminal output and logs.

## References

- `references/checklist.md` lists the twelve questions.
