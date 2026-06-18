# NOTES

To allow copy/pasting inside the builder, Bricks converts elements to JSON code format. It had never actually occurred to me that this is how it works, until I looked into Brixies - I couldn't figure out how it was possible to copy and paste elements from their site directly into the builder, so pasted into a text file, realised what it was, and set to work chucking them into ChatGPT one by one to build up a knowledge base.

Since then, I've been methodically working through the native Bricks elements, Bricksforge elements and others, and testing the limits of what AI can do with Bricks clipboard JSON. I've managed to build some very nice sites with a lot of custom functionality by having AI agents build me Bricks clipboard JSON that I can paste in.

You'll still need to develop a good understanding of Bricks Builder in general, and learning a bit of basic HTML and CSS can't hurt either - it makes everything easier to understand, plus AI will likely not get everything exactly right on the first attempt, so being able to manually tweak and edit things is useful.

# Suggested usage

The most reliable workflow is not to ask ChatGPT to generate a full Bricks section immediately.

Instead:

1. Start by giving ChatGPT the bricks-json-readme.md file.
2. Explain what you want to build.
3. Ask it to plan the Bricks structure first.
4. Review the plan and correct anything site-specific.
5. Only then ask for the complete clipboard JSON.
6. For large existing forms or complex layouts, ask for small targeted edits rather than a full rebuild.

This reduces the chance of broken parent/child relationships, lost form IDs, missing children, overcomplicated CSS, and risky BricksForge form rewrites.

I've recently had more success with planning each section methodically - first I'll define the structure of a section with the agent, then ask it to mock the page up based on our decisions (ChatGPT's generative image tool is great for this, haven't tested with Gemini or Claude yet) and once I'm happy with it, I'll ask it to generate the Bricks clipboard JSON to match the mockup as closely as possible.

# MY WORKFLOW - EXAMPLE PROMPTS

# 1. Start a new chat

I’m going to give you my bricks-json-readme.md file first. Please read it and use it as the working rules for this chat.

After that, I’ll explain the section/component/form I want to build.

For now, don’t generate JSON yet. First, help me plan the structure, confirm the likely Bricks elements we’ll need, and flag anything that might be risky or better handled manually.

# 2. Plan a section before generating JSON

Using the Bricks JSON README rules, help me plan a pasteable Bricks section for [page/site/component].

Goal:
[Explain what the section needs to do]

Content required:
- [Heading/copy/cards/buttons/images/etc.]

Design direction:
- [Brand style, layout idea, colours, examples, screenshots, etc.]

Please do not output the JSON yet. First give me:
1. The recommended Bricks element structure.
2. Any responsive layout notes.
3. Any CSS/JS that may be needed.
4. Anything I should prepare first, such as image URLs, links, icons or existing element IDs.

# 3. Generate one complete pasteable JSON blob

Now generate the complete Bricks clipboard JSON for the approved section.

Use the rules from bricks-json-readme.md:
- Native Bricks elements where possible.
- Exactly 6-character element IDs.
- Valid parent/child relationships.
- Scoped CSS/JS only where needed.
- BEM-style classes for hooks.
- Use Bricks settings before custom CSS.
- Include responsive settings.
- Put the full JSON in one fenced json code block.

Keep explanation minimal after the JSON, limited to any post-paste edits I’ll need to make.

# 4. Modify an existing Bricks JSON blob safely

I’m going to paste an existing Bricks clipboard JSON blob below.

Please inspect it against the README rules, but do not rebuild the whole thing unless absolutely necessary.

I want to change only:
[Describe the exact change]

Please preserve:
- Existing element IDs where practical.
- Existing form field IDs.
- Existing webhook/PDF/conditional logic.
- Existing parent/child structure unless it is broken.

Return either:
1. The smallest replacement JSON chunk, or
2. The specific settings/children changes I should make manually.

# 5. Bricksforge Pro Forms safe-edit prompt

This is a BricksForge Pro Forms JSON blob. Please be careful with it.

I only want to update:
[Step / field group / hidden fields / webhook mapping / PDF HTML / JS]

Do not regenerate the entire form.

Please preserve:
- Pro Forms field IDs.
- Element IDs where possible.
- Conditional logic.
- Calculations.
- PDF merge tags.
- Webhook mappings.
- Submit actions.

First explain the safest edit strategy, then provide the smallest practical replacement block or manual changes.

# 6. Debug a pasted Bricks JSON section

I pasted this Bricks JSON into Bricks and something went wrong.

Problem:
[Describe what happened]

JSON:
[Paste JSON]

Please check for:
- IDs longer or shorter than 6 characters.
- Missing child elements.
- Invalid parent references.
- Code elements not listed in parent children arrays.
- Unscoped CSS/JS.
- Bricks settings that may not import reliably.
- Mobile overflow issues.

Give me the likely cause first, then provide the corrected JSON or the smallest patch.

# 7. Turn a mockup into Bricks structure

Using the attached screenshot/mockup and the bricks-json-readme.md rules, translate this design into a Bricks-native element plan.

Do not generate JSON yet.

Please identify:
- Section/container/card structure.
- Which elements should be headings, text-basic, text, buttons, images or icons.
- Which parts need global classes.
- Which parts need BEM classes.
- Whether any CSS/JS is actually necessary.
- How the layout should behave on tablet and mobile.

(Once the agent comes back with a plan, verify it's all correct + then ask it to build the JSON).

# 8. Generate a reusable page section from approved copy

Here is the final copy for the section:

[Paste copy]

Please generate a pasteable Bricks clipboard JSON section using the README rules.

Requirements:
- One top-level section.
- Native Bricks elements.
- Editable text, not hardcoded HTML where avoidable.
- Real card containers for repeated items.
- FontAwesome icons where suitable.
- Placeholder links set to "#".
- Image placeholders using relative paths or example.com only.
- Responsive grid settings included.

# 9. Build an interactive section

I want an interactive Bricks section.

Behaviour:
[Describe interaction, e.g. tabs, hotspots, gallery, selector cards]

Please follow the README rules:
- Use native Bricks elements for the static structure.
- Use scoped JS only for the interaction.
- Use scoped CSS only where Bricks settings are not enough.
- Make the section still readable if JS fails.
- Avoid hidden image banks or opacity-swapped stacked images.
- Use the conveyor/track pattern for galleries if images need to change.

First give me the planned DOM/Bricks structure and interaction logic. Wait for approval before generating JSON.

# 9. Final pre-flight check before pasting

Before I paste this into Bricks, please run a final pre-flight check against the README.

Check:
- Clipboard wrapper.
- 6-character IDs.
- Unique IDs.
- Parent/child validity.
- Code element placement.
- Scoped CSS/JS.
- Responsive settings.
- Image/link placeholders.
- Whether any BricksForge field IDs or functional settings may have been broken.

Then give me either “safe to paste” or a corrected version.
