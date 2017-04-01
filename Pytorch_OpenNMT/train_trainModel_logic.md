**run in console**

`python train.py -data data/demo.train.pt -save_model demo_model`


**From `train.main()` --> `train.trainModel()`**

`trainModel(model, trainData, validData, dataset, optim)`

1. from `train.main()`, executing `trainModel(model, trainData, validData, dataset, optim)`, going inside `train.trainModel()`

2. print out the model

3. `model.train()`: set the model in the training mode

4. calc `nn.NLLLoss(weight, size_average=False)` for gpu or cpu, as `criterion`

5. start timing

6. create a function as `trainEpoch`:
    - it is like forward pass for each epoch
    - args: epoch
    - return: avg_loss, and acc

    - If opt.extra_shuffle `True` and epoch > opt.curriculum, then `trainData.shuffle()`
      - dive into `trainData.shuffle()`: only shuffle the order between `src` and `tgt`, not inside `src` and `tgt`

    - get a random ordered batch index from `torch.randperm(len(trainData))`

    - set 0 to `total_loss`, `total_words`, `total_num_correct`

    - set 0 to `report_loss`, `report_tgt_words`, `report_src_words`, `report_num_correct`

    - start timing

    - for each ordered idx `i` from 0 to len(trainData)-1: (here just from 0 to 1)
      - it is like for every batch of all dataset

      - get `batchIdx` from `batchOrder[i]` if required to shuffle the data using `opt.curriculum` as `False`

      - `batch` is one out of 2 items inside `trainData`: (see what inside `batch`)

      - set gradients of all model parameters to 0 using `model.zero_grad()`

      - calc `outputs` using `model(batch)`
        - dive in to see `model(batch)` as a whole
        - it is like forward pass up to `outputs`

      - get `targets` from `batch[1][1:]`
        - why remove the first row?

      - get `loss`, `gradOutput`, `num_correct` from  `memoryEfficientLoss()` with `outputs`, `targets`, `model.generator`, `criterion`
        - dive: compute generations one piece (one sample data) at a time
        - move on from `outputs` onto `loss`, `accuracy`, `gradOutput`

      - do backpass with `outputs.backward(gradOutput)`

      - update all parameters with `optim.step()`

      - get `num_words` using `targets.data.ne(onmt.Constants.PAD).sum()`

      - add up `loss` to `report_loss`

      - add up `num_correct` to `report_num_correct`

      - add up `num_words` to `report_tgt_words`

      - add up `sum(batch[0][1])` to `report_src_words`

      - add up `loss` to `total_loss`

      - add up `num_correct` to `total_num_correct`

      - add up `num_words` to `total_words`

      - Every 50 or `opt.log_interval`: print out
        - epoch
        - len(trainData)
        - accuracy
        - ppl
        - ...

      - return avg_loss as `total_loss/total_words`
      - return accuracy as `total_num_correct/total_words`

7. for each `epoch` of 13 epochs:
    - print empty string

    - get `train_loss` and `train_acc` from each epoch using `trainEpoch(epoch)`
    - calculate `train_ppl` with a formula
    - print out `train_ppl` and `train_acc`

    - get `valid_loss` and `valid_acc` using `eval()` with `model`, `criterion`, `validData`
    - calculate `valid_ppl` with a formula
    - print out `valid_ppl` and `valid_acc`

    - update the latest `learning_rate` with decay and latest ppl with `valid_loss` for each epoch

    - store a dictionary containing a whole state of the model as `model_state_dict`, using `model.state_dict()`

    - Make sure dict `model_state_dict` has no key name with 'generator' in it

    - store a whole state from `model.generator.state_dict()` into `generator_state_dict`

    - set up `checkpoint` as:
        'model': model_state_dict,
        'generator': generator_state_dict,
        'dicts': dataset['dicts'],
        'opt': opt,
        'epoch': epoch,
        'optim': optim

    - save this `checkpoint` dict into a torch file `*.pt` for each epoch
