Train piper tts 

```console
# Prepare dependencies

sudo apt-get install espeak-ng -y
git clone https://github.com/thewh1teagle/piper -b hebrew
cd piper/src/python
uv venv
uv pip install -e .
./build_monotonic_align.sh

# Prepare dataset

!wget https://huggingface.co/datasets/thewh1teagle/ILSpeech/resolve/main/ilspeech_2025_04_v1.zip
!unzip ilspeech_2025_04_v1.zip

# Prepare checkpoint
!wget https://huggingface.co/datasets/rhasspy/piper-checkpoints/resolve/main/en/en_US/ryan/medium/epoch=4641-step=3104302.ckpt
     
# Preprocess

!uv run python -m piper_train.preprocess \
    --language he \
    --input-dir ilspeech_2025_04_v1 \
    --output-dir ./train \
    --dataset-format ljspeech \
    --single-speaker \
    --sample-rate 22050 \
    --raw-phonemes

# Train
!uv run python -m piper_train \
        --dataset-dir "./train" \
        --accelerator 'gpu' \
        --devices 1 \
        --batch-size 32 \
        --validation-split 0 \
        --num-test-examples 0 \
        --max_epochs 90000 \
        --resume_from_checkpoint ./epoch=4641-step=3104302.ckpt \
        --checkpoint-epochs 1 \
        --precision 32


# infer

cat ../../etc/test_sentences/test_he.jsonl  | \
        python3 -m piper_train.infer \
            --sample-rate 22050 \
            --checkpoint ./train/lightning_logs/version_0/checkpoints/*.ckpt \
            --output-dir ./output
```
