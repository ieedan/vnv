# valid-env
A decorator enhanced .env syntax for validating your environment variables all within a .vnv file.

![CI](https://github.com/ieedan/valid-env/actions/workflows/continuous-integration.yml/badge.svg?branch=main)

```ruby
@startsWith("https://")
# Must be min 10 for name of domain and domain postfix Ex: (https://d.t)
@min(10)
ALLOWED_ORIGINS=["https://github.com", "https://aidanbleser.com"]

@min(1000)
@public
@dev
POLLING_INTERVAL=5000

@min(1000)
@public
@prod
POLLING_INTERVAL=5000

@min(1024)
@max(49151)
@public
PORT=3000

@dev
@min(1)
API_KEY="g74Ed6Z6txrEiGX9rSybQxWfVCFDfvAvhuOBrZvsTjfuGNrNt1jyjHfhQPSdzNh5kf6juBsGfRhjFpyfJEl8L2pw39DCs2A2yJKLfWht6sY7HCalLNpDNWcHbWip8Jpc"

# IP Address regex thanks ChatGPT
@matches("^(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$")
ALLOWED_IPS=["172.16.100.10", "192.168.1.1"]
```

```bash
C:\Users\aidan> vnv check
Checking '.vnv'...
ALLOWED_ORIGINS ✔️
POLLING_INTERVAL ❌
ERROR: 500 is too small. Minimum value is 1000.
--> .vnv:10:1
     |
10   |  POLLING_INTERVAL=500
     |                   ^^^

PORT ✔️
API_KEY ✔️
ALLOWED_IPS ✔️
Check completed in 1.74ms
```

> Note: This is not stable for production use

## Getting Started

1. Download the source code and compile the binary. 

2. Set your system path variable to point to the binary.

3. Check you have it correctly configured with `vnv --version`

4. Download the VS Code extension for syntax highlighting [here](https://marketplace.visualstudio.com/items?itemName=AidanBleser.valid-env).

5. Run `vnv init` to setup the config file and optional template files.

6. Run `vnv check` to validate your environment variables

7. Run `vnv build` to build your `.vnv` file into a `.env` file

## Variable Types
Currently valid-env supports 4 different types of environment variables.

- String
- Number
- String[]
- Number[]

For simplicity to the end user all numbers are 64 bit floating point integers.

## Decorator Enhanced
valid-env extends the .env syntax with decorators that allow you to validate and scope your environment variables.

### Decorators
- [@public](#public)
- [@private](#private)
- [@dev](#dev)
- [@prod](#prod)
- [@min](#min)
- [@max](#max)
- [@startsWith](#startsWith)
- [@endsWith](#endsWith)
- [@matches](#matches)
- [@doesNotMatch](#doesNotMatch)

### @public
Changes the scope of the environment variable to **public**;

Usage:
```ruby
@public
PORT=3000
```

#### Allowed Variable Types
- String
- Number
- String[]
- Number[]

### @private
This is not necessary as all variables by default are **private** but may be useful to annotate the importance for a variables privacy. 

Usage:
```ruby
@private
SECRET="this is a super private secret"
```

#### Allowed Variable Types
- String
- Number
- String[]
- Number[]

### @dev
Changes the environment of the environment variable to **dev**;

Usage:
```ruby
@dev
PORT=3000
```

#### Allowed Variable Types
- String
- Number
- String[]
- Number[]

### @prod
Changes the environment of the environment variable to **prod**;

Usage:
```ruby
@prod
PORT=3000
```

#### Allowed Variable Types
- String
- Number
- String[]
- Number[]

### @min
Allows you to validate the minimum length or size of a variable. For number types it will validate the size of the number. For string types it will validate the length.

Usage:
```ruby
@min(1000)
POLLING_INTERVAL=5000
@min(10)
DOMAIN="https://google.com"
@min(1024)
MICROSERVICE_PORTS=[8080, 8081, 8082]
@min(5)
ADMIN_EMAILS=["aidan@gmail.com", "john@yahoo.com"]
```

#### Allowed Variable Types
- String
- Number
- String[]
- Number[]

### @max
Allows you to validate the maximum length or size of a variable. For number types it will validate the size of the number. For string types it will validate the length.

Usage:
```ruby
@max(45000)
POLLING_INTERVAL=5000
@max(25)
APP_NAME="super-powered-app"
@max(49151)
MICROSERVICE_PORTS=[8080, 8081, 8082]
@max(254)
ADMIN_EMAILS=["aidan@gmail.com", "john@yahoo.com"]
```

#### Allowed Variable Types
- String
- Number
- String[]
- Number[]

### @startsWith
Allows you to validate the start of a string variable.

Usage:
```ruby
@startsWith("https://")
DOMAIN="https://google.com"
@startsWith("https://")
ALLOWED_ORIGINS=["https://google.com", "https://github.com"]
```

#### Allowed Variable Types
- String
- String[]

### @endsWith
Allows you to validate the end of a string variable.

Usage:
```ruby
@endsWith("@gmail.com")
EMAIL="john.doe@gmail.com"
@endsWith("@gmail.com")
ALLOWED_ORIGINS=["john.doe@gmail.com", "jane.doe@gmail.com"]
```

#### Allowed Variable Types
- String
- String[]

### @matches
Enables regex based pattern matching to validate string variables. Will return an error when the pattern doesn't match. 

Usage:
```ruby
# Digits only regex
@matches("^\d+$")
PHONE_NUMBER="4427211223"
@matches("^\d+$")
PHONE_NUMBERS=["4427211223", "4427511227", "4428211213"]
```

#### Allowed Variable Types
- String
- String[]

### @doesNotMatch
Enables regex based pattern matching to validate string variables. Will return an error when the pattern matches.

Usage:
```ruby
# Special characters regex
@doesNotMatch("[^\w.]")
SUPER_USER="admin"
@doesNotMatch("[^\w.]")
ADMIN_USERNAMES=["johnothy", "jimnothy"]
```

#### Allowed Variable Types
- String
- String[]

## Public and Private
Some environment variable handlers allow you to scope your variables to be public or private. (For example [SvelteKit](https://learn.svelte.dev/tutorial/env-static-private)). This allows you to separate privileges to use environment variables between server and client code. By default all variables are scoped as **private** but can be marked public using the `@public` decorator.

> Note: While the `@private` decorator is valid syntax and listed as a decorator it does not change the scope of the variable. However it can be useful for annotating something that should be treated as sensitive and should not be changed to public.

## Environments 
Sometimes you want to use different values for your variables for different environments or even different variables entirely. This is made possible with the [@dev](#dev) and [@prod](#prod) decorators.

Here are a few examples:

Different Value for same variable
```ruby
@dev
PORT=3000
@prod
PORT=8080
```

Omit a variable from prod
```ruby
@dev
KEY="..."
```

Omit a variable from dev
```ruby
@prod
KEY="..."
```

> Keep in mind any keys not marked with `@prod` or `@dev` will be included in all environments.

### How to specify the environment
Pass the `--prod` or `--dev` flag to the check/build command. By default the environment is set to `dev` so theres no need to supply the `--dev` flag.

.vnv file:
```ruby
@dev
PORT=3000
@prod
PORT=8080
```

Output:
```bash
C:\Users\aidan\project> vnv build --dev
Checking '.vnv'...
PORT ✔️
PORT ⏭️
Completed in 1.24ms
Completed build wrote output to .env.
```

Generated .env file: 
```ruby
# This file was generated from '.vnv' by vnv.

# @dev
PORT=3000
```