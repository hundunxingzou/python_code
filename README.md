# 运行顺序
- 一定要yecaptcha 或 2captcha的账号，我们需要里面的client_key
```
BeraChainTools(private_key=account.key, client_key=client_key,solver_provider='yescaptcha',rpc_url='https://rpc.ankr.com/berachain_testnet')
```
这里我们选择的是solver_provider='yescaptcha'。
- 在config文件里的Add_address.json文件添加你的私钥和client_key以及solver_provider，可以添加多个私钥
- 运行顺序，先运行bex_script——>honey_script——>bend_script——>OXhoney_script，后面这三个程序可以打乱顺序运行，只要确保手续费足够
- 手动领水地址：https://artio.faucet.berachain.com/

# BeraChainTools

BeraChainTools 一个为 BeraChain 生态系统设计的工具集，旨在帮助用户轻松地进行各种交互和操作。

### 安装依赖

在开始使用 BeraChainTools 之前，请确保安装了所有必要的依赖。

执行以下命令以安装依赖：

```
pip install -r requirements.txt
```

### Examples

- bera目前验证更改为 CloudflareTurnstile，目前支持 yecaptcha 和 2captcha 解码
- 如果你还没有 YesCaptcha 账号，请先在这里注册：[yescaptcha注册链接](https://yescaptcha.com/i/0vVEgw)。
- 如果你还没有 2captcha 账号，请先在这里注册：[2captcha注册链接](https://cn.2captcha.com/?from=9389597)。
- 如果你还没有 ez-captcha
  账号，请先在这里注册：[ez-captcha注册链接](https://dashboard.ez-captcha.com/#/register?inviteCode=djnhuqvHuQJ)。



Bex 交互:

```python
#注释部分运行不稳定
from eth_account import Account
from loguru import logger

from bera_tools import BeraChainTools
from config.address_config import (
    usdc_address, wbear_address, weth_address, bex_approve_liquidity_address,
    usdc_pool_liquidity_address, weth_pool_liquidity_address,address_key,
    bex_swap_address
)
from config.yescaptcha import client_key,solver_provider

for address in address_key:
    account = Account.from_key(address)#这里是私钥在config文件里添加地址
    #client_key与solver_provider需要注册yescaptcha
    bera = BeraChainTools(private_key=account.key,client_key=client_key, solver_provider=solver_provider,rpc_url='https://rpc.ankr.com/berachain_testnet')

    # bex 使用bera交换usdc
    bera_balance = bera.w3.eth.get_balance(account.address)
    logger.debug(bera_balance)
    result = bera.bex_swap(int(bera_balance * 0.2), wbear_address, usdc_address)
    logger.debug(result)
    
    # bex 使用usdc交换weth，目前与weth无法操作，官网也无法执行
    #usdc_balance = bera.usdc_contract.functions.balanceOf(account.address).call()
    #print(usdc_balance)
    #approve_result=bera.approve_token(bex_swap_address, int("0x" + "f" * 64, 16), usdc_address)
    #logger.debug(approve_result)
    #result = bera.bex_swap(int(usdc_balance * 0.1), usdc_address, weth_address)
    #logger.debug(result)##

    # 授权usdc
    approve_result = bera.approve_token(bex_approve_liquidity_address, int("0x" + "f" * 64, 16), usdc_address)
    logger.debug(approve_result)
    # bex 增加 usdc 流动性
    usdc_balance = bera.usdc_contract.functions.balanceOf(account.address).call()
    result = bera.bex_add_liquidity(int(usdc_balance * 0.5), usdc_pool_liquidity_address, usdc_address)
    logger.debug(result)
    
    # 授权weth
    #approve_result = bera.approve_token(bex_approve_liquidity_address, int("0x" + "f" * 64, 16), weth_address)
    #logger.debug(approve_result)
    # bex 增加 weth 流动性
    #weth_balance = bera.weth_contract.functions.balanceOf(account.address).call()
    #result = bera.bex_add_liquidity(int(weth_balance * 0.5), weth_pool_liquidity_address, weth_address)
    #logger.debug(result)

```
Honey 交互:

```python
#注释部分运行不稳定
from eth_account import Account
from loguru import logger

from bera_tools import BeraChainTools
from config.address_config import honey_swap_address, usdc_address, honey_address,address_key
from config.yescaptcha import client_key,solver_provider
for address in address_key:
   account = Account.from_key(address)
   bera = BeraChainTools(private_key=account.key, rpc_url='https://rpc.ankr.com/berachain_testnet',client_key=client_key, solver_provider=solver_provider)

   # 授权usdc
   #approve_result = bera.approve_token(honey_swap_address, int("0x" + "f" * 64, 16), usdc_address)
   #logger.debug(approve_result)

   # 使用usdc mint honey
   usdc_balance = bera.usdc_contract.functions.balanceOf(account.address).call()
   result = bera.honey_mint(int(usdc_balance * 0.5))
   logger.debug(result)

    # 授权honey
    #approve_result = bera.approve_token(honey_swap_address, int("0x" + "f" * 64, 16), honey_address)
    #logger.debug(approve_result)
    # 赎回 
    #honey_balance = bera.honey_contract.functions.balanceOf(account.address).call()
    #result = bera.honey_redeem(int(honey_balance * 0.5))
    #logger.debug(result)

```

 Bend 交互:

```python
#注释部分运行不稳定
from eth_account import Account
from loguru import logger

from bera_tools import BeraChainTools
from config.address_config import bend_address, weth_address, honey_address, bend_pool_address,address_key, wbear_address
from config.yescaptcha import client_key,solver_provider

for address in address_key:
    account = Account.from_key(address)#这里是私钥在config文件里添加地址
    #client_key与solver_provider需要注册yescaptcha,在yescaptcha文件里添加
    bera = BeraChainTools(private_key=account.key,client_key=client_key, solver_provider=solver_provider,rpc_url='https://rpc.ankr.com/berachain_testnet')

    # 授权
    #approve_result = bera.approve_token(bend_address, int("0x" + "f" * 64, 16),  wbear_address)
    #logger.debug(approve_result)
    # deposit
    #bera_balance = bera.w3.eth.get_balance(account.address)
    #result = bera.bend_deposit(int(bera_balance*0.2),  wbear_address)
    #logger.debug(result)

    # borrow
    balance = bera.bend_contract.functions.getUserAccountData(account.address).call()[2]
    logger.debug(balance)
    result = bera.bend_borrow(int(balance * 0.8 * 1e10), honey_address)
    logger.debug(result)

   
    # 查询数量 
    call_result = bera.bend_borrows_contract.functions.getUserReservesData(bend_pool_address, bera.account.address).call()
    repay_amount = call_result[0][0][4]
    logger.debug(repay_amount)
    # 授权
    approve_result = bera.approve_token(bend_address, int("0x" + "f" * 64, 16), honey_address)
    logger.debug(approve_result)
    # repay
    result = bera.bend_repay(int(repay_amount * 1), honey_address)
    logger.debug(result)
```

0xhoneyjar 交互:

```python

from eth_account import Account
from loguru import logger

from bera_tools import BeraChainTools
from config.address_config import ooga_booga_address, honey_address,address_key
from config.yescaptcha import client_key,solver_provider
for address in address_key:
  account = Account.from_key(address)
  bera = BeraChainTools(private_key=account.key,client_key=client_key, solver_provider=solver_provider, rpc_url='https://rpc.ankr.com/berachain_testnet')


    # https://faucet.0xhoneyjar.xyz/mint
  # 授权
  approve_result = bera.approve_token(ooga_booga_address, int("0x" + "f" * 64, 16), honey_address)
  logger.debug(approve_result)
  # 花费4.2 honey mint
  result = bera.honey_jar_mint()
  logger.debug(result)

```





