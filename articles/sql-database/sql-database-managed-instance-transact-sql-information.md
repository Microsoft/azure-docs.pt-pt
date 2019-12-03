---
title: Diferenças de T-SQL de instância gerenciada
description: Este artigo discute as diferenças de T-SQL entre uma instância gerenciada no banco de dados SQL do Azure e SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
ms.date: 11/04/2019
ms.custom: seoapril2019
ms.openlocfilehash: e517b6030aa1c9549e33c00425851afae90aac42
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707649"
---
# <a name="managed-instance-t-sql-differences-limitations-and-known-issues"></a>Diferenças de T-SQL de instância gerenciada, limitações e problemas conhecidos

Este artigo resume e explica as diferenças na sintaxe e no comportamento entre a instância gerenciada do banco de dados SQL do Azure e o SQL Server local Mecanismo de Banco de Dados. A opção de implantação de instância gerenciada fornece alta compatibilidade com o SQL Server local Mecanismo de Banco de Dados. A maioria dos recursos do mecanismo de banco de dados SQL Server tem suporte em uma instância gerenciada.

![Migração](./media/sql-database-managed-instance/migration.png)

Há algumas limitações de PaaS que são introduzidas em Instância Gerenciada e algumas alterações de comportamento em comparação com SQL Server. As diferenças são divididas nas seguintes categorias:<a name="Differences"></a>

- A [disponibilidade](#availability) inclui as diferenças em [Always on grupos de disponibilidade](#always-on-availability-groups) e [backups](#backup).
- A [segurança](#security) inclui as diferenças de [auditoria](#auditing), [certificados](#certificates), [credenciais](#credential), [provedores criptográficos](#cryptographic-providers), [logons e usuários](#logins-and-users)e a [chave de serviço e a chave mestra de serviço](#service-key-and-service-master-key).
- A [configuração](#configuration) inclui as diferenças na [extensão do pool de buffers](#buffer-pool-extension), [agrupamento](#collation), [níveis de compatibilidade](#compatibility-levels), [espelhamento de banco de dados](#database-mirroring), opções de banco de [dados](#database-options), [SQL Server Agent](#sql-server-agent)e [Opções de tabela](#tables).
- As [funcionalidades](#functionalities) incluem [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [transações distribuídas](#distributed-transactions), [eventos estendidos](#extended-events), [bibliotecas externas](#external-libraries), [FileStream e filetable](#filestream-and-filetable), [pesquisa semântica de texto completo](#full-text-semantic-search), [servidores vinculados](#linked-servers), [polybase](#polybase), [replicação](#replication), [restauração](#restore-statement), [Service Broker](#service-broker), [procedimentos armazenados, funções e gatilhos](#stored-procedures-functions-and-triggers).
- [Configurações de ambiente](#Environment) , como VNets e configurações de sub-rede.

A maioria desses recursos são restrições de arquitetura e representam recursos de serviço.

Esta página também explica os [problemas temporários conhecidos](#Issues) que são descobertos na instância gerenciada, que serão resolvidos no futuro.

## <a name="availability"></a>Disponibilidade

### <a name="always-on-availability-groups"></a>Always On grupos de disponibilidade

A [alta disponibilidade](sql-database-high-availability.md) é incorporada à instância gerenciada e não pode ser controlada por usuários. As instruções a seguir não têm suporte:

- [CRIAR PONTO DE EXTREMIDADE... PARA DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [CRIAR GRUPO DE DISPONIBILIDADE](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTERAR GRUPO DE DISPONIBILIDADE](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [REMOVER GRUPO DE DISPONIBILIDADE](/sql/t-sql/statements/drop-availability-group-transact-sql)
- A cláusula [set HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) da instrução [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

As instâncias gerenciadas têm backups automáticos, para que os usuários possam criar bancos de dados `COPY_ONLY` backups completos. Não há suporte para backups diferenciais, de log e de instantâneo de arquivo.

- Com uma instância gerenciada, você pode fazer backup de um banco de dados de instância somente para uma conta de armazenamento de BLOBs do Azure:
  - Há suporte apenas para `BACKUP TO URL`.
  - Não há suporte para dispositivos `FILE`, `TAPE`e backup.
- Há suporte para a maioria das opções de `WITH` geral.
  - `COPY_ONLY` é obrigatório.
  - Não há suporte para `FILE_SNAPSHOT`.
  - Opções de fita: `REWIND`, `NOREWIND`, `UNLOAD`e `NOUNLOAD` não têm suporte.
  - Opções específicas de log: não há suporte para `NORECOVERY`, `STANDBY`e `NO_TRUNCATE`.

Limitações 

- Com uma instância gerenciada, você pode fazer backup de um banco de dados de instância em um backup com até 32 faixas, o que é suficiente para bancos de dados de até 4 TB se a compactação de backup for usada.
- Não é possível executar `BACKUP DATABASE ... WITH COPY_ONLY` em um banco de dados que é criptografado com o TDE (Transparent Data Encryption gerenciado por serviço). O TDE gerenciado por serviço força os backups a serem criptografados com uma chave TDE interna. A chave não pode ser exportada, portanto, não é possível restaurar o backup. Use backups automáticos e restauração pontual ou use o [TDE (BYOK) gerenciado pelo cliente](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key) em vez disso. Você também pode desabilitar a criptografia no banco de dados.
- O tamanho máximo de distribuição de backup usando o comando `BACKUP` em uma instância gerenciada é 195 GB, que é o tamanho máximo do blob. Aumente o número de faixas no comando backup para reduzir o tamanho individual da distribuição e permanecer dentro desse limite.

    > [!TIP]
    > Para solucionar essa limitação, ao fazer backup de um banco de dados de qualquer SQL Server em um ambiente local ou em uma máquina virtual, você pode:
    >
    > - Faça backup em `DISK` em vez de fazer backup em `URL`.
    > - Carregue os arquivos de backup no armazenamento de BLOBs.
    > - Restaure na instância gerenciada.
    >
    > O comando `Restore` em uma instância gerenciada dá suporte a tamanhos de blob maiores nos arquivos de backup porque um tipo de blob diferente é usado para o armazenamento dos arquivos de backup carregados.

Para obter informações sobre backups usando o T-SQL, consulte [backup](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Segurança

### <a name="auditing"></a>Auditoria

As principais diferenças entre a auditoria em bancos de dados no banco de dados SQL do Azure e nos dados no SQL Server são:

- Com a opção de implantação de instância gerenciada no banco de dados SQL do Azure, a auditoria funciona no nível do servidor. Os arquivos de log do `.xel` são armazenados no armazenamento de BLOBs do Azure.
- Com as opções de implantação de banco de dados individual e pool elástico no banco de dados SQL do Azure, a auditoria funciona no nível do banco de dados.
- Em SQL Server máquinas virtuais ou locais, a auditoria funciona no nível do servidor. Os eventos são armazenados no sistema de arquivos ou nos logs de eventos do Windows.
 
A auditoria de XEvent na instância gerenciada dá suporte a destinos do armazenamento de BLOBs do Azure. Não há suporte para logs de arquivo e do Windows.

As principais diferenças na sintaxe de `CREATE AUDIT` para a auditoria para o armazenamento de BLOBs do Azure são:

- É fornecida uma nova sintaxe `TO URL` que você pode usar para especificar a URL do contêiner de armazenamento de BLOBs do Azure em que os arquivos de `.xel` são colocados.
- Não há suporte para a sintaxe `TO FILE` porque uma instância gerenciada não pode acessar compartilhamentos de arquivos do Windows.

Para obter mais informações, veja: 

- [CRIAR AUDITORIA DE SERVIDOR](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTERAR AUDITORIA DO SERVIDOR](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Auditoria](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificados

Uma instância gerenciada não pode acessar compartilhamentos de arquivos e pastas do Windows, portanto, as seguintes restrições se aplicam:

- O arquivo de `CREATE FROM`/`BACKUP TO` não tem suporte para certificados.
- O `CREATE`/`BACKUP` certificado do `FILE`/`ASSEMBLY` não tem suporte. Arquivos de chave privada não podem ser usados. 

Consulte [criar](/sql/t-sql/statements/create-certificate-transact-sql) certificado e [certificado de backup](/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Solução alternativa**: em vez de criar backup de certificado e restaurar o backup, [obtenha o conteúdo binário do certificado e a chave privada, armazene-os como arquivo. SQL e crie a partir do binário](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database):

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Credencial

Há suporte apenas para Azure Key Vault e `SHARED ACCESS SIGNATURE` identidades. Não há suporte para usuários do Windows.

Consulte [criar credencial](/sql/t-sql/statements/create-credential-transact-sql) e [alterar credencial](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Provedores criptográficos

Uma instância gerenciada não pode acessar arquivos, portanto, os provedores criptográficos não podem ser criados:

- Não há suporte para `CREATE CRYPTOGRAPHIC PROVIDER`. Consulte [criar provedor criptográfico](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- Não há suporte para `ALTER CRYPTOGRAPHIC PROVIDER`. Consulte [alterar provedor criptográfico](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Inícios de sessão e utilizadores

- Há suporte para logons SQL criados usando `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`e `FROM SID`. Consulte [criar logon](/sql/t-sql/statements/create-login-transact-sql).
- As entidades de segurança (logons) do servidor do Azure Active Directory (Azure AD) criadas com a sintaxe [Create login](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) ou [Create User from login [Azure ad login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) têm suporte. Esses logons são criados no nível do servidor.

    A instância gerenciada dá suporte a entidades de banco de dados do Azure AD com a sintaxe `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Esse recurso também é conhecido como usuários de banco de dados independente do Azure AD.

- Não há suporte para logons do Windows criados com a sintaxe `CREATE LOGIN ... FROM WINDOWS`. Use Azure Active Directory logons e usuários.
- O usuário do Azure AD que criou a instância tem [privilégios de administrador irrestrito](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Usuários de nível de banco de dados não administrador do Azure AD podem ser criados usando a sintaxe `CREATE USER ... FROM EXTERNAL PROVIDER`. Consulte [criar usuário... DO provedor externo](sql-database-manage-logins.md#non-administrator-users).
- As entidades de segurança do servidor do Azure AD (logons) dão suporte a recursos SQL somente em uma instância gerenciada. Recursos que exigem interação entre instâncias, independentemente de estarem dentro do mesmo locatário do Azure AD ou locatários diferentes, não têm suporte para usuários do Azure AD. Exemplos desses recursos são:

  - Replicação transacional do SQL.
  - Servidor de link.

- Não há suporte para a definição de um logon do Azure AD mapeado para um grupo do Azure AD como o proprietário do banco de dados.
- Há suporte para a representação de entidades de segurança no nível de servidor do Azure AD usando outras entidades do Azure AD, como a cláusula [Execute as](/sql/t-sql/statements/execute-as-transact-sql) . AS limitações de executar como são:

  - Não há suporte para EXECUTE AS USER para usuários do Azure AD quando o nome difere do nome de logon. Um exemplo é quando o usuário é criado por meio da sintaxe CREATE USER [myAadUser] do logon [john@contoso.com] e a representação é tentada por meio de EXEC como USER = _myAadUser_. Quando você cria um **usuário** de uma entidade de segurança de servidor do Azure AD (logon), especifique o user_name como o mesmo Login_name do **logon**.
  - Somente as entidades de nível de SQL Server (logons) que fazem parte da função `sysadmin` podem executar as seguintes operações que visam entidades do Azure AD:

    - EXECUTAR COMO USUÁRIO
    - EXECUTAR COMO LOGON

- A exportação/importação de banco de dados usando arquivos bacpac tem suporte para usuários do Azure AD na instância gerenciada usando o [SSMS v 18.4 ou posterior](/sql/ssms/download-sql-server-management-studio-ssms)ou [SqlPackage. exe](/sql/tools/sqlpackage-download).
  - As configurações a seguir têm suporte usando o arquivo bacpac do banco de dados: 
    - Exportar/importar um banco de dados entre diferentes instâncias de gerenciamento dentro do mesmo domínio do Azure AD.
    - Exporte um banco de dados da instância gerenciada e importe para o banco de dados SQL no mesmo domínio do Azure AD. 
    - Exportar um banco de dados do banco de dados SQL e importar para a instância gerenciada dentro do mesmo domínio do Azure AD.
    - Exporte um banco de dados da instância gerenciada e importe para SQL Server (versão 2012 ou posterior).
      - Nessa configuração, todos os usuários do Azure AD são criados como entidades de segurança do banco de dados SQL (usuários) sem logons. O tipo de usuário é listado como SQL (visível como SQL_USER em sys. database_principals). Suas permissões e funções permanecem no SQL Server metadados do banco de dados e podem ser usadas para representação. No entanto, eles não podem ser usados para acessar e fazer logon no SQL Server usando suas credenciais.

- Somente o logon da entidade de segurança no nível do servidor, que é criado pelo processo de provisionamento de instância gerenciada, os membros das funções de servidor, como `securityadmin` ou `sysadmin`ou outros logons com a permissão ALTER ANY LOGIN no nível do servidor podem criar entidades de segurança do Azure AD Server (logons) no banco de dados mestre para a instância gerenciada.
- Se o logon for uma entidade de segurança SQL, somente os logons que fizerem parte da função `sysadmin` poderão usar o comando Create para criar logons para uma conta do Azure AD.
- O logon do Azure AD deve ser um membro de um Azure AD no mesmo diretório usado para a instância gerenciada do banco de dados SQL do Azure.
- As entidades de segurança do servidor do Azure AD (logons) são visíveis no Pesquisador de objetos, começando com SQL Server Management Studio 18,0 Preview 5.
- É permitido sobrepor entidades de segurança do Azure AD (logons) com uma conta de administrador do Azure AD. As entidades de segurança do servidor do Azure AD (logons) têm precedência sobre o administrador do Azure AD quando você resolve a entidade de segurança e aplica permissões à instância gerenciada.
- Durante a autenticação, a sequência a seguir é aplicada para resolver a entidade de autenticação:

    1. Se a conta do Azure AD existir como mapeada diretamente para a entidade de segurança do servidor do Azure AD (logon), que está presente em sys. server_principals como tipo "E" conceder acesso e aplicar permissões da entidade de segurança do servidor do Azure AD (logon).
    2. Se a conta do Azure AD for um membro de um grupo do Azure AD que é mapeado para a entidade de segurança de servidor do Azure AD (logon), que está presente em sys. server_principals como tipo "X", conceder acesso e aplicar permissões do logon do grupo do Azure AD.
    3. Se a conta do Azure AD for um administrador especial configurado pelo portal do Azure AD para a instância gerenciada, que não existe em exibições do sistema de instância gerenciada, aplique permissões fixas especiais do administrador do Azure AD para a instância gerenciada (modo herdado).
    4. Se a conta do Azure AD existir como mapeada diretamente para um usuário do Azure AD em um banco de dados, que está presente em sys. database_principals como tipo "E" conceder acesso e aplicar permissões do usuário de banco de dados do Azure AD.
    5. Se a conta do Azure AD for um membro de um grupo do Azure AD que é mapeado para um usuário do Azure AD em um banco de dados, que está presente em sys. database_principals como tipo "X", conceder acesso e aplicar permissões do logon do grupo do Azure AD.
    6. Se houver um logon do Azure AD mapeado para uma conta de usuário do Azure AD ou uma conta de grupo do Azure AD, que seja resolvida para o usuário que está Autenticando, todas as permissões desse logon do Azure AD serão aplicadas.

### <a name="service-key-and-service-master-key"></a>Chave de serviço e chave mestra de serviço

- O [backup da chave mestra](/sql/t-sql/statements/backup-master-key-transact-sql) não tem suporte (gerenciado pelo serviço do banco de dados SQL).
- Não há suporte para a [restauração da chave mestra](/sql/t-sql/statements/restore-master-key-transact-sql) (gerenciada pelo serviço do banco de dados SQL).
- O [backup da chave mestra de serviço](/sql/t-sql/statements/backup-service-master-key-transact-sql) não tem suporte (gerenciado pelo serviço de banco de dados SQL).
- Não há suporte para a [restauração da chave mestra de serviço](/sql/t-sql/statements/restore-service-master-key-transact-sql) (gerenciada pelo serviço do banco de dados SQL).

## <a name="configuration"></a>Configuração

### <a name="buffer-pool-extension"></a>Extensão do pool de buffers

- Não há suporte para a [extensão do pool de buffers](/sql/database-engine/configure-windows/buffer-pool-extension) .
- Não há suporte para `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`. Consulte [ALTER Server Configuration](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Agrupamento

O agrupamento de instância padrão é `SQL_Latin1_General_CP1_CI_AS` e pode ser especificado como um parâmetro de criação. Consulte [agrupamentos](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Níveis de compatibilidade

- Os níveis de compatibilidade com suporte são 100, 110, 120, 130, 140 e 150.
- Os níveis de compatibilidade abaixo de 100 não têm suporte.
- O nível de compatibilidade padrão para novos bancos de dados é 140. Para bancos de dados restaurados, o nível de compatibilidade permanece inalterado se foi 100 e superior.

Veja [nível de compatibilidade de ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Espelhamento da base de dados

Não há suporte para o espelhamento de banco de dados.

- Não há suporte para as opções `ALTER DATABASE SET PARTNER` e `SET WITNESS`.
- Não há suporte para `CREATE ENDPOINT … FOR DATABASE_MIRRORING`.

Para obter mais informações, consulte [ALTER DATABASE SET Partner e Set testemunha](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) e [CREATE ENDPOINT... PARA DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Opções de banco de dados

- Não há suporte para vários arquivos de log.
- Não há suporte para objetos na memória na camada de serviço Uso Geral. 
- Há um limite de 280 arquivos por Uso Geral instância, o que implica um máximo de 280 arquivos por banco de dados. Os arquivos de dados e de log na camada de Uso Geral são contados em direção a esse limite. [A camada de comercialmente crítico dá suporte a 32.767 arquivos por banco de dados](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- O banco de dados não pode conter grupos de fileque contenham dado FileStream. A restauração falhará se. bak contiver dados de `FILESTREAM`. 
- Cada arquivo é colocado no armazenamento de BLOBs do Azure. E/s e taxa de transferência por arquivo dependem do tamanho de cada arquivo individual.

#### <a name="create-database-statement"></a>Instrução CREATE DATABASE

As seguintes limitações se aplicam a `CREATE DATABASE`:

- Arquivos e grupos de arquivo não podem ser definidos. 
- Não há suporte para a opção `CONTAINMENT`. 
- Não há suporte para opções de `WITH`. 
   > [!TIP]
   > Como alternativa, use `ALTER DATABASE` depois de `CREATE DATABASE` para definir opções de banco de dados para adicionar arquivos ou para definir a contenção. 

- Não há suporte para a opção `FOR ATTACH`.
- Não há suporte para a opção `AS SNAPSHOT OF`.

Para obter mais informações, consulte [criar banco de dados](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instrução ALTER DATABASE

Algumas propriedades de arquivo não podem ser definidas ou alteradas:

- Não é possível especificar um caminho de arquivo na instrução `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL. Remova `FILENAME` do script porque uma instância gerenciada coloca os arquivos automaticamente. 
- Um nome de arquivo não pode ser alterado usando a instrução `ALTER DATABASE`.

As opções a seguir são definidas por padrão e não podem ser alteradas:

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

Para obter mais informações, consulte [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- Não há suporte para habilitar e desabilitar SQL Server Agent atualmente na instância gerenciada. O SQL Agent está sempre em execução.
- SQL Server Agent configurações são somente leitura. Não há suporte para o procedimento `sp_set_agent_properties` na instância gerenciada. 
- Tarefas
  - Há suporte para as etapas de trabalho T-SQL.
  - Há suporte para os seguintes trabalhos de replicação:
    - Leitor de log de transações
    - Instantâneo
    - Distribuidor
  - Há suporte para as etapas de trabalho do SSIS.
  - Atualmente, não há suporte para outros tipos de etapas de trabalho:
    - Não há suporte para a etapa de trabalho de replicação de mesclagem. 
    - Não há suporte para o leitor de fila. 
    - Ainda não há suporte para o Shell de comando.
  - As instâncias gerenciadas não podem acessar recursos externos, por exemplo, compartilhamentos de rede via Robocopy. 
  - Não há suporte para SQL Server Analysis Services.
- Há suporte parcial para notificações.
- Há suporte para notificação por email, embora exija que você configure um perfil de Database Mail. SQL Server Agent pode usar apenas um perfil de Database Mail e deve ser chamado `AzureManagedInstance_dbmail_profile`. 
  - Não há suporte para o pager.
  - O Netsend não tem suporte.
  - Ainda não há suporte para alertas.
  - Não há suporte para proxies.
- O log de eventos não tem suporte.

Os seguintes recursos do SQL Agent atualmente não têm suporte:

- Proxies
- Agendando trabalhos em uma CPU ociosa
- Habilitando ou desabilitando um agente
- Alertas

Para obter informações sobre SQL Server Agent, consulte [SQL Server Agent](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabelas

Os seguintes tipos de tabela não têm suporte:

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [Tabela externa](/sql/t-sql/statements/create-external-table-transact-sql) (polybase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (sem suporte apenas na camada uso geral)

Para obter informações sobre como criar e alterar tabelas, consulte [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) e [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funcionalidades

### <a name="bulk-insert--openrowset"></a>Inserção em massa/OPENROWSET

Uma instância gerenciada não pode acessar compartilhamentos de arquivos e pastas do Windows, portanto, os arquivos devem ser importados do armazenamento de BLOBs do Azure:

- `DATASOURCE` é necessário no comando `BULK INSERT` enquanto importa arquivos do armazenamento de BLOBs do Azure. Consulte [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` é necessário na função `OPENROWSET` quando você lê o conteúdo de um arquivo do armazenamento de BLOBs do Azure. Consulte [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` pode ser usado para ler dados de outros bancos de dado SQL do Azure, instâncias gerenciadas ou instâncias de SQL Server. Não há suporte para outras fontes, como bancos de dados Oracle ou arquivos do Excel.

### <a name="clr"></a>CLR

Uma instância gerenciada não pode acessar compartilhamentos de arquivos e pastas do Windows, portanto, as seguintes restrições se aplicam:

- Há suporte apenas para `CREATE ASSEMBLY FROM BINARY`. Confira [criar Assem clicando do binário](/sql/t-sql/statements/create-assembly-transact-sql). 
- Não há suporte para `CREATE ASSEMBLY FROM FILE`. Consulte [criar assembly do arquivo](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` não pode fazer referência a arquivos. Consulte [ALTER assembly](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Database Mail (db_mail)
 - `sp_send_dbmail` não pode enviar anexos usando @file_attachments parâmetro. O sistema de arquivos local e os compartilhamentos externos ou o armazenamento de BLOBs do Azure não são acessíveis a partir desse procedimento.
 - Consulte os problemas conhecidos relacionados ao parâmetro `@query` e à autenticação.
 
### <a name="dbcc"></a>DBCC

As instruções DBCC não documentadas que estão habilitadas no SQL Server não têm suporte em instâncias gerenciadas.

- Há suporte apenas para um número limitado de sinalizadores de rastreamento globais. Não há suporte para `Trace flags` em nível de sessão. Consulte [sinalizadores de rastreamento](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) e [DBCC tracen](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) funcionam com o número limitado de sinalizadores de rastreamento globais.
- Não é possível usar o [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) com opções REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST e REPAIR_REBUILD porque o banco de dados não pode ser definido no modo de `SINGLE_USER`-consulte [diferenças de ALTER DATABASE](#alter-database-statement). As possíveis corrupções de banco de dados são tratadas pela equipe de suporte do Azure. Contate o suporte do Azure se você estiver percebendo corrupção de banco de dados que deve ser corrigido.

### <a name="distributed-transactions"></a>Transações distribuídas

Atualmente, não há suporte para transações de MSDTC e [elástico](sql-database-elastic-transactions-overview.md) em instâncias gerenciadas.

### <a name="extended-events"></a>Eventos Expandidos

Não há suporte para alguns destinos específicos do Windows para eventos estendidos (XEvents):

- Não há suporte para o destino de `etw_classic_sync`. Armazene `.xel` arquivos no armazenamento de BLOBs do Azure. Consulte [etw_classic_sync Target](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Não há suporte para o destino de `event_file`. Armazene `.xel` arquivos no armazenamento de BLOBs do Azure. Consulte [event_file Target](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Bibliotecas externas

R e Python no banco de dados, ainda não há suporte para bibliotecas externas. Consulte [SQL Server serviços de Machine Learning](/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream e Filetable

- Não há suporte para dados FILESTREAM.
- O banco de dados não pode conter grupos de File`FILESTREAM`dos.
- Não há suporte para `FILETABLE`.
- As tabelas não podem ter tipos de `FILESTREAM`.
- As funções a seguir não têm suporte:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Para obter mais informações, consulte [FileStream](/sql/relational-databases/blob/filestream-sql-server) e [filetables](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Pesquisa semântica de texto completo

A [pesquisa semântica](/sql/relational-databases/search/semantic-search-sql-server) não tem suporte.

### <a name="linked-servers"></a>Servidores ligados

Os servidores vinculados em instâncias gerenciadas dão suporte a um número limitado de destinos:

- Os destinos com suporte são instâncias gerenciadas, bancos de dados individuais e instâncias de SQL Server. 
- Os servidores vinculados não dão suporte a transações graváveis distribuídas (MS DTC).
- Os destinos que não têm suporte são arquivos, Analysis Services e outros RDBMS. Tente usar a importação de CSV nativo do armazenamento de BLOBs do Azure usando `BULK INSERT` ou `OPENROWSET` como uma alternativa para a importação de arquivos.

Operations

- Não há suporte para transações de gravação entre instâncias.
- `sp_dropserver` tem suporte para remover um servidor vinculado. Consulte [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- A função `OPENROWSET` pode ser usada para executar consultas somente em instâncias SQL Server. Eles podem ser gerenciados, localmente ou em máquinas virtuais. Consulte [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- A função `OPENDATASOURCE` pode ser usada para executar consultas somente em instâncias SQL Server. Eles podem ser gerenciados, localmente ou em máquinas virtuais. Somente os valores `SQLNCLI`, `SQLNCLI11`e `SQLOLEDB` têm suporte como um provedor. Um exemplo é `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Consulte [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- Os servidores vinculados não podem ser usados para ler arquivos (Excel, CSV) dos compartilhamentos de rede. Tente usar [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) ou [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) que leia arquivos CSV do armazenamento de BLOBs do Azure. Acompanhar essas solicitações no [item de comentário da instância gerenciada](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Não há suporte para tabelas externas que fazem referência aos arquivos no HDFS ou no armazenamento de BLOBs do Azure. Para obter informações sobre o polybase, consulte [polybase](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replicação

- Há suporte para os tipos de replicação de instantâneo e bidirecional. Não há suporte para replicação de mesclagem, replicação ponto a ponto e assinaturas atualizáveis.
- A [replicação transacional](sql-database-managed-instance-transactional-replication.md) está disponível para visualização pública na instância gerenciada com algumas restrições:
    - Todos os tipos de participantes de replicação (editor, distribuidor, assinante de pull e assinante push) podem ser colocados em instâncias gerenciadas, mas o Publicador e o distribuidor devem estar tanto na nuvem quanto no local.
    - As instâncias gerenciadas podem se comunicar com as versões recentes do SQL Server. Consulte as versões com suporte [aqui](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems).
    - A replicação transacional tem alguns [requisitos de rede adicionais](sql-database-managed-instance-transactional-replication.md#requirements).

Para obter informações sobre como configurar a replicação, consulte o [tutorial de replicação](replication-with-sql-database-managed-instance.md).


Se a replicação estiver habilitada em um banco de dados em um [grupo de failover](sql-database-auto-failover-group.md), o administrador da instância gerenciada deverá limpar todas as publicações na primária antiga e reconfigurá-las no novo primário após a ocorrência de um failover. As seguintes atividades são necessárias neste cenário:

1. Pare todos os trabalhos de replicação em execução no banco de dados, se houver algum.
2. Remova os metadados da assinatura do Publicador executando o seguinte script no banco de dados do Publicador:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Remova os metadados da assinatura do Assinante. Execute o seguinte script no banco de dados de assinatura na instância do Assinante:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Force a remoção de todos os objetos de replicação do Publicador executando o seguinte script no banco de dados publicado:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Force a remoção do distribuidor antigo da instância primária original (se estiver fazendo failover novamente para um antigo primário que costumava ter um distribuidor). Execute o seguinte script no banco de dados mestre na instância gerenciada antiga do distribuidor:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

### <a name="restore-statement"></a>Instrução RESTOre 

- Sintaxe com suporte:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Sintaxe sem suporte:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Fonte: 
  - `FROM URL` (armazenamento de BLOBs do Azure) é a única opção com suporte.
  - Não há suporte para `FROM DISK`/dispositivo `TAPE`/backup.
  - Não há suporte para conjuntos de backup.
- Não há suporte para `WITH` opções, como nenhuma `DIFFERENTIAL` ou `STATS`.
- `ASYNC RESTORE`: a restauração continua mesmo que a conexão do cliente seja interrompida. Se a conexão for descartada, você poderá verificar a `sys.dm_operation_status` exibição do status de uma operação de restauração e para criar e remover um banco de dados. Consulte [Sys. dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

As opções de banco de dados a seguir são definidas ou substituídas e não podem ser alteradas posteriormente: 

- `NEW_BROKER` se o agente não estiver habilitado no arquivo. bak. 
- `ENABLE_BROKER` se o agente não estiver habilitado no arquivo. bak. 
- `AUTO_CLOSE=OFF` se um banco de dados no arquivo. bak tiver `AUTO_CLOSE=ON`. 
- `RECOVERY FULL` se um banco de dados no arquivo. bak tiver `SIMPLE` ou `BULK_LOGGED` modo de recuperação.
- Um grupo de arquivos com otimização de memória é adicionado e chamado de XTP se ele não estava no arquivo Source. bak. 
- Qualquer grupo de arquivos com otimização de memória existente é renomeado para XTP. 
- as opções `SINGLE_USER` e `RESTRICTED_USER` são convertidas em `MULTI_USER`.

Limitações 

- Os backups dos bancos de dados corrompidos podem ser restaurados dependendo do tipo de corrupção, mas os backups automatizados não serão feitos até que a corrupção seja corrigida. Certifique-se de executar `DBCC CHECKDB` na instância de origem e usar `WITH CHECKSUM` de backup para evitar esse problema.
- A restauração do arquivo de `.BAK` de um banco de dados que contém qualquer limitação descrita neste documento (por exemplo, `FILESTREAM` ou `FILETABLE` objetos) não pode ser restaurada em Instância Gerenciada.
- `.BAK` arquivos que contêm vários conjuntos de backup não podem ser restaurados. 
- `.BAK` arquivos que contêm vários arquivos de log não podem ser restaurados.
- Os backups que contêm bancos de dados maiores que 8 TB, objetos OLTP na memória ativas ou o número de arquivos que excedem 280 arquivos por instância não podem ser restaurados em uma instância de Uso Geral. 
- Os backups que contêm bancos de dados maiores que 4 TB ou objetos OLTP na memória com o tamanho total maior do que o tamanho descrito nos [limites de recursos](sql-database-managed-instance-resource-limits.md) não podem ser restaurados na instância comercialmente crítico.
Para obter informações sobre instruções RESTORE, consulte [instruções RESTORE](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > As mesmas limitações se aplicam à operação de restauração pontual interna. Por exemplo, Uso Geral banco de dados maior que 4 TB não pode ser restaurado na instância Comercialmente Crítico. Comercialmente Crítico banco de dados com arquivos OLTP na memória ou mais de 280 arquivos não podem ser restaurados na instância Uso Geral.

### <a name="service-broker"></a>Service Broker

Não há suporte para o Service Broker de instância cruzada:

- `sys.routes`: como um pré-requisito, você deve selecionar o endereço de sys. routes. O endereço deve ser LOCAL em cada rota. Consulte [Sys. routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: não é possível usar `CREATE ROUTE` com `ADDRESS` diferente de `LOCAL`. Consulte [Criar rota](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: não é possível usar `ALTER ROUTE` com `ADDRESS` diferente de `LOCAL`. Consulte [ALTER Route](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Procedimentos armazenados, funções e gatilhos

- Não há suporte para `NATIVE_COMPILATION` na camada de Uso Geral.
- As seguintes opções de [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) não têm suporte: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- Não há suporte para `sp_execute_external_scripts`. Consulte [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- Não há suporte para `xp_cmdshell`. Consulte [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- Não há suporte para `Extended stored procedures`, que inclui `sp_addextendedproc` e `sp_dropextendedproc`. Consulte [procedimentos armazenados estendidos](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- Não há suporte para `sp_attach_db`, `sp_attach_single_file_db`e `sp_detach_db`. Consulte [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)e [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Funções e variáveis do sistema

As seguintes variáveis, funções e exibições retornam resultados diferentes:

- `SERVERPROPERTY('EngineEdition')` retorna o valor 8. Essa propriedade identifica exclusivamente uma instância gerenciada. Consulte [ServerProperty](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` retorna NULL porque o conceito de instância existente para SQL Server não se aplica a uma instância gerenciada. Consulte [SERVERPROPERTY (' InstanceName ')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` retorna um nome de DNS "conectável" completo, por exemplo, my-managed-instance.wcus17662feb9ce98.database.windows.net. Consulte [@@SERVERNAME](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` retorna um nome de DNS "conectável" completo, como `myinstance.domain.database.windows.net` para as propriedades "Name" e "data_source". Consulte [Sys. SERVIDORES](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` retorna NULL porque o conceito de serviço existente para SQL Server não se aplica a uma instância gerenciada. Consulte [@@SERVICENAME](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` tem suporte. Retornará NULL se o logon do Azure AD não estiver em sys. syslogins. Consulte [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- Não há suporte para `SUSER_SID`. Os dados errados são retornados, o que é um problema temporário conhecido. Consulte [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Environment"></a>Restrições de ambiente

### <a name="subnet"></a>Subrede
-  Você não pode inserir outros recursos (por exemplo, máquinas virtuais) na sub-rede em que você implantou sua instância gerenciada. Implante esses recursos usando uma sub-rede diferente.
- A sub-rede deve ter um número suficiente de [endereços IP](sql-database-managed-instance-connectivity-architecture.md#network-requirements)disponíveis. O mínimo é 16, enquanto a recomendação é ter pelo menos 32 endereços IP na sub-rede.
- [Os pontos de extremidade de serviço não podem ser associados à sub-rede da instância gerenciada](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Verifique se a opção pontos de extremidade de serviço está desabilitada quando você cria a rede virtual.
- O número de vCores e tipos de instâncias que você pode implantar em uma região têm algumas [restrições e limites](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
- Há algumas [regras de segurança que devem ser aplicadas na sub-rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>VIRTUAL
- A VNet pode ser implantada usando modelo de recurso-modelo clássico para VNet sem suporte.
- Depois que uma instância gerenciada é criada, não há suporte para a movimentação da instância gerenciada ou da VNet para outro grupo de recursos ou assinatura.
- Alguns serviços, como ambientes de serviço de aplicativo, aplicativos lógicos e instâncias gerenciadas (usados para replicação geográfica, replicação transacional ou por meio de servidores vinculados) não poderão acessar instâncias gerenciadas em regiões diferentes se seus VNets estiverem conectados usando o [emparelhamento global](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Você pode se conectar a esses recursos via ExpressRoute ou VNet a VNet por meio de gateways de VNet.

### <a name="tempdb"></a>TEMPDB

O tamanho máximo do arquivo de `tempdb` não pode ser maior que 24 GB por núcleo em uma camada de Uso Geral. O tamanho máximo de `tempdb` em uma camada de Comercialmente Crítico é limitado pelo tamanho do armazenamento da instância. `Tempdb` tamanho do arquivo de log é limitado a 120 GB na camada Uso Geral. Algumas consultas podem retornar um erro se precisarem de mais de 24 GB por núcleo em `tempdb` ou se produzirem mais de 120 GB de dados de log.

### <a name="error-logs"></a>Registos de erros

Uma instância gerenciada coloca informações detalhadas nos logs de erros. Há muitos eventos internos do sistema que são registrados no log de erros do. Use um procedimento personalizado para ler logs de erro que filtram algumas entradas irrelevantes. Para obter mais informações, consulte [instância gerenciada – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) ou [extensão de instância gerenciada (versão prévia)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) para Azure Data Studio.

## <a name="Issues"></a>Problemas conhecidos

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Os limites de memória OLTP na memória não são aplicados

**Data:** Outubro de 2019

Comercialmente Crítico camada de serviço não aplicará corretamente [os limites máximos de memória para objetos com otimização de memória](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) em alguns casos. A instância gerenciada pode permitir que a carga de trabalho use mais memória para operações OLTP na memória, o que pode afetar a disponibilidade e a estabilidade da instância. As consultas OLTP na memória que estão atingindo os limites podem não falhar imediatamente. Esse problema será corrigido em breve. As consultas que usam mais memória OLTP na memória falharão mais cedo se atingirem os [limites](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Solução alternativa:** [monitore o uso de armazenamento OLTP na memória](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) usando [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) para garantir que a carga de trabalho não esteja usando mais do que a memória disponível. Aumente os limites de memória que dependem do número de vCores ou Otimize sua carga de trabalho para usar menos memória.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Erro incorreto retornado ao tentar remover um arquivo que não está vazio

**Data:** Outubro de 2019

SQL Server/Instância Gerenciada [não permitir que o usuário descarte um arquivo que não esteja vazio](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Se você tentar remover um arquivo de dados não vazio usando a instrução `ALTER DATABASE REMOVE FILE`, o erro `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` não será retornado imediatamente. Instância Gerenciada continuará tentando descartar o arquivo, e a operação falhará após 30 min com `Internal server error`.

**Solução alternativa**: Remova o conteúdo do arquivo usando `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` comando. Se esse for o único arquivo no grupo de arquivos, você precisará excluir dados da tabela ou da partição associada a esse grupo de arquivos antes de reduzir o arquivo e, opcionalmente, carregar esses dados em outra tabela/partição.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Alterar a camada de serviço e criar operações de instância são bloqueadas pela restauração de banco de dados em andamento

**Data:** Setembro de 2019

A instrução de `RESTORE` contínua, o processo de migração do serviço de migração de dados e a restauração pontual interna bloquearão a atualização da camada de serviço ou o redimensionamento da instância existente e a criação de novas instâncias até que o processo de restauração seja concluído. O processo de restauração bloqueará essas operações nas instâncias gerenciadas e nos pools de instância na mesma sub-rede em que o processo de restauração está em execução. As instâncias em pools de instâncias não são afetadas. Criar ou alterar as operações da camada de serviço não falharão ou tempo limite-eles continuarão quando o processo de restauração for concluído ou cancelado.

**Solução alternativa**: Aguarde até que o processo de restauração seja concluído ou cancele o processo de restauração se a operação de criação ou atualização da camada de serviço tiver prioridade mais alta.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Resource Governor na camada de serviço Comercialmente Crítico talvez precise ser reconfigurada após o failover

**Data:** Setembro de 2019

[Resource governor](/sql/relational-databases/resource-governor/resource-governor) recurso que permite limitar os recursos atribuídos à carga de trabalho do usuário pode classificar incorretamente alguma carga de trabalho do usuário após o failover ou a alteração da camada de serviço iniciada pelo usuário (por exemplo, a alteração do tamanho máximo do armazenamento de instância vCore ou máximo).

**Solução alternativa**: execute `ALTER RESOURCE GOVERNOR RECONFIGURE` periodicamente ou como parte do trabalho do SQL Agent que executa a tarefa SQL quando a instância for iniciada se você estiver usando [resource governor](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>As caixas de diálogo de Service Broker entre bancos de dados devem ser reinicializadas após a atualização da camada de serviço

**Data:** 2019 de agosto

As caixas de diálogo de Service Broker de banco de dados cruzado deixarão de entregar as mensagens para os serviços em outros bancos de dados após a operação de alteração da camada de serviço. As mensagens **não são perdidas** e podem ser encontradas na fila do remetente. Qualquer alteração de tamanho de armazenamento de instância ou vCores no Instância Gerenciada fará com que `service_broke_guid` valor na exibição [Sys. databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) seja alterado para todos os bancos de dados. Qualquer `DIALOG` criado usando a instrução [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) que referencie os agentes de serviço em outro banco de dados interromperá a entrega de mensagens ao serviço de destino.

**Solução alternativa:** Pare qualquer atividade que use conversas de caixa de diálogo Service Broker de banco de dados antes de atualizar a camada de serviço e reinicializá-la após. Se houver mensagens restantes que não são entregues após a alteração da camada de serviço, leia as mensagens da fila de origem e reenvie-as para a fila de destino.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Não há suporte para a impessoa de tipos de logon do Azure AD

**Data:** Julho de 2019

Não há suporte para a representação usando `EXECUTE AS USER` ou `EXECUTE AS LOGIN` das seguintes entidades do AAD:
-   Usuários com alias do AAD. O erro a seguir é retornado neste caso `15517`.
- Logons e usuários do AAD com base em aplicativos do AAD ou entidades de serviço. Os erros a seguir são retornados nesse caso `15517` e `15406`.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query parâmetro sem suporte no sp_send_db_mail

**Data:** Abril de 2019

O parâmetro `@query` no procedimento [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) não funciona.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>A replicação transacional deve ser reconfigurada após o failover geográfico

**Data:** Mar de 2019

Se a replicação transacional estiver habilitada em um banco de dados em um grupo de failover automático, o administrador da instância gerenciada deverá limpar todas as publicações no antigo primário e reconfigurá-los no novo primário após a ocorrência de um failover para outra região. Consulte [replicação](#replication) para obter mais detalhes.

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>Não há suporte para logons e usuários do AAD no SSDT

**Data:** 2019 de novembro

SQL Server Data Tools não dão suporte total a logons e usuários do Azure Active Directory.

### <a name="temporary-database-is-used-during-restore-operation"></a>O banco de dados temporário é usado durante a operação de restauração

Quando um banco de dados estiver restaurando em Instância Gerenciada, o serviço de restauração criará primeiro um banco de dados vazio com o nome desejado para alocar o nome na instância. Após algum tempo, esse banco de dados será descartado e a restauração do banco de dados real será iniciada. O banco de dados que está no estado de *restauração* temporário terá um valor de GUID aleatório em vez de nome. O nome temporário será alterado para o nome desejado especificado na instrução `RESTORE` depois que o processo de restauração for concluído. Na fase inicial, o usuário pode acessar o banco de dados vazio e, até mesmo, criar tabelas ou carrega-los. Esse banco de dados temporário será Descartado quando o serviço de restauração iniciar a segunda fase.

**Solução alternativa**: não acesse o banco de dados que você está restaurando até ver que a restauração foi concluída.

### <a name="tempdb-structure-and-content-is-re-created"></a>A estrutura TEMPDB e o conteúdo são recriados

O banco de dados do `tempdb` sempre é dividido em 12 arquivos e a estrutura do arquivo não pode ser alterada. O tamanho máximo por arquivo não pode ser alterado e novos arquivos não podem ser adicionados a `tempdb`. `Tempdb` é sempre recriado como um banco de dados vazio quando a instância inicia ou faz failover, e quaisquer alterações feitas no `tempdb` não serão preservadas.

### <a name="exceeding-storage-space-with-small-database-files"></a>Excedendo o espaço de armazenamento com arquivos de banco de dados pequenos

as instruções `CREATE DATABASE`, `ALTER DATABASE ADD FILE`e `RESTORE DATABASE` podem falhar porque a instância pode alcançar o limite de armazenamento do Azure.

Cada instância gerenciada Uso Geral tem até 35 TB de armazenamento reservado para o espaço em disco Premium do Azure. Cada arquivo de banco de dados é colocado em um disco físico separado. Os tamanhos de disco podem ser 128 GB, 256 GB, 512 GB, 1 TB ou 4 TB. O espaço não utilizado no disco não é cobrado, mas a soma total dos tamanhos de disco Premium do Azure não pode exceder 35 TB. Em alguns casos, uma instância gerenciada que não precisa de 8 TB no total pode exceder o limite de 35 TB do Azure no tamanho do armazenamento devido à fragmentação interna.

Por exemplo, uma instância gerenciada Uso Geral pode ter um arquivo grande que seja de 1,2 TB de tamanho colocado em um disco de 4 TB. Ele também pode ter 248 arquivos com tamanho de 1 GB cada um colocado em discos separados de 128 GB. Neste exemplo:

- O tamanho total de armazenamento em disco alocado é 1 x 4 TB + 248 x 128 GB = 35 TB.
- O espaço reservado total para bancos de dados na instância é 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Este exemplo ilustra que, em determinadas circunstâncias, devido a uma distribuição específica de arquivos, uma instância gerenciada pode alcançar o limite de 35 TB reservado para um disco Premium do Azure conectado quando você talvez não o espere.

Neste exemplo, os bancos de dados existentes continuam funcionando e podem crescer sem qualquer problema, desde que novos arquivos não sejam adicionados. Novos bancos de dados não podem ser criados ou restaurados porque não há espaço suficiente para novas unidades de disco, mesmo que o tamanho total de todos os bancos de dados não atinja o limite de tamanho da instância. O erro retornado nesse caso não é claro.

Você pode [identificar o número de arquivos restantes](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) usando exibições do sistema. Se você atingir esse limite, tente [esvaziar e excluir alguns dos arquivos menores usando a instrução DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) ou alterne para a [camada comercialmente crítico, que não tem esse limite](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Valores de GUID mostrados em vez de nomes de banco de dados

Várias exibições do sistema, contadores de desempenho, mensagens de erro, XEvents e entradas de log de erros exibem identificadores de banco de dados GUID em vez dos nomes de banco de dados reais. Não confie nesses identificadores GUID porque eles são substituídos por nomes de banco de dados reais no futuro.

### <a name="error-logs-arent-persisted"></a>Os logs de erros não são persistentes

Os logs de erros que estão disponíveis na instância gerenciada não são mantidos e seu tamanho não é incluído no limite máximo de armazenamento. Os logs de erros poderão ser apagados automaticamente se ocorrer um failover. Pode haver lacunas no histórico do log de erros porque Instância Gerenciada foi movido várias vezes em várias máquinas virtuais.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>O escopo da transação em dois bancos de dados na mesma instância não tem suporte

A classe `TransactionScope` no .NET não funciona se duas consultas forem enviadas a dois bancos de dados dentro da mesma instância no mesmo escopo de transação:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

Embora esse código funcione com dados dentro da mesma instância, ele exigia o MSDTC.

**Solução alternativa:** Use [SqlConnection. ChangeDatabase (String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) para usar outro banco de dados em um contexto de conexão em vez de usar duas conexões.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Os módulos CLR e os servidores vinculados às vezes não podem fazer referência a um endereço IP local

Os módulos CLR colocados em uma instância gerenciada e servidores vinculados ou consultas distribuídas que referenciam uma instância atual às vezes não podem resolver o IP de uma instância local. Esse erro é um problema transitório.

**Solução alternativa:** Use conexões de contexto em um módulo CLR, se possível.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre instâncias gerenciadas, consulte [o que é uma instância gerenciada?](sql-database-managed-instance.md)
- Para obter uma lista de recursos e comparação, consulte comparação de recursos [do banco de dados SQL do Azure](sql-database-features.md).
- Para obter um guia de início rápido que mostra como criar uma nova instância gerenciada, consulte [criar uma instância gerenciada](sql-database-managed-instance-get-started.md).
