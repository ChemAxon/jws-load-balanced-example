# Install before run

This little guide is based on https://github.com/tdi/vagrant-docker-swarm You can also consult
that [README](https://github.com/tdi/vagrant-docker-swarm/blob/master/README.md) for 
further infomration.

## Install:

* Install docker-compose on every machine of the swarm.
* Init docker swarm machine on the manager node and add the desired nodes to the swarm.


## Setups before first run:

* unzip a JWS tar.gz here (in the end you should have a `jws` folder with the whole application) (download one from here: [jcb-jws-repo](https://artifacts.chemaxon.com/artifactory/list/jchem-generic-snapshot-local/jws/) I have explicitly used: [19.17.0-10-10179](https://artifacts.chemaxon.com/artifactory/list/jchem-generic-snapshot-local/jws/jws-19.17.0-10179/jws_unix_19.17.tar.gz)) **This should be replaced by the released artifact**
* copy a valid JWS license to `jws/license/license.cxl` (this is good for the purpose: [devlicense](http://devlicense.bpo.cxn/license.cxl)) 
* download [Postgres JDBC driver](http://central.maven.org/maven2/org/postgresql/postgresql/42.2.5/postgresql-42.2.5.jar) and copy it to `jws/jws-db/jdbc-drivers/` folder
* ensure a running docker swarm and run the following docker commands on the manager node.


# Execute

Build the image, this must be executed on every node in the directory of `jws-load-balanced-example`: 

    docker-compose build

Now the image cxn/jws should be built.

Deploy the docker compose file on the master node: 

    docker stack deploy --compose-file docker-compose.yml demo

This command will create a new service stack with name `demo` based on the docker-compose file.

## Other commands

* `docker stack ls` lists all stacks in swarm
* `docker stack ps <stack-name> --no-trunc` list all services in stack (identified by <stack-name>)
* `docker stack rm <stack-name>` stops and removes all services in stack

# Good to know

You can not _just click through_ the demobecause the default table name `testExtendedTable`
is too long when it is in table names like `fingerprint_testExtendedTable_dot_sample_sorted_fpPage_hash_T_index`
(names should be less than 64 characters). But __table name `alma` works__ fine.

**Volumes are local by default** despite of anything I have claimed earlier. It is possible
to install additional drivers for docker and create network attached storages and put docker 
voulmes on them but I hade not enough time to figure them out in an automatic manner. But
you can find more information [here](https://forums.docker.com/t/docker-swarm-nfs-mount/39007/5).
This means that if something (like postgres) has it's data on a volume and we kill the machine
that runs that container then data will be lost also. However, it is possible now to kill any worker
because postgres container is fixed to manager which should not be killed anyway and jws containers
are fixed to the three workers.

