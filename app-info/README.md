## Mechanism by which apps appear in the ‘Featured’ tab in App Center

Firstly, read [the upstream documentation on how Featured apps and Editor’s
Choice work](https://gitlab.gnome.org/GNOME/gnome-software/-/blob/main/doc/vendor-customisation.md#user-content-featured-apps-and-editors-choice)
in GNOME Software.

The Endless-specific infrastructure is:

* Our external-appstream is currently a file named `org.gnome.Software-eos-extra.xml.gz` and can be found in `/var/cache/app-info/xmls/`. The contents of this file are [here](./eos-extra.xml).
* The content of `eos-extra.xml` are published to an [S3 URL](https://d3lapyynmdp1i9.cloudfront.net/app-info/eos-extra.xml.gz) via [this job](https://ci.endlessm-sf.com/job/gnome-software-data/).
* The S3 URL [is preset](https://github.com/endlessm/eos-theme/blob/9bd2312ad7650654c09f4267759ea6217a8d9d40/settings/com.endlessm.settings.gschema.override.in#L121) as the `external-appstream-urls` GSetting for `org.gnome.software` when images are built. The external-appstream plugin of gnome-software fetches the file at this S3 URL and places it at `/var/cache/app-info/xmls/org.gnome.Software-eos-extra.xml.gz` on disk.
* We mention `<bundle type="flatpak"/>` in the `eos-extra.xml` so that the wildcard apps created by the external-appstream file can be adopted by the flatpak plugin. See `gs_plugin_adopt_app()`.

GNOME Software picks up the `eos-extra.xml` file as per its `external-appstream-urls`
configuration, using the upstream functionality.

## How to add a Flathub app (or non-com.endless* app) in the ‘Featured’ category?

* Add the app to [eos-extra.txt](./eos-extra.txt)
* Run [generate-eos-extra](./generate-eos-extra)
* Commit the result and submit a pull request
