# iEBE-MUSIC
This is a repository is an overarching numerical framework for event-by-event simulations of relativistic heavy-ion collisions. We implemented a EoS derived from a gaussian process, and the instructions to use it are the following.

## Setup & Usage:

The EoS is incremented in the MUSIC and iSS versions specified in the "get_code_packages" files inside ./codes. 

To use the GP EoS, the following parameters must be defined in the configuration file.

| Parameter | Default | Description |
|-----------|---------|-------------|
| `EOS_to_use` | `24` | Selects the GP EoS. |
| `EOS_gp_l` | `400` | Correlation-length hyperparameter of the GP covariance matrix. |
| `EOS_gp_sigma` | `15` | Sigma hyperparameter of the GP, controlling the strength of the correlations. |
| `EOS_gp_type` | `hrg` | Defines how the EoS is extrapolated toward zero temperature. |
| `EOS_gp_sample` | `s0` | Selects the GP sample. For multiple runs, this can be changed to `s1`, `s2`, etc. |
| `gp_type` | `constrained` | Selects whether the GP is constrained by lattice data (`constrained`) or uses an unconstrained mean with larger variations (`unconstrained`). |
| `T_sw` | `136` | Switching temperature in MeV. |

For example, inside your parameters.py file, add:

'EOS_to_use' = 24,
'EOS_gp_l' = 400,
'EOS_gp_sigma' = 15,
'EOS_gp_type' = "hrg",
'EOS_gp_sample' = "s0",
'gp_type' = "constrained",
'T_sw' = 136.

The GP files are available at "https://github.com/luizafperin/MUSIC-EOS-data.git". Make sure that the GP EoS parameters choosen in parameter_file.py correspond to files available in the MUSIC-EOS-data repository.

To generate a local job, run:
'''python3 generate_jobs.py -w test_job -par config/parameter_file.py'''

The generated job will be located in:

test_job/
└── event_0/

Enter the event directory and submit the job:
'''
cd test_job/event_0
bash submit_job.script
'''

## Running on HTcondor
First, copy the iEBE repository:
'''git clone https://github.com/luizafperin/iEBE-MUSIC.git -b clean-final
'''

Then, to create the docker
'''
cd docker
docker build -t iebe-music .
'''

Then, create the folder you want your results to be saved in, an do
'''
apptainer build iebe-music.sif docker-daemon:iebe-music:latest
cp -r  caminho/da/pasta/iEBE-MUSIC/shared_seeds
cp  caminho/da/pasta/iEBE-MUSIC/config/parameter_file.py 
python3 caminho/da/pasta/iEBE-MUSIC/Cluster_supports/HTCondor/generate_submission_script_final.py     -param parameters_file.py     -singularity ./iebe-music.sif     -n 1     -n_urqmd 1     -n_hydro 1     -nth 1     -jobid id
condor_submit singularity_submit
'''
