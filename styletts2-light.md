```console
# Prepare dependencies

git clone https://github.com/thewh1teagle/StyleTTS2-lite -b hebrew2
pip install uv
uv sync
wget https://huggingface.co/dangtr0408/StyleTTS2-lite/resolve/main/Models/base_model.pth -O ./Models/Finetune/base_model.pth
wget https://huggingface.co/dangtr0408/StyleTTS2-lite/resolve/main/Models/config.yaml -O ./Configs/config.yaml

# In config.yaml replace root_path to wav folder path
# Split val.txt and train.txt as you want, format: name.wav|phonemes

uv run python main.py

# Tensorboard
uv run tensorboard --logdir ./Models/Finetune/

# Monitor GPU
uv pip install nvitop
uv run nvitop
```
