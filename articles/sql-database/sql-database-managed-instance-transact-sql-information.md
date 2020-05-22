---
title: Diferenças t-SQL de instância gerida
description: Este artigo discute as diferenças T-SQL entre um caso gerido na Base de Dados Azure SQL e no Servidor SQL
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova, danil
ms.date: 03/11/2020
ms.custom: seoapril2019
ms.openlocfilehash: bc5aed6c00afc5ffc9d44f43a9163b2ae2de1e17
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772306"
---
# <a name="managed-instance-t-sql-differences-and-limitations"></a>Diferenças e limitações t-SQL geridas

Este artigo resume e explica as diferenças de sintaxe e comportamento entre a Base de Dados Azure SQL gerida e o motor de base de dados do Servidor SQL no local. A opção de implementação de instância gerida proporciona alta compatibilidade com o motor de base de dados do Servidor SQL no local. A maioria das funcionalidades do motor de base de dados Do SQL Server são suportadas numa instância gerida.

![Migração](./media/sql-database-managed-instance/migration.png)

Existem algumas limitações paaS que são introduzidas em Caso Gerido e algumas alterações de comportamento em comparação com o Servidor SQL. As diferenças dividem-se nas seguintes categorias:<a name="Differences"></a>

- [A disponibilidade](#availability) inclui as diferenças em [Sempre Em Grupos de Disponibilidade](#always-on-availability-groups) e [backups](#backup).
- [A segurança](#security) inclui as diferenças na [auditoria,](#auditing) [certificados,](#certificates) [credenciais,](#credential) [fornecedores criptográficos,](#cryptographic-providers) [logins e utilizadores,](#logins-and-users)e a chave de serviço e chave principal de [serviço.](#service-key-and-service-master-key)
- [A configuração](#configuration) inclui as diferenças na extensão do [pool tampão,](#buffer-pool-extension) [na colagem,](#collation)nos níveis de [compatibilidade,](#compatibility-levels) [no espelho da base de dados,](#database-mirroring) [nas opções de base](#database-options)de dados, no [Agente servidor SQL](#sql-server-agent)e [nas opções de mesa.](#tables)
- [As funcionalidades](#functionalities) incluem [INSERÇÃO A GRANEL/OPENROWSET,](#bulk-insert--openrowset) [CLR,](#clr) [DBCC,](#dbcc) [transações distribuídas, eventos alargados,](#distributed-transactions) [bibliotecas externas,](#external-libraries) [filestream e FileTable,](#filestream-and-filetable)Pesquisa [extended events](#extended-events) [Semântica de texto completo,](#full-text-semantic-search) [servidores ligados,](#linked-servers) [PolyBase,](#polybase) [Replicação,](#replication) [RESTAURO,](#restore-statement)Corretor de [Serviços, procedimentos armazenados, funções e gatilhos.](#stored-procedures-functions-and-triggers) [Service Broker](#service-broker)
- [Configurações ambientais](#Environment) tais como VNets e configurações de sub-rede.

A maioria destas características são constrangimentos arquitetónicos e representam características de serviço.

As questões conhecidas temporáriamente que são descobertas em instância gerida e que serão resolvidas no futuro são descritas na página de notas de [lançamento](sql-database-release-notes.md).

## <a name="availability"></a>Disponibilidade

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Sempre em Grupos de Disponibilidade

[A elevada disponibilidade](sql-database-high-availability.md) é incorporada em instância gerida e não pode ser controlada pelos utilizadores. As seguintes declarações não são apoiadas:

- [CRIAR ENDPOINT ... PARA DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [CRIAR GRUPO DE DISPONIBILIDADE](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER GRUPO DE DISPONIBILIDADE](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- A cláusula [DE SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) da declaração [alter base](/sql/t-sql/statements/alter-database-transact-sql) de dados

### <a name="backup"></a>Cópia de segurança

As instâncias geridas têm cópias de segurança automáticas, para que os utilizadores possam criar cópias de `COPY_ONLY` dados completas. As cópias de segurança diferenciais, de registo e de instantâneo sem suporte não são suportadas.

- Com um caso gerido, pode fazer o back up a uma base de dados de instâncias apenas para uma conta de armazenamento Azure Blob:
  - Só `BACKUP TO URL` é apoiado.
  - `FILE`E os `TAPE` dispositivos de reserva não são suportados.
- A maioria das `WITH` opções gerais são apoiadas.
  - `COPY_ONLY`é obrigatório.
  - `FILE_SNAPSHOT`não é apoiado.
  - Opções de fita: `REWIND` , , , e não são `NOREWIND` `UNLOAD` `NOUNLOAD` suportados.
  - Opções específicas de registo: `NORECOVERY` , e não são `STANDBY` `NO_TRUNCATE` suportadas.

Limitações: 

- Com um caso gerido, pode fazer o backup de uma base de dados de instância seletiva com até 32 faixas, o que é suficiente para bases de dados até 4 TB se for utilizada uma compressão de reserva.
- Não é possível executar numa base de dados encriptada com encriptação transparente de `BACKUP DATABASE ... WITH COPY_ONLY` dados (TDE) gerida pelo serviço. O TDE gerido pelo serviço obriga as cópias de segurança a serem encriptadas com uma chave TDE interna. A chave não pode ser exportada, por isso não podes restaurar a reserva. Utilize cópias de segurança automáticas e restauro pontual, ou utilize [tDE (BYOK) gerido pelo cliente.](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key) Também pode desativar a encriptação na base de dados.
- O tamanho máximo da risca de reserva utilizando o comando numa instância gerida é de `BACKUP` 195 GB, que é o tamanho máximo da bolha. Aumente o número de listras no comando de reserva para reduzir o tamanho das listras individuais e permaneça dentro deste limite.

    > [!TIP]
    > Para contornar esta limitação, quando faz o backup a partir de uma base de dados do SQL Server num ambiente no local ou numa máquina virtual, pode:
    >
    > - Recua em `DISK` vez de recuar `URL` até...
    > - Faça o upload dos ficheiros de reserva para o armazenamento blob.
    > - Restaurar na instância gerida.
    >
    > O `Restore` comando numa instância gerida suporta tamanhos de bolha maiores nos ficheiros de backup porque um tipo de blob diferente é usado para armazenamento dos ficheiros de backup carregados.

Para obter informações sobre backups usando T-SQL, consulte [BACKUP](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Segurança

### <a name="auditing"></a>Auditoria

As principais diferenças entre a auditoria em bases de dados em Bases de Dados Azure SQL e bases de dados no Servidor SQL são:

- Com a opção de implementação de instância gerida na Base de Dados Azure SQL, a auditoria funciona ao nível do servidor. Os `.xel` ficheiros de registo são armazenados no armazenamento da Blob Azure.
- Com a base de dados única e as opções de implantação de piscinas elásticas na Base de Dados Azure SQL, a auditoria funciona ao nível da base de dados.
- No SQL Server no local ou em máquinas virtuais, a auditoria funciona ao nível do servidor. Os eventos são armazenados no sistema de ficheiros ou nos registos de eventos do Windows.
 
A auditoria do XEvent em instância gerida suporta os alvos de armazenamento da Blob Azure. Os registos de ficheiros e janelas não são suportados.

As principais diferenças na `CREATE AUDIT` sintaxe para a auditoria ao armazenamento da Blob Azure são:

- É fornecida uma nova sintaxe `TO URL` que pode utilizar para especificar o URL do recipiente de armazenamento Azure Blob onde os `.xel` ficheiros são colocados.
- A sintaxe `TO FILE` não é suportada porque uma instância gerida não pode aceder a partilhas de ficheiros do Windows.

Para obter mais informações, consulte: 

- [CRIAR AUDITORIA DO SERVIDOR](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTERAR AUDITORIA DO SERVIDOR](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Auditoria](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificados

Uma instância gerida não pode aceder a partilhas de ficheiros e pastas do Windows, pelo que se aplicam os seguintes constrangimentos:

- O `CREATE FROM` / `BACKUP TO` ficheiro não é suportado para certificados.
- O `CREATE` / `BACKUP` certificado não `FILE` / `ASSEMBLY` é suportado. Ficheiros de chaves privadas não podem ser usados. 

Consulte O [CERTIFICADO](/sql/t-sql/statements/backup-certificate-transact-sql)DE [CRIAÇÃO](/sql/t-sql/statements/create-certificate-transact-sql) e O CERTIFICADO DE BACKUP . 
 
**Suposição:** Em vez de criar cópia de segurança do certificado e restaurar a cópia de segurança, [obtenha o conteúdo binário do certificado e a chave privada, guarde-o como ficheiro .sql e crie](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)a partir de binário:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Credencial

Apenas o Cofre de Chaves Azure e `SHARED ACCESS SIGNATURE` as identidades são suportadas. Os utilizadores do Windows não são suportados.

Ver [CRIAR CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql) e [ALTERAR CREDENTIAL](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Fornecedores criptográficos

Uma instância gerida não pode aceder a ficheiros, por isso os fornecedores criptográficos não podem ser criados:

- `CREATE CRYPTOGRAPHIC PROVIDER`não é apoiado. Consulte [CRIAR FORNECEDOR CRIPTOGRÁFICO](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER`não é apoiado. Consulte [ALTER CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Inícios de sessão e utilizadores

- Os logins SQL criados pela `FROM CERTIFICATE` `FROM ASYMMETRIC KEY` utilização, e são `FROM SID` suportados. Ver [CRIAR LOGIN](/sql/t-sql/statements/create-login-transact-sql).
- Os diretores de servidores azure Ative Directory (Azure AD) (logins) criados com a sintaxe [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) ou o [CREATE USER FROM LOGIN [Azure AD Login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) são suportados. Estes logins são criados ao nível do servidor.

    A instância gerida suporta os principais da base de dados Azure AD com a sintaxe `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` . Esta funcionalidade também é conhecida como Azure AD continha utilizadores de base de dados.

- Os logins do Windows criados com a `CREATE LOGIN ... FROM WINDOWS` sintaxe não são suportados. Utilize logins e utilizadores de Diretório Ativo Azure.
- O utilizador da AD Azure que criou a instância tem [privilégios administrativos ilimitados.](sql-database-manage-logins.md)
- Os utilizadores de bases de dados ad azure não administradores podem ser criados através da `CREATE USER ... FROM EXTERNAL PROVIDER` sintaxe. Ver [CREATE USER ... Do FORNECEDOR EXTERNO.](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)
- Os diretores do servidor Azure AD (logins) suportam as funcionalidades SQL dentro de uma instância gerida apenas. As funcionalidades que requerem interação transversal, independentemente de estarem dentro do mesmo inquilino da AD Azure ou de diferentes inquilinos, não são suportadas para utilizadores da AD Azure. Exemplos de tais características são:

  - Replicação transacional SQL.
  - Servidor de ligação.

- Definir um login Azure AD mapeado para um grupo DeA Azure como o proprietário da base de dados não é suportado.
- A personificação dos diretores de servidores Azure AD utilizando outros diretores de AD Azure é suportada, como a cláusula [EXECUTE AS.](/sql/t-sql/statements/execute-as-transact-sql) EXECUTAR AS limitações são:

  - Execute COMO UTILIZADOR não é suportado para utilizadores de Anúncios Azure quando o nome difere do nome de login. Um exemplo é quando o utilizador é criado através do utilizador criar sintaxe CREATE USER [myAadUser] FROM LOGIN [ ] e a john@contoso.com personificação é tentada através do EXEC AS USER = _myAadUser_. Quando criar um **UTILIZADOR** a partir de um servidor Azure AD (login), especifique o user_name como o mesmo login_name do **LOGIN**.
  - Apenas os principais de nível SQL (logins) que fazem parte da `sysadmin` função podem executar as seguintes operações que visam os principais da AD Azure:

    - EXECUTAR COMO UTILIZADOR
    - EXECUTAR COMO LOGIN

- A exportação/importação de bases de dados utilizando ficheiros bacpac são suportadas para utilizadores de AD Azure em instância gerida, utilizando [sSMS V18.4 ou posteriores](/sql/ssms/download-sql-server-management-studio-ssms), ou [SQLPackage.exe](/sql/tools/sqlpackage-download).
  - As seguintes configurações são suportadas utilizando ficheiro bacpac de base de dados: 
    - Exportar/importar uma base de dados entre diferentes casos de gestão dentro do mesmo domínio DaA Azure.
    - Exportar uma base de dados de instância gerida e importar para base de dados SQL dentro do mesmo domínio DaD Azure. 
    - Exportar uma base de dados da Base de Dados SQL e importar para instância gerida dentro do mesmo domínio DaA Azure.
    - Exportar uma base de dados de instância gerida e importar para o SQL Server (versão 2012 ou posterior).
      - Nesta configuração, todos os utilizadores de AD Azure são criados como principais de base de dados SQL (utilizadores) sem logins. O tipo de utilizadores está listado como SQL (visível como SQL_USER em sys.database_principals). As suas permissões e funções permanecem nos metadados da base de dados do SQL Server e podem ser usadas para personificação. No entanto, não podem ser utilizados para aceder e iniciar sessão no Servidor SQL utilizando as suas credenciais.

- Apenas o login principal ao nível do servidor, que é criado pelo processo de fornecimento de instâncias gerido, membros das funções do servidor, tais como `securityadmin` ou , ou outros `sysadmin` logins com a permissão ALTER ANY LOGIN ao nível do servidor podem criar diretores de servidores AD Azure (logins) na base de dados principal para por exemplo gerido.
- Se o login for um principal SQL, apenas os logins que fazem parte da `sysadmin` função podem usar o comando de criação para criar logins para uma conta Azure AD.
- O login Azure AD deve ser membro de um Anúncio Azure dentro do mesmo diretório que é usado para a instância gerida pela Base de Dados Azure SQL.
- Os diretores do servidor Azure AD (logins) são visíveis no Object Explorer a partir da pré-visualização do SQL Server Management Studio 18.0 5.
- É permitido sobrepor-se aos diretores de servidores da AD Azure (logins) com uma conta de administração Azure AD. Os diretores de servidores da Azure AD (logins) têm precedência sobre o administrador da AD Azure quando resolve o diretor e aplica permissões à instância gerida.
- Durante a autenticação, aplica-se a seguinte sequência para resolver o princípio autenticador:

    1. Se a conta AD Azure existir como diretamente mapeada para o servidor Azure AD (login), que está presente em sys.server_principals como tipo "E", conceder acesso e aplicar permissões do servidor Azure AD (login).
    2. Se a conta Azure AD for membro de um grupo Azure AD que seja mapeado para o servidor Azure AD (login), que está presente em sys.server_principals como tipo "X", conceder acesso e aplicar permissões do login do grupo Azure AD.
    3. Se a conta Azure AD for um administrador azure ad configurado por portal especial para instância gerida, que não existe em vistas de sistema de instância geridas, aplique permissões fixas especiais do administrador azure AD para por exemplo gerido (modo legado).
    4. Se a conta Azure AD existir como diretamente mapeada para um utilizador da AD Azure numa base de dados, que está presente em sys.database_principals como tipo "E", conceder acesso e aplicar permissões do utilizador da base de dados Azure AD.
    5. Se a conta Azure AD for membro de um grupo Azure AD que seja mapeado para um utilizador da AD Azure numa base de dados, que está presente em sys.database_principals como tipo "X", conceder acesso e aplicar permissões do login do grupo Azure AD.
    6. Se houver um login Azure AD mapeado para uma conta de utilizador Da AD Azure ou uma conta de grupo Azure AD, que resolve para o utilizador que está autenticando, todas as permissões deste login Azure AD são aplicadas.

### <a name="service-key-and-service-master-key"></a>Chave de serviço e chave de serviço principal

- [A cópia de segurança da chave master](/sql/t-sql/statements/backup-master-key-transact-sql) não é suportada (gerida pelo serviço De base de dados SQL).
- [A restabelecimento](/sql/t-sql/statements/restore-master-key-transact-sql) da chave master não é suportada (gerida pelo serviço De base de dados SQL).
- [A cópia](/sql/t-sql/statements/backup-service-master-key-transact-sql) de segurança da chave de serviço não é suportada (gerida pelo serviço SQL Database).
- [A restabelecimento](/sql/t-sql/statements/restore-service-master-key-transact-sql) da chave principal do serviço não é suportada (gerida pelo serviço De base de dados SQL).

## <a name="configuration"></a>Configuração

### <a name="buffer-pool-extension"></a>Extensão da piscina tampão

- [A extensão](/sql/database-engine/configure-windows/buffer-pool-extension) da piscina tampão não é suportada.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`não é apoiado. Ver [ALTER SERVER CONFIGURAÇÃO](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Agrupamento

A colagem de instância padrão é e pode ser especificada como um parâmetro de `SQL_Latin1_General_CP1_CI_AS` criação. Ver [Collations.](/sql/t-sql/statements/collations)

### <a name="compatibility-levels"></a>Níveis de compatibilidade

- Os níveis de compatibilidade suportados são 100, 110, 120, 130, 140 e 150.
- Níveis de compatibilidade abaixo de 100 não são suportados.
- O nível de compatibilidade padrão para novas bases de dados é de 140. Para bases de dados restauradas, o nível de compatibilidade permanece inalterado se for de 100 ou superior a 100.

Ver [ALTER DATABASE Nível de compatibilidade](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Espelhamento da base de dados

O espelho da base de dados não é suportado.

- `ALTER DATABASE SET PARTNER`e `SET WITNESS` as opções não são apoiadas.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING`não é apoiado.

Para mais informações, consulte [alter DATABASE SET PARTNER e SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) e CREATE [ENDPOINT ... Para DATABASE_MIRRORING.](/sql/t-sql/statements/create-endpoint-transact-sql)

### <a name="database-options"></a>Opções de base de dados

- Vários ficheiros de registo não são suportados.
- Os objetos na memória não são suportados no nível de serviço de Propósito Geral. 
- Há um limite de 280 ficheiros por instância de Propósito Geral, o que implica um máximo de 280 ficheiros por base de dados. Tanto os dados como os ficheiros de registo no nível de Propósito Geral são contados para este limite. [O nível Business Critical suporta 32.767 ficheiros por base de dados](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- A base de dados não pode conter grupos de ficheiros que contenham dados de filestream. Restaurar falha se .bak contiver `FILESTREAM` dados. 
- Todos os ficheiros são colocados no armazém do Azure Blob. IO e a entrada por ficheiro dependem do tamanho de cada ficheiro individual.

#### <a name="create-database-statement"></a>Criar declaração de BASE DE DADOS

As seguintes limitações aplicam-se `CREATE DATABASE` a:

- Ficheiros e grupos de ficheiros não podem ser definidos. 
- A `CONTAINMENT` opção não é apoiada. 
- `WITH`opções não são suportadas. 
   > [!TIP]
   > Como suver, utilize depois para definir opções de base de `ALTER DATABASE` `CREATE DATABASE` dados para adicionar ficheiros ou para definir a contenção. 

- A `FOR ATTACH` opção não é apoiada.
- A `AS SNAPSHOT OF` opção não é apoiada.

Para mais informações, consulte [CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Declaração de BASE DE DADOS ALTER

Algumas propriedades de ficheiros não podem ser definidas ou alteradas:

- Um caminho de ficheiro não pode ser especificado na `ALTER DATABASE ADD FILE (FILENAME='path')` declaração t-SQL. Retire `FILENAME` do script porque uma instância gerida coloca automaticamente os ficheiros. 
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

Para mais informações, consulte [alter DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- Ativar e desativar o Agente de Servidor SQL não é suportado em instância gerida. O Agente SQL está sempre a correr.
- As definições do Agente do Servidor SQL são lidas apenas. O procedimento `sp_set_agent_properties` não é suportado em instância gerida. 
- Tarefas
  - Os passos de trabalho da T-SQL são apoiados.
  - Apoiam-se os seguintes trabalhos de replicação:
    - Leitor de registo de transações
    - Instantâneo
    - Distribuidor
  - Os passos de trabalho do SSIS são apoiados.
  - Outros tipos de passos de trabalho não são atualmente suportados:
    - O passo do trabalho de replicação não é suportado. 
    - O Leitor de Fila não é apoiado. 
    - A concha do comando ainda não é suportada.
  - Casos geridos não podem aceder a recursos externos, por exemplo, partilhas de rede através de robocopia. 
  - Os Serviços de Análise do Servidor SQL não são suportados.
- As notificações são parcialmente suportadas.
- A notificação por e-mail é suportada, embora exija que configure um perfil de Correio de Base de Dados. O Agente servidor SQL só pode utilizar um perfil de Correio de Base de Dados, e deve ser chamado `AzureManagedInstance_dbmail_profile` . 
  - Pager não é apoiado.
  - NetSend não é suportado.
  - Os alertas ainda não são suportados.
  - Os proxies não são apoiados.
- O EventLog não é suportado.

As seguintes funcionalidades do Agente SQL não são suportadas atualmente:

- Proxies
- Agendamento de postos de trabalho numa CPU ociosa
- Habilitar ou desativar um agente
- Alertas

Para obter informações sobre o Agente servidor SQL, consulte [o Agente servidor SQL](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabelas

Os seguintes tipos de tabelanão são suportados:

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql) (Polybase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (não suportado apenas no nível de propósito geral)

Para obter informações sobre como criar e alterar tabelas, consulte [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) e [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funcionalidades

### <a name="bulk-insert--openrowset"></a>Inserção a granel / OPENROWSET

Uma instância gerida não pode aceder a partilhas de ficheiros e pastas Windows, pelo que os ficheiros devem ser importados do armazenamento do Azure Blob:

- `DATASOURCE`é exigido no `BULK INSERT` comando enquanto importa ficheiros do armazenamento Azure Blob. Ver [INSERT A GRANEL](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE`é necessário na `OPENROWSET` função quando ler o conteúdo de um ficheiro a partir do armazenamento Da Blob Azure. Ver [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET`pode ser usado para ler dados de outras bases de dados únicas Azure SQL, instâncias geridas ou instâncias do Servidor SQL. Outras fontes, como as bases de dados oracle ou os ficheiros Excel, não são suportadas.

### <a name="clr"></a>CLR

Uma instância gerida não pode aceder a partilhas de ficheiros e pastas do Windows, pelo que se aplicam os seguintes constrangimentos:

- Só `CREATE ASSEMBLY FROM BINARY` é apoiado. Ver [MONTAGEM CRIAR A PARTIR DE BINÁRIO](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE`não é apoiado. Consulte [A MONTAGEM CREATE FROM FILE](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY`não pode referência ficheiros. Ver [ALTER MONTAGEM](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Correio de Base de Dados (db_mail)
 - `sp_send_dbmail`não pode enviar anexos usando @file_attachments parâmetro. O sistema de ficheiros local e as ações externas ou o Armazenamento De Blob Azure não estão acessíveis a partir deste procedimento.
 - Consulte as questões conhecidas relacionadas com `@query` o parâmetro e a autenticação.
 
### <a name="dbcc"></a>DBCC

As declarações dBCC não documentadas que são ativadas no SQL Server não são suportadas em casos geridos.

- Apenas um número limitado de bandeiras da Global Trace são suportadas. O nível da sessão `Trace flags` não é suportado. Ver [Trace flags](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) e [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) trabalham com o número limitado de trace-flags globais.
- [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) com opções REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST e REPAIR_REBUILD não podem ser utilizados porque a base de dados não pode ser definida no `SINGLE_USER` modo - ver alter [diferenças de BASE DE DADOS](#alter-database-statement). Potenciais corrupçãos na base de dados são tratadas pela equipa de apoio do Azure. Contacte o suporte do Azure se notar a corrupção na base de dados que deve ser corrigida.

### <a name="distributed-transactions"></a>Transações distribuídas

MSDTC e [transações elásticas](sql-database-elastic-transactions-overview.md) atualmente não são suportadas em casos geridos.

### <a name="extended-events"></a>Eventos Expandidos

Alguns alvos específicos do Windows para Eventos Estendidos (XEvents) não são suportados:

- O `etw_classic_sync` alvo não é apoiado. Guarde `.xel` ficheiros no armazenamento da Blob Azure. Consulte [etw_classic_sync alvo.](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)
- O `event_file` alvo não é apoiado. Guarde `.xel` ficheiros no armazenamento da Blob Azure. Ver [event_file alvo.](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)

### <a name="external-libraries"></a>Bibliotecas externas

Na base de dados R e Python, as bibliotecas externas ainda não são suportadas. Consulte [os serviços de aprendizagem automática do servidor SQL](/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>Filestream e FileTable

- Os dados do filestream não são suportados.
- A base de dados não pode conter grupos de ficheiros com `FILESTREAM` dados.
- `FILETABLE`não é apoiado.
- As mesas não podem ter `FILESTREAM` tipos.
- As seguintes funções não são suportadas:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Para mais informações, consulte [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) e [FileTables](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Pesquisa Semântica de texto completo

[A procura semântica](/sql/relational-databases/search/semantic-search-sql-server) não é apoiada.

### <a name="linked-servers"></a>Servidores ligados

Servidores ligados em casos geridos suportam um número limitado de alvos:

- Os alvos suportados são instâncias geridas, bases de dados únicas e casos de Servidor SQL. 
- Os servidores ligados não suportam transações responsadas distribuídas (MS DTC).
- Os alvos que não são suportados são ficheiros, serviços de análise e outros RDBMS. Tente utilizar a importação nativa de CSV do Armazenamento De Blob Azure utilizando ou como alternativa para a importação de `BULK INSERT` `OPENROWSET` ficheiros.

Operações

- As transações de escrita de instâncias cruzadas não são suportadas.
- `sp_dropserver`é suportado para deixar cair um servidor ligado. Veja [sp_dropserver.](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)
- A `OPENROWSET` função só pode ser utilizada para executar consultas em casos de Servidor SQL. Podem ser geridos, no local ou em máquinas virtuais. Ver [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- A `OPENDATASOURCE` função só pode ser utilizada para executar consultas em casos de Servidor SQL. Podem ser geridos, no local ou em máquinas virtuais. Apenas os `SQLNCLI` `SQLNCLI11` , e `SQLOLEDB` valores são suportados como fornecedor. Um exemplo é `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Ver [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- Os servidores ligados não podem ser utilizados para ler ficheiros (Excel, CSV) a partir das partilhas da rede. Tente utilizar [inserção](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) a granel ou [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) que leia ficheiros CSV a partir do Armazenamento De Blob Azure. Acompanhe estes pedidos em itens de feedback de [instância gerida](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

As tabelas externas que referenciam os ficheiros no armazenamento HDFS ou Azure Blob não são suportadas. Para obter informações sobre a PolyBase, consulte [PolyBase](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replicação

- Os tipos de replicação instantânea e bidirecional são suportados. A replicação de fusão, a replicação peer-to-peer e as subscrições updatable não são suportadas.
- [A Replicação Transacional](sql-database-managed-instance-transactional-replication.md) está disponível para pré-visualização pública em instância gerida com alguns constrangimentos:
    - Todos os tipos de participantes de replicação (Editor, Distribuidor, Pull Subscriber e Push Subscriber) podem ser colocados em instâncias geridas, mas o editor e o distribuidor devem estar na nuvem ou ambos no local.
    - Casos geridos podem comunicar com as versões recentes do SQL Server. Consulte a matriz de [versões suportadas](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems) para obter mais informações.
    - A Replicação Transacional tem [alguns requisitos adicionais de rede.](sql-database-managed-instance-transactional-replication.md#requirements)

Para obter mais informações sobre a configuração da replicação transacional, consulte os seguintes tutoriais:
- [Replicação entre um editor do MI e um assinante](replication-with-sql-database-managed-instance.md)
- [Replicação entre um editor do MI, distribuidor MI e subscritor do SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)

### <a name="restore-statement"></a>Declaração de RESTAURO 

- Sintaxe apoiada:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Sintaxe não apoiada:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Origem: 
  - `FROM URL`(Armazenamento Azure Blob) é a única opção suportada.
  - `FROM DISK`/`TAPE`/dispositivo de reserva não é suportado.
  - Os conjuntos de reserva não são suportados.
- `WITH`opções não são suportadas, tais como não `DIFFERENTIAL` ou `STATS` .
- `ASYNC RESTORE`: A restauração continua mesmo que a ligação do cliente se rompa. Se a sua ligação for abandonada, pode verificar `sys.dm_operation_status` a visualização do estado de uma operação de restauro e para uma base de dados CREATE e DROP. Ver [sys.dm_operation_status.](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 

As seguintes opções de base de dados são definidas ou ultrapassadas e não podem ser alteradas mais tarde: 

- `NEW_BROKER`se o corretor não estiver ativado no ficheiro .bak. 
- `ENABLE_BROKER`se o corretor não estiver ativado no ficheiro .bak. 
- `AUTO_CLOSE=OFF`se uma base de dados no ficheiro .bak tiver `AUTO_CLOSE=ON` . 
- `RECOVERY FULL`se uma base de dados no ficheiro .bak tiver ou modo de `SIMPLE` `BULK_LOGGED` recuperação.
- Um grupo de ficheiros otimizado pela memória é adicionado e chamado XTP se não estivesse no ficheiro fonte .bak. 
- Qualquer grupo de ficheiros otimizado para memória existente é renomeado para XTP. 
- `SINGLE_USER`e `RESTRICTED_USER` as opções são convertidas para `MULTI_USER` .

Limitações: 

- Os backups das bases de dados corrompidas podem ser restaurados dependendo do tipo de corrupção, mas os backups automatizados não serão tomados até que a corrupção seja corrigida. Certifique-se de que executa a instância de origem e utilize cópias de `DBCC CHECKDB` segurança para evitar este `WITH CHECKSUM` problema.
- A restauração do ficheiro de uma base de `.BAK` dados que contenha qualquer limitação descrita neste documento (por exemplo, `FILESTREAM` ou `FILETABLE` objetos) não pode ser restaurada em Instância Gerida.
- `.BAK`ficheiros que contenham vários conjuntos de backup não podem ser restaurados. 
- `.BAK`ficheiros que contenham vários ficheiros de registo não podem ser restaurados.
- As cópias de segurança que contenham bases de dados superiores a 8 TB, objetos OLTP ativos na memória ou número de ficheiros que excederiam 280 ficheiros por instância não podem ser restaurados numa instância de Propósito Geral. 
- As cópias de segurança que contenham bases de dados superiores a 4 tb ou objetos OLTP em memória com o tamanho total maior do que o tamanho descrito nos [limites](sql-database-managed-instance-resource-limits.md) de recursos não podem ser restauradas em instância Business Critical.
Para obter informações sobre as declarações de restauro, consulte [as declarações do RESTORE](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > As mesmas limitações aplicam-se à operação de restauro ponto-a-tempo incorporado. A seu exemplo, a base de dados General Purpose superior a 4 TB não pode ser restaurada em instância seleções críticas. A base de dados Business Critical com ficheiros OLTP em memória ou mais de 280 ficheiros não pode ser restaurada na instância De propósito Geral.

### <a name="service-broker"></a>Corretor de serviços

O corretor de serviços de instâncias cruzadas não é suportado:

- `sys.routes`: Como pré-requisito, deve selecionar o endereço a partir de sys.routes. O endereço deve ser LOCAL em todas as rotas. Ver [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Não pode usar com outra coisa que não `CREATE ROUTE` `ADDRESS` seja `LOCAL` . Ver [CREATE ROUTE](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Não pode usar com outra coisa que não `ALTER ROUTE` `ADDRESS` seja `LOCAL` . Ver [ALTER ROUTE](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Procedimentos, funções e gatilhos armazenados

- `NATIVE_COMPILATION`não é suportado no nível de Propósito Geral.
- As [seguintes opções sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) não são suportadas: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts`não é apoiado. Veja [sp_execute_external_scripts.](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)
- `xp_cmdshell`não é apoiado. Veja [xp_cmdshell.](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)
- `Extended stored procedures`não são suportados, o que inclui `sp_addextendedproc`   e `sp_dropextendedproc` . Ver [Procedimentos armazenados alargados](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`E `sp_attach_single_file_db` `sp_detach_db` não são apoiados. Ver [sp_attach_db,](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql) [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)e [sp_detach_db.](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)

### <a name="system-functions-and-variables"></a>Funções e variáveis do sistema

As seguintes variáveis, funções e pontos de vista retornam resultados diferentes:

- `SERVERPROPERTY('EngineEdition')`devolve o valor 8. Esta propriedade identifica de forma única um caso gerido. Ver [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')`devoluções NULO porque o conceito de instância tal como existe para o SQL Server não se aplica a uma instância gerida. Ver [SERVERPROPERTY ('Nome de instância')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME`devolve um nome completo de DNS "conectável", por exemplo, my-managed-instance.wcus17662feb9ce98.database.windows.net. Ver [ @SERVERNAME @](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS`devolve um nome completo de DNS "conectável", como `myinstance.domain.database.windows.net` para as propriedades "nome" e "data_source". Ver [Sys. SERVIDORES](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME`devoluções NULO porque o conceito de serviço tal como existe para o SQL Server não se aplica a uma instância gerida. Ver [ @SERVICENAME @](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID`é apoiado. Devolve NULO se o login Azure AD não estiver em sys.syslogins. Veja [SUSER_ID.](/sql/t-sql/functions/suser-id-transact-sql) 
- `SUSER_SID`não é apoiado. Os dados errados são devolvidos, o que é uma questão temporária conhecida. Veja [SUSER_SID.](/sql/t-sql/functions/suser-sid-transact-sql) 

## <a name="environment-constraints"></a><a name="Environment"></a>Restrições ambientais

### <a name="subnet"></a>Subrede
-  Não é possível colocar outros recursos (por exemplo, máquinas virtuais) na sub-rede onde implementou a sua instância gerida. Implementar estes recursos usando uma subrede diferente.
- A subnet deve dispor de um número suficiente de [endereços IP](sql-database-managed-instance-connectivity-architecture.md#network-requirements)disponíveis . O mínimo é de 16, enquanto a recomendação é ter pelo menos 32 endereços IP na sub-rede.
- [Os pontos finais do serviço não podem ser associados à sub-rede da instância gerida](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Certifique-se de que a opção de pontos finais do serviço está desativada quando criar a rede virtual.
- O número de vCores e tipos de casos que pode implementar numa região tem [alguns constrangimentos e limites.](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)
- Há algumas regras de [segurança que devem ser aplicadas na sub-rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>VNET
- A VNet pode ser implantada utilizando o Modelo de Recurso - O Modelo Clássico para VNet não é suportado.
- Após a criação de uma instância gerida, a mudança da instância gerida ou vNet para outro grupo de recursos ou subscrição não é suportada.
- Alguns serviços, como ambientes de serviço de aplicação, aplicações lógicas e instâncias geridas (usadas para geo-replicação, replicação transacional ou através de servidores ligados) não podem aceder a instâncias geridas em diferentes regiões se os seus VNets estiverem ligados através [de peering global.](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) Pode ligar-se a estes recursos através do ExpressRoute ou VNet-to-VNet através de Gateways VNet.

### <a name="tempdb"></a>TEMPDB

O tamanho máximo do ficheiro não pode ser superior a `tempdb` 24 GB por núcleo num nível de Propósito Geral. O tamanho máximo `tempdb` num nível Business Critical é limitado pelo tamanho de armazenamento por exemplo. `Tempdb`o tamanho do ficheiro de registo está limitado a 120 GB no nível de Propósito Geral. Algumas consultas podem devolver um erro se precisarem de mais de 24 GB por núcleo `tempdb` ou se produzirem mais de 120 GB de dados de registo.

### <a name="msdb"></a>MSDB

Os seguintes schemas de MSDB em instância gerida devem ser propriedade das respetivas funções predefinidas:

- Funções gerais
  - TargetServersRole
- [Funções fixas de base de dados](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [Funções DatabaseMail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile):
  - Base de DadosMailUserRole
- [Funções de serviços de integração:](https://docs.microsoft.com/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15)
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> A alteração dos nomes de papéis pré-definidos, nomes de esquemas e proprietários de esquemas por parte dos clientes terá impacto no funcionamento normal do serviço. Quaisquer alterações feitas a estes serão revertidas para os valores predefinidos logo que detetados, ou na próxima atualização de serviço, o mais tardar para garantir o funcionamento normal do serviço.

### <a name="error-logs"></a>Registos de erros

Uma instância gerida coloca informações verbosas em registos de erros. Há muitos eventos do sistema interno que estão registados no registo de erros. Utilize um procedimento personalizado para ler registos de erros que filtram algumas entradas irrelevantes. Para mais informações, consulte a [exemplo gerida – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) ou [extensão de instância gerida (pré-visualização)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) para o Azure Data Studio.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre casos geridos, consulte [O que é um caso gerido?](sql-database-managed-instance.md)
- Para obter uma lista de funcionalidades e comparação, consulte a comparação de funcionalidades de [recurso Azure SQL](sql-database-features.md).
- Para atualizações de lançamento e estado de problemas conhecidos, consulte as notas de lançamento da Base de [Dados SQL](sql-database-release-notes.md)
- Para um arranque rápido que lhe mostre como criar um novo exemplo gerido, consulte [Criar um exemplo gerido](sql-database-managed-instance-get-started.md).
