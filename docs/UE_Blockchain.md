# TP Blockchain Ethereum


## Premières transactions

> Objectif: Créer son premier Wallet et réaliser ses premières transactions d'ETH sur un réseau de test.

  - Installer l'extension Metamask dans votre navigateur (Chrome ou Firefox)
  - Créer un compte et copier sur une feuille (ou sauvegarder) les douze mots.
  - Connecter Metamask au réseau de test **Ropsten**.
  - Récuperer 1.0 ETH via le *faucet*.
  - Surveiller via un explorateur de blockchain (etherscan) la confirmation de la transaction.
  - Envoyer 1 ETH au faucet. Pourquoi la transaction est rejetée ?
  - Envoyer 0.1 ETH à un autre compte vous appartenant. Vous devrez le créer au préalable.
  - Envoyer 0.1 ETH vers le compte d'un camarade.
	
**Travail à rendre** : 
  
  1. Lorsque vous avez envoyé 1 ETH pourquoi la transaction a t'elle été rejetée ?
  1. Que constatez-vous à propos de votre relevé de compte ? La somme exact est elle prélevée/déposée sur votre compte ? 
  1. Combien coûte en GAS et en Ether (ou Wei) l'envoie d'Ether ?
  1. Les frais en GAS sont-il proportionnel à la quantité d'Ether envoyé ?
  1. Quel est la particularité de *Ropsten* ? 


## Premières interaction avec la Blockchain Ethereum

> Objectif: Faire ses premières requêtes sur la blockchain *Ropsten* et le *mainnet*.

 - Se créer un compte sur [infura](https://infura.io/) (sauvegarder votre identifiant) 
 - Faire une requête *curl* permettant de connaitre le numéro du dernier bloque miné.

[comment]: # (curl -X POST --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}' http://... )

Liens utiles :  https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_blocknumber

**Travail à rendre** : 

  1. Quel est le numéro du dernier bloque miné en décimal (préciser la date et l'heure de relevé) ?



## Premier Smart Contract

> Objectif: Compiler et déployer un premier Smart Contract.

  - Copier/coller le Smart Contract (Hello.sol) en Solidity fournit ci-dessous dans l'IDE Remix ([https://remix.ethereum.org](https://remix.ethereum.org)).
  - Compiler et déployer le Smart Contract sur le réseau *Ropsten* (Injected Web3 / Ropsten).
  - Combien a couté le déploiement du contrat ? Sa taille influe t-elle sur le coût ? 
  - Retrouver l'adresse du contract sur [Etherscan](http://ropsten.etherscan.io). Quel est la démarche ? 
  - Essayer de modifier la chaine de caractères *message*. Combien cela coûte t-il ?
  - Écrire un contrat similaire à *Hello.sol* pour gérer cette fois-ci un entier `uint256`.

```
pragma solidity ^0.5.0;

contract Hello {
  string private message;
  constructor(string memory _message) public {
    message = _message;
  }
  function getMessage() public view returns (string memory) {
    return message;
  }
  function setMessage(string memory _message) public {
    message = _message;
  }
}
```

## Smart Contract simple

> Objectif: Création d'un premier Token basé sur un contrat simple.

  - Lire, comprendre et déployer sur *Ropsten* le contrat ci-dessous ([Minimum viable token](https://www.ethereum.org/token))
  - Qui est le propriétaire initial des tokens ? 
  - Transférer quelques tokens d'un compte vers un autre compte.
  - Visualiser le nombre de tokens appartenant à chacun des comptes sur [www.myetherwallet.com](https://www.myetherwallet.com) (nombre de décimal est 0).

```
pragma solidity >=0.4.22 <0.6.0;

contract MyToken {
    /* This creates an array with all balances */
    mapping (address => uint256) public balanceOf;

    /* Initializes contract with initial supply tokens to the creator of the contract */
    constructor(
        uint256 initialSupply
        ) public {
        balanceOf[msg.sender] = initialSupply;              // Give the creator all initial tokens
    }

    /* Send coins */
    function transfer(address _to, uint256 _value) public returns (bool success) {
        require(balanceOf[msg.sender] >= _value);           // Check if the sender has enough
        require(balanceOf[_to] + _value >= balanceOf[_to]); // Check for overflows
        balanceOf[msg.sender] -= _value;                    // Subtract from the sender
        balanceOf[_to] += _value;                           // Add the same to the recipient
        return true;
    }
}
```


## Introduction à Truffle (TODO)

> Objectif: Initiation à l'outil Truffle

  - Le framework Truffle permet d'héberger une blockchain Ethereum sur votre poste : [Ganache](https://truffleframework.com/ganache)
  - Installer *node.js* (https://nodejs.org), puis `npm install truffle -g`.
  - `npm install -g ganache-cli`
  - `mkdir MetaCoin && cd MetaCoin && truffle unbox MetaCoin`
  - Changer le numéro de port dans *truffle.js* (7545 -> 8545)
  - `truffle compile`
  - `truffle migrate`
  - `truffle console`

```
let accounts = await web3.eth.getAccounts()
await web3.eth.getBalance(accounts[0]) 

let instance = await Hello.deployed()
instance.getBalance(accounts[0])
```





