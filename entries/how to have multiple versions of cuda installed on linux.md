---
title: how to have multiple versions of cuda installed on linux
permalink: how-to-have-multiple-versions-of-cuda-installed-on-linux
date: 2025-06-13T20:44:59-07:00
tags: [cuda]
---

I originally followed the tutorial here
"https://notesbyair.github.io/blog/cs/2020-05-26-installing-multiple-versions-of-cuda-cudnn/"
but that disappeared so I'm writing my own.

- Archive link is here:
  https://web.archive.org/web/20220129002741/https://notesbyair.github.io/blog/cs/2020-05-26-installing-multiple-versions-of-cuda-cudnn/

Differences:

- we're installing cuda more than cudnn since I only run ML workflows
- the `_switch_cuda` by itself will list versions installed, and the currently
  active one. Previously it would be easy to forget which versions were
  installed.

I'm running on Linux Mint so I'm used to the apt world. Unix wizards need not
apply.

## Steps

### One-time setup

1. Add the following to your `~/.bashrc` or `~/.zshrc`

```bash
function _switch_cuda {
   if [ $# -eq 0 ]; then
       echo "Available CUDA versions:"
       find /usr/local -maxdepth 1 -type d -name "cuda-*" | while read cuda_dir; do
           echo "  ${cuda_dir##*/}"
       done
       echo -e "\nCurrently active CUDA version:"
       if command -v nvcc >/dev/null 2>&1; then
           nvcc --version | grep "release" | awk '{print $5}'
       else
           echo "  No CUDA version currently active (nvcc not found)"
       fi
       return
   fi

   v=$1
   if [ ! -d "/usr/local/cuda-$v" ]; then
       echo "Error: CUDA version $v not found in /usr/local/cuda-$v. Syntax: _switch_cuda MAJOR.MINOR e.g. _switch_cuda 12.1"
       return 1
   fi

   export PATH=$PATH:/usr/local/cuda-$v/bin
   export CUDADIR=/usr/local/cuda-$v
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-$v/lib64

   if command -v nvcc >/dev/null 2>&1; then
       nvcc --version
   else
       echo "Error: nvcc not found after switching to CUDA $v"
       return 1
   fi
}
```

### For each CUDA install...

1. Download the right runfile from
   [Nvidia's CUDA Toolkit Archive](https://developer.nvidia.com/cuda-toolkit-archive)
2. Select the right architecture for your computer. _Make sure to choose the
   runfile and not the .deb._
   - For me, I did Linux > x86_64 > Ubuntu > 22.04 > runfile (local)
3. Use the provided wget command to download the runfile
   - e.g.
     `wget https://developer.download.nvidia.com/compute/cuda/12.1.1/local_installers/cuda_12.1.1_530.30.02_linux.run`
4. Install the cuda
   - `sudo sh cuda_12.1.1_530.30.02_linux.run --silent --toolkit --toolkitpath=/usr/local/cuda-12.1`

Done!

Then before you need to use CUDA, or after you enter your python venv you can
just `_switch_cuda 12.1`.

Alternatively, after you have the runfile URL, you can just paste it in this
generator instead of editing the commands yourself.

<iframe
  src="https://kevinjiang.ca/install-cuda-script/"
  width="500"
  height="600"
  frameborder="0"
  style="border: 1px solid #ddd; border-radius: 8px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); display: block; margin: 20px auto;"
  title="CUDA Installation Command Generator"
></iframe>
