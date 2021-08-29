---
description: 'Run croncat directly via CLI'
sidebar: 'docs'
# prev: '/docs/'
next: '/docs/agent-docker/'
---

# Агент CLI

Привет друг! Поскольку ты здесь, ты вероятно заинтересован в установке cron agent, чтобы помочь в децентрализации cron и зарабатывать вознаграждения выполняя задачи! Это руководство посвящено настройке, с нуля до создания собственных задач. Обратите внимание: вам понадобятся немного токенов NEAR, чтобы иметь возможность подписывать транзакции. Но не волнуйтесь, все средства, необходимые для подписания транзакций, будут возвращены в дополнение к вознаграждениям, которые вы заработаете.

Как исполнитель задач, вы можете поставлять Cron с надежным и постоянным механизмом который будет подписывать транзакции, зарегистрировав свой сервер в качестве официального агента Cron. Это займет несколько шагов, так что приступим! 

## 1. Настройка

### Установить

```bash
npm i -g croncat near-cli
```

### Логин

Перед запуском агента убедитесь, что вы вошли в аккаунт NEAR:

```bash
near login
```

Авторизация позволяет вашему cron-агенту иметь доступ к аккаунту, который будет использоваться для подписи транзакций. Если у вас нет NEAR аккаунта с 1 NEAR или более, вы можете создать его здесь: [Создать NEAR Кошелек](https://wallet.near.org/)

В качестве альтернативы вы можете создать новый набор ключей только для агента croncat:

```bash
near generate-key agent-007.testnet
```

Это должно создать следущую директорию (если оно не была созданна раннее):

```bash
ls ~/.near-credentials
default testnet
```

После создания скопируйте открытый ключ (который будет использоваться для добавления как signer), затем выполните следующую команду с финансируемой учетной записью, которой вы владеете:

```bash
near add-key your_account.testnet ed25519:EkiM...
```

## 2. Test out the CLI

Run docker in detached mode and set the agent account id:
```bash
croncat tasks
```

## 3. Register Your Agent

To be able to earn rewards with croncat, the manager needs to know which account will be earning rewards. You can register by the following:

```bash
# croncat register <agent account> <rewards account>
croncat register agent-007.testnet your_main_account.testnet
```

Note, you if make your rewards account different than your agent account, you will need to setup a way to make sure the agent account stays funded, as it does need to pay for transaction executions.

## 4. Testing Task Runner

Run the croncat agent and set the agent account id:

```bash
croncat go agent-007.testnet
```

## 5. Running in Production

Once you've run all the above commands, its time to move into something much more stable. If you are only using the CLI as your agent, you can setup a screen session so the runtime persists even when you are not active in the shell runtime. You can also checkout the "Agent Docker" setup for another way to persist agent runtime.

```bash
# Start a new session
screen -S croncat

# Start the croncat agent
croncat go agent-007.testnet > log 2>&1

# Exit the session
Ctrl+a d

# Watch session logs
tail -n 1000 -f log

# Resume session
screen -rD croncat

# Stop croncat
Ctrl+c
```

----

## All CLI Commands

For a list of up-to-date commands, run `croncat --help` in your terminal.

```bash
Usage: croncat <command> [options]

Commands:
  croncat register <accountId> <payableAccountId>  Add your agent to cron known agents
  croncat update <accountId> <payableAccountId>    Update your agent to cron known agents
  croncat unregister <accountId>                   Account to remove from list of active agents.
  croncat withdraw <accountId>                     Withdraw all rewards earned for this account
  croncat status <accountId>                       Check agent status and balance for this account
  croncat tasks                                    Check how many tasks are available
  croncat go <accountId>                           Run tasks that are available, if agent is registered and has balance
```

----

## Register
 In order to run Cron tasks and receive rewards you need to register your server as a participant in the Cron service by submitting your server wallet account ID and reward account id (`payableAccountId`).

Add your agent to Cron known agents:
```bash
croncat register <accountId> <payableAccountId>
```

Example:
```bash
croncat register cron-agent.testnet cron-agent-wallet.testnet
```

## Update Agent
If you need to change your Cron Agent account or the rewards account run the following:

Example:
```bash
croncat update cron-agent.testnet new-cron-agent-wallet.testnet
```


## Unregister
At any time you may choose to unregister your Cron Agent and stop it from running any further Cron tasks. Note that this will cancel any upcoming tasks that may have been pre-assigned, but it will not cancel any tasks that are pending / submitted. You may re-register your agent at anytime.

Remove your agent from list of known Cron agents:
```bash
croncat unregister <accountId>
```

Example:
```bash
croncat unregister cron-agent.testnet
```

## Rewards
Rewards will be automatically paid out directly to your configured beneficiary account ID at time of execution or in batches.

Withdraw all rewards earned for this account:
```bash
croncat withdraw <accountId>
```

Example:
```bash
croncat withdraw cron-agent.testnet
```

## Tasks
At any time you may view all tasks that your agent can run. In most cases, this list will be how your agent knows if it should be signing a transaction or maintaining an idle but active status. To view the list execute the following:

```bash
croncat tasks
```
