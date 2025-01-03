# Folders containing generated index.html files that are ready to be copied
# as-is into /docs
INDEX_DIRS = \
  . \
  archive \
  about \
  contact \
  all \
  catalog \
  series/* \
  tag/* \
  art/* \
  meta

DOCROOT = ../docs
IMG = img
CSS = css

.PHONY: build
build:
	mypy --strict build
	./build

.PHONY: rebuild
rebuild:
	mypy --strict build
	./build --force

.PHONY: redirect
redirect:
	./redirect $(DOCROOT) catalogue /catalog
	./redirect $(DOCROOT) cat /catalog

.PHONY: all
all:
	$(MAKE) build
	$(MAKE) redirect
	$(MAKE) local
	git status

# Make QR codes for all images, linking to the catalog page.
#
# Requires 'qr':
#   pip install image qrcode
#
QR_DIR = artdb/labels/qr
.PHONY: qr
qr:
	mkdir -p $(QR_DIR)
	for x in img/original/img-*.jpg; do \
	  id=$$(basename "$$x" .jpg); \
          url="https://martinj.art/catalog/#$$id"; \
	  echo "encoding $$url"; \
	  qr "$$url" --output=$(QR_DIR)/qr-code-"$$id".png; \
	done

LOCAL_PORT = 8080
LOCAL_URL = http://0.0.0.0:$(LOCAL_PORT)/

.PHONY: live
live:
	xdg-open $(LOCAL_URL)
	python3 -m http.server --directory ../docs $(LOCAL_PORT)

# Check for broken links on the local HTTP server. This takes 20 s
# because it downloads images.
# Requires 'make live'.
.PHONY: links
links:
	./scripts/check-links $(LOCAL_URL)

# Copy all website files to /docs
.PHONY: local
local:
	mkdir -p $(DOCROOT)
	cp -u root/robots.txt $(DOCROOT)/
	cp -u favicon/* $(DOCROOT)/
	for dir in $(INDEX_DIRS); do \
	  mkdir -p $(DOCROOT)/"$$dir"; \
	  cp -u "$$dir"/index.html $(DOCROOT)/"$$dir"/; \
	done
	for x in $(IMG); do \
	  mkdir -p $(DOCROOT)/$$x; \
	  cp -r -u $$x/* $(DOCROOT)/$$x/; \
	done
	for x in $(CSS); do \
	  mkdir -p $(DOCROOT)/$$x; \
	  cp -r -u $$x/* $(DOCROOT)/$$x/; \
	done

# Publish all changes
# TODO: check links in CI rather than locally?
.PHONY: push
push:
	-git -C artdb add gallery-photos
	-git -C artdb commit -a -m 'Update data'
	-git -C artdb push origin main
	git add ..
	-git commit -a -m 'Publish site'
	-git push origin main
	$(MAKE) links
