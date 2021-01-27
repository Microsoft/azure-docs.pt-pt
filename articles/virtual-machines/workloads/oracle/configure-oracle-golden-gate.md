---
title: Implementar o Portão Dourado do Oráculo num | VM Azure Linux Microsoft Docs
description: Rapidamente coloque um Oracle Golden Gate a funcionar no seu ambiente Azure.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: 6b3f4ef82813fd4c0e5b3b24be59b68a2dc5b2a7
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880394"
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>Implementar o Portão Dourado do Oráculo num VM Azure Linux 

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia detalha como usar o CLI Azure para implementar uma base de dados Oracle 12c a partir da imagem da galeria Azure Marketplace. 

Este documento mostra-lhe passo a passo como criar, instalar e configurar o Oracle Golden Gate num VM Azure. Neste tutorial, duas máquinas virtuais são configuras numa única região. O mesmo tutorial pode ser usado para configurar o OracleGolden Gate para VMs em diferentes Zonas de Disponibilidade numa única região de Azure ou para a configuração de VMs em duas regiões diferentes.

Antes de começar, certifique-se de que a CLI do Azure foi instalada. Para obter mais informações, veja [Guia de instalação da CLI do Azure](/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Preparar o ambiente

Para realizar a instalação Oracle Golden Gate, é necessário criar dois VMs Azure no mesmo conjunto de disponibilidade. A imagem do Marketplace que utiliza para criar os VMs é **Oracle:Oracle-Database-Ee:12.1.0.2:last**.

Você também precisa estar familiarizado com o editor Unix vi e ter uma compreensão básica de x11 (X Windows).

Segue-se um resumo da configuração do ambiente:
> 
> |  | **Site primário** | **Site de replicação** |
> | --- | --- | --- |
> | **Libertação do Oráculo** |Oracle 12c Release 2 – (12.1.0.2) |Oracle 12c Release 2 – (12.1.0.2)|
> | **Nome da máquina** |myVM1 |myVM2 |
> | **Sistema operativo** |Oráculo Linux 6.x |Oráculo Linux 6.x |
> | **Oráculo SID** |CDB1 |CDB1 |
> | **Esquema de replicação** |TESTE|TESTE |
> | **Dono/réplica golden gate** |C##GGADMIN |REPUSER |
> | **Processo Golden Gate** |EXTORA |REPORA|


### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão na sua subscrição Azure com o comando [de login az.](/cli/azure/reference-index) Em seguida, siga as instruções no ecrã.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos Azure é um recipiente lógico no qual os recursos da Azure são implantados e a partir do qual podem ser geridos. 

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westus`.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

O passo seguinte é opcional, mas recomendado. Para mais informações, consulte [o guia de conjuntos de disponibilidades do Azure.](/previous-versions/azure/virtual-machines/windows/infrastructure-example)

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com o comando [z vm create](/cli/azure/vm). 

O exemplo a seguir cria dois VMs nomeados `myVM1` e `myVM2` . Crie teclas SSH se ainda não existirem num local de chave predefinido. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.

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

Após a criação do VM, o Azure CLI mostra informações semelhantes ao exemplo seguinte. (Tome nota do `publicIpAddress` . . Este endereço é utilizado para aceder ao VM.)

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

Tome nota do `publicIpAddress` bem depois de ter sido criado.

### <a name="open-the-tcp-port-for-connectivity"></a>Abra a porta TCP para a conectividade

O próximo passo é configurar pontos finais externos, que lhe permitem aceder remotamente à base de dados da Oracle. Para configurar os pontos finais externos, executar os seguintes comandos.

#### <a name="open-the-port-for-myvm1"></a>Abra a porta para o myVM1:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Os resultados devem ser semelhantes à seguinte resposta:

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

#### <a name="open-the-port-for-myvm2"></a>Abra a porta para o myVM2:

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

### <a name="create-the-database-on-myvm1-primary"></a>Criar a base de dados no myVM1 (primário)

O software Oracle já está instalado na imagem do Marketplace, pelo que o próximo passo é instalar a base de dados. 

Executar o software como o superuser 'oráculo':

```bash
sudo su - oracle
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
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

Desa estada as variáveis ORACLE_SID e ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Opcionalmente, pode adicionar ORACLE_HOME e ORACLE_SID ao ficheiro .bashrc, de modo a que estas definições sejam guardadas para futuras insuposições:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Iniciar ouvinte oráculo

```bash
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>Criar a base de dados no myVM2 (replicar)

```bash
sudo su - oracle
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

Desa estada as variáveis ORACLE_SID e ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Opcionalmente, pode adicionar ORACLE_HOME e ORACLE_SID ao ficheiro .bashrc, de modo a que estas definições sejam guardadas para futuras insuposições.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Iniciar ouvinte oráculo

```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>Configure Golden Gate 
Para configurar o Golden Gate, dê os passos nesta secção.

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
Ativar a registo de força e certifique-se de que pelo menos um ficheiro de registo está presente.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>Descarregue o software Golden Gate
Para descarregar e preparar o software Oracle Golden Gate, complete os seguintes passos:

1. Descarregue o ficheiro **fbo_ggs_Linux_x64_shiphome.zip** da página de [descarregamento](https://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html)do Oracle Golden Gate . Sob o título de download **Oracle GoldenGate 12.x.x.x.x para Oracle Linux x86-64**, deve haver um conjunto de ficheiros .zip para descarregar.

2. Depois de transferir os ficheiros .zip para o computador do seu cliente, utilize o Secure Copy Protocol (SCP) para copiar os ficheiros para o seu VM:

   ```bash
   $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
   ```

3. Mover os ficheiros .zip para a pasta **/opt.** Em seguida, mude o proprietário dos ficheiros da seguinte forma:

   ```bash
   $ sudo su -
   # mv <folder>/*.zip /opt
   ```

4. Desaperte os ficheiros (instale o utilitário Linux unzip se ainda não estiver instalado):

   ```bash
   # yum install unzip
   # cd /opt
   # unzip fbo_ggs_Linux_x64_shiphome.zip
   ```

5. Alterar a permissão:

   ```bash
   # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
   ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Prepare o cliente e a VM para executar x11 (apenas para clientes Windows)
Este passo é opcional. Pode saltar este passo se estiver a utilizar um cliente Linux ou já tiver configuração x11.

1. Baixe o PuTTY e o Xming para o seu computador Windows:

   * [Baixar PuTTY](https://www.putty.org/)
   * [Baixar Xming](https://xming.en.softonic.com/)

2. Depois de instalar o PuTTY, na pasta PuTTY (por exemplo, C:\Program Files\PuTTY), executar puttygen.exe (Gerador de Chaves PuTTY).

3. No gerador de chaves PuTTY:

   - Para gerar uma tecla, selecione o botão **Gerar.**
   - Copiar o conteúdo da chave **(Ctrl+C).**
   - Selecione o botão **de tecla privada Save.**
   - Ignore o aviso que aparece e, em seguida, selecione **OK**.

   ![Screenshot da página do gerador de chaves PuTTY](./media/oracle-golden-gate/puttykeygen.png)

4. No seu VM, execute estes comandos:

   ```bash
   # sudo su - oracle
   $ mkdir .ssh (if not already created)
   $ cd .ssh
   ```

5. Crie um ficheiro chamado **authorized_keys**. Cole o conteúdo da chave neste ficheiro e, em seguida, guarde o ficheiro.

   > [!NOTE]
   > A chave deve conter a `ssh-rsa` corda. Além disso, o conteúdo da chave deve ser uma única linha de texto.
   >  

6. Inicie o PuTTY. No painel **de categorias,** selecione **Connection**  >  **SSH**  >  **Auth**. No ficheiro chave Privado para a caixa **de autenticação,** consulte a chave que gerou anteriormente.

   ![Screenshot da página de chave privada set](./media/oracle-golden-gate/setprivatekey.png)

7. No painel **de categorias,** selecione **Connection**  >  **SSH**  >  **X11**. Em seguida, selecione a caixa **de reencaminhamento Enable X11.**

   ![Screenshot da página Enable X11](./media/oracle-golden-gate/enablex11.png)

8. No painel **de categorias,** vá à **Sessão.** Introduza as informações do anfitrião e, em seguida, **selecione Abrir**.

   ![Screenshot da página da sessão](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>Instale o software Golden Gate

Para instalar o Oracle Golden Gate, complete os seguintes passos:

1. Inscreva-se como oráculo. (Deve poder iniciar sedução sem ser solicitado para uma senha.) Certifique-se de que o Xming está a funcionar antes de iniciar a instalação.

   ```bash
   $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
   $ ./runInstaller
   ```

2. Selecione 'Oracle GoldenGate for Oracle Database 12c'. Em seguida, selecione **Next** para continuar.

   ![Screenshot do instalador Selecione a página de instalação](./media/oracle-golden-gate/golden_gate_install_01.png)

3. Mude a localização do software. Em seguida, selecione a caixa **'Start Manager'** e introduza a localização da base de dados. Selecione **Seguinte** para continuar.

   ![Screenshot da página de instalação select](./media/oracle-golden-gate/golden_gate_install_02.png)

4. Mude o diretório de inventário e, em seguida, selecione **Seguinte** para continuar.

   ![Screenshot da página Select Installation que mostra o diretório de instalação.](./media/oracle-golden-gate/golden_gate_install_03.png)

5. No ecrã **Resumo,** **selecione Instalar** para continuar.

   ![Screenshot que mostra a página De Instalação Select e o botão Instalar.](./media/oracle-golden-gate/golden_gate_install_04.png)

6. Pode ser solicitado a executar um guião como "raiz". Em caso afirmativo, abra uma sessão separada, ssh para o VM, sudo para raiz e, em seguida, executar o script. Selecione **OK** continue.

   ![Screenshot que mostra a localização do script e como executar o script de configuração.](./media/oracle-golden-gate/golden_gate_install_05.png)

7. Quando a instalação tiver terminado, selecione **Close** para concluir o processo.

   ![Screenshot da página De Instalação Select que mostra o botão Fechar.](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>Configurar o serviço no myVM1 (primário)

1. Criar ou atualizar o ficheiro tnsnames.ora:

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

2. Crie o proprietário do Golden Gate e as contas de utilizador.

   > [!NOTE]
   > A conta do proprietário deve ter prefixo C##.
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

3. Crie a conta de utilizador de teste Golden Gate:

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

4. Configure o ficheiro do parâmetro de extração.

   Iniciar a interface de linha de comando Golden Gate (ggsci):

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

5. Adicione o seguinte ao ficheiro de parâmetros EXTRACT (utilizando comandos vi). Prima a tecla Esc, ':wq!' para salvar ficheiro. 

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

6. Extrato integrado do registo:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci

   GGSCI> dblogin userid C##GGADMIN, password ggadmin
   Successfully logged into database CDB$ROOT.

   GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

   2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

   GGSCI> exit
   ```

7. Configurar os pontos de verificação de extração e iniciar o extrato em tempo real:

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

   Neste passo, encontra-se o SCN inicial, que será usado mais tarde, numa secção diferente:

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


1. Criar ou atualizar o ficheiro tnsnames.ora:

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

2. Criar uma conta replicada:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> create user repuser identified by rep_pass container=current;
   SQL> grant dba to repuser;
   SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
   SQL> connect repuser/rep_pass@pdb1 
   SQL> EXIT;
   ```

3. Criar uma conta de utilizador de teste Golden Gate:

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

4. Ficheiro de parâmetro REPLICAT para replicar alterações: 

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> EDIT PARAMS REPORA  
   ```

   Conteúdo do ficheiro de parâmetros REPORA:

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

5. Configurar um ponto de verificação replicado:

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

Atualize o ficheiro com o seguinte:

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```

Em seguida, reinicie o serviço Manager:

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

Altere o número SCN com o número que obteve antes:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```

A replicação começou, e pode testá-la inserindo novos registos em tabelas de TESTE.


### <a name="view-job-status-and-troubleshooting"></a>Ver estado de trabalho e resolução de problemas

#### <a name="view-reports"></a>Ver relatórios
Para ver relatórios sobre o myVM1, executar os seguintes comandos:

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
Para ver relatórios sobre o myVM2, executar os seguintes comandos:

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>Ver estado e história
Para ver o estado e a história no myVM1, execute os seguintes comandos:

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

Para ver o estado e a história no myVM2, executar os seguintes comandos:

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
Isto completa a instalação e configuração do Golden Gate no Oracle linux.


## <a name="delete-the-virtual-machine"></a>Elimine a máquina virtual

Quando já não é necessário, o seguinte comando pode ser usado para remover o grupo de recursos, VM, e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximos passos

[Tutorial sobre a criação de máquinas virtuais altamente disponíveis](../../linux/create-cli-complete.md)

[Explorar amostras de CLI de implementação de VM](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)