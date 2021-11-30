# Format String Attack Lab

## Task 1

- Started by running the docker commands, to initiate the containers
- Access shell by running docksh with the initial digits of the container: `docksh 93`
- Run the command `echo hello | nc 10.9.0.5 9090` and see the prints in the shell where the container is running, including the smiley faces.
- Run the `build_string.py` script, which will generate 1500 bytes and write them to the file `badfile`
- Run the command `cat badfile | nc 10.9.0.5 9090` and notice that the smiley faces don't apear in the shell running the containers, which means that the server crashed. This happened because the string written to the `badfile` contained the bytes `%.8x` (could be `%s` instead, for example), which makes the program look for an address that doesn't exist.

## Task 2

#### Task 2.A
