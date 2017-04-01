**Make Download work**

```bash
mkdir -p data/multi30k
wget http://www.quest.dcs.shef.ac.uk/wmt16_files_mmt/training.tar.gz &&  tar -xf training.tar.gz -C data/multi30k && rm training.tar.gz
wget http://www.quest.dcs.shef.ac.uk/wmt16_files_mmt/validation.tar.gz && tar -xf validation.tar.gz -C data/multi30k && rm validation.tar.gz
wget https://staff.fnwi.uva.nl/d.elliott/wmt16/mmt16_task1_test.tgz && tar -xf mmt16_task1_test.tgz -C data/multi30k && rm mmt16_task1_test.tgz
```

1. download from console is too slow:
`wget http://www.quest.dcs.shef.ac.uk/wmt16_files_mmt/training.tar.gz &&  tar -xf training.tar.gz -C data/multi30k && rm training.tar.gz`

2. just download with the link `http://www.quest.dcs.shef.ac.uk/wmt16_files_mmt/training.tar.gz`

3. then open the `tar.gz` file and move it to `data/multi30k/`

**Make preprocess work**

```bash
for l in en de; do for f in data/multi30k/*.$l; do if [[ "$f" != *"test"* ]]; then sed -i "$ d" $f; fi;  done; done

for l in en de; do for f in data/multi30k/*.$l; do perl tokenizer.perl -a -no-escape -l $l -q  < $f > $f.atok; done; done

python preprocess.py -train_src data/multi30k/train.en.atok -train_tgt data/multi30k/train.de.atok -valid_src data/multi30k/val.en.atok -valid_tgt data/multi30k/val.de.atok -save_data data/multi30k.atok.low -lower

```

1. the python code from console above is not working
2. it may be the wrong file path
3. the following code is working
`python preprocess.py -train_src data/multi30k/training/train.en -train_tgt data/multi30k/training/train.de -valid_src data/multi30k/validation/val.en -valid_tgt data/multi30k/validation/val.de -save_data data/multi30k.atok.low -lower`


**Shrink data during preprocess**
1. shrink dataset down to just the first 100 sentences
2. insert the following commented out codes into `preprocess.py`
```python
srcF.close()
tgtF.close()

# # -shrink
# shrink = True
# if shrink:
# 	src = src[0:100]
# 	tgt = tgt[0:100]


if opt.shuffle == 1:
    print('... shuffling sentences')
    perm = torch.randperm(len(src))
    src = [src[idx] for idx in perm]
    tgt = [tgt[idx] for idx in perm]
    sizes = [sizes[idx] for idx in perm]

```

**Make Training Work**
The original code is using gpu:

```bash
python train.py -data data/multi30k.atok.low.train.pt -save_model multi30k_model -gpus 0
```

I am using cpu here:

```bash
python train.py -data data/multi30k.atok.low.train.pt -save_model multi30k_model
```

**Make Translation Work**
The original code get file path wrong
```bash
python translate.py -gpu 0 -model multi30k_model_e13_*.pt -src data/multi30k/test.en.atok -tgt data/multi30k/test.de.atok -replace_unk -verbose -output multi30k.test.pred.atok
```

I corrected the file path:
```bash
python translate.py -model multi30k_model_acc_61.42_ppl_10.69_e8.pt -src data/multi30k/mmt16_task1_test/test.en -tgt data/multi30k/mmt16_task1_test/test.de -replace_unk -verbose -output multi30k.test.pred.atok
```

**Make Evaulation work**
The original code has file path wrong:

```bash
perl multi-bleu.perl data/multi30k/test.de.atok < multi30k.test.pred.atok
```

I have corrected it

```bash
perl multi-bleu.perl data/multi30k/mmt16_task1_test/test.de < multi30k.test.pred.atok
```
