# Miniflux Reader

This is a differently opinionated reader frontend
for the Miniflux RSS engine.


## Opinionated?

As opposed to the mobile-first(?) stock UI of Miniflux,
Reader is desktop-first and very possibly desktop-exclusive.

* It gives 25% of your browser width to the sidebar
  which displays your categories and feeds tree structure.
  (That tree shows you
  which feeds and categories the current unread entries are in.
  Also, it is the interface to controlling subscriptions.

* In the remaining 75%,
  it presents you with all your unread feed entries,
  one by one, in a long scrollable list,
  assuming you want to read them all in sequence.
  (You can of course stop at any moment,
  it won’t mark them all read at once.)

* It puts important reading commands on keyboard shortcuts
  and right-click context menus.

* It has not been tested in any browser except Firefox.


## Installation

Assuming you host your own instance of Miniflux,
you probably keep it behind a reverse proxy
such as nginx.
Probably on the root of a virtual server, too.
So put `index.html` from this project
into a subdirectory
(e.g. `/srv/www/miniflux/reader/index.html`)
and expose it in a separate location:

```
server {
  listen 443 ssl;
  server_name miniflux.mydomain.example;
  ssl_certificate […];
  ssl_certificate_key […];

  error_log /var/log/nginx/miniflux/error.log;
  access_log /var/log/nginx/miniflux/access.log;

  location /reader {
    root /srv/www/miniflux;
  }

  location / {
    proxy_pass http://127.0.0.1:8080;
    proxy_redirect off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```

**Note the SSL!**
Unless you only access Reader on `localhost`,
your credentials will travel over the network
with each API request.
If you use unsecured HTTP,
anyone who can listen to transit traffic
will be able to intercept them.

By default, Reader will talk to the Miniflux API
thinking it’s at the root of the server.
If you keep Miniflux under a prefix,
find the function `myFetch`
and change the first argument of `fetch`
from `uri` to e.g. `` `/miniflux${uri}` ``.

(You could also put Reader and Miniflux on different servers,
but then you’d have to set up CORS headers
the recipe for which this README is too small to contain.)


## Usage

### Logging in and out

Point your browser to `https://miniflux.mydomain.example/reader/`.
On first visit,
Reader will ask you to log in.
If you check the “Remember me” checkbox,
it will save your credentials in your browser’s local storage
(which is like cookies but different).
The logout button is in the top right corner.

### Filtering

By default, Reader will show you unread entries in all feeds.
You can switch to All entries using the switch in the topbar.
You can also narrow to a single category or feed.
Watch the address bar as it changes.
You can bookmark any filter combination.

### Scrolling and marking

Reader implements smart page scrolling.
`Page Down`, `Space`, and `n` all scroll down
to the next entry or a little less than one pageful,
whichever is smaller.
`Page Up`, `Shift`+`Space`, and `p` scroll up
by the same logic.

As you scroll the entries,
those that scroll off the top will be marked read.
You can scroll back and press `M` (that’s capital em!)
to keep them unread.
`m` marks an entry read,
`M` marks it unread,
and `x` marks it removed.
All three act on the entry straddling the top edge of the window.

See other key shortcuts in the help screen,
displayed with `?`.

### Feed management

* To add a feed, right-click a category and select “Add feed”.

* To modify a feed, right-click it and select “Edit”.

* To unsubscribe, right-click a feed and select “Unsubscribe”.

* To move a feed to a different category, use drag and drop.

### Category management

* To add a category, right-click “All feeds”
  and select “Create category”.

* To rename a category, right-click it and select “Rename”.

* To unsubscribe from all feeds in a category and delete it,
  right-click it and select “Delete”.

### OPML import and export

Not implemented yet.
Meanwhile, you can always access them in the stock UI.
