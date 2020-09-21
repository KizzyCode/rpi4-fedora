
# Creating a Fedora AArch64 Image for the RPi 4

- Prerequisites:
    - A working Fedora installation

- Install `arm-image-installer`:
    ```sh
    sudo dnf install arm-image-installer
    ```

- Define the base image and download components:
  ```sh
  export RELEASE=32; export IMAGE=Fedora-Minimal-32-1.6
  wget https://download.fedoraproject.org/pub/fedora-secondary/releases/$RELEASE/Spins/aarch64/images/$IMAGE.aarch64.raw.xz
  wget https://raw.githubusercontent.com/KizzyCode/well_known_id_rsa/master/well_known_id_rsa.pub
  ```
    - See also https://github.com/KizzyCode/well_known_id_rsa for more information

- Create and attach 8 GiB working image:
    ```sh
    dd if=/dev/zero bs=1M count=8192 | speed > $IMAGE.rpi4.img
    sudo losetup -f $IMAGE.rpi4.img && losetup -l
    ```

- Create image (where `loop0` must be replaced with the real loop device from the step above):
    ```sh
    export LOOP=/dev/loop0
    sudo arm-image-installer --target=rpi4 \
        --image=$IMAGE.aarch64.raw.xz --media=$LOOP \
        --addkey=well_known_id_rsa.pub \
        --relabel --resizefs
    sudo losetup -d $LOOP
    ```

- Login as root via SSH using the [well_known_id_rsa key](https://github.com/KizzyCode/well_known_id_rsa)

- [Optional] Create an xz-compressed template from the image:
    ```sh
    speed < $IMAGE.rpi4.img | xz -ce -T 8 > $IMAGE.rpi4.img.xz
    ```
