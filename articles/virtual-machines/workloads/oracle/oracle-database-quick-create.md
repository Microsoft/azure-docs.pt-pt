---
title: Criar uma base de dados Oracle numa VM do Azure | Documentos da Microsoft
description: Obtenha rapidamente uma base de dados do Oracle Database 12C cópia de segurança e em execução no seu ambiente do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 26c6abc75e653b489a7385c423a9b2d00a4ed063
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705315"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Criar uma base de dados Oracle numa VM do Azure

Este guia detalha a utilização da CLI do Azure para implementar uma máquina virtual do Azure a partir da [imagem de galeria do marketplace Oracle](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) para criar uma base de dados Oracle 12C. Quando o servidor estiver implementado, irá ligar-se através de SSH para configurar a base de dados Oracle. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
## <a name="create-virtual-machine"></a>Criar a máquina virtual

Para criar uma máquina virtual (VM), utilize o [az vm criar](/cli/azure/vm) comando. 

O exemplo seguinte cria uma VM com o nome `myVM`. Ele também cria chaves SSH, caso não existam numa localização chave predefinida. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

Depois de criar a VM, a CLI do Azure mostra informações semelhantes ao seguinte exemplo. Tenha em atenção o valor para `publicIpAddress`. Utilize este endereço para aceder à VM.

```azurecli
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

Para criar uma sessão SSH com a VM, utilize o seguinte comando. Substitua o endereço IP com o `publicIpAddress` valor para a sua VM.

```bash 
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>Criar a base de dados

O software Oracle já está instalado na imagem do Marketplace. Crie uma base de dados de exemplo da seguinte forma. 

1.  Mude para o *oracle* Superutilizador, em seguida, initialize, o serviço de escuta para o registo:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    O resultado é semelhante ao seguinte:

    ```bash
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

2.  Crie a base de dados:

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

    Demora alguns minutos para criar a base de dados.

3. Definir as variáveis do Oracle

Antes de ligar, precisa definir duas variáveis de ambiente: *ORACLE_HOME* e *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```
Também pode adicionar variáveis ORACLE_HOME e ORACLE_SID para o ficheiro de .bashrc. Isso salvará as variáveis de ambiente para inícios de sessão futuras. Confirme que foram adicionadas as seguintes instruções para o `~/.bashrc` ficheiro com o editor à sua escolha.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Conectividade do Oracle EM Express

Para uma ferramenta de gestão GUI que pode utilizar para explorar a base de dados, configure o Express do Oracle EM. Para ligar a Express de EM Oracle, tem de configurar primeiro a porta no Oracle. 

1. Ligar à base de dados usando sqlplus:

    ```bash
    sqlplus / as sysdba
    ```

2. Assim que estiver ligado, defina a porta 5502 Pro EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Abra o contentor PDB1 se não o tiver feito aberta, mas, primeiro verifique o estado:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    O resultado é semelhante ao seguinte:

    ```bash
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Se o OPEN_MODE para `PDB1` não é ler escrever, em seguida, execute os comandos da seguinte para abrir PDB1:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

Precisa digitar `quit` para terminar a sessão de sqlplus e tipo `exit` a fim de sessão do utilizador oracle.

## <a name="automate-database-startup-and-shutdown"></a>Automatizar a base de dados inicialização e desligamento

A base de dados Oracle por predefinição não iniciar automaticamente quando reiniciar a VM. Para configurar a base de dados Oracle para iniciar automaticamente, primeiro inicie sessão como raiz. Em seguida, criar e atualizar alguns ficheiros de sistema.

1. Iniciar sessão como raiz
    ```bash
    sudo su -
    ```

2.  Com o seu editor favorito, edite o ficheiro `/etc/oratab` e altere a predefinição `N` para `Y`:

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Crie um ficheiro denominado `/etc/init.d/dbora` e cole o seguinte conteúdo:

    ```
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

5.  Crie links simbólicos para inicialização e desligamento da seguinte forma:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Para testar as suas alterações, reinicie a VM:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Abrir portas para conectividade

A tarefa final é configurar alguns pontos finais externos. Para configurar o grupo de segurança de rede do Azure que protege a VM, saia do primeiro a sessão SSH na VM (deve ter sido iniciada fora de SSH ao reiniciar no passo anterior). 

1.  Para abrir o ponto final que utilizar para aceder remotamente a base de dados do Oracle, crie uma regra de grupo de segurança de rede com [criar regra de nsg de rede de az](/cli/azure/network/nsg/rule) da seguinte forma: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Para abrir o ponto final que usar para acessar o Express do Oracle EM remotamente, crie uma regra de grupo de segurança de rede com [criar regra de nsg de rede de az](/cli/azure/network/nsg/rule) da seguinte forma:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Se for necessário, obtenha o endereço IP público da sua VM com [show de public-ip de rede de az](/cli/azure/network/public-ip) da seguinte forma:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Ligue-se EM Express partir do seu browser. Certifique-se de que o browser é compatível com o Express EM (é necessária instalação Flash): 

    ```
    https://<VM ip address or hostname>:5502/em
    ```

Pode iniciar sessão utilizando o **SYS** da conta e verifique o **como sysdba** caixa de verificação. Utilize a palavra-passe **OraPasswd1** que definir durante a instalação. 

![Captura de ecrã da página de início de sessão Express do Oracle OEM](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Limpar recursos

Depois de terminar de explorar a sua primeira base de dados do Oracle no Azure e a VM já não é necessário, pode utilizar o [eliminação do grupo de az](/cli/azure/group) de comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre outras [soluções da Oracle no Azure](oracle-considerations.md). 

Experimente o [instalar e configurar o Oracle automatizada gestão de armazenamento](configure-oracle-asm.md) tutorial.
