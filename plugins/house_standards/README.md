# house_standards

The shared standard every domain plugin builds on. Right now it carries one skill, `report_style`, the tone and formatting rules for every operational report the org produces.

It exists so house style lives in exactly one place. When you add the executive ops plugin or any other domain, its reports inherit the same voice without copying the rules. Change the voice here and every report changes.

## Contents

* skills/report_style: tone, substance, and formatting rules. Loads automatically whenever Claude is producing a report or summary.

## Install

Install this alongside any domain plugin from this marketplace. The domain plugins expect it to be present and reference its skill by name. In a Team or Enterprise marketplace you can set it to install automatically so nobody forgets it.

## Adding shared standards later

If a second concept turns out to be truly cross domain, for example a shared definition of a fiscal period or a shared escalation severity scale, add it here as its own skill rather than duplicating it into each domain plugin.
