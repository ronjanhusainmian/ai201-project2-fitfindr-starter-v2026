# FitFindr

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, every
> command, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> python app.py listings --full -n 6      # read the data (Milestone 1)
> python app.py fields                    # what you can filter on
> python app.py ask 'vintage graphic tee under $30'
> ```
>
> All three tools are stubs, so that last command will do nothing useful yet.
> That's the starting position.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     This is your submission. Fill each section in as you finish the milestone
     it belongs to — don't leave it all to the end.

     Unit 3 asks for the first five sections. Unit 4 adds the five below them.
     Leave the unit 4 sections alone until then; they're here so you know
     what's coming.

     Everything is pasted as TEXT. No screenshots, no images, no video links.
     A typed block of output gets full credit; a picture of the same output
     gets none.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 3 — THE BUILD ═══════════════════════ -->

## What This Does

FitFindr helps users find secondhand clothing based on their preferences, including style, size, and budget. Users can search for clothing items and receive outfit suggestions using pieces they already own. The app also generates a short caption for the selected item. If no matching items are found, it suggests ways to adjust the search.
<!-- Three or four sentences: what a user asks for, and what they get back. -->

---

## Fields in listing:
  id 
  title 
  description
  category 
  style_tags  
  size  
  condition  
  price
  colors     
  brand   
  platform 

## Tool Inventory

<!-- Four lines per tool. This is worth 2 points and it's the single most
     common place students lose them.

     "Returns a list" earns NOTHING. The description has to say what is IN
     the list.

     The empty case isn't optional either — it's the thing your loop branches
     on, and if you don't decide it here you'll discover it as a crash in
     Milestone 5. -->

### `search_listings`

- **What it does:** Searches clothing listings based on a description, with optional size and maximum price filters.
- **Inputs:** `description` (str), `size` (str | None), `max_price` (float | None).
- **Returns:** A list of matching listing dictionaries, ordered by best match first. Each dictionary contains `id`, `title`, `description`, `category`, `style_tags`, `size`, `condition`, `price`, `colors`, `brand`, and `platform`.
- **When it has nothing:** Returns an empty list `[]` if no listings match.

### `suggest_outfit`

- **What it does:** Suggests one or two outfits by combining a thrifted clothing item with items from the user's wardrobe.
- **Inputs:** `new_item` (dict), `wardrobe` (dict containing an `items` list).
- **Returns:** A non-empty string containing outfit suggestions and styling advice.
- **When it has nothing:** If the wardrobe is empty, returns general styling advice for the new item instead of an empty string or an error.

### `create_fit_card`

- **What it does:** Generates a short, social-media-style caption for a suggested outfit and new clothing item.
- **Inputs:** `outfit` (str), `new_item` (dict).
- **Returns:** A string containing a 2–4 sentence caption mentioning the item, its price, platform, and overall style.
- **When it has nothing:** Returns a descriptive message if `outfit` is empty or contains only whitespace.

---

## Planning Loop

<!-- Your branch rule, stated as a rule — the condition AND both paths — plus
     the file and function that holds it.

     Like this:
       "If search_listings returns an empty list, put a message in the session
        and stop. Otherwise take the first result and go to suggest_outfit."
        — agent.py::run_agent

     The grader checks your code against what you claim here, so the file and
     function have to be real. -->

**Branch rule:** 
If `search_listings` returns an empty list, add a message to the session explaining that no matching listings were found and stop. Otherwise, take the first listing and pass it to `suggest_outfit`. If an outfit is generated, pass it along with the selected listing to `create_fit_card`.

**Where it lives:** `agent.py::run_agent`

**How the query is parsed:** <!-- regex, string splitting, or asking the model — say which -->
The agent uses regular expressions to extract the maximum price and size from the user's query. The remaining text is used as the clothing description for search_listings.

**What moves through the session:** <!-- which fields, in what order -->
---
The session stores the user's query, parsed description, size, and maximum price. The results from search_listings are saved in search_results, and the first matching item is stored in selected_item. This item and the user's wardrobe are passed to suggest_outfit, which saves its response in outfit_suggestion. Finally, create_fit_card uses the selected item and outfit suggestion to generate a caption, which is stored in fit_card. If no matching items are found, the session stores an error message and stops before calling the remaining tools.


## Sample Run

<!-- Two things go here.

     1. One FULL query and its output, pasted as text.
     2. Your three per-tool terminal tests — the command and what it printed. -->

**One full query**

```
$ python app.py ask 'vintage graphic tee under $30'

```
  Found:    Y2K Baby Tee — Butterfly Print — $18.0 on depop

  Outfit:   Here are 2 outfit ideas combining the **Y2K Baby Tee with Butterfly Print** with items already in your wardrobe, leaning into its nostalgic early-2000s aesthetic:

### Outfit 1: Classic Y2K Streetwear
Pair the cropped, fitted nature of the baby tee with high-waisted, voluminous bottoms for that signature Y2K silhouette.
* **Top:** Y2K Baby Tee — Butterfly Print (New item)
* **Bottoms:** Baggy straight-leg jeans, dark wash (`w_001`)
* **Shoes:** Chunky white sneakers (`w_007`)
* **Accessories:** Black crossbody bag (`w_010`)
* *Why it works:* The fitted crop of the baby tee balances out the baggy, high-waisted fit of the dark wash jeans, while the chunky sneakers and crossbody bag tie the streetwear look together.

### Outfit 2: Edgy Contrast
Layer the playful, girly butterfly graphic with darker, tougher pieces for a balanced grunge-meets-sweet look.
* **Top:** Y2K Baby Tee — Butterfly Print (New item)
* **Outerwear:** Vintage black denim jacket (`w_006`)
* **Bottoms:** Wide-leg khaki trousers (`w_002`)
* **Shoes:** Black combat boots (`w_008`)
* **Accessories:** Black crossbody bag (`w_010`)
* *Why it works:* The khaki trousers and combat boots ground the vibrant, feminine tee, and throwing the slightly cropped black denim jacket on top adds an effortlessly cool layer for cooler days.

  Fit card: Channeling all the peak 2000s pop star energy with this Y2K Baby Tee — Butterfly Print! 🦋✨ Style it with baggy jeans and chunky sneakers for an effortless streetwear vibe, or toughen it up with combat boots. Grab this throwback gem on Depop right now for just $18.00 before it's gone!

0 model calls this session, 2 served from cache


**The three tools, tested one at a time**

```
$ python -c "from tools import search_listings; print(search_listings('graphic tee', max_price=30))"

```
[{'id': 'lst_002', 'title': 'Y2K Baby Tee — Butterfly Print', 'description': 'Super cute early 2000s baby tee with butterfly graphic. Fitted crop length. Tag says medium but fits like a small.', 'category': 'tops', 'style_tags': ['y2k', 'vintage', 'graphic tee', 'cottagecore'], 'size': 'S/M', 'condition': 'excellent', 'price': 18.0, 'colors': ['white', 'pink', 'purple'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_006', 'title': 'Graphic Tee — 2003 Tour Bootleg Style', 'description': 'Vintage-style bootleg tee with faded graphic. Slightly boxy fit. 100% cotton, soft and worn-in.', 'category': 'tops', 'style_tags': ['graphic tee', 'vintage', 'grunge', 'streetwear', 'band tee'], 'size': 'L', 'condition': 'good', 'price': 24.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_017', 'title': 'Mesh Long-Sleeve Top — Black', 'description': 'Sheer black mesh long-sleeve. Great for layering under a graphic tee or over a bralette. Stretchy material, fits true to size.', 'category': 'tops', 'style_tags': ['y2k', 'grunge', 'goth', 'layering'], 'size': 'S/M', 'condition': 'excellent', 'price': 15.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_033', 'title': 'Vintage Band Tee — Faded Grey', 'description': 'Faded grey band-style tee with distressed graphic. Crew neck. Fits boxy. Well-loved but no holes or major damage.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'band tee', 'graphic tee', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 19.0, 'colors': ['grey', 'charcoal'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_015', 'title': 'Vintage Graphic Hoodie — Faded Black', 'description': 'Faded black pullover hoodie with barely-visible vintage graphic on the chest. Cozy interior. Some pilling but adds to the worn-in look.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'graphic', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 26.0, 'colors': ['black', 'charcoal'], 'brand': None, 'platform': 'depop'}]

```
$ python -c "from tools import suggest_outfit; from utils.data_loader import get_example_wardrobe, load_listings; print(suggest_outfit(load_listings()[0], get_example_wardrobe()))"
```

Here are two outfit ideas combining the **Vintage Levi's 501 Jeans (Medium Wash)** with pieces already in your wardrobe:

### Outfit 1: Casual & Classic Streetwear
*This look leans into effortless, everyday style by pairing the straight-leg vintage denim with clean basics and chunky footwear.*
* **Tops:** **White ribbed tank top** (w_003) as the base.
* **Outerwear:** Layered with the **Oversized grey crewneck sweatshirt** (w_004) for an easy, relaxed silhouette.
* **Accessories:** Capped off with the **Brown leather belt** (w_009) to accent the waist and the **Black crossbody bag** (w_010).
* **Shoes:** **Chunky white sneakers** (w_007) to tie in the bright white of the tank.

### Outfit 2: Edgy & Vintage-Inspired
*A slightly tougher combination that plays on textures by mixing vintage medium-wash denim with black layers and combat boots.*
* **Tops:** **Black cropped zip hoodie** (w_005) to balance out the straight-leg fit of the 501s with a cropped silhouette.
* **Outerwear:** The **Vintage black denim jacket** (w_006) thrown over top for a cool, double-denim contrast against the medium wash.
* **Accessories:** Secured with the **Brown leather belt** (w_009) and the **Black crossbody bag** (w_010).
* **Shoes:** **Black combat boots** (w_008) to anchor the outfit with a grunge-inspired finish.
```


$ python -c "from tools import create_fit_card; from utils.data_loader import load_listings; print(create_fit_card('jeans and white sneakers', load_listings()[0]))"

```

Nothing beats the holy grail of denim, and these vintage Levi's 501 jeans are giving major effortless cool-girl energy. They have the most perfect medium wash and just the right amount of lived-in knee fading. Grab them on depop for just $38.00 and throw them on with fresh white sneakers for the ultimate off-duty uniform!


---

## How I Used AI

<!-- Two specific moments. What you asked, what came back, what you changed.

     "I used Claude to help me code" is not enough.

     "I gave Claude my search_listings spec. It returned None on no match
     instead of an empty list, so I changed it" is the level we want. -->

**Moment 1**

- *What I asked for:*
I asked AI to explain the existing starter code in tools.py and data_loader.py so I could understand how the functions work and what I needed to implement.
- *What came back:*
AI explained the purpose of each function, its inputs and outputs, and how the tools access the listings and wardrobe data.
- *What I changed:*
I used the explanations to understand the code structure and fill in the missing implementations.

**Moment 2**

- *What I asked for:* I asked AI to help me understand how to test each of the three tools individually using terminal commands.
- *What came back:* AI explained the test commands, what outputs to expect, and how to check whether each tool was working correctly.
- *What I changed:* I ran the tests myself, reviewed the outputs, and checked that the tools handled different inputs correctly, including empty results.

<!-- ═══════════════════════ UNIT 4 — THE TEST ═══════════════════════

     Don't fill these in during unit 3.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Run Log — Before

<!-- Five criteria, five tries each, in this exact format.

     Five, because your criteria are written out of five. Mark each try PASS
     or FAIL, count the passes, and read that count against your target — a
     row targeting 4 of 5 with three PASS cells is MISSED (3/5).

     `python run_eval.py --label before` runs everything and writes the table
     into results/. Paste it here and fill in the verdicts. -->

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |  |  |
| 2.  |  |  |  |  |  |  |  |
| 3.  |  |  |  |  |  |  |  |
| 4.  |  |  |  |  |  |  |  |
| 5.  |  |  |  |  |  |  |  |

**Real output from one try**, pasted as text, naming the file and function
that produced it:

```

```

---

## Verdicts and Diagnoses

<!-- MET or MISSED per criterion against LAST UNIT's target, plus a sentence on
     how you decided.

     Then, for every miss: which of the four places it happened — a tool, the
     loop's branch, the session, or the model's output — AND the mechanism.

     Not a diagnosis:  "The fit card was bad."
     A diagnosis:      "The fit card criterion missed on 2 of 5 items. Both had
                        an empty brand field. My prompt puts the brand in the
                        first sentence, so the card opened with a blank and read
                        like a fragment. The tool worked; the prompt assumed a
                        field that isn't always there."

     Look for a pattern. Three misses on the same tool is one problem, not
     three. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**



---

## Loop Trace

<!-- One full run, printed step by step, with the MCP call visible in it.

     `python app.py ask '...' --trace` once you've added the trace.step()
     calls in Milestone 2.

     Worth pasting BOTH the happy path and the empty-search path. The empty
     one should be visibly shorter, because it stops. If your two traces are
     the same length, your branch isn't working — and this is the fastest way
     anyone will ever find that out. -->

**Happy path**

```

```

**Empty search**

```

```

**On the MCP move:** <!-- what changed in your code, and whether anything
behaved differently afterwards. If the rewire didn't work, say exactly where it
broke — the error text and the last thing that worked. That earns the point in
full. -->



---

## The Improvement

<!-- What you changed, why your diagnosis pointed at it, and the after-run in
     the same table format. One change, measured properly.

     `python run_eval.py --label after` -->

**What I changed:**

**Which failure it was meant to fix:**

### Run Log — After

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |  |  |
| 2.  |  |  |  |  |  |  |  |
| 3.  |  |  |  |  |  |  |  |
| 4.  |  |  |  |  |  |  |  |
| 5.  |  |  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it made things worse, say that. Honestly reported, that earns full
     credit and is more interesting than one that worked. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped where
     you did. "I ran out of time" is fine if it's true. Pretending nothing is
     left is not. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 3

       [ ] criteria.md has five numbered criteria, each with a target
       [ ] Each criterion has a reason underneath it
       [ ] All five unit 3 sections above have real content
       [ ] Tool Inventory: all three tools, inputs WITH TYPES, a specific
           return value, and the empty case
       [ ] Planning Loop names the branch rule and agent.py::run_agent
       [ ] Sample Run: one full query plus the three per-tool tests, as text
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN, you submit the same one
           next unit

     SUBMISSION CHECKLIST — unit 4

       [ ] mcp_server.py exists with one tool registered
           (or a written record of exactly where the rewire broke)
       [ ] Run Log — Before, five criteria, five tries each
       [ ] Real output pasted underneath, naming file and function
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, naming a place AND a mechanism
       [ ] Loop Trace, with the MCP call visible in it
       [ ] All three failure modes triggered and handled
       [ ] One improvement, with Run Log — After in the same format
       [ ] What's Still Broken
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository. Your commit history is what
     shows your criteria existed before your results did.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
