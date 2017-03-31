# Pytorch Experiment

## Example: Open Neural Machine Translation Library in Pytorch [OpenNMT-py](https://github.com/OpenNMT/OpenNMT-py)

### Exploration

**How it preprocess data?**


**How it train?**


**How it test?**

After done preprocessing and training, use the last saved model to test:

```python translate.py -model demo_model_acc_4.97_ppl_6934.21_e13.pt -src data/src-test.txt -tgt data/tgt-test.txt -replace_unk -verbose -output demo_pred.txt```

``translate.py` -> `main()` -> create a translator ->
