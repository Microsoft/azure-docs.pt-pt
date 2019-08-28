---
title: Fazer backup e recuperar um banco de dados Oracle Database 12c em uma máquina virtual Linux do Azure | Microsoft Docs
description: Saiba como fazer backup e recuperar um banco de dados Oracle Database 12c em seu ambiente do Azure.
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
ms.openlocfilehash: c493f79a066f872be6b38d127622cc757ab3c1cc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100248"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Fazer backup e recuperar um banco de dados Oracle Database 12c em uma máquina virtual Linux do Azure

Você pode usar CLI do Azure para criar e gerenciar recursos do Azure em um prompt de comando ou usar scripts. Neste artigo, usamos CLI do Azure scripts para implantar um banco de dados 12c Oracle Database de uma imagem da galeria do Azure Marketplace.

Antes de começar, verifique se o CLI do Azure está instalado. Para obter mais informações, consulte o [Guia de instalação do CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Preparar o ambiente

### <a name="step-1-prerequisites"></a>Passo 1: Pré-requisitos

*   Para executar o processo de backup e recuperação, você deve primeiro criar uma VM do Linux que tenha uma instância instalada do Oracle Database 12c. A imagem do Marketplace usada para criar a VM é denominada *Oracle: Oracle-Database-EE: 12.1.0.2: Latest*.

    Para saber como criar um banco de dados Oracle, consulte o guia de [início rápido do Oracle CREATE DATABASE](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Passo 2: Ligar à VM

*   Para criar uma sessão de Secure Shell (SSH) com a VM, use o comando a seguir. Substitua o endereço IP e a combinação de nome de host `publicIpAddress` pelo valor de sua VM.

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Passo 3: Preparar o banco de dados

1.  Esta etapa pressupõe que você tenha uma instância do Oracle (cdb1) que esteja em execução em uma VM denominada *myVM*.

    Execute a raiz de superusuário do *Oracle* e, em seguida, inicialize o ouvinte:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
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

2.  Adicional Verifique se o banco de dados está no modo de log de arquivo morto:

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
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```
3.  Adicional Crie uma tabela para testar a confirmação:

    ```bash
    SQL> alter session set "_ORACLE_SCRIPT"=true ;
    Session altered.
    SQL> create user scott identified by tiger;
    User created.
    SQL> grant create session to scott;
    Grant succeeded.
    SQL> grant create table to scott;
    Grant succeeded.
    SQL> alter user scott quota 100M on users;
    User altered.
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    Table created.
    SQL> insert into scott_Table VALUES(1,'Line 1');
    1 row created.
    SQL> commit;
    Commit complete.
    ```
4.  Verifique ou altere o local e o tamanho do arquivo de backup:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```
5. Use o RMAN (Oracle Recovery Manager) para fazer backup do banco de dados:

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Passo 4: Backup consistente com o aplicativo para VMs Linux

Backups consistentes com o aplicativo são um novo recurso no backup do Azure. Você pode criar e selecionar scripts para executar antes e depois do instantâneo da VM (pré-instantâneo e pós-instantâneo).

1. Baixe o arquivo JSON.

    Baixe VMSnapshotScriptPluginConfig. JSON de https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig. O conteúdo do arquivo deve ser semelhante ao seguinte:

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "",
        "postScriptLocation" : "",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

2. Crie a pasta/etc/Azure na VM:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Copie o arquivo JSON.

    Copie VMSnapshotScriptPluginConfig. JSON para a pasta/etc/Azure.

4. Edite o arquivo JSON.

    Edite o arquivo VMSnapshotScriptPluginConfig. JSON para incluir `PreScriptLocation` os `PostScriptlocation` parâmetros e. Por exemplo:

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "/etc/azure/pre_script.sh",
        "postScriptLocation" : "/etc/azure/post_script.sh",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

5. Crie os arquivos de script de pré-instantâneo e pós-instantâneo.

    Aqui está um exemplo de scripts de pré-instantâneo e pós-instantâneo para um "backup frio" (um backup offline, com desligamento e reinicialização):

    Para/etc/Azure/pre_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    Para/etc/Azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    Aqui está um exemplo de scripts de pré-instantâneo e pós-instantâneo para um "backup dinâmico" (um backup online):

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Para/etc/Azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Para/etc/Azure/pre_script.SQL, modifique o conteúdo do arquivo de acordo com seus requisitos:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    Para/etc/Azure/post_script.SQL, modifique o conteúdo do arquivo de acordo com seus requisitos:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Alterar permissões de arquivo:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Teste os scripts.

    Para testar os scripts, primeiro, entre como raiz. Em seguida, verifique se não há erros:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Para obter mais informações, consulte [backup consistente com o aplicativo para VMs do Linux](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Passo 5: Usar cofres dos serviços de recuperação do Azure para fazer backup da VM

1.  Na portal do Azure, pesquise os **cofres dos serviços de recuperação**.

    ![Página cofres dos serviços de recuperação](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Na folha **cofres dos serviços de recuperação** , para adicionar um novo cofre, clique em **Adicionar**.

    ![Página Adicionar cofres dos serviços de recuperação](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Para continuar, cliqueem myvault.

    ![Página de detalhes dos cofres dos serviços de recuperação](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Na folha **myvault** , clique em **backup**.

    ![Página de backup dos cofres dos serviços de recuperação](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Na folha **meta de backup** , use os valores padrão do **Azure** e da **máquina virtual**. Clique em **OK**.

    ![Página de detalhes dos cofres dos serviços de recuperação](./media/oracle-backup-recovery/recovery_service_05.png)

6.  Para **política de backup**,use DefaultPolicy ou selecione **criar nova política**. Clique em **OK**.

    ![Página de detalhes da política de backup de cofres dos serviços de recuperação](./media/oracle-backup-recovery/recovery_service_06.png)

7.  Na folha **selecionar máquinas virtuais** , marque a caixa de seleção **myVM1** e clique em **OK**. Clique no botão **habilitar backup** .

    ![Itens de cofres dos serviços de recuperação na página de detalhes do backup](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Depois de clicar em **habilitar backup**, o processo de backup não será iniciado até que o horário agendado expire. Para configurar um backup imediato, conclua a próxima etapa.

8.  Na folha **myvault-itens de backup** , em **contagem de itens de backup**, selecione a contagem de itens de backup.

    ![Página de detalhes dos cofres dos serviços de recuperação-cofre](./media/oracle-backup-recovery/recovery_service_08.png)

9.  Na folha **itens de backup (máquina virtual do Azure)** , no lado direito da página, clique no botão de reticências ( **...** ) e clique em **fazer backup agora**.

    ![Comando fazer backup de cofres dos serviços de recuperação agora](./media/oracle-backup-recovery/recovery_service_09.png)

10. Clique no botão **backup** . Aguarde a conclusão do processo de backup. Em seguida, vá [para a etapa 6: Remova os arquivos](#step-6-remove-the-database-files)de banco de dados.

    Para exibir o status do trabalho de backup, clique em **trabalhos**.

    ![Página de trabalho de cofres dos serviços de recuperação](./media/oracle-backup-recovery/recovery_service_10.png)

    O status do trabalho de backup aparece na imagem a seguir:

    ![Página de trabalho de cofres dos serviços de recuperação com status](./media/oracle-backup-recovery/recovery_service_11.png)

11. Para um backup consistente com o aplicativo, resolva os erros no arquivo de log. O arquivo de log está localizado em/var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Passo 6: Remover os arquivos de banco de dados 
Posteriormente neste artigo, você aprenderá a testar o processo de recuperação. Antes de poder testar o processo de recuperação, você precisa remover os arquivos de banco de dados.

1.  Remova o espaço de tabela e os arquivos de backup:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  Adicional Desligue a instância do Oracle:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Restaurar os arquivos excluídos dos cofres dos serviços de recuperação
Para restaurar os arquivos excluídos, conclua as seguintes etapas:

1. Na portal do Azure, procure o item cofres dos serviços de recuperação do *myvault* . Na folha **visão geral** , em **itens de backup**, selecione o número de itens.

    ![Itens de backup do myvault dos cofres dos serviços de recuperação](./media/oracle-backup-recovery/recovery_service_12.png)

2. Em **contagem de itens de backup**, selecione o número de itens.

    ![Cofres dos serviços de recuperação contagem de itens de backup de máquina virtual do Azure](./media/oracle-backup-recovery/recovery_service_13.png)

3. Na folha **myvm1** , clique em **recuperação de arquivo (versão prévia)** .

    ![Captura de tela da página de recuperação de arquivos dos cofres dos serviços de recuperação](./media/oracle-backup-recovery/recovery_service_14.png)

4. No painel **recuperação de arquivo (versão prévia)** , clique em **baixar script**. Em seguida, salve o arquivo de download (. sh) em uma pasta no computador cliente.

    ![Baixar opções de salvamento de arquivo de script](./media/oracle-backup-recovery/recovery_service_15.png)

5. Copie o arquivo. sh para a VM.

    O exemplo a seguir mostra como usar um comando de cópia segura (SCP) para mover o arquivo para a VM. Você também pode copiar o conteúdo para a área de transferência e, em seguida, colar o conteúdo em um novo arquivo que está configurado na VM.

    > [!IMPORTANT]
    > No exemplo a seguir, certifique-se de atualizar os valores de endereço IP e pasta. Os valores devem ser mapeados para a pasta em que o arquivo é salvo.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. Altere o arquivo, para que ele seja de propriedade da raiz.

    No exemplo a seguir, altere o arquivo para que ele seja de propriedade da raiz. Em seguida, altere as permissões.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    O exemplo a seguir mostra o que você deve ver depois de executar o script anterior. Quando for solicitado a continuar, digite **Y**.

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    The script requires 'open-iscsi' and 'lshw' to run.
    Do you want us to install 'open-iscsi' and 'lshw' on this machine?
    Please press 'Y' to continue with installation, 'N' to abort the operation. : Y
    Installing 'open-iscsi'....
    Installing 'lshw'....

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sde  |  /dev/sde1  |  /root/myVM-20170517093913/Volume1

    2)  | /dev/sde  |  /dev/sde2  |  /root/myVM-20170517093913/Volume2

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

7. O acesso aos volumes montados é confirmado.

    Para sair, insira **p**e procure os volumes montados. Para criar uma lista dos volumes adicionados, em um prompt de comando, digite **df-k**.

    ![O comando df-k](./media/oracle-backup-recovery/recovery_service_16.png)

8. Use o script a seguir para copiar os arquivos ausentes de volta para as pastas:

    ```bash
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cp *.bkp /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cd /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # chown oracle:oinstall *.bkp
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/oradata/cdb1
    # cp *.dbf /u01/app/oracle/oradata/cdb1
    # cd /u01/app/oracle/oradata/cdb1
    # chown oracle:oinstall *.dbf
    ```
9. No script a seguir, use o RMAN para recuperar o banco de dados:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```
    
10. Desmonte o disco.

    Na portal do Azure, na folha **recuperação de arquivo (versão prévia)** , clique em desmontar **discos**.

    ![Comando desmontar discos](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Restaurar toda a VM

Em vez de restaurar os arquivos excluídos dos cofres dos serviços de recuperação, você pode restaurar toda a VM.

### <a name="step-1-delete-myvm"></a>Passo 1: Excluir myVM

*   Na portal do Azure, vá para o cofre **myVM1** e, em seguida, selecione **excluir**.

    ![Comando de exclusão do cofre](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Passo 2: Recuperar a VM

1.  Vá para **cofres dos serviços de recuperação**e selecione **myvault**.

    ![entrada do myvault](./media/oracle-backup-recovery/recover_vm_02.png)

2.  Na folha **visão geral** , em **itens de backup**, selecione o número de itens.

    ![itens de backup do myvault](./media/oracle-backup-recovery/recover_vm_03.png)

3.  Na folha **itens de backup (máquina virtual do Azure)** , selecione **myvm1**.

    ![Página VM de recuperação](./media/oracle-backup-recovery/recover_vm_04.png)

4.  Na folha **myvm1** , clique no botão de reticências ( **...** ) e, em seguida, clique em **restaurar VM**.

    ![Comando restaurar VM](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Na folha **selecionar ponto de restauração** , selecione o item que você deseja restaurar e clique em **OK**.

    ![Selecionar o ponto de restauração](./media/oracle-backup-recovery/recover_vm_06.png)

    Se você tiver habilitado o backup consistente com o aplicativo, uma barra azul vertical será exibida.

6.  Na folha **configuração de restauração** , selecione o nome da máquina virtual, selecione o grupo de recursos e clique em **OK**.

    ![Restaurar valores de configuração](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Para restaurar a VM, clique no botão **restaurar** .

8.  Para exibir o status do processo de restauração, clique em **trabalhos**e em **trabalhos de backup**.

    ![Comando de status de trabalhos de backup](./media/oracle-backup-recovery/recover_vm_08.png)

    A figura a seguir mostra o status do processo de restauração:

    ![Status do processo de restauração](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Passo 3: Definir o endereço IP público
Depois que a VM for restaurada, configure o endereço IP público.

1.  Na caixa de pesquisa, insira **endereço IP público**.

    ![Lista de endereços IP públicos](./media/oracle-backup-recovery/create_ip_00.png)

2.  Na folha **endereços IP públicos** , clique em **Adicionar**. Na folha **criar endereço IP público** , em **nome**, selecione o nome do IP público. Em **Grupo de recursos**, selecione **Utilizar existente**. Em seguida, clique em **criar**.

    ![Criar endereço IP](./media/oracle-backup-recovery/create_ip_01.png)

3.  Para associar o endereço IP público com a interface de rede da VM, procure e selecione **myVMip**. Em seguida, clique em **associar**.

    ![Associar endereço IP](./media/oracle-backup-recovery/create_ip_02.png)

4.  Para **tipo de recurso**, selecione **interface de rede**. Selecione a interface de rede que é usada pela instância myVM e clique em **OK**.

    ![Selecionar tipo de recurso e valores de NIC](./media/oracle-backup-recovery/create_ip_03.png)

5.  Pesquise e abra a instância do myVM que é transportada do Portal. O endereço IP que está associado à VM aparece na folha **visão geral** do myVM.

    ![Valor do endereço IP](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Passo 4: Ligar à VM

*   Para se conectar à VM, use o seguinte script:

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Passo 5: Testar se o banco de dados está acessível
*   Para testar a acessibilidade, use o seguinte script:

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Se o comando de **inicialização** do banco de dados gerar um erro, para recuperar [o banco de dados, consulte a etapa 6: Use o RMAN para recuperar o](#step-6-optional-use-rman-to-recover-the-database)banco de dados.

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Passo 6: Adicional Usar o RMAN para recuperar o banco de dados
*   Para recuperar o banco de dados, use o seguinte script:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

O backup e a recuperação do banco de dados do Oracle Database 12c em uma VM Linux do Azure agora estão concluídos.

## <a name="delete-the-vm"></a>Elimine a VM

Quando você não precisar mais da VM, poderá usar o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Criar VMs altamente disponíveis](../../linux/create-cli-complete.md)

[Explorar exemplos de implantação de VM CLI do Azure](../../linux/cli-samples.md)



