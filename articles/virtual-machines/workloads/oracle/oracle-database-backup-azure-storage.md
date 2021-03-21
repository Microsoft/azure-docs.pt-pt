---
title: Ressarça uma base de dados oracle Database 19c em um Azure Linux VM com RMAN e Azure Storage
description: Saiba como fazer o back up de uma base de dados Oracle Database 19c para o armazenamento em nuvem Azure.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: a6ce5446bd6470ef7a829925646d486801b28ebc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670018"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-storage"></a>Recue e recupere uma base de dados oracle Database 19c num VM Azure Linux usando o Armazenamento Azure

Este artigo demonstra o uso do Azure Storage como um meio de comunicação para apoiar e restaurar uma base de dados Oráculo em execução num Azure VM. Você irá fazer o back-up usando o armazenamento oracle RMAN para Azure File montado no VM usando o protocolo SMB. A utilização do armazenamento Azure para meios de segurança é extremamente rentável e performant. No entanto, para bases de dados muito grandes, o Azure Backup fornece uma solução melhor.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- Para realizar o processo de backup e recuperação, tem primeiro de criar um Linux VM que tenha uma instância instalada da Oracle Database 19c. A imagem do Marketplace atualmente utilizada para criar o VM é  **Oracle:oracle-database-19-3:oracle-database-19-0904:last**. Siga os passos no [Oráculo crie uma base de dados rápida](./oracle-database-quick-create.md) para criar uma base de dados Oracle para completar este tutorial.

## <a name="prepare-the-database-environment"></a>Preparar o ambiente da base de dados

1. Para criar uma sessão Secure Shell (SSH) com o VM, utilize o seguinte comando. Substitua o endereço IP e a combinação de nome de anfitrião pelo `publicIpAddress` valor do seu VM.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
2. Mude para o utilizador ***raiz:***
 
   ```bash
   sudo su -
   ```
    
3. Adicione o utilizador oráculo ao ficheiro ***/etc/sudoers:***

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

4. Este passo pressupõe que você tem uma instância Oráculo (teste) que está em execução em um VM chamado *vmoracle19c*.

   Mude o utilizador para o utilizador *oráculo:*

   ```bash
   sudo su - oracle
   ```
    
5. Antes de se ligar, tem de definir a variável ambiental ORACLE_SID:
    
    ```bash
    export ORACLE_SID=test;
    ```
   
   Deve também adicionar a variável ORACLE_SID ao ficheiro dos `oracle` utilizadores `.bashrc` para futuras insuposições utilizando o seguinte comando:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
6. Inicie o ouvinte do Oráculo se ainda não estiver a funcionar:
    
   ```bash
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

7.  Criar a localização da Área de Recuperação Rápida (FRA):

    ```bash
    mkdir /u02/fast_recovery_area
    ```

8.  Ligue-se à base de dados:

    ```bash
    sqlplus / as sysdba
    ```

9.  Inicie a base de dados se ainda não estiver em funcionamento:

    ```bash
    SQL> startup
    ```

10. Definir variáveis de ambiente de base de dados para área de recuperação rápida:

    ```bash
    SQL>  system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
11. Certifique-se de que a base de dados está no modo de registo de arquivo para ativar cópias de segurança online.
    Verifique primeiro o estado do arquivo de registo:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    E se no modo NOARCHIVELOG, executar os seguintes comandos em sqlplus:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

12. Criar uma tabela para testar as operações de backup e restauro:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

## <a name="back-up-to-azure-files"></a>Voltar para os Ficheiros Azure

Para fazer o back up até a Azure Files, complete estes passos:

1. Configurar o Armazenamento de Ficheiros Azure.
1. Monte a partilha do Ficheiro de Armazenamento Azure para o seu VM.
1. A fazer o reforço da base de dados.
1. Restaurar e recuperar a base de dados.

### <a name="set-up-azure-file-storage"></a>Configurar o armazenamento de ficheiros Azure

Neste passo, você irá fazer o back up da base de dados Oracle usando o Oracle Recovery Manager (RMAN) para o armazenamento de ficheiros Azure. As ações de ficheiros Azure são totalmente geridas por ações de ficheiros que vivem na nuvem. Podem ser acedidos utilizando o protocolo Bloco de Mensagens do Servidor (SMB) ou o protocolo Sistema de Ficheiros de Rede (NFS). Este passo abrange a criação de uma partilha de ficheiros que utiliza o protocolo SMB para montar no seu VM. Para obter informações sobre como montar utilizando o NFS, consulte [o armazenamento do Monte Blob utilizando o protocolo NFS 3.0](../../../storage/blobs/network-file-system-protocol-support-how-to.md).

Ao montar os Ficheiros Azure, utilizaremos `cache=none` o para desativar os dados de partilha de ficheiros. E para garantir que os ficheiros criados na partilha são propriedade do utilizador da Oracle definir as `uid=oracle` opções e `gid=oinstall` opções também. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Primeiro, crie a sua conta de armazenamento.

1. Configure o armazenamento de ficheiros no portal Azure

    No portal Azure, selecione ***+ Crie um recurso** _ e procure e selecione _ Conta de *_Armazenamento_**
    
    ![Screenshot que mostra onde criar um recurso e selecionar Conta de Armazenamento.](./media/oracle-backup-recovery/storage-1.png)
    
2. Na página de conta de armazenamento Create, escolha o seu grupo de recursos existente ***rg-oráculo** _, nomeie a sua conta de armazenamento _*_oracbkup1_*_ e escolha _*_Armazenamento V2 (generalpurpose v2)_*_ para o Tipo de Conta. Alterar a replicação para _*_armazenamento localmente redundante (LRS)_*_ e definir desempenho para _*_Standard_**. Certifique-se de que a Localização está definida para a mesma região que todos os seus outros recursos no grupo de recursos. 
    
    ![Screenshot que mostra onde escolher o seu grupo de recursos existente.](./media/oracle-backup-recovery/file-storage-1.png)
   
   
3. Clique no separador ***Advanced** _ e em Azure Files desconfiem _*_de grandes partilhas de ficheiros_*_ para _*_Ativado_**. Clique em 'Revisão + Criar' e, em seguida, clique em Criar.
    
    ![Screenshot que mostra onde definir grandes ações de ficheiros para Ativado.](./media/oracle-backup-recovery/file-storage-2.png)
    
    
4. Quando a conta de armazenamento tiver sido criada, vá ao recurso e escolha ***ações de arquivo***
    
    ![Screenshot que mostra onde selecionar partilhas de ficheiros.](./media/oracle-backup-recovery/file-storage-3.png)
    
5. Clique em ***+ Partilha de ficheiros** _ e no novo nome da lâmina _*_de partilha de ficheiros_*_ a sua partilha de _*_ficheiros orabkup1_*_. Descreva _*_quota_*_ para _*_10240_*_ GiB e verifique _*_a Transação otimizada_*_ como o nível. A quota reflete um limite superior a que a parte do ficheiro pode crescer. Como estamos a usar o armazenamento Standard, os recursos são PAYG e não são aprovisionados, pelo que defini-lo para 10 TiB não incorrerá em custos para além do que você usa. Se a sua estratégia de backup necessitar de mais armazenamento, tem de definir a quota para um nível adequado para manter todos os backups.   Quando tiver concluído a nova lâmina de partilha de ficheiros, clique em _*_Criar_**.
    
    ![Screenshot que mostra onde adicionar uma nova partilha de ficheiros.](./media/oracle-backup-recovery/file-storage-4.png)
    
    
6. Quando criado, clique em ***orabkup1*** na página de definições de partilha de ficheiros. 
    Clique no separador ***Connect** _ para abrir a lâmina 'Ligar' e, em seguida, clique no separador _ *_Linux*_* Copie os comandos fornecidos para montar a Partilha de Ficheiros utilizando o protocolo SMB. 
    
    ![Página de adicionar conta de armazenamento](./media/oracle-backup-recovery/file-storage-5.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para configurar a sua conta de armazenamento e partilha de ficheiros, execute os seguintes comandos no Azure CLI.

1. Crie a conta de armazenamento no mesmo grupo de recursos e localização que o seu VM:
   ```azurecli
   az storage account create -n orabackup1 -g rg-oracle -l eastus --sku Standard_LRS --enable-large-file-share
   ```

2. Criar ações de ficheiro na conta de armazenamento com uma quota de 10 TiB:

   ```azurecli
   az storage share create --account-name orabackup1 --name orabackup --quota 10240
   ```

3. Recupere a chave primária da conta de armazenamento (key1) necessária para a montagem da parte do ficheiro no seu VM:

   ```azurecli
   az storage account keys list --resource-group rg-oracle --account-name orabackup1
   ```

---

### <a name="mount-the-azure-storage-file-share-to-your-vm"></a>Monte a partilha de ficheiros Azure Storage para o seu VM

1. Criar o ponto de montagem:

   ```bash
   sudo mkdir /mnt/orabackup
   ```

2. Configurar credenciais:

   ```bash
   if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
   fi
   ```

   Substitua `<Your Storage Account Key1>` a chave da conta de armazenamento recuperada anteriormente, antes de executar o seguinte comando:
   ```bash
   if [ ! -f "/etc/smbcredentials/orabackup1.cred" ]; then
     sudo bash -c 'echo "username=orabackup1" >> /etc/smbcredentials/orabackup1.cred'
     sudo bash -c 'echo "password=<Your Storage Account Key1>" >> /etc/smbcredentials/orabackup1.cred'
   fi
   ```

3. Alterar permissões no ficheiro de credenciais:

   ```bash
   sudo chmod 600 /etc/smbcredentials/orabackup1.cred
   ```

4. Adicione o suporte ao /etc/fstab:

   ```bash
   sudo bash -c 'echo "//orabackup1.file.core.windows.net/orabackup /mnt/orabackup cifs nofail,vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall" >> /etc/fstab'
   ```

5. Executar os comandos para montar o armazenamento de Ficheiros Azure utilizando o protocolo SMB:

   ```bash
   sudo mount -t cifs //orabackup1.file.core.windows.net/orabackup /mnt/orabackup -o vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall
   ```

   Se receber um erro semelhante a:

   ```output
   mount: wrong fs type, bad option, bad superblock on //orabackup1.file.core.windows.net/orabackup 
   ```

   em seguida, o pacote CIFS não pode ser instalado no seu anfitrião Linux. 
   
   Para verificar se o CIS está instalado, execute o seguinte comando:

   ```bash
   sudo rpm -qa|grep cifs-utils
   ```

   Se o comando não retornar nenhuma saída, instale a embalagem CIFS utilizando o seguinte comando:

   ```bash 
   sudo yum install cifs-utils
   ```

   Agora reexamque o comando de montagem acima para montar o armazenamento de Ficheiros Azure.

6. Verifique se a partilha de ficheiros está montada corretamente com o seguinte comando:

   ```bash
   df -h
   ```

   O resultado deverá ter um aspeto semelhante a este:

   ```output
   $ df -h
   Filesystem                                    Size  Used Avail Use% Mounted on
   devtmpfs                                      3.3G     0  3.3G   0% /dev
   tmpfs                                         3.3G     0  3.3G   0% /dev/shm
   tmpfs                                         3.3G   17M  3.3G   1% /run
   tmpfs                                         3.3G     0  3.3G   0% /sys/fs/cgroup
   /dev/sda2                                      30G  9.1G   19G  34% /
   /dev/sdc1                                      59G  2.7G   53G   5% /u02
   /dev/sda1                                     497M  199M  298M  41% /boot
   /dev/sda15                                    495M  9.7M  486M   2% /boot/efi
   tmpfs                                         671M     0  671M   0% /run/user/54321
   /dev/sdb1                                      14G  2.1G   11G  16% /mnt/resource
   tmpfs                                         671M     0  671M   0% /run/user/54322
   //orabackup1.file.core.windows.net/orabackup   10T     0   10T   0% /mnt/orabackup
   ```

### <a name="back-up-the-database"></a>Fazer o back-up da base de dados

Nesta secção, vamos usar o Oracle Recovery Manager (RMAN) para fazer uma cópia de segurança completa da base de dados e registos de arquivo e escrever a cópia de segurança como um conjunto de backup para a partilha do Ficheiro Azure montada anteriormente. 

1. Configure a RMAN para voltar ao ponto de montagem dos Ficheiros Azure:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/mnt/orabkup/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s'; 
    ```

2. Como as ações de ficheiro padrão Azure têm um tamanho máximo de ficheiro de 1 TiB, vamos limitar o tamanho das peças de reserva RMAN a 1 TiB. (Note que as Ações de Ficheiro Premium têm um limite máximo de tamanho de ficheiro de 4 TiB. Para obter mais informações, consulte [Azure Files Escalaability and Performance Targets](../../../storage/files/storage-files-scale-targets.md).)

    ```bash
    RMAN> configure channel device type disk maxpiecesize 1000G;
    ```

3. Confirme os detalhes da alteração de configuração:

    ```bash
    RMAN> show all;
    ```

4. Agora, faça o backup. O seguinte comando irá ter uma cópia de segurança completa da base de dados, incluindo ficheiros de arquivo, como um conjunto de backup em formato comprimido:   

    ```bash
    RMAN> backup as compressed backupset database plus archivelog;
    ```

Já tem o backup da base de dados on-line usando o Oracle RMAN, com a cópia de segurança localizada no armazenamento de ficheiros Azure. Este método tem a vantagem de utilizar as funcionalidades de RMAN enquanto armazena cópias de segurança no armazenamento de ficheiros Azure, onde podem ser acedidos a partir de outros VMs, úteis se precisar de clonar a base de dados.  
    
Embora a utilização do armazenamento de ficheiros RMAN e Azure para a cópia de segurança da base de dados tenha muitas vantagens, o tempo de backup e restauro está ligado ao tamanho da base de dados, pelo que para bases de dados muito grandes pode levar um tempo considerável para estas operações.  Um mecanismo alternativo de backup, utilizando backups VM consistentes com aplicações Azure Backup, utiliza tecnologia snapshot para realizar backups, que tem a vantagem de backups muito rápidos independentemente do tamanho da base de dados. Integração com Serviços de Recuperação O cofre fornece armazenamento seguro das suas cópias de segurança da Oracle Database em armazenamento em nuvem Azure acessível a partir de outras regiões de VMs e Azure. 

### <a name="restore-and-recover-the-database"></a>Restaurar e recuperar a base de dados

1.  Desligue a instância do Oráculo:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Remova os ficheiros de dados e cópias de segurança:

    ```bash
    cd /u02/oradata/TEST
    rm -f *.dbf
    ```

3. Os seguintes comandos utilizam o RMAN para restaurar os ficheiros de dados em falta e recuperar a base de dados:

    ```bash
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
4. Verifique se o conteúdo da base de dados foi totalmente recuperado:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```


A cópia de segurança e recuperação da base de dados 19c da Oracle Database num VM Azure Linux está agora concluída.

## <a name="delete-the-vm"></a>Elimine a VM

Quando já não precisar do VM, pode utilizar o seguinte comando para remover o grupo de recursos, o VM e todos os recursos relacionados:

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Criar VMs altamente disponíveis](../../linux/create-cli-complete.md)

[Explore amostras de CLI de implantação VM](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)