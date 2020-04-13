Hello World template
This template is a "Hello World" example that teaches you how to:

Provide arguments to a contract constructor using the Configure contract modal.
Store state in a contract and to update it.
Fetch your newly created contract's information from the blockchain and render it to a front end.
Tutorial content supplied by kauri.io.
Explanation of the template
The smart contract
Find the smart contract file in contracts/HelloWorld.sol
The first line, pragma solidity ^0.5.10 specifies that the source code is for a Solidity version greater than 0.5.10. Pragmas are common instructions for compilers about how to treat the source code (e.g., pragma once).

A contract in the sense of Solidity is a collection of code (its functions) and data (its state) that resides at a specific address on the Ethereum blockchain. The line string public message declares a public state variable called message of type string. You can think of it as a single slot in a database that you can query and alter by calling functions of the code that manages the database. The keyword public automatically generates a function that allows you to access the current value of the state variable from outside of the contract. Without this keyword, other contracts have no way to access the variable.

The constructor is a special function run during the creation of the contract and cannot be called afterward. In this case, it takes a string value initMessage, stores the value in the memory data storage area, and sets message to that value.

The update function is another public function that is similar to the constructor, taking a string as a parameter, and updating the message variable.

1. Configure
Configuring the contract allows you to set the name of the contract and the initial values sent to the constructor as arguments. You can configure the contract by clicking on the gear icon in the left panel. In this example, it configures the string displayed in the front end interface.

2. Compile
Solidity is a compiled language, and you need to convert the Solidity code into bytecode before the contract can run. In the file tree under the contract file, you can compile the contract by clicking the Compile sub-section, and output appears in the Output pane.

3. Deploy
Every smart contract runs at an address on the Ethereum blockchain, and you must deploy it to an address before it can run. When using Studio, the browser simulates the network, but there are several test networks and one main network for the Ethereum blockchain.

Deploy the contract by clicking the Deploy sub-section (nested under the contract file in the file tree), and output appears in the Output pane.

The Web app
Find the HTML file in app/app.html Find the CSS file in app/app.css Find the JavaScript file in app/app.js
This tutorial doesn't cover the HTML or CSS as it's not web3 specific, aside from the element IDs that the JavaScript manipulates. A lot of the JavaScript code follows standard patterns for object-oriented JavaScript, so this tutorial focuses on the web3js specific parts.

First create an instance of the smart contract, passing it as a property, which allows web3js to interact with it.

function HelloWorld(Contract) {
    this.web3 = null;
    this.instance = null;
    this.Contract = Contract;
}
Initialize the HelloWorld object and create an instance of the web3js library, passing Metamask as a provider for the contract. The initialization function then defines the interface for the contract using the web3js contract object and then defines the address of the instance of the contract for the HelloWorld object.

HelloWorld.prototype.init = function () {

    this.web3 = new Web3(
        (window.web3 && window.web3.currentProvider) ||
        new Web3.providers.HttpProvider(this.Contract.endpoint));

    var contract_interface = this.web3.eth.contract(this.Contract.abi);

    this.instance = this.Contract.address ? contract_interface.at(this.Contract.address) :  { message: () => {} };
};
Add other JavaScript boilerplate to create the instance of the HelloWorld object defined above, and show the HTML elements on the page:

HelloWorld.prototype.main = function () {
    $(".blocknumber").show();
    $(".message").show();
    this.update();
}

HelloWorld.prototype.onReady = function () {
    this.init();
    this.main();
};

if(typeof(Contracts) === "undefined") var Contracts={ HelloWorld: { abi: [] }};
var helloWorld = new HelloWorld(Contracts['HelloWorld']);

$(document).ready(function () {
    helloWorld.onReady();
});
The getMessage function gets the message value passed to the instance of the contract. With the IDE, you pass this value from the Configure option found under the disclosure triangle of the contract file, but outside of the IDE, you could pass the value in a variety of ways.

The getBlockNumber works similarly but uses the web3js getBlockNumber function to return the value of the latest block in the configured endpoint.

HelloWorld.prototype.getMessage = function (cb) {
    this.instance.message(function (error, result) {
        cb(error, result);
    });
};

HelloWorld.prototype.getBlockNumber = function (cb) {
    this.web3.eth.getBlockNumber(function (error, result) {
        cb(error, result);
    });
};
The update function ties everything together, calling the two functions defined above, and setting the H2 tags to the values they return or showing an error message.

HelloWorld.prototype.update = function () {
    var that = this;
    this.getMessage(function (error, result) {
        if (error) {
            $(".error").show();
            return;
        }
        $("#message").text(result);

        that.getBlockNumber(function (error, result) {
            if (error) {
                $(".error").show();
                return;
            }
            $("#blocknumber").text(result);
            setTimeout(function () { that.update() }, 1000);
        });
    });
}
Find out more
You can read a full tutorial that accompanies this example dapp, plus many more tutorials, on kauri.io.
