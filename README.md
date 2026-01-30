# WAMessagePlatformIdentifier

A script that identifies and displays the platform (e.g., iPhone, Android, Web, etc...) from which a WhatsApp message was sent directly within the chat interface.

> [!NOTE]
> This script adds visual labels to messages indicating the sender's platform (like iPhone, Android, etc...). It works by intercepting internal WhatsApp Web functions to determine the platform associated with each message's unique ID .

> [!CAUTION]
> This JavaScript code is for **research purposes** only and is not intended to be used as a basis for any commercial or non-research purposes.
> The use of this code is at the user's own risk, and the author(s) assume no responsibility for any misuse or unintended consequences.

> **By using this code, the user acknowledges and agrees to the following:**
>
> - The user is solely responsible for any legal consequences arising from the use of this code.
> - The user will not hold the author(s) liable for any damages, losses, or legal actions resulting from the use of this code.
> - The user agrees to comply with any applicable laws and regulations, including but not limited to copyright, intellectual property, and privacy laws.
> - Furthermore, the user acknowledges that using this code may violate WhatsApp's Terms of Service and could potentially lead to account bans or other penalties.
> - The user is advised to consult their own legal counsel before using this code to ensure compliance with all applicable laws and regulations.

## Usage

1. Open WhatsApp Web in your browser.
2. Press F12 to open the developer tools.
3. Go to the `console` tab.
4. Paste the script.

```javascript
(() => {
    let resolvePlatformFromStanza;
    try {
        const platformModule = require('WAWebGetPlatformFromStanzaId');
        resolvePlatformFromStanza =
            typeof platformModule?.getPlatformFromStanzaId === 'function'
                ? platformModule.getPlatformFromStanzaId
                : null;
    } catch {
        resolvePlatformFromStanza = null;
    }

    const getPlatform = stanzaId =>
        resolvePlatformFromStanza ? resolvePlatformFromStanza(stanzaId) : 'unknown';

    const addPlatformLabel = messageElement => {
        if (messageElement.previousElementSibling?.classList.contains('platform-label')) return;

        const dataId = messageElement.dataset.id;
        if (!dataId) return;

        const parts = dataId.split('_');
        if (parts.length < 3) return;

        const platform = getPlatform(parts[2]); 

        const labelElement = document.createElement('div');
        labelElement.className = 'platform-label';
        labelElement.textContent = platform;
        
        labelElement.style.cssText = 'text-align:center;font-size:.7em;color:#666;padding:5px;';

        messageElement.parentNode?.insertBefore(labelElement, messageElement);
    };

    const scanTree = root =>
        root.querySelectorAll('div[role="row"] > div[data-id]').forEach(addPlatformLabel);

    new MutationObserver(mutations => {
        for (const mutation of mutations) {
            for (const node of mutation.addedNodes) {
                if (node.nodeType === 1) scanTree(node);
            }
        }
    }).observe(document.body, { childList: true, subtree: true });

    scanTree(document);
})();
```
