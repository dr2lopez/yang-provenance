# YANG Trees


```shell
$ pyang ietf-notification-provenance@2024-02-28.yang -f tree --tree-print-structures -p dependencies
$ pyang dependencies/ietf-notification@2024-01-22.yang ietf-notification-provenance@2024-02-28.yang -f tree --tree-print-structures
```

```shell
$ pyang ietf-notification-provenance@2025-05-09.yang -p dependencies --ietf -f tree --tree-print-structures --tree-line-length=69
```