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


1. be aware of arguments of `def from_specification(spec, kurfile=None):`
				- `spec` == `entry` == dict for data described in kurfile
				- `spec` == a dict {'mnist':{ ...}}

2. If `spec` is not a dict:
				- raise error

3. Get unique qualified supplier names from `spec`, assign them to `candidates`:
				- get all suppliers name: using `cls.get_name() for cls in Supplier.get_all_suppliers()`
				- get all unique names using `set()`
				- get all suppliers name from `spec` using `set(spec.keys())`
				- `spec.keys()` is a 'mnist'

4. If `candidates` is empty:
				- raise error

5. If `candidates` has more than 1 supplier names:
				- raise error

6. Get the first supplier name from `candidates` and assign to `name`: using `candidates.pop()`

7. Get value of the supplier dict from `spec[name]`, assign it to `params`

7. Get value of `spec['name']` or `spec.get('name')`, assign to `supplier_name`: in this case, None

8. If `params` is a dict:
				- get supplier class using `Supplier.get_supplier_by_name(name)`

				- initiate supplier class with an object using `MnistSupplier(
					name=supplier_name, kurfile=kurfile, **params)`
								- `**params` is for added to `MnistSupplier()` based on `Supplier()`
								- `**params`: for unknown named arguments
								- `*params`: for unknow number of argus
				- assign this object to `result`

**What exactly is stored inside MnistSupplier?**
- dive into

9. If `params` is a str or list or tuple
				- do the same with slight change on using `params` or `*params`

10. If `params` is not str, list, tuple or dict:
				- raise error

11. return `result`, which is MnistSupplier object with `params` given from a kurfile

## Further explore
- what inside `MnistSupplier`?
