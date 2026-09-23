---
title: "Knowledge Base"
draft: true
---

# Code Block with Filename

```{.c filename="main.c"}
#include <stdio.h>

int main() {
    printf("Hello, world!\n");

    return(0);
}
```

# Callout Blocks

:::{.callout-note}
This is a note.
:::

:::{.callout-tip}
This is a tip.
:::

:::{.callout-warning}
This is a warning.
:::

:::{.callout-caution}
This is a caution.
:::

:::{.callout-important}
This is important!
:::

# Spans

[This text is smallcaps]{.smallcaps}

[This text is underlined]{.underline}

[This text is highlighted]{.mark}

# Equations

inline math: $E = mc^{2}$

display math:

$$E = mc^{2}$$

# Raw Content

```{=html}
<iframe src="https://quarto.org/" width="500" height="400"></iframe>
```

# Videos

{{< video https://www.youtube.com/embed/wo9vZccmqwc >}}
