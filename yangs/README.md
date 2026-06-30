# YANG Trees


```shell
$ pyang ietf-yp-provenance@2026-06-30.yang -f tree --tree-print-structures -p dependencies --yang-line-length=69
$ pyang dependencies/ietf-yp-notification@2026-05-11.yang ietf-yp-provenance@2026-06-30.yang -f tree --tree-print-structures --yang-line-length=69 -p dependencies
```

```shell
$ pyang ietf-yp-provenance@2025-05-09.yang -p dependencies --ietf -f tree --tree-print-structures --tree-line-length=69
```