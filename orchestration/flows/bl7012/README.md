# Data movement BL7012
This package contains functions to initiate Prefect data processing workflows for COSMIC data. The current implemented Prefect workflow enables data movement from a Globus beamline endpoint to the desinated NERSC Globus endpoint. 

# Getting start
Set up a python enviornment as follow:
```
$   git clone https://github.com/grace227/splash_flows_globus.git
$   cd splash_flows_globus
$   pip install -e .
```
Provide information of a Globus collection endpoint in `splash_flows_globus/config.yml`:
```
globus:
  globus_endpoints:
    spot832:
      root_path: /
      uri: spot832.lbl.gov
      uuid: 44ae904c-ab64-4145-a8f0-7287de38324d
```
Provide information of Prefect and Globus authentication in `.env` file:
```
GLOBUS_CLIENT_ID=<globus_client_id>
GLOBUS_CLIENT_SECRET=<globus_client_secret>
PREFECT_API_URL=<url_of_prefect_server>
PREFECT_API_KEY=<prefect_client_secret>
```
Create a Prefect deployment workflow. Append new workflow in the `create_deployments.sh` file:
```
prefect deployment build <path_of_file>:<prefect_function> -n 'name_of_the_workflow' -q <tag>
prefect deployment apply <prefect_function>-deployment.yaml
```
Following example creates a Prefect workflow for the function of `process_new_file` in file of `./orchestration/flows/bl7012/move.py`
```
prefect deployment build ./orchestration/flows/bl7012/move.py:process_new_file -n 'process_newdata7012' -q bl7012
prefect deployment apply process_new_file-deployment.yaml
```

# Starting a Prefect workflow manually
Below is the command to start the Prefect workflow:
```
python -m orchestration.flows.bl7012.move <Relative path of file respect to the root_path defined in Globus endpoint>
```

# Submitting workflow via Prefect API
An example is shown `example.ipynb` to submit a PREFECT workflow to PREFECT server. 

Once the job is submitted, a workflow agent is needed to work on jobs in queue. A workflow agent can be launched by:
```
prefect agent start -q <name-of-work-queue>
```
It requires to have the `PREFECT_API_URL` and `PREFECT_API_KEY` stored as an environment variables, such that the agent knows where to get the work queue. Once the agent is launched, the following message indicates where the agent is currently listening to.
```
Starting v2.7.9 agent connected to https://.../api...

  ___ ___ ___ ___ ___ ___ _____     _   ___ ___ _  _ _____
 | _ \ _ \ __| __| __/ __|_   _|   /_\ / __| __| \| |_   _|
 |  _/   / _|| _|| _| (__  | |    / _ \ (_ | _|| .` | | |
 |_| |_|_\___|_| |___\___| |_|   /_/ \_\___|___|_|\_| |_|


Agent started! Looking for work from queue(s): <name-of-work-queue>...
```