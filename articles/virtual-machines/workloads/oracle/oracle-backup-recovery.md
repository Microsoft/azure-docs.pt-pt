---
title: Volte atrás e recupere uma base de dados oracle Database 12c numa máquina virtual Azure Linux Microsoft Docs
description: Saiba como fazer o back up e recuperar uma base de dados Oracle Database 12c no seu ambiente Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rgardler
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogardle
ms.openlocfilehash: 7f6b9ddfd5b25ea8482e0bbf5622fa16bc450662
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525607"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Recue e recupere uma base de dados de 12c da Oracle Database numa máquina virtual Azure Linux

Você pode usar O CLI Azure para criar e gerir recursos Azure em uma origem de comando, ou usar scripts. Neste artigo, usamos scripts Azure CLI para implementar uma base de dados Oracle Database 12c a partir de uma imagem da galeria Azure Marketplace.

Antes de começar, certifique-se de que o Azure CLI está instalado. Para mais informações, consulte o guia de instalação do [Azure CLI.](/cli/azure/install-azure-cli)

## <a name="prepare-the-environment"></a>Preparar o ambiente

### <a name="step-1-prerequisites"></a>Passo 1: Pré-requisitos

*   Para realizar o processo de backup e recuperação, tem primeiro de criar um Linux VM que tenha uma instância instalada da Oracle Database 12c. A imagem do Marketplace que utiliza para criar o VM *chama-se Oracle:Oracle-Database-Ee:12.1.0.2:last*.

    Para aprender a criar uma base de dados Oracle, consulte o [Oracle criar uma base de dados rápida.](./oracle-database-quick-create.md)


### <a name="step-2-connect-to-the-vm"></a>Passo 2: Ligar ao VM

*   Para criar uma sessão Secure Shell (SSH) com o VM, utilize o seguinte comando. Substitua o endereço IP e a combinação de nome de anfitrião pelo `publicIpAddress` valor do seu VM.

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Passo 3: Preparar a base de dados

1.  Este passo pressupõe que você tem um exemplo de Oráculo (cdb1) que está em execução em um VM chamado *myVM*.

    Executar a raiz do super-alimentador *oráculo* e, em seguida, inicializar o ouvinte:

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

2.  (Opcional) Certifique-se de que a base de dados está no modo de registo de arquivo:

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

3.  (Opcional) Criar uma tabela para testar o compromisso:

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

4.  Verifique ou altere a localização e o tamanho do ficheiro de cópia de segurança:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```

5. Utilize o Gestor de Recuperação da Oracle (RMAN) para fazer o back-up da base de dados:

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Passo 4: Backup consistente de aplicação para Os VMs Linux

As cópias de segurança consistentes com aplicações são uma nova funcionalidade no Azure Backup. Pode criar e selecionar scripts para executar antes e depois do instantâneo VM (pré-instantâneo e pós-instantâneo).

1. Descarregue o ficheiro JSON.

    Faça o download VMSnapshotScriptPluginConfig.jsa partir de https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig . O conteúdo do ficheiro é semelhante ao seguinte:

    ```output
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

2. Criar a pasta /etc/azure no VM:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Copie o ficheiro JSON.

    Copie VMSnapshotScriptPluginConfig.jsna pasta /etc/azul.

4. Edite o ficheiro JSON.

    Edite o VMSnapshotScriptPluginConfig.jsno ficheiro para incluir os `PreScriptLocation` parâmetros e `PostScriptlocation` parâmetros. Por exemplo:

    ```output
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

5. Crie os ficheiros de script pré-instantâneo e pós-instantâneo.

    Aqui está um exemplo de scripts pré-instantâneos e pós-instantâneos para uma "cópia de segurança fria" (uma cópia de segurança offline, com encerramento e reinício):

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

    Aqui está um exemplo de scripts pré-instantâneos e pós-instantâneos para uma "cópia de segurança quente" (uma cópia de segurança online):

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

    Para /etc/azure/pre_script.sql, modificar o conteúdo do ficheiro de acordo com os seus requisitos:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    Para /etc/azure/post_script.sql, modificar o conteúdo do ficheiro de acordo com os seus requisitos:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Alterar permissões de ficheiros:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Teste os guiões.

    Para testar os scripts, primeiro, inscreva-se como raiz. Em seguida, certifique-se de que não há erros:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Para obter mais informações, consulte [a cópia de segurança consistente da aplicação para Os VMs Linux](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Passo 5: Use cofres dos Serviços de Recuperação Azure para apoiar o VM

1.  No portal Azure, procure **cofres dos Serviços de Recuperação.**

    ![Página de cofres de serviços de recuperação](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Na lâmina dos cofres dos **Serviços de Recuperação,** para adicionar um novo cofre, clique em **Adicionar**.

    ![Cofres de serviços de recuperação adicionam página](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Para continuar, clique **no myVault**.

    ![Página de detalhes de cofres de serviços de recuperação](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Na **lâmina myVault,** clique **em Backup**.

    ![Página de backup de cofres de serviços de recuperação](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Na lâmina **Backup Goal,** utilize os valores predefinidos da **máquina** **Azure** e Virtual . Clique em **OK**.

    ![Página de detalhes de cofres de serviços de recuperação](./media/oracle-backup-recovery/recovery_service_05.png)

6.  Para **a política de backup,** utilize a Política **Padrão**ou selecione **Criar Nova política**. Clique em **OK**.

    ![Página de detalhes de política de backup de cofres de serviços de recuperação](./media/oracle-backup-recovery/recovery_service_06.png)

7.  Na lâmina **de máquinas virtuais Select,** selecione a caixa de verificação **myVM1** e, em seguida, clique em **OK**. Clique no botão **de backup Ativa.**

    ![Serviços de Recuperação abóbadas itens para a página de detalhes de backup](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Depois de clicar **em Ativar a cópia de segurança,** o processo de backup não começa até que a hora programada expire. Para criar um reforço imediato, complete o próximo passo.

8.  No **myVault - Lâmina de itens de reserva,** em **CONTAGEM DE PONTOS DE CÓPIA,** selecione a contagem de artigos de reserva.

    ![Serviços de Recuperação abóbadas myVault details page](./media/oracle-backup-recovery/recovery_service_08.png)

9.  Na lâmina **de Itens de Reserva (Azure Virtual Machine),** no lado direito da página, clique no botão elipse **(...**) e, em seguida, clique em **Backup agora**.

    ![Serviços de Recuperação cofres Backup agora comando](./media/oracle-backup-recovery/recovery_service_09.png)

10. Clique no botão **De reserva.** Aguarde que o processo de reserva termine. Em seguida, vá ao [passo 6: Remova os ficheiros da base de dados](#step-6-remove-the-database-files).

    Para ver o estado do trabalho de backup, clique em **Jobs**.

    ![Página de trabalho de cofres de serviços de recuperação](./media/oracle-backup-recovery/recovery_service_10.png)

    O estado do trabalho de reserva aparece na seguinte imagem:

    ![Página de trabalho de cofres de serviços de recuperação com estado](./media/oracle-backup-recovery/recovery_service_11.png)

11. Para uma cópia de segurança consistente com a aplicação, aborde quaisquer erros no ficheiro de registo. O ficheiro de registo está localizado em /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Passo 6: Remover os ficheiros da base de dados 
Mais tarde neste artigo, você vai aprender a testar o processo de recuperação. Antes de poder testar o processo de recuperação, tem de remover os ficheiros da base de dados.

1.  Remova o espaço de mesa e os ficheiros de backup:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  (Opcional) Desligue a instância do Oráculo:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Restaurar os ficheiros apagados dos cofres dos Serviços de Recuperação
Para restaurar os ficheiros apagados, complete os seguintes passos:

1. No portal Azure, procure o item dos cofres dos Serviços de Recuperação *myVault.* Na lâmina **'Vista Geral',** em **itens de cópia de segurança,** selecione o número de itens.

    ![Serviços de Recuperação abóbadas myVault itens de backup](./media/oracle-backup-recovery/recovery_service_12.png)

2. Em **CONTAGEM DE ITEM DE CÓPIA,** selecione o número de itens.

    ![Serviços de Recuperação abóbadas Azure Virtual Machine contando item de backup](./media/oracle-backup-recovery/recovery_service_13.png)

3. Na lâmina **myvm1,** clique em **Recuperação de Ficheiros (Pré-visualização)**.

    ![Screenshot da página de recuperação de ficheiros de cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery_service_14.png)

4. No painel **de recuperação de ficheiros (pré-visualização),** clique em **Baixar Script**. Em seguida, guarde o ficheiro de descarregamento (.sh) para uma pasta no computador cliente.

    ![Baixar ficheiro de script economiza opções](./media/oracle-backup-recovery/recovery_service_15.png)

5. Copie o ficheiro .sh para o VM.

    O exemplo a seguir mostra como utilizar um comando de cópia segura (scp) para mover o ficheiro para o VM. Também pode copiar o conteúdo para a área de transferência e, em seguida, colar o conteúdo num novo ficheiro que é configurado no VM.

    > [!IMPORTANT]
    > No exemplo seguinte, certifique-se de atualizar os valores do endereço IP e da pasta. Os valores devem mapear para a pasta onde o ficheiro é guardado.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```

6. Mude o ficheiro, para que seja propriedade da raiz.

    No exemplo seguinte, altere o ficheiro para que seja propriedade da raiz. Então, mude as permissões.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```

    O exemplo que se segue mostra o que deve ver depois de executar o script anterior. Quando te pedirem para continuar, entra **em Y.**

    ```output
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

7. Confirma-se o acesso aos volumes montados.

    Para sair, insira **q**e, em seguida, procure os volumes montados. Para criar uma lista dos volumes adicionados, num pedido de comando, insira **df-k**.

    ![O comando df-k](./media/oracle-backup-recovery/recovery_service_16.png)

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

9. No seguinte script, utilize o RMAN para recuperar a base de dados:

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

    No portal Azure, na lâmina de recuperação de **ficheiros (pré-visualização),** clique em **Discos Desmonte**.

    ![Desmonte o comando dos discos](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Restaurar todo o VM

Em vez de restaurar os ficheiros apagados dos cofres dos Serviços de Recuperação, pode restaurar todo o VM.

### <a name="step-1-delete-myvm"></a>Passo 1: Apagar o myVM

*   No portal Azure, vá ao cofre **myVM1** e, em seguida, **selecione Delete**.

    ![Comando de eliminação de cofre](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Passo 2: Recuperar o VM

1.  Vá aos **cofres dos Serviços de Recuperação**e, em seguida, selecione **myVault**.

    ![entrada myVault](./media/oracle-backup-recovery/recover_vm_02.png)

2.  Na lâmina **'Vista Geral',** em **itens de cópia de segurança,** selecione o número de itens.

    ![myVault voltar a subir itens](./media/oracle-backup-recovery/recover_vm_03.png)

3.  Na **lâmina de itens de reserva (Azure Virtual Machine),** selecione **myvm1**.

    ![Página VM de recuperação](./media/oracle-backup-recovery/recover_vm_04.png)

4.  Na lâmina **myvm1,** clique no botão elipse **(...**) e, em seguida, clique em **Restaurar VM**.

    ![Restaurar o comando VM](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Na lâmina **de ponto de restauro Selecione,** selecione o item que pretende restaurar e, em seguida, clique em **OK**.

    ![Selecione o ponto de restauro](./media/oracle-backup-recovery/recover_vm_06.png)

    Se tiver ativado uma cópia de segurança consistente com a aplicação, aparece uma barra azul vertical.

6.  Na lâmina **de configuração 'Restaurar',** selecione o nome da máquina virtual, selecione o grupo de recursos e, em seguida, clique em **OK**.

    ![Restaurar valores de configuração](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Para restaurar o VM, clique no botão **Restaurar.**

8.  Para ver o estado do processo de restauro, clique em **Jobs**e, em seguida, clique em **Backup Jobs**.

    ![Comando de estado de trabalhos de reserva](./media/oracle-backup-recovery/recover_vm_08.png)

    A seguinte figura mostra o estado do processo de restauro:

    ![Estado do processo de restauro](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Passo 3: Definir o endereço IP público
Após a restauração do VM, crie o endereço IP público.

1.  Na caixa de pesquisa, insira **o endereço IP público.**

    ![Lista de endereços IP públicos](./media/oracle-backup-recovery/create_ip_00.png)

2.  Na lâmina **dos endereços IP públicos,** clique em **Adicionar**. Na lâmina **do endereço IP público Create,** para **nome,** selecione o nome IP público. Em **Grupo de recursos**, selecione **Utilizar existente**. Em seguida, clique em **Criar**.

    ![Criar endereço IP](./media/oracle-backup-recovery/create_ip_01.png)

3.  Para associar o endereço IP público à interface de rede para o VM, procure e selecione **myVMip**. Em seguida, clique **em Associate**.

    ![Endereço IP associado](./media/oracle-backup-recovery/create_ip_02.png)

4.  Para **o tipo de recurso,** selecione interface de **rede.** Selecione a interface de rede que é usada pela instância myVM e, em seguida, clique em **OK**.

    ![Selecione o tipo de recurso e os valores de NIC](./media/oracle-backup-recovery/create_ip_03.png)

5.  Procure e abra a instância do myVM que é a partir do portal. O endereço IP associado ao VM aparece na lâmina de **visão geral** myVM.

    ![Valor do endereço IP](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Passo 4: Ligar ao VM

*   Para ligar ao VM, utilize o seguinte script:

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
    > Se o comando **de arranque** da base de dados gerar um erro, para recuperar a base de dados, consulte o [passo 6: Utilize o RMAN para recuperar a base de dados](#step-6-optional-use-rman-to-recover-the-database).

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Passo 6: (Opcional) Utilize o RMAN para recuperar a base de dados
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

A cópia de segurança e recuperação da base de dados 12c da Oracle Database num VM Azure Linux está agora concluída.

## <a name="delete-the-vm"></a>Elimine a VM

Quando já não precisar do VM, pode utilizar o seguinte comando para remover o grupo de recursos, o VM e todos os recursos relacionados:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximos passos

[Tutorial: Criar VMs altamente disponíveis](../../linux/create-cli-complete.md)

[Explore amostras de CLI de implantação VM](../../linux/cli-samples.md)
