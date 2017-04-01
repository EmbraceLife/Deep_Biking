# Hack kur

All changes made to kur source code:
1. indentation must be `hard` style, `1` space in `atom editor`

**Display console args**

Where:
1. `__main__.py`
2. `main()`
3. after line `args = parse_args()`

What:
`print("Check your console arguments: %s", args)`

**Display Simple Overview of dataset**

Where to change (part1):
1. `__main__.py`
2. `prepare_data()`

```python
num_entries = None
keys = sorted(batch.keys())
num_entries = len(batch[keys[0]])

# --single ####### start

num_samples = 0
if args.single:
	num_samples = 1
	num_batches = provider.num_batches
	train_entries = provider.entries
	logger.info("We are training with %s batches using %s samples of data", num_batches, train_entries)
	batch_shape = {}
	for k in keys:
		batch_shape[k] = batch[k].shape
	logger.info("A single batch consists of: %s", batch_shape)
else:
	num_samples = num_entries

for entry in range(num_samples):
	############# end

	print('Entry {}/{}:'.format(entry+1, num_entries))
	for k in keys:
		print('  {}: {}'.format(k, batch[k][entry]))

```

Where to change (part2):
1. `__main__.py`
2. `parse_args()`


```python
# Add the codes below 
# --single  #### start of change
subparser.add_argument('--single', action='store_true', help='Only '
	'display one single sample of dataset, '
	'not a whole batch.')
######## end of changes
subparser.set_defaults(func=prepare_data)
```
