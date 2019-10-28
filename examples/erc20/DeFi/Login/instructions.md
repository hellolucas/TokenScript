# How to get started with Tokenscript

Tokenscript makes SmartTokens which allow extra functionality added to the token, which extends the SmartToken structure and usability. 

Tokenscript is like the front-end for tokens. 

# Contents.
1. Setting up for Tokenscript Development.
2. Tutorial: How to Create a Login Screen on Tokenscript.
3. Signing the TokenScript.
4. Publishing your Tokenscript

## Setting up for Tokenscript Development. 
Before jumping into the tutorial, you can easily set up your developer environment with these simple steps. 

1. Fork and clone this repository. 
Firstly, we'd like to clone or fork the Tokenscript repository so that we have the latest source code. There are no dependencies for 
Tokenscript so you're able to run through the tutorial straight off the bat. 

1a. When you fork and clone the Tokenscript repository, you'll notice our file structure should look like this:

+-- .idea
+-- doc
+-- examples
|   +-- action
|   +-- devcon5-nft
|   +-- EntryToken
|   +-- erc20
|       +-- AlphaWallet-Discovery-Token
|       +-- DAI
|       +-- DeFi (We are going to be working in here!)
|       +-- WETH
|   +-- fifa
+-- resources
+-- schema
+-- tutorial
-- .gitignore
-- README.md
-- TokenScript.iml 

2. Install IntelliJ or VS Code. 
- IntelliJ is the recommended IDE for Tokenscript as it highlights dependencies in the .xml files and schema.

3. Installing `xmlsectool` for signing the tokenscript.
- On Mac: `brew install xmlsectool`
- Linux/Windows: (Install here)[https://wiki.shibboleth.net/confluence/display/XSTJ2/xmlsectool+V2+Home#xmlsectoolV2Home-ObtainingandUsingxmlsectool]

## Tutorial: How to Create a Login Screen on Tokenscript.
1. Navigate to `/Tokenscript/examples/erc20/DeFi` folder. 

2. Create a new directory called `Login`. This is where we will be building and making our Tokenscript files.

3. Create a new file called `login.xml`. 

This `login.xml` file will be the "skeleton" of your project where we fill in the required fields which will then be displayed onto the `login.en.shtml` file. 

4. Copy and paste the following code into `login.xml`:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE token  [
        <!ENTITY style SYSTEM "../shared.css">
        <!ENTITY login.en SYSTEM "login.en.shtml">
        ]>
<ts:token xmlns:ts="http://tokenscript.org/2019/10/tokenscript"
          xmlns="http://www.w3.org/1999/xhtml"
          xmlns:xml="http://www.w3.org/XML/1998/namespace"
          xsi:schemaLocation="http://tokenscript.org/2019/10/tokenscript http://tokenscript.org/2019/10/tokenscript.xsd"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          custodian="false"
>
    <ts:name>
        <ts:string xml:lang="en">ENTER A TITLE HERE</ts:string>
    </ts:name>
    <ts:contract name="CONTRACT_NAME">
        <!-- test account -->
        <ts:address network="1">0xF5DCe57282A584D2746FaF1593d3121Fcac444dC</ts:address>
    </ts:contract>
    <ts:origins>
        <ts:ethereum contract="CONTRACT_NAME"/>
    </ts:origins>

    <ts:cards>
        <ts:token-card>
            <ts:view-iconified/>
            <ts:view>
            </ts:view>
        </ts:token-card>

        <!-- Creating action buttons for our TokenScript. -->
        <ts:action>
            <ts:name>
                <ts:string xml:lang="en">NAME_OF_BUTTON</ts:string>
            </ts:name>
            <ts:transaction>
            <!-- For the sake of this exercise, we are utilising the DAI crypto network, however, you're able to experiment and use something else. -->
                <ts:ethereum function="approve" contract="DAI" as="bool">
                    <ts:data>
                        <!-- These are dummy addresses which won't work in real life :)  -->
                        <ts:address>0xF5DCe57282A584D2746FaF1593d3121Fcac444dC</ts:address>
                        <ts:uint256>115792089237316195423570985008687907853269984665640564039457584007913129639935</ts:uint256>
                    </ts:data>
                </ts:ethereum>
            </ts:transaction>
            <!-- These reference a style sheet and a view page in xml -->
            <style type="text/css">&style;</style>
            <ts:view xml:lang="en">&login.en;</ts:view> 
        </ts:action>


    </ts:cards>

    <ts:attribute-types>
        <ts:attribute-type id="allowance" syntax="1.3.6.1.4.1.1466.115.121.1.36">
            <ts:origins>
                <ts:ethereum function="allowance" contract="DAI" as="uint">
                    <ts:data>
                        <ts:address ref="ownerAddress"/>
                        <ts:address>0xF5DCe57282A584D2746FaF1593d3121Fcac444dC</ts:address>
                    </ts:data>
                </ts:ethereum>
            </ts:origins>
        </ts:attribute-type>

    </ts:attribute-types>
</ts:token>
```

5. As you can see our xml file now has data and if you read through it I have placed some comments that explain parts of the code. Replace the following variables
with these, or make your own: 

`ENTER A TITLE HERE` => `Login`
`CONTRACT_NAME` => `login`
`NAME_OF_BUTTON` => `Login`

6. Once you're happy with your `login.xml` file, you're ready to start coding the view! Create a new file called `login.en.shtml`. This will be our 'front-end' interface that talks to the `login.xml` for data that you have just created. Head over to that file and copy and paste the code: 

```
<script type="text/javascript"><![CDATA[
    class Token {

        constructor(tokenInstance) {
            this.props = tokenInstance;
            this.props.underlyingToken = this.props.name.replace("c", "");
        }

        mustEnableFirstPage(APR, cTokenBalance, tokenBalance) {
            window.onConfirm = function() { window.close() };
            return`
            <div class="ui container">
                <div class="ui segment">
                    <span><bold><h3>Logged in</h3></bold></span>
                </div>
            </div>
            `
        }
    
        render() {

            try {
                if(this.props.allowance <= 0) {
                    return this.mustEnableFirstPage();
                }
            } catch {
                //this would hit if it is cETH which does not have this requirement and so does not have the allowance attribute
            }
            window.onConfirm = function() { window.close() };
            return`
            <div class="ui container">
              <div class="ui segment">
                <span><bold><h3>TITLE</h3></bold></span>
                <h3 id="message"></h3>
                    <div id="inputBox">
                        <bold><h3>Username</h3></bold>
                            <span><input id="username" type="string"></span>
                        </div>
                        <div id="inputBox">
                            <bold><h3>Password</h3></bold>
                            <span><input id="password" type="password"></span>
                        </div>
              </div>
            </div>

    `;
        }
    }
    
    web3.tokens.dataChanged = (oldTokens, updatedTokens) => {
    const currentTokenInstance = web3.tokens.data.currentInstance;
    document.getElementById('root').innerHTML = new Token(currentTokenInstance).render();
};
    
    ]]></script>
    <div id="root"></div>
    
```

Let's explain the code a bit. 

So this code renders html using javascript. You're able to use basic html in the `render()` method such as forms and divs etc. 

One thing to note is that This code doesn't handle smart contract logic. This page is the view, which handles styling and what is being displayed in the TokenScript. 

## Signing the TokenScript.

7. Drag the `MakeFile` into your directory from the `/defi` folder.

8. Navigate to your working directory in your terminal and run `make login.canonicalized.xml` to create a `canonicalized.xml file`.

9. Observe the terminal. You'll see some output being made that builds the Tokenscript file. The main thing is that it says "login.canonicalized.xml - valid".

10. You're now able to AirDrop or move the file into your AlphaWallet app or folder.

### Debugging when signing
Some tips on debugging and moving the file to your phone and on the AlphaWallet app.

#### Debugging on iOS
After cloning the source code you can run it with either the emulator or your own phone via xcode or AppCode. If you are using the emulator, simply copy the path it prints when loading up in xcode and cd into it. Once there you shoud see a directory called 'assetDefinitionsOverrides', simply drop the file into this directory.

If you are using your real device, simply airdrop the canonicalized.xml file or drop the xml file with its three layout files `(shared.css, enter.en.shtml & token.en.shtml).`

#### Debugging on Android
As with iOS you can either use your own device or the emulator. 
- Run on Android Studio/Intellij and use your device or the emulator. 
- In the AlphaWallet settings choose 'enable dev override', allow the app access to file structure then drag and drop the file into the AlphaWallet directory at root or upload to sdcard/AlphaWallet using the Device File Explorer. 
- If the 'enable dev override' is not chosen inside the app, it will not be able to pick up files in this directory.

## Publishing your Tokenscript
To test your current .tsml file, you can simply drop in onto your device with the instructions provided by step 3. As of now, we are serving signed tsml files from a server that the device can pull from to load into it's corresponding token card.

If you would like to publish your own tsml file to run on this server, create a PR in (this)[https://github.com/AlphaWallet/TokenScript-Repo] repo and we will review it.

