`kur -v data --single mnist.yml`

# How to access data from Kur?

## Dive into `__main__.py` and `__main__.main()`

## Further dive into `__main__.prepare_data(args)`

### Prepares a model's data provider.

1. receive `args` from works done in `main()`

2. convert `mnist.yml` with its `include` files into `spec` using `parse_kurfile(args.kurfile, args.engine)`

3. If `args.target` == 'auto':
				- set `result` as `None`
				- if 'train' is a key of `spec.data` and 'data' is a key of `spec.data['train']`,
				- then give 'train' to `result`
				- then give `result` to `args.target`
				- but if 'train' is not available, do the same to ('validate', 'test', 'evaluate') in order

4. get data `providers` for 'train' section with `spec.get_provider(args.target, ...)`
				- dive...


5. if `args.assemble` == `True`:
				- get the `default_provider` from `providers` or get any provider stored inside `providers`

				- Returns the parsed Model instance as `spec.model` using `spec.get_model(provider)`

				- if `args.target` == 'train':
						- set `target` to be a new `trainer` instance from the kurfile
						- this `trainer` is `Executor(model, loss, optimizer)` using `spec.get_trainer(with_optimizer=True)`
						- seemingly no data used here

				- if `args.target` == 'test':
						- like above but `with_optimizer = False`

				- if `args.evaluate` == 'evaluate':
				  - set `target` to be a new `evaluator` instance
						- this `evaluator` is `Executor(model)` using `spec.get_evaluator()`

				- To compile a model, to generates a backend-specific representation of the model, using `target.compile(assemble_only=True)`
						- use `Executor` or `trainer` or `evaluator` instance define above, and do `Executor.model.backend.compile()`
						- Not sure whether data is used here??

6. get the first provider of `providers` and assign it to `provider`

6. set `batch` to be `None`

7. Access `provider` and assign its first batch to `batch`
				- `batch` is a dict, contain `images` and `labels`, each with 32 samples
				- `provider` is not a dict, not a list, hardly accessible on its own
				- `for batch in provider:` can access all dataset in batches, one `batch` at a time

8. if `batch` is empty:
				- log error and exit as failure

9. set `num_entries` to be `None`

10. set `keys` to be `sorted(batch.keys())`, make sure

11. set `num_entries` to be `len(batch[keys[0]])`

12. print out each sample of a full batch of data:
				- for each entry of a batch:
								- print out sample index: `print('Entry {}/{}:'.format(entry+1, num_entries))`
								- for each key and its values:
												- print out each row of value at a time: using
												- `for key in keys:
																		print('  {}: {}'.format(key, batch[key][entry]))`

				- If `num_entries` is None: (no data in a batch)
								- log error
								- exit as failure
