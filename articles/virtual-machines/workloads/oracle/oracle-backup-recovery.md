---
title: Criar cópias de segurança e recuperar uma base de dados do Oracle Database 12C numa máquina virtual Linux do Azure | Documentos da Microsoft
description: Saiba como criar cópias de segurança e recuperar uma base de dados do Oracle Database 12C no ambiente do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
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
ms.openlocfilehash: c41f13a6437f69121d3bbb387c96d8e13f2be0b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567082"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Criar cópias de segurança e recuperar uma base de dados do Oracle Database 12C numa máquina virtual Linux do Azure

Pode utilizar a CLI do Azure para criar e gerir recursos do Azure no prompt de comando ou utilizar scripts. Neste artigo, usamos scripts da CLI do Azure para implementar uma base de dados do Oracle Database 12C partir de uma imagem de galeria do Azure Marketplace.

Antes de começar, certifique-se de que a CLI do Azure está instalada. Para obter mais informações, consulte a [guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Preparar o ambiente

### <a name="step-1-prerequisites"></a>Passo 1: Pré-requisitos

*   Para executar o processo de cópia de segurança e recuperação, tem primeiro de criar uma VM do Linux que tenha uma instância instalada do Oracle Database 12C. A imagem do Marketplace que utilizar para criar a VM com o nome *Oracle: Oracle-base de dados-Ee:12.1.0.2:latest*.

    Para saber como criar uma base de dados do Oracle, veja a [Oracle criar o guia de introdução do banco de dados](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Passo 2: Ligar à VM

*   Para criar uma sessão Secure Shell (SSH) com a VM, utilize o seguinte comando. Substitua o endereço IP e a combinação de nome de anfitrião com o `publicIpAddress` valor para a sua VM.

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Passo 3: Preparar a base de dados

1.  Este passo parte do princípio de que tem uma instância de Oracle (cdb1) que está em execução numa VM com o nome *myVM*.

    Executar o *oracle* Superutilizador raiz e, em seguida, initialize o serviço de escuta:

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

2.  (Opcional) Certifique-se de que a base de dados está no modo de registo do arquivo:

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
3.  (Opcional) Crie uma tabela para testar a consolidação:

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
4.  Verificar ou alterar a localização do ficheiro de cópia de segurança e o tamanho:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```
5. Utilize o Gestor de recuperação de Oracle (RMAN) para criar cópias de segurança da base de dados:

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Passo 4: Cópia de segurança consistentes com aplicações para as VMs do Linux

As cópias de segurança consistentes com a aplicação é um novo recurso do Azure Backup. Pode criar e selecione os scripts sejam executados antes e depois o instantâneo VM (anterior ao instantâneo e posterior ao instantâneo).

1. Transfira o ficheiro JSON.

    Transferir VMSnapshotScriptPluginConfig.json de https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig. O conteúdo do ficheiro ter um aspeto semelhante ao seguinte:

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

2. Crie a pasta de /etc/Azure. na VM:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Copie o ficheiro JSON.

    Copie VMSnapshotScriptPluginConfig.json para a pasta de /etc/Azure.

4. Edite o ficheiro JSON.

    Edite o ficheiro de VMSnapshotScriptPluginConfig.json para incluir o `PreScriptLocation` e `PostScriptlocation` parâmetros. Por exemplo:

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

5. Crie os arquivos de script anterior ao instantâneo e posterior ao instantâneo.

    Eis um exemplo de scripts anterior ao instantâneo e posterior ao instantâneo para uma "fria cópia de segurança" (uma cópia de segurança offline, com o encerramento e reinício):

    Para /etc/azure/pre_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    Para /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    Eis um exemplo de scripts anterior ao instantâneo e posterior ao instantâneo para uma "acesso frequente cópia de segurança" (uma cópia de segurança online):

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Para /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Para /etc/azure/pre_script.sql, modifica o conteúdo do ficheiro, de acordo com suas necessidades:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    Para /etc/azure/post_script.sql, modifica o conteúdo do ficheiro, de acordo com suas necessidades:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Altere as permissões de ficheiro:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Os scripts de teste.

    Para testar os scripts, primeiro, inicie sessão como raiz. Em seguida, certifique-se de que não há nenhum erro:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Para obter mais informações, consulte [cópias de segurança para VMs do Linux consistentes](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Passo 5: Cofres dos serviços de recuperação do Azure de utilização para fazer uma cópia de segurança da VM

1.  No portal do Azure, procure **cofres dos serviços de recuperação**.

    ![Página de cofres dos serviços de recuperação](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Sobre o **cofres dos serviços de recuperação** painel, para adicionar um novo cofre, clique em **Add**.

    ![Os cofres dos serviços de recuperação Adicionar página](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Para continuar, clique em **myVault**.

    ![Página de detalhe de cofres dos serviços de recuperação](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Sobre o **myVault** painel, clique em **cópia de segurança**.

    ![Os cofres dos serviços de recuperação de cópia de segurança página](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Sobre o **objetivo de cópia de segurança** painel, utilize os valores predefinidos de **Azure** e **Máquina Virtual**. Clique em **OK**.

    ![Página de detalhe de cofres dos serviços de recuperação](./media/oracle-backup-recovery/recovery_service_05.png)

6.  Para **política de cópia de segurança**, utilize **DefaultPolicy**, ou selecione **criar nova política**. Clique em **OK**.

    ![Página de detalhes da política de cópia de segurança dos cofres dos serviços de recuperação](./media/oracle-backup-recovery/recovery_service_06.png)

7.  Sobre o **selecionar máquinas virtuais** painel, selecione a **myVM1** caixa de verificação e, em seguida, clique em **OK**. Clique nas **ativar cópia de segurança** botão.

    ![Itens de cofres dos serviços de recuperação para a página de detalhes de cópia de segurança](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Depois de clicar em **ativar cópia de segurança**, o processo de cópia de segurança não iniciar até que expire a hora agendada. Para configurar uma cópia de segurança de imediato, conclua o passo seguinte.

8.  Sobre o **myVault - itens de cópia de segurança** painel, em **CONTAGEM de itens de cópia de segurança**, selecione o número de itens de cópia de segurança.

    ![Página de detalhes de myVault de cofres dos serviços de recuperação](./media/oracle-backup-recovery/recovery_service_08.png)

9.  Sobre o **itens de cópia de segurança (Máquina Virtual do Azure)** painel, no lado direito da página, clique nas reticências (**...** ) botão e, em seguida, clique em **cópia de segurança agora**.

    ![Cópia de segurança agora comando cofres de serviços de recuperação](./media/oracle-backup-recovery/recovery_service_09.png)

10. Clique nas **cópia de segurança** botão. Aguarde que o processo de cópia de segurança concluir. Em seguida, vá para [passo 6: Remover os ficheiros de base de dados](#step-6-remove-the-database-files).

    Para ver o estado da tarefa de cópia de segurança, clique em **tarefas**.

    ![Página de tarefa de cofres dos serviços de recuperação](./media/oracle-backup-recovery/recovery_service_10.png)

    O estado da tarefa de cópia de segurança é apresentado na imagem seguinte:

    ![Os cofres dos serviços de recuperação página com o estado da tarefa](./media/oracle-backup-recovery/recovery_service_11.png)

11. Para uma cópia de segurança consistentes com aplicações, endereço de quaisquer erros no ficheiro de registo. O ficheiro de registo está localizado em /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Passo 6: Remover os ficheiros de base de dados 
Neste artigo, aprenderá como testar o processo de recuperação. Antes de testar o processo de recuperação, tem de remover os ficheiros de base de dados.

1.  Remova os ficheiros de cópia de segurança e de espaço de tabelas:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  (Opcional) Encerre a instância de Oracle:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Restaurar ficheiros eliminados a partir de cofres de serviços de recuperação
Para restaurar os arquivos excluídos, conclua os seguintes passos:

1. No portal do Azure, procure o *myVault* item de cofres dos serviços de recuperação. Sobre o **descrição geral** painel, em **itens de cópia de segurança**, selecione o número de itens.

    ![Itens de myVault de cofres dos serviços do recuperação cópia de segurança](./media/oracle-backup-recovery/recovery_service_12.png)

2. Sob **número de itens de cópia de segurança**, selecione o número de itens.

    ![Número de itens de cópia de segurança de Máquina Virtual do Azure de cofres dos serviços de recuperação](./media/oracle-backup-recovery/recovery_service_13.png)

3. Sobre o **myvm1** painel, clique em **recuperação de ficheiros (pré-visualização)**.

    ![Página de recuperação de ficheiros de cofres de captura de ecrã dos serviços de recuperação](./media/oracle-backup-recovery/recovery_service_14.png)

4. Sobre o **recuperação de ficheiros (pré-visualização)** painel, clique em **transferir Script**. Em seguida, guarde o ficheiro de transferência (. SH) para uma pasta no computador cliente.

    ![Opções de poupa de ficheiro de script de download](./media/oracle-backup-recovery/recovery_service_15.png)

5. Copie o ficheiro. SH para a VM.

    O exemplo seguinte mostra como a utilização de uma cópia segura (scp) de comando para mover o ficheiro para a VM. Também pode copiar o conteúdo na área de transferência e, em seguida, cole o conteúdo num novo ficheiro que está configurado na VM.

    > [!IMPORTANT]
    > No exemplo a seguir, certifique-se que Atualize os valores de endereço e a pasta IP. Os valores tem de mapear para a pasta onde o ficheiro é guardado.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. Altere o arquivo, para que o proprietário é a raiz.

    No exemplo seguinte, altere o arquivo para que o proprietário é a raiz. Em seguida, altere as permissões.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    O exemplo seguinte mostra o que deve vir depois de executar o script anterior. Quando lhe for pedido para continuar, introduza **Y**.

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

7. Acesso para os volumes montados foi confirmado.

    Para sair, introduza **p**e, em seguida, procure os volumes montados. Para criar uma lista dos volumes foi adicionados, uma linha de comandos, introduza **df -k**.

    ![O comando de -k df](./media/oracle-backup-recovery/recovery_service_16.png)

8. Utilize o seguinte script para copiar os ficheiros em falta para as pastas:

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
9. No seguinte script, utilize RMAN para recuperar a base de dados:

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

    No portal do Azure, sobre o **recuperação de ficheiros (pré-visualização)** painel, clique em **desmontar discos**.

    ![Desmonte o comando de discos](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Restaurar a VM completa

Em vez de restaurar os arquivos excluídos dos cofres de serviços de recuperação, pode restaurar a VM inteira.

### <a name="step-1-delete-myvm"></a>Passo 1: Eliminar myVM

*   No portal do Azure, vá para o **myVM1** cofre e, em seguida, selecione **eliminar**.

    ![Comando de eliminação do Cofre](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Passo 2: Recuperar VM

1.  Aceda a **cofres dos serviços de recuperação**e, em seguida, selecione **myVault**.

    ![entrada de myVault](./media/oracle-backup-recovery/recover_vm_02.png)

2.  Sobre o **descrição geral** painel, em **itens de cópia de segurança**, selecione o número de itens.

    ![myVault itens de cópia de segurança](./media/oracle-backup-recovery/recover_vm_03.png)

3.  Sobre o **itens de cópia de segurança (Máquina Virtual do Azure)** painel, selecione **myvm1**.

    ![Página de VM de recuperação](./media/oracle-backup-recovery/recover_vm_04.png)

4.  Sobre o **myvm1** painel, clique nas reticências (**...** ) botão e, em seguida, clique em **restaurar a VM**.

    ![Restaurar o comando VM](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Sobre o **selecionar ponto de restauro** painel, selecione o item que pretende restaurar e clique em **OK**.

    ![Selecione o ponto de restauro](./media/oracle-backup-recovery/recover_vm_06.png)

    Se ativou a cópia de segurança consistentes com aplicações, é apresentada uma barra vertical azul.

6.  Sobre o **configuração do restauro** painel, selecione o nome de máquina virtual, selecione o grupo de recursos e, em seguida, clique em **OK**.

    ![Restaura os valores de configuração](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Para restaurar a VM, clique nas **restaurar** botão.

8.  Para ver o estado do processo de restauro, clique em **trabalhos**e, em seguida, clique em **as tarefas de cópia de segurança**.

    ![Comando de estado de tarefas de cópia de segurança](./media/oracle-backup-recovery/recover_vm_08.png)

    A figura a seguir mostra o estado do processo de restauro:

    ![Estado do processo de restauro](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Passo 3: Definir o endereço IP público
Depois de restaurar a VM, configure o endereço IP público.

1.  Na caixa de pesquisa, introduza **endereço IP público**.

    ![Lista de endereços IP públicos](./media/oracle-backup-recovery/create_ip_00.png)

2.  Sobre o **endereços IP públicos** painel, clique em **Add**. Sobre o **Criar endereço IP público** painel, para **nome**, selecione o nome do IP público. Em **Grupo de recursos**, selecione **Utilizar existente**. Em seguida, clique em **Criar**.

    ![Criar endereço IP](./media/oracle-backup-recovery/create_ip_01.png)

3.  Para associar o endereço IP público a interface de rede para a VM, procure e selecione **myVMip**. Em seguida, clique em **associar**.

    ![Associar endereço IP](./media/oracle-backup-recovery/create_ip_02.png)

4.  Para **tipo de recurso**, selecione **interface de rede**. Selecione a interface de rede que é utilizada pela instância myVM e, em seguida, clique em **OK**.

    ![Selecione o tipo de recurso e os valores NIC](./media/oracle-backup-recovery/create_ip_03.png)

5.  Procurar e abrir a instância do myVM foi portado no portal. O endereço IP que está associado com a VM aparece no myVM **descrição geral** painel.

    ![Valor do endereço IP](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Passo 4: Ligar à VM

*   Para ligar à VM, utilize o seguinte script:

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Passo 5: Testar se a base de dados está acessível
*   Para testar a acessibilidade, utilize o seguinte script:

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Se a base de dados **inicialização** comando gera um erro, para recuperar a base de dados, consulte [passo 6: Utilize RMAN para recuperar a base de dados](#step-6-optional-use-rman-to-recover-the-database).

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Passo 6: (Opcional) Utilize RMAN para recuperar a base de dados
*   Para recuperar a base de dados, utilize o seguinte script:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

A cópia de segurança e recuperação da base de dados do Oracle Database 12C numa VM do Linux do Azure está agora concluído.

## <a name="delete-the-vm"></a>Elimine a VM

Quando já não precisar da VM, pode utilizar o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

[Tutorial: Criar VMs de elevada disponibilidade](../../linux/create-cli-complete.md)

[Explore exemplos de CLI do Azure de implementação de VM](../../linux/cli-samples.md)



