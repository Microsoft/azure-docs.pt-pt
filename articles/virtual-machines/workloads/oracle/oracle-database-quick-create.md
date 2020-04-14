---
title: Crie uma base de dados Oracle num Azure VM [ Microsoft Docs
description: Rapidamente obtenha uma base de dados Oracle Database 12c em funcionamento no seu ambiente Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: mimckitt
ms.openlocfilehash: 901a6ed9075f7e368c0706ac6ba5fc2900bea9a8
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262133"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Crie uma Base de Dados Oracle num VM Azure

Este guia detalha a utilização do Azure CLI para implantar uma máquina virtual Azure a partir da imagem da [galeria de marketplace Oracle,](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) a fim de criar uma base de dados Oracle 12c. Assim que o servidor for implantado, irá ligar-se via SSH para configurar a base de dados Oracle. 

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Para criar uma máquina virtual (VM), utilize o [az vm criar](/cli/azure/vm) comando. 

O exemplo seguinte cria uma VM com o nome `myVM`. Também cria chaves SSH, se ainda não existirem num local de chave predefinido. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

Depois de criar o VM, o Azure CLI apresenta informações semelhantes ao seguinte exemplo. Note o `publicIpAddress`valor para . Usa este endereço para aceder ao VM.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>Ligar à VM

Para criar uma sessão SSH com o VM, utilize o seguinte comando. Substitua o endereço `publicIpAddress` IP pelo valor do seu VM.

```bash
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>Criar a base de dados

O software Oracle já está instalado na imagem do Marketplace. Crie uma base de dados de amostras da seguinte forma. 

1.  Mude para o superutilizador *oráculo* e, em seguida, inicialize o ouvinte para a exploração madeireira:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    O resultado é semelhante ao seguinte:

    ```output
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  Criar a base de dados:

    ```bash
    dbca -silent \
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

    Leva alguns minutos para criar a base de dados.

3. Definir variáveis oráculo

Antes de se ligar, precisa de definir duas variáveis ambientais: *ORACLE_HOME* e *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```

Também pode adicionar ORACLE_HOME e ORACLE_SID variáveis ao ficheiro .bashrc. Isto pouparia as variáveis ambientais para futuras inscrições. `~/.bashrc` Confirme que as seguintes declarações foram adicionadas ao ficheiro usando o editor da sua escolha.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Conectividade Oracle EM Express

Para uma ferramenta de gestão GUI que pode utilizar para explorar a base de dados, configurar o Oracle EM Express. Para ligar ao Oracle EM Express, tem primeiro de montar a porta em Oráculo. 

1. Ligue-se à sua base de dados utilizando sqlplus:

    ```bash
    sqlplus / as sysdba
    ```

2. Uma vez ligado, detete a porta 5502 para a EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Abra o recipiente PDB1 se ainda não estiver aberto, mas primeiro verifique o estado:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    O resultado é semelhante ao seguinte:

    ```output
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Se o `PDB1` OPEN_MODE não for READ WRITE, então execute os seguintes comandos para abrir PDB1:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

É necessário `quit` escrever para terminar a sessão `exit` de sqlplus e digitar para iniciar sessão do utilizador oráculo.

## <a name="automate-database-startup-and-shutdown"></a>Automatizar arranque e encerramento da base de dados

A base de dados Oracle por padrão não começa automaticamente quando reiniciar o VM. Para configurar a base de dados Oracle para iniciar automaticamente, inicie primeiro o primeiro sinal como raiz. Em seguida, crie e atualize alguns ficheiros do sistema.

1. Inscreva-se como raiz

    ```bash
    sudo su -
    ```

2.  Utilizando o seu editor `/etc/oratab` favorito, edite o ficheiro e altere o padrão `N` para: `Y`

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Criar um `/etc/init.d/dbora` ficheiro nomeado e colar os seguintes conteúdos:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Alterar permissões em ficheiros com *chmod* da seguinte forma:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Criar links simbólicos para arranque e encerramento da seguinte forma:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Para testar as suas alterações, reinicie o VM:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Portas abertas para conectividade

A tarefa final é configurar alguns pontos finais externos. Para criar o Grupo de Segurança da Rede Azure que protege o VM, primeiro saia da sua sessão SSH no VM (deveria ter sido expulso do SSH quando reiniciava o passo anterior). 

1.  Para abrir o ponto final que utiliza para aceder remotamente à base de dados da Oracle, crie uma regra do Grupo de Segurança da Rede com a regra nsg da [rede Az criar](/cli/azure/network/nsg/rule) da seguinte forma: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Para abrir o ponto final que utiliza para aceder remotamente ao Oracle EM Express, crie uma regra do Grupo de Segurança da Rede com a regra nsg da [rede Az criar](/cli/azure/network/nsg/rule) da seguinte forma:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Se necessário, obtenha novamente o endereço IP público do seu VM com o [programa público ip da rede Az](/cli/azure/network/public-ip) da seguinte forma:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Conecte o EM Express a partir do seu navegador. Certifique-se de que o seu navegador é compatível com o EM Express (é necessária a instalação flash): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

Pode fazer login utilizando a conta **SYS** e verificar a caixa de verificação **sysdba.** Utilize a palavra-passe **OraPasswd1** que definiu durante a instalação. 

![Screenshot da página de login oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Limpar recursos

Uma vez que tenha terminado de explorar a sua primeira base de dados Oracle no Azure e o VM já não é necessário, pode utilizar o comando de eliminação do [grupo AZ](/cli/azure/group) para remover o grupo de recursos, VM, e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Conheça [outras soluções Oracle no Azure.](oracle-considerations.md) 

Experimente o tutorial de Gestão automática de [Armazenamento da Oracle.](configure-oracle-asm.md)
