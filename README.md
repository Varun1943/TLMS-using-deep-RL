# TLMS-using-deep-RL
hey all..!
this repo is all about optimizing the traffic flow escpecially in the peek hours of the day..
main features:
1) single agent based (upon using multiple instances in the scenario you can observe the green wave optimization).
2) prioritizing the emergency vechiles.
3) Dynamic lane-shifiting and collision based rerouting protocols.

here's how you can see it work on your system.
1) Download sumo(traffic simulator).
2) clone this repo.
3) you'll find the scenario
4) download vunarabalities :
5) pytorch
6) stable Baselines3(SB3[extra])
7) open-ai's gym env
8) traci(important api to interact with sumo env)
9) torch torchvision torchaudio
10) use cuda for better performance upon cpu
11) **only for colab users**
12) it uses cloud resources by default so u need to switch for the connecting to local runtime ..
13) here's how u can
14) open via chrome (less security)
15) run the command(in the anaconda prompt): jupyter notebook ^--NotebookApp.allow_origin='https://colab.research.google.com' ^--port=8888 ^--NotebookApp.port_retries=0
16)  upon observation u can see the token something looks like example(format) -http://localhost:8888/tree?token=ec441ae3d2798c9a4c209ff8ebd3a8326eaff2ecd7093abf
17)  now your are ready to run the ipynb scripts

