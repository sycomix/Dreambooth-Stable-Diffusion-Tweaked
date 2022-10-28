# DreamBooth on Stable Diffusion (with some customizations)

This is a fork of the [DreamBooth repo](https://github.com/XavierXiao/Dreambooth-Stable-Diffusion) with some small changes to make easier to use:

- Fixed a couple of runtime errors I was getting.
- Enabled passing the identifier (the unique prompt word for your subject) in as an argument so you don't have to modify Python code.
- Created a script called `generate_regularization.py` specifically for making regularization images (see docs below).
- Added utilities for pruning the `ckpt` file and for turning a `ckpt` file into the [Hugging Face diffusers](https://huggingface.co/docs/diffusers/index) file format.
- Tweaked the conda env slightly to accommodate the utility scripts.
- Assumed some defaults in the suggested commands below to make setup easier (things like the location of the `model.ckpt` file, regularization images, and training images).
- Rewrote the documentation to hopefully make it more widely accessible.

Some people report being able to get this to run on a 3090. I haven't been able to. I've been renting A100s from [vast.ai](https://vast.ai/) (no affiliation), and I'm able to do a full fine-tune in about 15 minutes. If you use Vast, be sure to bump up your disk space way past the default 10GB. That's not nearly enough. I use 100GB so I never have to think about it.

**Note that the workflow described below for generating regularization images can be done on a 3090, but the fine-tuning itself cannot.**

If you just installed conda (or you're running on a fresh VM):

`% conda init bash`

`% bash`

(If your shell isn't bash, substitute the correct shell command.)

Create the conda environment:

`% conda env create -f environment.yaml`

If you need to update your conda environment for any reason, use this command:

`% conda env update -f environment.yaml`

If something went wrong and you want to delete the environment and start again:

`% conda env remove -n dream-booth`

Activate:

`% conda activate dream-booth`

Next, move your `model.ckpt` file into the models directory. This is likely a file downloaded [from Hugging Face](https://huggingface.co/CompVis/stable-diffusion). Make sure to rename the file to `model.ckpt` (that simplifies the commands below).

## Generating Regularization Images

If you want to generate regularization images, you can easily do so (even on a 3090) with the following command:

`% python scripts/generate_regularization.py --ckpt models/model.ckpt --prompt "a photo of a dog"`

Your 200 images will be placed in the directory `inputs/regularization` where they need to be for the rest of the process to work.

## Fine-Tuning

Once your regularization images are in place, put your training data into `inputs/training`. Training data should be between 5 - 20 512x512 PNGs of the subject you want to train on.

Next, copy and paste the command below, but make the appropriate changes to `--identifier` and `--class_word`:

- `--identifier`: The unique word that you want to use to reference your subject in your prompts. It should be something unlikely to already be in the training data.
- `--class_word`: The class of what you're training on. For example: man, woman, person, dog, etc.

```
python main.py --ckpt models/model.ckpt --name DreamBoothFineTune --gpus 0, --data_root inputs/training --reg_data_root inputs/regularization --identifier UNIQUE_IDENTIFIER --class_word SUBJECT_CLASS
```

(Optional) When you're done, you can prune the checkpoints to create a smaller `ckpt` file ([credit](https://github.com/huggingface/diffusers/blob/main/scripts/convert_original_stable_diffusion_to_diffusers.py)):

`% python scripts/prune.py --input path/to/model.ckpt`

(Optional) If you did the training on a remote machine, you can copy the ckpt file to your local machine with a command that looks something like this (replace the port number, IP address, and paths):

```
% scp -P PORT_NUM root@123.456.789.000:/root/Dreambooth-Stable-Diffusion-Tweaked/logs/training2022-10-25T21-25-03_DreamBoothFineTune/checkpoints/last.ckpt .
```

(Optional) Turn the `ckpt` file into the diffusers file format ([credit](https://github.com/harubaru/waifu-diffusion/blob/main/scripts/prune.py)) compatible with the [Stable Diffusion Photoshop REST API Server](https://github.com/cantrell/stable-diffusion-api-server):

```
% python scripts/convert_original_stable_diffusion_to_diffusers.py --checkpoint_path /path/to/model.ckpt --dump_path /output/path
```
