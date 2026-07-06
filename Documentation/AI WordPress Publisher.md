# AI WordPress Publisher - Instruction Manual

Last updated: July 6, 2026  
Google Sheet: https://docs.google.com/spreadsheets/d/1K8zXlbjJuL0ejB4AQXeuRl--37drlhP3HvyDN1D4MBE/edit  
Apps Script project: `AI WordPress Publisher`  
Current verified site: `Hyperion Times`  
Current verified WordPress draft test: post ID `621`

## What This Sheet Does

This Google Sheet is an AI-assisted WordPress publisher.

The simple version:

1. You paste a source article, notes, report excerpt, transcript, or URL into the `Publish` tab.
2. You run the `AI Publisher` menu.
3. The Apps Script sends the source material through the prompt stack in `Prompts`.
4. The AI fills in WordPress-ready draft fields.
5. You review those fields.
6. The Sheet sends the post to WordPress as a draft, update, or published post.

The Sheet is designed to be copied and reused for different WordPress sites. It can handle normal WordPress posts and custom post types if the WordPress REST API is set up correctly.

## Current Status

This workbook has been wired and tested.

Confirmed working:

- Bound Apps Script is installed.
- `AI Publisher` menu appears in the Google Sheet.
- WordPress REST authentication works.
- WordPress draft creation works.
- OpenAI API call path works.
- Tiered prompt workflow runs.
- Draft/update flow writes back to WordPress.
- The current test draft is post ID `621`.

Important: The current build intentionally reads credentials from the `Sites` tab because this is a fast single-user prototype. Do not share this Sheet with anyone who should not see your WordPress and OpenAI credentials.

## The Four Tabs

### 1. `Publish`

This is the daily working tab.

Use this tab when you want to create or update a WordPress draft.

Main sections:

- `Site`: shows the active site from the `Sites` tab.
- `Action`: choose what the script should do.
- `WP Post ID`: controls whether the script creates a new post or updates an existing post.
- `WP Status`: shows the current workflow state.
- `Last Run`: shows the last action time and result.
- `Error`: shows the failure message if something breaks.
- `Source URL`: optional URL to fetch.
- `Source Text / Notes`: the main input area.
- `Review WordPress Draft Fields`: AI-generated title, slug, excerpt, terms, tags, media, author.
- `Article Body`: the AI-generated post body.
- `Custom Fields / SCF Output`: custom field values that will be sent to WordPress.

This is the tab you should live in day to day.

### 2. `Prompts`

This is the visible prompt stack.

The prompt stack has five AI passes:

- `System`: the site-wide editorial role and behavior.
- `1 Intake`: turns your raw source material into a structured source packet.
- `2 Metadata`: creates WordPress title, slug, excerpt, tags, terms, confidence, and media idea.
- `3 Draft`: writes the WordPress-ready article body.
- `4 Custom Fields`: fills enabled custom fields from the field map.
- `5 QA`: reviews the draft and flags weak sourcing, uncertainty, or missing items.

Each prompt row has an `On?` column. Set it to `TRUE` to run that pass or `FALSE` to skip it.

For normal use, leave all prompt passes on.

### 3. `Setup`

This is the site configuration and custom field map.

Use it when setting up a new site or changing site behavior.

Important sections:

- Site name
- WordPress base URL
- Post type slug
- REST base
- Default publish status
- Category taxonomy REST base
- Tag taxonomy REST base
- Custom field map
- Editorial voice
- Copy-to-new-site checklist

You usually do not touch this tab during daily publishing.

### 4. `Sites`

This is the active site and credential table for the current prototype.

Columns:

- `URL`
- `WP Admin User`
- `WP Admin Pass`
- `WP Admin App Pass`
- `Open AI Key`
- `Site Name`
- `Post Type`
- `REST Base`
- `Default Status`
- `Active`
- `Test Post ID`
- `Last Test Result`

Only one site row should have `Active` set to `TRUE`.

Do not publish, screenshot, export, or share this tab unless you are comfortable exposing the credentials in it.

## Daily Workflow

### Safest Daily Workflow

Use this when you are writing a real post.

1. Open the `Publish` tab.
2. Clear old source material from `Source URL` and `Source Text / Notes`.
3. If you want a brand-new WordPress post, clear `WP Post ID`.
4. If you want to update an existing WordPress post, enter the existing WordPress post ID in `WP Post ID`.
5. Paste your source material into `Source Text / Notes`.
6. Optional: paste one URL into `Source URL`.
7. Set `Action` to `Draft from Input`.
8. Click `AI Publisher > Run Selected Action`.
9. Wait for the Sheet to finish.
10. Review the generated title, slug, excerpt, terms, tags, article body, and custom fields.
11. Fix anything you do not like directly in the cells.
12. Set `Action` to `Stage Draft`.
13. Click `AI Publisher > Run Selected Action`.
14. Open the WordPress draft and review it in WordPress.
15. Publish from WordPress when you are happy.

This is the recommended everyday workflow because it gives you a review step before anything is sent live.

### Faster Workflow

Use this only when you trust the output.

1. Paste the source material.
2. Run `Draft from Input`.
3. Review quickly.
4. Run `Stage Draft`.

This creates or updates a WordPress draft.

### Live Publish Workflow

Use this carefully.

1. Make sure the draft fields are correct.
2. Make sure `WP Post ID` is blank for a new post or filled for an update.
3. Set `Action` to `Publish / Update`.
4. Click `AI Publisher > Run Selected Action`.

This sends the post to WordPress with publish status.

Do not use `Publish / Update` as your default daily action unless you intentionally want the Sheet to publish live.

## What Each Action Does

### `Draft from Input`

Runs the AI prompt stack.

It does not publish to WordPress by itself.

It reads:

- `Source URL`
- `Source Text / Notes`
- `Setup`
- `Prompts`
- `Custom Field Map`

It writes:

- title
- slug
- excerpt
- categories/terms
- tags
- featured media idea or ID
- author ID
- article body
- custom field values
- editor notes/QA

Use this first.

### `Stage Draft`

Sends the current draft fields to WordPress as a draft.

If `WP Post ID` is blank, it creates a new WordPress draft.

If `WP Post ID` has a number, it updates that existing WordPress post.

Use this after reviewing the AI output.

### `Publish / Update`

Sends the current draft fields to WordPress as a published post.

If `WP Post ID` is blank, it creates a new published WordPress post.

If `WP Post ID` has a number, it updates and publishes that existing WordPress post.

Use this carefully.

### `Pull Existing Post`

Pulls an existing WordPress post into the Sheet.

Use this when you want to edit an already-existing post from the Sheet.

Required first:

1. Enter the WordPress post ID in `WP Post ID`.
2. Set `Action` to `Pull Existing Post`.
3. Click `AI Publisher > Run Selected Action`.

This is a utility action, not the normal daily workflow.

### `Clear Intake`

Clears the source URL, source text, and error message.

It does not clear the generated draft fields.

Use this when you are starting a new article and want to remove old input.

### `Test: WordPress Draft Only`

Runs a test that does not call OpenAI.

It creates or updates a WordPress draft with simple test content.

Use this to confirm WordPress credentials and REST publishing work.

### `Test: OpenAI + WordPress Draft`

Runs a full test:

1. Adds test source material.
2. Runs the AI prompt stack.
3. Sends the result to WordPress as a draft.

Use this to confirm both OpenAI and WordPress work.

## The Most Important Rule: `WP Post ID`

`WP Post ID` decides whether the Sheet creates a new post or updates an existing post.

If `WP Post ID` is blank:

- `Stage Draft` creates a new draft.
- `Publish / Update` creates a new published post.

If `WP Post ID` has a number:

- `Stage Draft` updates that post as a draft.
- `Publish / Update` updates and publishes that post.

Before starting a new article, always check `WP Post ID`.

If you accidentally leave an old ID in place, the Sheet will update that old WordPress post instead of creating a new one.

## Best Input Practices

The best input is pasted text.

Good input examples:

- full article text
- transcript
- report excerpt
- press release
- notes from multiple articles
- pasted source bundle
- bullet points with links
- your own editorial notes

Pasted text is usually better than only a URL because:

- it saves web-fetch calls
- it gives the AI more context
- it avoids paywall or bot-block issues
- it lets you control exactly what the AI sees
- it reduces hallucination risk

The `Source URL` field is optional.

If you provide a URL, the script tries to fetch it and strip the page text. If fetching fails, paste the article text manually.

## Good Source Input Template

When in doubt, paste something like this:

```text
SOURCE 1:
Title:
URL:
Date:
Publisher:
Text / notes:

SOURCE 2:
Title:
URL:
Date:
Publisher:
Text / notes:

MY ANGLE:
What this should focus on:

DO NOT OVERSTATE:
Anything uncertain:

TARGET POST TYPE:
News brief / explainer / research note / analysis / profile / report:
```

You do not have to use this every time, but it helps.

## Prompt Stack: What The AI Is Doing

The AI does not write the final post in one blind step.

It runs in layers.

### Pass 1: Intake

The AI reads the source material and extracts:

- main facts
- entities
- dates
- source URLs
- known unknowns
- claims that should not be overstated

This pass is there to keep the model grounded.

### Pass 2: Metadata

The AI proposes WordPress metadata:

- title
- slug
- excerpt
- category terms
- tags
- featured media idea
- author ID if relevant
- confidence level

This keeps SEO and taxonomy separate from body writing.

### Pass 3: Draft

The AI writes the article body.

It should produce WordPress-friendly HTML.

It should include:

- strong lead
- clear headings
- source-aware language
- no invented facts
- bottom-line section

### Pass 4: Custom Fields

The AI fills enabled fields from the `Setup` tab field map.

If the source does not support a custom field, the AI should leave the value blank and explain what is missing in notes.

### Pass 5: QA

The AI reviews its own output.

It should flag:

- unsupported claims
- weak sourcing
- missing dates
- questionable tags or terms
- overconfident language
- missing custom fields
- anything that should block publication

## How To Edit Prompts

Open the `Prompts` tab.

Each prompt row has:

- `Pass`
- `On?`
- `Purpose`
- `Prompt Text`
- `Writes To`

Edit only the `Prompt Text` column unless you know what you are doing.

Safe prompt edits:

- change tone
- change article structure
- tell it to use shorter posts
- tell it to write for a specific audience
- add site-specific editorial rules
- add banned phrases
- add preferred formatting

Risky prompt edits:

- removing the JSON shape instructions
- telling the model to ignore uncertainty
- telling the model to invent missing details
- removing source grounding
- changing placeholder names like `{SOURCE_TEXT}` or `{FIELD_MAP}`

If you break a prompt, restore the JSON contract shape in that row.

## Placeholders Used In Prompts

These tokens are replaced by the script before the prompt is sent to OpenAI:

- `{SITE_NAME}`
- `{EDITORIAL_VOICE}`
- `{POST_TYPE}`
- `{REST_BASE}`
- `{SOURCE_URL}`
- `{SOURCE_TEXT}`
- `{FIELD_MAP}`
- `{DRAFT_FIELDS}`
- `{ACTION}`

Do not rename these placeholders unless you also update the Apps Script.

## Setting Up A New WordPress Site

Use this when copying the Sheet for another WordPress site.

### Step 1: Make A Copy

In Google Drive:

1. Right-click the Sheet.
2. Click `Make a copy`.
3. Rename it for the site.
4. Open the copied Sheet.
5. Confirm `AI Publisher` appears in the top menu.

If the menu does not appear:

1. Refresh the Sheet.
2. Wait a few seconds.
3. Open `Extensions > Apps Script`.
4. Confirm the Apps Script project exists.

### Step 2: Fill `Sites`

In the `Sites` tab, add or edit one active site row.

Fill:

- `URL`: site homepage, like `https://example.com`
- `WP Admin User`: WordPress username
- `WP Admin App Pass`: WordPress application password
- `Open AI Key`: OpenAI API key
- `Site Name`: public name of the site
- `Post Type`: WordPress post type slug, usually `post`
- `REST Base`: REST route base, usually `posts`
- `Default Status`: usually `draft`
- `Active`: `TRUE`

Only one row should be active.

For security, do not share the Sheet after adding credentials.

### Step 3: Fill `Setup`

In the `Setup` tab, confirm:

- site name
- WordPress base URL
- default post type slug
- REST base
- default publish status
- category taxonomy REST base
- tag taxonomy REST base
- editorial voice

For normal WordPress posts:

- Post type slug: `post`
- REST base: `posts`
- Category taxonomy REST base: `categories`
- Tag taxonomy REST base: `tags`

### Step 4: Configure Custom Fields

In `Setup`, use the `SCF / Custom Field Map`.

Columns:

- `Enabled`: `TRUE` or `FALSE`
- `Field Key`: field name
- `Label`: human-readable label
- `Type`: text, textarea, number, select, true_false, json
- `AI Instruction`: what the AI should put in the field
- `WP Payload Path`: where the field goes in the WordPress REST payload
- `Required`: whether the field is required

Common payload paths:

- `meta.field_key`
- `acf.field_key`

Use `meta.field_key` when the field is registered as WordPress post meta and exposed to REST.

Use `acf.field_key` only if your site exposes ACF/SCF fields through the REST API that way.

If custom fields do not appear in WordPress, the problem is usually WordPress REST exposure, not the Sheet.

### Step 5: Check WordPress REST Requirements

Your WordPress site must support:

- application passwords
- authenticated REST API requests
- REST access to the post type
- REST access to taxonomies
- REST access to custom fields if you use them

For custom post types:

- the CPT must have REST enabled
- the CPT must have a REST base
- the Sheet `Post Type` and `REST Base` must match WordPress

Example:

If your CPT is `brief` and its REST base is `briefs`:

- Post Type: `brief`
- REST Base: `briefs`
- Endpoint used by script: `/wp-json/wp/v2/briefs`

### Step 6: Authorize Apps Script

The first time you run the script on a copied Sheet, Google will ask for authorization.

Expected flow:

1. Click `AI Publisher > Run Selected Action`.
2. Google asks for permissions.
3. Click `Review permissions`.
4. Choose your Google account.
5. If Google says the app is not verified, click `Advanced`.
6. Continue to the Apps Script project.
7. Click `Allow`.

This is normal for a private bound Apps Script.

### Step 7: Run Tests

Run these before using a new site.

Test 1:

1. Open `AI Publisher`.
2. Click `Test: WordPress Draft Only`.
3. Confirm the script completes.
4. Confirm `Sites > Last Test Result` says `OK draft`.
5. Confirm a draft exists in WordPress.

Test 2:

1. Open `AI Publisher`.
2. Click `Test: OpenAI + WordPress Draft`.
3. Confirm the script completes.
4. Confirm draft fields update.
5. Confirm the WordPress draft updates.

Do not use `Publish / Update` until both tests pass.

## Custom Post Types

This Sheet can publish to custom post types if WordPress exposes them through REST.

You need:

- CPT slug
- REST base
- application password user with permission to create/edit that CPT
- taxonomies exposed to REST if you want terms
- custom fields exposed to REST if you want SCF/meta fields

If a custom post type fails:

1. Confirm the REST endpoint exists.
2. Visit `/wp-json/wp/v2/{REST_BASE}` in a browser while logged in.
3. Confirm the WordPress user can create that post type.
4. Confirm the `REST Base` in `Sites` and `Setup` matches WordPress.

## Categories And Tags

The script can send category and tag information to WordPress.

Best practice:

- Use reusable terms.
- Avoid one-off tags.
- Keep categories broad.
- Keep tags specific but not silly.

If terms fail:

- check taxonomy REST base
- check user permissions
- check whether terms already exist
- check whether the site allows term creation through REST

## Featured Media

The `Featured Media` field can be:

- a WordPress media ID
- a supported image URL, if the script/site can upload it
- blank

For normal use, the safest path is to leave this blank or use an existing WordPress media ID.

If the AI produces a featured image idea, treat it as an editorial suggestion, not an automatic image.

## What To Review Before Staging

Before running `Stage Draft`, check:

- title is accurate
- slug is clean
- excerpt is not hype
- body does not invent facts
- dates are present when needed
- names are spelled correctly
- categories and tags make sense
- custom fields are not nonsense
- source URLs are present if needed
- uncertainty is explained
- `WP Post ID` is correct

If anything looks wrong, edit the cells manually before staging.

## Troubleshooting

### `AI Publisher` menu is missing

Try:

1. Refresh the Sheet.
2. Wait 5-10 seconds.
3. Reopen the Sheet.
4. Open `Extensions > Apps Script` and confirm the project exists.

### Google asks for authorization

This is normal.

Authorize the Apps Script for your Google account.

### Google says the app is not verified

This is normal for a private Apps Script.

Click:

1. `Advanced`
2. continue to the project
3. `Allow`

Only do this for your own trusted copy of the Sheet.

### WordPress draft is not created

Check:

- `Sites` active row is correct
- site URL is correct
- WordPress username is correct
- WordPress application password is correct
- REST base is correct
- WordPress user can create posts
- application passwords are enabled
- the `Error` cell in `Publish`

### The script updates the wrong post

Check `WP Post ID`.

If it has an old number, the Sheet updates that old WordPress post.

Clear `WP Post ID` before creating a new post.

### OpenAI does not run

Check:

- OpenAI key in `Sites`
- prompt rows are set to `TRUE`
- source text is not blank
- Apps Script execution log
- `Error` cell in `Publish`

### AI output says source context is missing

This usually means the pasted source material was too thin or blank.

Fix:

1. Paste the actual article/report/transcript text.
2. Include source URLs.
3. Include dates, names, and context.
4. Run `Draft from Input` again.

### Custom fields are blank

This can be correct.

The prompt tells the AI to leave custom fields blank when the source does not support them.

If fields should have values:

- improve the source material
- improve the field `AI Instruction`
- make sure the field is enabled
- make sure the `WP Payload Path` is correct

### Custom fields do not show in WordPress

Check WordPress, not just the Sheet.

Common causes:

- meta field is not registered with `show_in_rest`
- SCF/ACF field is not exposed through REST
- wrong payload path
- wrong field key
- WordPress user lacks permission
- CPT does not support that meta field

### JSON or parser errors

The prompt stack now asks for exact JSON shapes.

If parser errors return:

- check whether someone edited away the JSON shape instructions in `Prompts`
- restore the original prompt contract
- run again

## Safe Defaults

For daily use, these are the safest defaults:

- Use pasted source text, not only URLs.
- Use `Draft from Input` first.
- Review manually.
- Use `Stage Draft`, not `Publish / Update`.
- Publish from WordPress after final review.
- Clear `WP Post ID` before a new article.
- Keep only one active row in `Sites`.
- Do not share the Sheet after adding credentials.

## What Not To Touch Unless You Mean It

Avoid changing:

- Apps Script code
- named ranges
- hidden formulas
- prompt placeholder names
- dropdown values
- custom field payload paths
- `REST Base`
- `Post Type`
- `Active` values in multiple site rows

If something breaks after an edit, undo the last change first.

## Practical Publishing Checklist

Before you run the AI:

- Source material pasted
- Source URL included if useful
- Right site is active
- `WP Post ID` is blank for new post or correct for update
- Action is `Draft from Input`

Before staging:

- Title reviewed
- Slug reviewed
- Excerpt reviewed
- Body reviewed
- Terms/tags reviewed
- Custom fields reviewed
- Error cell is blank
- Action is `Stage Draft`

Before publishing live:

- WordPress draft reviewed
- Links checked
- Source claims checked
- Category/tag choices checked
- Featured image checked
- `Publish / Update` is intentional

## Mental Model

Think of the Sheet as a control panel, not the final CMS.

The Sheet is for:

- collecting source context
- running the AI prompt stack
- preparing WordPress fields
- staging drafts
- updating posts

WordPress is still for:

- final preview
- visual formatting
- media selection
- final editorial approval
- live publishing when you want manual control

## The One-Sentence Version

Paste source material into `Publish`, run `Draft from Input`, review the generated fields, then run `Stage Draft` to send it to WordPress as a draft.

