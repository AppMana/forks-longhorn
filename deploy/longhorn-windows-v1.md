# Windows Server V1 data path

This experimental public-fork add-on is not an upstream-supported Longhorn
configuration. It keeps the normal Linux Longhorn chart and adds Windows Server
2022/2025 HostProcess managers, engine-image staging, instance managers, and CSI
node services. All engine and replica processes retain Longhorn's ordinary
per-volume lifecycle and distinct upgrade process/port allocation.

Install the chart and add-on with the same coordinated release-set tag:

```sh
helm upgrade --install longhorn ./chart \
  --namespace longhorn-system --create-namespace \
  --values deploy/longhorn-windows-v1-values.yaml
kubectl apply -f deploy/longhorn-windows-v1.yaml
```

Apply the values file on the first chart installation. It pins stock workloads
and transient discovery pods to Linux before the driver deployer starts; the
add-on's Windows-aware controllers create their Windows peers separately.

Windows nodes must be Server editions with HostProcess support, a running
`MSiSCSI` service, and a writable `C:\var\lib\longhorn`. The supplied test
harness provisions these requirements. `longhorn-windows-ntfs` and
`longhorn-windows-refs` use delayed binding and advertise Windows topology.

The Windows V1 capability set is intentionally fail-closed. RWO/RWOP volumes
may use mixed Linux/Windows replicas. RWX and strict-local requirements cannot
place replicas on Windows until the exact engine image advertises those role
capabilities. ReFS is exposed for workloads and remains a known expected-fail
test until its CSI formatting path is complete.
