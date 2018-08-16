"""
# Registering to a remote testnet

This file can be executed as a python script: 'python3 
registering_to_testnode.md'.

## Set-up

The set-up statements are explained at <a href="setup.html">cases/setup</a>.

### Switching between testnodes

There is many testnodes: all we know are volatile: the one that you are 
prepared to use is stopped just when you need it. Therefore we have provision 
for easy change of the used testnode:

```md
"""
import setup
file_prefix = setup.remote_testnet_setup("88.99.97.30:38888")
"""
```
Where the argument is the url of a testnode. The statement makes the following
arrangements:

* sets the url of the remote node;
* sets the default name of the singleton wallet object, for example here
    `88_99_97_30_38888_default.wallet`;
* sets accordingly the names of the files that map passwords and account 
    names (note that passwords for real-money wallets are never stored 
    automatically)

We do not want to reserve this default naming system for the current test, 
then we use an option of the function `setup.remote_testnet_setup(...)` --

```md
"""
file_prefix = setup.remote_testnet_setup(
    "88.99.97.30:38888", "registering_to_testnode")
"""
```
-- resulting with a specific naming prefix, for example, the wallet name is `registering_to_testnode_default.wallet`, now.

### A test trick

The following account exists in the blockchain of a testnode. It is used, in
this article, for testing. It is referred to as the 'testing account'.

```md
Owner Public Key: EOS8AipFftYjovw8xpuqCxsjid57XqNstDyeTVmLtfFYNmFrgY959
Active Public Key: EOS6HDfGKbR79Gcs74LcQfvL6x8eVhZNXMGZ48Ti7u84nDnyq87rv

Owner Private Key: OWNER_KEY
Active Private Key: ACTIVE_KEY 
```
### The header of the test

Note the statement put above, namely `setup.remote_testnet_setup("88.99.97.30:38888")`.

```md
"""
import os
import unittest
import setup
import eosf
import eosf_account

from eosf import Verbosity
from eosf_wallet import Wallet
from eosf_account import account_master_create
from user_data import *

eosf.Logger.verbosity = [Verbosity.EOSF, Verbosity.OUT]
_ = eosf.Logger()

"""
```

### Specify that the KEOSD Wallet Manager is used

```md
"""
eosf.use_keosd(True)
"""
```

### Throw an exception if the testnode is off:

Now make sure that the chosen testnode is operative. The 
`eosf.set_throw_error(...)` statement below switches between error management 
modes: an error may either throw a fatal exception or it may print a message. 

We dedicate the former way to the setup phase of a test.

```md
"""
eosf.set_throw_error(True)
eosf.info()
eosf.set_throw_error(False)
"""
```

### Clean the results of a possible previous use of the current script.

```md
"""
eosf.kill_keosd()   # otherwise, the manager may protects the wallet files

dir = eosf.wallet_dir()
files = os.listdir(dir)
for file in files:
    if file.startswith(file_prefix):
        os.remove(os.path.join(dir,file))
"""
```

```md
"""
wallet = Wallet()

eosf.set_is_testing_errors(False)
eosf.set_throw_error(True)

"""
```

### Introduce a test trick

We use an active account, named 'account_master_test' to simulate the 
registration procedure: if set, this account substitutes one that would be
physically registered.

In the following definitions, we use real data obtained with a real registration
session executed with the procedure that is shown in this article.

The constants 'ACCOUNT_NAME', 'OWNER_KEY', 'ACTIVE_KEY' are defined in the 
script 'user_data.py`. We hope, that you will replace them with your own 
data, as they are used in other articles.

```md
"""
exit()
eosf_account.account_master_test = eosf_account.GetAccount(
    "account_master_test",
    ACCOUNT_NAME, 
    OWNER_KEY,
    ACTIVE_KEY
)
eosf_account.account_master_test.ERROR()
"""
```

### End the set-up part

```md
"""
eosf.set_throw_error(False)         # on error, do not throw exception
eosf.set_is_testing_errors()        # make error messages less alarming
"""
```

## Case

In subsequent tests, you may have to change the account object name, here 
'account_master', or to resolve name conflicts, if you are prompted.

### Register to the testnode

```md
"""
account_master_create("account_master")
"""
```

### Test run

With a linux bash, change directory to where this file exists, that is the 
directory 'docs/source/cases' in the repository, and enter the following 
command:

```md
$ python3 registering_to_testnode.md
```

We hope that you get something similar to this one shown in the image below.

<img src="registering.png" 
    onerror="this.src='../../../source/cases/registering.png'"   
    alt="registering" width="640px"/>
    
"""