# Dreambooth on Stable Diffusion (with some customizations)

If you just installed conda:

`% conda init bash`

Create the conda environment:

`% conda env create -f environment.yaml`

Activate:

`% conda activate ldm`

1. Move your `model.ckpt` into the models directory.
1. Put your training data into `inputs/training`.
1. Put your regularization data into `inputs/regularization`.

Now run:

```
python main.py --base configs/stable-diffusion/v1-finetune_unfrozen.yaml \
                -t \
                --actual_resume models/model.ckpt \
                -n DreamBoothFineTune \
                --gpus 0, \
                --data_root inputs/training \
                --reg_data_root inputs/regularization \
                --identifier unique_word_for_your_custom_subject \
                --class_word the_class_of_your_custom_subject
```

Prune the checkpoints:

`% python scripts/prune.py --input path/to/model.ckpt`

Turn the `ckpt` file into the diffusers file format:

```
% python .scripts/convert_original_stable_diffusion_to_diffusers.py --checkpoint_path /path/to/model.ckpt \
                                                                    --dump_path /output/path
```
