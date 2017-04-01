`kur -v data --single mnist.yml`

# How to access data from Kur?

## Dive into `__main__.py` and `__main__.main()`

1. get `args` from console with `parse_args()`

2. set up `loglevel` with:
    - 0 as logging.WARNING
    - 1 as logging.INFO
    - 2 as logging.DEBUG

3. choose config func as `config` from `logging.basicConfig` or `logcolor.basicConfig`

4. configurate it with `config()` and many format set up

5. redirect all warnings to the logging package with `logging.captureWarnings(True)`

6. monitor process if args required it with `do_monitor(args)`
				- print(args) or args.monitor to see

7. If `args.version` is `True`, set `args.func` to be `version`

8. If `args.version` is `False` and No `args.func` is available using `non hasattr(args, 'func')`:
				- print out a few messages
				- exit program as failure using `sys.exit(1)`

9. create an JinjaEngine object as `engine` using `JinjaEngine()`

10. give `engine` to `args.engine`

11. exit program by running `args.func(args)` as success or just exit directly with success

				- `args.func(args)` is `prepare_data(args)` here
				- let's explore further from here
