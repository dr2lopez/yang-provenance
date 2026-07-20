# YANG Trees


```shell
$ pyang ietf-yp-provenance@2026-06-30.yang -f tree --tree-print-structures -p dependencies --yang-line-length=69
$ pyang dependencies/ietf-yp-notification@2026-05-11.yang ietf-yp-provenance@2026-06-30.yang -f tree --tree-print-structures --yang-line-length=69 -p dependencies
```

```shell
$ pyang ietf-yp-provenance@2025-05-09.yang -p dependencies --ietf -f tree --tree-print-structures --tree-line-length=69
```

# SID File Generation

Experimental version of PYANG is required for it to function properly.

```shell
$ cd sid
$ pyang --sid-generate-file 3160:20 ietf-yang-provenance@2026-06-30.yang -p ../dependencies
```

```shell
$ cd sid
$ pyang --sid-generate-file 3180:20 ietf-yp-provenance@2026-06-30.yang -p ..:../dependencies
```

```shell
$ cd sid
$ pyang --sid-generate-file 3200:20 ietf-yang-instance-data-provenance@2026-06-30.yang -p ..:../dependencies
```

```shell
$ cd sid
$ pyang --sid-generate-file 3220:20 ietf-yang-provenance-annotation@2026-06-30.yang -p ..:../dependencies
$ echo "TODO: add the annotation's SID assignment by hand, functionality missing"
```

And edit the `"description"` and `"reference"` fields.
