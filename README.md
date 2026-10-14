# Joshi's Corner · Kitchen Manual

Static site (HTML, CSS, vanilla JS). No build step.

```
joshis-kitchen-manual/
├── index.html        app: search, filters, cards, detail drawer
├── recipes.json      all dish data (edit this)
├── .nojekyll
└── assets/images/    dish photos, named as in image_url
```

## Add or edit a dish
1. Open `recipes.json`. Copy an existing object and paste it before the closing `]`. Add a comma between objects.
2. Give it a new unique `id` (`item_005`, ...).
3. Prices are in rupees as plain numbers. Use `null` for a tier that does not apply or is not set yet.
4. Put the photo at `assets/images/<name>.jpg` and match `image_url`. A missing photo shows the dish initial.
5. `youtube_reference_url`: a `watch?v=` or `youtu.be` link is embedded in the page; any other YouTube link shows as a button.
6. Categories come from the data. A new `category` value creates a new filter button automatically.
7. Validate the JSON at jsonlint.com before committing (a stray comma breaks the page).


## Upload a whole menu from Excel
1. Press **Excel template**, fill it in, then press **Import Excel**.
2. Columns (any order, names are matched loosely): Item, Category, Ground Floor, Mezzanine, Online, Ground Breakfast, Mezzanine Breakfast, Staff, Video Link. Only Item is required.
3. Dishes are matched by name. Existing dishes get their prices and category updated and keep their recipe and photo. New names are added. A blank price cell never erases an existing price.
4. A workbook with several sheets uses each sheet name as the category when there is no Category column.

## Edit, pictures, videos
Open a dish and press **Edit** to change any field, upload a picture (resized to 800 px) and paste a YouTube link. **Add dish** creates a new one.

## Fill in recipes
1. Press **Dishes needing recipes** to download `dishes-needing-recipes.json`.
2. Send that file to Claude in chat and ask for recipe, SOP, nutrition, storage and shelf-life details in the same format.
3. Press **Load menu file** and choose the JSON Claude returns. Prices, photos and videos you already set are kept.
4. Review every recipe with your chef before kitchen use.

## Saving and sharing
Edits are stored in the browser you made them in (IndexedDB), not on GitHub. To share with other tablets: press **Save menu file**, replace `recipes.json` in the repository with the downloaded file, and commit. Embedded pictures make the file large; for many dishes, upload photos to `assets/images/` instead and set `image_url` to the path.


## Languages (English, Hindi, Bengali)
Pick a language in the selector at the top. Hindi and Bengali lines show the translation with the English line underneath. Translations live in `i18n/hi.json` and `i18n/bn.json` and are looked up by the exact English sentence, so any dish that uses the same sentence is translated automatically. To translate more, add `"English sentence": "translation"` pairs to the `map` section, and base ingredient words to `terms`. A line with no translation simply shows in English.

## Several videos per dish
Open a dish, press Edit, and list the videos one per line as `Label | URL` (for example `Plating | https://youtu.be/...`). YouTube links are embedded, other links show as buttons. In Excel, put several links in the Video Link column separated by spaces. Loading a menu file never removes links you added; it only adds new ones.

## Photos
- Fastest: name each photo after the dish in lowercase with hyphens (`masala-dosa.jpg`) and put it in `assets/images/`. The page finds it automatically.
- Or press **Bulk photos** and select many pictures at once; they are matched to dishes by file name. Then press **Photos ZIP**, unzip into the repository, and commit.


## Mint theme and brand
The page uses a mint-green background. "Joshi's Corner" is set in Brush Script MT (gold). Brush Script MT is installed on Windows and Mac; Android phones fall back to a similar handwriting font.

## Admin login with Google, Drive sync, view-only users (optional)
Leave `config.js` empty to run in simple local mode (no login). To switch on login and sync:

1. Go to console.cloud.google.com and create a project.
2. **APIs & Services → Library**: enable **Google Drive API**.
3. **OAuth consent screen**: user type External, app name Joshi's Corner, add the admin's Gmail under **Test users**.
4. **Credentials → Create credentials → OAuth client ID** → Web application → under **Authorized JavaScript origins** add `https://<your-username>.github.io`. Copy the **Client ID**.
5. **Credentials → Create credentials → API key**. Under restrictions choose **Drive API** and **Websites** `https://<your-username>.github.io/*`. Copy the key.
6. Put both values in `config.js` (`CLIENT_ID`, `API_KEY`) and commit it.
7. Open the site. The login box says first-time setup. Type a new **admin passphrase** (8+ characters) and press **Admin: sign in with Google**. The app creates an encrypted file in your Drive and shows its **File ID**. Paste it into `config.js` as `FILE_ID` and commit.
8. From then on: admin signs in with the admin passphrase plus the Google button. Press **Viewer accounts** to create staff logins (username + password).

How it behaves:
- Admin edits are saved to Drive about 2 seconds after each change. Viewers see updates within about 30 seconds.
- Viewers can only look. Only your Google account can write to the Drive file, so viewers cannot edit or delete anything even if they tamper with the page.
- The Drive file is encrypted. The passphrase and each viewer password unlock it, so a leaked link alone reveals nothing. Keep the admin passphrase safe: if it is lost, the data cannot be recovered. Use long passwords.
- Photos are not stored in the Drive file. Keep them in `assets/images/` in the repository.
- Removing a viewer blocks future logins. Use **Remove all viewers and change the access key** if someone who held access should lose it completely.

## Test locally
`python3 -m http.server` inside the folder, then open http://localhost:8000. Double-clicking `index.html` will not load the JSON.

## Publish on GitHub Pages
1. Create a repository and push these files to the `main` branch.
2. In the repository: Settings → Pages → Build and deployment → Source: "Deploy from a branch" → Branch: `main`, folder `/ (root)` → Save.
3. After a minute the site is live at `https://<username>.github.io/<repo>/`.
4. Every later commit to `recipes.json` updates the live site.

Note: a public repository makes your recipes public. Use a private repository (requires a GitHub plan that supports private Pages) or keep recipes off the public site if they are trade secrets.
