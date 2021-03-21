---
title: Migrar bases de dados para SQL Managed Instance usando o Serviço de Reprodução de Registos
description: Saiba como migrar bases de dados do SQL Server para SQL Managed Instance utilizando o Serviço de Reprodução de Registos
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: 0bc00aea67fa2f71599ee62e657e1ca1b0627681
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199854"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>Migrar bases de dados do SQL Server para SQL Managed Instance utilizando o Serviço de Reprodução de Registo (Pré-visualização)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica como configurar manualmente a migração da base de dados do SQL Server 2008-2019 para Azure SQL Managed Instance utilizando o Log Replay Service (LRS), atualmente em pré-visualização pública. O LRS é um serviço de nuvem que está habilitado para a SQL Managed Instance e é baseado na tecnologia de envio de registos sql Server. 

[O Azure Database Migration Service](/azure/dms/tutorial-sql-server-to-managed-instance) e o LRS utilizam a mesma tecnologia de migração subjacente e as mesmas APIs. Ao lançar lRS, estamos ainda a permitir migrações personalizadas complexas e arquitetura híbrida entre o SQL Server e o SQL Managed Instance.

## <a name="when-to-use-log-replay-service"></a>Quando utilizar o Serviço de Reprodução de Registos

Quando não pode utilizar o Azure Database Migration Service para migração, pode utilizar o LRS diretamente com cmdlets PowerShell, Azure CLI ou APIs para construir e orquestrar manualmente migrações de bases de dados para a SQL Managed Instance. 

Pode considerar a utilização de LRS nos seguintes casos:
- Precisa de mais controlo para o seu projeto de migração de bases de dados.
- Há pouca tolerância para o tempo de inatividade no corte migratório.
- O ficheiro executável do Serviço de Migração da Base de Dados não pode ser instalado no seu ambiente.
- O ficheiro executável do Serviço de Migração da Base de Dados não tem acesso a cópias de segurança de bases de dados.
- Não há acesso ao so hospedeiro, ou não existem privilégios de administrador.
- Não é possível abrir portas de rede do seu ambiente para Azure.
- As cópias de segurança são armazenadas diretamente no Azure Blob Storage através da `TO URL` opção.
- Tens de usar reforços diferenciais.

> [!NOTE]
> Recomendamos automatizar a migração de bases de dados do SQL Server para SQL Managed Instance utilizando o Serviço de Migração de Bases de Dados. Este serviço utiliza o mesmo serviço de nuvem LRS na parte de trás, com envio de registo no `NORECOVERY` modo. Considere usar manualmente o LRS para orquestrar migrações quando o Serviço de Migração de Bases de Dados não suporta totalmente os seus cenários.

## <a name="how-it-works"></a>Como funciona

A construção de uma solução personalizada utilizando lRS para migrar bases de dados para a nuvem requer várias etapas de orquestração, como mostrado no diagrama e tabela mais tarde nesta secção.

A migração consiste em fazer cópias de dados completas no SQL Server com `CHECKSUM` ativação e copiar ficheiros de backup para o Azure Blob Storage. O LRS é utilizado para restaurar ficheiros de backup do Blob Storage para SQL Managed Instance. Blob Storage é armazenamento intermediário entre SQL Server e SQL Managed Instance.

O LRS monitoriza o Blob Storage para qualquer novo diferencial ou cópias de segurança adicionadas após a restauração da cópia de segurança completa. O LRS restaura automaticamente estes novos ficheiros. Pode utilizar o serviço para monitorizar o progresso dos ficheiros de backup que estão a ser restaurados em SQL Managed Instance, e pode parar o processo se necessário.

O LRS não requer uma convenção específica de nomeação para ficheiros de backup. Digitaliza todos os ficheiros colocados no Blob Storage e constrói a cadeia de backup a partir da leitura apenas dos cabeçalhos de ficheiro. As bases de dados estão num estado de "restauração" durante o processo de migração. As bases de dados são restauradas no modo [NORECOVERY,](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) pelo que não podem ser utilizadas para leitura ou escrita até que o processo de migração esteja concluído. 

Se estiver a migrar várias bases de dados, tem de:
 
- Coloque cópias de segurança para cada base de dados numa pasta separada no Blob Storage.
- Inicie o LRS separadamente para cada base de dados.
- Especifique diferentes caminhos para separar as pastas de armazenamento Blob. 

Pode iniciar o LRS em modo *autocompleto* ou *contínuo.* Quando o iniciar no modo de preconização automática, a migração terminará automaticamente quando o último dos ficheiros de cópia de segurança especificados tiver sido restaurado. Quando iniciar o LRS em modo contínuo, o serviço irá restaurar continuamente quaisquer novos ficheiros de cópia de segurança adicionados, e a migração terminará apenas na redução manual. 

Recomendamos que corte manualmente depois de ter sido tomada a cópia de segurança final da cauda de log e que seja mostrada como restaurada em SQL Managed Instance. O passo final de corte fará com que a base de dados fique on-line e disponível para utilização de leitura e escrita em SQL Managed Instance.

Depois de o LRS ser interrompido, quer automaticamente, através de um corte automático ou manualmente, não é possível retomar o processo de restauro de uma base de dados que foi introduzida online em SQL Managed Instance. Para restaurar ficheiros de cópias de segurança adicionais após o fim da migração através de um corte automático ou de corte, é necessário eliminar a base de dados. Também precisa restaurar toda a cadeia de backup do zero reiniciando o LRS.

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="Diagrama que explica os passos de orquestração do Serviço de Reprodução de Registo para SQL Managed Instance." border="false":::
    
| Operação | Detalhes |
| :----------------------------- | :------------------------- |
| **1. Copiar cópias de segurança da base de dados do SQL Server para o Blob Storage**. | Copie cópias completas, diferenciais e registar backups do SQL Server para um recipiente de armazenamento Blob utilizando [a Azcopy](/azure/storage/common/storage-use-azcopy-v10) ou [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). <br /><br />Use os nomes dos ficheiros. O LRS não requer uma convenção específica de nomeação de ficheiros.<br /><br />Ao migrar várias bases de dados, precisa de uma pasta separada para cada base de dados. |
| **2. Inicie o LRS na nuvem**. | Pode reiniciar o serviço com uma escolha de cmdlets: PowerShell[(start-azsqlinstancedatabaselogreplay)](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)ou Azure CLI[(az_sql_midb_log_replay_start cmdlets).](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start) <br /><br /> Inicie o LRS separadamente para cada base de dados que aponta para uma pasta de reserva no Blob Storage. <br /><br /> Depois de iniciar o serviço, irá retirar cópias de segurança do recipiente blob Storage e começar a restaurá-los em SQL Managed Instance.<br /><br /> Se iniciar o LRS em modo contínuo, depois de todas as cópias de segurança inicialmente carregadas serem restauradas, o serviço irá observar quaisquer novos ficheiros carregados para a pasta. O serviço aplicará continuamente registos com base na corrente do número de sequência de registo (LSN) até que seja parado. |
| **2.1. Acompanhar o progresso da operação**. | Pode monitorizar o progresso da operação de restauro com uma escolha de cmdlets: PowerShell[(get-azsqlinstancedatabaselogreplay)](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)ou Azure CLI[(az_sql_midb_log_replay_show cmdlets).](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show) |
| **2.2. Parar a operação se necessário**. | Se precisar de parar o processo de migração, tem uma escolha de cmdlets: PowerShell[(stop-azsqlinstancedatabaselogreplay)](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)ou Azure CLI[(az_sql_midb_log_replay_stop).](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) <br /><br /> Parar a operação irá apagar a base de dados que está a restaurar no SQL Managed Instance. Depois de parar uma operação, não pode retomar o LRS para uma base de dados. Tens de reiniciar o processo de migração do zero. |
| **3. Corte na nuvem quando estiver pronto**. | Pare a aplicação e a carga de trabalho. Pegue a última cópia de segurança da cauda de registo e faça o upload para o Azure Blob Storage.<br /><br /> Complete o corte iniciando uma operação LRS `complete` com uma escolha de cmdlets: PowerShell[(complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)) ou Azure CLI [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete). Esta operação irá parar o LRS e fazer com que a base de dados fique online para ler e escrever no SQL Managed Instance.<br /><br /> Reponte a cadeia de ligação de aplicação do SQL Server para a SQL Managed Instance. |

## <a name="requirements-for-getting-started"></a>Requisitos para começar

### <a name="sql-server-side"></a>Lado do servidor SQL
- SQL Server 2008-2019
- Cópia de segurança completa das bases de dados (um ou vários ficheiros)
- Cópia de segurança diferencial (um ou vários ficheiros)
- Registar cópia de segurança (não dividida para um ficheiro de registo de transações)
- `CHECKSUM` habilitado para backups (obrigatório)

### <a name="azure-side"></a>Lado azul
- PowerShell Az.SQL versão do módulo 2.16.0 ou posterior[(instalado](https://www.powershellgallery.com/packages/Az.Sql/) ou acedido através do [Azure Cloud Shell)](/azure/cloud-shell/)
- Versão Azure CLI 2.19.0 ou posterior[(instalada)](/cli/azure/install-azure-cli)
- Recipiente de armazenamento Azure Blob abastado
- Ficha de segurança de assinatura de acesso partilhado (SAS) com permissões de leitura e lista geradas para o recipiente de armazenamento blob

### <a name="migration-of-multiple-databases"></a>Migração de várias bases de dados
Tem de colocar ficheiros de cópia de segurança para diferentes bases de dados em pastas separadas no Blob Storage.

Inicie o LRS separadamente para cada base de dados apontando para uma pasta apropriada no Blob Storage. O LRS pode suportar até 100 processos de restauro simultâneos por instância gerida única.

### <a name="azure-rbac-permissions"></a>Permissões Azure RBAC
A execução de LRS através dos clientes fornecidos requer uma das seguintes funções Azure:
- Função do Proprietário de Assinatura
- [Papel de contribuinte de instância gerida](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)
- Função personalizada com a seguinte permissão: `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Melhores práticas

Recomendamos as seguintes boas práticas:
- Executar [o Assistente de Migração de Dados](/sql/dma/dma-overview) para validar que as suas bases de dados estão prontas para serem migradas para a SQL Managed Instance. 
- Divida cópias de segurança completas e diferenciais em vários ficheiros, em vez de utilizar um único ficheiro.
- Ativar a compressão de backup.
- Utilize a Cloud Shell para executar scripts, pois será sempre atualizado para os mais recentes cmdlets lançados.
- Planeie completar a migração dentro de 47 horas depois de iniciar o LRS. Este é um período de graça que impede a instalação de patches de software geridos pelo sistema.

> [!IMPORTANT]
> - Não pode usar a base de dados que está a ser restaurada através do LRS até que o processo de migração termine. 
> - O LRS não suporta o acesso apenas à leitura das bases de dados durante a migração.
> - Após o fim da migração, o processo de migração é finalizado porque o LRS não suporta o reinício do processo de restauro.

## <a name="steps-to-execute"></a>Passos a executar

### <a name="make-backups-of-sql-server"></a>Faça backups do SQL Server

Pode fazer backups do SQL Server utilizando qualquer uma das seguintes opções:

- Faça o backup de discos locais e, em seguida, faça upload de ficheiros para o Azure Blob Storage, se o seu ambiente restringir as cópias de segurança diretas ao Blob Storage.
- Faça o back-up diretamente para o Blob Storage com a `TO URL` opção em T-SQL, se o seu ambiente e os procedimentos de segurança o permitirem. 

Desa ajuste bases de dados que pretende migrar para o modo de recuperação completo para permitir cópias de segurança de registo.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Para fazer manualmente cópias de segurança completas, diferenciais e registar cópias de segurança da sua base de dados no armazenamento local, utilize os seguintes scripts T-SQL da amostra. Certifique-se de que a `CHECKSUM` opção está ativada, porque é obrigatória para o LRS.

```SQL
-- Example of how to make a full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example of how to make a differential database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

O Azure Blob Storage é utilizado como armazenamento intermediário para ficheiros de backup entre o SQL Server e o SQL Managed Instance. Para criar uma nova conta de armazenamento e um recipiente de bolhas dentro da conta de armazenamento, siga estes passos:

1. [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md?tabs=azure-portal).
2. [Crete um recipiente de bolhas](../../storage/blobs/storage-quickstart-blobs-portal.md) dentro da conta de armazenamento.

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>Cópia de backups do SQL Server para Blob Storage

Nas bases de dados migratórias para um caso gerido utilizando o LRS, pode utilizar as seguintes abordagens para enviar cópias de segurança para o Blob Storage:
- Usando backup nativo do SQL Server [para funcionalidade de URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)
- Usando [a Azcopy](/azure/storage/common/storage-use-azcopy-v10) ou [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer) para enviar backups para um recipiente de bolhas
- Utilização do Explorador de Armazenamento no portal Azure

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>Faça backups do SQL Server diretamente para o Blob Storage
Se as suas políticas corporativas e de rede o permitirem, uma alternativa é fazer cópias de segurança do SQL Server diretamente para o Blob Storage utilizando a opção [DE BACKUP TO URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) nativa do SQL Server. Se você pode seguir esta opção, você não precisa fazer backups no armazenamento local e enviá-los para Blob Storage.

Como primeiro passo, esta operação requer que gere um token de autenticação SAS para o Blob Storage e, em seguida, importe o token para SQL Server. O segundo passo é fazer backups com a `TO URL` opção em T-SQL. Certifique-se de que todas as cópias de segurança são feitas com a `CHEKSUM` opção ativada.

Para referência, o seguinte código de amostra faz cópias de segurança para o Blob Storage. Este exemplo não inclui instruções sobre como importar o token SAS. Pode encontrar instruções detalhadas, incluindo como gerar e importar o símbolo SAS para o SQL Server, no tutorial [Use Azure Blob Storage com SQL Server](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

```SQL
-- Example of how to make a full database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO
-- Example of how to make a differential database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to a URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>Gerar um símbolo de autenticação SAS de armazenamento blob para LRS

O Azure Blob Storage é utilizado como armazenamento intermediário para ficheiros de backup entre o SQL Server e o SQL Managed Instance. Você precisa gerar um token de autenticação SAS, com apenas lista e leitura permissões, para LRS. O token permitirá ao LRS aceder ao Blob Storage e utilizar os ficheiros de backup para os restaurar em SQL Managed Instance. 

Siga estes passos para gerar o símbolo:

1. Abrir o Explorador de Armazenamento a partir do portal Azure.
2. Expandir **recipientes blob**.
3. Clique com o botão direito no recipiente blob e selecione **Obter Assinatura de Acesso Partilhado**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Screenshot que mostra seleções para gerar um token de autenticação S A S.":::

4. Selecione o prazo para a expiração do token. Certifique-se de que o token é válido durante a duração da sua migração.
5. Selecione o fuso horário para o token: UTC ou a sua hora local.
    
   > [!IMPORTANT]
   > O fuso horário do símbolo e o seu caso gerido podem desajustar. Certifique-se de que o token SAS tem a validade adequada, tendo em conta os fusos horários. Se possível, defina o fuso horário para uma hora mais cedo e mais tarde da sua janela de migração planeada.
6. Selecione apenas permissões **de Leitura** e **Lista.**

   > [!IMPORTANT]
   > Não selecione outras permissões. Se o fizeres, o LRS não começa. Este requisito de segurança é por desígnio.
7. Selecione **Criar**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Screenshot que mostra seleções para expiração, fuso horário e permissões de S A S, fuso horário e permissões, juntamente com o botão Criar.":::

A autenticação SAS é gerada com a validade do tempo que especificou. Precisa da versão URI do token, como mostra a imagem seguinte.

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Screenshot que mostra um exemplo da versão U R I de um token S A S.":::

### <a name="copy-parameters-from-the-sas-token"></a>Copiar parâmetros do token SAS

Antes de usar o token SAS para iniciar o LRS, precisa entender a sua estrutura. O URI do token SAS gerado consiste em duas partes separadas com um ponto de interrogação ( `?` , como mostrado neste exemplo:

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Exemplo U R I para um token S A S gerado para o Serviço de Reprodução de Registo." border="false":::

A primeira parte, a começar `https://` pelo ponto de interrogação , é usada para `?` o parâmetro que é alimentado como na entrada para o `StorageContainerURI` LRS. Dá informações ao LRS sobre a pasta onde os ficheiros de cópia de segurança da base de dados são armazenados.

A segunda parte, a começar após o ponto de interrogação `?` e a ir até ao fim da corda, é o `StorageContainerSasToken` parâmetro. Este é o token de autenticação assinado real, que é válido durante o tempo especificado. Esta parte não precisa necessariamente de começar `sp=` como mostrado no exemplo. O seu caso pode diferir.

Copie os parâmetros da seguinte forma:

1. Copie a primeira parte do token, começando de todo o caminho até ao ponto de `https://` interrogação ( `?` ). Utilize-o como `StorageContainerUri` parâmetro no PowerShell ou no Azure CLI para iniciar o LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="Screenshot que mostra copiar a primeira parte do token.":::

2. Copie a segunda parte do token, partindo do ponto de interrogação até `?` ao fim da cadeia. Utilize-o como `StorageContainerSasToken` parâmetro no PowerShell ou no Azure CLI para iniciar o LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="Screenshot que mostra copiar a segunda parte do token.":::
   
> [!NOTE]
> Não inclua o ponto de interrogação quando copiar qualquer parte do símbolo.

### <a name="log-in-to-azure-and-select-a-subscription"></a>Faça login no Azure e selecione uma subscrição

Utilize o seguinte cmdlet PowerShell para iniciar sessão no Azure:

```powershell
Login-AzAccount
```

Selecione a subscrição apropriada onde a sua instância gerida reside utilizando o seguinte cmdlet PowerShell:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Iniciar a migração

Começa-se a migração começando o LRS. Pode iniciar o serviço em modo auto-completa ou contínuo. 

Quando utilizar o modo de prealto automático, a migração terminará automaticamente quando o último dos ficheiros de cópia de segurança especificados tiver sido restaurado. Esta opção requer que o comando inicial especifique o nome de ficheiro do último ficheiro de backup. 

Quando utilizar o modo contínuo, o serviço irá restaurar continuamente quaisquer novos ficheiros de backup que foram adicionados. A migração terminará apenas no corte manual. 

### <a name="start-lrs-in-autocomplete-mode"></a>Iniciar LRS no modo auto-completa

Para iniciar o LRS no modo auto-completa, utilize os seguintes comandos PowerShell ou Azure CLI. Especifique o último nome do ficheiro de cópia de segurança utilizando o `-LastBackupName` parâmetro. Ao restaurar o último dos ficheiros de backup especificados, o serviço iniciará automaticamente uma redução.

Aqui está um exemplo de iniciar o LRS no modo autocompleto utilizando o PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Aqui está um exemplo de iniciar o LRS no modo auto-completa, utilizando o Azure CLI:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Iniciar LRS em modo contínuo

Aqui está um exemplo de iniciar o LRS em modo contínuo utilizando o PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Aqui está um exemplo de iniciar o LRS em modo contínuo utilizando o Azure CLI:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Os clientes PowerShell e CLI para iniciar o LRS em modo contínuo são sincronizados. Isto significa que os clientes vão esperar pela resposta da API para reportar sobre o sucesso ou o insucesso no início do trabalho. 

Durante esta espera, o comando não devolverá o controlo à ordem do comando. Se estiver a escrever a experiência de migração e precisar do comando de início do LRS para devolver o controlo imediatamente para continuar com o resto do script, pode executar o PowerShell como um trabalho de fundo com o `-AsJob` switch. Por exemplo:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Quando se inicia um trabalho de fundo, um objeto de trabalho regressa imediatamente, mesmo que o trabalho leve um tempo prolongado para terminar. Pode continuar a trabalhar na sessão sem interrupção enquanto o trabalho funciona. Para obter detalhes sobre a execução da PowerShell como um trabalho de fundo, consulte a documentação [powerShell Start-Job.](/powershell/module/microsoft.powershell.core/start-job#description)

Da mesma forma, para iniciar um comando Azure CLI no Linux como um processo de fundo, use a ampersand `&` () no final do comando de início do LRS:

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> Depois de iniciar o LRS, quaisquer patches de software geridos pelo sistema são interrompidos durante 47 horas. Após esta janela, o próximo patch de software automatizado irá parar automaticamente o LRS. Se isso acontecer, não podes retomar a migração e tens de reiniciá-la do zero. 

## <a name="monitor-the-migration-progress"></a>Monitorizar o progresso da migração

Para monitorizar o progresso da migração através da PowerShell, utilize o seguinte comando:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Para acompanhar o progresso da migração através do CLI Azure, utilize o seguinte comando:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Parar a migração

Se precisar de parar a migração, utilize os seguintes cmdlets. Parar a migração irá eliminar a base de dados de restauro em SQL Managed Instance, para que não seja possível retomar a migração.

Para interromper o processo de migração através do PowerShell, utilize o seguinte comando:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Para interromper o processo de migração através do CLI Azure, utilize o seguinte comando:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Complete a migração (modo contínuo)

Se iniciou o LRS em modo contínuo, depois de ter assegurado que todas as cópias de segurança foram restauradas, iniciando o corte completará a migração. Após o corte, a base de dados será migrada e pronta para ler e escrever acesso.

Para completar o processo de migração no modo contínuo LRS através do PowerShell, utilize o seguinte comando:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Para completar o processo de migração no modo contínuo LRS através do CLI Azure, utilize o seguinte comando:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>Limitações funcionais

As limitações funcionais do LRS são:
- A base de dados que está a restaurar não pode ser usada apenas para acesso de leitura durante o processo de migração.
- Os patches de software geridos pelo sistema são bloqueados durante 47 horas após o início do LRS. Após o termo desta janela de tempo, a próxima atualização de software irá parar o LRS. Em seguida, tem de reiniciar o LRS do zero.
- O LRS exige que as bases de dados no SQL Server sejam apoiadas com a `CHECKSUM` opção ativada.
- O token SAS que o LRS utilizará deve ser gerado para todo o recipiente de armazenamento Azure Blob, e deve ter apenas permissões de leitura e lista.
- Os ficheiros de cópia de segurança para diferentes bases de dados devem ser colocados em pastas separadas no Blob Storage.
- O LRS deve ser iniciado separadamente para cada base de dados que aponta para separar pastas com ficheiros de cópia de segurança no Blob Storage.
- O LRS pode suportar até 100 processos de restauro simultâneos por instância gerida única.

## <a name="troubleshooting"></a>Resolução de problemas

Depois de iniciar o LRS, utilize o cmdlet de monitorização `get-azsqlinstancedatabaselogreplay` (ou `az_sql_midb_log_replay_show` ) para ver o estado da operação. Se o LRS não começar após algum tempo e tiver um erro, verifique se há questões mais comuns:

- Uma base de dados existente no SQL Managed Instance tem o mesmo nome que aquele que está a tentar migrar do SQL Server? Resolva este conflito renomeando uma das bases de dados.
- A cópia de segurança da base de dados do SQL Server foi feita através da `CHECKSUM` opção?
- As permissões no token SAS são apenas lidas e listas para LRS?
- Copiou o símbolo SAS para LRS após o ponto de interrogação ( `?` ), com conteúdo a partir deste ponto: `sv=2020-02-10...` ? 
- O tempo de validade do símbolo SAS é aplicável para o intervalo de tempo de início e conclusão da migração? Pode haver incompatibilidades devido aos diferentes fusos horários utilizados para a SQL Managed Instance e o token SAS. Tente regenerar o token SAS e prolongar a validade simbólica da janela de tempo antes e depois da data atual.
- O nome da base de dados, o nome do grupo de recursos e o nome de instância gerido são corretamente escritos?
- Se iniciou o LRS no modo de preenchamento automático, foi especificado um nome de ficheiro válido para o último ficheiro de cópia de segurança especificado?

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [a migração do SQL Server para o SQL.](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)
- Saiba mais sobre [as diferenças entre o SQL Server e o SQL Managed Instance](transact-sql-tsql-differences-sql-server.md).
- Saiba mais sobre [as melhores práticas para custos e cargas de trabalho de tamanho migradas para Azure.](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
