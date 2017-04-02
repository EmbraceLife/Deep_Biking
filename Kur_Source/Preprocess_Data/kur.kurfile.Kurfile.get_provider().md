`kur -v data --single mnist.yml`

# How to access data from Kur?

## Dive into `__main__.py` and `__main__.main()`

## Further dive into `__main__.prepare_data(args)`

## Diving into the following:

```Python
pdb.set_trace()

providers = spec.get_provider(
	args.target,
	accept_many=args.target == 'test'
)
```

## Address:

'get_provider', <function kur.kurfile.Kurfile.get_provider>

## Why explore this func?
- through `for batch in provider` we can access every batch of a dataset
- I want to know how `provider` or `providers` achieve it

## Let's dive into `kur.kurfile.Kurfile.get_provider`

1. be aware of arguments of this func:
`def get_provider(self, section, accept_many=False):`
				- self: Kurfile object
				- section: a string for naming sections
				- accept_many: allow single data or more data

2. If `section` is a key inside `self.data`:
				- self: a kurfile object
				- assign `self.data[section]` to `section`
				- now `section` is a dict

				- If neither 'data' nor 'provider' is a key of `section`
								- return None

2. Else `section` is Not a key inside `self.data`:
				- return None

3. assign `section.get('data')` or {} to `supplier_list`
					- a list of a single dict
					- dict is like {'mnist': {'labels':..., 'images':...}}

4. If `supplier_list` is list or tuple:
				- convert `supplier_list` to a dict {'default': supplier_list}
				- now `supplier_list` is a dict

4. elif `supplier_list` is not a dict:
				- raise error

6. If there are more than 1 data providers inside `supplier_list` and `accept_many` is set `False`:
				- raise error

7. set `suppliers` to {}

8. access every key `k` and value `v` of `supplier_list`:
				- if `v` is not list or tuple:
								- raise error
				- `k` is a string names, e.g., 'default'; `v` is list
				- `entry` is the or a dict inside list `v`
				- convert `entry` from a dict to a real supplier object using `Supplier.from_specification(entry, kurfile=self) for entry in v` (Creates a new Supplier from a specification)
				- `suppliers` is a dict contains {k: [supplier1, supplier2, ...]}, same `k` as key name, `v` as a list of suppliers dicts

**Further explore:**
`Supplier.from_specification(entry, kurfile=self)`				

9. get provider specification, which is `dict(section.get('provider') or {})` (num_batches in this case), assign to `provider_spec`

	10. If 'name' is a key of `provider_spec`:
					- `provider` is assigned using `Provider.get_provider_by_name(provider_spec.pop('name'))`

11. If 'name' is not a key of `provider_spec`:
					- assign `Kurfile.DEFAULT_PROVIDER` to `provider_spec`
					- default is `BatchProvider`

12. Finally, return a dict:
					- `k`: key name
					- `v`: a list of supplier objects
					- merge all supplier objects using `provider(
						sources=Supplier.merge_suppliers(v),
						**provider_spec
					)`
					- `Supplier.merge_suppliers(v)` Merges a number of suppliers together, usually in order to create
					- `provider()`: to create batch provider object

**Further explore:**
<module 'kur.providers.batch_provider' from '/Users/Natsume/Downloads/kur/kur/providers/batch_provider.py'>
