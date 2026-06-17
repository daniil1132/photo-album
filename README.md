# Фотоальбом (GitHub Pages + Supabase)

Сайт-галерея, где **фото загружаются кнопкой прямо со страницы**, хранятся в облаке Supabase, а сама страница лежит на GitHub Pages. Доступ — по ссылке + паролю.

- 🔒 Просмотр — по паролю `VIEW_PASSWORD`
- ⬆️ Загрузка/удаление — по админ-паролю `ADMIN_PASSWORD` (только для тебя)

---

## ЭТАП 1. Создать проект на Supabase (хранилище фото)

1. Зайди на **https://supabase.com** → нажми **Start your project** → войти через GitHub.
2. **New project**:
   - Name: `photo-album` (любое)
   - Database Password: задай любой, запиши
   - Region: ближайший (например, Frankfurt)
   - Нажми **Create new project**, подожди ~2 мин.
3. Создать хранилище (Bucket):
   - Слева меню → **Storage** → **New bucket**
   - Name: `photos`  (именно так, маленькими)
   - ✅ **Public bucket** — включи (чтобы фото можно было показывать по ссылке)
   - **Save**
4. Разрешить загрузку без логина:
   - Слева **Storage** → **Policies** (или вкладка Policies у бакета `photos`)
   - Нажми **New policy** → **For full customization**
   - Заполни:
     - Name: `public_upload`
     - Allowed operation: **All** (или по очереди Select/Insert/Update/Delete)
     - Target roles: оставить пусто
     - WITH check expression: `true`
     - USING expression: `true`
   - **Save**
5. Взять ключи:
   - Слева **Project Settings** (шестерёнка) → **API**
   - Скопируй:
     - **Project URL** → это `SUPABASE_URL`
     - **anon public key** → это `SUPABASE_KEY`

> ⚠️ Копируй именно **anon public**, НЕ service_role — service_role нельзя класть в сайт!

---

## ЭТАП 2. Вписать ключи и пароли в сайт

1. Открой `index.html`, найди блок `// ====== НАСТРОЙКИ ======`.
2. Замени:

```js
const SUPABASE_URL  = "https://ТВОЙ_ПРОЕКТ.supabase.co";   // ← Project URL
const SUPABASE_KEY  = "ТВОЙ_ANON_KEY";                       // ← anon public key
const BUCKET        = "photos";

const VIEW_PASSWORD  = "view";    // ← пароль для тех, кому даёшь ссылку
const ADMIN_PASSWORD = "admin";   // ← пароль для загрузки/удаления (твой)
```

---

## ЭТАП 3. Залить сайт на GitHub

1. Создай репозиторий на GitHub (например, `photo-album`). Поставь **Public**.
2. Загрузи туда содержимое папки `photo-site/` (`index.html` и `README.md`):
   - Простой способ: на странице репозитория → **Add file → Upload files** → перетащи оба файла → **Commit**.
   - Либо через терминал:
     ```bash
     cd photo-site
     git init
     git add .
     git commit -m "photo album"
     git branch -M main
     git remote add origin https://github.com/ТВОЙ_ЛОГИН/photo-album.git
     git push -u origin main
     ```

---

## ЭТАП 4. Включить GitHub Pages

1. В репозитории → **Settings → Pages**.
2. **Build and deployment**:
   - Source: **Deploy from a branch**
   - Branch: `main` / **/ (root)**
   - **Save**
3. Через ~1 мин сверху появится ссылка:
   `https://ТВОЙ_ЛОГИН.github.io/photo-album/`

Это и есть адрес твоего сайта.

---

## Как пользоваться

**Загрузить фото (ты):**
1. Открой сайт → введи пароль просмотра.
2. Нажми **«＋ Загрузить»** → введи админ-пароль.
3. Перетащи фото или выбери файл → они сразу появятся в галерее.

**Показать друзьям:**
- Дай ссылку на сайт + пароль просмотра (`VIEW_PASSWORD`).
- Они смогут смотреть, но не загружать/удалять.

**Удалить фото:** наведи на фото → ✕ (нужен админ-режим).

---

## Лимиты бесплатного Supabase

- **1 ГБ** хранилища и **2 ГБ** передачи в месяц на бесплатном тарифе.
- Этого хватает на ~500–1000 фото в среднем.
- Если превысишь — фото не удалятся, просто перестанут грузиться новые до следующего месяца.

## Безопасность

- Бакет `public` = кто знает URL файла, тот его и так откроет. Пароль сайта защищает **галерею**, а не сами файлы напрямую. Для семейных/личных фото этого достаточно.
- Если хочешь, чтобы фото вообще никто без пароля не открывал — отключи **Public bucket** и поменяй Policy на проверку, но тогда и сайт нужно будет усложнять. Скажи — сделаю.
- `anon key` безопасно держать в коде страницы — он и создан для фронтенда. Но доступ ограничивается именно Policy в Supabase.
