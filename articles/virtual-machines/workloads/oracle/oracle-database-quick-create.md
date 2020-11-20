---
title: Criar uma base de dados Oráculo num VM Azure Microsoft Docs
description: Obtenha rapidamente uma base de dados Oracle Database 12c em funcionamento no seu ambiente Azure.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: 6468acb598cee26c46b62d64c748f0e393f27271
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967963"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Criar uma Base de Dados Oráculo num VM Azure

Este guia detalha a utilização do CLI Azure para implantar uma máquina virtual Azure a partir da imagem da galeria do [mercado Oracle,](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) a fim de criar uma base de dados Oracle 12c. Assim que o servidor for implantado, irá ligar-se via SSH para configurar a base de dados Oráculo. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Para criar uma máquina virtual (VM), utilize o comando [az vm create.](/cli/azure/vm) 

O exemplo seguinte cria uma VM com o nome `myVM`. Também cria chaves SSH, se já não existirem num local de chave predefinido. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

Depois de criar o VM, o Azure CLI exibe informações semelhantes ao exemplo seguinte. Note o valor para `publicIpAddress` . Utilize este endereço para aceder ao VM.

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

Para criar uma sessão de SSH com o VM, utilize o seguinte comando. Substitua o endereço IP `publicIpAddress` pelo valor do seu VM.

```bash
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>Criar a base de dados

O software Oracle já está instalado na imagem marketplace. Crie uma base de dados de amostras da seguinte forma. 

1.  Mude para o utilizador *oráculo* e, em seguida, inicie o ouvinte Oráculo:

    ```bash
    $ sudo -su oracle
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
2. Criar um diretório de dados para os ficheiros de dados da Oracle

    ```bash
        mkdir /u01/app/oracle/oradata
    ```

3.  Criar a base de dados:

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
           -datafileDestination "/u01/app/oracle/oradata/" \
           -ignorePreReqs
    ```

    Leva alguns minutos para criar a base de dados.

    Verá uma saída semelhante à seguinte:

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

4. Definir variáveis Oráculo

    Antes de se ligar, é necessário definir duas variáveis ambientais: *ORACLE_HOME* e *ORACLE_SID*.

    ```bash
        ORACLE_SID=cdb1; export ORACLE_SID
    ```

    Também pode adicionar variáveis ORACLE_HOME e ORACLE_SID ao ficheiro .bashrc. Isto pouparia as variáveis ambientais para futuras inscrições. Confirme as seguintes declarações adicionadas ao ficheiro utilizando o `~/.bashrc` editor à sua escolha.

    ```bash
    # Add ORACLE_SID. 
    export ORACLE_SID=cdb1 
    ```

## <a name="oracle-em-express-connectivity"></a>Conectividade Oracle EM Express

Para uma ferramenta de gestão GUI que pode utilizar para explorar a base de dados, crie a Oracle EM Express. Para ligar à Oracle EM Express, tem primeiro de instalar a porta em Oráculo. 

1. Ligue-se à sua base de dados utilizando sqlplus:

    ```bash
    sqlplus / as sysdba
    ```

2. Uma vez ligado, deite a porta 5502 para a EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Abra o recipiente PDB1 se ainda não estiver aberto, mas verifique primeiro o estado:

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

4. Se o OPEN_MODE `PDB1` para não for READ WRITE, então executar os seguintes comandos para abrir PDB1:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

É necessário escrever `quit` para terminar a sessão sqlplus e escrever `exit` para o logout do utilizador oráculo.

## <a name="automate-database-startup-and-shutdown"></a>Automatizar arranque e encerramento de bases de dados

A base de dados Oracle por padrão não começa automaticamente quando reinicia o VM. Para configurar a base de dados Oráculo para iniciar automaticamente, inicie primeiro o teste como raiz. Em seguida, crie e atualize alguns ficheiros do sistema.

1. Inscreva-se como raiz

    ```bash
    sudo su -
    ```

2.  Utilizando o seu editor favorito, edite o ficheiro `/etc/oratab` e altere o padrão `N` `Y` para:

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Crie um ficheiro com o nome `/etc/init.d/dbora` e cole os seguintes conteúdos:

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

4.  Altere as permissões em ficheiros com *o chmod* da seguinte forma:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Criar ligações simbólicas para arranque e encerramento da seguinte forma:

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

A tarefa final é configurar alguns pontos finais externos. Para configurar o Grupo de Segurança da Rede Azure que protege o VM, saia primeiro da sessão SSH no VM (deveria ter sido expulso do SSH ao reiniciar no passo anterior). 

1.  Para abrir o ponto final que utiliza para aceder remotamente à base de dados da Oracle, crie uma regra do Grupo de Segurança da Rede com [a regra az network nsg create](/cli/azure/network/nsg/rule) da seguinte forma: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Para abrir o ponto final que utiliza para aceder remotamente ao Oracle EM Express, crie uma regra do Grupo de Segurança de Rede com [a regra az network nsg create](/cli/azure/network/nsg/rule) da seguinte forma:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Se necessário, obtenha novamente o endereço IP público do seu VM com [o programa público-ip da rede AZ](/cli/azure/network/public-ip) da seguinte forma:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Ligue o EM Express a partir do seu navegador. Certifique-se de que o seu navegador é compatível com o EM Express (é necessária a instalação Flash): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

Pode iniciar sessão utilizando a conta **SYS** e verificar como caixa de verificação **sysdba.** Utilize a palavra-passe **OraPasswd1** que definiu durante a instalação. 

![Screenshot da página de login do Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Uma vez terminada a exploração da sua primeira base de dados Oracle em Azure e o VM já não é necessário, pode utilizar o comando de eliminação do [grupo AZ](/cli/azure/group) para remover o grupo de recursos, VM e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [outras soluções Oráculos no Azure.](./oracle-overview.md) 

Experimente o tutorial [de Gestão automatizada de Armazenamento Automatizado de Instalação e Configuração do Oráculo.](configure-oracle-asm.md)
