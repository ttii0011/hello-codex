# Thank-You Page Code Review

## Summary
The provided thank-you page is largely well-structured, but it exposes a client-side security issue in the chat widget.

## Findings
- **Cross-site scripting (XSS) risk in `pushMsg`:** When the chat widget renders user messages, it writes the raw input string with `innerHTML`. Because user-supplied text is never sanitized or escaped, entering a string such as `<img src=x onerror=alert('XSS')>` will inject arbitrary HTML/JavaScript into the DOM. This allows an attacker to execute scripts in the customer’s browser and potentially steal sensitive data. Update the logic so user messages use `textContent` (or an explicit sanitizer) instead of `innerHTML`. Only trusted bot messages should be rendered as HTML. Example fix:
  ```js
  if (who === 'bot') {
    div.innerHTML = text;
  } else {
    div.textContent = text;
  }
  ```
  This preserves intended formatting for bot responses while blocking malicious markup from customers.

## Recommendations
- Patch the chat widget as shown above before deploying the page.
- Consider adding basic HTML escaping utilities if you need limited formatting in user messages (e.g., links) without reintroducing the XSS risk.
