# 🧺 Smart Grocery Basket

A simple, serverless web app to monitor how much of each grocery item is left at home. When something runs low, its card turns red and blinks, so nobody has to call or text "we're out of potatoes".

🔗 **Live demo:** https://asifur-rahman-ai.github.io/smart-grocery-basket/

**Phase 1 (this repo):** web app with manual weight control.
**Phase 2 (planned):** ESP32 + HX711 + load cell sends live weight to the app.

## Features

- **Dashboard** with one card per item: emoji, name, current / max weight, and a progress bar with an alert-level marker
- **Stock status**: green `✅ Stock is fine`, or red blinking `⚠️ Low stock! Need to buy` when weight drops below the alert level
- **Add Item modal** with name suggestions (`<datalist>`), but any custom item name is allowed
- **Custom emoji** per item; auto-picked if left empty (📦 for unknown items)
- **Manual control**: ➕ / ➖ buttons (±0.5 kg per tap) and ✖ to delete
- **LocalStorage** persistence, so data survives page refresh
- **Demo simulation** switch: weights drop slightly every 5 seconds
- Responsive layout for mobile, tablet and desktop
- Names accept English and Bengali (e.g. `Potato`, `aloo`, `আলু`) for emoji matching

## Tech

HTML, CSS, vanilla JavaScript and [Bootstrap 5](https://getbootstrap.com/) (via CDN). Font: Hind Siliguri. No build step, no backend, no Node.js.

## Run locally

Download `index.html` and double-click it. An internet connection is needed once to load Bootstrap and the font from their CDNs.

## Deploy on GitHub Pages

1. Push `index.html` (and this README) to the root of a repository, e.g. `smart-grocery-basket`
2. Go to **Settings → Pages**
3. Under **Build and deployment**, choose **Deploy from a branch**, branch `main`, folder `/ (root)`
4. After a minute the app is live at `https://asifur-rahman-ai.github.io/smart-grocery-basket/`

## Data & privacy

All data is stored only in the visitor's own browser (LocalStorage). Nothing is sent to any server, there is no database, and no company information is included. Each person who opens the link sees only their own data.

> Because of this, data is **not shared between devices**. A phone and a laptop each keep their own list. Sharing data across devices needs a backend, which is outside Phase 1.

## Hardware integration (Phase 2)

The app is structured to receive weights from an ESP32 + HX711 + load cell setup.

### Option A: polling with Fetch

In `index.html`, edit the `HARDWARE` config:

```js
const HARDWARE = {
  enabled: true,
  mode: 'fetch',
  url: 'https://your-device/weights',
  intervalMs: 5000
};
```

The device should return JSON like this:

```json
[
  { "id": "potato", "weight": 3.2 },
  { "name": "Rice", "weight": 7.5 }
]
```

### Option B: WebSocket

```js
const HARDWARE = { enabled: true, mode: 'websocket', wsUrl: 'ws://192.168.0.50:81' };
```

Each message should be the same JSON shape as above.

### Option C: call the API directly

```js
GroceryBasket.updateWeight('Potato', 3.2);              // by id or name
GroceryBasket.updateMany([{ name: 'Rice', weight: 7.5 }]);
GroceryBasket.getItems();                               // read current items
```

Items are matched by `id` or `name` (case-insensitive). Weights are clamped between 0 and the item's capacity.

> **Note:** a page served over `https` (GitHub Pages) cannot fetch a plain `http://` device because browsers block mixed content. Use HTTPS on the device, or open the file locally while testing.

## Roadmap

- [x] Dashboard, add/delete items, manual +/- control, LocalStorage
- [x] Hardware-ready code structure (Fetch / WebSocket / JS API)
- [ ] ESP32 + HX711 firmware and wiring guide
- [ ] Optional shared backend so family members can see the same data on different devices
- [ ] Low-stock notifications

## Author

Md Asifur Rahman ([@asifur-rahman-ai](https://github.com/asifur-rahman-ai))

## License

MIT (add a `LICENSE` file if you want to publish under this license).
