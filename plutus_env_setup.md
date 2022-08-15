
## With Ubuntu installed

Execute:
```bash
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install snap git nano curl
sudo adduser anyNameYouLike (optional)
sudo usermod -aG sudo anyNameYouLike (optional)
```
Creating a different user with sudo capabilities is an optional step, recommended for
security in production environments.
Close session and enter with the new created user

## Install Nix
Execute
```bash
curl -L https://nixos.org/nix/install > install-nix.sh
chmod +x install-nix.sh
./install-nix.sh
```
Log Off user and LogIn again.
Open a terminal and verify installations
Execute
```bash
nix --version
git --version
nano --version
```
III IOHK Binary Cache (necessary for saving hours of time in the Plutus Libraries installation)
Execute
11. sudo mkdir -p /etc/nix
12. sudo nano /etc/nix/nix.conf

Include on the open file this: (this is copy-pasteable)
experimental-features = nix-command flakes
allow-import-from-derivation = true
substituters = https://hydra.iohk.io https://iohk.cachix.org https://cache.nixos.org
trusted-public-keys = hydra.iohk.io:f/Ea+s+dFdN+3Y/G+FDgSq+a5NEWhJGzdjvKNGv0/EQ= iohk.cachix.org-1:DpRUyj7h7V830dp/i6Nti+NEO2/nhblbov/8MW7Rqoo= cache.nixos.org-1:6NCHdD59X431o0gWypbMrAURkbJ16ZPMQFGspcDShjY=
(Reference: You can Save with Ctrl-O and close the file with Ctrl-X)
IV PLUTUS-APPLICATION-FRAMEWORK
Clone the Plutus-Apps repository
Execute
13. git clone https://github.com/input-output-hk/plutus-apps.git
git checkout 6aff97d596ac9d59460aab5c65627b1c8c0a1528 (for running our examples)
Change into the plutus-apps and open nix:
Execute
14. cd plutus-apps
15. nix-shell (Takes a while the first time)
PLUTUS-APPLICATION-FRAMEWORK
Execute
16. cd plutus-apps
17. cabal update (Takes a short while)
V NOW THE PLUTUS PLAYGROUND
In the same window change the directory by executing:
18. cd plutus-playground-server
19. cabal update
20. plutus-playground-server
Takes a while and ends with:
Interpreter ready
Open a new terminal (on the GUI or a new SSH connection if no gui)
Change into the plutus-apps and open nix:
Execute
21. cd plutus-apps
22. nix-shell
Change into plutus-playground-client folder, update and start
23. cd plutus-application-client
24. cabal update
25. npm start
You might use a browser to navigate to https://localhost:8009 (if your Linux has GUI) or, you can
use the IP of the VM on the network (https://<VM IP ADDRESS>:8009) and be able to see the
Plutus Application Playground, try compiling and running the test contract to see if you were
successfull.
