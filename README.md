# 404 // SECTOR NOT FOUND

A Tron-themed 404 page with a playable light-cycle mini-game and a pile of hidden Tron and Star Wars easter eggs. It's one self-contained HTML file with no external dependencies: no frameworks, no CDN scripts, no web fonts and no images. It works offline and on air-gapped networks.

## Features

- **Animated background.** A perspective grid scrolls toward a horizon while light-cycle trails race across it, all drawn on a `<canvas>`.
- **CRT look.** Scanlines and a pure-CSS glitch effect on the 404.
- **Grid Dodge.** A light-cycle mini-game. Collect data discs, avoid red corruption blocks and don't hit your own trail. Your high score is saved in the browser.
- **Procedural sound.** Every sound effect is synthesized with the Web Audio API, so there are no audio files. A toggle in the header turns sound on and off.
- **Diagnostics terminal.** A small command line built into the page.
- **Works on mobile.** The game shows an on-screen D-pad on small screens.
- **Easter eggs.** See the spoiler section below.

## Quick start

Rename the file to `404.html` and open it in a browser to preview it. To use it as your site's real 404 page, point your web server's 404 handling at it, using one of the setups below.

The page has no assets to load, so it renders correctly at any URL depth (`/a/b/c/missing`), which is the usual problem with custom 404 pages.

### Caddy

```caddyfile
example.com {
    root * /srv/www
    file_server

    handle_errors {
        @notfound expression {err.status_code} == 404
        handle @notfound {
            rewrite * /404.html
            file_server
        }
    }
}
```

If Caddy runs in Docker, `root` is the path **inside the container** where your site is mounted, not the path on the host.

### Nginx

```nginx
server {
    root /var/www/html;
    error_page 404 /404.html;

    location = /404.html {
        internal;
    }
}
```

### Apache

In your vhost or `.htaccess`:

```apache
ErrorDocument 404 /404.html
```

### Static hosts

GitHub Pages, Netlify, Cloudflare Pages and Vercel (static) all pick up a `404.html` in the site root automatically.

## Controls

| Action | Desktop | Mobile |
| --- | --- | --- |
| Steer the light cycle | WASD or arrow keys | On-screen D-pad |
| Start or restart | **Initialize Cycle** button | Same |
| Toggle sound | **SFX** button in the header | Same |
| Open the terminal | **Diagnostics** button | Same |

Each data disc is worth 10 points, and the cycle speeds up a little with every pickup. The high score is stored in `localStorage` under the key `tron_404_highscore`.

## Customizing

Everything lives in the one HTML file.

### Colors

The palette is set as CSS variables in `:root`:

```css
--neon-cyan: #00fff2;
--neon-rgb: 0, 255, 242;   /* must match --neon-cyan; used for glows and tints */
--neon-blue: #0088ff;
--neon-pink: #ff0055;
--bg-dark: #030712;
```

If you change `--neon-cyan`, change `--neon-rgb` to the same color. The canvases read the theme color from CSS, so the grid and game follow along automatically.

### Text and links

- The **Main Grid** button links to `/`. Change its `href` if your home page lives elsewhere.
- The heading, description and badge are plain HTML near the top of `<main>`.

### Game tuning

In the script:

- `GRID_SIZE` sets the board size in cells (default `15`).
- `speed` in `initGame()` is the starting step delay in milliseconds (default `130`). Lower is faster.
- The `if (speed > 60) speed -= 2;` line sets the speed cap and how fast the game ramps up.

### Adding terminal commands

Commands live in the `cmd` object. Add an entry whose key is the lowercase command and whose value is a function:

```js
'greetings program': () => print('Greetings.'),
```

`print()` accepts a string or an array of lines and an optional class: `'warn'` (pink), `'holo'` (flickering blue hologram) or `'echo'` (grey). Input is lowercased, extra spaces are collapsed and trailing `!?.` is stripped before matching, so you only need one spelling. Aliases go in the `Object.assign(cmd, { ... })` block below it.

## Browser support

The page works in any current browser: Chrome, Edge, Firefox and Safari, on desktop and mobile. It uses `<canvas>`, the Web Audio API and `localStorage`, and degrades gracefully if audio or storage is unavailable. Browsers don't allow sound until the visitor interacts with the page, so the first sound plays after the first click or keypress.

Spinning, flickering and hyperspace effects are disabled when the visitor's system has **reduced motion** turned on.

## Easter eggs

The whole point is that people find these on their own. Spoilers are below.

<details>
<summary><strong>Show all easter eggs (spoilers)</strong></summary>

### Hidden terminal commands

Open **Diagnostics** and type. Running `help` only lists `help`, `scan`, `whoami`, `ls`, `cat <file>`, `clear` and `exit`. Everything else is hidden.

**Files** (`ls`, then `cat <file>`):

| File | What it shows |
| --- | --- |
| `flynn_arcade.log` | "FLYNN LIVES." graffiti |
| `space_paranoids.bin` | Flynn's game, with signs that Dillinger tampered with the authorship records |
| `death_star_plans.dat` | Decrypts the thermal exhaust port details as a hologram |
| `bit.exe` | Tells you to run it. Run it with `bit.exe`, `./bit.exe` or `bit` |

**Tron:**

| Command | Effect |
| --- | --- |
| `flynn` | FLYNN LIVES. |
| `tron` | Restores the default cyan theme. "I fight for the Users." |
| `clu` | Recolors the whole grid and game Clu orange |
| `mcp` | Master Control takes over and turns everything red |
| `end of line` | Prints END OF LINE. and closes the terminal |
| `castor` or `zuse` | "Biodigital jazz, man." |
| `sudo <anything>` | The MCP refuses |
| `rm -rf /` | Pretends to derez the Grid |
| `greetings` or `hello` | "Greetings, program." |

**Star Wars:**

| Command | Effect |
| --- | --- |
| `help me`, `obiwan` or `obi-wan` | Leia's message as a flickering blue hologram |
| `hello there` | "General Kenobi." |
| `use the force` or `force` | Jedi mind trick: the page description changes for a few seconds |
| `execute order 66` or `order 66` | Red theme, and the badge reads ORDER 66 EXECUTED |
| `punch it`, `hyperspace` or `jump` | Jump to hyperspace |
| `i am your father` | The famous reaction |
| `vader` | Mechanical breathing |
| `thx 1138`, `thx` or `1138` | Prisoner transfer from cell block 1138 |
| `scan` | The trace points to detention level AA-23, cell 1138 |

### On the page

- **Konami code** (↑ ↑ ↓ ↓ ← → ← → B A): the background jumps to hyperspace with a rising synth whoosh, and the terminal opens with "Punch it, Chewie."
- **Bit:** click the pulsing dot in the header and Bit from the original Tron appears, answering YES (yellow diamond) or NO (red spiky star) with a beep.
- **Jedi mind trick:** click the big 404 five times quickly.
- **Tab title:** switch to another tab and the title changes to "END OF LINE."
- **Game-over messages:** "DEREZZED" at 0 points, "DEREGISTERED" normally, "END OF LINE" at 150 points or more.
- **Hover tooltips:** "ENCOM 511" on the GRID OS label and a cell block 1138 reference on the footer.

### Dates

The badge at the top changes on these days:

| Date | Badge |
| --- | --- |
| May 4 | MAY THE 4TH BE WITH YOU |
| May 25 | A NEW HOPE // 05.25.1977 |
| July 9 | TRON DAY // 07.09.1982 |

### For people who dig

- **DevTools console:** a "GREETINGS, PROGRAM." banner with hints.
- **View Source:** a comment at the top of the file with hints, including the Konami code.

</details>

## License

Add your license here.

---

END OF LINE.
