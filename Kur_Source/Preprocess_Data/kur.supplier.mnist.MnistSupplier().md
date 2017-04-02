`kur -v data --single mnist.yml`

# How to access data from Kur?

## Dive into `__main__.py` and `__main__.main()`

## Further dive into `__main__.prepare_data(args)`

## dive into `kur.kurfile.Kurfile.get_provider`
- why?
- it seems `provider` turn dataset into batches
- `provider` get dataset from `suppliers`
- what exactly does `supplier.from_specification` do?

## conclusion from diving below:
- `Supplier.from_specification(entry, kurfile=self)` returns `MnistSupplier` object defined with `spec` from kurfile
- many other suppliers objects can be created and assigned similarly

## Explore `supplier.from_specification` from above

```python
pdb.set_trace()
suppliers = {}
for k, v in supplier_list.items():
	if not isinstance(v, (list, tuple)):
		raise ValueError('Data suppliers must form a list of '
			'suppliers.')
	suppliers[k] = [
		Supplier.from_specification(entry, kurfile=self)
		for entry in v
	]
	```

## Dive into 'from_specification':
- location:
 		`kur.supplier.supplier.Supplier.from_specification`
- Purpose of function:
				'Creates a new Supplier from a specification.'

- dive into MnistSupplier
`if isinstance(params, dict):
	result = Supplier.get_supplier_by_name(name)(
		name=supplier_name, kurfile=kurfile, **params)`

## Explore MnistSupplier:

`MnistSupplier`from '/Users/Natsume/Downloads/kur/kur/supplier/mnist.py'

### What exactly `MnistSupplier` do?
- create a supplier
- supply Mnist image/label pairs
- dataset downloaded online

### What does it mean to initialize an MnistSupplier?

`class MnistSupplier(Supplier):`
				- super class: `Supplier`

create a class method: `get_name(cls)`:
				- `cls`: MnistSupplier object
				- only return `mnist`


initialize `MnistSupplier` object:
1. `def __init__(self, labels, images, *args, **kwargs):`

2. inherit from `Supplier` class:
`super().__init__(*args, **kwargs)`

3. create a large dict and assigned to `MnistSupplier.data`

4. the keys of the large dict are fixed on 'images' and 'labels'

5. values of both keys are following same pattern:
				- `images` and `labels`: in this case, are both dict, which has the same structure
				- {'url': URL, 'checksum': SHA256, 'path': PATH}

6. get the dataset as values of the large dict:
				- `'images': MnistSupplier._normalize(
	VanillaSource(idx.load(MnistSupplier._get_filename(images)))
)`
				- `images` and `labels` are dicts, not 'images' or 'labels'

				- get the dataset file path: `MnistSupplier._get_filename(labels)`

				- load the idx file into a numpy.ndarray:
				`idx.load(MnistSupplier._get_filename(images))`

				- `VanillaSource()`, see below: get the ndarray into `self.data`

7. Create a `VanillaSource` object:

				- `class VanillaSource(ChunkSource):`
				`VanillaSource` is a data source which is simple wrapper around an in-memory array

				- create an object: Create a new vanilla data provider: `def __init__(self, data, *args, **kwargs):`

				- argument `data`: array like

				- inherit from super class: `ChunkSource`: `super().__init__(*args, **kwargs)`

				- assign the array to `self.data = data`

8. Normalize and one-hot the `VanillaSource` data provider or source: `MnistSupplier._normalize(VanillaSource object)` or `MnistSupplier._onehot(VanillaSource object)`

				- access `source.data` which is numpy.array stored inside the `VanillaSource` object `source`

				- `VanillaSource` has `__len__()`, so we can run `len(source)` to access `source.data.shape[0]`

				- build a one-hot encoding container for mnist labels: `onehot = numpy.zeros((len(source), 10))`


## Knowing above, we almost hack `MnistSupplier` for other images and labels dataset, now we need to know how to get dataset in the format of `idx`?

Let's go to `kur.utils.idx.py`
