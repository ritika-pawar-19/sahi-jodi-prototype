# Sahi Jodi

A short memory game for store staff. It is **mandatory**: staff play it before the LMS opens.
It takes about a minute: a round is 60 to 70 seconds of play, plus a 16 second demo on the first visit.
(Measured with a player who reads every line to the end, takes 1.5 seconds per card and needs 19 to 22 flips:
about 63 seconds at 20 flips, 67 to 69 at 22. Add about 5 seconds for the deal and the change between grids.)
A round with slips (24 to 28 flips) takes 70 to 80 seconds.
There is no time limit, so nobody gets stuck.
There is no Later button and no close button.

Eight cards lie face down: three shoppers, three moves and two traps. The player flips two at a time.
A shopper and the right move make a pair ("sahi jodi"): both cards glow, the shopper smiles, and the deck's line for that moment shows.
A shopper with the wrong move reacts to that move (the man who is just looking edges away from the open palm) and both cards flip back. No words.
The two traps never pair: Iris springs out of her card and bats her lashes ("Customer first. Product later."),
and the pointing hand sends the shopper wandering in a circle ("Don't just point. Make the connection.").

A round is two grids. Grid 1: Anjali wants a laptop (walk her there), Mr Sethi is just looking (give space),
Meera finds the laptop desk empty (get help). Grid 2 brings in Pranav, who is already being helped (observe),
and Dadi, who has just walked in (ask a broad question), plus the grid 1 pair that took the most flips.

It teaches ushering from the Level 1 deck: the right move for each floor situation, and the two don'ts.
No sign-in. English only.

## What the player sees

1. **First time on this device only:** a 16 second demo plays by itself.
   A ghost hand flips Anjali and the hourglass (she checks her watch, the cards flip back),
   then Anjali and the open palm (a pair, and the line), then Iris (a trap). Then a big "Your turn" button.
   The demo cannot be skipped the first time.
2. The round: grid 1, then grid 2. The cards are dealt, shuffled and dealt again between the grids.
3. After each pair, a panel at the bottom shows the deck's line while the board waits.
   A trap shows its line only the first time it is turned in a round; after that it just plays its gag.
   In grid 2 the pair that comes back shows only its key sentence, as a reminder.
   The line moves on by itself (about 4.5 words a second, so 1.2 to 2.6 seconds a part).
   After 0.8 seconds the player can move it on sooner: a tap on the panel or its arrow (or Enter) shows the next part;
   a tap on any face-down card closes the line and flips that card, so play never waits. A tap in the first 0.8 seconds
   only makes the arrow bob.
4. The end card: score, stars, pairs, flips, time, one thing to remember, and **Enter LMS**. "Play again" is there too.
   The two buttons sit where the line panel was, so they only wake up about half a second after they appear,
   and a quick run of taps does not press them: a tap meant for the last line never ends the round or starts a new one.
5. Enter LMS closes the gate and the LMS opens.

On later visits the demo is skipped. The (?) icon at the top of the game plays it again whenever no cards are turning;
the player then goes back to the same board, with the same cards found. It is not offered on the end card,
so a curious tap never throws a finished round away.

Each round is a little different: the cards land in new places every grid (never the same board twice in a row),
and grid 2 brings back whichever grid 1 pair the player found hardest.

Archie's three lamps at the top start lit and go out one by one as flips are wasted. They match the stars on the end card.

## Files

| File | What it is |
| --- | --- |
| `sahi-jodi.html` | The game. One file with everything inside: styles, script, art and sound. |
| `demo.html` | A plain sample LMS page with the mandatory gate in front of it. Open it in a browser to try it. |
| `README.md` | This file. |
| `shots/` | Screenshots, one per beat: the gate, the demo, a wrong move, a pair and its line, the two traps, the end card, the LMS opening. |

Upload `sahi-jodi.html` to a place your LMS can serve a web page. The same server as the LMS is best.

## Try the demo

Open `demo.html`. The gate covers the page at once. Play a round and tap Enter LMS: the gate fades away.
Reload the page: it goes straight in, because the round is done for today.

**Reset demo** (bottom right, marked "Demo only") clears today's pass and the game's saved flags,
so the gate and the first-time demo show again. It is only for testing. Do not copy it into the LMS.

It works best from a web server. It also works as a local file in Chrome.

## Add the gate to your LMS

The idea: when the page loads, cover it with the game. When the game says `enter`, remove the cover.

1. Wrap your LMS page in one element, for example `<div id="lms-root">`.
2. When the user still has to play, render the page with `<html class="sj-gated">` and the gate below, right after `<body>`.
   (Rendering the class on the server means the LMS never flashes before the gate.)
3. When the user does not have to play, leave all of this out. The game is then not loaded at all.

```html
<style>
  html.sj-gated, html.sj-gated body { overflow: hidden; height: 100%; }
  html.sj-gated body { position: fixed; left: 0; right: 0; top: 0; }
  html.sj-gated #lms-root { filter: blur(6px); pointer-events: none; }
  #sj-gate { position: fixed; inset: 0; z-index: 2147483000; display: flex; align-items: center;
             justify-content: center; background: rgba(16, 12, 52, .58); transition: opacity .4s; }
  #sj-gate.leaving { opacity: 0; }
  #sj-frame { width: 320px; height: 400px; border: 0; border-radius: 16px; background: #5B4BDB; }
  @media (max-width: 600px), (max-height: 440px) {
    #sj-frame { width: 100%; height: 100%; border-radius: 0; }   /* phones: full screen */
  }
</style>

<div id="sj-gate" role="dialog" aria-modal="true" aria-label="Play the floor game to enter the LMS">
  <iframe id="sj-frame" src="sahi-jodi.html" title="Sahi Jodi game" allow="autoplay"></iframe>
</div>

<!-- at the end of <body> -->
<script>
(function () {
  var gate = document.getElementById('sj-gate');
  var frame = document.getElementById('sj-frame');
  var lms = document.getElementById('lms-root');
  var html = document.documentElement;
  if (!gate) return;

  html.classList.add('sj-gated');
  lms.setAttribute('inert', '');      // nothing behind the gate can be clicked or tabbed to
  frame.focus();                      // so the keyboard works at once

  function noEsc(e) { if (e.key === 'Escape') e.preventDefault(); }
  document.addEventListener('keydown', noEsc, true);

  window.addEventListener('message', function (e) {
    if (e.source !== frame.contentWindow) return;          // only messages from the game
    var d = e.data;
    if (!d || d.type !== 'sahi-jodi') return;
    if (d.event === 'complete') {
      // Send it to your server here: d.score (0 to 100), d.stars (1 to 3), d.flips, d.seconds, d.round.
      // The same round is sent again with d.repeat === true just before 'enter': keep one record per round.
    }
    if (d.event === 'enter') {
      document.removeEventListener('keydown', noEsc, true);
      lms.removeAttribute('inert');
      html.classList.remove('sj-gated');
      gate.classList.add('leaving');
      setTimeout(function () { gate.remove(); }, 400);     // also stops the game and its sound
    }
  });
})();
</script>
```

`demo.html` does the same and adds a few extras you can copy:
a line of text above and under the game on desktop, a focus loop that keeps the keyboard inside the gate (it tells the game which edge Tab wrapped from, with
`{type: 'sahi-jodi-focus', edge: 'start' | 'end'}`, so focus lands on the game's last or first control),
the "once a day" memory in the browser, a small "Floor game done today" note on the LMS page, and the demo-only reset.

If the game is on a different web address than the LMS, also check `e.origin` against the game's address.

## How often to show it

The LMS decides. Pick one:

- **Every login.** Show the gate every time the user logs in.
- **Once a day.** Show the gate if the user has no `complete` recorded today. This is what `demo.html` does, to show the idea.
- **Once ever.** Show the gate until the user's first `complete` is recorded.

**Our advice for this version: once ever, or at most once a week.** There are five shoppers and two traps. The cards move
every grid, but by the second or third day a player knows every pair, and a daily mandatory round will feel like a chore.
Show it more often only when more card sets exist.

Decide it on the LMS server if you can. `demo.html` keeps the date in the browser (local storage) only to show the idea.
To try each one: `demo.html?gate=login`, `demo.html?gate=day`, `demo.html?gate=ever`.

## Sizes

- Smallest: 300 x 360 px.
- Best: 320 x 400 px, or larger in the same shape (4:5). It scales up cleanly.
- Other shapes work too. The spare space is filled with the game's violet.
- Phones: full screen. `demo.html` switches to full screen below 600 px wide (or 440 px tall).

## URL options

Add these to the game's address, for example `sahi-jodi.html?pass=60`.

| Option | What it does |
| --- | --- |
| `?pass=60` | Below 60% the end card offers only "Play again", with an encouraging line. Without it, any score can enter. Use with care: a high bar can keep staff out of work. |
| `?demo=always` | Always play the how-to-play demo first. |
| `?demo=never` | Never play the demo. |
| `?close=1` | Shows a close X that sends `close`. Only for a later, optional (not mandatory) use. Do not use it in the gate. The X gets its own corner slot; the six pair dots move under the lamps. |
| `?shuffle=0` | For testing only: the cards always land in the same places, so a run can be repeated exactly. |

`demo.html` passes `?pass` and `?demo` on to the game, for testing: `demo.html?pass=60&demo=always`.

## Events (postMessage)

The game sends these messages to the page that holds it (`window.parent`):

| Message | When |
| --- | --- |
| `{type: 'sahi-jodi', event: 'complete', score, stars, flips, seconds, round}` | The round ended. `score` is 0 to 100. `stars` is 1 to 3. `flips` is how many cards were turned. `seconds` is the playing time. `round` counts the rounds played since the game opened (1, 2, ...). |
| `{type: 'sahi-jodi', event: 'complete', score, stars, flips, seconds, round, repeat: true}` | Sent again just before `enter`, for the same round (same `round` number). |
| `{type: 'sahi-jodi', event: 'enter'}` | The player tapped Enter LMS. Remove the gate. It always comes after `complete`. |
| `{type: 'sahi-jodi', event: 'close'}` | Only with `?close=1`. The gate ignores it. |

So `complete` can arrive twice for one round, and once more for each "Play again". If you save it on your server,
keep one record per `round` (or ignore messages with `repeat: true`).

How the score works: each grid is worth 50 points. The first 11 flips of a grid are free (with a perfect memory a grid
takes about 10). Each extra flip costs 3 points. A **slip** costs 5 more: turning a card you have already seen that does
not pair, that is, choosing a wrong move for a shopper, or a trap you already know. A grid never drops below 10 points.
A first look at any card is never a slip, so finding a trap by chance only costs the flip.
3 stars from 85%, 2 stars from 55%. "Six pairs. Zero hovering." is kept for 95% and above with no slips.
The "To remember" line comes from the biggest slip (the shopper you gave a wrong move, or the trap you went back to).
The messages carry no personal data. Check `e.source === iframe.contentWindow` so you only act on messages from the game.

## Score and SCORM

This version has no SCORM. To keep scores in the LMS, listen for `complete` and save it on your server.
The game also keeps the player's best score and "demo seen" in the browser's local storage.
It still works if storage is blocked; it just cannot remember that the demo was seen.

## How strict is the gate? (please read)

The gate runs in the browser. It stops normal use: there is no button, link or key that gets past it.
But someone who knows how can get past it, for example with the browser's developer tools
(remove the cover, or send a fake `enter` message).
Clearing browser storage does not help them: it only makes them play again.

For real enforcement, record the `complete` event on the LMS server, with the user and the time,
and let the server decide whether to show the gate. Even then, a determined user could fake the message.
Treat the game as a training step, not an exam.

Also:
- Esc does nothing while the gate is up. Inside the gate the browser's Back button leaves the page as usual
  (the game adds no history entry there); the gate shows again on the next visit until a round is done.
  Opened on its own as a pop-up (not in a frame), the game holds the first Back press so it is not closed by accident.
- If the game file cannot load, the gate stays up. Keep a quick way to switch the gate off on the server.

## Offline and fonts

The game works without internet once the page is open. It asks Google Fonts for the Poppins and Figtree fonts.
The request does not hold the game up: if it fails or hangs (a slow or filtered store network), the game starts at once with
the device's own font and switches to Poppins if it arrives. Nothing else is loaded from outside.
All sounds are made in the browser (no audio files). Sound is off at the start; the speaker button turns it on.
`demo.html` loads nothing from outside.

## Browser support

- Desktop: current Chrome, Edge, Firefox and Safari.
- Phones: Chrome on Android, Safari on iPhone.
- Internet Explorer is not supported.
- `inert` (which blocks the page behind the gate) needs Chrome 102, Safari 15.5 or Firefox 112 or newer.
  On older browsers the cover still blocks clicks.

Keyboard: Tab or the arrow keys move between the cards; Enter or Space flips one. While a deck line is showing,
Enter or Space moves it on. Found pairs drop out of the Tab order. Focus stays inside the gate.
During the demo the cards are out of the Tab order, and Enter starts the round as soon as "Your turn" shows.
Keyboard players get the same Back protection as touch players when the game is opened on its own.
Cards are read out by screen readers (face down, or what is on the card), and so are the reactions and lines.
Reduced-motion settings are respected: the cards turn quickly without bounces and nothing shakes.

## Change the texts

Open `sahi-jodi.html` in a text editor.
All the words are in **the CONTENT block at the top of the script**:
the card labels, what a screen reader says for each card, the deck lines shown after a pair or a trap,
which shoppers are in each grid, Archie's lines, the end-card lines, the "To remember" lines and the demo captions.

- Change only the text inside the quotes. Keep the commas and brackets.
- Keep it short: card labels 1 or 2 words, demo captions up to 5 words. A long deck line can be split into two parts.
- No exclamation marks.
- The deck lines are the deck's exact words. Change them only if the deck changes.

How each shopper reacts to a wrong move is in the `REACT` table just under CONTENT. The scoring numbers are in `SCORE`.
How long a line stays up is set in `cardDur`, `nextDur` and `againDur` (just under "SMALL ACTIONS"). Each extra 0.02 seconds
a word adds about 1.5 seconds to a round.

The two lines around the game on the demo page ("Before you start: one quick game" and "About a minute.
Your LMS opens when you finish.") are in `demo.html`, inside the gate.

Save, then open `demo.html` to check.
