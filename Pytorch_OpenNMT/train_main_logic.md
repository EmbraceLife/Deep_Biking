`python train.py -data data/demo.train.pt -save_model demo_model`

`train.py` --> `main()`

1. print out `opt` automatically

2. print: load data from `opt.data` (a file name)

3. load data from a file into variable `dataset` (a large dict)

4. see whether to train any pre-trained models: get file name from `opt.train_from` or `opt.train_from_state_dict` (which are file names) to `dict_checkpoint`

5. If `dict_checkpoint` is available:
    - print: loading model as dict from ...
    - load the dict from a file name into `checkpoint`
    - give `checkpoint['dicts']` to `dataset['dicts']`, which is vocab (object with funcs and dicts)

6. create `trainData` Dataset using `onmt.Dataset()` `dataset['train']['src']`, `dataset['train']['tgt']`, `opt.batch_size`, `opt.gpus`: trainData is just a container for:
    - train src data
    - train tgt data
    - batch_size
    - num of batches
    - cpu or gpu

7. create `validData` the same way, but `volatile` set `True`

8. give `dataset['dicts']` to `dicts`

9. print out: num of vocab in source file and num of vocab in target file
10. print out: num of training file sentences, and batch_size

11. start build model

12. build `encoder` layer using `dicts['src']` and `onmt.Models.Encoder()`

13. build `decoder` layer using `dicts['tgt']` and `onmt.Models.Decoder()`

14. build `generator` layer using `nn.Sequential()` `nn.Linear()`, `nn.LogSoftmax`, `opt.rnn_size`, `dicts['tgt']`

15. build `model` using `onmt.Models.NMTModel()`, `encoder` and `decoder`

16. if to train from a pre-trained model:
      - print where the model is stored
      - get the model from `checkpoint['model']` as `chk_model`
      - get a dict with bias and weights as keys, from `chk_model.generator.state_dict()` to `generator_state_dict`
      - get `model_state_dict` the same way from `chk_model.state_dict()`
      - load `model_state_dict` into `model`
      - load `generator_state_dict` into `generator`
      - get `opt.start_epoch`


17. if to train from pre-saved state dictionary:
      - print out where the dictionary stored
      - load `checkpoint['model']` to `model`
      - load `checkpoint['generator']` to `generator`
      - get `opt.start_epoch`

18. select gpu or cpu, set `model` and `generator` accordingly

19. assign `generator` to `model.generator`

20. If not to train from any pre-trained model:
      - get model parameters from `model.parameters()`, for each of them:
        - make each of them within value (-0.1 and 0.1)
        - (it is like initialization of weights)

      - create an Optim object as `optim` using `onmt.Optim()`: which has lots of attributes defined inside:
          - opt.optim,
          - learning_rate,
          - max_grad_norm...

     Else: to train from pre-trained model:
       - print
       - get `optim` from `checkpoint['optim']`
       - print `optim`


21. update `optim.params` and `optim.optimizer` using `optim.set_parameters()` and `model.paramters()`

22. If pre-trained model is available:
      - update optimizer by `optim.optimizer.load_state_dict()` and `checkpoint['optim'].optimizer.state_dict`

23. get the total number of parameters

24. print out the total number

25. train model: explore on next step
