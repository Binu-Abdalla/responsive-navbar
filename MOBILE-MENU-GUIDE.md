# Build the Mobile Menu Yourself

This guide walks through adding an open/close interaction to this project's navbar. It is a learning reference, not a command to paste everything at once. Do one stage, test it, and make sure you can explain what changed before moving on.

## What the finished interaction should do

On a phone-sized screen:

1. The logo and menu button are visible.
2. The navigation links start hidden.
3. Clicking the menu button shows the links.
4. Clicking it again hides the links.
5. The button's `aria-expanded` value matches whether the links are open.

On a wider screen, the links remain visible and the menu button is hidden.

## The three-part model

- **HTML** gives the control and the links clear identities.
- **CSS** decides what the closed and open states look like.
- **JavaScript** responds to the click and switches between those states.

JavaScript does not need to directly style every link. It can add or remove one class, and CSS handles the appearance of that class.

## Stage 1: Connect the button to the links

Your HTML already uses a `<button class="menu hide-desktop">`. A button is the right element because it performs an action. Add state information and a link to the list:

```html
<button
  class="menu hide-desktop"
  type="button"
  aria-expanded="false"
  aria-controls="primary-links"
  aria-label="Toggle navigation"
>
  <img src="Materials/menu.svg" alt="">
</button>
```

Give the existing list the matching ID:

```html
<ul id="primary-links" class="hide-mobile nav-links">
  <!-- Keep your existing list items here. -->
</ul>
```

What these attributes mean:

- `type="button"` makes the button's purpose explicit.
- `aria-controls` names the element this button affects. Its value must match the list's `id` exactly.
- `aria-expanded="false"` starts the button in the closed state. JavaScript will update it.
- `aria-label` gives the icon-only button a name for assistive technology.
- `alt=""` makes the icon decorative because the button already has a label.

**Checkpoint:** Inspect the page and confirm it still looks the same. The button does not open anything yet; this stage only describes the control and its target.

## Stage 2: Make the closed and open styles

Your phone media query already hides `.hide-mobile`. Keep that behavior, and add a rule for the open state:

```css
@media (max-width: 767px) {
  .hide-mobile {
    display: none;
  }

  .hide-desktop {
    display: inline-flex;
  }

  .nav-links.is-open {
    display: flex;
    flex-direction: column;
  }
}
```

The important relationship is:

- Closed list: it has `.nav-links` but not `.is-open`; `.hide-mobile` hides it at phone widths.
- Open list: it also has `.is-open`; `.nav-links.is-open` has a more specific selector than `.hide-mobile`, so it can show the list.
- Wide screen: the phone media query no longer applies, so `.hide-mobile` links are visible and `.hide-desktop` is hidden by the base rule.

If your phone links should have spacing or a background when open, add those after the basic show/hide behavior works.

**Checkpoint:** You can temporarily add `is-open` to the list in the HTML and refresh. At a phone width, the links should appear. Remove the temporary class afterward. This tests the CSS before JavaScript is involved.

## Stage 3: Load a JavaScript file

Create a file named `main.js` in the same folder as `index.html` and `main.css`. Add this script element inside the HTML `<head>`:

```html
<script src="main.js" defer></script>
```

`src` is the path to the JavaScript file. `defer` tells the browser to wait until the HTML has been parsed before running it, so the button and list can be found.

**Checkpoint:** Open the browser developer tools Console, refresh, and confirm there are no file-loading errors. The menu still will not open yet.

## Stage 4: Find the elements in JavaScript

At the top of `main.js`, select the button and the list:

```js
const menuButton = document.querySelector(".menu");
const navigation = document.querySelector("#primary-links");
```

`querySelector` finds the first element matching a CSS selector:

- `".menu"` means an element with the class `menu`.
- `"#primary-links"` means the element whose ID is `primary-links`.

The result is stored in a `const` so you can use it later. The selector strings must match your HTML.

**Checkpoint:** Temporarily add these lines and inspect the Console. To see what was found, you can log `menuButton` and `navigation` with `console.log(...)`. Each should show an HTML element, not `null`. Remove the logs when finished.

## Stage 5: React to a click

Add a click listener to the button. First, read the current state and calculate the next state:

```js
menuButton.addEventListener("click", () => {
  const isExpanded = menuButton.getAttribute("aria-expanded") === "true";
  const nextIsExpanded = !isExpanded;

  // Update the button and list using nextIsExpanded.
});
```

Reason through the two values:

- If `aria-expanded` is currently `"false"`, `isExpanded` is `false` and `nextIsExpanded` becomes `true`.
- If it is currently `"true"`, `isExpanded` is `true` and `nextIsExpanded` becomes `false`.

Now finish the listener by doing two updates:

1. Set the button's `aria-expanded` attribute to `String(nextIsExpanded)`.
2. Toggle the list's `is-open` class using `nextIsExpanded` as the force value.

Reference for the two APIs:

```js
someElement.setAttribute("attribute-name", "new value");
someElement.classList.toggle("class-name", trueOrFalse);
```

The second argument to `classList.toggle` makes the class match the requested state: `true` adds it, `false` removes it. This keeps the CSS state and accessibility state synchronized.

**Checkpoint:** At a phone width, click once and check that the links appear and `aria-expanded` becomes `true`. Click again and check that the links disappear and it becomes `false`.

## Stage 6: Test the whole behavior

- At a phone width, links start hidden and the button is visible.
- One click opens the links; another click closes them.
- At a wide width, links are visible and the menu button is hidden.
- Use Tab to focus the menu button, then press Enter or Space. A real button supports keyboard activation automatically.
- Check the Console for errors if nothing happens.

## Debugging clues

**Click does nothing:** Check that the script path is correct, `defer` is present, and both `querySelector` calls find elements rather than `null`.

**The button works but links stay hidden:** Check that the class name in JavaScript is exactly `is-open`, and the CSS selector is `.nav-links.is-open`.

**Links appear but cannot be hidden again:** Make sure the class toggle uses `nextIsExpanded` as its second argument. Without that state value, adding/removing classes can drift out of sync with `aria-expanded`.

**The wrong element is affected:** Check that the button has `aria-controls="primary-links"` and the list has `id="primary-links"`. IDs must be unique on the page.

**It works on desktop but not phone:** Check the width condition in the media query and inspect whether another CSS rule is overriding `display`.

## Practice challenge

After the basic menu works, try making it close when a navigation link is selected. Before changing code, predict which event should trigger the close and which two states need to be updated. Test that behavior separately from opening and closing with the button.