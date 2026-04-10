# Main Idea:

- Automatically reduces prompt length while preserving semantic meaning using token pruning + importance scoring
- Use intent recognition + LLM service based prompt templates + custom templates meant for tasks (have a selection in boxes)
- GUI is needed for this / use CLI option
- Try to make it a browser plugin
- Langchain / Langraph / Llamaindex integration
- Use prompt caching ("word bulk -> word breakdown" is stored)
- Check for all the optimisation techniques, for example translation to chinese, etc.
- "I built a tool that cuts your LLM API costs in half without losing quality. Here's how semantic compression works..."
- All of this while being BLAZING fast
    - Make it so that the user can press "enter" on the optimisation and it automatically pastes and presses enter on the text blob

---

# Process:

1. Take user prompt

2. Options:
    - Template:
        - The template is LLM provider dependant / changes when the LLM is changed
        - Methods of template selection
            - Select pre-made template
            - Auto-template selection:
                - Uses intent recognition (/ classifier model / keyword detection / smart content detection) to classify the prompt into one of the following categories:
            - Custom template:
                - Create custom template and upload it 
                    - can use bitly or online free notepads to break the template into something shareable -> then paste it and it will fill up the template
                - Save it on local
        - Options of templates:
            - coding / personal / mental / health / planning / discussion / web search / generic / etc
    - GUI Options: # Note - Pick one MVP, expand after.
        - Website
        - CLI
        - Browser-Extension
    - N-Shot Inference:
        - Multi-shot (provided examples)
        - Single-shot (no example needs to be provided)
    - History:
        - On - (Basically multi-shot)
        - Off - (No history, each optimisation flow is new)
    - A/B Testing:
        - try the same prompt with different optimization levels, compare results side-by-side
    - % Optimisation Slider:
        - Low / Medium / High / Overkill

3. Processing:
    - Intent Recognition
    - Named Entity Recognition
    - Off-loading to LLM 
        - Faster - Grunt (Generic) LLM
        - Slower - Local LLM # Note - defeats the purpose, skip this initially or gate it as an optional feature.
            - Or use other non-LLM methods
    - Template addition
    - History injection (if multi-shot)
    - Template pruning
    - Importance scoring
    - Need to take care of longer prompts which include code blocks in them:
        - Identify which parts of the prompt do not need to be changed
        - aka change just the "ordering / telling" part of it / having the user select -> right click -> optimise prompt -> and it optimises it for them
    - Optimization:
        - Use non-LLM techniques as well (eg: translating to chinese helps reduce token count)
    - Cache the result of small chunk-based optimisations
    - Rollback history
        - Show user the 3-4 optimization candidates, not just the best one [ex: "Option A: 280 tokens (94% quality)"]

4. Output the optimised prompt:
    - Options:
        - Make it copy to clipboard
        - Override / Paste into selection
        - Auto-work: Make it so that the user can press "enter" on the optimisation and it automatically pastes and presses enter on the text blob on AI sites
            - Note - raises security concerns
    - Add metrics dashboard (ex.):
        ```
        - Original: 450 tokens, $0.0045
        - Optimized: 280 tokens, $0.0028
        - Savings: 38% tokens, 39% cost
        - Quality score: 94% (how much meaning preserved)
        ```

---

# Possible Issues:

- Quality Score Problem
    - You mention "preserving semantic meaning" but don't address how to verify it. Options:
        - Embedding similarity (fast): Compare embeddings of original vs optimized, show % match
        - LLM judge (slower): Have Claude/GPT rate quality preservation (ironic but works)
        - User feedback loop: Let users rate if output quality suffered

- Template System Refinement
    - Current: Templates are LLM-dependent
    - Problem: That's implementation detail, not user-facing

    - Better framing:
        - Intent-based templates (coding, creative, analysis, etc)
        - Provider-specific variants stored internally
        - User doesn't need to care about provider details

- Handle Code Blocks Explicitly
    - Instead of "smart detection," do this:
        - Detect markdown code blocks (```...```)
        - Give user checkbox: "Preserve code blocks as-is?"
        - If yes: Only optimize the prose around it
        - If no: Optimize entire thing

- Cache Strategy Clarification
    - What to cache:
        - Template + intent classification results (tiny, high hit rate)
        - Full optimized prompts (for recurring tasks)

    - What NOT to cache:
        - Individual token scoring (not reusable)
        - Intermediate NER results (too specific)

    - Use: Simple key-value store (IndexedDB for browser, SQLite for CLI)

---

# References / Sources

- LLMLingua // Microsoft:
    - https://llmlingua.com/llmlingua2.html
    - https://huggingface.co/spaces/microsoft/llmlingua-2
    - https://www.microsoft.com/en-us/research/project/llmlingua/
    - https://github.com/microsoft/LLMLingua

- DiffuMask:
    - https://arxiv.org/html/2604.06627v1#A4

- CompactPrompt:
    - https://arxiv.org/html/2510.18043v1