# Lumen/* Service worker: кэширует приложение, чтобы оно открывалось без интернета. */
const CACHE = 'lumen-english-v1';
const FILES = ['./', './index.html', './manifest.json', './icon-192.png', './icon-512.png'];

self.addEventListener('install', event => {
  event.waitUntil(caches.open(CACHE).then(c => c.addAll(FILES)).then(() => self.skipWaiting()));
});

self.addEventListener('activate', event => {
  event.waitUntil(
    caches.keys()
      .then(keys => Promise.all(keys.filter(k => k !== CACHE).map(k => caches.delete(k))))
      .then(() => self.clients.claim())
  );
});

self.addEventListener('fetch', event => {
  if (event.request.method !== 'GET') return;
  event.respondWith(
    caches.match(event.request).then(hit => hit ||
      fetch(event.request).then(res => {
        const copy = res.clone();
        caches.open(CACHE).then(c => c.put(event.request, copy));
        return res;
      }).catch(() => caches.match('./index.html'))
    )
  );
});
<img width="512" height="512" alt="image" src="https://github.com/user-attachments/assets/b6d8f4eb-3615-4789-b24a-0f8cb22ab9c5" />
