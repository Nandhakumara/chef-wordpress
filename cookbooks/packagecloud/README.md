# packagecloud cookbook

This cookbook provides an LWRP for installing https://packagecloud.io repositories.

## Usage

Be sure to depend on `packagecloud` in `metadata.rb` so that the packagecloud
resource will be loaded.

For public repos:

```ruby
packagecloud_repo "computology/packagecloud-cookbook-test-public" do
  type "deb"
end
```

For private repos, you need to supply a `master_token`:

```ruby
packagecloud_repo "computology/packagecloud-cookbook-test-private" do
  type "deb"
  master_token "762748f7ae0bfdb086dd539575bdc8cffdca78c6a9af0db9"
end
```

For packagecloud:enterprise users:
Override these attributes:
```
  "packagecloud": {
    "base_url": "https://packagecloud.local:9192"
  }
```

Valid options for `type` include `deb`, `rpm`, and `gem`.

## Interactions with other cookbooks

On CentOS 5, the official chef yum cookbook overwrites the file
`/etc/yum.conf` setting some default values. When it does this, the `cachedir`
value is changed from the CentOS5 default to the default value in the
cookbook. The result of this change is that any packagecloud repository
installed *before* a repository installed with the yum cookbook will appear as
though it's gpg keys were not imported.

There are a few potential workarounds for this:

- Pass the "-y" flag to package resource using the `options` attribute. This
  should cause yum to import the GPG key automatically if it was not imported
  already.
- Move your packagecloud repos so that they are installed last, after any/all
  repos installed via the yum cookbook.
- Set the cachedir option in the chef yum cookbook to the system default value
  of `/var/cache/yum` using the `yum_globalconfig` resource.

CentOS 6 and 7 are not affected as the default `cachedir` value provided by
the yum chef cookbook is set to the system default, unless you use the
`yum_globalconfig` resource to set a custom cachedir. If you do set a custom
`cachedir`, you should make sure to setup packagecloud repos after that
resource is set so that the GPG keys end up in the right place.

## Credits
Computology, LLC.
