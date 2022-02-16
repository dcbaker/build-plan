# Design Considerations

## Terms

__Orchestrator__: The build system at the "root", which is ultimately responsible for executing the plan

__Provider__: A build system executed in "planning" mode, it provides only a plan to the Orchestrator, and doesn't execute anything itself.

## Two way communication

All of this needs to be generic enough that we could support *any* language. That my require certain per language fields. Not sure yet

The Orchestrator should be able to communicate the following information to each Provider:

    - Any compilers it is using (for each machine, there should be one compiler, per language)
    - The host, build, and target machines (with the assumption that target == host if unprovided, and host == build if unprovided)
    - any dependencies that are already found, and the Provider *must* use these instead of replacing them 
        - (what to do about mismatched versions?)
        - how to differentiate system provided dependencies from ones being built?

The Provider needs to return the following information to the Orchestrator:

    - Any dependencies it provides
    - Any compilers it has detected (those not provided by the Orchestrator)
    - A list of targets, with their inputs and outputs (and thus dependency ordering)

Here's a *very* rough idea I have the of the Provider -> Orchestrator
```json
{
    "dependencies": {
        "zlib": {
            "ABI": "c",
            "link_args": ["-L", "/usr/local/lib", "-lz"],
            "compile_args": ["-DSOME_FEATURE"]
        }
    ],
    "compilers": {
        "host": {
            "c": ["/usr/bin/ccache", "/usr/local/bin/gcc-foo"]
        }
    },
    "targets": [
        {
            "language": "c",
            "outputs": ["foo.o"],
            "inputs": ["src/foo.c"],
            "arguments": ["-DHAVE_FOO"]
        }
    ]
}
```
