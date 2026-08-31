# Linux CIFS Hang On Disconnect: Reproduce

The VMs are configured to look for their images in the default pool of
`/var/lib/libvirt/images/`. The server and client images for the configuration
you want to test have to be copied there.

The actual steps to reproduce the hang are described in `playbook.yml`. Most of
the variables were configured in Step 1. However, the `repro_output` variable
must also be configured. That's the directory where the collected logs will be
dumped.

_Action Items_:

- Make sure the VMs and the network are stopped with

  ```sh
  sudo virsh destroy linux-cifs-server || true
  sudo virsh destroy linux-cifs-client || true
  sudo virsh net-destroy linux-cifs || true
  ```

- Copy the server and client images from Step 4 to `/var/lib/libvirt/images/`.
  For example

  ```sh
  sudo cp -v results/rxe/bad/qcow/linux-cifs-server.qcow2 /var/lib/libvirt/images/
  sudo cp -v results/rxe/bad/qcow/linux-cifs-client.qcow2 /var/lib/libvirt/images/
  ```

- Start the network and the VMs with

  ```sh
  sudo virsh net-start linux-cifs
  sudo virsh start linux-cifs-server
  sudo virsh start linux-cifs-client
  ```

- Run the playbook. For example

  ```sh
  ansible-playbook repro/playbook.yaml -i repro/inventory.yaml \
    -e @variables.json -e @password.json \
    -e "repro_output=${PWD}/results/rxe/bad/logs/"
  ```
