---
title: Fazer o back up do servidor MABS
description: Saiba como fazer backup do Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: de62f0f57273ad7bd77df917d909627819165adb
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946829"
---
# <a name="back-up-the-mabs-server"></a>Fazer o back up do servidor MABS

Para garantir que os dados podem ser recuperados se o Microsoft Azure Backup Server (MABS) falhar, precisará de uma estratégia para fazer o backup do servidor MABS. Se não estiver apoiado, terá de o reconstruir manualmente depois de uma falha, e os pontos de recuperação baseados em disco não serão recuperáveis. Pode fazer backup dos servidores MABS através da base de dados MABS.

## <a name="back-up-the-mabs-database"></a>Ressarce na base de dados do MABS

Como parte da sua estratégia de backup MABS, terá de fazer backup na base de dados do MABS. A base de dados MABS chama-se DPMDB. Esta base de dados contém a configuração do MABS juntamente com dados sobre cópias de segurança do MABS. Se houver um desastre, pode reconstruir a maior parte da funcionalidade de um servidor MABS utilizando uma cópia de segurança recente da base de dados. Assumindo que pode restaurar a base de dados, as cópias de segurança baseadas em fita estão acessíveis e mantêm todas as definições de grupos de proteção e horários de backup. Se os discos de armazenamento MABS não foram afetados pela paragem, as cópias de segurança baseadas em disco também são utilizáveis após uma reconstrução. Existem vários métodos diferentes para fazer uma cópia de segurança da base de dados.

|Método de cópia de segurança da base de dados|Vantagens|Desvantagens|
|--------------------------|--------------|-----------------|
|[Criar uma cópia de segurança no Azure](#back-up-to-azure)|<li>Facilmente configurado e monitorizado em MABS.<li>Múltiplas localizações dos ficheiros da cópia de segurança da base de dados.<li>O armazenamento em nuvem oferece uma solução robusta para a recuperação após desastre.<li>Armazenamento muito seguro da base de dados.<li>Suporta até 120 pontos de recuperação online.|<li>Requer conta Azure e configuração MABS adicional. Implica alguns custos de armazenamento do Azure.<li> Requer uma versão suportada do sistema baseado no Windows Server com o agente Azure para ter acesso às cópias de segurança MABS armazenadas no cofre de backup do Azure. Isto não pode ser outro servidor MABS.<li>Não é uma opção válida se a base de dados estiver alojada localmente e o utilizador pretender ativar a proteção secundária. <li>É necessária alguma preparação adicional e tempo de recuperação.|
|[Fazer o backup da base de dados através do backup do pool de armazenamento MABS](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>Simples de configurar e monitorizar.<li>A cópia de segurança é mantida nos discos da piscina de armazenamento MABS e é fácil de aceder localmente.<li>As cópias de segurança programadas do MABS suportam 512 backups expressos completos. Se recuar de hora em hora, terá 21 dias de proteção total.|<li>Não é uma boa opção para a recuperação após desastre. Está online e a recuperação pode não funcionar como esperado se o servidor MABS ou o disco de armazenamento falhar.<li>Não é uma opção válida se a base de dados estiver alojada localmente e o utilizador pretender ativar a proteção secundária. <li>Algumas preparações e passos especiais são necessários para ter acesso aos pontos de recuperação se o serviço ou consola MABS não estiver em funcionamento ou a funcionar.|
|[Criar uma cópia de segurança com a cópia de segurança nativa do SQL Server num disco local](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>Incorporação no SQL Server.<li>A cópia de segurança é mantida num disco local que é facilmente acessível.<li>Pode ser agendada para ser executada o número de vezes que for necessário.<li>Totalmente independente do MABS.<li>Pode agendar uma limpeza dos ficheiros das cópias de segurança.|<li>Não é uma boa opção para a recuperação após desastre, exceto se as cópias de segurança forem copiadas para uma localização remota.<li>Requer armazenamento local para cópias de segurança, o que pode limitar a retenção e frequência.|
|[Apoie-se com backup nativo sql e proteção MABS para uma parte protegida pelo MABS](#back-up-to-a-share-protected-by-mabs)|<li>Facilmente monitorizado em MABS.<li>Múltiplas localizações dos ficheiros da cópia de segurança da base de dados.<li>Fácil de aceder a partir de qualquer computador do Windows na rede.<li>É possivelmente o método de recuperação mais rápido.|<li>Só suporta 64 pontos de recuperação.<li>Não é uma boa opção para a recuperação após desastre no local. O servidor MABS ou a falha do disco de armazenamento MABS podem dificultar os esforços de recuperação.<li>Não é uma opção se o MABS DB estiver alojado localmente e pretender uma proteção secundária. <li>É necessária alguma preparação adicional para o configurar e testar.<li>É necessário algum tempo extra de preparação e recuperação caso o próprio servidor MABS esteja em baixo, mas os discos de armazenamento MABS estão bem.|

- Se fizer o seu apoio utilizando um grupo de proteção MABS, recomendamos que utilize um grupo de proteção único para a base de dados.

    > [!NOTE]
    > Para fins de restauro, a instalação MABS que pretende restaurar com a base de dados MABS deve corresponder à versão da própria base de dados MABS.  Por exemplo, se a base de dados que pretende recuperar for de um MABS V3 com instalação Update Rollup 1, o servidor MABS deve estar a executar a mesma versão com o Update Rollup 1. Isto significa que poderá ter de desinstalar e reinstalar o MABS com uma versão compatível antes de restaurar a base de dados.  Para ver a versão da base de dados, poderá ter de montá-la manualmente num nome de base de dados temporário e, em seguida, executar uma consulta SQL na base de dados para verificar o último rollup instalado com base na versão principal e secundária.

- Para verificar a versão da base de dados MABS, siga estes passos:

    1. Para executar a consulta, abra o SQL Management Studio e, em seguida, ligue-se à instância SQL que está a executar a base de dados MABS.

    2. Selecione a base de dados MABS e, em seguida, inicie uma nova consulta.

    3. Cole a seguinte consulta SQL no painel de consulta e execute-a:

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    Se nada for devolvido nos resultados da consulta, ou se o servidor MABS tiver sido atualizado a partir de versões anteriores, mas não foi instalado nenhum novo rollup de atualização desde então, não haverá entrada para o maior, menor para uma instalação base de MABS. Para verificar as versões MABS associadas aos rollups de atualização ver [Lista de Números de Construção para MABS](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx).

### <a name="back-up-to-azure"></a>Criar uma cópia de segurança no Azure

1. Antes de começar, terá de executar um script para recuperar o caminho do ponto de montagem de volume de réplica MABS para que saiba qual o ponto de recuperação que contém a cópia de segurança MABS. Faça-o após a replicação inicial com a Cópia de Segurança do Azure. No script, `dplsqlservername%` substitua-o pelo nome da instância SQL Server que hospeda a base de dados MABS.

    ```SQL
    Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%dpmdb%'
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
    ```

    Certifique-se de que tem a senha que foi especificada quando o Agente de Serviços de Recuperação do Azure foi instalado e o servidor MABS foi registado no cofre de backup Azure. Precisará deste código de acesso para restaurar a cópia de segurança.

2. Crie um cofre da Cópia de Segurança do Azure e transfira o ficheiro de instalação do Agente de Cópia de Segurança do Azure e as credenciais do cofre. Executar o ficheiro de instalação para instalar o agente no servidor MABS e utilizar as credenciais de abóbada para registar o servidor MABS no cofre. [Saiba mais](backup-azure-microsoft-azure-backup.md).

3. Depois de configurar o cofre, crie um grupo de proteção MABS que contenha a base de dados MABS. Selecione para o fazer até ao disco e ao Azure.

#### <a name="recover-the-mabs-database-from-azure"></a>Recuperar a base de dados MABS do Azure

Pode recuperar a base de dados do Azure utilizando qualquer servidor MABS registado no cofre de backup Azure, da seguinte forma:

1. Na consola MABS, selecione **Recovery**  >  **Add External MABS**.

2. Forneça as credenciais do cofre (descarregue a partir do cofre Azure Backup). Tenha em atenção que as credenciais apenas são válidas durante dois dias.

3. Em **Select External MABS for Recovery**, selecione o servidor MABS para o qual pretende recuperar a base de dados, digite a palavra-passe de encriptação e selecione **OK.**

4. Selecione o ponto de recuperação que pretende utilizar na lista de pontos de disponíveis. Selecione **Clear External MABS** para voltar à vista MABS local.

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>Ressarce a base de dados MABS para o pool de armazenamento MABS

> [!NOTE]  
> Esta opção é aplicável para MABS com armazenamento de backup moderno.

1. Na consola MABS, selecione **Protection**  >  **Create protection group**.
2. Na página **Selecionar Tipo de Grupo de Proteção** , selecione **Servidores**.
3. Na página **de membros** do grupo Select, selecione base de **dados DPM**. Expanda o servidor MABS e selecione DPMDB.
4. Na página 'Selecionar método **de proteção de dados',** selecione **I want I want a curto prazo protection using disk**. Especifique as opções de política de proteção a curto prazo.
5. Após a replicação inicial da base de dados MABS, execute o seguinte script SQL:

```SQL
select AG.NetbiosName, DS.DatasourceName, V.AccessPath, LR.PhysicalReplicaId from tbl_IM_DataSource DS
join tbl_PRM_LogicalReplica as LR
on DS.DataSourceId = LR.DataSourceId
join tbl_AM_Server as AG
on DS.ServerId=AG.ServerId
join tbl_PRM_ReplicaVolume RV
on RV.ReplicaId = LR.PhysicalReplicaId
join tbl_STM_Volume V
on RV.StorageId = V.StorageId
where datasourcename like N'%dpmdb%' and ds.ProtectedGroupId is not null
and LR.Validity in (1,2)
and AG.ServerName like N'%<dpmsqlservername>%' -- <dpmsqlservername> is a placeholder, put netbios name of server hosting DPMDB
```

### <a name="recover-mabs-database"></a>Recuperar base de dados MABS

Para reconstruir o seu MABS com o mesmo DB, é necessário primeiro recuperar a base de dados MABS e sincronizá-la com o MABS recentemente instalado.

#### <a name="use-the-following-steps"></a>Use os seguintes passos

1. Abra uma pronta de comando administrativo e corra `psexec.exe -s powershell.exe` para iniciar uma janela PowerShell no contexto do sistema.
2. Decida a localização de onde pretende recuperar a base de dados:

#### <a name="to-copy-the-database-from-the-last-backup"></a>Para copiar a base de dados a partir da última cópia de segurança

1. Navegue para replicar o caminho VHD `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>`
2. Monte o **disco0.vhdx** presente nele com `mount-vhd disk0.vhdx` o comando.
3. Uma vez montada a réplica VHD, utilize `mountvol.exe` para atribuir uma letra de unidade ao volume de réplica utilizando o ID de réplica física a partir da saída do script SQL. Por exemplo: `mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>Para copiar a base de dados de um ponto de recuperação anterior

1. Navegue para o diretório de contentores DPMDB  `\<MABSServer FQDN\>\<PhysicalReplicaId\>` . Você verá vários diretórios com alguns identificadores GUID únicos sob ele correspondentes pontos de recuperação tomados para o MABS DB. Outros diretórios representam um ponto pit/ponto de recuperação.
2. Navegue para qualquer caminho PIT vhd, por exemplo, `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>` e monte o **disco0.vhdx** presente nele usando o `mount-vhd disk0.vhdx` comando.
3. Uma vez montada a réplica VHD, utilize `mountvol.exe` para atribuir uma letra de unidade ao volume de réplica, utilizando o ID de réplica física a partir da saída do script SQL. Por exemplo: `mountvol X: \?\Volume{}\`

   Todos os termos que aparecem com aparelhos angulares nos degraus acima são suportes de lugar. Substitua-as por valores adequados da seguinte forma:
   - **ReFSVolume** - Caminho de acesso a partir da saída do script SQL
   - **MABSServer FQDN** - Nome totalmente qualificado do servidor MABS
   - **PhysicalReplicaId** - ID de réplica física do script SQL para fora
   - **PITId** - identificador GUID que não seja a réplica física ID no diretório do contentor.
4. Abra outra solicitação de comando administrativo e corra `psexec.exe -s cmd.exe` para iniciar uma solicitação de comando no contexto do sistema.
5. Altere o diretório para o X: dirija e navegue até à localização dos ficheiros de base de dados MABS.
6. Copie-os para uma localização a partir da qual seja fácil restaurar. Saia da janela do CMD psexec depois de copiar.
7. Vá até à janela powerShell psexec aberta no passo 1, navegue até ao caminho VHDX e desmonte o VHDX utilizando o comando `dismount-vhd disk0.vhdx` .
8. Depois de reinstalar o servidor MABS, pode utilizar o DPMDB restaurado para se ligar ao servidor MABS executando o `DPMSYNC-RESTOREDB` comando.
9. Corra `DPMSYNC-SYNC` uma vez que esteja `DPMSYNC-RESTOREDB` completo.

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>Fazer o backup da base de dados através do backup do pool de armazenamento MABS

> [!NOTE]
> Esta opção é aplicável para MABS com armazenamento legado.

Antes de começar, terá de executar um script para recuperar o caminho do ponto de montagem de volume de réplica MABS para que saiba qual o ponto de recuperação que contém a cópia de segurança MABS. Faça-o após a replicação inicial com a Cópia de Segurança do Azure. No script, `dplsqlservername%` substitua-o pelo nome da instância SQL Server que hospeda a base de dados MABS.

```SQL
Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
from tbl_IM_DataSource as ds
join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
and vol.Usage =1
and lr.Validity in (1,2)
where ds.datasourcename like '%dpmdb%'
and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
```

1. Na consola MABS, selecione **Protection**  >  **Create protection group**.

2. Na página **'Tipo de Grupo de Proteção de Protecção' selecione**  **Servidores**.

3. Na página de membros do **grupo Select,** selecione a base de dados MABS. Expanda o item do servidor MABS e selecione **DPMDB**.

4. Na página 'Selecionar método  **de proteção de dados',** selecione **I want I want a curto prazo protection using disk**. Especifique as opções de política de proteção a curto prazo. Recomendamos um intervalo de retenção de duas semanas para bases de dados MABS.

#### <a name="recover-the-database"></a>Recuperar a base de dados

Se o servidor MABS ainda estiver operacional e o pool de armazenamento estiver intacto (por exemplo, problemas com o serviço MABS ou consola), então copie a base de dados a partir do volume de réplica ou de uma cópia-sombra da seguinte forma:

1. Decida a partir de quando pretende recuperar a base de dados.

    - Se pretender copiar a base de dados da última cópia de segurança tirada diretamente do volume de réplica MABS, utilize **mountvol.exe** para atribuir uma letra de unidade ao volume de réplica utilizando o GUID a partir da saída do script SQL. Por exemplo: `C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - Se pretender copiar a base de dados a partir de um ponto de recuperação anterior (cópia-sombra), então tem de listar todas as cópias-sombra para a réplica utilizando o volume GUID a partir da saída do script SQL. Este comando lista cópias-sombra para este volume: `C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` . Note o tempo de criação e o ID da cópia-sombra de que quer recuperar.

2. Em seguida, utilize **diskshadow.exe** para montar a cópia de sombra numa letra de unidade X não utilizada: utilizando o ID da cópia-sombra, para que possa copiar os ficheiros da base de dados.

3. Abra um pedido de comando administrativo e corra `psexec.exe -s cmd.exe` para iniciar uma solicitação de comando em contexto de sistema, para que tenha permissão para navegar para o volume de réplica (X:) e copiar os ficheiros.

4. CD para o X: dirija e navegue até à localização dos ficheiros de base de dados MABS. Copie-os para uma localização a partir da qual seja fácil restaurar. Após a cópia estar completa, existe a janela psexec cmd, e executar **diskshadow.exe** e não expor o X: volume.

5. Agora pode restaurar os ficheiros da base de dados utilizando o SQL Management Studio ou executando **o DPMSYNC \- RESTOREDB**.

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>Criar uma cópia de segurança com a cópia de segurança nativa do SQL Server num disco local

Pode fazer backup da base de dados MABS para um disco local com cópia de segurança nativa do SQL Server, independente do MABS.

- Obtenha uma [descrição geral](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) da cópia de segurança do SQL Server.

- [Saiba mais](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) sobre criar uma cópia de segurança do SQL Server na nuvem.

## <a name="back-up-to-a-share-protected-by-mabs"></a>Voltar a uma parte protegida pelo MABS

Esta opção de backup utiliza SQL nativo para fazer backup da base de dados MABS para uma partilha, protege a partilha com o MABS e utiliza versões anteriores do Windows VSS para facilitar a restauração.

### <a name="before-you-start"></a>Antes de começar

1. No SQL Server, faça uma pasta numa unidade com espaço livre suficiente para conter uma única cópia de uma cópia de uma cópia de uma cópia. Por exemplo: `C:\MABSBACKUP`.

1. Partilhe a pasta. Por exemplo, partilhe `C:\MABSBACKUP` a pasta como *DPMBACKUP*.

1. Copie e cole o comando OSQL abaixo no Bloco de Notas e guarde-o para um ficheiro chamado `C:\MABSACKUP\bkupdb.cmd` . Certifique-se de que não há extensão .txt. Modifique o SQL_Instance_name e DPMDB_NAME para corresponder à instância e nome DPMDB utilizado pelo seu servidor MABS.

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. Utilizando o Bloco de Notas, abra o ficheiro **ScriptingConfig.xml** localizado sob a `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` pasta no servidor MABS.

1. Modifique **ScriptingConfig.xml** e **altere DataSourceName=** seja a letra de unidade que contém a pasta/partilha DPMDBBACKUP. Altere a entrada Pré-BackupScript para o caminho e nome completos de **bkupdb.cmd** guardado no passo 3.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="https://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
    <DatasourceScriptConfig DataSourceName="C:">
    <PreBackupScript>C:\MABSDBBACKUP\bkupdb.cmd</PreBackupScript>
    <TimeOut>120</TimeOut>
    </DatasourceScriptConfig>
    </ScriptConfiguration>
    ```

1. Guarde as alterações para **ScriptingConfig.xml**.

1. Proteja a pasta C:\MABSBACKUP ou a `\sqlservername\MABSBACKUP` partilha utilizando mABS e aguarde a criação da réplica inicial. Deve haver um **dpmdb.bak** na pasta C:\MABSBACKUP como resultado do script pré-backup em execução, que por sua vez foi copiado para a réplica MABS.

1. Se não ativar a recuperação do autosserviço, precisará de alguns passos adicionais para partilhar a pasta MABSBACKUP na réplica:

    1. Na consola MABS > **Protection**, localize a fonte de dados MABSBACKUP e selecione-a. Na secção de detalhes, **selecione Click para ver detalhes** sobre o link para o caminho da réplica e copie o caminho para o Bloco de Notas. Remova o caminho de origem e mantenha o caminho de destino. O caminho deve ser semelhante ao seguinte: `C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP` .

    2. Faça uma parte desse caminho utilizando o nome de partilha **MABSSERVERNAME-DPMDB**. Pode utilizar o comando Net Share abaixo indicado a partir de uma linha de comandos administrativa.

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>Configurar a cópia de segurança

Pode fazer backup na base de dados MABS como qualquer outra base de dados do SQL Server utilizando a cópia de segurança nativa do SQL Server.

- Obtenha uma [descrição geral](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) da cópia de segurança do SQL Server.

- [Saiba mais](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) sobre criar uma cópia de segurança do SQL Server na nuvem.

### <a name="recover-the-mabs-database"></a>Recuperar a base de dados MABS

1. Ligue-se à partilha utilizando o `\\MABSServer\MABSSERVERNAME-dpmdb` Explorer a partir de qualquer computador Windows.

2. Clique com o botão direito no ficheiro **dpmdb.bak** para visualizar propriedades. No separador **Versões Anteriores** encontram-se todas as cópias de segurança que pode selecionar e copiar. Há também a última cópia de segurança ainda localizada na pasta C:\MABSBACKup que também é facilmente acessível.

3. Se precisar de mover um disco de armazenamento MABS anexado a SAN para outro servidor para ser capaz de ler a partir do volume de réplica, ou reinstalar o Windows para ler discos ligados localmente, terá de conhecer previamente o percurso do ponto de montagem do volume de réplica MABS ou o Volume GUID para saber qual o volume que detém a cópia de segurança da base de dados. Pode utilizar o script SQL abaixo para extrair essas informações em qualquer altura após a proteção inicial, mas antes de ser necessário efetuar um restauro. Substitua-o `%dpmsqlservername%` pelo nome do SQL Server que hospeda a base de dados.

    ```sql
    Select ag.NetbiosName as
    ServerName,ds.DataSourceName,vol.MountPointPath,vol.GuidName
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%C:\%' -- volume drive letter for DPMBACKUP
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB

    ```

4. Se precisar de recuperar depois de mover os discos de armazenamento MABS ou uma reconstrução do servidor MABS:

    1. Tem o volume GUID, por isso, se esse volume precisar de ser montado noutro servidor do Windows ou após a reconstrução de um servidor MABS, utilize **mountvol.exe** para atribuir uma letra de unidade utilizando o volume GUID a partir da saída do script SQL: `C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` .

    2. Repartir a pasta MABSBACKUP no volume de réplica utilizando a letra de acionamento e a parte do caminho da réplica que representa a estrutura da pasta.

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. Ligue-se à partilha utilizando o `\\SERVERNAME\MABSSERVERNAME-dpmdb` Explorer a partir de qualquer computador Windows.

    4. Clique com o botão direito no ficheiro **dpmdb.bak** para ver as propriedades. No separador **Versões Anteriores** encontram-se todas as cópias de segurança que pode selecionar e copiar.

## <a name="using-dpmsync"></a>Utilização de DPMSync

**O DpmSync** é uma ferramenta de linha de comando que permite sincronizar a base de dados MABS com o estado dos discos na piscina de armazenamento e com os agentes de proteção instalados. O DpmSync restaura a base de dados MABS, sincroniza a base de dados MABS com as réplicas na piscina de armazenamento, restaura a base de dados do Relatório e redistribui réplicas em falta.

### <a name="parameters"></a>Parâmetros

| Parâmetro      | Descrição    |
|----------------|-----------------------------|
| **-Restaurar ODB**                       | Restaura uma base de dados MABS a partir de um local especificado.|
| **-Sincronização**                            | Sincroniza as bases de dados restauradas. Tem de executar **O DpmSync –Sync** depois de restaurar as bases de dados. Depois de executar **o DpmSync –Sync,** algumas réplicas podem ainda estar marcadas como desaparecidas. |
| **-Localização DbLoc** *location*                | Identifica a localização da cópia de segurança da base de dados MABS.|
| **-Nome de instância** <br/>*servidor\instância*     | Instância para a qual o DPMDB deve ser restaurado.|
| **-RealocalatoReplica**         | Realoca todos os volumes de réplicas em falta sem sincronização. |
| **-DadosCopiados**                      | Indica que preencheu os dados de carregamento nos volumes de réplicas recentemente atribuídos. Isto é aplicável apenas para computadores clientes. |

**Exemplo 1:** Para restaurar a base de dados MABS a partir de meios de segurança locais no servidor MABS, execute o seguinte comando:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

Depois de restaurar a base de dados MABS, para sincronizar as bases de dados, execute o seguinte comando:

```cmd
DpmSync -Sync
```

Depois de restaurar e sincronizar a base de dados MABS e antes de restaurar a réplica, executar o seguinte comando para realocar o espaço do disco para a réplica:

```cmd
DpmSync -ReallocateReplica
```

**Exemplo 2:** Para restaurar a base de dados MABS a partir de uma base de dados remota, execute o seguinte comando no computador remoto:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

Depois de restaurar a base de dados MABS, para sincronizar as bases de dados, execute o seguinte comando no servidor MABS:

```cmd
DpmSync -Sync
```

Depois de restaurar e sincronizar a base de dados MABS e antes de restaurar a réplica, execute o seguinte comando no Servidor MABS para realocar o espaço do disco para a réplica:

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>Próximos passos

- [Matriz de suporte MABS](backup-support-matrix-mabs-dpm.md)
- [MABS FAQ](backup-azure-dpm-azure-server-faq.md)
