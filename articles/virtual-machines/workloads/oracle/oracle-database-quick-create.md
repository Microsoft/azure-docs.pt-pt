---
title: Crie uma base de dados Oráculo num | VM Azure Microsoft Docs
description: Obtenha rapidamente uma base de dados Oracle Database 12c em funcionamento no seu ambiente Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: kegorman
ms.openlocfilehash: 8964248bb23b2b615c7e73e26d730fbd79b4e9e7
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102184462"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Criar uma Base de Dados Oráculo num VM Azure

Este guia detalha a utilização do CLI Azure para implantar uma máquina virtual Azure a partir da imagem da galeria do [mercado Oracle,](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) a fim de criar uma base de dados Oracle 19c. Assim que o servidor for implantado, irá ligar-se via SSH para configurar a base de dados Oráculo. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo a seguir cria um grupo de recursos chamado *rg-oráculo* na localização *leste.*

```azurecli-interactive
az group create --name rg-oracle --location eastus
```

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Para criar uma máquina virtual (VM), utilize o comando [az vm create.](/cli/azure/vm) 

O exemplo seguinte cria uma VM com o nome `vmoracle19c`. Também cria chaves SSH, se já não existirem num local de chave predefinido. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  

```azurecli-interactive 
az vm create ^
    --resource-group rg-oracle ^
    --name vmoracle19c ^
    --image Oracle:oracle-database-19-3:oracle-database-19-0904:latest ^
    --size Standard_DS2_v2 ^
    --admin-username azureuser ^
    --generate-ssh-keys ^
    --public-ip-address-allocation static ^
    --public-ip-address-dns-name vmoracle19c

```

Depois de criar o VM, o Azure CLI exibe informações semelhantes ao exemplo seguinte. Note o valor para `publicIpAddress` . Utilize este endereço para aceder ao VM.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/rg-oracle/providers/Microsoft.Compute/virtualMachines/vmoracle19c",
  "location": "eastus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "rg-oracle"
}
```
## <a name="create-and-attach-a-new-disk-for-oracle-datafiles-and-fra"></a>Criar e anexar um novo disco para ficheiros oracle e FRA

```bash
az vm disk attach --name oradata01 --new --resource-group rg-oracle --size-gb 128 --sku StandardSSD_LRS --vm-name vmoracle19c
```

## <a name="open-ports-for-connectivity"></a>Portas abertas para conectividade
Nesta tarefa, deve configurar alguns pontos finais externos para o ouvinte da base de dados e o EXPRESSO utilizarem através da criação do Grupo de Segurança da Rede Azure que protege o VM. 

1. Para abrir o ponto final que utiliza para aceder remotamente à base de dados da Oracle, crie uma regra do Grupo de Segurança da Rede da seguinte forma:
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle ^
       --protocol tcp ^
       --priority 1001 ^
       --destination-port-range 1521
   ```
2. Para abrir o ponto final que utiliza para aceder remotamente ao Oracle EM Express, crie uma regra do Grupo de Segurança de Rede com a regra az network nsg create da seguinte forma:
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle-EM ^
       --protocol tcp ^
       --priority 1002 ^
       --destination-port-range 5502
   ```
3. Se necessário, obtenha novamente o endereço IP público do seu VM com o programa público-ip da rede AZ da seguinte forma:

   ```bash
   az network public-ip show ^
       --resource-group rg-oracle ^
       --name vmoracle19cPublicIP ^
       --query [ipAddress] ^
       --output tsv
   ```

## <a name="prepare-the-vm-environment"></a>Preparar o ambiente VM

1. Ligar à VM

   Para criar uma sessão de SSH com o VM, utilize o seguinte comando. Substitua o endereço IP `publicIpAddress` pelo valor do seu VM.

   ```bash
   ssh azureuser@<publicIpAddress>
   ```

2. Mude para o utilizador raiz

   ```bash
   sudo su -
   ```

3. Verifique se o último dispositivo de disco criado que iremos utilizar para a manutenção dos ficheiros da Oracle

   ```bash
   ls -alt /dev/sd*|head -1
   ```

   A saída será semelhante a esta:
   ```output
   brw-rw----. 1 root disk 8, 16 Dec  8 22:57 /dev/sdc
   ```

4. Formate o dispositivo. 
   Como o utilizador raiz executado se separou no dispositivo 
   
   Primeiro criar uma etiqueta de disco:
   ```bash
   parted /dev/sdc mklabel gpt
   ```
   Em seguida, crie uma divisória primária abrangendo todo o disco:
   ```bash
   parted -a optimal /dev/sdc mkpart primary 0GB 64GB   
   ```
   Por fim, verifique os detalhes do dispositivo imprimindo os seus metadados:
   ```bash
   parted /dev/sdc print
   ```
   O resultado deverá ter um aspeto semelhante a este:
   ```bash
   # parted /dev/sdc print
   Model: Msft Virtual Disk (scsi)
   Disk /dev/sdc: 68.7GB
   Sector size (logical/physical): 512B/4096B
   Partition Table: gpt
   Disk Flags:
   Number  Start   End     Size    File system  Name     Flags
    1      1049kB  64.0GB  64.0GB  ext4         primary
   ```

5. Criar um sistema de ficheiros na partição do dispositivo

   ```bash
   mkfs -t ext4 /dev/sdc1
   ```

6. Criar um ponto de montagem
   ```bash
   mkdir /u02
   ```

7. Monte o disco

   ```bash
   mount /dev/sdc1 /u02
   ```

8. Alterar permissões no ponto de montagem

   ```bash
   chmod 777 /u02
   ```

9. Adicione o suporte ao ficheiro /etc/fstab. 

   ```bash
   echo "/dev/sdc1               /u02                    ext4    defaults        0 0" >> /etc/fstab
   ```
   
10. Atualize o ficheiro ***/etc/hosts*** com o IP público e o nome de anfitrião.

    Altere o ***IP público e o VMname*** para refletir os seus valores reais:
  
    ```bash
    echo "<Public IP> <VMname>.eastus.cloudapp.azure.com <VMname>" >> /etc/hosts
    ```
11. Atualizar o ficheiro nome de anfitrião
    
    Utilize o seguinte comando para adicionar o nome de domínio do VM ao ficheiro **/etc/nome de anfitrião.** Isto pressupõe que criou o seu grupo de recursos e VM na região **leste:**
    
    ```bash
    sed -i 's/$/\.eastus\.cloudapp\.azure\.com &/' /etc/hostname
    ```

12. Portas de firewall abertas
    
    Como o SELinux é ativado por padrão na imagem do Marketplace, precisamos de abrir a firewall para o tráfego para a porta de escuta da base de dados 1521, e a porta Enterprise Manager Express 5502. Executar os seguintes comandos como utilizador de raiz:

    ```bash
    firewall-cmd --zone=public --add-port=1521/tcp --permanent
    firewall-cmd --zone=public --add-port=5502/tcp --permanent
    firewall-cmd --reload
    ```
   

## <a name="create-the-database"></a>Criar a base de dados

O software Oracle já está instalado na imagem marketplace. Crie uma base de dados de amostras da seguinte forma. 

1.  Mude para o utilizador **oráculo:**

    ```bash
    sudo su - oracle
    ```
2. Inicie o ouvinte da base de dados

   ```bash
   lsnrctl start
   ```
   O resultado é semelhante ao seguinte:
  
   ```output
   LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 20-OCT-2020 01:58:18

   Copyright (c) 1991, 2019, Oracle.  All rights reserved.

   Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))

   Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
   STATUS of the LISTENER
   ------------------------
   Alias                     LISTENER
   Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Start Date                20-OCT-2020 01:58:18
   Uptime                    0 days 0 hr. 0 min. 0 sec
   Trace Level               off
   Security                  ON: Local OS Authentication
   SNMP                      OFF
   Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
   The listener supports no services
   The command completed successfully
   ```
3. Criar um diretório de dados para os ficheiros de dados da Oracle:

   ```bash
   mkdir /u02/oradata
   ```
    

3.  Executar o Assistente de Criação de Bases de Dados:

    ```bash
    dbca -silent \
       -createDatabase \
       -templateName General_Purpose.dbc \
       -gdbname test \
       -sid test \
       -responseFile NO_VALUE \
       -characterSet AL32UTF8 \
       -sysPassword OraPasswd1 \
       -systemPassword OraPasswd1 \
       -createAsContainerDatabase false \
       -databaseType MULTIPURPOSE \
       -automaticMemoryManagement false \
       -storageType FS \
       -datafileDestination "/u02/oradata/" \
       -ignorePreReqs
    ```

    Leva alguns minutos para criar a base de dados.

    Verá uma saída semelhante à seguinte:

    ```output
        Prepare for db operation
       10% complete
       Copying database files
       40% complete
       Creating and starting Oracle instance
       42% complete
       46% complete
       50% complete
       54% complete
       60% complete
       Completing Database Creation
       66% complete
       69% complete
       70% complete
       Executing Post Configuration Actions
       100% complete
       Database creation complete. For details check the logfiles at: /u01/app/oracle/cfgtoollogs/dbca/test.
       Database Information:
       Global Database Name:test
       System Identifier(SID):test
       Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/test/test.log" for further details.
    ```

4. Definir variáveis Oráculo

    Antes de se ligar, tem de definir a variável ambiental *ORACLE_SID:*

    ```bash
        export ORACLE_SID=test
    ```

    Deve também adicionar a variável ORACLE_SID ao ficheiro dos `oracle` utilizadores `.bashrc` para futuras insuposições utilizando o seguinte comando:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```

## <a name="oracle-em-express-connectivity"></a>Conectividade Oracle EM Express

Para uma ferramenta de gestão GUI que pode utilizar para explorar a base de dados, crie a Oracle EM Express. Para ligar à Oracle EM Express, tem primeiro de instalar a porta em Oráculo. 

1. Ligue-se à sua base de dados utilizando sqlplus:

    ```bash
    sqlplus sys as sysdba
    ```

2. Uma vez ligado, deite a porta 5502 para a EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3.  Ligue o EM Express a partir do seu navegador. Certifique-se de que o seu navegador é compatível com o EM Express (é necessária a instalação Flash): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

    Pode iniciar sessão utilizando a conta **SYS** e verificar como caixa de verificação **sysdba.** Utilize a palavra-passe **OraPasswd1** que definiu durante a instalação. 

    ![Screenshot da página de login do Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="automate-database-startup-and-shutdown"></a>Automatizar arranque e encerramento de bases de dados

A base de dados Oracle por padrão não começa automaticamente quando reinicia o VM. Para configurar a base de dados Oráculo para iniciar automaticamente, inicie primeiro o teste como raiz. Em seguida, crie e atualize alguns ficheiros do sistema.

1. Inscreva-se como raiz

    ```bash
    sudo su -
    ```

2.  Executar o seguinte comando para alterar a bandeira de arranque automatizada de `N` para `Y` dentro do `/etc/oratab` ficheiro:

    ```bash
    sed -i 's/:N/:Y/' /etc/oratab
    ```

3.  Crie um ficheiro com o nome `/etc/init.d/dbora` e cole os seguintes conteúdos:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/19.0.0/dbhome_1
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

## <a name="clean-up-resources"></a>Limpar os recursos

Uma vez terminada a exploração da sua primeira base de dados Oracle em Azure e o VM já não é necessário, pode utilizar o comando de eliminação do [grupo AZ](/cli/azure/group) para remover o grupo de recursos, VM e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Compreenda como proteger a sua base de dados em Azure com [estratégias de backup da Oracle](./oracle-database-backup-strategies.md)

Saiba mais sobre [outras soluções Oráculos no Azure.](./oracle-overview.md) 

Experimente o tutorial [de Gestão automatizada de Armazenamento Automatizado de Instalação e Configuração do Oráculo.](configure-oracle-asm.md)
