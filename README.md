# Bootstrap Re‑usable Confirm Modal

A drop‑in, **single‑template Bootstrap 5 confirm modal** that saves you from sprinkling repeated confirmation dialogs throughout your project.  
Just include the markup once, call a tiny helper, and you instantly have a polished confirm dialog everywhere you need one.

---

## ✨ Features

* **One & done** – add the markup once, reuse infinitely.
* **Tiny API** – a single helper (`confirm_init_modal_*`) opens the dialog and injects your route/message.
* **AJAX powered** – submits behind the scenes and reloads only when you decide.
* **Bootstrap 5 & jQuery** – leverages the libraries you already ship.
* **Theming ready** – swap the main button theme via Blade’s `$theme` variable.

---

## 📦 Requirements

| Library | Tested Version |
|---------|---------------|
| [Bootstrap](https://getbootstrap.com/) | 5.x |
| [jQuery](https://jquery.com/) | 3.x |
| [jQuery Toast](https://github.com/kamranahmedse/jquery-toast-plugin) | latest |
| Font Awesome (optional – icons) | 6.x |

> **Laravel users**: the examples below assume Blade templates and CSRF helpers, but the modal works in any stack.

---

## 🚀 Installation

1. **Copy the modal markup** (below) somewhere near the end of your `<body>`.
2. Ensure the required libraries are loaded **before** the modal (Bootstrap JS, jQuery, Toast plugin).
3. (Laravel) Add `@csrf` inside the form.
4. Optionally adjust the `$theme` Blade variable to match your design system.

```html
<!-- confirm‑modal.blade.php -->
<div class="modal fade" tabindex="-1" id="form-confirm-modal">
  <div class="modal-dialog">
    <div class="modal-content rounded-0">
      <div class="modal-header">
        <h5 class="modal-title">Important!</h5>
        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
      </div>
      <form id="submit-form-confirm">
        @csrf
        <div class="modal-body">
          <input type="hidden" id="confirm-route" readonly>
          <h2 id="confirm-message"></h2>
        </div>
        <div class="modal-footer">
          <button type="button" class="rounded-0 btn btn-{{ $theme ?? 'light' }}" data-bs-dismiss="modal">
            <i class="fa fa-times"></i> Cancel
          </button>
          <button type="submit" class="rounded-0 btn btn-danger px-5" onclick="form_confirm_action(event)">
            <i class="fa fa-check"></i> Confirm
          </button>
        </div>
      </form>
    </div>
  </div>
</div>
```

---

## 🛠️ Usage

### 1. Place a trigger button/link

```html
<button class="btn btn-danger"
        onclick="confirm_init_modal_delete(42, '{{ route('posts.destroy', 42) }}')">
  Delete post
</button>
```

### 2. Provide an init helper

```js
function confirm_init_modal_delete(id, route) {
  $('#form-confirm-modal').modal('show');
  $('#confirm-route').val(route);
  $('#confirm-message').text('Are you sure you want to delete this post?');
}
```
> Create as many uniquely named helpers as you like (`confirm_init_modal_archive`, `confirm_init_modal_reset`, …). Only the message/route differ.

### 3. AJAX submit handler

```js
function form_confirm_action(event) {
  event.preventDefault();
  $('#preloader').show();

  $.ajax({
    url: $('#confirm-route').val(),
    method: 'POST',
    data: $('#submit-form-confirm').serialize(),
    success: function (response) {
      $('#preloader').hide();
      $('#form-confirm-modal').modal('hide');
      if (response.success) {
        $.toast({
          heading: 'Success!',
          text: response.message ?? 'Success!',
          icon: 'success',
          position: 'top-center',
          hideAfter: 1025,
        });
        setTimeout(() => location.reload(), 1025);
      }
    },
    error: function (xhr) {
      $('#preloader').hide();
      const { message, errors } = JSON.parse(xhr.responseText);
      let details = Object.values(errors || {}).flat().join(' ');
      $.toast({
        heading: 'Error!',
        text: message ?? details ?? 'Error',
        icon: 'error',
        position: 'top-center',
        hideAfter: false,
      });
    },
  });
}
```

---

## 🎨 Customization

| What | How |
|------|-----|
| **Header text** | Change `<h5 class="modal-title">Important!</h5>` |
| **Body message** | Set in your `confirm_init_modal_*` helper (`$('#confirm-message').text(...)`) |
| **Cancel button theme** | Override Blade variable `$theme` (e.g. `'secondary'`) |
| **Confirm action** | Adjust `form_confirm_action` to PUT/DELETE, change reload timing, etc. |

---

## 🖼 Preview

![Modal preview](./Screenshot%202025-04-20%20at%2022.44.39.png)

---

## 🤝 Contributing

Pull requests are welcome! Please open an issue first to discuss major changes.

1. Fork the repo
2. Create your feature branch (`git checkout -b feat/amazing-feature`)
3. Commit your changes (`git commit -m 'feat: add amazing feature'`)
4. Push to the branch (`git push origin feat/amazing-feature`)
5. Open a PR

---

## 📄 License

Distributed under the MIT License. See `LICENSE` for more information.

---

### ⭐ If this saved you time, please star the repo!
