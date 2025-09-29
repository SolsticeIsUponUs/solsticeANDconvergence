<html lang="en">
<head>
	<meta charset="utf-8" />
	<title>Puzzle Thing</title>
	<style>
		body { font-family: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif; max-width: 900px; margin: 32px auto; padding: 0 20px; line-height: 1.5; color:#111; }
		h1,h2 { margin-bottom:8px }
		.puzzle { padding:16px; border:1px solid #ddd; border-radius:8px; margin-bottom:20px; background:#fafafa; }
		.resources { margin-top:18px; padding:14px; border-radius:8px; background:#fff; border:1px solid #eee; }
		pre { white-space:pre-wrap; font-family:inherit; margin:8px 0; }
		.result { margin-top:10px; font-weight:600; }
		button { padding:8px 12px; }
	</style>
</head>
<body>
	<h1>Puzzle Thing</h1>
	<div class="puzzle" id="puzzle">
		<p>Solve the riddle and enter the answer (case-sensitive):</p>
		<p><input id="answer" placeholder="type answer..." /> <button onclick="check()">Check</button></p>
		<p class="result" id="result" aria-live="polite"></p>
		<p style="font-size:.9em;color:#555">Hint: the resources below contain the clues. Decode, shift and convert as the verse suggests.</p>
	</div>
	<section class="resources" id="resources">
		<h2>Resources</h2>
		<h3>The Ciphered Waltz of Sixteen Suns</h3>
		<pre id="poem1">
In gardens where glyphs on parchment lie,  
A priest of code with an inkless eye  
Sings verses shaped in numbered tone,  
Where letters sleep in skin and bone.

From silent scrolls, their forms unmasked—  
The flesh of bytes, once tightly tasked—  
Now whisper truths beneath their shroud,  
As ancient tongues speak clear and loud.

To **hearts aligned in decimal grace**,  
One must **extract each hidden face**,  
But **shift the stage** where they perform—  
Let **sixteen suns** make shadows warm.

The dancers turn with silent mirth,  
Their steps askew, reborn from birth.  
Each twirl, a loop, each leap, a code,  
A spiral road they twice reload.

For once the stars have made their spin,  
The masks removed reveal within  
The final truth—not once, but **twain**,  
Revealed in fire, then sealed again.

So if you seek what’s tucked in thrice,  
**Decode**, **displace**, **rotate**, **convert**—then **splice**.
		</pre>
		<h3>The Oracle's Arithmetic</h3>
		<pre id="poem2">
In the House of Half and One,  
Where mirrors count the moon and sun,  
The oracles chant in pairs of four,  
Each tone a glyph, each breath a score.

**Begin where six and ten are twins,**  
Then **subtract the weight of sin**—  
A **score from which two apples fell**,  
**Leaves five and two**, the seer’s spell.

Then drift to lands of even tide,  
Where **dozened knights in chrome abide**,  
Take **half of eight**, then drop the crown——  
**Six remains** to jot things down.

In **trios bound by mirrored fate**,  
Where **nines wear masks as seven and eight**,  
You’ll find that **five is not alone**—  
It **dances with one**, in dulcet tone.

A **pair of twins**, too round to climb,  
Make **nothing thrice** in echoed time.  
But **cast your gaze where silence walks**,  
And **space** divides the ticking clocks.

Return, then, to the mirrored place,  
Where **five and two** again embrace—  
But now they whisper something more:  
A truth you’ve **seen**, yet **not before**.

Where **half of ten and triple naught**,  
Sit quietly as time forgot.  
The **sum of six and double naught**  
Completes the string the stars once brought.
		</pre>
		<figure>
			<img id="puzzleImage" src="https://files.catbox.moe/597vos.png" alt="Puzzle image" style="max-width:100%;border-radius:6px;border:1px solid #eee;margin-top:10px" />
			<figcaption style="font-size:.9em;color:#555;margin-top:6px">Image resource</figcaption>
		</figure>
	</section>
	<script>
		// Encrypted answer stored as base64. It was produced by XORing each byte of "b2432" with
		// a key derived at runtime from the image URL char codes sum mod 256.
		// This keeps the plaintext out of the static source.
		(function () {
			window._enc = "+amvqKk="; // base64 of the XORed bytes
			// helper: base64 -> Uint8Array
			function base64ToBytes(b64) {
				var bin = atob(b64);
				var len = bin.length, bytes = new Uint8Array(len);
				for (var i = 0; i < len; i++) bytes[i] = bin.charCodeAt(i);
				return bytes;
			}
			// compute key: sum char codes of image src mod 256
			function computeKeyFromImage() {
				var img = document.getElementById('puzzleImage');
				var s = img && img.src ? img.src : "";
				var sum = 0;
				for (var i = 0; i < s.length; i++) sum = (sum + s.charCodeAt(i)) & 0xFFFFFFFF;
				return sum % 256;
			}
			// decrypt to a string
			window._decryptAnswer = function () {
				var key = computeKeyFromImage();
				var bytes = base64ToBytes(window._enc);
				var chars = [];
				for (var i = 0; i < bytes.length; i++) {
					chars.push(String.fromCharCode(bytes[i] ^ key));
				}
				return chars.join('');
			};
		})();
		function check() {
			var input = document.getElementById('answer').value || "";
			var resultEl = document.getElementById('result');
			// decrypt only in memory — do not leak the plaintext to console by default
			var secret = window._decryptAnswer();
			if (input === secret) {
				resultEl.style.color = "green";
				resultEl.textContent = "Correct!";
			} else {
				resultEl.style.color = "crimson";
				resultEl.textContent = "Incorrect — try again.";
			}
			// small security note: secret exists in memory for comparison only.
		}
	</script>
</body>
</html>
