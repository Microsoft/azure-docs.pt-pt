---
title: Implementar a Oracle Data Guard numa máquina virtual Azure Linux Microsoft Docs
description: Rapidamente coloque a Oracle Data Guard a funcionar no seu ambiente Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dbakevlar
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: f8a40f87b8fdaa2adf445392bd598dd8133aae4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335906"
---
# <a name="implement-oracle-data-guard-on-an-azure-linux-virtual-machine"></a>Implementar a Oracle Data Guard numa máquina virtual Azure Linux 

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este artigo descreve como usar o Azure CLI para implantar uma base de dados Oracle Database 12c a partir da imagem do Azure Marketplace. Este artigo mostra-lhe, passo a passo, como instalar e configurar a Data Guard numa máquina virtual Azure (VM).

Antes de começar, certifique-se de que o Azure CLI está instalado. Para mais informações, consulte o guia de instalação do [Azure CLI.](/cli/azure/install-azure-cli)

## <a name="prepare-the-environment"></a>Preparar o ambiente
### <a name="assumptions"></a>Pressupostos

Para instalar a Oracle Data Guard, é necessário criar dois VMs Azure no mesmo conjunto de disponibilidade:

- O VM primário (myVM1) tem uma instância Oráculo em execução.
- O VM de standby (myVM2) tem o software Oracle instalado apenas.

A imagem do Marketplace que utiliza para criar os VMs é Oracle:Oracle-Database-Ee:12.1.0.2:mais recente.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão na sua subscrição Azure utilizando o comando [de login az](/cli/azure/reference-index) e siga as instruções no ecrã.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos Azure é um recipiente lógico no qual os recursos da Azure são implantados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

Criar um conjunto de disponibilidade é opcional, mas recomendamos. Para mais informações, consulte [as diretrizes de disponibilidade do Azure.](../../windows/infrastructure-example.md)

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie um VM utilizando o comando [az vm criar.](/cli/azure/vm) 

O exemplo a seguir cria dois VMs nomeados `myVM1` e `myVM2` . Também cria chaves SSH, se já não existirem num local de chave predefinido. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.

Criar myVM1 (primário):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

Depois de criar o VM, o Azure CLI mostra informações semelhantes ao exemplo seguinte. Note o valor de `publicIpAddress` . Utilize este endereço para aceder ao VM.

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

Criar myVM2 (standby):

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

Note o valor de depois de `publicIpAddress` criar o myVM2.

### <a name="open-the-tcp-port-for-connectivity"></a>Abra a porta TCP para a conectividade

Este passo configura pontos finais externos, que permitem o acesso remoto à base de dados Oráculo.

Abra a porta para o myVM1:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

O resultado deve ser semelhante à seguinte resposta:

```output
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

Abra a porta para o myVM2:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual

Utilize o seguinte comando para criar uma sessão SSH com a máquina virtual. Substitua o endereço IP pelo `publicIpAddress` valor da sua máquina virtual.

```bash 
$ ssh azureuser@<publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Criar a base de dados no myVM1 (primário)

O software Oracle já está instalado na imagem do Marketplace, pelo que o próximo passo é instalar a base de dados. 

Mude para o super-alimentador Oráculo:

```bash
$ sudo su - oracle
```

Criar a base de dados:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```

As saídas devem ser semelhantes à seguinte resposta:

```output
Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
```

Definir as variáveis ORACLE_SID e ORACLE_HOME:

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
```

Opcionalmente, pode adicionar ORACLE_HOME e ORACLE_SID ao ficheiro /home/oracle/.bashrc, de modo a que estas definições sejam guardadas para futuros logins:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

## <a name="configure-data-guard"></a>Configure a Guarda de Dados

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>Ativar o modo de registo de arquivo no myVM1 (primário)

```bash
$ sqlplus / as sysdba
SQL> SELECT log_mode FROM v$database;

LOG_MODE
------------
NOARCHIVELOG

SQL> SHUTDOWN IMMEDIATE;
SQL> STARTUP MOUNT;
SQL> ALTER DATABASE ARCHIVELOG;
SQL> ALTER DATABASE OPEN;
```

Ativar a exploração de força e certificar-se de que está presente pelo menos um ficheiro de registo:

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
```

Criar registos de redo de espera:

```bash
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo01.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo02.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo03.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo04.log') SIZE 50M;
```

Ligue o Flashback (o que facilita muito a recuperação) e desemote a GESTÃO DE FICHEIROS STANDBY \_ \_ para auto. Sai do SQL*Plus depois disso.

```bash
SQL> ALTER DATABASE FLASHBACK ON;
SQL> ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
SQL> EXIT;
```

### <a name="set-up-service-on-myvm1-primary"></a>Configurar o serviço no myVM1 (primário)

Editar ou criar o ficheiro tnsnames.ora, que está na pasta $ORACLE_HOME\network\admin.

Adicione as seguintes entradas:

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

Editar ou criar o ficheiro listener.ora, que está na pasta $ORACLE_HOME\network\admin.

Adicione as seguintes entradas:

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

Ativar o corretor de guarda de dados:

```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

Inicie o ouvinte:

```bash
$ lsnrctl stop
$ lsnrctl start
```

### <a name="set-up-service-on-myvm2-standby"></a>Configurar o serviço no myVM2 (standby)

SSH para myVM2:

```bash 
$ ssh azureuser@<publicIpAddress>
```

Faça login como Oráculo:

```bash
$ sudo su - oracle
```

Editar ou criar o ficheiro tnsnames.ora, que está na pasta $ORACLE_HOME\network\admin.

Adicione as seguintes entradas:

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

Editar ou criar o ficheiro listener.ora, que está na pasta $ORACLE_HOME\network\admin.

Adicione as seguintes entradas:

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

Inicie o ouvinte:

```bash
$ lsnrctl stop
$ lsnrctl start
```


### <a name="restore-the-database-to-myvm2-standby"></a>Restaurar a base de dados para o myVM2 (standby)

Criar o ficheiro de parâmetro /tmp/initcdb1_stby.ora com os seguintes conteúdos:

```bash
*.db_name='cdb1'
```

Criar pastas:

```bash
mkdir -p /u01/app/oracle/oradata/cdb1/pdbseed
mkdir -p /u01/app/oracle/oradata/cdb1/pdb1
mkdir -p /u01/app/oracle/fast_recovery_area/cdb1
mkdir -p /u01/app/oracle/admin/cdb1/adump
```

Criar um ficheiro de senha:

```bash
$ orapwd file=/u01/app/oracle/product/12.1.0/dbhome_1/dbs/orapwcdb1 password=OraPasswd1 entries=10
```

Inicie a base de dados no myVM2:

```bash
$ export ORACLE_SID=cdb1
$ sqlplus / as sysdba

SQL> STARTUP NOMOUNT PFILE='/tmp/initcdb1_stby.ora';
SQL> EXIT;
```

Restaurar a base de dados utilizando a ferramenta RMAN:

```bash
$ rman TARGET sys/OraPasswd1@cdb1 AUXILIARY sys/OraPasswd1@cdb1_stby
```

Executar os seguintes comandos em RMAN:

```bash
DUPLICATE TARGET DATABASE
  FOR STANDBY
  FROM ACTIVE DATABASE
  DORECOVER
  SPFILE
    SET db_unique_name='CDB1_STBY' COMMENT 'Is standby'
  NOFILENAMECHECK;
```

Deverá ver mensagens semelhantes às seguintes quando o comando estiver concluído. Saída RMAN.

```output
media recovery complete, elapsed time: 00:00:00
Finished recover at 29-JUN-17
Finished Duplicate Db at 29-JUN-17
```

```bash
RMAN> EXIT;
```

Opcionalmente, pode adicionar ORACLE_HOME e ORACLE_SID ao ficheiro /home/oracle/.bashrc, de modo a que estas definições sejam guardadas para futuros logins:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

Ativar o corretor de guarda de dados:
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

### <a name="configure-data-guard-broker-on-myvm1-primary"></a>Configure o corretor de guarda de dados no myVM1 (primário)

Inicie o Data Guard Manager e faça login utilizando sYS e uma senha. (Não utilize a autenticação do SO.) Executar o seguinte:

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> CREATE CONFIGURATION my_dg_config AS PRIMARY DATABASE IS cdb1 CONNECT IDENTIFIER IS cdb1;
Configuration "my_dg_config" created with primary database "cdb1"
DGMGRL> ADD DATABASE cdb1_stby AS CONNECT IDENTIFIER IS cdb1_stby MAINTAINED AS PHYSICAL;
Database "cdb1_stby" added
DGMGRL> ENABLE CONFIGURATION;
Enabled.
```

Reveja a configuração:

```bash
DGMGRL> SHOW CONFIGURATION;

Configuration - my_dg_config

  Protection Mode: MaxPerformance
  Members:
  cdb1      - Primary database
    cdb1_stby - Physical standby database

Fast-Start Failover: DISABLED

Configuration Status:
SUCCESS   (status updated 26 seconds ago)
```

Completou a instalação da Oracle Data Guard. A próxima secção mostra-lhe como testar a conectividade e mudar.

### <a name="connect-the-database-from-the-client-machine"></a>Ligue a base de dados da máquina de clientes

Atualize ou crie o ficheiro tnsnames.ora na sua máquina de cliente. Este ficheiro encontra-se normalmente em $ORACLE_HOME\network\admin.

Substitua os endereços IP pelos seus `publicIpAddress` valores para myVM1 e myVM2:

```bash
cdb1=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM1 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1)
    )
  )

cdb1_stby=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM2 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1_stby)
    )
  )
```

Iniciar SQL*Plus:

```bash
$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

## <a name="test-the-data-guard-configuration"></a>Teste a configuração da Guarda de Dados

### <a name="switch-over-the-database-on-myvm1-primary"></a>Altere a base de dados no myVM1 (primário)

Para mudar de primário para standby (cdb1 para cdb1_stby):

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1_stby;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1_stby"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1_stby" is opening...
Operation requires start up of instance "cdb1" on database "cdb1"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1_stby"
DGMGRL>
```

Pode agora ligar-se à base de dados de espera.

Iniciar SQL*Plus:

```bash

$ sqlplus sys/OraPasswd1@cdb1_stby
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

### <a name="switch-over-the-database-on-myvm2-standby"></a>Altere a base de dados no myVM2 (standby)

Para mudar, passe o seguinte no myVM2:

```bash
$ dgmgrl sys/OraPasswd1@cdb1_stby
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1" is opening...
Operation requires start up of instance "cdb1" on database "cdb1_stby"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1"
```

Mais uma vez, deverá poder ligar-se à base de dados primária.

Iniciar SQL*Plus:

```bash

$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

Terminou a instalação e configuração da Data Guard no Oracle Linux.


## <a name="delete-the-virtual-machine"></a>Elimine a máquina virtual

Quando já não precisar do VM, pode utilizar o seguinte comando para remover o grupo de recursos, VM e todos os recursos relacionados:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Criar máquinas virtuais altamente disponíveis](../../linux/create-cli-complete.md)

[Explore amostras de CLI de implantação VM](../../linux/cli-samples.md)
