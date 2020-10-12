---
title: Diferenças T-SQL entre SQL Server & Azure SQL Managed Instance
description: Este artigo discute as diferenças Transact-SQL (T-SQL) entre uma Instância Gerida Azure SQL e o SQL Server.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: reference
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, bonova, danil
ms.date: 06/02/2020
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 1298a1676d7a7ac0321ae768c3e596f481e80a8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617881"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>Diferenças T-SQL entre SQL Server & Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo resume e explica as diferenças de sintaxe e comportamento entre Azure SQL Managed Instance e SQL Server. 


A SQL Managed Instance fornece alta compatibilidade com o motor de base de dados sql Server, e a maioria das funcionalidades são suportadas numa SqL Managed Instance.

![Fácil migração do SQL Server](./media/transact-sql-tsql-differences-sql-server/migration.png)

Existem algumas limitações paaS que são introduzidas em SQL Managed Instance e algumas mudanças de comportamento em comparação com o SQL Server. As diferenças dividem-se nas seguintes categorias: <a name="Differences"></a>

- [A disponibilidade](#availability) inclui as diferenças em [Grupos de Disponibilidade Sempre e](#always-on-availability-groups) [cópias de segurança.](#backup)
- [A](#security) segurança inclui as diferenças na [auditoria,](#auditing) [certificados,](#certificates) [credenciais,](#credential) [fornecedores criptográficos,](#cryptographic-providers) [logins e utilizadores,](#logins-and-users)e a [chave de serviço e de serviço principal.](#service-key-and-service-master-key)
- [A configuração](#configuration) inclui as diferenças na [extensão do conjunto tampão,](#buffer-pool-extension) [colagem,](#collation) [níveis de compatibilidade,](#compatibility-levels) [espelhamento da base de dados,](#database-mirroring) [opções de base de dados,](#database-options) [Agente do Servidor SQL](#sql-server-agent)e [opções de tabela](#tables).
- [As funcionalidades](#functionalities) incluem [INSERÇÃO A GRANEL/OPENROWSET,](#bulk-insert--openrowset) [CLR,](#clr) [DBCC,](#dbcc) [transações distribuídas, eventos alargados, bibliotecas](#distributed-transactions) [externas,](#external-libraries) [filestream e FileTable,](#filestream-and-filetable)Pesquisa [extended events](#extended-events) [Semântica de texto completo,](#full-text-semantic-search) [servidores ligados,](#linked-servers) [PolyBase,](#polybase) [Replicação,](#replication) [RESTAURAR,](#restore-statement)Corretor de [Serviços, procedimentos, funções e gatilhos.](#stored-procedures-functions-and-triggers) [Service Broker](#service-broker)
- [Configurações de](#Environment) ambiente, tais como VNets e configurações de sub-redes.

A maioria destas características são restrições arquitetónicas e representam características de serviço.

Problemas temporários conhecidos que são descobertos em SQL Managed Instance e serão resolvidos no futuro são descritos na [página de notas de lançamento](../database/doc-changes-updates-release-notes.md).

## <a name="availability"></a>Disponibilidade

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Grupos de Disponibilidade Always On

[A alta disponibilidade](../database/high-availability-sla.md) é incorporada em SQL Managed Instance e não pode ser controlada pelos utilizadores. As seguintes declarações não são apoiadas:

- [CRIAR PONTO FINAL... PARA DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [CRIAR GRUPO DE DISPONIBILIDADE](/sql/t-sql/statements/create-availability-group-transact-sql)
- [GRUPO DE ALTER AVAILABILITY](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [GRUPO DE DISPONIBILIDADE DE DROP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- A cláusula [SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) da declaração [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

SQL Managed Instance tem backups automáticos, para que os utilizadores possam criar `COPY_ONLY` cópias de dados completas. As cópias de segurança diferenciais, de registos e de ficheiros não são suportadas.

- Com uma SQL Managed Instance, pode fazer uma rede de dados de casos apenas para uma conta de armazenamento Azure Blob:
  - Só `BACKUP TO URL` é apoiado.
  - `FILE`, `TAPE` e os dispositivos de reserva não são suportados.
- A maioria das `WITH` opções gerais são apoiadas.
  - `COPY_ONLY` é obrigatório.
  - `FILE_SNAPSHOT` não é apoiado.
  - Opções de fita: `REWIND` , , e não são `NOREWIND` `UNLOAD` `NOUNLOAD` suportados.
  - Opções específicas do registo: `NORECOVERY` , e não são `STANDBY` `NO_TRUNCATE` suportadas.

Limitações: 

- Com uma SQL Managed Instance, pode fazer backup de uma base de dados de casos para uma cópia de segurança com até 32 listras, o que é suficiente para bases de dados até 4 TB se for utilizada compressão de backup.
- Não é possível executar `BACKUP DATABASE ... WITH COPY_ONLY` numa base de dados encriptada com encriptação de dados transparentes gerida pelo serviço (TDE). O TDE gerido pelo serviço obriga a que as cópias de segurança sejam encriptadas com uma chave TDE interna. A chave não pode ser exportada, por isso não podes restaurar a reserva. Utilize cópias de segurança automáticas e restauro pontual ou utilize [o TDE gerido pelo cliente (BYOK).](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key) Também pode desativar a encriptação na base de dados.
- O tamanho máximo de listra de backup utilizando o `BACKUP` comando em SQL Managed Instance é de 195 GB, que é o tamanho máximo de blob. Aumente o número de listras no comando de backup para reduzir o tamanho das listras individuais e permaneça dentro deste limite.

    > [!TIP]
    > Para contornar esta limitação, quando faz uma produção de uma base de dados a partir de um SQL Server num ambiente no local ou numa máquina virtual, pode:
    >
    > - Voltar para `DISK` em vez de recuar até `URL` .
    > - Faça o upload dos ficheiros de reserva para o armazenamento blob.
    > - Restaurar em SQL Caso Gerido.
    >
    > O `Restore` comando em SQL Managed Instance suporta tamanhos de bolhas maiores nos ficheiros de cópia de segurança porque um tipo de blob diferente é usado para armazenamento dos ficheiros de backup carregados.

Para obter informações sobre cópias de segurança utilizando o T-SQL, consulte [BACKUP](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Segurança

### <a name="auditing"></a>Auditoria

As principais diferenças entre a auditoria no Microsoft Azure SQL e no SQL Server são:

- Com a SQL Managed Instance, a auditoria funciona ao nível do servidor. Os `.xel` ficheiros de registo são armazenados no armazenamento da Azure Blob.
- Com a Azure SQL Database, a auditoria funciona ao nível da base de dados. Os `.xel` ficheiros de registo são armazenados no armazenamento da Azure Blob.
- Com o SQL Server, no local ou em máquinas virtuais, a auditoria funciona ao nível do servidor. Os eventos são armazenados no sistema de ficheiros ou nos registos de eventos do Windows.
 
A auditoria do XEvent em SQL Managed Instance suporta alvos de armazenamento Azure Blob. Os registos de ficheiros e windows não são suportados.

As principais diferenças na `CREATE AUDIT` sintaxe para a auditoria ao armazenamento da Azure Blob são:

- Uma nova sintaxe `TO URL` é fornecida que pode usar para especificar o URL do recipiente de armazenamento Azure Blob onde os `.xel` ficheiros são colocados.
- A sintaxe `TO FILE` não é suportada porque a SQL Managed Instance não consegue aceder às partilhas de ficheiros do Windows.

Para obter mais informações, consulte: 

- [CRIAR AUDITORIA DE SERVIDOR](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTERAR AUDITORIA DO SERVIDOR](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Auditoria](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificados

A SQL Managed Instance não pode aceder a partilhas de ficheiros e pastas Windows, pelo que aplicam-se os seguintes constrangimentos:

- O `CREATE FROM` / `BACKUP TO` ficheiro não é suportado para certificados.
- O `CREATE` / `BACKUP` certificado `FILE` / `ASSEMBLY` não é suportado. Ficheiros de chaves privadas não podem ser usados. 

Consulte [o Certificado CREATE](/sql/t-sql/statements/create-certificate-transact-sql) e O Certificado de [Reserva.](/sql/t-sql/statements/backup-certificate-transact-sql) 
 
**Resumo :** Em vez de criar cópia de segurança do certificado e restaurar a cópia de segurança, [obtenha o conteúdo binário do certificado e a chave privada, guarde-o como ficheiro .sql e crie a partir de binário:](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Credencial

Apenas Azure Key Vault e `SHARED ACCESS SIGNATURE` identidades são suportadas. Os utilizadores do Windows não são suportados.

Consulte [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql) e [ALTERAR CREDENCIAL](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Fornecedores criptográficos

A SQL Managed Instance não consegue aceder a ficheiros, por isso os fornecedores criptográficos não podem ser criados:

- `CREATE CRYPTOGRAPHIC PROVIDER` não é apoiado. Consulte [CREATE CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` não é apoiado. Consulte [o PROVEDOR CRIPTOGRÁFICO ALTER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Inícios de sessão e utilizadores

- Os logins SQL criados através da utilização `FROM CERTIFICATE` `FROM ASYMMETRIC KEY` , e são `FROM SID` suportados. Ver [CRIAR LOGIN](/sql/t-sql/statements/create-login-transact-sql).
- Os principais do servidor Azure Ative (Azure AD) criados com a sintaxe [DE LOGIN CREATE](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) ou com a sintaxe CREATE USER FROM LOGIN [[Azure AD Login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) são suportados. Estes logins são criados ao nível do servidor.

    SQL Managed Instance suporta os principais da base de dados AZure AD com a sintaxe `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` . Esta funcionalidade também é conhecida como Azure AD continha utilizadores de bases de dados.

- Os logins do Windows criados com a `CREATE LOGIN ... FROM WINDOWS` sintaxe não são suportados. Utilize logins e utilizadores do Azure Ative Directory.
- O utilizador da AD AZure que criou o caso tem [privilégios administrativos ilimitados.](../database/logins-create-manage.md)
- Os utilizadores de nível de base de dados Azure AD não-administrador podem ser criados utilizando a `CREATE USER ... FROM EXTERNAL PROVIDER` sintaxe. Ver [CREATE USER ... Do fornecedor externo.](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)
- Os principais servidores AD do Azure suportam funcionalidades SQL dentro de uma única 22.º SQL. Características que requerem interação entre instâncias, não importa se estão dentro do mesmo inquilino AZure AD ou inquilinos diferentes, não são suportados para utilizadores de AD Azure. Exemplos de tais características são:

  - Replicação transacional SQL.
  - Servidor de ligação.

- Configurar um login AD Azure mapeado para um grupo AZure AD, uma vez que o proprietário da base de dados não é suportado.
- A personificação dos principais de nível de AD do Azure utilizando outros princípios AD Azure é suportada, como a cláusula [EXECUTE AS.](/sql/t-sql/statements/execute-as-transact-sql) EXECUTE AS limitações são:

  - EXECUTE COMO UTILIZADOR não é suportado para utilizadores Azure AD quando o nome difere do nome de login. Um exemplo é quando o utilizador é criado através da sintaxe CREATE USER [myAadUser] FROM LOGIN [ john@contoso.com ] e a personificação é tentada através do EXEC AS USER = _myAadUser_. Quando criar um **UTILIZADOR** a partir de um servidor AD AD principal (login), especifique a user_name como a mesma login_name do **LOGIN**.
  - Apenas os principais do nível sql server (logins) que fazem parte do `sysadmin` papel podem executar as seguintes operações que visam os principais AD do Azure:

    - EXECUTAR COMO UTILIZADOR
    - EXECUTAR COMO LOGIN

- A exportação/importação de base de dados utilizando ficheiros bacpac são suportadas para utilizadores de Azure AD em SQL Managed Instance utilizando [sSMS V18.4 ou posteriormente](/sql/ssms/download-sql-server-management-studio-ssms), ou [SQLPackage.exe](/sql/tools/sqlpackage-download).
  - As seguintes configurações são suportadas utilizando o ficheiro bacpac da base de dados: 
    - Exportar/importar uma base de dados entre diferentes instâncias de gestão dentro do mesmo domínio Azure AD.
    - Exporte uma base de dados da SQL Managed Instance e importe para a SQL Database dentro do mesmo domínio AD Azure. 
    - Exporte uma base de dados da Base de Dados SQL e importe para a SQL Managed Instance dentro do mesmo domínio AD Azure.
    - Exporte uma base de dados da SQL Managed Instance e importe para o SQL Server (versão 2012 ou posterior).
      - Nesta configuração, todos os utilizadores AD do Azure são criados como principais da base de dados do SQL Server (utilizadores) sem logins. O tipo de utilizadores está listado como `SQL` e é visível como em `SQL_USER` sys.database_principals). As suas permissões e funções permanecem nos metadados da base de dados do SQL Server e podem ser utilizados para a personificação. No entanto, não podem ser utilizados para aceder e iniciar sessão no SQL Server utilizando as suas credenciais.

- Apenas o login principal de nível de servidor, que é criado pelo processo de provisionamento de instância gerida SQL, membros das funções do servidor, tais como `securityadmin` `sysadmin` ou , ou outros logins com permissão DE LOGIN alter any ao nível do servidor podem criar principais do servidor Azure (logins) na base de dados principal para SQL Managed Instance.
- Se o login for um principal SQL, apenas os logins que fazem parte da `sysadmin` função podem usar o comando criar para criar logins para uma conta AD Azure.
- O login Azure AD deve ser membro de um AD Azure dentro do mesmo diretório que é usado para Azure SQL Managed Instance.
- Os principais servidores AD do Azure (logins) são visíveis no Object Explorer a partir do SQL Server Management Studio 18.0 pré-visualização 5.
- São permitidos os principais dos servidores AZure AD (logins) com uma conta de administração AD Azure. Os principais servidores AD do Azure (logins) têm precedência sobre a administração AD Azure quando resolve o principal e aplica permissões à SQL Managed Instance.
- Durante a autenticação, é aplicada a seguinte sequência para resolver o principal autenticador:

    1. Se a conta AD AZure existir como diretamente mapeada para o principal do servidor AD Azure (login), que está presente em sys.server_principals como tipo "E", conceder acesso e aplicar permissões do servidor AD Ad (login).
    2. Se a conta AD Azure é membro de um grupo AD Azure que está mapeado para o principal servidor AD do Azure (login), que está presente em sys.server_principals como tipo "X", conceder acesso e aplicar permissões do login do grupo Azure AD.
    3. Se a conta AZure AD for um administrador AD Azure Ad de um portal especial configurado para a SQL Managed Instance, que não existe nas vistas do sistema SQL Managed Instance, aplique permissões fixas especiais da administração AD Ad para SQL Managed Instance (modo legado).
    4. Se a conta AZure AD existir como diretamente mapeada para um utilizador AZure AD numa base de dados, que está presente em sys.database_principals como tipo "E", conceder acesso e aplicar permissões do utilizador da base de dados Azure AD.
    5. Se a conta AZure AD é membro de um grupo AD Azure que está mapeado para um utilizador AZure AD em uma base de dados, que está presente em sys.database_principals como tipo "X", conceder acesso e aplicar permissões do login do grupo Azure AD.
    6. Se houver um login AD Azure mapeado para uma conta de utilizador AZure ou uma conta do grupo AZure, que resolve para o utilizador que está autenticando, todas as permissões deste login Azure AD são aplicadas.

### <a name="service-key-and-service-master-key"></a>Chave de serviço e chave principal de serviço

- [A cópia de segurança principal](/sql/t-sql/statements/backup-master-key-transact-sql) não é suportada (gerida pelo serviço SQL Database).
- [A restauração da chave](/sql/t-sql/statements/restore-master-key-transact-sql) master não é suportada (gerida pelo serviço SQL Database).
- [A cópia de segurança da chave principal](/sql/t-sql/statements/backup-service-master-key-transact-sql) de serviço não é suportada (gerida pelo serviço SQL Database).
- [A restauração da chave principal](/sql/t-sql/statements/restore-service-master-key-transact-sql) de serviço não é suportada (gerida pelo serviço SQL Database).

## <a name="configuration"></a>Configuração

### <a name="buffer-pool-extension"></a>Extensão da piscina tampão

- [A extensão](/sql/database-engine/configure-windows/buffer-pool-extension) da piscina tampão não é suportada.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` não é apoiado. VER [CONFIGURAÇÃO DO SERVIDOR ALTER](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Agrupamento

A colagem de instância padrão é `SQL_Latin1_General_CP1_CI_AS` e pode ser especificada como um parâmetro de criação. Ver [Collations](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Níveis de compatibilidade

- Os níveis de compatibilidade suportados são 100, 110, 120, 130, 140 e 150.
- Níveis de compatibilidade abaixo de 100 não são suportados.
- O nível de compatibilidade padrão para novas bases de dados é de 140. Para as bases de dados restauradas, o nível de compatibilidade permanece inalterado se for 100 ou superior.

Consulte [o nível de compatibilidade da BASE DE DADOS ALTER](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Espelhamento da base de dados

O espelhamento da base de dados não é suportado.

- `ALTER DATABASE SET PARTNER` e `SET WITNESS` as opções não são apoiadas.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` não é apoiado.

Para obter mais informações, consulte [alter database set partner e SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) and CREATE [ENDPOINT ... PARA DATABASE_MIRRORING.](/sql/t-sql/statements/create-endpoint-transact-sql)

### <a name="database-options"></a>Opções de base de dados

- Vários ficheiros de registo não são suportados.
- Os objetos na memória não são suportados no nível de serviço De fim geral. 
- Há um limite de 280 ficheiros por instância de Finalidade Geral, o que implica um máximo de 280 ficheiros por base de dados. Tanto os ficheiros de dados como os ficheiros de registo no nível de Finalidade Geral são contados para este limite. [O nível Business Critical suporta 32.767 ficheiros por base de dados.](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)
- A base de dados não pode conter grupos de ficheiros que contenham dados de fluxo de ficheiros. A restauração falha se .bak contiver `FILESTREAM` dados. 
- Todos os ficheiros são colocados no armazém da Azure Blob. IO e produção por ficheiro dependem do tamanho de cada ficheiro individual.

#### <a name="create-database-statement"></a>CRIAR DECLARAÇÃO DE BASE DE DADOs

Aplicam-se as seguintes `CREATE DATABASE` limitações:

- Os ficheiros e grupos de ficheiros não podem ser definidos. 
- A `CONTAINMENT` opção não é apoiada. 
- `WITH` opções não são suportadas. 
   > [!TIP]
   > Como solução alternativa, utilize `ALTER DATABASE` depois `CREATE DATABASE` para definir opções de base de dados para adicionar ficheiros ou para definir a contenção. 

- A `FOR ATTACH` opção não é apoiada.
- A `AS SNAPSHOT OF` opção não é apoiada.

Para mais informações, consulte [a BASE DE DADOS CREATE.](/sql/t-sql/statements/create-database-sql-server-transact-sql)

#### <a name="alter-database-statement"></a>Declaração de BASE DE DADOS ALTER

Algumas propriedades de ficheiros não podem ser definidas ou alteradas:

- Um caminho de arquivo não pode ser especificado na `ALTER DATABASE ADD FILE (FILENAME='path')` declaração T-SQL. `FILENAME`Remova do script porque SQL Managed Instance coloca automaticamente os ficheiros. 
- Um nome de ficheiro não pode ser alterado usando a `ALTER DATABASE` declaração.

As seguintes opções são definidas por padrão e não podem ser alteradas:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

As seguintes opções não podem ser modificadas:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Para mais informações, consulte [a ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- Ativar e desativar o Agente do Servidor SQL não é suportado atualmente em SQL Managed Instance. O SQL Agent está sempre em execução.
- As definições do Agente do Servidor SQL são lidas apenas. O procedimento `sp_set_agent_properties` não é suportado em SQL Managed Instance. 
- Tarefas
  - Os passos de trabalho T-SQL são suportados.
  - São apoiados os seguintes trabalhos de replicação:
    - Leitor de registo de transações
    - Instantâneo
    - Distribuidor
  - As medidas de trabalho da SSIS são apoiadas.
  - Outros tipos de passos de trabalho não são suportados atualmente:
    - O passo de trabalho de replicação de fusão não é suportado. 
    - O Leitor de Fila não é apoiado. 
    - A cápsula de comando ainda não está suportada.
  - A SQL Managed Instance não consegue aceder a recursos externos, por exemplo, partilhas de rede via robocopia. 
  - Os Serviços de Análise de Servidores SQL não são suportados.
- As notificações são parcialmente suportadas.
- A notificação por e-mail é suportada, embora exija que configuure um perfil de Correio de Base de Dados. O Agente do Servidor SQL pode utilizar apenas um perfil de Correio de base de dados, e deve ser chamado `AzureManagedInstance_dbmail_profile` de . 
  - Pager não é apoiado.
  - NetSend não é apoiado.
  - Os alertas ainda não estão suportados.
  - Os proxies não são apoiados.
- O EventLog não é suportado.

As seguintes funcionalidades do Agente SQL não são suportadas:

- Proxies
- Agendamento de postos de trabalho num CPU ocioso
- Permitir ou desativar um agente
- Alertas

Para obter informações sobre o Agente do Servidor [SQL,](/sql/ssms/agent/sql-server-agent)consulte o Agente do Servidor SQL .

### <a name="tables"></a>Tabelas

Os seguintes tipos de tabela não são suportados:

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql) (Polibase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (não suportado apenas no nível de finalidade geral)

Para obter informações sobre como criar e alterar tabelas, consulte [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) e [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funcionalidades

### <a name="bulk-insert--openrowset"></a>Inserção a granel / OPENROWSET

A SQL Managed Instance não consegue aceder a partilhas de ficheiros e pastas Windows, pelo que os ficheiros devem ser importados a partir do armazenamento do Azure Blob:

- `DATASOURCE` é necessário no `BULK INSERT` comando enquanto importa ficheiros do armazenamento Azure Blob. Ver [INSERÇÃO A GRANEL](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` é necessário na `OPENROWSET` função quando ler o conteúdo de um ficheiro a partir do armazenamento da Azure Blob. Ver [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` pode ser usado para ler dados a partir de Azure SQL Database, Azure SQL Managed Instance, ou SQL Server instances. Outras fontes, como bases de dados Oracle ou ficheiros Excel, não são suportadas.

### <a name="clr"></a>CLR

Uma sql Managed Instance não pode aceder a partilhas de ficheiros e pastas Windows, pelo que aplicam-se os seguintes constrangimentos:

- Só `CREATE ASSEMBLY FROM BINARY` é apoiado. Consulte [O CONJUNTO CREATE FROM BINARY](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` não é apoiado. Consulte [O CONJUNTO CREATE A PARTIR DE FICHEIRO](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` não pode fazer referência a ficheiros. Consulte [o CONJUNTO ALTER](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Correio da Base de Dados (db_mail)
 - `sp_send_dbmail` não pode enviar anexos usando @file_attachments parâmetro. O sistema de ficheiros local e as ações externas ou o Azure Blob Storage não estão acessíveis a partir deste procedimento.
 - Consulte as questões conhecidas relacionadas com `@query` o parâmetro e a autenticação.
 
### <a name="dbcc"></a>DBCC

As declarações não documentadas do DBCC que estão ativadas no SQL Server não são suportadas em SQL Managed Instance.

- Apenas um número limitado de bandeiras globais de rastreio são suportados. O nível da `Trace flags` sessão não é suportado. Ver [trace flags](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) e [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) trabalham com o número limitado de trace-flags globais.
- [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) com opções REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST e REPAIR_REBUILD não podem ser utilizados porque a base de dados não pode ser definida no `SINGLE_USER` modo - ver [diferenças DE BASE DE DADOS ALTER](#alter-database-statement). A corrupção na base de dados potencial é tratada pela equipa de apoio do Azure. Contacte o suporte do Azure se houver alguma indicação de corrupção na base de dados.

### <a name="distributed-transactions"></a>Transações distribuídas

MsDTC e [transações elásticas](../database/elastic-transactions-overview.md) atualmente não são suportados em SQL Managed Instance.

### <a name="extended-events"></a>Eventos Alargados

Alguns alvos específicos do Windows para Eventos Prolongados (XEvents) não são suportados:

- O `etw_classic_sync` alvo não é apoiado. Guarde `.xel` ficheiros no armazenamento Azure Blob. Consulte [etw_classic_sync alvo.](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)
- O `event_file` alvo não é apoiado. Guarde `.xel` ficheiros no armazenamento Azure Blob. Consulte [event_file alvo.](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)

### <a name="external-libraries"></a>Bibliotecas externas

As bibliotecas externas in-database R e Python são suportadas em pré-visualização pública limitada. Ver [Serviços de Machine Learning em Azure SQL Managed Instance (pré-visualização)](machine-learning-services-overview.md).

### <a name="filestream-and-filetable"></a>Filestream e FileTable

- Os dados do fluxo de ficheiros não são suportados.
- A base de dados não pode conter grupos de ficheiros com `FILESTREAM` dados.
- `FILETABLE` não é apoiado.
- As mesas não podem ter `FILESTREAM` tipos.
- As seguintes funções não são suportadas:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Para obter mais informações, consulte [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) e [FileTables](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Pesquisa semântica de texto completo

[A Busca Semântica](/sql/relational-databases/search/semantic-search-sql-server) não é apoiada.

### <a name="linked-servers"></a>Servidores ligados

Servidores ligados em SQL Managed Instance suportam um número limitado de alvos:

- Os alvos suportados são sql Gestd Instance, SQL Database, Azure Synapse SQL e SQL Server. 
- Os servidores ligados não suportam transações writable distribuídas (MS DTC).
- Os alvos que não são suportados são ficheiros, Serviços de Análise e outros RDBMS. Tente utilizar a importação de CSV nativa do Azure Blob Storage utilizando `BULK INSERT` ou como alternativa para a importação de `OPENROWSET` ficheiros.

Operações: 

- As transações de escrita cruzadas não são suportadas.
- `sp_dropserver` é suportado para deixar cair um servidor ligado. Ver [sp_dropserver.](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)
- A `OPENROWSET` função pode ser usada para executar consultas apenas em instâncias do SQL Server. Podem ser geridos, no local ou em máquinas virtuais. Ver [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- A `OPENDATASOURCE` função pode ser usada para executar consultas apenas em instâncias do SQL Server. Podem ser geridos, no local ou em máquinas virtuais. Apenas os `SQLNCLI` `SQLNCLI11` valores e `SQLOLEDB` valores são suportados como fornecedor. Um exemplo é `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Ver [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- Os servidores ligados não podem ser utilizados para ler ficheiros (Excel, CSV) a partir das partilhas de rede. Tente utilizar [o BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) ou o [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) que lê ficheiros CSV do Azure Blob Storage. Acompanhe este pedido no [item de feedback de instância gerida do SQL](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

As tabelas externas que referenciam os ficheiros no armazenamento hdfs ou Azure Blob não são suportadas. Para obter informações sobre a PolyBase, consulte [a PolyBase](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replicação

- Os tipos de replicação snapshot e bidis são suportados. A replicação de fusão, a replicação peer-to-peer e as subscrições updatable não são suportadas.
- [A replicação transacional](replication-transactional-overview.md) está disponível para pré-visualização pública em SQL Managed Instance com alguns constrangimentos:
    - Todos os tipos de participantes de replicação (Editor, Distribuidor, Assinante Pull e Push) podem ser colocados em SQL Managed Instance, mas o editor e o distribuidor devem estar tanto na nuvem como em ambos no local.
    - SQL Managed Instance pode comunicar com as versões recentes do SQL Server. Consulte a matriz de [versões suportadas](replication-transactional-overview.md#supportability-matrix) para obter mais informações.
    - A Replicação Transacional tem alguns [requisitos adicionais de rede.](replication-transactional-overview.md#requirements)

Para obter mais informações sobre a configuração da replicação transacional, consulte os seguintes tutoriais:
- [Replicação entre um editor DO SQL MI e assinante do SQL MI](replication-between-two-instances-configure-tutorial.md)
- [Replicação entre um editor MM SQL, distribuidor SQL MI e subscritor do SQL Server](replication-two-instances-and-sql-server-configure-tutorial.md)

### <a name="restore-statement"></a>Declaração DE RESTAURO 

- Sintaxe suportada:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Sintaxe não suportada:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Origem: 
  - `FROM URL` (Armazenamento Azure Blob) é a única opção suportada.
  - `FROM DISK`/`TAPE`/dispositivo de reserva não é suportado.
  - Os conjuntos de reserva não são suportados.
- `WITH` opções não são suportadas. As tentativas de `WITH` `DIFFERENTIAL` restauro, `STATS` incluindo, `REPLACE` etc., falharão.
- `ASYNC RESTORE`: A restauração continua mesmo que a ligação do cliente se rompa. Se a sua ligação for deixada cair, pode verificar a `sys.dm_operation_status` vista para o estado de uma operação de restauro e para uma base de dados CREATE e DROP. Ver [sys.dm_operation_status.](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 

As seguintes opções de base de dados são definidas ou ultrapassadas e não podem ser alteradas mais tarde: 

- `NEW_BROKER` se o corretor não estiver ativado no ficheiro .bak. 
- `ENABLE_BROKER` se o corretor não estiver ativado no ficheiro .bak. 
- `AUTO_CLOSE=OFF` se uma base de dados no ficheiro .bak tiver `AUTO_CLOSE=ON` . 
- `RECOVERY FULL` se uma base de dados no ficheiro .bak tiver `SIMPLE` ou o modo de `BULK_LOGGED` recuperação.
- Um grupo de ficheiros otimizado pela memória é adicionado e chamado XTP se não estivesse no ficheiro source .bak. 
- Qualquer grupo de ficheiros otimizado pela memória existente é renomeado para XTP. 
- `SINGLE_USER` e `RESTRICTED_USER` as opções são convertidas para `MULTI_USER` .

Limitações: 

- Cópias de segurança das bases de dados corrompidas podem ser restauradas dependendo do tipo de corrupção, mas não serão tomadas cópias de segurança automatizadas até que a corrupção seja corrigida. Certifique-se de que funciona `DBCC CHECKDB` na fonte SQL Managed Instance e utilize cópia de segurança `WITH CHECKSUM` para evitar este problema.
- A restauração do `.BAK` ficheiro de uma base de dados que contenha qualquer limitação descrita neste documento (por exemplo, ou `FILESTREAM` `FILETABLE` objetos) não pode ser restaurada em SQL Managed Instance.
- `.BAK` ficheiros que contêm vários conjuntos de backup não podem ser restaurados. 
- `.BAK` ficheiros que contêm vários ficheiros de registo não podem ser restaurados.
- As cópias de segurança que contenham bases de dados maiores do que 8 TB, objetos OLTP ativos na memória ou um número de ficheiros que excedam 280 ficheiros por exemplo não podem ser restaurados numa instância de Finalidade Geral. 
- As cópias de segurança que contenham bases de dados maiores do que 4 objetos OLTP com o tamanho total superior ao tamanho descrito nos [limites de recursos](resource-limits.md) não podem ser restaurados em caso De Produção Crítica.
Para obter informações sobre as declarações de restauro, consulte [as declarações RESTORE](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > As mesmas limitações aplicam-se à operação de restauro de pontos de acesso incorporados. Como exemplo, a base de dados de Finalidade Geral superior a 4 TB não pode ser restaurada em caso de Critical Business. A base de dados Critical de Negócios com ficheiros OLTP de memória ou mais de 280 ficheiros não pode ser restaurada em caso de Finalidade Geral.

### <a name="service-broker"></a>Corretor de serviços

O corretor de serviços de cross-instance não é suportado:

- `sys.routes`: Como pré-requisito, deve selecionar o endereço de sys.routes. O endereço deve ser LOCAL em todas as rotas. Ver [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Não pode usar `CREATE ROUTE` com outra coisa que `ADDRESS` `LOCAL` não. Ver [ROTA CREATE](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Não pode usar `ALTER ROUTE` com outra coisa que `ADDRESS` `LOCAL` não. Ver [ROTA ALTER](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Procedimentos, funções e gatilhos armazenados

- `NATIVE_COMPILATION` não é suportado no nível de propósito geral.
- As [seguintes opções sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) não são suportadas: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` não é apoiado. Ver [sp_execute_external_scripts.](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)
- `xp_cmdshell` não é apoiado. Ver [xp_cmdshell.](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)
- `Extended stored procedures`não são apoiados, o que inclui `sp_addextendedproc`   `sp_dropextendedproc` e. Consulte [os procedimentos de armazenação alargados](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`E `sp_attach_single_file_db` `sp_detach_db` não são apoiados. Ver [sp_attach_db,](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql) [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)e [sp_detach_db.](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)

### <a name="system-functions-and-variables"></a>Funções e variáveis do sistema

As seguintes variáveis, funções e vistas retornam diferentes resultados:

- `SERVERPROPERTY('EngineEdition')` devolve o valor 8. Esta propriedade identifica exclusivamente uma SQL Managed Instance. Ver [SERVERProPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` retorna NU PORQUE o conceito de instância tal como existe para o SQL Server não se aplica a SQL Managed Instance. Ver [SERVERPROPERTY ('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` retorna um nome "conectável" DNS completo, por exemplo, my-managed-instance.wcus17662feb9ce98.database.windows.net. Ver [ @SERVERNAME @](/sql/t-sql/functions/servername-transact-sql) 
- `SYS.SERVERS` retorna um nome "conectável" DNS completo, como `myinstance.domain.database.windows.net` para as propriedades "nome" e "data_source". Ver [Sys. SERVIDORES](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` retorna NU PORQUE o conceito de serviço tal como existe para o SQL Server não se aplica a SQL Managed Instance. Ver [ @SERVICENAME @](/sql/t-sql/functions/servicename-transact-sql)
- `SUSER_ID` é apoiado. Devolve NUD Se o login AZure AD não estiver em sys.syslogins. Ver [SUSER_ID.](/sql/t-sql/functions/suser-id-transact-sql) 
- `SUSER_SID` não é apoiado. Os dados errados são devolvidos, o que é uma questão temporária conhecida. Ver [SUSER_SID.](/sql/t-sql/functions/suser-sid-transact-sql) 

## <a name="environment-constraints"></a><a name="Environment"></a>Constrangimentos ambientais

### <a name="subnet"></a>Sub-rede
-  Não é possível colocar quaisquer outros recursos (por exemplo, máquinas virtuais) na sub-rede onde implementou a sua SQL Managed Instance. Implemente estes recursos usando uma sub-rede diferente.
- A sub-rede deve ter um número suficiente de [endereços IP](connectivity-architecture-overview.md#network-requirements)disponíveis . O mínimo é 16, enquanto a recomendação é ter pelo menos 32 endereços IP na sub-rede.
- [Os pontos finais de serviço não podem ser associados à sub-rede sql Managed Instance](connectivity-architecture-overview.md#network-requirements). Certifique-se de que a opção de pontos finais de serviço é desativada quando criar a rede virtual.
- O número de vCores e tipos de casos que pode implementar numa região tem [alguns constrangimentos e limites.](resource-limits.md#regional-resource-limitations)
- Existem algumas [regras de segurança que devem ser aplicadas na sub-rede.](connectivity-architecture-overview.md#network-requirements)

### <a name="vnet"></a>VNET
- O VNet pode ser implementado utilizando o Modelo de Recurso - O modelo clássico para o VNet não é suportado.
- Após a criação de um SQL Managed Instance, a deslocação da SQL Managed Instance ou VNet para outro grupo de recursos ou subscrição não é suportada.
- Alguns serviços como Ambientes de Serviço de Aplicações, aplicações lógicas e sql Managed Instance (utilizados para geo-replicação, replicação transacional ou através de servidores ligados) não podem aceder ao SQL Managed Instance em diferentes regiões se os seus VNets estiverem ligados através de [um espreguite global.](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) Pode ligar-se a estes recursos através do ExpressRoute ou VNet-to-VNet através do VNet Gateways.

### <a name="failover-groups"></a>Grupos de ativação pós-falha
As bases de dados do sistema não são replicadas para a instância secundária num grupo de failover. Portanto, os cenários que dependem de objetos das bases de dados do sistema serão impossíveis na instância secundária, a menos que os objetos sejam criados manualmente no secundário.

### <a name="failover-groups"></a>Grupos de ativação pós-falha
As bases de dados do sistema não são replicadas para a instância secundária num grupo de failover. Portanto, os cenários que dependem de objetos das bases de dados do sistema serão impossíveis na instância secundária, a menos que os objetos sejam criados manualmente no secundário.

### <a name="tempdb"></a>TEMPDB

O tamanho máximo do ficheiro `tempdb` não pode ser superior a 24 GB por núcleo num nível de Finalidade Geral. O tamanho máximo `tempdb` num nível Business Critical é limitado pelo tamanho de armazenamento SQL Managed Instance. `Tempdb` o tamanho do ficheiro de registo é limitado a 120 GB no nível de Finalidade Geral. Algumas consultas podem devolver um erro se precisarem de mais de 24 GB por núcleo `tempdb` ou se produzirem mais de 120 GB de dados de registo.

### <a name="msdb"></a>MSDB

Os seguintes esquemas MSDB em SQL Managed Instance devem ser propriedade das respetivas funções predefinidas:

- Funções gerais
  - TargetServersRole
- [Funções de base de dados fixas](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [Funções databaseMail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile):
  - DatabaseMailUserRole
- [Funções de serviços de integração:](https://docs.microsoft.com/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15)
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> Alterar os nomes de funções predefinidos, nomes de esquemas e proprietários de esquemas pelos clientes terá impacto no funcionamento normal do serviço. Quaisquer alterações feitas a estes serão revertidas para os valores predefinidos logo que detetadas, ou na próxima atualização de serviço, o mais tardar, para garantir o funcionamento normal do serviço.

### <a name="error-logs"></a>Registos de erros

SQL Gestd Instance coloca informações verbosas em registos de erro. Existem muitos eventos internos do sistema que são registados no registo de erros. Utilize um procedimento personalizado para ler registos de erros que filtram algumas entradas irrelevantes. Para obter mais informações, consulte [a extensão de exemplo gerida do SQL – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) ou [SQL Para](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) o Azure Data Studio.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a SQL Managed Instance, consulte [o que é a SqL Managed Instance?](sql-managed-instance-paas-overview.md)
- Para obter uma lista de funcionalidades e comparação, consulte [a comparação de funcionalidades Azure SQL Managed Instance](../database/features-comparison.md).
- Para atualizações de lançamento e problemas conhecidos, consulte [as notas de lançamento da SQL Managed Instance](../database/doc-changes-updates-release-notes.md)
- Para um arranque rápido que lhe mostre como criar uma nova SQL Managed Instance, consulte [Criar uma SqL Managed Instance](instance-create-quickstart.md).
