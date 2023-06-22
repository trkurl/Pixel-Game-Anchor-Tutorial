<a href="https://www.rust-lang.org/" target="_blank" rel="noreferrer"><img src="https://raw.githubusercontent.com/danielcranney/readme-generator/main/public/icons/skills/rust-colored.svg" width="36" height="36" alt="Rust" /></a>  


 // ***PIXEL GAME TUTORIAL*** //  
 -------------------------------------
 
![Adsız](https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/17d58330-aa0d-4662-b2fe-aa6805da4ada)

------------------------------------------------------------------------------------------------------
**Tutorial Level : Intermediate**

**Time: 2 Hours**

**This tutorial is for intermediate developers. However, every step is explained in detail. Don't be afraid to experiment.**


**Contents:**

**1-Introduction**

**2-Setup**

**3-The Program**

**4-The UI**

**5-Deploy to Devnet**

-------------------------------------------------------------------------------------------------------------------------------

What we’re building
--------------------
We’re going to build a fun little collaborative pixel art app. It’ll let us get into the nitty gritty of storing and updating data on chain in Solana, and using events to make our app real time. All the canvas state will be stored on-chain. At the end you’ll have a canvas where you can draw with frens!

![26](https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/350a3c65-4401-4e54-a0b0-dce9397485b7)


About Solana
------------------

Solana is a super fast blockchain with super low fees. It’s perfect for this sort of app, where we’re making lots of state changes (drawing on our canvas!)

We need it to be fast so our canvas updates super fast. We’re not gonna be waiting like 10 seconds for something to happen!

We need it to be cheap so our users aren’t worrying about fees. None of that $50 gas nonsense here!

Feel free to check out their website to find out more about Solana!(https://solana.com/tr)

About Anchor
---------------

Anchor is a framework for building awesome Solana apps. It makes it much easier to write secure programs on Solana, and saves us writing a whole bunch of code ourselves. It also provides awesome tools to help us test and deploy our app.

It also helps us write the frontend of our app. It generates Typescript types for us for all our data, and gives us a really nice API for reading our program’s state and calling its instructions.

In short: it’s the easiest way to write Solana apps!

Let's get started! 🚀

Setup
------------
We’re going to start by getting a bunch of stuff installed to develop Solana programs locally. We’ll be writing our program in Rust, using a really awesome framework called Anchor. Don’t worry if that doesn’t make any sense yet, we’ll walk through each step together.

As a heads up, if you're on Windows then you're best using WSL - it tends to work better.
First up we’re going to install Rust, a pretty neat programming language. It’s cool if you’ve never used Rust before - to be honest, we don’t really use a huge amount of it here. If you know Javascript you’ll be fine! But we do use its compiler and a bunch of awesome tooling. So let’s get that installed first.

```
Already have Rust installed?
Check your current version using rustc --version. Make sure you have at least rustc 1.62.0. If you’re on an older version, you can run rustup update to update it.
```

Head to https://www.rust-lang.org/tools/install. Depending on your OS (Windows, macOS or Linux) you’ll see recommended install instructions. We don’t need any customizations, just follow the instructions for your OS and accept the defaults.

You might need to restart your terminal to get all the cool stuff you just installed available!

Once it’s installed check the version:
```
$ rustc --version
rustc 1.62.0 (a8314ef7d 2022-06-27)
```
Yours will probably say something slightly different, just make sure it’s at least 1.62.0 so it’s no older than what I’m using. rustc is the Rust compiler BTW. We won’t really be using these Rust tools directly, but Anchor relies on them being available.

You won’t need to change Rust versions in this tutorial, but just so you know, you can update to the latest version any time using rustup update.

Install Solana CLI
--------------------

The next thing to install is the Solana CLI, which lets us interact with the Solana blockchain (and do crazy things like run our own one!) from the comfort of our own terminal.
```
Already have Solana CLI installed?
Check your current version using solana --version. Make sure you have at least 1.10.31. If you’re on an older version, you can run solana-install update to update it.
```
Head to https://docs.solana.com/cli/install-solana-cli-tools#use-solanas-install-tool. There are separate instructions for Windows and MacOS/Linux. The command will include a version number, just use the one on the site to get the current recommended version.

After the install it might show a command that you need to copy/paste before the Solana CLI tools will be available.

Once it’s installed check the version:
```
$ solana --version
solana-cli 1.10.31 (src:77a40cd8; feat:4192065167)
```
Generate a Solana keypair
----------------------------

Now that we’ve got Solana CLI installed, we’re going to use it to generate a keypair. A keypair is just a public key and a private key. The public key can be given to other people or to apps, but only the private key allows making payments from the account. BTW account and wallet are other terms you’ll hear, you can just think of them as being a keypair. Every interaction with Solana is done by an account, so Anchor needs us to have one configured.

If you already have a keypair locally, you don’t need to do anything. We can use it as-is, no magic here!

Generate it with solana-keygen new:
```
$ solana-keygen new
Generating a new keypair

For added security, enter a BIP39 passphrase

NOTE! This passphrase improves security of the recovery seed phrase NOT the
keypair file itself, which is stored as insecure plain text

BIP39 Passphrase (empty for none):

Wrote new keypair to /Users/anchor-tutorial/.config/solana/id.json
=======================================================================
pubkey: 7uw6aVF52CW8yWKfkMeAS2jukkSc2YBogGMQRqV4wAAu
=======================================================================
Save this seed phrase and your BIP39 passphrase to recover your new keypair:
<snip!> (never share this)
=======================================================================
```
The BIP39 passphrase is optional. We’re only going to use this account locally so I didn’t use one, but you should for a real account!

There are two interesting parts of this output. Firstly it tells us where it’s written the keypair: /Users/anchor-tutorial/.config/solana/id.json in my case. There’s nothing really special here, it’s just a JSON file holding an array of small numbers, which represent bytes. A byte is just a number from 0-255. In Javascript land, it’s a Uint8Array. That byte array represents the private key, which the public key can be derived from.

The second interesting output is the public key, which is how the keypair will be referred to. Each public key uniquely identifies a single Solana account.

All the Solana CLI commands will use this keypair by default now. Let’s take a quick look at the CLI we just installed.

First we can confirm it’s using the address we generated:
```
$ solana address
7uw6aVF52CW8yWKfkMeAS2jukkSc2YBogGMQRqV4wAAu
```
Now let’s check our SOL balance:
```
$ solana balance
0 SOL
```

Unsurprisingly our new account doesn’t have any. Let’s switch to devnet :
```
$ solana config set --url devnet
Config File: /Users/anchor-tutorial/.config/solana/cli/config.yml
RPC URL: https://api.devnet.solana.com
WebSocket URL: wss://api.devnet.solana.com/ (computed)
Keypair Path: /Users/anchor-tutorial/.config/solana/id.json
Commitment: confirmed

$ solana balance
0 SOL
```
Still no SOL! Let’s get some:
```
$ solana airdrop 2
Requesting airdrop of 2 SOL

Signature: 34gtEy4rxXMengtTUgCYCcMnTru6AYpnbFyStCFYFS5QVpcNZ4Y66Vk4mXcf5ALUKzU5FGuEiymSGL74SXdETVb8

2 SOL
```
Now we have some SOL.. on devnet. Unsurprisingly but sadly, that airdrop doesn’t work on mainnet!

If all of that worked then you have a Solana CLI ready to go!

Install Yarn
----------------
Yarn is a package/project manager for Javascript projects. You might already have it installed, lots of JS projects use it. If not head to https://yarnpkg.com/getting-started/install and follow the instructions. If you do already have it you might want to run yarn set version stable to update to the latest version. I’m using 3.2.1 so if you have any issues make sure you’re on at least that version.

Install Anchor
--------------------

Now that we’ve installed all its prerequisites we can install Anchor itself! Anchor is a lot of things:

A CLI to initialise a new project (kinda like create-next-app or create-react-app etc)

A tonne of Rust helpers to make it way easier to write Solana programs

A CLI to build, test and deploy our programs to the Solana blockchain

A nice Typescript SDK to help us write an app that interacts with our deployed program

When we talk about installing Anchor, we’re referring to its CLI.
```
Already have Anchor CLI installed?


Check your current version using anchor --version. Make sure you have at least 0.25.0

If you need to update it use avm:

$ avm install latest
$ avm use latest
```
Head to https://www.anchor-lang.com/docs/installation#anchor and follow the instructions to install avm. That’s the Anchor version manager, which just installs versions of Anchor (and lets you change to different versions for different projects). The instructions will use cargo, which is the rust package manager. You installed that with Rust.

Once you have avm you can use it to install the latest version of Anchor:
```
$ avm install latest
$ avm use latest
```
Make sure that installed at least version 0.27.0 of anchor:
```
$ anchor --version
anchor-cli 0.27.0
```
We won’t need to change Anchor versions during this tutorial, but you can use the avm instructions above at any time to install + use the latest version of Anchor.

Once we have Anchor installed we can give the terminal a break - nothing else to install for now!

Install a Solana browser wallet
-----------------------------------

A Solana browser wallet is a browser extension that can be used to log in to a Solana account, and will allow using that account to connect to and interact with Solana apps. The big picture here is that we’re going to be building a Solana app.. so we need a browser wallet to connect to it!

If you already have a browser wallet, nothing to do!

Otherwise I’d recommend using Phantom (https://phantom.app/download) or Solflare (https://solflare.com/download). Install the extension and follow the instructions to create a new account. Don’t worry that this isn’t the same as the keypair we set up locally - you can have as many accounts as you like.

Starter code
----------------------

Awesome! We’ve set up a whole bunch of stuff and we’re ready to get to coding!

I’ve included some starter code for the frontend app, but we’ll write all the solana stuff from scratch together.

Head to the starter code repo, hit fork to make your own copy and then clone it locally:
*********************************************************************************************

Make sure you have the start branch checked out

If you browse to the frontend/ directory you should see a pretty standard NextJS app. You can run it:
```
# install dependencies
$ npm install

# run the app
$ npm run dev
```
Browse to localhost:3000 and you should see the app running with most of the frontend layout in place:

<img width="1190" alt="4" src="https://github.com/Yusufcihan1/gametutorial/assets/50721899/bdc899bf-b1e0-4153-91d4-2e9aab30f718">

Feel free to check out the starter code if you’re interested. But for now, in the next lesson we’re going to get to writing our program! Later we’ll loop back and connect the frontend up to it.

Let’s start writing some Anchor code!

In the repo root (so outside the frontend directory), run anchor init --no-git draw-with-frens. This will create a new directory draw-with-frens where we’re going to do all our Anchor stuff. We used the --no-git parameter because we’re already in a git repo, we don’t need a weird nested one!

```
$ anchor init --no-git draw-with-frens
yarn install v1.22.15
warning package.json: No license field
warning ../../package.json: No license field
info No lockfile found.
warning No license field
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
warning "@project-serum/anchor > @solana/web3.js > react-native-url-polyfill@1.3.0" has unmet peer dependency "react-native@*".
[4/4] 🔨  Building fresh packages...
success Saved lockfile.
✨  Done in 9.48s.
draw-with-frens initialized
```
Don’t worry too much about exactly what that generated, we’ll explain everything we need as we go. The two main directories we’re going to care about are programs/draw-with-frens which is where we’re going to actually write our program, and tests/ where we’ll write our Typescript tests.

Both of these have default generated code, feel free to have a look through it if you’re interested. Again we’ll explain everything as we go though :)

One thing before we continue, you can delete the generated empty app/ directory if you like. Often you’ll find the frontend for an Anchor project in that directory, but I prefer to keep them a bit more separate, so we have our frontend/ directory for that.

Now in our Anchor directory let’s run anchor test to check everything is set up correctly:
```
$ anchor test
BPF SDK: /Users/callummcintyre/.local/share/solana/install/releases/beta-90ee0ba19c2e6cd4a9ec967d472bdea73ccbc661/solana-release/bin/sdk/bpf
cargo-build-bpf child: rustup toolchain list -v
cargo-build-bpf child: cargo +bpf build --target bpfel-unknown-unknown --release
warning: unused variable: `ctx`
 --> programs/draw-with-frens/src/lib.rs:9:23
  |
9 |     pub fn initialize(ctx: Context<Initialize>) -> Result<()> {
  |                       ^^^ help: if this is intentional, prefix it with an underscore: `_ctx`
  |
  = note: `#[warn(unused_variables)]` on by default

warning: `draw-with-frens` (lib) generated 1 warning
    Finished release [optimized] target(s) in 0.23s
cargo-build-bpf child: /Users/callummcintyre/.local/share/solana/install/releases/beta-90ee0ba19c2e6cd4a9ec967d472bdea73ccbc661/solana-release/bin/sdk/bpf/dependencies/bpf-tools/llvm/bin/llvm-readelf --dyn-symbols /Users/callummcintyre/projects/anchor-tutorial/draw-with-frens/target/deploy/draw_with_frens.so

To deploy this program:
  $ solana program deploy /Users/callummcintyre/projects/anchor-tutorial/draw-with-frens/target/deploy/draw_with_frens.so
The program address will default to this keypair (override with --program-id):
  /Users/callummcintyre/projects/anchor-tutorial/draw-with-frens/target/deploy/draw_with_frens-keypair.json

Found a 'test' script in the Anchor.toml. Running it as a test suite!

Running test suite: "/Users/callummcintyre/projects/anchor-tutorial/draw-with-frens/Anchor.toml"

yarn run v1.22.15
warning package.json: No license field
warning ../../package.json: No license field
$ /Users/callummcintyre/projects/anchor-tutorial/draw-with-frens/node_modules/.bin/ts-mocha -p ./tsconfig.json -t 1000000 'tests/**/*.ts'


  draw-with-frens
Your transaction signature 2vuvmnbzURQKgsXXJo7eVsHUaThsxVsf2TzsRHZCtZcSdPDLge5XhDM2ru3FpkrfuxLHJBSvmbNjcPir3o9Ktse3
    ✔ Is initialized! (395ms)


  1 passing (399ms)

✨  Done in 6.37s.
```
If you see that test passing at the bottom then all is good! Don’t worry about that warning, that’s just because the generated starter code doesn’t do very much and doesn’t make use of variables. Our code will do a bit more :)

Anchor is actually doing a lot for us when we run anchor test. It compiles all our Rust code, which is where that warning comes from. It runs a local Solana validator, basically it runs a brand new instance of Solana on your dev machine. We installed that with those Solana CLI tools. It deploys the program we’ve built to that local instance. And then it runs our tests, which talk to that local instance and interact with our program. We’re going to see a lot more of anchor test!

Our first instruction
---------------------------

Let’s jump into some Rust code. We’re going to be working in programs/draw-with-frens/src/lib.rs. First we’ll talk through the generated starter code in that file, just so we have an idea of what the pieces are and how they fit together.

You should have something like this:

```
use anchor_lang::prelude::*;

declare_id!("Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkg476zPFsLnS");

#[program]
pub mod draw_with_frens {
    use super::*;

    pub fn initialize(ctx: Context<Initialize>) -> Result<()> {
        Ok(())
    }
}

#[derive(Accounts)]
pub struct Initialize {}
```

Let’s step through it together.

The first line is an import statement. We’re importing everything from the prelude of anchor_lang. Rust libraries often define a prelude, which lets you import all their commonly used components together. So we’re importing all the main Anchor things so we can use them.

The next line is giving the program an ID, which is just a Solana public key. It looks like your wallet address. Everything in Solana is an account and is referenced with one of these addresses. This default address is hardcoded, each Anchor project you generate will have the same one. We’ll have to update this later to be unique but for now, we don’t need to worry about it.

#[program] is a Rust macro provided by Anchor. We’re telling Anchor that pub mod draw_with_frens is the Rust module that contains the instructions for our program. A Solana transaction is made up of instructions, so any Solana transaction will be able to call any of our program’s instructions.

Then inside the module we have our first example of an instruction:

```
pub fn initialize(ctx: Context<Initialize>) -> Result<()> {
    Ok(())
}

```
This is a Rust function. It takes as input an object of type Context<Initialize> and outputs an object of type Result<()>.

Every Solana instruction requires the list of accounts that it operates on. That’s what’s passed in Context<Initialize>. We’ll also see some other stuff in ctx but the <Initialize> is telling us the accounts list that is present.

And every Solana instruction can either succeed or fail with an error. Note that Solana instructions don’t output a result to the caller. So we return Result<()>, which means we succeed with an empty output or we error. If any instruction in a transaction does error, then the whole transaction is aborted and rolled back.

This default instruction does nothing, it just returns. Ours will be a bit more interesting!

The final piece of the puzzle is at the bottom:
```
  #[derive(Accounts)]
pub struct Initialize {}
```
This is where we’re defining that list of accounts for the initialize instruction. It’s an empty object, meaning the instruction doesn’t require any accounts. Our more interesting instructions will require accounts.

While this starter code is very small it does cover most of the basics: we write instructions that are functions, and they take as their first argument a Context<ListOfAccounts> object.

Pixel Account
----------------
 The major thing that it doesn’t cover is storing data. If you’ve written a contract for any other chain then this probably works quite differently to what you’re used to.

First, let’s consider exactly what state we need to store.
![5](https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/e04c2542-edb8-46db-80f6-b5a02eeb1c0f)

Each position on our canvas is uniquely identified by an x and y co-ordinate. And each of these can have a color, or it might not have been colored by anybody yet.

One way we could store this would be in a long array: we give each position a unique id and store its color at that position. It’d be a sparse array, which just means there’d be lots of holes where we haven’t colored a space yet.

But we can do better than that. In Solana we pay for storage. When we want to store some bytes of data on the blockchain we create an account with a specific size, and we deposit enough SOL into that account to make it rent exempt. In practice that just means that the data will stay there for as long as the SOL stays there. The bigger the account, the more SOL we have to deposit to keep it open.  
```
For more about rent, check out the Solana docs: https://docs.solana.com/implemented-proposals/rent

In practice we always make our accounts rent exempt by depositing enough SOL that we’re not charged rent. Anchor will help us with this too.
```
So we don’t want to create an account that can hold 10,000 colors because that’d be expensive. Instead we’re going to have one account per position on our canvas. (0,0) is an account which stores the color blue. (1,0) isn’t initialized since it doesn’t have a color yet, so nobody has had to pay for it. When we want to render the canvas we will just fetch all the pixel accounts, and display color for the ones we have.

Solana and Anchor have some really great support for this way of storing our data, which we’ll see shortly, and Solana is optimized for many accounts storing a little data each.

Before we get there though, let’s create the account that’s going to hold the data for each position on our canvas. Just because it’s shorter and gets the point across we’ll call these a Pixel.

Add the Pixel account to the program:
```
use anchor_lang::prelude::*;

declare_id!("Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkg476zPFsLnS");

#[program]
pub mod draw_with_frens {
    use super::*;

    pub fn initialize(ctx: Context<Initialize>) -> Result<()> {
        Ok(())
    }
}

#[derive(Accounts)]
pub struct Initialize {}

// Define structure of pixel account
#[account]
pub struct Pixel {
    pub pos_x: u8,
    pub pos_y: u8,
    pub col_r: u8,
    pub col_g: u8,
    pub col_b: u8
}
```
This is the structure of the data that we will store on-chain for each pixel. We have an x and y coordinate that tells us where the pixel is on our grid and RGB colors that tell us the color that pixel currently is. So we can make any pixel on our grid any color we like.

Pixel account size
----------------------

When we create a Pixel account we will need to tell the Solana runtime how much space it needs, in bytes. Each of our u8 properties is 1 byte, so that’s 5 bytes for our data. In addition every Anchor account is prefixed with an 8-byte discriminator. This is a unique hash per account, which allows Anchor to determine what the type of the account is. It’s a security feature that means we can be sure that when we write an instruction that expects a Pixel account, that’s the account that will be passed in. If somebody tried to call the instruction with a different account type, it would be rejected since it’d have the wrong discriminator.

So here’s what our account layout is going to look like:
<img width="1123" alt="8" src="https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/2a51814d-2f89-468d-80f7-0c0ce17d8ec4">

There’s the 8-byte discriminator, and then our 5 u8 values. The total size is 13 bytes.

Let’s update our code to include this:
```
// Define structure of pixel account
#[account]
pub struct Pixel {
    pub pos_x: u8,
    pub pos_y: u8,
    pub col_r: u8,
    pub col_g: u8,
    pub col_b: u8,
}

// Define size of pixel account
const DISCRIMINATOR_LENGTH: usize = 8;
const POS_LENGTH: usize = 1;
const COL_LENGTH: usize = 1;

impl Pixel {
    const LEN: usize = DISCRIMINATOR_LENGTH + (2 * POS_LENGTH) + (3 * COL_LENGTH);
}
```
It’s good practice to split it into variables and show how it’s calculated in case the fields get updated later. But all we’re doing is 8 + 2 + 3.

Note that in Rust structs can only store data, they don’t have any functionality. So we’re using impl Pixel to add some functionality. We can reference this with Pixel::LEN, which we’ll see shortly.

BTW your IDE might be yelling at you about how LEN isn’t being used yet, we’ll fix that soon!

Create a pixel
---------------------------

The first instruction we’re going to write is going to create one of these pixel accounts. The first time a user wants to assign a color to a specific pixel (say the top-left one, with x=0 and y=0) they will call this instruction, creating a Pixel account for it with their intended color. When a user wants to update a pixel that has already been created, they’ll use a different instruction that just updates the existing account.

So in short, we want to write an instruction that takes an x and y coordinate, and RGB colors, and then creates a Pixel account with those values.

The key thing to know about Solana instructions is that they must define the public key, or address, of every account that they read or write. This is basically the main thin that makes Solana super fast: it can optimize transactions using this information. So what are the accounts we’ll need for our instruction? First there’s the Pixel one which we’re going to initialize. This might be a bit confusing but we need to pass the address of that account in, and then our instruction will put Pixel data into it. We’ll see exactly how we know which address that should be shortly, but for now we just need to know that it’ll be passed in. There’s also the user submitting the transaction, who is going to pay for the storage for that account. They will need to have signed the transaction that contains this instruction to approve that payment.

We need to define this list of accounts, like this:
```
#[derive(Accounts)]
pub struct Initialize {}

#[derive(Accounts)]
pub struct CreatePixel<'info> {
    pub pixel: Account<'info, Pixel>,
    pub user: Signer<'info>,
}
```

BTW we’re going to delete Initialize soon so don’t worry about it.

So this is a little more complex. First we have this new generic-looking thing on the struct: CreatePixel<'info>. That’s a rust lifetime, and TBH you don’t really need to know about them in any depth for this tutorial but you can read about them in the Anchor book if you’re interested. It basically tells Rust how long a variable will stay available for, in this case everything with <'info> will be available at the same time.

I’ll come back to pixel in a moment, let’s look at the second account. This is the user who is creating the pixel. They’re required to be a Signer, which means that Anchor will make sure they’ve signed the transaction and error if they haven’t.

Now let’s look at the pixel account. It has the type Account<'info, Pixel>. So Account is part of the Anchor framework and it’s going to make sure the account passed in is owned by our program and has the Pixel type. But remember that at this point we’re initializing that account - it’s literally just a public key. It doesn’t have an owner or any data. So we need to give Anchor some more information:
```
#[derive(Accounts)]
pub struct CreatePixel<'info> {
    #[account(init, payer=user, space=Pixel::LEN)]
    pub pixel: Account<'info, Pixel>,
    #[account(mut)]
    pub user: Signer<'info>,
    pub system_program: Program<'info, System>,
}
```

The first thing I added was an account init attribute for the pixel account. This is telling Anchor that it’s going to need to initialize the pixel account, the user will be paying for that, and the space will be Pixel::LEN which we added before.

If we add just that line then the compiler will complain: the init constraint requires the system program. This is the Solana program that is responsible for creating accounts (among other stuff). If we need to call the system program then it needs to be in our list of accounts! Anchor has a nice type for this: Program<'info, System> That’ll make sure it’s the correct account being passed in.

And now the compiler complains again: the payer of init must be mutable. That makes sense - we need to charge the user a small amount of SOL to initialize the account. So we also need to add the #[account(mut)] attribute to the user.

And now the compiler is happy! BTW this is a common theme: Rust has a really powerful compiler and Anchor takes full advantage of it to help us out.

We will have to loop back and update this later, but let’s carry on for now and start writing our instruction to get a better idea what’s going on here.

Here’s the program with a simple instruction added:
```
use anchor_lang::prelude::*;

declare_id!("Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkg476zPFsLnS");

#[program]
pub mod draw_with_frens {
    use super::*;

    pub fn create_pixel(
        ctx: Context<CreatePixel>,
        pos_x: u8,
        pos_y: u8,
        init_col_r: u8,
        init_col_g: u8,
        init_col_b: u8,
    ) -> Result<()> {
        let pixel = &mut ctx.accounts.pixel;
        pixel.pos_x = pos_x;
        pixel.pos_y = pos_y;
        pixel.col_r = init_col_r;
        pixel.col_g = init_col_g;
        pixel.col_b = init_col_b;

        Ok(())
    }
}

#[derive(Accounts)]
pub struct CreatePixel<'info> {
    #[account(init, payer=user, space=Pixel::LEN)]
    pub pixel: Account<'info, Pixel>,
    #[account(mut)]
    pub user: Signer<'info>,
    pub system_program: Program<'info, System>,
}

// Define structure of pixel account
#[account]
pub struct Pixel {
    pub pos_x: u8,
    pub pos_y: u8,
    pub col_r: u8,
    pub col_g: u8,
    pub col_b: u8,
}

// Define size of pixel account
const DISCRIMINATOR_LENGTH: usize = 8;
const POS_LENGTH: usize = 1;
const COL_LENGTH: usize = 1;

impl Pixel {
    const LEN: usize = DISCRIMINATOR_LENGTH + (2 * POS_LENGTH) + (3 * COL_LENGTH);
}
```
I’ve just added a create_pixel instruction, and removed the Initialize stuff we don’t need any more.

Let’s take a closer look at our instruction:
```
pub fn create_pixel(
        ctx: Context<CreatePixel>,
        pos_x: u8,
        pos_y: u8,
        init_col_r: u8,
        init_col_g: u8,
        init_col_b: u8,
    ) -> Result<()> {
        let pixel = &mut ctx.accounts.pixel;
        pixel.pos_x = pos_x;
        pixel.pos_y = pos_y;
        pixel.col_r = init_col_r;
        pixel.col_g = init_col_g;
        pixel.col_b = init_col_b;

        Ok(())
    }
```
We’re passing in the CreatePixel context, which is that list of accounts we’ve just been working on. We’re also passing in the values we want to set on the Pixel account: the x/y position, and the initial RGB color.

We can access the pixel account on ctx.accounts.pixel. Since we want to update (mutate) it, we need to do a mutable borrow, which is what &mut does. This is just telling the Rust compiler that we’re going to be updating this struct.

Once we have that, we just set the fields on it! Nice and straightforward :)

We’ve written our first instruction: we can call this and create a new pixel account with the expected values. Next we’re going to write some tests for it, and use them to iterate on it. Anchor makes this workflow really nice and it’s definitely worth taking advantage of.

Testing and iterating
----------------------------

If you run anchor test at this point then it’ll fail, because there’s a default test that uses that initialize stuff we removed. So we’ll remove that test and add our own.

The tests are in tests/draw-with-frens.ts

Let’s add our first test:
```
import * as anchor from "@project-serum/anchor";
import { Program } from "@project-serum/anchor";
import { web3 } from "@project-serum/anchor";
import { DrawWithFrens } from "../target/types/draw_with_frens";
import { assert } from "chai"

describe("draw-with-frens", () => {
  // Configure the client to use the local cluster.
  const anchorProvider = anchor.AnchorProvider.env();
  anchor.setProvider(anchorProvider);

  const program = anchor.workspace.DrawWithFrens as Program<DrawWithFrens>;

  it("Can create a pixel", async () => {
    const pixelKeypair = web3.Keypair.generate()

    await program.methods
      .createPixel(10, 10, 0, 0, 255)
      .accounts({
        pixel: pixelKeypair.publicKey,
        user: anchorProvider.wallet.publicKey,
        systemProgram: web3.SystemProgram.programId,
      })
      .signers([pixelKeypair])
      .rpc()

    const storedPixel = await program.account.pixel.fetch(pixelKeypair.publicKey)
    assert.equal(storedPixel.posX, 10)
    assert.equal(storedPixel.posY, 10)
    assert.equal(storedPixel.colR, 0)
    assert.equal(storedPixel.colG, 0)
    assert.equal(storedPixel.colB, 255)
  });
});
```
Our tests are written in Typescript and use anchor in the same way that we’ll use it in our web app.

When we run anchor test it runs a local Solana network, and also initializes a wallet with some SOL. When we use anchor.setProvider here we’re configuring it to use that local network. And then we can use anchorProvider.wallet to access that wallet that contains SOL. This means that wallet can pay fees.

Let’s step through that test step-by-step.
```
const pixelKeypair = web3.Keypair.generate()
```

We’re creating a keypair, which is a public key + private key pair. Note that web3 here is the same library as @solana/web3.js, anchor just exports it. So if you’re familiar with that library you’ll recognise things from it. Don’t worry if you’ve not seen it before though.

```
await program.methods
  .createPixel(10, 10, 0, 0, 255)
  .accounts({
    pixel: pixelKeypair.publicKey,
    user: anchorProvider.wallet.publicKey,
    systemProgram: web3.SystemProgram.programId,
  })
  .signers([pixelKeypair])
  .rpc()
```

Anchor generates a really handy API for us to call the instructions that we’ve defined. We call our create_pixel instruction (Anchor takes care of changing the name to match Typescript conventions for us) with x=10, y=10, and a colour of (0, 0, 255).

We pass it the accounts that we’ve discussed earlier: the pixel account (public key of that keypair we generated), the user (that’s that anchorProvider.wallet) and the system program.

When we call anchor with .rpc() at the end, that anchorProvider.wallet will sign the transaction before it’s sent. So we don’t need to have the user sign it explicitly. However we do need the pixel account to be a signer too, which allows it to be initialized.

Note that .rpc() will sign the transaction as mentioned, and also send it to the blockchain.

Run anchor test and make sure that test passes before continuing.

Okay, let’s add another test:
```
// Updated import
import * as anchor from "@project-serum/anchor";
import { AnchorError, Program } from "@project-serum/anchor";
import { web3 } from "@project-serum/anchor";
import { DrawWithFrens } from "../target/types/draw_with_frens";
import { assert } from "chai"

// New test to add inside the describe block
it("Does not allow creating a pixel out of bounds", async () => {
  const pixelKeypair = web3.Keypair.generate()

  await program.methods
    .createPixel(0, 200, 0, 0, 255)
    .accounts({
      pixel: pixelKeypair.publicKey,
      user: anchorProvider.wallet.publicKey,
      systemProgram: web3.SystemProgram.programId,
    })
    .signers([pixelKeypair])
    .rpc()
    .then(
      () => Promise.reject(new Error('Expected to error!')),
      (e: AnchorError) => {
        console.log(e.errorLogs)
      }
    );
})
```
This test is creating a pixel that is outside of our canvas: the canvas is 100 x 100 so there’s no (0, 200). If you run anchor test again then unsurprisingly this test will fail with “Error: Expected to error!”. We didn’t add any validation for our inputs.

Let’s go back to our program and update it to validate the inputs.

First we need to add some Anchor errors that we can return:

```
use anchor_lang::prelude::*;

declare_id!("Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkg476zPFsLnS");

#[program]
pub mod draw_with_frens {
    use super::*;

    pub fn create_pixel(
        ctx: Context<CreatePixel>,
        pos_x: u8,
        pos_y: u8,
        init_col_r: u8,
        init_col_g: u8,
        init_col_b: u8,
    ) -> Result<()> {
        let pixel = &mut ctx.accounts.pixel;
        pixel.pos_x = pos_x;
        pixel.pos_y = pos_y;
        pixel.col_r = init_col_r;
        pixel.col_g = init_col_g;
        pixel.col_b = init_col_b;

        Ok(())
    }
}

#[derive(Accounts)]
pub struct CreatePixel<'info> {
    #[account(init, payer=user, space=Pixel::LEN)]
    pub pixel: Account<'info, Pixel>,
    #[account(mut)]
    pub user: Signer<'info>,
    pub system_program: Program<'info, System>,
}

// Define structure of pixel account
#[account]
pub struct Pixel {
    pub pos_x: u8,
    pub pos_y: u8,
    pub col_r: u8,
    pub col_g: u8,
    pub col_b: u8,
}

// Define size of pixel account
const DISCRIMINATOR_LENGTH: usize = 8;
const POS_LENGTH: usize = 1;
const COL_LENGTH: usize = 1;

impl Pixel {
    const LEN: usize = DISCRIMINATOR_LENGTH + (2 * POS_LENGTH) + (3 * COL_LENGTH);
}

#[error_code]
pub enum ErrorCode {
    #[msg("The given X co-ordinate is not between 0-99")]
    InvalidXCoordinate,
    #[msg("The given Y co-ordinate is not between 0-99")]
    InvalidYCoordinate,
    #[msg("The given R color is not between 0-255")]
    InvalidRColor,
    #[msg("The given G color is not between 0-255")]
    InvalidGColor,
    #[msg("The given B color is not between 0-255")]
    InvalidBColor,
}
```

I’ve added them right at the bottom there. Each error code (eg. InvalidXCoordinate) has a #[msg] attribute, which gives a nice readable error message. The whole enum has #[error_code] which is another nice Anchor helper that makes it easy to use these errors.

Now we can add validation to our create_pixel function:
```
#[program]
pub mod draw_with_frens {
    use super::*;

    const MIN_POS: u8 = 0;
    const MAX_POS: u8 = 99;
    const MIN_COL: u8 = 0;
    const MAX_COL: u8 = 255;

    pub fn create_pixel(
        ctx: Context<CreatePixel>,
        pos_x: u8,
        pos_y: u8,
        init_col_r: u8,
        init_col_g: u8,
        init_col_b: u8,
    ) -> Result<()> {
        // Validation
        if pos_x < MIN_POS || pos_x > MAX_POS {
            return Err(error!(ErrorCode::InvalidXCoordinate));
        }

        if pos_y < MIN_POS || pos_y > MAX_POS {
            return Err(error!(ErrorCode::InvalidYCoordinate));
        }

        if init_col_r < MIN_COL || init_col_r > MAX_COL {
            return Err(error!(ErrorCode::InvalidRColor));
        }

        if init_col_b < MIN_COL || init_col_b > MAX_COL {
            return Err(error!(ErrorCode::InvalidBColor));
        }

        if init_col_g < MIN_COL || init_col_g > MAX_COL {
            return Err(error!(ErrorCode::InvalidGColor));
        }

        // Set values
        let pixel = &mut ctx.accounts.pixel;
        pixel.pos_x = pos_x;
        pixel.pos_y = pos_y;
        pixel.col_r = init_col_r;
        pixel.col_g = init_col_g;
        pixel.col_b = init_col_b;

        Ok(())
    }
}
```
Remember how our function returns Result<()>? That means it can return that Ok(()) thing we’ve already seen, or it can return an error. We return an error from one of our error codes with return Err(error!(ErrorCode::InvalidXCoordinate)).

Now let’s run our test again and see what we get:

```
draw-with-frens
    ✔ Can create a pixel (170ms)
[
  'Program log: AnchorError occurred. Error Code: InvalidYCoordinate. Error Number: 6001. Error Message: The given Y co-ordinate is not between 0-99.'
]
    ✔ Does not allow creating a pixel out of bounds


  2 passing (205ms)
```
Nice, it passed! We’re logging the errorLogs for the transaction, and we can see that it includes both our error code InvalidYCoordinate and our message “The given Y co-ordinate is not between 0-99.”

Let’s update our test to make sure we’re getting the exact error we expect:
```
await program.methods
  .createPixel(0, 200, 0, 0, 255)
  .accounts({
    pixel: pixelKeypair.publicKey,
    user: anchorProvider.wallet.publicKey,
    systemProgram: web3.SystemProgram.programId,
  })
  .signers([pixelKeypair])
  .rpc()
  .then(
    () => Promise.reject(new Error('Expected to error!')),
    (e: AnchorError) => {
      // Log is eg. `AnchorError occurred. Error Code: InvalidYCoordinate. Error Number: 6001. Error Message: The given Y co-ordinate is not between 0-99.`
      assert.ok(e.errorLogs.some(log => log.includes('InvalidYCoordinate') && log.includes('The given Y co-ordinate is not between 0-99.')))
    }
  );
```

Nice! Now let’s add another new test:

```
it("Does not allow creating the same pixel twice", async () => {
    const x = 20
    const y = 20

    const pixelKeypair1 = web3.Keypair.generate()

    // Create the pixel: this should pass
    await program.methods
      .createPixel(x, y, 0, 0, 255)
      .accounts({
        pixel: pixelKeypair1.publicKey,
        user: anchorProvider.wallet.publicKey,
        systemProgram: web3.SystemProgram.programId,
      })
      .signers([pixelKeypair1])
      .rpc()

    const pixelKeypair2 = web3.Keypair.generate()

    // Create the same pixel: this should fail
    await program.methods
      .createPixel(x, y, 0, 0, 255)
      .accounts({
        pixel: pixelKeypair2.publicKey,
        user: anchorProvider.wallet.publicKey,
        systemProgram: web3.SystemProgram.programId,
      })
      .signers([pixelKeypair2])
      .postInstructions([
        // make the transaction unique
        web3.SystemProgram.transfer({
          fromPubkey: anchorProvider.wallet.publicKey,
          toPubkey: anchorProvider.wallet.publicKey,
          lamports: 1,
        })
      ])
      .rpc()
      .then(
        () => Promise.reject(new Error('Expected to error!')),
        (e: web3.SendTransactionError) => {
          console.error(e.logs)
        }
      )
  })
```
In Anchor we can use preInstructions or postInstructions to add extra Solana instructions to the transaction that we’re creating. In this case we’re adding an instruction to send 1 lamport (the smallest fraction of a SOL) to ourself. We need to do this to ensure our transactions are unique. Remember that we’re intentionally sending the same instruction twice. 

Technically this is caused by a bug in Anchor. If you’d like to learn more feel free to check out this Pull Request that will fix the bug: Pull Request #2092 For now though, we need to make our transactions unique if they’re sent around the same time!
Here I’m creating the pixel (20, 20) twice at different addresses. The test is a bit contrived, but you can easily imagine this happening with two different users for example. This shouldn’t be possible, our app won’t know what color a pixel is if there are multiple accounts with the same x/y values.

Note that in Anchor all our tests run sequentially on the local network. So the state is shared between them. That’s a bit different to how we usually try to make unit tests independent, so it can be a bit confusing. In our case the easiest thing is to use a different x/y value for each test.

![9](https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/d24be58e-2b0a-421e-a03e-a17b9384d4a0)

But currently it is possible! If we run anchor test the test fails: I get that “Error: Expected to error!” message. So.. how can we fix this?

We can’t conveniently fetch all the accounts and check their x and y values in our create_pixel instruction, so we can’t de-duplicate it like that.

We need to somehow derive an address for each (x, y) pair and guarantee that that pixel account will be stored at that address. That way it’ll be impossible to have a duplicate: we’d be trying to initialize the same address twice, and that’ll fail.

Okay so let’s say we can write an algorithm that takes x and y and gives us the address to use. We could run that, pass it in, and that’d work right? Well.. almost. The address is passed to our program, which means our program is going to need to verify it. We can’t just trust it - someone could write their own app that talks to our contract and doesn’t follow the rules, and then we’d have the same problem. So how we do generate such an address in our program? That’s where Program Derived Addresses, or PDAs come in.

Program Derived Addresses (PDAs)
--------------------------------------

PDAs are addresses that we derive from a program ID and a set of seeds. So for example in our case we might want to use the x and y value as seeds.

They also have one really critical property: they are off the curve. To understand what that means we need to learn a little bit about how Solana’s cryptography actually works.

In short, Solana (and other chains like Bitcoin and Ethereum) use a cryptography standard called Elliptic Curve Cryptography (ECC). A private key is just a really big number: it’s 32 bytes or 128 bits. A public key can be represented as a point on a curve, and that point is calculated from that really big number. The way the math works means that it’d require an insane amount of time to get from that point on the curve and figure out the massive number that was used to calculate it. And that’s the basis of the security: if you have a public key you can’t figure out the private one!
![10](https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/db8d968a-02b4-4fa4-af6d-6546fb4860b8)

BTW that graphic is just an example, not an actual ECC curve! Importantly every private key can be used to calculate a point on the curve, a public key. These are the wallets we can import into Phantom and things! But not every (x, y) coordinate is on the curve, just like any other graph.

So when we say a PDA is not on the curve, all we mean is that it represents a point that isn’t generated by a private key. But that has a super important security implication: it literally doesn’t have a private key. Nobody can import that address into their wallet app. Nobody can sign transactions as that address. If you were a supercomputer, and you didn’t mind waiting until the heat death of the universe, you could try every private key and you still wouldn’t find one that matches the PDA.

When a PDA has been derived from the program, that program is able to sign for that PDA. Since there’s no private key we can no longer make pixel a signer when we call the instruction. This is really important because it means that the program doesn’t need to know private keys. Even if a program isn’t open source, its bytecode on the blockchain can be decompiled. So there’s no way to safely store a private key in a program’s code.

The algorithm that we use to derive an address from a program ID and a set of seeds has a roughly 50/50 chance of whether the address generated is on or off the curve. So we also add an extra seed, a bump, which starts at 255. Each time an address is generated on the curve, we decrease it by 1 and try again, until we find one that works. There is an extremely small (about 1 in 2^256) chance that we can’t find one and need to change the seeds, but we won’t worry about that here.

Enough theory, let’s jump back into the code and see how this works for real! Remember, our motivation for all this was to find a way to make sure there’s only one account per pixel!

Initializing a PDA
-----------------------

Back in lib.rs we’re going to update our accounts struct:

```
#[derive(Accounts)]
#[instruction(pos_x: u8, pos_y: u8)]
pub struct CreatePixel<'info> {
    #[account(
        init,
        payer = user,
        space = Pixel::LEN,
        seeds = [b"pixel".as_ref(), [pos_x, pos_y].as_ref()],
        bump
    )]
    pub pixel: Account<'info, Pixel>,
    #[account(mut)]
    pub user: Signer<'info>,
    pub system_program: Program<'info, System>,
}
```
We’ve updated the init annotation to include seeds, which will tell Anchor that it’s initializing a PDA here. The first seed is just the word “pixel”, which just namespaces our PDA in case we add more later. And then we have our pos_x and pos_y parameters. The format we need to use for seeds is a bit weird, each seed needs to be an array of bytes. Our u8 parameters are already 1 byte each, so we just put them in an array together. We could also give them an array each, but together ends up being a bit more straightforward. Secondly we add the bump parameter which tells Anchor to find the right bump for us. So it’ll find that first number from 255 down that generates an address off the curve, and then use that address.

The other important new thing here is the #[instruction] annotation, which lets us use the values of pos_x and pos_y passed into the instruction for deriving the account.

And that’s all we need to do to make this address a PDA!

Generating a PDA
------------------------

Now let’s take a look at how our tests change. Here’s the first one:
```
it("Can create a pixel", async () => {
  const x = 10
  const y = 10

  const [pixelPublicKey] = web3.PublicKey.findProgramAddressSync(
    [Buffer.from("pixel"), Buffer.from([x, y])],
    program.programId,
  )

  await program.methods
    .createPixel(x, y, 0, 0, 255)
    .accounts({
      pixel: pixelPublicKey,
      user: anchorProvider.wallet.publicKey,
      systemProgram: web3.SystemProgram.programId,
    })
    .rpc()

  const storedPixel = await program.account.pixel.fetch(pixelPublicKey)
  assert.equal(storedPixel.posX, x)
  assert.equal(storedPixel.posY, y)
  assert.equal(storedPixel.colR, 0)
  assert.equal(storedPixel.colG, 0)
  assert.equal(storedPixel.colB, 255)
});
```
Instead of generating a random public key, we’re generating one using findProgramAddressSync. We’re passing in the same seeds that we have in the program, on the Javascript side they need to be passed as buffers. This outputs a 2-element list, with the first element being the public key and the second being the bump that was used. This means that we could tell Anchor the bump and save it calculating it, but since this instruction is initializing the account that’s not allowed. This is how we guarantee that one set of seeds → exactly one address. So we only use the first element here: pixelPublicKey.

That gets passed in as our pixel account. And notice that the extra signer has gone, the PDA doesn’t need to be (and can’t be, remember it has no private key!) a signer.

Now we need to update our second test, that’s the one with the out-of-bounds pixel position. Try updating it yourself, it should be very similar to the example we just did. If you run anchor test after updating it you should see it passing.

Our third test also needs updating - we didn’t know the right error message to check for so we’re not checking for one yet. Let’s update it to use the PDA address and then we can tighten up its assertion.
```
it("Does not allow creating the same pixel twice", async () => {
  const x = 10
  const y = 10

  const [pixelPublicKey] = web3.PublicKey.findProgramAddressSync(
    [Buffer.from("pixel"), Buffer.from([x, y])],
    program.programId,
  )

  await program.methods
    .createPixel(x, y, 0, 0, 255)
    .accounts({
      pixel: pixelPublicKey,
      user: anchorProvider.wallet.publicKey,
      systemProgram: web3.SystemProgram.programId,
    })
    .postInstructions([
      // make the transaction unique
      web3.SystemProgram.transfer({
        fromPubkey: anchorProvider.wallet.publicKey,
        toPubkey: anchorProvider.wallet.publicKey,
        lamports: 1,
      })
    ])
    .rpc()
    .then(
      () => Promise.reject(new Error('Expected to error!')),
      (e: web3.SendTransactionError) => {
        console.error(e.errorLogs)
      }
    )
})
```
Here are the logs I’m getting:
```
[
  'Program Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkg476zPFsLnS invoke [1]',
  'Program log: Instruction: CreatePixel',
  'Program 11111111111111111111111111111111 invoke [2]',
  'Allocate: account Address { address: 6V4qyzgQ9zdDrjiP74hoaece98gLcRt874JFqTsexrQd, base: None } already in use',
  'Program 11111111111111111111111111111111 failed: custom program error: 0x0',
  'Program Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkg476zPFsLnS consumed 5960 of 200000 compute units',
  'Program Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkg476zPFsLnS failed: custom program error: 0x0'
]
```

Let’s update the test to make sure we get that “already in use” log.
```
it("Does not allow creating the same pixel twice", async () => {
    const x = 20
    const y = 20

    const [pixelPublicKey] = web3.PublicKey.findProgramAddressSync(
      [Buffer.from("pixel"), Buffer.from([x, y])],
      program.programId,
    )

    // Create the pixel: this should pass
    await program.methods
      .createPixel(x, y, 0, 0, 255)
      .accounts({
        pixel: pixelPublicKey,
        user: anchorProvider.wallet.publicKey,
        systemProgram: web3.SystemProgram.programId,
      })
      .rpc()

    // Create the same pixel: this should fail
    await program.methods
      .createPixel(x, y, 0, 0, 255)
      .accounts({
        pixel: pixelPublicKey,
        user: anchorProvider.wallet.publicKey,
        systemProgram: web3.SystemProgram.programId,
      })
      .postInstructions([
        // make the transaction unique
        web3.SystemProgram.transfer({
          fromPubkey: anchorProvider.wallet.publicKey,
          toPubkey: anchorProvider.wallet.publicKey,
          lamports: 1,
        })
      ])
      .rpc()
      .then(
        () => Promise.reject(new Error('Expected to error!')),
        (e: web3.SendTransactionError) => {
          // Log is eg. 'Allocate: account Address { address: 6V4qyzgQ9zdDrjiP74hoaece98gLcRt874JFqTsexrQd, base: None } already in use'
          assert.ok(e.logs.some(log => log.includes(pixelPublicKey.toBase58()) && log.includes('already in use')))
        }
      )
  })
```
As one final test, let’s make sure the address is being correctly checked. This is important because if we don’t have to use findProgramAddressSync and pass in the generated address, then we won’t get the guarantee that we need.
```
it("Does not allow passing an incorrect address", async () => {
  // Generate the PDA for (0, 0)
  const [pixelPublicKey] = web3.PublicKey.findProgramAddressSync(
    [Buffer.from("pixel"), Buffer.from([0, 0])],
    program.programId,
  )

  // Attempt to use it to create (30, 30)
  await program.methods
    .createPixel(30, 30, 0, 0, 255)
    .accounts({
      pixel: pixelPublicKey,
      user: anchorProvider.wallet.publicKey,
      systemProgram: web3.SystemProgram.programId,
    })
    .rpc()
    .then(
      () => Promise.reject(new Error('Expected to error!')),
      (e: web3.SendTransactionError) => {
        // Log is eg. '5NbE1G4B95BMHrz94jLk3Q1GivRgh9Eyj8mtHss3sVZA's signer privilege escalated'
        const expectedError = `${pixelPublicKey.toBase58()}'s signer privilege escalated`
        assert.ok(e.logs.some(log => log === expectedError))
      }
    )
})
```
This test should pass!

With that our first instruction, which initializes a pixel account, is complete. We had to take a bit of a detour and learn about PDAs to make sure there can only be one pixel account at each (x, y) position.

Updating pixels
-----------------------

Our drawing isn’t much fun if we can only use each pixel once. Let’s add a second instruction that lets us update an existing pixel account with a new color. Since this instruction is just updating an existing account, it’ll be quite a bit simpler.

First, we need a new context:
```
#[derive(Accounts)]
#[instruction(pos_x: u8, pos_y: u8)]
pub struct CreatePixel<'info> {
    #[account(
        init,
        payer = user,
        space = Pixel::LEN,
        seeds = [b"pixel".as_ref(), [pos_x, pos_y].as_ref()],
        bump
    )]
    pub pixel: Account<'info, Pixel>,
    #[account(mut)]
    pub user: Signer<'info>,
    pub system_program: Program<'info, System>,
}

#[derive(Accounts)]
pub struct UpdatePixel<'info> {
    #[account(
        mut,
        seeds = [b"pixel".as_ref(), [pixel.pos_x, pixel.pos_y].as_ref()],
        bump
    )]
    pub pixel: Account<'info, Pixel>,
}
```

This time there’s only one account needed: the pixel to be updated. We don’t need to initialize an account, so we don’t need the user to pay. We also don’t need the system program, we only used that to be able to initialize an account. Note that the user (or somebody) will still sign the transaction as the fee payer, but our instruction isn’t concerned with who that is.

The account is mutable, we’re going to update it. Again we pass in the PDA seeds, though this time we can read the pos_x and pos_y from the existing account data instead of having to pass it in to the instruction. For now we’re having Anchor calculate the bump again, we’ll come back to that shortly.

Next we can add our update_pixel instruction using this context:

```
pub fn update_pixel(
    ctx: Context<UpdatePixel>,
    new_col_r: u8,
    new_col_g: u8,
    new_col_b: u8,
) -> Result<()> {
    // Validation
    if new_col_r < MIN_COL || new_col_r > MAX_COL {
        return Err(error!(ErrorCode::InvalidRColor));
    }

    if new_col_g < MIN_COL || new_col_g > MAX_COL {
        return Err(error!(ErrorCode::InvalidBColor));
    }

    if new_col_b < MIN_COL || new_col_b > MAX_COL {
        return Err(error!(ErrorCode::InvalidGColor));
    }

    // Set values
    let pixel = &mut ctx.accounts.pixel;
    pixel.col_r = new_col_r;
    pixel.col_g = new_col_g;
    pixel.col_b = new_col_b;

    Ok(())
}
```

It’s basically just a simplified version of our create instruction: it doesn’t need x and y co-ordinates and it only sets the new color. Pretty straightforward!

Okay nice, let’s add a test for that:
```
it("Can update a created pixel", async () => {
  const x = 40
  const y = 40

  const [pixelPublicKey] = web3.PublicKey.findProgramAddressSync(
    [Buffer.from("pixel"), Buffer.from([x, y])],
    program.programId,
  )

  // Create the pixel
  await program.methods
    .createPixel(x, y, 0, 0, 255)
    .accounts({
      pixel: pixelPublicKey,
      user: anchorProvider.wallet.publicKey,
      systemProgram: web3.SystemProgram.programId,
    })
    .rpc()

  // Update the pixel
  await program.methods
    .updatePixel(255, 0, 0)
    .accounts({
      pixel: pixelPublicKey,
    })
    .rpc()

  const storedPixel = await program.account.pixel.fetch(pixelPublicKey)
  assert.equal(storedPixel.posX, x)
  assert.equal(storedPixel.posY, y)
  assert.equal(storedPixel.colR, 255)
  assert.equal(storedPixel.colG, 0)
  assert.equal(storedPixel.colB, 0)
})
```
We create the pixel, then we update it, and then we check the stored color is what we updated it to. This should be straightforward and pass.
Optimizing the bump

This update instruction is really simple and works great. But it is a bit inefficient: Anchor doesn’t know the bump so it’s having to go and check each bump from 255, 254, etc. until it finds the off-curve PDA address. But it already did that on create. We don’t want to pass the bump in to the instruction, because there can be multiple bumps that produce an off-curve address. Remember that Anchor always finds the first one, and that’s the one we need it to use.

So the tradeoff that we can make is to store the bump that Anchor computed for us on the pixel account on-chain, at the point that we initialize it. Then just like we could read the pos_x and pos_y values in the context, we can read the stored bump too. This will improve the efficiency on update, at the cost of storing a tiny bit more data (one more byte) in the account. Let’s take a look at how we can add this.

First we update the Pixel account struct to add the extra field:
```
// Define structure of pixel account
#[account]
pub struct Pixel {
    pub pos_x: u8,
    pub pos_y: u8,
    pub col_r: u8,
    pub col_g: u8,
    pub col_b: u8,
    pub bump: u8,
}
```

Now because we’ve updated the fields, we need to update the size of the account:
```
// Define size of pixel account
const DISCRIMINATOR_LENGTH: usize = 8;
const POS_LENGTH: usize = 1;
const COL_LENGTH: usize = 1;
const BUMP_LENGTH: usize = 1;

impl Pixel {
    const LEN: usize = DISCRIMINATOR_LENGTH + (2 * POS_LENGTH) + (3 * COL_LENGTH) + BUMP_LENGTH;
}
```
And finally we need to update our create_pixel instruction to set the bump field:
```
pub fn create_pixel(
    ctx: Context<CreatePixel>,
    pos_x: u8,
    pos_y: u8,
    init_col_r: u8,
    init_col_g: u8,
    init_col_b: u8,
) -> Result<()> {
    // Validation
    if pos_x < MIN_POS || pos_x > MAX_POS {
        return Err(error!(ErrorCode::InvalidXCoordinate));
    }

    if pos_y < MIN_POS || pos_y > MAX_POS {
        return Err(error!(ErrorCode::InvalidYCoordinate));
    }

    if init_col_r < MIN_COL || init_col_r > MAX_COL {
        return Err(error!(ErrorCode::InvalidRColor));
    }

    if init_col_b < MIN_COL || init_col_b > MAX_COL {
        return Err(error!(ErrorCode::InvalidBColor));
    }

    if init_col_g < MIN_COL || init_col_g > MAX_COL {
        return Err(error!(ErrorCode::InvalidGColor));
    }

    // Set values
    let pixel = &mut ctx.accounts.pixel;
    pixel.pos_x = pos_x;
    pixel.pos_y = pos_y;
    pixel.col_r = init_col_r;
    pixel.col_g = init_col_g;
    pixel.col_b = init_col_b;
    pixel.bump = *ctx.bumps.get("pixel").unwrap();

    Ok(())
}
```
Anchor makes any bumps it computes for us available on ctx.bumps, in this case we want the one for the pixel account.

And finally we update our UpdatePixel context to use this bump:
```
#[derive(Accounts)]
pub struct UpdatePixel<'info> {
    #[account(
        mut,
        seeds = [b"pixel".as_ref(), [pixel.pos_x, pixel.pos_y].as_ref()],
        bump = pixel.bump,
    )]
    pub pixel: Account<'info, Pixel>,
}

```
Nice! Now if you run anchor test again then all the tests should pass. If you’d like to add some extra tests for the update_instruction for either the validation logic or PDA checks then feel free to.

Pixel changed event
-------------------------

Those 2 instructions are all we need to implement our app. You’ll often find that because of the account model, your Solana programs can be a lot simpler than you might expect.

We do want to add one more thing though: an event that we can emit whenever a pixel changes. That will let us listen to that event from the app, and we’ll automatically see pixels changed by anybody immediately reflected in our app. A tiny amount of code to make our app collaborative!

First we’ll add an event at the bottom of the lib.rs file:
```
#[event]
pub struct PixelChanged {
    pub pos_x: u8,
    pub pos_y: u8,
    pub col_r: u8,
    pub col_g: u8,
    pub col_b: u8,
}

```
It’s just another struct with x, y co-ordinates and RGB colors. We use the #[event] attribute to identify it as an event in Anchor.

Now we can update our instructions to emit this event, using the emit! macro. Here’s how our create instruction looks:
```
pub fn create_pixel(
    ctx: Context<CreatePixel>,
    pos_x: u8,
    pos_y: u8,
    init_col_r: u8,
    init_col_g: u8,
    init_col_b: u8,
) -> Result<()> {
    // Validation
    if pos_x < MIN_POS || pos_x > MAX_POS {
        return Err(error!(ErrorCode::InvalidXCoordinate));
    }

    if pos_y < MIN_POS || pos_y > MAX_POS {
        return Err(error!(ErrorCode::InvalidYCoordinate));
    }

    if init_col_r < MIN_COL || init_col_r > MAX_COL {
        return Err(error!(ErrorCode::InvalidRColor));
    }

    if init_col_b < MIN_COL || init_col_b > MAX_COL {
        return Err(error!(ErrorCode::InvalidBColor));
    }

    if init_col_g < MIN_COL || init_col_g > MAX_COL {
        return Err(error!(ErrorCode::InvalidGColor));
    }

    // Set values
    let pixel = &mut ctx.accounts.pixel;
    pixel.pos_x = pos_x;
    pixel.pos_y = pos_y;
    pixel.col_r = init_col_r;
    pixel.col_g = init_col_g;
    pixel.col_b = init_col_b;
    pixel.bump = *ctx.bumps.get("pixel").unwrap();

    // Emit event
    emit!(PixelChanged {
        pos_x,
        pos_y,
        col_r: init_col_r,
        col_g: init_col_g,
        col_b: init_col_b,
    });

    Ok(())
}
```
The update one is really similar. Try doing it yourself before checking the solution below.

Finally, let’s add some unit tests for this. We use program.addEventListener to listen to these events. We’ll see this in more detail when we use them in our web app.
```
it("Emits an event when a pixel is created", async () => {
  let events = [];
  const listener = program.addEventListener('PixelChanged', (event: any) => {
    events.push(event)
  })

  const x = 50
  const y = 50

  const [pixelPublicKey] = web3.PublicKey.findProgramAddressSync(
    [Buffer.from("pixel"), Buffer.from([x, y])],
    program.programId,
  )

  await program.methods
    .createPixel(x, y, 0, 0, 255)
    .accounts({
      pixel: pixelPublicKey,
      user: anchorProvider.wallet.publicKey,
      systemProgram: web3.SystemProgram.programId,
    })
    .rpc()

  assert.equal(events.length, 1)
  const event = events[0];

  assert.equal(event.posX, x)
  assert.equal(event.posY, y)
  assert.equal(event.colR, 0)
  assert.equal(event.colG, 0)
  assert.equal(event.colB, 255)

  program.removeEventListener(listener)
})

it("Emits an event when a pixel is updated", async () => {
  // Update the (50, 50) from the previous test
  let events = [];
  const listener = program.addEventListener('PixelChanged', (event: any) => {
    events.push(event)
  })

  const x = 50
  const y = 50

  const [pixelPublicKey] = web3.PublicKey.findProgramAddressSync(
    [Buffer.from("pixel"), Buffer.from([x, y])],
    program.programId,
  )

  await program.methods
    .updatePixel(255, 0, 0)
    .accounts({
      pixel: pixelPublicKey,
    })
    .rpc()

  assert.equal(events.length, 1)
  const event = events[0];

  assert.equal(event.posX, x)
  assert.equal(event.posY, y)
  assert.equal(event.colR, 255)
  assert.equal(event.colG, 0)
  assert.equal(event.colB, 0)

  program.removeEventListener(listener)
})
```
Okay awesome! Our program is done and tested! In the next lesson we’ll build our UI for it. We’ve actually already seen a lot of how that’s going to work while writing our Typescript tests, it’s the same API.

The UI
--------------
Before we can build the UI for our app we need to get it deployed. For now we’re going to use the local validator, which is basically an instance of Solana running on your dev machine. It’s pretty slick! This is what Anchor has been doing behind the scenes when we run our tests too.

Deploying to local network
--------------
In a terminal just run solana-test-validator:
```
$ solana-test-validator

--faucet-sol argument ignored, ledger already exists
Ledger location: test-ledger
Log: test-ledger/validator.log
⠴ Initializing...
Identity: 6jc6DVPjnuwTTNDTcPmpZjzmaYXidd26SMUJRFMDPRDa
Genesis Hash: 81KL7T3Tm7kHjLc6jq5F3zXMEUu6DvWLKA2hXpd4gX9D
Version: 1.10.31
Shred Version: 6794
Gossip Address: 127.0.0.1:1024
TPU Address: 127.0.0.1:1027
JSON RPC URL: http://127.0.0.1:8899
⠤ 00:00:08 | Processed Slot: 3410 | Confirmed Slot: 3410 | Finalized Slot: 3377 | Full Snapshot Slot: 3300 | Incremental Snapshot Slot: - | Transactions: 3662 | ◎499.983600000
```

Note that it’ll just keep running there, so use a terminal window you don’t mind keeping around. When we run anchor test Anchor starts a local validator for us, but by running it ourselves we can keep it open for as long as we need.

Okay, so now we’re going to deploy our program to the local validator. In the draw-with-frens directory try running anchor deploy:
```
$ anchor deploy
Deploying workspace: http://localhost:8899
Upgrade authority: /Users/callummcintyre/.config/solana/id.json
Deploying program "draw-with-frens"...
Program path: /Users/callummcintyre/projects/anchor-tutorial/draw-with-frens/target/deploy/draw_with_frens.so...
===========================================================================
Recover the intermediate account's ephemeral keypair file with
`solana-keygen recover` and the following 12-word seed phrase:
===========================================================================
<snip>
===========================================================================
To resume a deploy, pass the recovered keypair as the
[BUFFER_SIGNER] to `solana program deploy` or `solana program write-buffer'.
Or to recover the account's lamports, pass it as the
[BUFFER_ACCOUNT_ADDRESS] argument to `solana program close`.
===========================================================================
Error: Account 8x13yTKCGG7se8VBDMUB8AyNHkctDvZSCnC1PYVn1zQW has insufficient funds for spend (1.61536728 SOL) + fee (0.00117 SOL)
There was a problem deploying: Output { status: ExitStatus(unix_wait_status(256)), stdout: "", stderr: "" }.
```
If this is the first time you’ve done this, you’ll get an error. The address that it’s trying to use to deploy the program has no SOL, so the deploy transactions fails. Deploying a program is basically a transaction that makes an account big enough for the compiled code. So we need SOL to deploy, and pay for our code to stay on the blockchain.

If you take a look at draw-with-frens/Anchor.toml you can see a bit more detail of what’s going on here:
```
[provider]
cluster = "localnet"
wallet = "/Users/callummcintyre/.config/solana/id.json"
```
We’re telling Anchor that we want to use the local network, and we want to use this default wallet. Since the path includes my name, yours will be a bit different :) It’ll be that default one that we created when we set up the Solana CLI earlier.

You can get the address of that wallet like this:
```
$ solana address -k "/Users/callummcintyre/.config/solana/id.json"              
8x13yTKCGG7se8VBDMUB8AyNHkctDvZSCnC1PYVn1zQW
```
This will also match the address in the error we saw when we tried to deploy:
```
Error: Account 8x13yTKCGG7se8VBDMUB8AyNHkctDvZSCnC1PYVn1zQW has insufficient funds for spend (1.61536728 SOL) + fee (0.00117 SOL)
```

Okay so let’s send some (local network) SOL to that address:
```
$ solana airdrop 1000 8x13yTKCGG7se8VBDMUB8AyNHkctDvZSCnC1PYVn1zQW                                            
Requesting airdrop of 1000 SOL

Signature: q9WV9szWGV4232ECt95oPFuR8RXiT4LvAY4kkyKW5i7gh8MgKdUVQHSYqtsnoes1UWkF8cjVCe3CMW4GTCkV7hv

1000 SOL
```
On the local network we can airdrop as much as we like! 1000 SOL should cover us :)

Now if you run anchor deploy again it should succeed:
```
$ anchor deploy                                                   
Deploying workspace: http://localhost:8899
Upgrade authority: /Users/callummcintyre/.config/solana/id.json
Deploying program "draw-with-frens"...
Program path: /Users/callummcintyre/projects/anchor-tutorial/draw-with-frens/target/deploy/draw_with_frens.so...
Program Id: A7Ly2N39Rstzo6ZUBDsdiTjD8rMkdZ6h7ZGrtN5fjRv1

Deploy success

```

Nice! The program is now deployed to our local network.

There is one problem though: that program ID A7Ly2N39Rstzo6ZUBDsdiTjD8rMkdZ6h7ZGrtN5fjRv1 doesn’t match the one in my program:

```
declare_id!("Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkg476zPFsLnS");
```

The default one was generated, but now an actual keypair has been created and we have a new address where our program will be deployed. We can also see it with anchor keys list:
```
$ anchor keys list                                                
draw_with_frens: A7Ly2N39Rstzo6ZUBDsdiTjD8rMkdZ6h7ZGrtN5fjRv1
```
If we leave it as-is we’re going to get a pretty confusing error later. So let’s fix it now!
```
declare_id!("A7Ly2N39Rstzo6ZUBDsdiTjD8rMkdZ6h7ZGrtN5fjRv1");
```

Make sure you use your ID, it’ll be different from mine!

Now let’s get that deployed to our local network, make sure you build first:

```
$ anchor build

$ anchor deploy
anchor deploy
Deploying workspace: http://localhost:8899
Upgrade authority: /Users/callummcintyre/.config/solana/id.json
Deploying program "draw-with-frens"...
Program path: /Users/callummcintyre/projects/anchor-tutorial/draw-with-frens/target/deploy/draw_with_frens.so...
Program Id: A7Ly2N39Rstzo6ZUBDsdiTjD8rMkdZ6h7ZGrtN5fjRv1

Deploy success
```
Okay, now onto our UI! :)

Solana Wallet Connect

The first thing we need to do is allow a user to connect their Solana wallet to our app. We’ll use the awesome wallet-adapter library for this, which makes it pretty effortless.

First, let’s install our Solana dependencies. Make sure you’re in the frontend/ directory here!
```
$ npm install @solana/web3.js @solana/wallet-adapter-react @solana/wallet-adapter-react-ui @solana/wallet-adapter-base @solana/wallet-adapter-wallets
```

Next, we’re going to update pages/_app.tsx. Basically we’re going to wrap our whole app in a few providers that will make the connection and the connected wallet available to our components. Here’s how it looks:
```
import '../styles/index.css'
import type { AppProps } from 'next/app'
import Head from 'next/head'
import { ConnectionProvider, WalletProvider } from '@solana/wallet-adapter-react'
import { WalletModalProvider } from '@solana/wallet-adapter-react-ui'
import { PhantomWalletAdapter, SolflareWalletAdapter } from '@solana/wallet-adapter-wallets'

// Default styles that can be overridden by your app
require('@solana/wallet-adapter-react-ui/styles.css');

function MyApp({ Component, pageProps }: AppProps) {
  // You can also provide a custom RPC endpoint.
  const endpoint = 'http://localhost:8899';

  // @solana/wallet-adapter-wallets includes all the adapters but supports tree shaking and lazy loading --
  // Only the wallets you configure here will be compiled into your application, and only the dependencies
  // of wallets that your users connect to will be loaded.
  const wallets = [
    new PhantomWalletAdapter(),
    new SolflareWalletAdapter(),
  ];

  return (
    <ConnectionProvider endpoint={endpoint}>
      <WalletProvider wallets={wallets} autoConnect>
        <WalletModalProvider>
          <Head>
            <title>Draw With Frens</title>
          </Head>
          <Component {...pageProps} />
        </WalletModalProvider>
      </WalletProvider>
    </ConnectionProvider>
  )
}

export default MyApp

```
We set the endpoint to localhost:8899. That’s where our local Solana validator is running by default. Later we’ll see how to connect to a public network instead.

I’ve made our app use Phantom and Solflare. Wallet-adapter supports a tonne of different Solana wallets, so if you have a different favorite then feel free to add it too! The idea of wallet-adapter is to make it really easy for our app to support whichever wallets we want to with a common API.

ConnectionProvider is going to make the Solana connection object available to our app

WalletProvider is going to make the connected wallet available to us. It exposes the publicKey and functions like sendTransaction that use the connected wallet. We use autoConnect so if the user has connected a wallet before then it’ll connect again automatically.

WalletModalProvider is a UI modal that will be used to let the user select their wallet. We’ll see that UI in a minute!

Now let’s update pages/index.tsx to add a wallet select button. The wallet-adapter react UI library makes this super simple!
```
import ColorSelector from "../components/ColorSelector";
import Title from "../components/Title";
import { Color, colors } from "../lib/colors";
import { useState } from "react";
import Canvas from "../components/Canvas";
import { WalletMultiButton } from "@solana/wallet-adapter-react-ui";

export default function Home() {
  const [selectedColor, setSelectedColor] = useState<Color>(colors[0])

  return (
    <div className="flex flex-col items-stretch gap-8 px-4 pt-24 mx-auto max-w-max">
      <main className="flex flex-col gap-4">
        <Title>Draw With Frens</Title>

        <div className="basis-1/4">
          <WalletMultiButton className='!bg-gray-900 hover:scale-105' />
        </div>

        <ColorSelector selectedColor={selectedColor} setSelectedColor={setSelectedColor} />

        <Canvas />
      </main>
    </div >
  )
}
```

We’re just using WalletMultiButton which lets the user select from any of the wallets we set. It also provides some nice convenience features:
<img width="684" alt="11" src="https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/abad7b56-1e19-4831-b681-017f54f887ae">
And that’s all we need to connect a wallet!

Fetching the pixels
-----------------------

Next up we’re going to look at how we can communicate with our Anchor program from our app. We’ll need to do a bit of setup up-front, and then it’ll be pretty similar to our unit tests! The first thing we’ll do is look at how to fetch all the pixel accounts. There won’t be any yet, but finding that out in our app will give us most of the setup we need.

We’ll do the setup in pages/index.tsx, and then pass what we need down to the child components.

First we need one more dependency: npm install @project-serum/anchor. That’ll give us all the Anchor helpers to make talking to our program much more straightforward. This is the library we used for our unit tests too. Make sure you’re in the frontend/ directory BTW!

Now in our unit tests we basically had 3 lines configuring anchor:
```
const anchorProvider = anchor.AnchorProvider.env();
anchor.setProvider(anchorProvider);
const program = anchor.workspace.DrawWithFrens as Program<DrawWithFrens>;
```
We want to create these same structures in our app. It’s a bit more complex because Anchor isn’t running our app like it ran the tests.

While the unit test version hides it a bit, there is a relationship between them: the provider is a dependency for the program. That’s why we use anchor.setProvider, but it’s still less obvious than what we’ll see when we build them ourselves. The provider gives us the ability to send transactions to Solana: it’s a combination of the network connection and a wallet that will be used to send transactions. It doesn’t know anything about our program specifically. The program combines the provider with knowledge of our program and of all its functionality and types, to provide a nice API to our program. Here’s a diagram showing how it all fits together:

![12](https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/6382ce4b-014a-4ff1-9a77-6f4ebddf8134)

We already have the ability to get the Solana network connection and connected wallet from the wallet connect code we added previously. So we can create our Anchor Provider! Let’s do that, and then come back to the Program.

Creating the AnchorProvider
-------------------------------

We’ll update our pages/index.tsx to construct the anchor provider first:
```
import ColorSelector from "../components/ColorSelector";
import Title from "../components/Title";
import { Color, colors } from "../lib/colors";
import { useMemo, useState } from "react";
import Canvas from "../components/Canvas";
import { WalletMultiButton } from "@solana/wallet-adapter-react-ui";
import { AnchorWallet, useAnchorWallet, useConnection } from "@solana/wallet-adapter-react";
import { AnchorProvider } from "@project-serum/anchor";

export default function Home() {
  const [selectedColor, setSelectedColor] = useState<Color>(colors[0])

  const { connection } = useConnection();

  const anchorWallet: AnchorWallet | undefined = useAnchorWallet()

  const anchorProvider: AnchorProvider | undefined = useMemo(() => {
    if (anchorWallet) {
      return new AnchorProvider(connection, anchorWallet, { commitment: 'confirmed' })
    } else {
      return undefined
    }
  }, [connection, anchorWallet])

  return (
    <div className="flex flex-col items-stretch gap-8 px-4 pt-24 mx-auto max-w-max">
      <main className="flex flex-col gap-4">
        <Title>Draw With Frens</Title>

        <div className="basis-1/4">
          <WalletMultiButton className='!bg-gray-900 hover:scale-105' />
        </div>

        <ColorSelector selectedColor={selectedColor} setSelectedColor={setSelectedColor} />

        <Canvas />
      </main>
    </div >
  )
}
```
Let’s step through these additions.

We get the Solana connection:

```
const { connection } = useConnection();
```

Nothing Anchor specific here, we can use useConnection() to get the connection anywhere in our app.

We get the connected wallet:
```
const anchorWallet: AnchorWallet | undefined = useAnchorWallet()
```
This hook will return an AnchorWallet if the user has connected a wallet, otherwise undefined.

Now we can create the AnchorProvider, but only if anchorWallet is defined:
```
const anchorProvider: AnchorProvider | undefined = useMemo(() => {
  if (anchorWallet) {
    return new AnchorProvider(connection, anchorWallet, { commitment: 'confirmed' })
  } else {
    return undefined
  }
}, [connection, anchorWallet])
```
In react useMemo is used to re-compute an object when one of its dependencies change. In our case if either connection or anchorWallet changes then we will re-run this code to set anchorProvider. In practice, this means when the page loads without a connected wallet, the provider will be undefined. When we then connect a wallet, this code will re-run and the provider will be defined.

Creating the program
------------------------
Now let’s take a closer look at how we build our Program
![13](https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/117f6a9e-b951-46c8-8391-0a55872b41f3)
We’ve got the provider now, we’ve already seen the program ID, now we’re just missing the IDL.

IDL stands for Interactive Description Language, and it’s something that Anchor generates for us for our program. It describes all the types and functions in our program, and combined with TypeScript it makes our AnchorProgram really awesome.

Anchor will have generated our IDL in its directory at draw-with-frens/target/types/draw_with_frens.ts

If you take a look at that file you’ll see its structure:
```
export type DrawWithFrens = {
  ...
};

export const IDL: DrawWithFrens = {
  ...
};

```

There’s a pretty complex generated type DrawWithFrens and then an IDL JSON object of that type.

The IDL JSON is also at draw-with-frens/target/idl/draw_with_frens.json if you need to use it in an app without TypeScript. Anchor really benefits from TypeScript though!

We’re going to copy the target/types/draw_with_frens.ts into our frontend directory. Create a new directory frontend/idl and just copy that whole file in. Note that if we ever changed our program we’d need to copy the new version of the IDL over to keep it in sync. So you should have that file with the type + IDL at frontend/idl/draw_with_frens.ts.

Now we have everything we need to get our program, which will let us start fetching our accounts and calling our instructions. Here’s how that looks:
```
import ColorSelector from "../components/ColorSelector";
import Title from "../components/Title";
import { Color, colors } from "../lib/colors";
import { useMemo, useState } from "react";
import Canvas from "../components/Canvas";
import { WalletMultiButton } from "@solana/wallet-adapter-react-ui";
import { AnchorWallet, useAnchorWallet, useConnection } from "@solana/wallet-adapter-react";
import { AnchorProvider, Program } from "@project-serum/anchor";
import { DrawWithFrens, IDL } from "../idl/draw_with_frens";
import { PublicKey } from "@solana/web3.js";

export default function Home() {
  const [selectedColor, setSelectedColor] = useState<Color>(colors[0])

  const { connection } = useConnection();

  const anchorWallet: AnchorWallet | undefined = useAnchorWallet()

  const anchorProvider: AnchorProvider | undefined = useMemo(() => {
    if (anchorWallet) {
      return new AnchorProvider(connection, anchorWallet, { commitment: 'confirmed' })
    } else {
      return undefined
    }
  }, [connection, anchorWallet])

  // Make sure you use your program ID!
  const programId = new PublicKey("your-program-id");

  const anchorProgram: Program<DrawWithFrens> | undefined = useMemo(() => {
    if (anchorProvider) {
      return new Program(IDL, programId, anchorProvider)
    } else {
      return undefined
    }
  }, [anchorProvider])

  return (
    <div className="flex flex-col items-stretch gap-8 px-4 pt-24 mx-auto max-w-max">
      <main className="flex flex-col gap-4">
        <Title>Draw With Frens</Title>

        <div className="basis-1/4">
          <WalletMultiButton className='!bg-gray-900 hover:scale-105' />
        </div>

        <ColorSelector selectedColor={selectedColor} setSelectedColor={setSelectedColor} />

        <Canvas />
      </main>
    </div >
  )
}

```
We’ve imported the IDL and type from that file we copied over. Our program ID is just a public key. And once we have those, we can create our Program. Like the provider, we’re using useMemo and the program is only defined if the provider is. So when we connect a wallet, first the provider will be created, and then the program will. And then we can interact with our Anchor program as the connected user!

Fetching the accounts
-------------------------

We’re going to keep the pixel data in our Canvas component. If you take a look at the code we already have there, it’s just creating a table of 100x100 Pixel components. It’s going to be responsible for knowing what colour all of those pixels should be.

Anchor makes it super easy to fetch accounts. To fetch all the accounts of type Pixel we’ll use await program.account.pixel.all()

So we need to update our Canvas component to pass the program to it, and then we can use it to fetch the accounts. Here’s what that looks like:
```
import { Program } from "@project-serum/anchor";
import clsx from "clsx"
import { useEffect } from "react";
import { DrawWithFrens } from "../idl/draw_with_frens";
import Pixel from "./Pixel"

interface Props {
  program?: Program<DrawWithFrens>
}

export default function Canvas({ program }: Props) {
  const disabled = !program;

  const fetchPixels = async () => {
    if (program) {
      const pixels = await program.account.pixel.all()
      console.log("got the pixels!", pixels)
    }
  }

  useEffect(() => {
    fetchPixels()
  }, [program])

  return (
    <div className={clsx(disabled && "opacity-25 cursor-not-allowed")}>
      <table className="border border-gray-300 table-fixed">
        <tbody className="divide-y divide-gray-300">
          {[...Array(100)].map((_, y) => {
            return (
              <tr className="divide-x divide-gray-300" key={y}>
                {[...Array(100)].map((_, x) => {

                  return <Pixel
                    posX={x}
                    posY={y}
                    key={x}
                  />
                })}
              </tr>
            )
          })}
        </tbody>
      </table>
    </div>
  )
}
```
There’s a lot new here! We’ve added Props, our component can now receive the program as a prop. We’ve set the component to be disabled if the program isn’t defined. Remember that the program isn’t defined if the provider isn’t, and the provider isn’t defined if there’s no wallet connected. We have a new function fetchPixels which just fetches all the pixel accounts using the program, and we call that in a useEffect.

Now we just need to update our index.tsx to pass the program to Canvas. I’ve just updated the return, everything else stays the same:
```
return (
  <div className="flex flex-col items-stretch gap-8 px-4 pt-24 mx-auto max-w-max">
    <main className="flex flex-col gap-4">
      <Title>Draw With Frens</Title>

      <div className="basis-1/4">
        <WalletMultiButton className='!bg-gray-900 hover:scale-105' />
      </div>

      <ColorSelector selectedColor={selectedColor} setSelectedColor={setSelectedColor} />

      <Canvas program={anchorProgram} />
    </main>
  </div>
)
```
Awesome! Now if you refresh the app you should see that if there’s no account connected the canvas gets disabled:
<img width="1192" alt="14" src="https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/f2dce1a8-68f6-4d9e-83fc-a5ca9d8a1757">
And if you connect an account you should see the pixels get fetched!
<img width="1202" alt="15" src="https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/23363775-605b-4d40-95d9-2a1112b80b77">

Hmm yea, so there aren’t any yet. Makes sense! We’ll come back to that once we’ve created some!

Drawing on the canvas!
-------------------------

For now when the user clicks on a pixel we’re going to call our createPixel instruction using its x and y co-ordinates. So we need to add a click handler to our Pixel component.

Here’s what that looks like:
```
import { Program } from "@project-serum/anchor";
import { PublicKey, SystemProgram } from "@solana/web3.js";
import { DrawWithFrens } from "../idl/draw_with_frens";

interface Props {
  posX: number,
  posY: number,
  program: Program<DrawWithFrens>,
}

export default function Pixel({ posX, posY, program }: Props) {
  const color = "white";

  const getPixelAddress = () => {
    const [pixelPublicKey] = PublicKey.findProgramAddressSync(
      [Buffer.from("pixel"), Buffer.from([posX, posY])],
      program.programId,
    )
    return pixelPublicKey
  }

  const createPixel = async () => {
    await program.methods
      .createPixel(posX, posY, 255, 0, 0)
      .accounts({
        pixel: getPixelAddress(),
        user: program.provider.publicKey,
        systemProgram: SystemProgram.programId,
      })
      .rpc();
  }

  return <td
    className="h-4 min-w-[1rem]"
    style={{ backgroundColor: color }}
    onClick={createPixel}
  />
}
```
We’re adding the program to Props for this component, just like we did for Canvas. The code to get the pixel PDA and call the createPixel instruction should all be familiar - it’s exactly the same code we used in our unit tests. I’ve hardcoded the color for now, we’ll come back to that later.

This is one of the really nice things about the Anchor Typescript library, we have this consistent program RPC API which works the same no matter which wallet is connected.

Now we need to update the Canvas to pass the program through. Again just updating the return:

```

return (
  <div className={clsx(disabled && "opacity-25 cursor-not-allowed")}>
    <table className="border border-gray-300 table-fixed">
      <tbody className="divide-y divide-gray-300">
        {[...Array(100)].map((_, y) => {
          return (
            <tr className="divide-x divide-gray-300" key={y}>
              {[...Array(100)].map((_, x) => {

                return <Pixel
                  posX={x}
                  posY={y}
                  program={program}
                  key={x}
                />
              })}
            </tr>
          )
        })}
      </tbody>
    </table>
  </div>
)
```

Now if you click on a pixel on the canvas, you should see:
<img width="889" alt="16" src="https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/f779ca01-3f01-40d8-b87f-f8319e4cb6a4">
If you approve that transaction, and you don’t get an error message, then it worked! Unfortunately there’s no feedback yet but one step at a time :)

If you refresh the page you should see it come back in the fetched pixels that we log in the browser console:
<img width="362" alt="17" src="https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/1c44d509-522f-4b43-a50d-3694ab9411f2">

Feel free to create a few, and make sure they all come back in that array when you refresh!

Also notice the structure of what comes back: it’s an array of objects, where each object has a publicKey and then our Pixel struct under account.

Rendering the canvas
------------------------

Now that we have some pixels coming back when we fetch them, we can loop back and get them rendering on our canvas.

When we fetch the pixel data we get an array of all the accounts in no particular order. We’re going to convert this into a data structure that makes it easy for us to look up the data for a particular (x, y) coordinate. Remember that for most (x, y) cooordinates there won’t be any data most of the time, there are going to be gaps on our canvas.

This data structure will be a map, with the keys being our coordinates and the value being the fetched pixel data. However in Javascript/Typescript, we can’t use a tuple/list as a key for a map. The reason is that lists are never considered equal, so if we had [1,2,3] as a key and then we asked “hey does the map contain [1,2,3]?” the answer would always be no. Kinda not great! You can see this yourself in your browser console:

<img width="143" alt="18" src="https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/0e3cc0bd-3c11-4e98-8056-edf66d81019f">

So instead we’ll give each pixel a unique ID. This will be 100 * y + x. So (0,0) has ID 0, and (10, 50) has ID 50 * 100 + 10 = 5010

I put y first because in our code we iterate over y first, which means the pixels will be rendered in sequential ID order. Just makes it a little easier to reason about. But it doesn’t matter too much: the important thing is that each ID is unique and calculated from x and y.

Here’s how it looks:
<img width="1609" alt="19" src="https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/bcdce195-5a16-44d5-8481-39170bb38d08">

Here’s our updated canvas code so we now generate the pixel map:

```
import { IdlAccounts, Program } from "@project-serum/anchor";
import clsx from "clsx"
import { useEffect, useState, useMemo } from "react";
import { DrawWithFrens } from "../idl/draw_with_frens";
import Pixel from "./Pixel"

interface Props {
  program?: Program<DrawWithFrens>
}

type PixelAccount = IdlAccounts<DrawWithFrens>['pixel']

export default function Canvas({ program }: Props) {
  const disabled = !program;
  const [fetchedPixels, setFetchedPixels] = useState<PixelAccount[]>([]);

  const fetchPixels = async () => {
    if (program) {
      const pixels = await program.account.pixel.all()
      console.log("got the pixels!", pixels)
      setFetchedPixels(pixels.map(p => p.account))
    }
  }

  useEffect(() => {
    fetchPixels()
  }, [program])

  const pixelsMap = useMemo(() => {
    const map: { [id: number]: PixelAccount } = {};
    fetchedPixels.forEach(p => {
      const id = p.posY * 100 + p.posX;
      map[id] = p;
    })
    return map
  }, [fetchedPixels])

  return (
```
Let’s take a look at what we’ve added.

We have this kinda weird new type:

```
type PixelAccount = IdlAccounts<DrawWithFrens>['pixel']
```

This looks a bit complicated but it’s a really cool feature of Anchor! We’re extracting a TypeScript type for the data in our Pixel struct that we defined. If you hover it in your IDE you should see the type:
<img width="479" alt="20" src="https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/7968df39-8331-4003-9e33-b66b4cbdfbc5">
Also notice that while we called our fields things like pos_x and col_r to match Rust conventions, Anchor gives us posX and colR in TypeScript to match those conventions. TypeScript and Anchor go together super well!

We’re defining React state to store a list of that type:
```
const [fetchedPixels, setFetchedPixels] = useState<PixelAccount[]>([]);
```
And then setting that when we fetch the pixels:
```
const fetchPixels = async () => {
    if (program) {
      const pixels = await program.account.pixel.all()
      console.log("got the pixels!", pixels)
      setFetchedPixels(pixels.map(p => p.account))
    }
  }

```

Remember that the actual data is nested under the account field, so we extract that to get the data of type PixelAccount.

And then for our map we’re using another useMemo, so that it’s computed only whenever the fetchedPixels changes. We iterate through each account and store it in the map using our ID formula:
```
const pixelsMap = useMemo(() => {
  const map: { [id: number]: PixelAccount } = {};
  fetchedPixels.forEach(p => {
    const id = p.posY * 100 + p.posX;
    map[id] = p;
  })
  return map
}, [fetchedPixels])
```
Now let’s look at how we’re going to use the account data in the Pixel component. We can just pass the PixelAccount object to it, if it’s got one. Remember that many pixels won’t though. If it does have a PixelAccount object then we can use that to set its color, otherwise we’ll keep the white default. Here’s our updated Pixel component:

```
import { IdlAccounts, Program } from "@project-serum/anchor";
import { PublicKey, SystemProgram } from "@solana/web3.js";
import { DrawWithFrens } from "../idl/draw_with_frens";

type PixelAccount = IdlAccounts<DrawWithFrens>['pixel']

interface Props {
  posX: number,
  posY: number,
  program: Program<DrawWithFrens>,
  pixelData?: PixelAccount,
}

export default function Pixel({ posX, posY, program, pixelData }: Props) {
  const { colR, colG, colB } = pixelData || {};
  const color = pixelData ? `rgb(${colR}, ${colG}, ${colB})` : "white"

  const getPixelAddress = () => {
    const [pixelPublicKey] = PublicKey.findProgramAddressSync(
      [Buffer.from("pixel"), Buffer.from([posX, posY])],
      program.programId,
    )
    return pixelPublicKey
  }

  const createPixel = async () => {
    await program.methods
      .createPixel(posX, posY, 255, 0, 0)
      .accounts({
        pixel: getPixelAddress(),
        user: program.provider.publicKey,
        systemProgram: SystemProgram.programId,
      })
      .rpc();
  }

  return <td
    className="h-4 min-w-[1rem]"
    style={{ backgroundColor: color }}
    onClick={createPixel}
  />
}

```
We’re using that nice PixelAccount type again, this time for the type of an optional prop pixelData. If that prop is set then we put the colors into a nice rgb(...) CSS color. We’re already using the color variable as the background color, so that’s all we need to change here.

Okay, back to Canvas!

We’re just going to update the return so that it passes pixelData to each Pixel as it renders:
```
return (
  <div className={clsx(disabled && "opacity-25 cursor-not-allowed")}>
    <table className="border border-gray-300 table-fixed">
      <tbody className="divide-y divide-gray-300">
        {[...Array(100)].map((_, y) => {
          return (
            <tr className="divide-x divide-gray-300" key={y}>
              {[...Array(100)].map((_, x) => {
                const id = y * 100 + x;
                const pixelData = pixelsMap[id];

                return <Pixel
                  posX={x}
                  posY={y}
                  program={program}
                  pixelData={pixelData}
                  key={x}
                />
              })}
            </tr>
          )
        })}
      </tbody>
    </table>
  </div>
)
```

We calculate the correct ID and then get the pixelData from our pixelsMap for that ID. Note that if there is no pixel data then this will return undefined. This is why we introduced the map: because we can super quickly get the data (or undefined) for the (x, y) pair. If we hadn’t added the map we’d need to loop through the whole fetchedPixels data for each pixel which would get pretty slow. We’re rendering all 100x100 pixels even if we don’t have many pixel accounts, so this loop needs to be efficient.

Now if you refresh the app you should be seeing the pixels where you created an account filled in red!

And if you click more pixels and create them, you’ll get more red on the canvas.. after you refresh.

This is pretty awesome progress! We can now create pixel accounts on-chain, and then fetch and render the data from them in our app. Now let’s get rid of that pesky refresh and make our app a bit more live!

Responding to the event
---------------------------

Remember this event that we’re emitting in our instruction?

```
emit!(PixelChanged {
    pos_x,
    pos_y,
    col_r: init_col_r,
    col_g: init_col_g,
    col_b: init_col_b,
});
```

We’re going to update our app to listen for that event and automatically update the relevant pixel. Then we won’t have to refresh any more!

In Anchor we can add an event listener like this:

```
program.addEventListener('PixelChanged', async (event, _slot, _sig) => {
  // do whatever we like!
  // Anchor gives us nicely cased fields again
  console.log(e.posX, e.colR)
} 
```

In our case when we get one of these events, we’re going to ignore the colors and ask Anchor to fetch the latest version of the pixel at (x, y). This tends to be a bit simpler and more stable, especially if the same pixel is updated often.

We’re then going to update our fetchedPixels, which is the array of fetched accounts. There’s no need to re-fetch all our accounts, we can just slot the updated one in to that array. When we update that it’ll cause our pixelMap to update, and that’ll feed through to the relevant Pixel object that we’ve updated.

Here’s how adding this to Canvas looks:
```
import { IdlAccounts, Program } from "@project-serum/anchor";
import { PublicKey } from "@solana/web3.js";
import clsx from "clsx"
import { useEffect, useState, useMemo } from "react";
import { DrawWithFrens } from "../idl/draw_with_frens";
import Pixel from "./Pixel"

interface Props {
  program?: Program<DrawWithFrens>
}

type PixelAccount = IdlAccounts<DrawWithFrens>['pixel']

interface PixelChangedEvent {
  posX: number,
  posY: number,
  colR: number,
  colG: number,
  colB: number,
}

export default function Canvas({ program }: Props) {
  const disabled = !program;
  const [fetchedPixels, setFetchedPixels] = useState<PixelAccount[]>([]);

  const fetchPixels = async () => {
    if (program) {
      const pixels = await program.account.pixel.all()
      console.log("got the pixels!", pixels)
      setFetchedPixels(pixels.map(p => p.account))
    }
  }

  useEffect(() => {
    fetchPixels()
  }, [program])

  const pixelsMap = useMemo(() => {
    const map: { [id: number]: PixelAccount } = {};
    fetchedPixels.forEach(p => {
      const id = p.posY * 100 + p.posX;
      map[id] = p;
    })
    return map
  }, [fetchedPixels])

  const getPixelAddress = (posX: number, posY: number) => {
    const [pixelPublicKey] = PublicKey.findProgramAddressSync(
      [Buffer.from("pixel"), Buffer.from([posX, posY])],
      program.programId,
    )
    return pixelPublicKey
  }

  // Listen to PixelChanged events
  useEffect(() => {
    if (!program) return;

    const listener = program.addEventListener('PixelChanged', async (event, _slot, _sig) => {
      const e = event as PixelChangedEvent;

      // Get the latest data from Anchor for this pixel
      const pixelAddress = await getPixelAddress(e.posX, e.posY);
      const updatedPixelAccount = await program.account.pixel.fetch(pixelAddress);

      // Update the state
      setFetchedPixels(pixels => {
        const newPixels = [...pixels];
        const index = newPixels.findIndex(p => p.posX === e.posX && p.posY === e.posY);
        if (index >= 0) {
          // We already have pixel data at this position, so replace it
          newPixels[index] = updatedPixelAccount;
        } else {
          // We don't have pixel data at this position, so add it
          newPixels.push(updatedPixelAccount);
        }
        return newPixels;
      })
    })

    return () => {
      program.removeEventListener(listener);
    }
  }, [program])

  return (
    // unchanged, snipping for brevity! 
  )
}
```

We’ve added a PixelChangedEvent interface, I wrote this manually because Anchor doesn’t have auto-generated types for events yet. Our event will be of that type.

To fetch the latest data from Anchor, first we need to derive the address of the updated pixel. We do that using the same getPixelAddress function we used in our Pixel component. I’ve just copied it over and added posX and posY as arguments. Then we can call await program.account.pixel.fetch(pixelAddress) to get the latest data. Exactly the same as what we used in our unit tests to check the data after calling our instructions.

The state update is a bit complicated, I’ve written it to handle both create and update events. We use findIndex to find the pixel in our array that matches the posX and posY of our event, if there is one. It’ll return -1 if there isn’t. If there is a match, then we just replace the pixel at that address with our newly fetched one. So if there’s a red one there and we’ve just fetched a blue one at that address, we swap red for blue. If there isn’t a match then we just pop the fetched account on the end of the list of pixels - this is a newly created pixel.

In either case, once our state updates the pixelMap will be automatically updated and React will re-render the updated pixel.

That’s all we have to do! If you create a new pixel now, you’ll see it update on the canvas!

You might notice that this is purely event driven: we’re not listening for the transaction that we sent, we’re just listening for the event to be emitted. That means that if you open your app in different browser windows, they’ll be kept in sync:
![21](https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/e42d03a5-83c7-484c-9a03-a3e72c231f0e)
Congrats! You just built a live, realtime, collaborative app!

When you deploy your app later, it’ll be synced for anyone anywhere in the world. We have no infrastructure to maintain to make that happen, we’re just listening to on-chain events.

Make it multicolor!
------------------------

The drawing feature of our app is a bit limited ATM because every pixel we add is red. Let’s add some color!

We actually already have code for selecting a color from the starter code. The default colors for those blocks at the top are in frontend/lib/colors.ts. There’s also a type there:
```
export interface Color {
  r: number,
  g: number,
  b: number,
}
```

Since our instruction is taking separate RGB inputs, that’s how we store the colors in the frontend too. We also already have selectedColor as a state in pages/index.tsx. As a spoiler we’re just going to use prop drilling here to get it to the Pixel components, you could of course use any react state management you like.

Let’s work backward from the Pixel component.
```
import { IdlAccounts, Program } from "@project-serum/anchor";
import { PublicKey, SystemProgram } from "@solana/web3.js";
import { DrawWithFrens } from "../idl/draw_with_frens";
import { Color } from "../lib/colors";

type PixelAccount = IdlAccounts<DrawWithFrens>['pixel']

interface Props {
  posX: number,
  posY: number,
  program: Program<DrawWithFrens>,
  pixelData?: PixelAccount,
  selectedColor: Color,
}

export default function Pixel({ posX, posY, program, pixelData, selectedColor }: Props) {
  const { colR, colG, colB } = pixelData || {};
  const color = pixelData ? `rgb(${colR}, ${colG}, ${colB})` : "white"

  const getPixelAddress = () => {
    const [pixelPublicKey] = PublicKey.findProgramAddressSync(
      [Buffer.from("pixel"), Buffer.from([posX, posY])],
      program.programId,
    )
    return pixelPublicKey
  }

  const createPixel = async () => {
    await program.methods
      .createPixel(posX, posY, selectedColor.r, selectedColor.g, selectedColor.b)
      .accounts({
        pixel: getPixelAddress(),
        user: program.provider.publicKey,
        systemProgram: SystemProgram.programId,
      })
      .rpc();
  }

  return <td
    className="h-4 min-w-[1rem]"
    style={{ backgroundColor: color }}
    onClick={createPixel}
  />
}
```
Nothing magic here: we have a new prop selectedColor and pass its components into our call to the createPixel instruction.

We need to update Canvas to pass the prop through:
```
import { IdlAccounts, Program } from "@project-serum/anchor";
import { PublicKey } from "@solana/web3.js";
import clsx from "clsx"
import { useEffect, useState, useMemo } from "react";
import { DrawWithFrens } from "../idl/draw_with_frens";
import { Color } from "../lib/colors";
import Pixel from "./Pixel"

interface Props {
  program?: Program<DrawWithFrens>,
  selectedColor: Color,
}

type PixelAccount = IdlAccounts<DrawWithFrens>['pixel']

interface PixelChangedEvent {
  posX: number,
  posY: number,
  colR: number,
  colG: number,
  colB: number,
}

export default function Canvas({ program, selectedColor }: Props) {
  const disabled = !program;
  const [fetchedPixels, setFetchedPixels] = useState<PixelAccount[]>([]);

  const fetchPixels = async () => {
    // this is unchanged
  }

  useEffect(() => {
    fetchPixels()
  }, [program])

  const pixelsMap = useMemo(() => {
    // and this
  }, [fetchedPixels])

  const getPixelAddress = (posX: number, posY: number) => {
    // also this
  }

  // Listen to PixelChanged events
  useEffect(() => {
    // they're just very long to scroll past!
  }, [program])

  return (
    <div className={clsx(disabled && "opacity-25 cursor-not-allowed")}>
      <table className="border border-gray-300 table-fixed">
        <tbody className="divide-y divide-gray-300">
          {[...Array(100)].map((_, y) => {
            return (
              <tr className="divide-x divide-gray-300" key={y}>
                {[...Array(100)].map((_, x) => {
                  const id = y * 100 + x;
                  const pixelData = pixelsMap[id];

                  return <Pixel
                    posX={x}
                    posY={y}
                    program={program}
                    pixelData={pixelData}
                    selectedColor={selectedColor}
                    key={x}
                  />
                })}
              </tr>
            )
          })}
        </tbody>
      </table>
    </div>
  )
}
```

And finally we need to pass it to Canvas:

```
return (
  <div className="flex flex-col items-stretch gap-8 px-4 pt-24 mx-auto max-w-max">
    <main className="flex flex-col gap-4">
      <Title>Draw With Frens</Title>

      <div className="basis-1/4">
        <WalletMultiButton className='!bg-gray-900 hover:scale-105' />
      </div>

      <ColorSelector selectedColor={selectedColor} setSelectedColor={setSelectedColor} />

      <Canvas program={anchorProgram} selectedColor={selectedColor} />
    </main>
  </div>
)
```
Everything outside of that return stays the same :)

And now if you select a different color block at the top, that’ll be used for your new pixels!

<img width="232" alt="21" src="https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/c3214da3-43d6-48ce-9ddf-99a8608a7b69">

Apps that can draw rainbows > apps that can’t!

Updating pixels
-------------------
There’s one thing missing from our app: we can’t update a pixel that’s already been created. If you try clicking on a pixel that’s already got a color you’ll get this lame “transaction may fail to confirm” error (or similar, depending on your wallet!)

<img width="472" alt="22" src="https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/44afb8ea-f856-4f1f-a7dc-3d9980579e7a">

The problem is that we’re always calling that createPixel instruction, and we know that doesn’t work if we already have a pixel at the address. We even wrote a test to make sure of that!

So we need to call our updatePixel instruction instead. Try doing this yourself! We need to do two things in our Pixel component. First, we need a function just like createPixel except it calls our updatePixel instruction instead. You can use our unit tests to see how we call that if you can’t remember. Second, we need to call that instead of createPixel if the address already exists.

You’ll know it’s worked when you can click on an already colored pixel and get something like this:

<img width="472" alt="23" src="https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/32930023-2948-4e14-a3fc-841a4b0cbdfa">

Remember how our update instruction doesn’t initialize any new accounts, so it doesn’t cost any SOL.

updated Pixel component:
```
import { IdlAccounts, Program } from "@project-serum/anchor";
import { PublicKey, SystemProgram } from "@solana/web3.js";
import { DrawWithFrens } from "../idl/draw_with_frens";
import { Color } from "../lib/colors";

type PixelAccount = IdlAccounts<DrawWithFrens>['pixel']

interface Props {
  posX: number,
  posY: number,
  program: Program<DrawWithFrens>,
  pixelData?: PixelAccount,
  selectedColor: Color,
}

export default function Pixel({ posX, posY, program, pixelData, selectedColor }: Props) {
  const { colR, colG, colB } = pixelData || {};
  const color = pixelData ? `rgb(${colR}, ${colG}, ${colB})` : "white"

  const getPixelAddress = () => {
    const [pixelPublicKey] = PublicKey.findProgramAddressSync(
      [Buffer.from("pixel"), Buffer.from([posX, posY])],
      program.programId,
    )
    return pixelPublicKey
  }

  const createPixel = async () => {
    await program.methods
      .createPixel(posX, posY, selectedColor.r, selectedColor.g, selectedColor.b)
      .accounts({
        pixel: getPixelAddress(),
        user: program.provider.publicKey,
        systemProgram: SystemProgram.programId,
      })
      .rpc();
  }

  const updatePixel = async () => {
    await program.methods
      .updatePixel(selectedColor.r, selectedColor.g, selectedColor.b)
      .accounts({
        pixel: getPixelAddress(),
      })
      .rpc();
  }

  return <td
    className="h-4 min-w-[1rem]"
    style={{ backgroundColor: color }}
    onClick={pixelData ? updatePixel : createPixel}
  />
}
```
Our app is now complete! We can draw pixel art on our canvas, and it’s all stored in these on-chain pixel accounts.

In the final lesson we’re going to get our program deployed to the Solana Devnet, and your app deployed to the actual internet. Then you’ll be able to share the domain and draw with frens!

Deploy to Devnet
---------------------
This project is way cooler if you take it off localhost. If we deploy it to Solana Devnet, which is totally free, then our pixels will be stored on the public blockchain and the live collaborative nature will really shine! Anyone, anywhere in the world, can draw with us!

Choosing a Devnet keypair
-------------------------------

We can deploy to Devnet using any keypair. So the one you already have locally is fine, so is the one you have in your browser wallet if they’re different.

We’ll use the one we have locally here. But if you’d like to use one you already have in eg. Phantom instead, you can export the private key. Make sure you don’t use a high value account, ideally it should only be used on localhost or Devnet. Don’t export the private key for a valuable account.

Optional: Using a Phantom wallet in Solana CLI

If you're happy to just keep using the account you have set up locally to deploy to Devnet, you can skip this section.

I’ve included a script in utils-decode that will convert the private key from base58 (as Phantom etc. export it), to the Solana CLI key format (that array of bytes we saw earlier).

Simply call it passing your private key as the only argument:
```
$ node decode.js yourexportedprivatekeyhere > key.json
```
If you check key.json you should see an array of numbers like we saw with our keypair earlier. This can now be used with the Solana CLI:

```
$ solana address -k key.json                              
9AUvdLggr4DezH1FEHFBVqQPNUF9q6sZGTYoPMyum2sk
```
If you like you can make the CLI use it by default:
```
$ solana config set --keypair ${PWD}/key.json
$ cd ~
$ solana address                             
9AUvdLggr4DezH1FEHFBVqQPNUF9q6sZGTYoPMyum2sk
```

Deploying
-------------

First let’s switch our Solana CLI to use devnet:
```
$ solana config set --url devnet                          
Config File: /Users/callummcintyre/.config/solana/cli/config.yml
RPC URL: https://api.devnet.solana.com 
WebSocket URL: wss://api.devnet.solana.com/ (computed)
Keypair Path: /Users/callummcintyre/.config/solana/devnet.json 
Commitment: confirmed
```
It’ll show the updated config, note that the RPC and WebSocket URLs both have devnet now. They were both localhost URLs.

Now in Anchor.toml find the provider section, it should be something like this:
```
[provider]
cluster = "localnet"
wallet = "/Users/callummcintyre/.config/solana/id.json"
```

Update the cluster to devnet

If you’d like to deploy with a different key (in my case I’m going to use the devnet one), update the wallet to the path to that keypair

Here’s my updated one:
```
[provider]
cluster = "devnet"
wallet = "/Users/callummcintyre/.config/solana/devnet.json"
```
Okay now we need to get some SOL into our wallet before deploying, just like we did on localhost:
```
$ solana airdrop 2
```
Unfortunately you can only airdrop 2 SOL at a time on Devnet. You’ll need about 4 SOL for the deploy.

Now we can deploy, this time to devnet:
```
$ anchor deploy        
Deploying workspace: https://api.devnet.solana.com
Upgrade authority: /Users/callummcintyre/.config/solana/devnet.json
Deploying program "draw-with-frens"...
Program path: /Users/callummcintyre/projects/anchor-tutorial/draw-with-frens/target/deploy/draw_with_frens.so...
Program Id: A7Ly2N39Rstzo6ZUBDsdiTjD8rMkdZ6h7ZGrtN5fjRv1

Deploy success
```
You can see your program deployed on the Solana explorer: https://explorer.solana.com/?cluster=devnet

Search the Program Id and you should see the account:

<img width="1139" alt="24" src="https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/f169bc0f-50d6-4388-a2e6-2a5534de8e42">

Updating the frontend
---------------------------
All we need to do is update the endpoint of our app in frontend/pages/_app.tsx:
```
import '../styles/index.css'
import type { AppProps } from 'next/app'
import Head from 'next/head'
import { ConnectionProvider, WalletProvider } from '@solana/wallet-adapter-react'
import { WalletModalProvider } from '@solana/wallet-adapter-react-ui'
import { PhantomWalletAdapter, SolflareWalletAdapter } from '@solana/wallet-adapter-wallets'
import { clusterApiUrl } from '@solana/web3.js'

// Default styles that can be overridden by your app
require('@solana/wallet-adapter-react-ui/styles.css');

function MyApp({ Component, pageProps }: AppProps) {
  // Use devnet cluster
  const endpoint = clusterApiUrl('devnet')

  // @solana/wallet-adapter-wallets includes all the adapters but supports tree shaking and lazy loading --
  // Only the wallets you configure here will be compiled into your application, and only the dependencies
  // of wallets that your users connect to will be loaded.
  const wallets = [
    new PhantomWalletAdapter(),
    new SolflareWalletAdapter(),
  ];

  return (
    // unchanged!
  )
}

export default MyApp
```
Now if you open your app you’ll see a new canvas, the devnet one! Update your wallet to use the Devnet network and make sure your connected wallet has some SOL. If you don’t have any, you can airdrop some to that account from the CLI: solana airdrop 2 Fkc4FN7PPhyGsAcHPW3dBBJ4BvtYkDr2rBFBgFpvy3nB - just use whichever address you have in your wallet. 2 SOL will be more than enough to try it out!

Awesome! Your app is deployed and working on Devnet!

Deploying the frontend
---------------------------

There’s nothing magic here, our app is just a fully static NextJS app. It can be deployed anywhere you like. It doesn’t require any server config or environment variables. All the infrastructure is provided by Solana!

The easiest place to deploy it is probably Vercel: just point it at your github repo with the fork (make sure you push your changes!). You’ll need to set the root directory to frontend, since you only want Vercel to deploy that directory:

<img width="869" alt="25" src="https://github.com/Yusufcihan1/pixel-game-using-anchor/assets/50721899/05d016a0-9db6-4d7d-bafd-193da9c09ffc">

Huge congrats on completing the tutorial! Hopefully you’ve learned lots building an awesome app! 🔥
-----
REFERENCES 
-----------------
https://solanacookbook.com/
https://docs.solana.com/
https://solana.com/tr/developers/gaming
https://www.anchor-lang.com/
https://learn.figment.io/tutorials/build-a-blog-dapp-using-anchor
https://www.youtube.com/watch?v=CmG5_sIas1Q

