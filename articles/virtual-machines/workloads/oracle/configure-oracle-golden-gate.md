---
title: Implementar o Oracle Golden Gate em uma VM Linux do Azure | Microsoft Docs
description: Coloque rapidamente um portão Oracle Golden em funcionamento em seu ambiente do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 40eaabb149e2e897ecd4e1109e0db7c42b990925
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101550"
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>Implementar o Oracle Golden Gate em uma VM Linux do Azure 

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia fornece detalhes sobre como usar o CLI do Azure para implantar um banco de dados Oracle 12c da imagem da galeria do Azure Marketplace. 

Este documento mostra passo a passo como criar, instalar e configurar o Oracle Golden Gate em uma VM do Azure.

Antes de começar, certifique-se de que a CLI do Azure foi instalada. Para obter mais informações, veja [Guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Preparar o ambiente

Para executar a instalação do Oracle Golden Gate, você precisa criar duas VMs do Azure no mesmo conjunto de disponibilidade. A imagem do Marketplace usada para criar as VMs é **Oracle: Oracle-Database-EE: 12.1.0.2: Latest**.

Você também precisa estar familiarizado com o editor do UNIX vi e ter uma compreensão básica do X11 (X Windows).

Veja a seguir um resumo da configuração do ambiente:
> 
> |  | **Site primário** | **Replicar site** |
> | --- | --- | --- |
> | **Versão do Oracle** |Oracle 12c versão 2 – (12.1.0.2) |Oracle 12c versão 2 – (12.1.0.2)|
> | **Nome do computador** |myVM1 |myVM2 |
> | **Sistema operativo** |Oracle Linux 6. x |Oracle Linux 6. x |
> | **SID do Oracle** |CDB1 |CDB1 |
> | **Esquema de replicação** |TESTAR|TESTAR |
> | **Proprietário/replicação do portão de ouro** |C##GGADMIN |REPUSER |
> | **Processo de portão dourado** |EXTORA |REPORA|


### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 

Entre em sua assinatura do Azure com o comando [AZ login](/cli/azure/reference-index) . Em seguida, siga as instruções na tela.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e dos quais eles podem ser gerenciados. 

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westus`.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

A etapa a seguir é opcional, mas recomendada. Para obter mais informações, consulte [Guia de conjuntos de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com o comando [az vm create](/cli/azure/vm). 

O exemplo a seguir cria duas VMs `myVM1` chamadas `myVM2`e. Crie chaves SSH se elas ainda não existirem em um local de chave padrão. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.

#### <a name="create-myvm1-primary"></a>Criar myVM1 (primário):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Depois que a VM tiver sido criada, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir. (Anote o `publicIpAddress`. Esse endereço é usado para acessar a VM.)

```azurecli
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

#### <a name="create-myvm2-replicate"></a>Criar myVM2 (replicar):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Anote o `publicIpAddress` também depois que ele tiver sido criado.

### <a name="open-the-tcp-port-for-connectivity"></a>Abrir a porta TCP para conectividade

A próxima etapa é configurar pontos de extremidade externos, que permitem que você acesse o banco de dados Oracle remotamente. Para configurar os pontos de extremidade externos, execute os comandos a seguir.

#### <a name="open-the-port-for-myvm1"></a>Abra a porta para myVM1:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Os resultados devem ser semelhantes à seguinte resposta:

```bash
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

#### <a name="open-the-port-for-myvm2"></a>Abra a porta para myVM2:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual

Utilize o seguinte comando para criar uma sessão SSH com a máquina virtual. Substitua o endereço IP pelo `publicIpAddress` da máquina virtual.

```bash 
ssh <publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Criar o banco de dados em myVM1 (primário)

O software Oracle já está instalado na imagem do Marketplace, portanto, a próxima etapa é instalar o banco de dados. 

Execute o software como o superusuário ' Oracle ':

```bash
sudo su - oracle
```

Crie o banco de dados:

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

```bash
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
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

Defina as variáveis ORACLE_SID e ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Opcionalmente, você pode adicionar ORACLE_HOME e ORACLE_SID ao arquivo. bashrc, para que essas configurações sejam salvas para futuras entradas:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Iniciar o Oracle Listener
```bash
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>Criar o banco de dados no myVM2 (replicar)

```bash
sudo su - oracle
```
Crie o banco de dados:

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
Defina as variáveis ORACLE_SID e ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Opcionalmente, você pode adicionar ORACLE_HOME e ORACLE_SID ao arquivo. bashrc, para que essas configurações sejam salvas para logons futuros.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Iniciar o Oracle Listener
```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>Configurar portão dourado 
Para configurar o portão dourado, siga as etapas nesta seção.

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>Habilitar o modo de log de arquivo em myVM1 (primário)

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
Habilite o registro em log forçado e verifique se pelo menos um arquivo de log está presente.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>Baixar o software da porta de ouro
Para baixar e preparar o software Oracle Golden Gate, conclua as seguintes etapas:

1. Baixe o arquivo **fbo_ggs_Linux_x64_shiphome. zip** da [página de download do Oracle Golden Gate](https://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html). No título de download **Oracle GoldenGate 12. x. x. x para Oracle Linux x86-64**, deve haver um conjunto de arquivos. zip para download.

2. Depois de baixar os arquivos. zip para o computador cliente, use o protocolo SCP para copiar os arquivos para a VM:

   ```bash
   $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
   ```

3. Mova os arquivos. zip para a pasta **/opt** . Em seguida, altere o proprietário dos arquivos da seguinte maneira:

   ```bash
   $ sudo su -
   # mv <folder>/*.zip /opt
   ```

4. Descompacte os arquivos (instale o utilitário de descompactação do Linux se ele ainda não estiver instalado):

   ```bash
   # yum install unzip
   # cd /opt
   # unzip fbo_ggs_Linux_x64_shiphome.zip
   ```

5. Alterar permissão:

   ```bash
   # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
   ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Preparar o cliente e a VM para executar o X11 (somente para clientes Windows)
Este é um passo opcional. Você pode ignorar esta etapa se estiver usando um cliente Linux ou já tiver a instalação do X11.

1. Baixe a saída e o Xming para o computador com Windows:

   * [Download de saída](https://www.putty.org/)
   * [Baixar o Xming](https://xming.en.softonic.com/)

2. Depois de instalar a geração, na pasta de saída (por exemplo, C:\Program Files\PuTTY), execute puttygen. exe (gerador de chave de geração).

3. No gerador de chave de geração:

   - Para gerar uma chave, selecione o botão **gerar** .
   - Copie o conteúdo da chave (**Ctrl + C**).
   - Selecione o botão **salvar chave privada** .
   - Ignore o aviso que aparece e, em seguida, selecione **OK**.

   ![Captura de tela da página do gerador de chave de geração](./media/oracle-golden-gate/puttykeygen.png)

4. Em sua VM, execute estes comandos:

   ```bash
   # sudo su - oracle
   $ mkdir .ssh (if not already created)
   $ cd .ssh
   ```

5. Crie um arquivo chamado **authorized_keys**. Cole o conteúdo da chave nesse arquivo e, em seguida, salve o arquivo.

   > [!NOTE]
   > A chave deve conter a cadeia `ssh-rsa`de caracteres. Além disso, o conteúdo da chave deve ser uma única linha de texto.
   >  

6. Inicie o PuTTY. No painel **categoria** , selecione **conexão** > **SSH** > **autenticação**. Na caixa **arquivo de chave privada para autenticação** , navegue até a chave que você gerou anteriormente.

   ![Captura de tela da página definir chave privada](./media/oracle-golden-gate/setprivatekey.png)

7. No painel **categoria** , selecione **conexão** > **SSH** > **X11**. Em seguida, selecione a caixa **habilitar encaminhamento X11** .

   ![Captura de tela da página habilitar X11](./media/oracle-golden-gate/enablex11.png)

8. No painel **categoria** , vá para **sessão**. Insira as informações do host e, em seguida, selecione **abrir**.

   ![Captura de tela da página da sessão](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>Instalar o software da porta Golden

Para instalar o Oracle Golden Gate, conclua as seguintes etapas:

1. Entre como Oracle. (Você deve ser capaz de entrar sem ser solicitado a fornecer uma senha.) Verifique se o Xming está em execução antes de começar a instalação.
 
   ```bash
   $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
   $ ./runInstaller
   ```
2. Selecione ' Oracle GoldenGate para Oracle Database 12c '. Em seguida, selecione **Avançar** para continuar.

   ![Captura de tela da página Selecionar instalação do instalador](./media/oracle-golden-gate/golden_gate_install_01.png)

3. Altere o local do software. Em seguida, selecione a caixa **Gerenciador de início** e insira o local do banco de dados. Selecione **Seguinte** para continuar.

   ![Captura de tela da página Selecionar instalação](./media/oracle-golden-gate/golden_gate_install_02.png)

4. Altere o diretório de inventário e, em seguida, selecione **Avançar** para continuar.

   ![Captura de tela da página Selecionar instalação](./media/oracle-golden-gate/golden_gate_install_03.png)

5. Na tela **Resumo** , selecione **instalar** para continuar.

   ![Captura de tela da página Selecionar instalação do instalador](./media/oracle-golden-gate/golden_gate_install_04.png)

6. Você pode ser solicitado a executar um script como "raiz". Nesse caso, abra uma sessão separada, SSH para a VM, sudo para raiz e, em seguida, execute o script. Selecione **OK** continuar.

   ![Captura de tela da página Selecionar instalação](./media/oracle-golden-gate/golden_gate_install_05.png)

7. Quando a instalação for concluída, selecione **fechar** para concluir o processo.

   ![Captura de tela da página Selecionar instalação](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>Configurar o serviço em myVM1 (primário)

1. Crie ou atualize o arquivo arquivo tnsnames. Ora:

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Crie o proprietário da porta de ouro e as contas de usuário.

   > [!NOTE]
   > A conta do proprietário deve C#ter # Prefix.
   >

    ```bash
    $ sqlplus / as sysdba
    SQL> CREATE USER C##GGADMIN identified by ggadmin;
    SQL> EXEC dbms_goldengate_auth.grant_admin_privilege('C##GGADMIN',container=>'ALL');
    SQL> GRANT DBA to C##GGADMIN container=all;
    SQL> connect C##GGADMIN/ggadmin
    SQL> ALTER SESSION SET CONTAINER=PDB1;
    SQL> EXIT;
    ```

3. Crie a conta de usuário de teste do portão dourado:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> @demo_ora_insert
   SQL> EXIT;
   ```

4. Configure o arquivo de parâmetro de extração.

   Inicie a interface de linha de comando do portão de ouro (ggsci):

   ```bash
   $ sudo su - oracle
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> DBLOGIN USERID test@pdb1, PASSWORD test
   Successfully logged into database  pdb1
   GGSCI>  ADD SCHEMATRANDATA pdb1.test
   2017-05-23 15:44:25  INFO    OGG-01788  SCHEMATRANDATA has been added on schema test.
   2017-05-23 15:44:25  INFO    OGG-01976  SCHEMATRANDATA for scheduling columns has been added on schema test.

   GGSCI> EDIT PARAMS EXTORA
   ```
5. Adicione o seguinte ao arquivo de parâmetro EXTRACT (usando comandos vi). Pressione a tecla ESC, ': Wq! ' para salvar o arquivo. 

   ```bash
   EXTRACT EXTORA
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTRAIL ./dirdat/rt  
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT 
   LOGALLSUPCOLS
   UPDATERECORDFORMAT COMPACT
   TABLE pdb1.test.TCUSTMER;
   TABLE pdb1.test.TCUSTORD;
   ```
6. Registrar extração--extração integrada:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci

   GGSCI> dblogin userid C##GGADMIN, password ggadmin
   Successfully logged into database CDB$ROOT.

   GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

   2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

   GGSCI> exit
   ```
7. Configure pontos de verificação de extração e inicie a extração em tempo real:

   ```bash
   $ ./ggsci
   GGSCI>  ADD EXTRACT EXTORA, INTEGRATED TRANLOG, BEGIN NOW
   EXTRACT (Integrated) added.

   GGSCI>  ADD RMTTRAIL ./dirdat/rt, EXTRACT EXTORA, MEGABYTES 10
   RMTTRAIL added.

   GGSCI>  START EXTRACT EXTORA

   Sending START request to MANAGER ...
   EXTRACT EXTORA starting

   GGSCI > info all

   Program     Status      Group       Lag at Chkpt  Time Since Chkpt

   MANAGER     RUNNING
   EXTRACT     RUNNING     EXTORA      00:00:11      00:00:04
   ```
   Nesta etapa, você encontrará o SCN inicial, que será usado posteriormente, em uma seção diferente:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> SELECT current_scn from v$database;
   CURRENT_SCN
   -----------
      1857887
   SQL> EXIT;
   ```

   ```bash
   $ ./ggsci
   GGSCI> EDIT PARAMS INITEXT
   ```

   ```bash
   EXTRACT INITEXT
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTASK REPLICAT, GROUP INITREP
   TABLE pdb1.test.*, SQLPREDICATE 'AS OF SCN 1857887'; 
   ```

   ```bash
   GGSCI> ADD EXTRACT INITEXT, SOURCEISTABLE
   ```

### <a name="set-up-service-on-myvm2-replicate"></a>Configurar o serviço no myVM2 (replicar)


1. Crie ou atualize o arquivo arquivo tnsnames. Ora:

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Criar uma conta de replicação:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> create user repuser identified by rep_pass container=current;
   SQL> grant dba to repuser;
   SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
   SQL> connect repuser/rep_pass@pdb1 
   SQL> EXIT;
   ```

3. Criar uma conta de usuário de teste do portão dourado:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> EXIT;
   ```

4. REPLICAr arquivo de parâmetro para replicar as alterações: 

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> EDIT PARAMS REPORA  
   ```
   Conteúdo do arquivo de parâmetro REPORA:

   ```bash
   REPLICAT REPORA
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/repora.dsc, PURGE, MEGABYTES 100
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT
   DBOPTIONS INTEGRATEDPARAMS(parallelism 6)
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;
   ```

5. Configurar um ponto de verificação replicar:

   ```bash
   GGSCI> ADD REPLICAT REPORA, INTEGRATED, EXTTRAIL ./dirdat/rt
   GGSCI> EDIT PARAMS INITREP

   ```

   ```bash
   REPLICAT INITREP
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/tcustmer.dsc, APPEND
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;   
   ```

   ```bash
   GGSCI> ADD REPLICAT INITREP, SPECIALRUN
   ```

### <a name="set-up-the-replication-myvm1-and-myvm2"></a>Configurar a replicação (myVM1 e myVM2)

#### <a name="1-set-up-the-replication-on-myvm2-replicate"></a>1. Configurar a replicação no myVM2 (replicar)

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS MGR
  ```
Atualize o arquivo com o seguinte:

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```
Em seguida, reinicie o serviço do Gerenciador:

  ```bash
  GGSCI> STOP MGR
  GGSCI> START MGR
  GGSCI> EXIT
  ```

#### <a name="2-set-up-the-replication-on-myvm1-primary"></a>2. Configurar a replicação no myVM1 (primário)

Inicie a carga inicial e verifique se há erros:

```bash
$ cd /u01/app/oracle/product/12.1.0/oggcore_1
$ ./ggsci
GGSCI> START EXTRACT INITEXT
GGSCI> VIEW REPORT INITEXT
```
#### <a name="3-set-up-the-replication-on-myvm2-replicate"></a>3. Configurar a replicação no myVM2 (replicar)

Altere o número de SCN com o número obtido antes:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```
A replicação foi iniciada e você pode testá-la inserindo novos registros para testar tabelas.


### <a name="view-job-status-and-troubleshooting"></a>Exibir o status e a solução de problemas do trabalho

#### <a name="view-reports"></a>Exibir relatórios
Para exibir relatórios no myVM1, execute os seguintes comandos:

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
Para exibir relatórios no myVM2, execute os seguintes comandos:

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>Exibir status e histórico
Para exibir o status e o histórico em myVM1, execute os seguintes comandos:

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

Para exibir o status e o histórico em myVM2, execute os seguintes comandos:

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
Isso conclui a instalação e a configuração do portão dourado no Oracle Linux.


## <a name="delete-the-virtual-machine"></a>Elimine a máquina virtual

Quando não for mais necessário, o comando a seguir poderá ser usado para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

[Tutorial sobre a criação de máquinas virtuais altamente disponíveis](../../linux/create-cli-complete.md)

[Explorar amostras de CLI de implementação de VM](../../linux/cli-samples.md)
