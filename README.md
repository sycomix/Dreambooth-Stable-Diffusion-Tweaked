# Dreambooth on Stable Diffusion (with some customizations)

If you just installed conda (or you're running on a fresh VM):

`% conda init bash`
`% bash`

(If your shell isn't bash, substitute the correct shell command.)

Create the conda environment:

`% conda env create -f environment.yaml`

Activate:

`% conda activate dream`

Then...

1. Move your `model.ckpt` into the models directory.
1. Put your training data into `inputs/training`.
1. Put your regularization data into `inputs/regularization`.

Copy and paste the command below, but make the appropriate changes to `--identifier` and `--class_word`:

```
python main.py --base configs/stable-diffusion/v1-finetune_unfrozen.yaml -t --actual_resume models/model.ckpt -n DreamBoothFineTune --gpus 0, --data_root inputs/training --reg_data_root inputs/regularization --identifier unique_word_for_your_custom_subject --class_word the_class_of_your_custom_subject
```

(Optional) Prune the checkpoints ([credit](https://github.com/huggingface/diffusers/blob/main/scripts/convert_original_stable_diffusion_to_diffusers.py)):

`% python scripts/prune.py --input path/to/model.ckpt`

(Optional) If you did the training on a remote machine, you can copy the ckpt file to your local machine with a command that looks something like this:

```
% scp -P PORT_NUM root@remote.machine.ip.address:/root/Dreambooth-Stable-Diffusion-Tweaked/logs/training2022-10-25T21-25-03_DreamBoothFineTune/checkpoints/last.ckpt .
```

(Optional) Turn the `ckpt` file into the diffusers file format ([credit](https://github.com/harubaru/waifu-diffusion/blob/main/scripts/prune.py)):

```
% python .scripts/convert_original_stable_diffusion_to_diffusers.py --checkpoint_path /path/to/model.ckpt --dump_path /output/path
```
