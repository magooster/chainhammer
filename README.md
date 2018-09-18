Public repo because [#346](https://github.com/jpmorganchase/quorum/issues/346), etc - for more info also see Electron-[internal repo](https://gitlab.com/electronDLT/training-material/).
# chainhammer v28
TPS measurements of Quorum, EnergyWebFoundation, etc. - should work with any Ethereum type chain; focus on PoA consensus

## instructions
* `chainhammer` - submits many transactions to blockchain - see next chapter 'chronology'
* `chainreader` - reads in the whole chain, and visualizes TPS, blocktime, gas, bytes - see [chainreader/README.md](chainreader/README.md)

### chronology

1. [log.md](log.md): initial steps; also tried *Quorum's private transactions*
1. [quorum.md](quorum.md): raft consensus, geth fork
1. [tobalaba.md](tobalaba.md): parity fork
1. [quorum-IBFT.md](quorum-IBFT.md): other consensus algo in quorum
1. [parity.md](parity.md): work in progress
1. [eos.md](eos.md): not begun

## faster wider more

See 

* logbook [log.md](log.md) for what I had done initially to get this faster *on Quorum*, step by step. 
* some ideas what to try next: [TODO.md](TODO.md) = e.g. geth/parity PoA, vary transaction size, run on host machine (not docker/vagrant), etc.


Suggestions please: how can I speed this up further? 

## you
See [other-projects.md](other-projects.md) using this, or projects which are similar to this. 

Please report back when you have done other / new measurements. 

## run

For more details e.g. how to run a network of parity nodes, see [reproduce.md](reproduce.md). This assumes it's already running.

The focus here is on chainhammer itself:
### dependencies
```
sudo apt install python3-pip libssl-dev
sudo pip3 install virtualenv 
virtualenv -p python3 py3eth
source py3eth/bin/activate

python3 -m pip install --upgrade pip==18.0
pip3 install --upgrade py-solc==2.1.0 web3==4.3.0 web3[tester]==4.3.0 rlp==0.6.0 eth-testrpc==1.3.4 requests pandas jupyter ipykernel matplotlib
ipython kernel install --user --name="Python.3.py3eth"
```
all python scripts & jupyer notebooks must be run within that virtualenv, e.g.:

```
source py3eth/bin/activate

touch account-passphrase.txt
./deploy.py 
```
use this ^ to test whether communication with the ethereum node is working, and to create local files about the compiled and deployed contract. If there are connection problems, check the ports in [config.py](config.py) --> `RPCaddress, RPCaddress2`.

### quickstart

first terminal:
```
./tps.py
```
second terminal:
```
./deploy.py notest; ./send.py threaded2 23
```

Then, after all (e.g. 20,001) transactions have been seen, extract the whole chain into `allblocks-parity_run7.db` (example)
```
cd chainreader
./blocksDB_create.py allblocks-parity_run7.db
```
and examine it with a jupyter notebook, to make the diagrams
```
jupyter notebook blocksDB_analyze_parity-aura_run7.ipynb
```
by menu --> Kernel --> Restart & Run All. 


## credits

Please credit this as:

> benchmarking scripts "chainhammer"  
> https://gitlab.com/electronDLT/chainhammer    
> by Dr Andreas Krueger, Electron.org.uk, London 2018  

Consider to submit your improvements & [usage](other-projects.md) as pull request. Thanks.

### short summary

> Electron created the open source tools chainhammer which submits a high load of smart contract transactions to an Ethereum based blockchain, and chainreader which reads in the whole chain, and produces diagrams of TPS, blocktime, gasUsed and gasLimit, and the blocksize.
> https://gitlab.com/electronDLT/chainhammer

---

---

---

## chainhammer --> chainreader -->  diagrammer

### quorum raft
[quorum.md](quorum.md) = Quorum (geth fork), raft consensus, 1000 transactions multi-threaded with 23 workers, average TPS around 160 TPS, and 20 raft blocks per second)
![chainreader/img/quorum_tps-bt-bs-gas_blks242-357.png](chainreader/img/quorum_tps-bt-bs-gas_blks242-357.png)

### quorum IBFT
[quorum-IBFT.md](quorum-IBFT.md) = Quorum (geth fork), IBFT consensus, 20 millions gasLimit, 1 second istanbul.blockperiod; 20000 transactions multi-threaded with 13 workers. Initial average >450 TPS then drops to ~270 TPS, see [quorum issue](https://github.com/jpmorganchase/quorum/issues/479#issuecomment-413603316))

![https://gitlab.com/electronDLT/chainhammer/raw/master/chainreader/img/istanbul-crux-docker-1s-gas20mio-RPC_run8_tps-bt-bs-gas_blks28-93.png](https://gitlab.com/electronDLT/chainhammer/raw/master/chainreader/img/istanbul-crux-docker-1s-gas20mio-RPC_run8_tps-bt-bs-gas_blks28-93.png)

### tobalaba
[tobalaba.md](tobalaba.md) = Public "Tobalaba" chain of the EnergyWebFoundation (parity fork), PoA; 20k transactions; > 150 TPS if client is well-connected.

![chainreader/img/tobalaba_tps-bt-bs-gas_blks5173630-5173671.png](chainreader/img/tobalaba_tps-bt-bs-gas_blks5173630-5173671.png)

### parity aura v1.11.8
[parity.md](parity.md) = using [parity-deploy.sh](https://github.com/paritytech/parity-deploy) dockerized network of 4 local nodes with 40 million gasLimit, and 4-8 seconds blocktime; 20k transactions; ~ 65 TPS. 

![chainreader/img/parity-aura_run7_tps-bt-bs-gas_blks3-90.png](chainreader/img/parity-aura_run7_tps-bt-bs-gas_blks3-90.png)

Calling all parity experts: How to improve this? See issue [PE#9393](https://github.com/paritytech/parity-ethereum/issues/9393), and the [detailed log of what I've tried already](parity.md). Thanks.
