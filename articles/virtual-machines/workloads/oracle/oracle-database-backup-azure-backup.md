---
title: Faça backup e recupere uma base de dados oracle Database 19c em um VM Azure Linux usando Azure Backup
description: Saiba como fazer backup e recuperar uma base de dados Oracle Database 19c utilizando o serviço de backup Azure.
author: cro27
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: d623d7b7ec25c096ebf54c030cf302e0a72e7fb2
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99064075"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-backup"></a>Faça backup e recupere uma base de dados oracle Database 19c em um VM Azure Linux usando Azure Backup

Este artigo demonstra o uso do Azure Backup para tirar fotografias de discos dos discos VM, que incluem os ficheiros de base de dados e a área de recuperação rápida. Utilizando a Cópia de Segurança Azure, pode tirar fotografias completas de disco adequadas como cópias de segurança, que são armazenadas no [Cofre dos Serviços de Recuperação](../../../backup/backup-azure-recovery-services-vault-overview.md).  O Azure Backup também fornece cópias de segurança consistentes com aplicações, que garantem que não são necessárias correções adicionais para restaurar os dados. Restaurar dados consistentes com as aplicações reduz o tempo de restauro, permitindo-lhe voltar rapidamente a um estado de execução.

> [!div class="checklist"]
>
> * Faça o backup da base de dados com backup consistente da aplicação
> * Restaurar e recuperar a base de dados de um ponto de recuperação
> * Restaurar o VM a partir de um ponto de recuperação

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

Para realizar o processo de backup e recuperação, tem primeiro de criar um Linux VM que tenha uma instância instalada da Oracle Database 19c. A imagem do Marketplace atualmente utilizada para criar o VM é  **Oracle:oracle-database-19-3:oracle-database-19-0904:last**. Siga os passos no [Oráculo crie uma base de dados rápida](./oracle-database-quick-create.md) para criar uma base de dados Oracle para completar este tutorial.


## <a name="prepare-the-environment"></a>Preparar o ambiente

Para preparar o ambiente, complete estes passos:

1. Ligue à VM.
1. Prepare a base de dados.

### <a name="connect-to-the-vm"></a>Ligar à VM

Para criar uma sessão Secure Shell (SSH) com o VM, utilize o seguinte comando. Substitua o endereço IP e a combinação de nome de anfitrião pelo `<publicIpAddress>` valor do seu VM.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
Mude para o utilizador *raiz:*

   ```bash
   sudo su -
   ```
    
Adicione o utilizador oráculo ao ficheiro */etc/sudoers:*

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

### <a name="prepare-the-database"></a>Preparar a base de dados

1. Este passo pressupõe que você tem uma instância Oráculo *(teste)* que está em execução em um VM chamado *vmoracle19c*.

   Mude o utilizador para o utilizador *oráculo:*
 
   ```bash
    sudo su - oracle
    ```
    
2. Antes de se ligar, tem de definir a variável ambiental ORACLE_SID:
    
    ```bash
    export ORACLE_SID=test;
    ```

    Deve também adicionar a variável ORACLE_SID ao ficheiro dos `oracle` utilizadores `.bashrc` para futuras insuposições utilizando o seguinte comando:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
3. Inicie o ouvinte do Oráculo se ainda não estiver a funcionar:

    ```output
    $ lsnrctl start
    ```

    O resultado deverá ter um aspeto semelhante ao seguinte exemplo:

    ```bash
    LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 18-SEP-2020 03:23:49

    Copyright (c) 1991, 2019, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    System parameter file is /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))

    Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    Start Date                18-SEP-2020 03:23:49
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Parameter File   /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
    The listener supports no services
    The command completed successfully
    ```

4.  Criar a localização da Área de Recuperação Rápida (FRA):

    ```bash
    mkdir /u02/fast_recovery_area
    ```

5.  Ligue-se à base de dados:

    ```bash
    SQL> sqlplus / as sysdba
    ```

6.  Inicie a base de dados se ainda não estiver em funcionamento:

    ```bash
    SQL> startup
    ```

7.  Definir variáveis de ambiente de base de dados para área de recuperação rápida:

    ```bash
    SQL> alter system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
8.  Certifique-se de que a base de dados está no modo de registo de arquivo para ativar cópias de segurança online.

    Verifique primeiro o estado do arquivo de registo:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    E se no modo NOARCHIVELOG, executar os seguintes comandos:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

9.  Criar uma tabela para testar as operações de backup e restauro:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

10. Configure a RMAN para voltar à Área de Recuperação Rápida localizada no disco VM:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/u02/fast_recovery_area/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s'; 
    ```

11. Confirme os detalhes da alteração de configuração:

    ```bash
    RMAN> show all;
    ```    

12.  Agora, faça o backup. O seguinte comando irá ter uma cópia de segurança completa da base de dados, incluindo ficheiros de arquivo, como um conjunto de backup em formato comprimido:

     ```bash
     RMAN> backup as compressed backupset database plus archivelog;
     ```

## <a name="using-azure-backup"></a>Utilizar o Azure Backup

O serviço Azure Backup fornece soluções simples, seguras e económicas que lhe permitem fazer cópias de segurança dos seus dados e recuperá-los a partir da cloud do Microsoft Azure. A Azure Backup fornece backups independentes e isolados para proteger contra a destruição acidental de dados originais. As cópias de segurança são armazenadas num cofre dos Serviços de Recuperação com gestão de pontos de recuperação incorporada. A configuração e a escalabilidade são simples, as cópias de segurança são otimizadas e pode facilmente restaurar conforme necessário.

O serviço Azure Backup fornece uma [estrutura](../../../backup/backup-azure-linux-app-consistent.md) para alcançar a consistência da aplicação durante cópias de segurança de Windows e Linux VMs para várias aplicações como Oracle, MySQL, Mongo DB, SAP HANA e PostGreSQL. Isto envolve invocar um pré-script (para quiesce as aplicações) antes de tirar uma foto dos discos e chamar o pós-script (comandos para descongelar as aplicações) após a conclusão do instantâneo, para devolver as aplicações ao modo normal. Enquanto os pré-scripts e pós-scripts da amostra são fornecidos no GitHub, a criação e manutenção destes scripts é da sua responsabilidade. 

Agora, o Azure Backup está a fornecer um quadro de pré-scripts e pós-scripts melhorados, onde o serviço de Backup Azure fornecerá pré-scripts e pós-scripts embalados para aplicações selecionadas. Os utilizadores do Azure Backup apenas precisam de nomear a aplicação e, em seguida, a cópia de segurança Azure VM irá automaticamente invocar os scripts pré-post relevantes. Os pré-scripts e pós-scripts embalados serão mantidos pela equipa de Backup do Azure e para que os utilizadores possam ter a certeza do suporte, propriedade e validade destes scripts. Atualmente, as aplicações apoiadas para o quadro reforçado são ***Oracle e MySQL** _, com mais tipos de aplicação esperados no futuro.

Nesta secção, utilizará o quadro melhorado do Azure Backup para obter imagens consistentes da sua base de dados VM e Oracle em execução. A base de dados será colocada no modo de backup permitindo que ocorra uma cópia de segurança on-line consistente transacionalmente enquanto o Azure Backup tira uma foto dos discos VM. O instantâneo será uma cópia completa do armazenamento e não um incremental ou copy on Write snapshot, por isso é um meio eficaz para restaurar a sua base de dados a partir de. A vantagem de usar instantâneos consistentes da aplicação Azure Backup é que são extremamente rápidos a tomar, não importa o tamanho da sua base de dados, e um instantâneo pode ser usado para operações de restauro assim que é tomada, sem ter que esperar que seja transferido para o cofre dos Serviços de Recuperação.

Para utilizar o Azure Backup para fazer backup na base de dados, complete estes passos:

1. Prepare o ambiente para uma cópia de segurança consistente da aplicação.
1. Configurar backups consistentes de aplicação.
1. Ativação de aplicação de gatilho cópia de segurança consistente do VM

### <a name="prepare-the-environment-for-application-consistent-backup"></a>Prepare o ambiente para apoio consistente de aplicações

1. Mude para o utilizador _ *raiz**

   ```bash
   sudo su -
   ```

1. Criar um novo utilizador de backup:

   ```bash
   useradd -G backupdba azbackup
   ```
   
2. Configurar o ambiente de utilizador de backup:

   ```bash
   echo "export ORACLE_SID=test" >> ~azbackup/.bashrc
   echo export ORACLE_HOME=/u01/app/oracle/product/19.0.0/dbhome_1 >> ~azbackup/.bashrc
   echo export PATH='$ORACLE_HOME'/bin:'$PATH' >> ~azbackup/.bashrc
   ```
   
3. Configurar a autenticação externa para o novo utilizador de backup. 
   O utilizador de backup precisa de ser capaz de aceder à base de dados utilizando a autenticação externa, de modo a não ser desafiado por uma palavra-passe.

   Primeiro mudar de volta para o utilizador **oráculo:**

   ```bash
   su - oracle
   ```

   Faça login na base de dados usando sqlplus e verifique as definições padrão para autenticação externa
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   A saída deve mostrar 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   Agora crie o azbackup do utilizador de base de dados autenticado externamente e conceda o privilégio sysbackup:
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   >[!IMPORTANT] 
   >Se receber erro "ORA-46953: O ficheiro de senha não está no formato 12.2".  quando executar a declaração GRANT acima, siga estes passos para migrar o ficheiro orapwd para o formato 12.2:
   >
   >Saia do sqlplus, mova o ficheiro de senha com formato antigo para um novo nome, migrar o ficheiro de senha e, em seguida, remover o ficheiro antigo. Depois de executar os comandos abaixo, reencambam a operação de concessão acima em sqlplus.
   
   ```bash
   mv $ORACLE_HOME/dbs/orapwtest $ORACLE_HOME/dbs/orapwtest.tmp
   orapwd file=$ORACLE_HOME/dbs/orapwtest input_file=$ORACLE_HOME/dbs/orapwtest.tmp
   rm $ORACLE_HOME/dbs/orapwtest.tmp
   ```
   
4. Crie um procedimento armazenado para registar mensagens de backup no registo de alerta da base de dados:

   ```bash
   sqlplus / as sysdba
   SQL> GRANT EXECUTE ON DBMS_SYSTEM TO SYSBACKUP;
   SQL> CREATE PROCEDURE sysbackup.azmessage(in_msg IN VARCHAR2)
   AS
     v_timestamp     VARCHAR2(32);
   BEGIN
     SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS')
     INTO v_timestamp FROM DUAL;
     DBMS_OUTPUT.PUT_LINE(v_timestamp || ' - ' || in_msg);
     SYS.DBMS_SYSTEM.KSDWRT(SYS.DBMS_SYSTEM.ALERT_FILE, in_msg);
   END azmessage;
   /
   SQL> SHOW ERRORS
   SQL> QUIT
   ```
   
### <a name="set-up-application-consistent-backups"></a>Configurar backups consistentes de aplicação  

1. Mude para o utilizador raiz 
   ```bash
   sudo su -
   ```

2. Crie o diretório de trabalho de backup consistente da aplicação
   ```bash
   if [ ! -d "/etc/azure" ]; then
      sudo mkdir /etc/azure
   fi
   ```
3. Criar um ficheiro em /etc/azure diretório chamado **workload.conf** com os seguintes conteúdos, que deve começar por `[workload]` . O seguinte comando criará o ficheiro e preencherá o conteúdo:
   ```bash
   echo "[workload]
   workload_name = oracle
   command_path = /u01/app/oracle/product/19.0.0/dbhome_1/bin/
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```
1. Descarregue os scripts pré-OracleMaster.sql e postOracleMaster.sql do [Repositório GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts) e copie-os para o diretório /etc/azure

4. Alterar as permissões do ficheiro
   ```bash
   chmod 744 workload.conf preOracleMaster.sql postOracleMaster.sql 
   ```

### <a name="trigger-application-consistent-backup-of-the-vm"></a>Ativação de aplicação de gatilho cópia de segurança consistente do VM

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  No portal Azure, vá ao seu grupo de recursos **rg-oráculo** e clique no seu **vmoracle19c** da Máquina Virtual.

2.  Na lâmina **de reserva,** crie um novo **Cofre de Serviços de Recuperação** no grupo de recursos **rg-oráculo** com o nome **myVault**.
    Para **escolher a política de backup**, use **(novo) DailyPolicy**. Se quiser alterar a frequência de backup ou o intervalo de retenção, **selecione Criar uma nova política.**

    ![Cofres de serviços de recuperação adicionam página](./media/oracle-backup-recovery/recovery-service-01.png)

3.  Para continuar, clique em **Ativar a Cópia de Segurança**.

    > [!IMPORTANT]
    > Depois de clicar **em Ativar a cópia de segurança,** o processo de backup não começa até que a hora programada expire. Para criar um reforço imediato, complete o próximo passo.

4. A partir da página do grupo de recursos, clique no seu recém-criado Cofre de Serviços de Recuperação **myVault**. Atenção: Pode ser necessário refrescar a página para a ver.

5.  No **myVault - Lâmina de itens de reserva,** em **CONTAGEM DE PONTOS DE CÓPIA,** selecione a contagem de artigos de reserva.

    ![Serviços de Recuperação abóbadas myVault details page](./media/oracle-backup-recovery/recovery-service-02.png)

6.  Na lâmina **de Itens de Reserva (Azure Virtual Machine),** no lado direito da página, clique no botão elipse **(...**) e, em seguida, clique em **Backup agora**.

    ![Serviços de Recuperação cofres Backup agora comando](./media/oracle-backup-recovery/recovery-service-03.png)

7.  Aceite a cópia de segurança por defeito Até ao valor e clique no botão **OK.** Aguarde que o processo de reserva termine. 

    Para ver o estado do trabalho de backup, clique em **Backup Jobs**.

    ![Página de trabalho de cofres de serviços de recuperação](./media/oracle-backup-recovery/recovery-service-04.png)

    O estado do trabalho de reserva aparece na seguinte imagem:

    ![Página de trabalho de cofres de serviços de recuperação com estado](./media/oracle-backup-recovery/recovery-service-05.png)
    
    Note que embora leve apenas segundos para executar o instantâneo, pode levar algum tempo para transferi-lo para o cofre, e o trabalho de reserva não está concluído até que a transferência esteja concluída.

8. Para uma cópia de segurança consistente com a aplicação, aborde quaisquer erros no ficheiro de registo. O ficheiro de registo está localizado em /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Criar um cofre de serviços de recuperação:

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```
2. Permitir a proteção de backup para o VM

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```
3. Desencadeie uma cópia de segurança para executar agora, em vez de esperar que a cópia de segurança desencadeie no horário predefinido (5h UTC). 

   ```azurecli
   az backup protection backup-now \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c 
   ```
   Pode monitorizar o progresso do trabalho de backup usando `az backup job list` e `az backup job show`

---

## <a name="recovery"></a>Recuperação

Para recuperar a base de dados, complete estes passos:

1. Remova os ficheiros da base de dados.
1. Gere um script de restauro a partir do cofre dos Serviços de Recuperação.
1. Monte o ponto de restauro.
1. Realizar recuperação.

### <a name="remove-the-database-files"></a>Remover os ficheiros da base de dados 

Mais tarde neste artigo, você vai aprender a testar o processo de recuperação. Antes de poder testar o processo de recuperação, tem de remover os ficheiros da base de dados.

1.  Desligue a instância do Oráculo:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Remova os ficheiros de dados e cópias de segurança:

    ```bash
    sudo su - oracle
    cd /u02/oradata/TEST
    rm -f *.dbf
    cd /u02/fast_recovery_area
    rm -f *
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>Gere um roteiro de restauro a partir do cofre dos Serviços de Recuperação

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No portal Azure, procure o item dos cofres *do MyVault* Recovery Services e clique nele.

    ![Serviços de Recuperação abóbadas myVault itens de backup](./media/oracle-backup-recovery/recovery-service-06.png)

2. Na lâmina **de visão geral,** selecione **itens de cópia de segurança** e a máquina virtual Azure **_Azure __*, que deve ter lista de itens de backup anon-zero.

    ![Serviços de Recuperação abóbadas Azure Virtual Machine contando item de backup](./media/oracle-backup-recovery/recovery-service-07.png)

3. Na página Itens de Cópias de Segurança (Azure Virtual Machines), o seu VM _ *vmoracle19c** está listado. Clique na elipse no direito de levantar o menu e selecione **A Recuperação de Ficheiros**.

    ![Screenshot da página de recuperação de ficheiros de cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery-service-08.png)

4. No painel **de recuperação de ficheiros (pré-visualização),** clique em **Baixar Script**. Em seguida, guarde o ficheiro de descarregamento (.py) para uma pasta no computador cliente. Uma palavra-passe é gerada para executar o script. Copie a palavra-passe para um ficheiro para utilização posterior. 

    ![Baixar ficheiro de script economiza opções](./media/oracle-backup-recovery/recovery-service-09.png)

5. Copie o ficheiro .py para o VM.

    O exemplo a seguir mostra como utilizar um comando de cópia segura (scp) para mover o ficheiro para o VM. Também pode copiar o conteúdo para a área de transferência e, em seguida, colar o conteúdo num novo ficheiro que é configurado no VM.

    > [!IMPORTANT]
    > No exemplo seguinte, certifique-se de atualizar os valores do endereço IP e da pasta. Os valores devem mapear para a pasta onde o ficheiro é guardado.

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para listar pontos de recuperação para o seu VM, utilize a lista de pontos de recuperação de backup az. Neste exemplo, selecionamos o mais recente ponto de recuperação para o VM nomeado myVM que está protegido no myRecoveryServicesVault:

```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
```

Para obter o script que liga, ou monta, o ponto de recuperação à VM, utilize az backup restore files mount-rp. O exemplo seguinte obtém o script para o VM nomeado myVM que está protegido no meuRecoveryServicesVault.

Substitua myRecoveryPointName pelo nome do ponto de recuperação que obteve no comando anterior:

```azurecli
   az backup restore files mount-rp \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --rp-name myRecoveryPointName
```

O script é transferido e é apresentada uma palavra-passe, como no exemplo seguinte:

```bash
   File downloaded: vmoracle19c_eus_4598131610710119312_456133188157_6931c635931f402eb543ee554e1cf06f102c6fc513d933.py. Use password c4487e40c760d29
```

Copie o ficheiro .py para o VM.

O exemplo a seguir mostra como utilizar um comando de cópia segura (scp) para mover o ficheiro para o VM. Também pode copiar o conteúdo para a área de transferência e, em seguida, colar o conteúdo num novo ficheiro que é configurado no VM.

> [!IMPORTANT]
> No exemplo seguinte, certifique-se de atualizar os valores do endereço IP e da pasta. Os valores devem mapear para a pasta onde o ficheiro é guardado.

```bash
$ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
```
---

### <a name="mount-the-restore-point"></a>Monte o ponto de restauro

1. Crie um ponto de montagem de restauro e copie o script para ele.

    No exemplo seguinte, crie um diretório **_/restaurar_* _ para o instantâneo montar para, mover o ficheiro para o diretório, e alterar o ficheiro de modo a que seja propriedade do utilizador raiz e tornado executável.

    ```bash 
    ssh azureuser@<publicIpAddress>
    sudo su -
    mkdir /restore
    chmod 777 /restore
    cd /restore
    cp /tmp/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py /restore
    chmod 755 /restore/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```
    
    Agora execute o script para restaurar a cópia de segurança. Será solicitado que forneça a palavra-passe gerada no portal Azure. 
  
   ```bash
    ./vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```

    O exemplo que se segue mostra o que deve ver depois de executar o script anterior. Quando te pedirem para continuares, entra em _*Y**.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : b1ad68e16dfafc6

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /restore/vmoracle19c-20201215123912/Volume1

    2)  | /dev/sdd  |  /dev/sdd1  |  /restore/vmoracle19c-20201215123912/Volume2

    3)  | /dev/sdd  |  /dev/sdd2  |  /restore/vmoracle19c-20201215123912/Volume3

    4)  | /dev/sdd  |  /dev/sdd15  |  /restore/vmoracle19c-20201215123912/Volume5

    The following partitions failed to mount since the OS couldn't identify the filesystem.

    ************ Volumes from unknown filesystem ************

    Sr.No.  |  Disk  |  Volume  |  Partition Type

    1)  | /dev/sdb  |  /dev/sdb14  |  BIOS Boot partition

    Please refer to '/restore/vmoracle19c-2020XXXXXXXXXX/Scripts/MicrosoftAzureBackupILRLogFile.log' for more details.

    ************ Open File Explorer to browse for files. ************

    After recovery, remove the disks and close the connection to the recovery point by clicking the 'Unmount Disks' button from the portal or by using the relevant unmount command in case of powershell or CLI.

    After unmounting disks, run the script with the parameter 'clean' to remove the mount paths of the recovery point from this machine.

    Please enter 'q/Q' to exit...
    ```

2. Confirma-se o acesso aos volumes montados.

    Para sair, insira **q** e, em seguida, procure os volumes montados. Para criar uma lista dos volumes adicionados, num pedido de comando, insira **df-h**.
    
    ```
    [root@vmoracle19c restore]# df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        3.8G     0  3.8G   0% /dev
    tmpfs           3.8G     0  3.8G   0% /dev/shm
    tmpfs           3.8G   17M  3.8G   1% /run
    tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
    /dev/sdd2        30G  9.6G   18G  36% /
    /dev/sdb1       126G  736M  119G   1% /u02
    /dev/sda1       497M  199M  298M  41% /boot
    /dev/sda15      495M  9.7M  486M   2% /boot/efi
    tmpfs           771M     0  771M   0% /run/user/54322
    /dev/sdc1       126G  2.9G  117G   3% /restore/vmoracle19c-20201215123912/Volume1
    /dev/sdd1       497M  199M  298M  41% /restore/vmoracle19c-20201215123912/Volume2
    /dev/sdd2        30G  9.6G   18G  36% /restore/vmoracle19c-20201215123912/Volume3
    /dev/sdd15      495M  9.7M  486M   2% /restore/vmoracle19c-20201215123912/Volume5
    ```

### <a name="perform-recovery"></a>Efetuar recuperação

1. Copie os ficheiros de cópias de segurança em falta para a área de recuperação rápida:

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/fast_recovery_area/TEST
    cp * /u02/fast_recovery_area/TEST
    cd /u02/fast_recovery_area/TEST
    chown -R oracle:oinstall *
    ```

2. Os seguintes comandos utilizam o RMAN para restaurar os ficheiros de dados em falta e recuperar a base de dados:

    ```bash
    sudo su - oracle
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
3. Verifique se o conteúdo da base de dados foi totalmente recuperado:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

4. Desmonte o ponto de restauro.

   ```bash
   umount /restore/vmoracle19c-20210107110037/Volume*
   ```

    No portal Azure, na lâmina de recuperação de **ficheiros (pré-visualização),** clique em **Discos Desmonte**.

    ![Desmonte o comando dos discos](./media/oracle-backup-recovery/recovery-service-10.png)
    
    Também pode desmontar os volumes de recuperação executando novamente o script python com a opção **-clean.**

## <a name="restore-the-entire-vm"></a>Restaurar todo o VM

Em vez de restaurar os ficheiros apagados dos cofres dos Serviços de Recuperação, pode restaurar todo o VM.

Para restaurar todo o VM, complete estes passos:

1. Parar e apagar vmoracle19c.
1. Recuperar o VM.
1. Desa estação o endereço IP público.
1. Ligue à VM.
1. Inicie a base de dados para montar o estágio e efetuar a recuperação.

### <a name="stop-and-delete-vmoracle19c"></a>Parar e apagar vmoracle19c

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No portal Azure, vá à máquina virtual **vmoracle19c** e, em seguida, **selecione Stop**.

1. Quando a Máquina Virtual já não estiver em funcionamento, **selecione Delete** e, em seguida, **Sim**.

   ![Comando de eliminação de cofre](./media/oracle-backup-recovery/recover-vm-01.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Parar e negociar o VM:

    ```azurecli
    az vm deallocate --resource-group rg-oracle --name vmoracle19c
    ```

2. Apague o VM. Insira 'y' quando solicitado:

    ```azurecli
    az vm delete --resource-group rg-oracle --name vmoracle19c
    ```

---

### <a name="recover-the-vm"></a>Recuperar o VM

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Criar uma conta de armazenamento para a encenação:
   
   Configure o armazenamento de ficheiros no portal Azure

   No portal Azure, selecione **_+ Crie um recurso_* _ e procure e selecione Conta de _*_Armazenamento_*_
    
   ![Página de adicionar conta de armazenamento](./media/oracle-backup-recovery/storage-1.png)
    
    
   Na página da conta de armazenamento Create, escolha o seu grupo de recursos _*_existente rg-oráculo,_*_ nomeie a sua conta de armazenamento _*_oracrestore_*_ e escolha _*_Armazenamento V2 (generalpurpose v2)_*_ para o Tipo de Conta. Alterar a replicação para _*_armazenamento localmente redundante (LRS)_*_ e definir desempenho para _*_padrão_*_. Certifique-se de que a Localização está definida para a mesma região que todos os seus outros recursos no grupo de recursos. 
    
   ![Página de adicionar conta de armazenamento](./media/oracle-backup-recovery/recovery-storage-1.png)
   
   Clique em 'Revisão + Criar' e, em seguida, clique em Criar.

2. No portal Azure, procure o item dos cofres dos Serviços de Recuperação _myVault* e clique nele.

    ![Serviços de Recuperação abóbadas myVault itens de backup](./media/oracle-backup-recovery/recovery-service-06.png)
    
3.  Na lâmina **de visão geral,** selecione **itens de cópia de segurança** e a máquina virtual Azure **_Azure __*, que deve ter lista de itens de backup anon-zero.

    ![Serviços de Recuperação abóbadas Azure Virtual Machine contando item de backup](./media/oracle-backup-recovery/recovery-service-07.png)

4.  Nos Itens de Cópias de Segurança (Azure Virtual Machines), a página do seu VM _ *vmoracle19c** está listado. Clique no nome VM.

    ![Página VM de recuperação](./media/oracle-backup-recovery/recover-vm-02.png)

5.  Na lâmina **vmoracle19c,** escolha um ponto de restauração que tenha um tipo de consistência de **Aplicação Consistente** e clique na elipse **(...**) no direito de levantar o menu.  A partir do menu clique **em Restaurar VM**.

    ![Restaurar o comando VM](./media/oracle-backup-recovery/recover-vm-03.png)

6.  Na lâmina da **máquina virtual restaurar,** escolha **Criar Nova** e Criar Nova **Máquina Virtual.** Introduza o nome da máquina virtual **vmoracle19c** e escolha o **VNet vmoracle19cVNET,** a sub-rede será automaticamente povoada para si com base na sua seleção VNet. O processo de restauro do VM requer uma conta de armazenamento Azure no mesmo grupo de recursos e região. Pode escolher a conta de armazenamento **que** configurar mais cedo.

    ![Restaurar valores de configuração](./media/oracle-backup-recovery/recover-vm-04.png)

7.  Para restaurar o VM, clique no botão **Restaurar.**

8.  Para ver o estado do processo de restauro, clique em **Jobs** e, em seguida, clique em **Backup Jobs**.

    ![Comando de estado de trabalhos de reserva](./media/oracle-backup-recovery/recover-vm-05.png)

    Clique na operação de restauro **em curso** para mostrar o estado do processo de restauro:

    ![Estado do processo de restauro](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para configurar a sua conta de armazenamento e partilha de ficheiros, execute os seguintes comandos no Azure CLI.

1. Crie a conta de armazenamento no mesmo grupo de recursos e localização que o seu VM:

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

2. Recupere a lista de pontos de recuperação disponíveis. 

   ```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
   ```

3. Restaurar o ponto de recuperação para a conta de armazenamento. Substitua `<myRecoveryPointName>` por um ponto de recuperação da lista gerada no passo anterior:

   ```azurecli
   az backup restore restore-disks \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --storage-account orarestore \
      --rp-name <myRecoveryPointName> \
      --target-resource-group rg-oracle
   ```

4. Recupere os detalhes do trabalho de restauro. O seguinte comando obtém mais detalhes para o trabalho restaurado desencadeado, incluindo o seu nome, que é necessário para recuperar o modelo URI. 

   ```azurecli
   az backup job list \
       --resource-group rg-oracle \
       --vault-name myVault \
       --output table
   ```

   A saída será semelhante a `(Note down the name of the restore job)` esta:

   ```output
   Name                                  Operation        Status     Item Name    Start Time UTC                    Duration
   ------------------------------------  ---------------  ---------  -----------  --------------------------------  --------------
   c009747a-0d2e-4ac9-9632-f695bf874693  Restore          Completed  vmoracle19c  2021-01-10T21:46:07.506223+00:00  0:03:06.634177
   6b779c98-f57a-4db1-b829-9e8eab454a52  Backup           Completed  vmoracle19c  2021-01-07T10:11:15.784531+00:00  0:21:13.220616
   502bc7ae-d429-4f0f-b78e-51d41b7582fc  ConfigureBackup  Completed  vmoracle19c  2021-01-07T09:43:55.298755+00:00  0:00:30.839674
   ```

5. Recupere os detalhes do URI para utilizar para recriar o VM. Substitua o nome de trabalho de restauração do passo anterior para `<RestoreJobName>` .

    ```azurecli
      az backup job show \
        -v myVault \
        -g rg-oracle \
        -n <RestoreJobName> \
        --query properties.extendedInfo.propertyBag
    ```

   A saída é semelhante a esta:

   ```output
   {
   "Config Blob Container Name": "vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2",
   "Config Blob Name": "config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Config Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Job Type": "Recover disks",
   "Recovery point time ": "1/7/2021 10:11:19 AM",
   "Target Storage Account Name": "orarestore",
   "Target resource group": "rg-oracle",
   "Template Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json"
   }
   ```

   O nome do modelo, que está no final do Modelo Blob Uri, que neste exemplo é `azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json` , e o nome do recipiente Blob, que está `vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2` listado. 

   Utilize estes valores no seguinte comando para atribuir variáveis na preparação para a criação do VM. Uma chave SAS é gerada para o recipiente de armazenamento com duração de 30 minutos.  


   ```azurecli
   expiretime=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
   connection=$(az storage account show-connection-string \
    --resource-group rg-oracle \
    --name orarestore \
    --query connectionString)
   token=$(az storage blob generate-sas \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
   url=$(az storage blob url \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --connection-string $connection \
    --output tsv)
   ```

   Agora implemente o modelo para criar o VM.

   ```azurecli
   az deployment group create \
      --resource-group rg-oracle \
      --template-uri $url?$token
   ```

   Ser-lhe-á solicitado que forneça um nome para o VM.

---

### <a name="set-the-public-ip-address"></a>Definir o endereço IP público

Após a restauração do VM, deverá reatribuir o endereço IP original para o novo VM.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  No portal Azure, aceda ao seu **vmoracle19c** da Máquina Virtual. Você vai notar que foi atribuído um novo IP público e NIC semelhante ao vmoracle19c-nic-XXXXXXXXXXXX, mas não tem um endereço DNS. Quando o VM original foi eliminado, o seu IP público e o NIC são mantidos e os próximos passos irão ligá-los ao novo VM.

    ![Lista de endereços IP públicos](./media/oracle-backup-recovery/create-ip-01.png)

2.  Parar a VM

    ![Criar endereço IP](./media/oracle-backup-recovery/create-ip-02.png)

3.  Ir para **networking**

    ![Endereço IP associado](./media/oracle-backup-recovery/create-ip-03.png)

4.  Clique na **interface de rede Attach**, escolha o NIC original **vmoracle19cVMNic, ao qual o endereço IP público original ainda está associado, e clique em **OK**

    ![Selecione o tipo de recurso e os valores de NIC](./media/oracle-backup-recovery/create-ip-04.png)

5.  Agora deve separar o NIC que foi criado com a operação de restauro VM, uma vez que está configurado como a interface principal. Clique na **interface de rede de desafogado** e escolha o novo NIC semelhante ao **vmoracle19c-nic-XXXXXXXXXXXXX,** em seguida, clique em **OK**

    ![Valor do endereço IP](./media/oracle-backup-recovery/create-ip-05.png)
    
    O seu VM recriado passará a ter o NIC original, que está associado ao endereço IP original e às regras do Grupo de Segurança da Rede
    
    ![Valor do endereço IP](./media/oracle-backup-recovery/create-ip-06.png)
    
6.  Volte para a **visão geral** e clique **em Iniciar** 

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Parar e negociar o VM:

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

2. Listar a corrente, restaurar VM NIC gerado

   ```azurecli
   az vm nic list --resource-group rg-oracle --vm-name vmoracle19c
   ```

   A saída será semelhante a esta, que lista o nome NIC gerado restaurado como `vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf`

   ```output
   {
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/rg-oracle/providers/Microsoft.Network/networkInterfaces/vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf",
    "primary": true,
    "resourceGroup": "rg-oracle"
   }
   ```

3. Anexar NIC original, que deve ter um nome `<VMName>VMNic` de, neste caso `vmoracle19cVMNic` . O endereço IP público original ainda está anexado a este NIC e será restaurado ao VM quando o NIC for recolocado. 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

4. Desprender o NIC gerado pela restauração

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```

5. Inicie o VM:

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="connect-to-the-vm"></a>Ligar à VM

* Para ligar ao VM, utilize o seguinte script:

    ```azurecli
    ssh <publicIpAddress>
    ```

### <a name="start-the-database-to-mount-stage-and-perform-recovery"></a>Inicie a base de dados para montar o estágio e realizar a recuperação

1. Pode verificar-se que o caso está a decorrer à medida que o arranque automático tentou iniciar a base de dados no arranque VM. No entanto, a base de dados requer recuperação e é provável que esteja apenas na fase de montagem, pelo que uma paragem preparatória é executada primeiro.

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> shutdown immediate
    SQL> startup mount
    SQL> recover automatic database;
    SQL> alter database open;
    ```
    
1. Verifique se o conteúdo da base de dados foi recuperado:

    ```bash
    SQL> select * from scott.scott_table;
    ```

A cópia de segurança e recuperação da base de dados 19c da Oracle Database num VM Azure Linux está agora concluída.

## <a name="delete-the-vm"></a>Elimine a VM

Quando já não precisar do VM, pode utilizar o seguinte comando para remover o grupo de recursos, o VM e todos os recursos relacionados:

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Criar VMs altamente disponíveis](../../linux/create-cli-complete.md)

[Explore amostras de CLI de implantação VM](../../linux/cli-samples.md)
