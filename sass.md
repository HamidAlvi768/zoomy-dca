In Dart Sass the old `@import` mechanism is being phased out in favor of the module system (`@use`/`@forward`), and you’re also pointing at files that live in your **scss** directory, not the **css** folder where your `main.scss` currently lives. You have two things to fix:

---

## 1. Switch your imports to the new module system

In `assets/css/main.scss`, replace all your `@import '…';` lines with `@use` (you can still “bring in” all the members into your namespace, or even namespace‑less if you want). Because your partials live in **assets/scss/**, you’ll need a relative path:

```scss
// assets/css/main.scss
@use "../scss/variables" as *;  // brings in all variables at the top level
@use "../scss/mixins"    as *;
@use "../scss/common"    as *;
@use "../scss/overlay"   as *;
@use "../scss/header"    as *;
// …etc
```

> **Note:**
>
> * The leading underscore (`_variables.scss`) is automatically inferred by Sass.
> * `as *` flattens the namespace so you can still write `color-primary` instead of `variables.$color-primary`; if you’d rather namespace, drop the `as *` and then refer via `variables.$color-primary`.

---

## 2. Tell the compiler where to look (or adjust your file layout)

### Option A: Use a load‐path

If you’d like to keep your `@use "variables";` lines without a relative path, add a `--load-path` flag:

```bash
sass \
  --load-path=assets/scss \
  assets/css/main.scss assets/css/main.css
```

Or, watching for changes:

```bash
sass --watch \
  --load-path=assets/scss \
  assets/css/main.scss:assets/css/main.css
```

This tells Sass “whenever you see `@use "variables"`, look first in `assets/scss/variables.scss`.”

---

### Option B: Move your `main.scss` into the scss folder

A more conventional layout is:

```
assets/
├─ scss/
│  ├─ main.scss
│  ├─ _variables.scss
│  └─ … other partials …
└─ css/
   └─ (output .css lives here)
```

Then from your project root:

```bash
sass scss/main.scss css/main.css
```

and watch:

```bash
sass --watch scss/main.scss:css/main.css
```

---

### Quick recap

1. **Migrate** your `@import` → `@use` (or `@forward` in your partials).
2. **Point** Sass at the right folder (via `--load-path`) *or* reorganize so `main.scss` and partials live under one `scss/` tree.

Once that’s done, rerun your compile command and those deprecation errors (and the “can’t find” errors) will go away. Let me know if you need an example of a full `package.json` script or watcher setup!
