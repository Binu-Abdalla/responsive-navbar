# Offline Frontend Reference: Navbar Project

Use this as a quick reminder while working without internet. The examples are small patterns, not a complete navbar solution. Try applying one idea at a time, then check it in your browser.

## 1. Basic HTML page

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="stylesheet" href="main.css">
  <title>Page title</title>
</head>
<body>
  <!-- Page content goes here -->
</body>
</html>
```

- `viewport` lets the page use the device's actual screen width.
- `href="main.css"` works when `main.css` is beside the HTML file.
- A relative image path such as `Materials/menu.svg` starts from the HTML file's folder.
- Use `<a>` for navigation to another page or section. Use `<button>` for an action.

## 2. Navigation structure

```html
<nav aria-label="Main navigation">
  <a href="index.html" class="nav-logo">Site name</a>
  <ul class="nav-links">
    <li><a href="#home">Home</a></li>
    <li><a href="#about">About</a></li>
  </ul>
</nav>
```

- A `<nav>` landmark helps people using assistive technology find navigation.
- Put each link inside an `<li>` when it is part of a list of links.
- Give links useful destinations. `href="#about"` should match an element with `id="about"`.
- For a logo image, write meaningful `alt` text if the image communicates the site name. Use `alt=""` only when the image is decorative or the adjacent link already has an equivalent accessible name.

## 3. CSS selector reminders

```css
nav { }                 /* every nav element */
.nav-links { }          /* every element with class="nav-links" */
nav a { }               /* links inside nav */
.nav-links a:hover { }  /* hovered links inside that class */
```

Classes begin with a dot in CSS. Element names do not. A selector only matches when the HTML element and class spelling agree exactly.

## 4. Flexbox navbar pattern

```css
nav {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
}

.nav-links {
  display: flex;
  align-items: center;
  gap: 1rem;
  list-style: none;
  margin: 0;
  padding: 0;
}
```

- `display: flex` lays the direct children out in a row by default.
- `align-items` aligns children across the row (vertically in this layout).
- `justify-content` distributes free space along the row.
- `gap` adds space between children without adding margins to each one.
- `list-style: none` removes the list bullets; reset the list's default margin and padding too.

## 5. Box model and sizing

```css
* {
  box-sizing: border-box;
}

nav {
  width: 100%;
  padding: 1rem;
  border: 1px solid currentColor;
}
```

With `border-box`, an element's declared width includes its padding and border. Without it, padding and border are added outside the declared width. Use padding for space inside an element and margin for space outside it.

## 6. Responsive CSS

Start with the layout that should work on a narrow screen, then add a breakpoint for wider screens:

```css
/* Narrow-screen defaults */
.nav-links {
  display: none;
}

.menu-button {
  display: inline-flex;
}

/* Wider-screen layout */
@media (min-width: 48rem) {
  .nav-links {
    display: flex;
  }

  .menu-button {
    display: none;
  }
}
```

The number in a media query is a design choice, not a device name. Choose a width where the content stops fitting comfortably. Keep the breakpoint rule and the base rule consistent: if a menu should appear on small screens, one rule must make it visible there.

In this project, `.hide-desktop` is set to `display: none` in the base CSS, and the current media query also sets it to `display: none`. That means the menu icon stays hidden at the moment. Decide which screen size should show each element, then make the base rule and media query express that behavior.

## 7. Link states and keyboard focus

```css
.nav-links a:hover {
  text-decoration: underline;
}

.nav-links a:focus-visible,
.menu-button:focus-visible {
  outline: 3px solid currentColor;
  outline-offset: 3px;
}
```

`:hover` is for pointer interaction. `:focus-visible` gives keyboard users a visible indication of where they are. Do not remove the outline unless you provide another clear focus style.

## 8. Optional: interactive mobile menu

If the menu icon is supposed to open and close the links, it should be a button, not a clickable `<div>` or an image by itself.

```html
<button class="menu-button" type="button" aria-expanded="false" aria-controls="primary-links">
  <img src="Materials/menu.svg" alt="">
  <span class="visually-hidden">Toggle navigation</span>
</button>

<ul id="primary-links" class="nav-links">
  <!-- navigation links -->
</ul>
```

```css
.visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
```

```js
const menuButton = document.querySelector(".menu-button");
const navigation = document.querySelector("#primary-links");

menuButton.addEventListener("click", () => {
  const isExpanded = menuButton.getAttribute("aria-expanded") === "true";
  menuButton.setAttribute("aria-expanded", String(!isExpanded));
  navigation.classList.toggle("is-open", !isExpanded);
});
```

```css
@media (max-width: 47.999rem) {
  .nav-links {
    display: none;
  }

  .nav-links.is-open {
    display: flex;
    flex-direction: column;
  }
}
```

The JavaScript changes the button's expanded state and toggles a class. CSS decides what that class looks like. If you use this pattern, place the `<script>` just before `</body>` or load it with `defer` so the HTML exists before JavaScript selects it.

## 9. Quick debugging checklist

1. **Styles not showing?** Check that the stylesheet link path and filename are correct.
2. **Image missing?** Check capitalization and the path from the HTML file to `Materials/`.
3. **Rule not applying?** Check selector spelling, class names, braces, and whether another rule overrides it later.
4. **Mobile layout wrong?** Check the viewport meta tag, the media-query condition, and which rule wins at that width.
5. **Items overflow?** Temporarily remove large margins, reduce the gap, and check whether a child has a fixed width.
6. **Menu does not open?** Check the browser Console for errors, verify the script loads, and ensure the selectors match the HTML.
7. **Looks different after a change?** Save, refresh the browser, and test both a narrow and a wide window.

## 10. A useful practice loop

Before each change, write down what you expect to happen. Change one thing, refresh, and compare the result with that prediction. If it differs, inspect the closest HTML element and the CSS rule that controls it before changing several things at once.