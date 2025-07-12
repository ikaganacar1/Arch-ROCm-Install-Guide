# Arch ROCm Installation Guide (with PyEnv & Conda Options)

This guide provides instructions for installing ROCm on Arch-based systems, offering options for managing Python environments with both **PyEnv** and **Conda**.

**Verified working On my RX 6700 XT (pyenv) RX 580 (conda)**

---

## System Preparation

1.  **Update your system:**

    ```bash
    sudo pacman -Syu
    ```

2.  **Install an AUR helper (yay recommended):**

    ```bash
    sudo pacman -S --needed git base-devel && git clone [https://aur.archlinux.org/yay.git](https://aur.archlinux.org/yay.git) && cd yay && makepkg -si
    ```

3.  **Install prerequisites:**

    ```bash
    yay -S wget make curl gperftools
    ```

---

## Python Environment Setup (Choose One: PyEnv or Conda)

### Option 1: PyEnv (Recommended for isolated Python versions)

4.  **Install PyEnv:**

    ```bash
    curl [https://pyenv.run](https://pyenv.run) | bash
    ```

5.  **Add PyEnv to your shell configuration (.bashrc):**

    ```bash
    nano ~/.bashrc
    ```

    Add these lines to the bottom of the file:

    ```bash
    export PATH="$HOME/.pyenv/bin:$PATH"
    eval "$(pyenv init --path)"
    eval "$(pyenv virtualenv-init -)"
    ```

    * Press `Ctrl+O` then `ENTER` to save changes.
    * Press `Ctrl+X` to exit.

6.  **Refresh your shell:**

    ```bash
    exec $SHELL
    ```

7.  **Verify PyEnv installation:**

    ```bash
    pyenv
    ```

    If it prints a list of commands, it's installed correctly.

8.  **Install Python 3.10.13:**

    ```bash
    pyenv install 3.10.13
    ```

9.  **Set global Python version:**

    ```bash
    pyenv global 3.10.13
    ```

10. **Confirm Python version:**

    ```bash
    python --version
    ```

    This command should return `Python 3.10.13`.

### Option 2: Conda (Recommended for complex data science environments)

4.  **Install Miniconda (or Anaconda if preferred):**

    ```bash
    mkdir -p ~/miniconda3
    wget [https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh](https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh) -O ~/miniconda3/miniconda.sh
    bash ~/miniconda3/miniconda.sh -b -p ~/miniconda3
    rm ~/miniconda3/miniconda.sh
    ```

5.  **Initialize Conda and add to your shell configuration (.bashrc):**

    ```bash
    ~/miniconda3/bin/conda init bash
    nano ~/.bashrc
    ```

    Ensure that the `conda initialize` block has been added to the end of your `.bashrc` file. If not, you might need to manually add it or re-run `~/miniconda3/bin/conda init bash`.

    * Press `Ctrl+O` then `ENTER` to save changes.
    * Press `Ctrl+X` to exit.

6.  **Refresh your shell:**

    ```bash
    exec $SHELL
    ```

7.  **Verify Conda installation:**

    ```bash
    conda --version
    ```

    This should display the Conda version.

8.  **Create and activate a new Conda environment for ROCm:**

    ```bash
    conda create -n rocm_env python=3.10.13 -y
    conda activate rocm_env
    ```

9.  **Confirm Python version within the Conda environment:**

    ```bash
    python --version
    ```

    This command should return `Python 3.10.13`. To deactivate the environment later, use `conda deactivate`.

---

## ROCm Installation

11. **Install ROCm SDKs:**

    ```bash
    yay -S rocm-hip-sdk rocm-opencl-sdk
    ```

12. **Add yourself to ROCm groups:**

    Replace `username` with your actual username. If unsure, run `whoami`.

    ```bash
    sudo gpasswd -a username render
    sudo gpasswd -a username video
    ```

13. **Add ROCm environment variables to your .bashrc:**

    ```bash
    nano ~/.bashrc
    ```

    Add these lines to the bottom of your file. If you have an **RX 7000 series card, change `10.3.0` to `11.0.0`**.

    ```bash
    export ROCM_PATH=/opt/rocm
    export HSA_OVERRIDE_GFX_VERSION=10.3.0
    ```

    * Press `Ctrl+O` then `ENTER` to save changes.
    * Press `Ctrl+X` to exit.

---

## Final Steps

14. **Reboot your system:**

    ```bash
    sudo reboot
    ```

15. **Verify ROCm installation after reboot:**

    ```bash
    rocminfo
    ```

    If it returns a wall of information and specs about your GPU, ROCm is installed and working correctly! 🎉

Congratulations! You've successfully installed ROCm on your AMD GPU.

---

## Extra Tips
1. For old devices like RX 580 you should install last known supported versions of ROCm and Torch for example i installed ROCm 5.4.2 and Torch 2.0.1
  * `pip install torch==2.0.1+rocm5.4.2 torchvision==0.15.2+rocm5.4.2 \
  --index-url https://download.pytorch.org/whl/rocm5.4.2`
2. `ImportError: libhiprtc.so` [this can be changed]: cannot enable executable stack as shared object requires: Invalid argument
   * `sudo pacman -S patchelf`
   * `find ~/.conda/envs/py311 -name libhiprtc.so`
   * `sudo patchelf --clear-execstack ~/.conda/envs/py311/lib/python3.11/site-packages/torch/lib/libhiprtc.so`
3. To test everything works `python -c "import torch; print(torch.version.hip, torch.cuda.is_available(), torch.cuda.get_device_name(0))"`






