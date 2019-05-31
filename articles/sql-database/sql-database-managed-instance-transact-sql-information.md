---
title: Diferenças de SQL da base de dados geridos instância T-SQL do Azure | Documentos da Microsoft
description: Este artigo aborda as diferenças do T-SQL entre uma instância gerida na base de dados do Azure SQL e SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
manager: craigg
ms.date: 03/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 5c8a15aa5198983a56a0238c1bb56f9345d07acc
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258599"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Diferenças de SQL da base de dados geridos instância T-SQL do Azure do SQL Server

Este artigo resume e explica as diferenças de sintaxe e o comportamento entre a instância gerida da base de dados SQL do Azure e no local motor de base de dados do SQL Server. Os seguintes assuntos são discutidos: <a name="Differences"></a>

- [Disponibilidade](#availability) inclui as diferenças nos [Always-On](#always-on-availability) e [cópias de segurança](#backup).
- [Segurança](#security) inclui as diferenças nos [auditoria](#auditing), [certificados](#certificates), [credenciais](#credential), [provedores criptográficos](#cryptographic-providers), [inícios de sessão e utilizadores](#logins-and-users)e o [chave de serviço e a chave mestra do serviço](#service-key-and-service-master-key).
- [Configuração](#configuration) inclui as diferenças nos [a extensão do conjunto da memória intermédia](#buffer-pool-extension), [agrupamento](#collation), [níveis de compatibilidade](#compatibility-levels), [espelhamento da base de dados ](#database-mirroring), [opções de base de dados](#database-options), [SQL Server Agent](#sql-server-agent), e [opções da tabela](#tables).
- [Funcionalidades](#functionalities) inclui [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [transações distribuídas](#distributed-transactions), [eventos expandidos](#extended-events), [bibliotecas externas](#external-libraries), [filestream e FileTable](#filestream-and-filetable), [pesquisa semântica de texto completo](#full-text-semantic-search), [servidores vinculados](#linked-servers), [PolyBase](#polybase), [replicação](#replication), [restaurar](#restore-statement), [Service Broker](#service-broker), [procedimentos armazenados, funções e disparadores](#stored-procedures-functions-and-triggers).
- [Definições de ambiente](#Environment) , tais como configurações de VNets e a sub-rede.
- [Instâncias geridas de recursos que têm um comportamento diferente no](#Changes).
- [Limitações temporárias e problemas conhecidos](#Issues).

A opção de implementação de instância gerida fornece compatibilidade com o motor de base de dados de servidor de SQL no local. A maioria das funcionalidades do motor de base de dados do SQL Server é suportada numa instância gerida.

![Migração](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>Disponibilidade

### <a name="always-on-availability"></a>Always On

[Elevada disponibilidade](sql-database-high-availability.md) baseia-se para instância gerida e não pode ser controlado pelos utilizadores. Não são suportadas as seguintes instruções:

- [CRIE O PONTO FINAL... PARA DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CRIAR GRUPO DE DISPONIBILIDADE](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTERAR GRUPO DE DISPONIBILIDADE](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [GRUPO DE DISPONIBILIDADE DE SOLTAR](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- O [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) cláusula da [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) instrução

### <a name="backup"></a>Cópia de segurança

Instâncias geridas têm cópias de segurança automáticas, para que os utilizadores podem criar a base de dados completa `COPY_ONLY` cópias de segurança. Diferenciais, registo e de cópias de segurança de instantâneos de ficheiros não são suportadas.

- Com uma instância gerida, pode fazer backup de uma base de dados de instância apenas a uma conta de armazenamento de Blobs do Azure:
  - Apenas `BACKUP TO URL` é suportada.
  - `FILE`, `TAPE`, e dispositivos de cópia de segurança não são suportados.
- A maioria de gerais `WITH` as opções são suportadas.
  - `COPY_ONLY` é obrigatório.
  - `FILE_SNAPSHOT` Não é suportada.
  - As opções da banda: `REWIND`, `NOREWIND`, `UNLOAD`, e `NOUNLOAD` não são suportados.
  - Opções específicas do registo: `NORECOVERY`, `STANDBY`, e `NO_TRUNCATE` não são suportados.

Limitações: 

- Com uma instância gerida, pode fazer backup de uma base de dados de instância para uma cópia de segurança com até 32 reparte, que é o suficiente para bases de dados até 4 TB se for utilizada a compactação de backup.
- O tamanho máximo stripe de cópia de segurança utilizando o `BACKUP` comando numa instância gerida é 195 GB, o que é o tamanho máximo de Blobs. Aumente o número de reparte no comando de cópia de segurança para reduzir o tamanho do stripe individuais e se manter dentro este limite.

    > [!TIP]
    > Para contornar esta limitação, quando faz uma base de dados de qualquer um dos SQL Server num ambiente no local ou numa máquina virtual, pode:
    >
    > - Voltar ao `DISK` em vez de backup em `URL`.
    > - Carregue os ficheiros de cópia de segurança para armazenamento de Blobs.
    > - Restaure para a instância gerida.
    >
    > O `Restore` comando numa instância gerida suporta tamanhos de BLOBs maiores nos ficheiros de cópia de segurança, porque é utilizado um tipo de blob diferente para o armazenamento dos ficheiros de cópia de segurança carregados.

Para obter informações sobre cópias de segurança com o T-SQL, consulte [cópia de segurança](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Segurança

### <a name="auditing"></a>Auditoria

As principais diferenças entre auditoria em bases de dados na base de dados do Azure SQL e bancos de dados no SQL Server são:

- Com a opção de implementação de instância gerida na base de dados do Azure SQL, a auditoria funciona ao nível do servidor. O `.xel` ficheiros de registo são armazenados no armazenamento de Blobs do Azure.
- Com a base de dados individual e opções de implementação do conjunto elástico na base de dados do Azure SQL, a auditoria funciona ao nível da base de dados.
- No SQL Server no local ou máquinas virtuais, a auditoria funciona ao nível do servidor. Eventos são armazenados no sistema de ficheiros ou registos de eventos do Windows.
 
Auditoria de XEvent na instância gerida suporta destinos de armazenamento de Blobs do Azure. Registos de ficheiros e do Windows não são suportados.

A chave de diferenças no `CREATE AUDIT` sintaxe de auditoria para o armazenamento de Blobs do Azure são:

- Uma nova sintaxe `TO URL` é desde que pode usar para especificar o URL do contentor de armazenamento de Blobs do Azure onde o `.xel` ficheiros são colocados.
- A sintaxe `TO FILE` não é suportada uma vez que uma instância gerida não é possível aceder a partilhas de ficheiros do Windows.

Para obter mais informações, consulte: 

- [CRIAR A AUDITORIA DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Auditoria](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificados

Uma instância gerida não é possível aceder a partilhas de ficheiros e pastas do Windows, para que as seguintes restrições aplicam-se:

- O `CREATE FROM` / `BACKUP TO` ficheiro não é suportado para certificados.
- O `CREATE` / `BACKUP` certificado do `FILE` / `ASSEMBLY` não é suportada. Não não possível utilizar os ficheiros de chave privados. 

Ver [Criar certificado](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) e [certificados de cópia de segurança](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Solução**: O script para o certificado ou chave privada e armazenar como arquivo. SQL para criar a partir do binário:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Credencial

Apenas o Azure Key Vault e `SHARED ACCESS SIGNATURE` identidades são suportadas. Os utilizadores do Windows não são suportados.

Ver [criar CREDENCIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) e [ALTER CREDENCIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Fornecedores de criptografia

Uma instância gerida não é possível aceder aos ficheiros, pelo que não não possível criar provedores criptográficos:

- `CREATE CRYPTOGRAPHIC PROVIDER` Não é suportada. Ver [fornecedor de CRIPTOGRAFIA de criar](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` Não é suportada. Ver [fornecedor de CRIPTOGRAFIA de ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Inícios de sessão e utilizadores

- Inícios de sessão SQL criados utilizando `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, e `FROM SID` são suportados. Ver [criar início de sessão](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Principais de servidor do Azure Active Directory (Azure AD) (inícios de sessão) criados com o [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) sintaxe ou o [criar utilizador de início de sessão [início de sessão do Azure AD]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) sintaxe são suportadas (pré-visualização pública). Estes inícios de sessão são criados ao nível do servidor.

    Instância gerida suporta entidades de base de dados do Azure AD com a sintaxe `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Esta funcionalidade também é conhecido como os utilizadores de base de dados do Azure AD contido.

- Inícios de sessão do Windows criados com o `CREATE LOGIN ... FROM WINDOWS` sintaxe não são suportados. Utilize inícios de sessão do Azure Active Directory e os utilizadores.
- O utilizador do Azure AD que criou a instância tiver [irrestrito privilégios de administrador](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Os utilizadores de nível de base de dados não-administrador do Azure AD podem ser criados utilizando o `CREATE USER ... FROM EXTERNAL PROVIDER` sintaxe. Consulte [utilizador de criar... DO FORNECEDOR EXTERNO](sql-database-manage-logins.md#non-administrator-users).
- Principais de servidor do Azure AD (inícios de sessão) suportam a recursos SQL dentro de uma só instância gerida. Recursos que exigem a interação entre a instância, não importa se eles estão no mesmo Azure AD de inquilino ou inquilinos diferentes, não são suportados para utilizadores do Azure AD. Exemplos de tais recursos são:

  - Replicação transacional do SQL.
  - Servidor de ligação.

- Definir um início de sessão do AD do Azure mapeado para um grupo do Azure AD, como o proprietário da base de dados não é suportado.
- Representação de entidades de segurança do Azure AD ao nível do servidor através da utilização de outras entidades de segurança do Azure AD é suportada, tal como o [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) cláusula. Limitações de EXECUTE AS são:

  - EXECUTE AS USER não é suportada para utilizadores do Azure AD quando o nome é diferente do nome de início de sessão. Um exemplo é quando o utilizador é criado através da sintaxe CREATE USER [myAadUser] de início de sessão [john@contoso.com] e representação é tentada por meio de EXEC como utilizador = _myAadUser_. Quando cria um **USUÁRIO** a partir de um principal do servidor do Azure AD (início de sessão), especificar o user_name como o mesmo login_name partir **início de sessão**.
  - Apenas os SQL Server principais ao nível (inícios de sessão) que fazem parte do `sysadmin` função pode executar as seguintes operações destinados a entidades de segurança do Azure AD:

    - EXECUTAR COMO UTILIZADOR
    - EXECUTAR COMO INÍCIO DE SESSÃO

- Limitações da pré-visualização pública para principais de servidor do Azure AD (inícios de sessão):

  - Limitações de administração de diretório Active Directory para instância gerida:

    - O administrador do Azure AD utilizado para configurar a instância gerida não pode ser utilizado para criar um Azure AD principal do servidor (início de sessão) dentro da instância gerida. Tem de criar o primeiro Azure principal do servidor do AD (início de sessão) com uma conta do SQL Server que um `sysadmin` função. Esta limitação temporária será removida depois de principais de servidor do Azure AD (inícios de sessão) estar em disponibilidade geral. Se tentar utilizar uma conta de administrador do Azure AD para criar o início de sessão, verá o seguinte erro: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - Atualmente, o início de sessão do Azure AD primeiro criado na base de dados mestra tem de ser criado pela conta do SQL Server standard (não pertencente ao Azure AD) que um `sysadmin` função utilizando [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) do fornecedor externo. Após a disponibilidade geral, essa limitação será removida. Em seguida, pode criar um inicial início de sessão do Azure AD utilizando o administrador do Active Directory para instância gerida.
    - Utilizado com o SQL Server Management Studio ou SqlPackage DacFx (exportar/importar) não é suportada para inícios de sessão do Azure AD. Esta limitação será removida depois de principais de servidor do Azure AD (inícios de sessão) estar em disponibilidade geral.
    - Utilização de principais de servidor do Azure AD (inícios de sessão) com o SQL Server Management Studio:

      - Não é suportada a criação de scripts de inícios de sessão do Azure AD que utilizam qualquer início de sessão autenticado.
      - O IntelliSense não reconhece a instrução criar início de sessão do fornecedor externo e mostra um sublinhado em vermelho.

- Apenas ao nível do servidor de início de sessão principal, que é criado pelo aprovisionamento de processo, os membros das funções de servidor, tais como a instância gerida `securityadmin` ou `sysadmin`, ou outros inícios de sessão com a permissão ALTER ANY LOGIN ao nível do servidor podem criar o Azure AD principais de servidor (inícios de sessão) na base de dados mestra para a instância gerida.
- Se o início de sessão for uma entidade de segurança do SQL, apenas inícios de sessão que fazem parte do `sysadmin` função pode utilizar o comando create para criar inícios de sessão para uma conta do Azure AD.
- O início de sessão do Azure AD tem de ser um membro de um Azure AD no mesmo diretório que é utilizado para a instância gerida da base de dados SQL do Azure.
- Principais de servidor do Azure AD (inícios de sessão) são visíveis no Object Explorer a partir do SQL Server Management Studio 18.0 preview 5.
- É permitida a sobreposição de principais de servidor (inícios de sessão) do Azure AD com uma conta de administrador do Azure AD. Principais de servidor do Azure AD (inícios de sessão) têm precedência sobre o administrador do Azure AD ao resolver a entidade de segurança e aplicar permissões para a instância gerida.
- Durante a autenticação, a seguinte sequência é aplicada para resolver o principal de autenticação:

    1. Se a conta do Azure AD existe como diretamente mapeado para o principal do servidor do Azure AD (início de sessão), que está presente no sys.server_principals como tipo de "E", conceder acesso e aplicar permissões do principal de servidor do Azure AD (início de sessão).
    2. Se a conta do Azure AD é um membro de um grupo do Azure AD que está mapeado para o principal do servidor do Azure AD (início de sessão), que está presente no sys.server_principals como sendo do tipo "X", conceder acesso e aplicar permissões de início de sessão de grupo do Azure AD.
    3. Se a conta do Azure AD é um especial configurado o portal de administrador do Azure AD para a instância gerida, que não existe nas vistas de sistema de instância gerida, aplicar permissões especiais de fixas de administrador do Azure AD para a instância gerida (modo Legado).
    4. Se a conta do Azure AD existe como diretamente mapeado para um utilizador numa base de dados, que está presente no database_principals como tipo de "E", conceder acesso e aplicar permissões do utilizador de base de dados do Azure AD.
    5. Se a conta do Azure AD é um membro de um grupo do Azure AD que está mapeado para um utilizador numa base de dados, que está presente no database_principals como tipo de "X", conceder acesso e aplicar permissões de início de sessão de grupo do Azure AD.
    6. Se houver um início de sessão do AD do Azure mapeado para uma conta de utilizador do Azure AD ou uma conta de grupo do Azure AD, que é resolvido para o utilizador que está a autenticar, são aplicadas a todas as permissões deste início de sessão do Azure AD.

### <a name="service-key-and-service-master-key"></a>Chave de mestre de chave e de serviço do serviço

- [Cópia de segurança da chave mestra](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) não é suportada (gerido pelo serviço de base de dados SQL).
- [Restauro da chave mestra](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) não é suportada (gerido pelo serviço de base de dados SQL).
- [Cópia de segurança de chave mestra de serviço](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) não é suportada (gerido pelo serviço de base de dados SQL).
- [Restauro da chave mestra de serviço](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) não é suportada (gerido pelo serviço de base de dados SQL).

## <a name="configuration"></a>Configuração

### <a name="buffer-pool-extension"></a>Extensão do conjunto de memória intermédia

- [A extensão do conjunto da memória intermédia](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) não é suportada.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` Não é suportada. Ver [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Agrupamento

O agrupamento de instância padrão é `SQL_Latin1_General_CP1_CI_AS` e pode ser especificado como um parâmetro de criação. Ver [agrupamentos](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Níveis de compatibilidade

- Níveis de compatibilidade suportados são 100, 110, 120, 130 e 140.
- Não são suportados os níveis de compatibilidade inferior a 100.
- O nível de compatibilidade predefinido para novos bancos de dados é 140. Bases de dados restaurada, o nível de compatibilidade permanece inalterado, caso tenha sido 100 e superior.

Ver [nível de compatibilidade de base de dados ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Espelhamento da base de dados

Espelhamento de banco de dados não é suportado.

- `ALTER DATABASE SET PARTNER` e `SET WITNESS` opções não são suportadas.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` Não é suportada.

Para obter mais informações, consulte [ALTER DATABASE SET PARTNER e SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) e [CREATE ENDPOINT... PARA DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Opções de base de dados

- Não são suportados vários ficheiros de registo.
- Objetos em memória não são suportados na camada de serviço de fins gerais. 
- Existe um limite de 280 ficheiros por instância de fins gerais, o que implica um máximo de 280 ficheiros por base de dados. Ficheiros de dados e de registo no escalão fins gerais são contabilizados para este limite. [O escalão crítico para a empresa suporta 32.767 ficheiros por base de dados](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- A base de dados não pode conter grupos de ficheiros que contêm dados filestream. Restauro falha se contiver. bak `FILESTREAM` dados. 
- Cada arquivo é colocado no armazenamento de Blobs do Azure. E/s e o débito por ficheiro dependem do tamanho de cada ficheiro individual.

#### <a name="create-database-statement"></a>Instrução CREATE DATABASE

As seguintes limitações aplicam-se a `CREATE DATABASE`:

- Não não possível definir a arquivos e grupos de ficheiros. 
- O `CONTAINMENT` opção não é suportada. 
- `WITH` as opções não são suportadas. 
   > [!TIP]
   > Como solução, utilize `ALTER DATABASE` depois de `CREATE DATABASE` para definir as opções de base de dados para adicionar ficheiros ou para definir a contenção. 

- O `FOR ATTACH` opção não é suportada.
- O `AS SNAPSHOT OF` opção não é suportada.

Para obter mais informações, consulte [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instrução ALTER DATABASE

Algumas propriedades de ficheiro não não possível definir ou alterar:

- Não é possível especificar um caminho de ficheiro a `ALTER DATABASE ADD FILE (FILENAME='path')` instrução T-SQL. Remover `FILENAME` de scripts porque uma instância gerida coloca automaticamente os ficheiros. 
- Um nome de ficheiro não pode ser alterado usando o `ALTER DATABASE` instrução.

As seguintes opções estão definidas por predefinição e não podem ser alteradas:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

Não não possível modificar as seguintes opções:

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

Para obter mais informações, consulte [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- Definições de agente do SQL Server são só de leitura. O procedimento `sp_set_agent_properties` não é suportada na instância gerida. 
- Tarefas
  - Passos da tarefa de T-SQL são suportados.
  - São suportadas as seguintes tarefas de replicação:
    - Leitor de log de transação
    - Instantâneo
    - Distribuidor
  - Passos da tarefa SSIS são suportados.
  - Outros tipos de passos da tarefa não são atualmente suportados:
    - O passo de tarefa de replicação de intercalação não é suportado. 
    - Leitor de fila não é suportada. 
    - Shell de comandos ainda não é suportada.
  - Instâncias geridas não é possível aceder a recursos externos, por exemplo, partilhas de rede através do robocopy. 
  - SQL Server Analysis Services não é suportado.
- Notificações parcialmente são suportadas.
- Notificação por correio eletrónico é suportada, embora ele requer que configure um perfil de correio de base de dados. SQL Server Agent, pode utilizar apenas um perfil de correio de base de dados e tem de ser chamado `AzureManagedInstance_dbmail_profile`. 
  - Não é suportada paginação. 
  - NetSend não é suportada.
  - Alertas ainda não são suportados.
  - Proxies não são suportados. 
- Registo de eventos não é suportado.

Atualmente, as seguintes funcionalidades não são suportadas mas serão ativadas no futuro:

- Proxies
- Agendar tarefas numa CPU de inatividade
- Ativar ou desativar um agente
- Alertas

Para obter informações sobre o agente do SQL Server, consulte [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabelas

As tabelas seguintes não são suportadas:

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED` 

Para obter informações sobre como criar e alterar as tabelas, veja [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) e [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funcionalidades

### <a name="bulk-insert--openrowset"></a>Inserção em massa / openrowset

Uma instância gerida não é possível aceder a partilhas de ficheiros e pastas do Windows, para que os ficheiros têm de ser importados do armazenamento de Blobs do Azure:

- `DATASOURCE` é necessária a `BULK INSERT` comando enquanto importar ficheiros do armazenamento de Blobs do Azure. Ver [inserção em massa](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` é necessária a `OPENROWSET` funcionar quando ler o conteúdo de um ficheiro do armazenamento de Blobs do Azure. Ver [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR

Uma instância gerida não é possível aceder a partilhas de ficheiros e pastas do Windows, para que as seguintes restrições aplicam-se:

- Apenas `CREATE ASSEMBLY FROM BINARY` é suportada. Ver [CREATE ASSEMBLY de binário](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` Não é suportada. Ver [CREATE ASSEMBLY de ficheiro](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` não pode referenciar arquivos. Ver [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="dbcc"></a>DBCC

Declarações DBCC não documentadas que estão ativadas no SQL Server não são suportadas em instâncias geridas.

- `Trace flags` não são suportados. Ver [sinalizadores de rastreio](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` Não é suportada. Ver [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` Não é suportada. Ver [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Transações distribuídas

MSDTC e [transações elásticas](sql-database-elastic-transactions-overview.md) atualmente, não são suportadas em instâncias geridas.

### <a name="extended-events"></a>Eventos Expandidos

Alguns destinos de Windows específicas de eventos expandidos (XEvents) não são suportados:

- O `etw_classic_sync` destino não é suportado. Store `.xel` ficheiros no armazenamento de Blobs do Azure. Ver [etw_classic_sync destino](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- O `event_file` destino não é suportado. Store `.xel` ficheiros no armazenamento de Blobs do Azure. Ver [event_file destino](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Bibliotecas externas

R e Python na base de dados, bibliotecas externas não são, mas suportam. Ver [serviços de aprendizagem de máquina do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream e FileTable

- dados FileStream não são suportados.
- A base de dados não pode conter grupos de ficheiros com `FILESTREAM` dados.
- `FILETABLE` Não é suportada.
- As tabelas não podem ter `FILESTREAM` tipos.
- Não são suportadas as seguintes funções:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Para obter mais informações, consulte [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) e [FileTables](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Pesquisa semântica de texto completo

[Pesquisa semântica](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) não é suportada.

### <a name="linked-servers"></a>Servidores ligados

Servidores ligados em instâncias geridas suportarem um número limitado de destinos:

- Destinos suportados são do SQL Server e base de dados SQL.
- Destinos que não são suportados são arquivos, Analysis Services e outros RDBMS.

Operações

- Transações de escrita de instância para várias não são suportadas.
- `sp_dropserver` é suportada para remover um servidor ligado. See [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- O `OPENROWSET` função pode ser utilizada para executar consultas apenas em instâncias do SQL Server. Eles podem ser gerido, no local, ou em máquinas virtuais. Ver [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- O `OPENDATASOURCE` função pode ser utilizada para executar consultas apenas em instâncias do SQL Server. Eles podem ser gerido, no local, ou em máquinas virtuais. Apenas os `SQLNCLI`, `SQLNCLI11`, e `SQLOLEDB` valores são suportados como um fornecedor. Um exemplo é `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Ver [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="polybase"></a>PolyBase

Externo tabelas essa referência que não são suportados os ficheiros no armazenamento do HDFS ou de Blobs do Azure. Para obter informações sobre o PolyBase, veja [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replicação

A replicação está disponível para pré-visualização pública para a instância gerida. Para obter informações sobre a replicação, consulte [replicação do SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance).

### <a name="restore-statement"></a>RESTAURAR a instrução 

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
  - `FROM URL` (Armazenamento de Blobs do azure) é a única opção suportada.
  - `FROM DISK`/`TAPE`/ dispositivo de cópia de segurança não é suportado.
  - Os conjuntos de cópia de segurança não são suportados.
- `WITH` as opções não são suportadas, como não `DIFFERENTIAL` ou `STATS`.
- `ASYNC RESTORE`: Restauro continua, mesmo que a quebra a ligação de cliente. Se sua conexão cair, pode verificar o `sys.dm_operation_status` vista para o estado de uma operação de restauro e para uma base de dados criar e LARGUE. Ver [DM operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

As seguintes opções de base de dados estiverem definidas ou substituídas e não é possível alterar mais tarde: 

- `NEW_BROKER` Se o agente não está ativado no ficheiro. bak. 
- `ENABLE_BROKER` Se o agente não está ativado no ficheiro. bak. 
- `AUTO_CLOSE=OFF` Se tiver uma base de dados no ficheiro. bak `AUTO_CLOSE=ON`. 
- `RECOVERY FULL` Se tiver uma base de dados no ficheiro. bak `SIMPLE` ou `BULK_LOGGED` modo de recuperação.
- Um grupo de ficheiros com otimização de memória é adicionado e chamado XTP se não fosse no ficheiro. bak de origem. 
- Qualquer grupo de ficheiros existente com otimização de memória foi mudado para XTP. 
- `SINGLE_USER` e `RESTRICTED_USER` opções são convertidas em `MULTI_USER`.

Limitações: 

- `.BAK` não não possível restaurar os ficheiros que contêm vários conjuntos de cópia de segurança. 
- `.BAK` não não possível restaurar os ficheiros que contêm vários ficheiros de registo.
- Restauro falha se contiver. bak `FILESTREAM` dados.
- Não não possível restaurar cópias de segurança que contêm bases de dados com objetos do Active Directory dentro da memória numa instância de fins gerais. Para obter informações sobre instruções de restauro, veja [instruções de RESTAURO](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Mediador de serviço

Instância de entre o service broker não é suportado:

- `sys.routes`: Como pré-requisito, tem de selecionar o endereço de sys.routes. O endereço tem de ser LOCAL em cada rota. Ver [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Não é possível usar `CREATE ROUTE` com `ADDRESS` diferente de `LOCAL`. Ver [criar rota](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Não é possível usar `ALTER ROUTE` com `ADDRESS` diferente de `LOCAL`. Ver [ALTER rota](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Procedimentos armazenados, funções e acionadores

- `NATIVE_COMPILATION` Não é suportada no escalão fins gerais.
- O seguinte procedimento [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) opções não são suportadas: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` Não é suportada. Ver [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` Não é suportada. Ver [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` não são suportados, que inclui `sp_addextendedproc`  e `sp_dropextendedproc`. Ver [procedimentos armazenados expandidos](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db`, e `sp_detach_db` não são suportados. Ver [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql), e [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

## <a name="Environment"></a>Restrições de Environmet

### <a name="subnet"></a>Subrede
- Na sub-rede reservada para a sua instância gerida não é possível colocar a quaisquer outros recursos (por exemplo, máquinas virtuais). Colocar estes recursos em outras sub-redes.
- Sub-rede tem de ter um número suficiente de disponíveis [endereços IP](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Mínimo é de 16, enquanto recomenda-se de ter pelo menos 32 endereços IP na sub-rede.
- [Pontos finais de serviço não podem ser associados a sub-rede a instância gerida](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Certifique-se de que a opção de pontos finais de serviço é desabilitada quando criar a rede virtual.
- O número e tipos de instâncias que podem ser colocados na sub-rede tem alguns [restrições e limites](sql-database-managed-instance-resource-limits.md#strategies-for-deploying-mixed-general-purpose-and-business-critical-instances)
- Existem algumas [regras de segurança que devem ser aplicadas na sub-rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>VNET
- VNet pode ser implementada com o modelo de recursos - modelo clássico para a VNet não é suportada.
- Alguns serviços, como ambientes de serviço de aplicações, aplicações lógicas e instâncias geridas (usado para georreplicação, os replicação transacional, ou através de servidores ligados) não é possível aceder a instâncias geridas em regiões diferentes, se as VNets estão ligadas utilizando [global peering](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Pode ligar a estes recursos através do ExpressRoute ou VNet a VNet através de Gateways de VNet.

## <a name="Changes"></a> Alterações de comportamento

As seguintes variáveis, funções e exibições devolvem resultados diferentes:

- `SERVERPROPERTY('EngineEdition')` Devolve o valor de 8. Esta propriedade identifica exclusivamente uma instância gerida. Ver [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` Devolve Nulo porque o conceito de instância, pois existe para o SQL Server não se aplica a uma instância gerida. Ver [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Devolve um nome de "conectável" DNS completo, por exemplo, meu instance.wcus17662feb9ce98.database.windows.net geridos. Ver [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` Devolve um nome de "conectável" DNS completo, como `myinstance.domain.database.windows.net` para as propriedades "name" e "data_source." Consulte [SYS. SERVIDORES](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` Devolve Nulo porque o conceito de serviço, pois existe para o SQL Server não se aplica a uma instância gerida. Ver [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` é suportada. Ela retorna NULL se o início de sessão do Azure AD não está no sys.syslogins. Ver [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` Não é suportada. É devolvido o de dados incorreto, que é uma problema conhecido de temporária. Ver [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Issues"></a> Limitações e problemas conhecidos

### <a name="tempdb-size"></a>Tamanho TEMPDB

O tamanho máximo de `tempdb` não pode ser superior a 24 GB por núcleo de um escalão de fins gerais. O máximo `tempdb` tamanho de um escalão crítico para a empresa é limitado com o tamanho de armazenamento de instância. O `tempdb` base de dados sempre é dividido em arquivos de dados de 12. Este tamanho máximo por ficheiro não pode ser alterado e não não possível adicionar novos ficheiros `tempdb`. Algumas consultas podem devolver um erro se precisarem de mais de 24 GB por núcleo no `tempdb`. `tempdb` é sempre novamente criado como uma base de dados vazio quando o início de instância ou a ativação pós-falha e a qualquer alteração feitas no `tempdb` não serão mantidas. 

### <a name="cant-restore-contained-database"></a>Não é possível restaurar a base de dados contida

Não é possível restaurar a instância gerida [bases de dados contidas](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Restauro de ponto no tempo das bases de dados contidas existentes não funciona na instância gerida. Este problema será resolvido em breve. Entretanto, recomendamos que remova a opção de contenção de seus bancos de dados que são colocados na instância gerida. Não utilize a opção de contenção para as bases de dados de produção. 

### <a name="exceeding-storage-space-with-small-database-files"></a>Que exceda o espaço de armazenamento com ficheiros de base de dados pequena

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`, e `RESTORE DATABASE` instruções podem falhar porque a instância pode atingir o limite de armazenamento do Azure.

Cada instância gerida de fins gerais tem até 35 TB de armazenamento reservado para o espaço em disco do Azure Premium. Cada ficheiro de base de dados é colocado no disco físico separado. Tamanhos de disco podem ser 128 GB, 256 GB, 512 GB, 1 TB ou 4 TB. Não é cobrado o espaço não utilizado no disco, mas a soma total dos tamanhos de disco do Azure Premium não pode ter mais de 35 TB. Em alguns casos, uma instância gerida que não necessita de 8 TB no total pode ter mais de 35 TB Azure limite para o tamanho de armazenamento devido a fragmentação interna.

Por exemplo, uma instância gerida de fins gerais pode ter um ficheiro que está a 1,2 TB de tamanho colocado num disco de 4 TB. Também pode ter 248 ficheiros que estão a cada 1 GB de tamanho de que são colocados em discos separados de 128 GB. Neste exemplo:

- O tamanho de armazenamento do disco alocado total é 1 x 4 TB + GB de 248 x 128 = 35 TB.
- total de espaço reservado para bases de dados na instância é 1 x 1.2 TB + GB de 248 1 = 1,4 TB.

Este exemplo ilustra que, em determinadas circunstâncias, devido a uma distribuição específica de ficheiros, uma instância gerida pode atingir o limite de 35 TB que está reservado para um disco de Premium do Azure ligado quando pode esperar que ele não.

Neste exemplo, bancos de dados existentes continuam a funcionar e cresça sem qualquer problema, desde que não são adicionados novos ficheiros. Novas bases de dados não não possível criar ou restaurados porque não existe espaço suficiente para novas unidades de disco, mesmo que o tamanho total de todas as bases de dados não atinge o limite de tamanho de instância. O erro devolvido nesse caso não está claro.

Pode [identificar o número de ficheiros restantes](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) utilizando vistas de sistema. Se atingir este limite, tente [vazio e eliminar alguns dos ficheiros mais pequenos com a instrução DBCC SHRINKFILE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) ou mudar para o [escalão crítico para a empresa, que não tem este limite](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="incorrect-configuration-of-the-sas-key-during-database-restore"></a>Restaurar a configuração incorreta da chave SAS durante a base de dados

`RESTORE DATABASE` que o lê o ficheiro. bak poderá ser constantemente Repetir para ler o. bak de ficheiros e devolver um erro após um longo período de tempo se a assinatura de acesso partilhado no `CREDENTIAL` está incorreto. Execute RESTORE HEADERONLY antes de restaurar uma base de dados não se esqueça de que a chave SAS está correta.
Certifique-se de que remove o líder `?` da chave de SAS gerado com o portal do Azure.

### <a name="tooling"></a>Ferramentas

SQL Server Management Studio e SQL Server Data Tools podem ter alguns problemas enquanto acedem a uma instância gerida.

- Utilizar principais de servidor do Azure AD (inícios de sessão) e os utilizadores (pré-visualização pública) com o SQL Server Data Tools atualmente não é suportada.
- Scripts para principais de servidor do Azure AD (inícios de sessão) e utilizadores (pré-visualização pública) não não suportada no SQL Server Management Studio.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Nomes de base de dados incorreto no algumas vistas, registos e as mensagens

Várias vistas de sistema, contadores de desempenho, mensagens de erro, XEvents e entradas de registo de erro ao apresentam identificadores de base de dados GUID, em vez dos nomes de banco de dados real. Não se baseie nestes identificadores GUID porque eles são substituídos com nomes de banco de dados real no futuro.

### <a name="database-mail"></a>Correio de base de dados

O `@query` parâmetro na [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) procedimento não funciona.

### <a name="database-mail-profile"></a>Perfil de correio de base de dados

O perfil de correio de base de dados utilizado pelo SQL Server Agent tem de ser chamado `AzureManagedInstance_dbmail_profile`. Não há restrições para outros nomes de perfil de correio de base de dados.

### <a name="error-logs-arent-persisted"></a>Registos de erros não são mantidos

Registos de erros que estão disponíveis na instância gerida não são mantidos e seu tamanho não está incluído no limite máximo de armazenamento. Registos de erros podem ser apagados automaticamente se ocorrer a ativação pós-falha.

### <a name="error-logs-are-verbose"></a>Registos de erros são detalhados

Uma instância gerida coloca informações verbosas em registos de erros e, muitas delas não é relevante. A quantidade de informações nos registos de erro sofrerão uma redução no futuro.

**Solução:** Use um procedimento personalizado para ler registos de erros que filtra os algumas entradas irrelevantes. Para obter mais informações, consulte [instância gerida – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Escopo da transação nas duas bases de dados dentro da instância do mesmo não é suportado

O `TransactionScope` classe no .NET não funciona se duas consultas são enviadas para duas bases de dados dentro da mesma instância sob o mesmo escopo de transação:

```C#
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

Embora esse código funciona com os dados dentro da instância do mesmo, ele exigia MSDTC.

**Solução:** Utilizar [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) utilizar outra base de dados num contexto de ligação em vez de usar duas conexões.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Módulos CLR e, às vezes, os servidores ligados não é possível referenciar um endereço IP local

Módulos CLR colocados numa instância gerida e servidores vinculados ou consultas distribuídas que fazem referência a uma instância atual, por vezes, não é possível resolver o IP de uma instância local. Este erro é um problema transitório.

**Solução:** Se for possível utilize ligações de contexto num módulo CLR.

### <a name="tde-encrypted-databases-with-a-service-managed-key-dont-support-user-initiated-backups"></a>Encriptação TDE bases de dados com uma chave gerida pelo serviço não suportam cópias de segurança iniciada pelo utilizador

Não é possível executar `BACKUP DATABASE ... WITH COPY_ONLY` numa base de dados são encriptados com gerida pelo serviço dados encriptação transparente (TDE). TDE gerida pelo serviço força seja encriptada com uma chave TDE interna. Não é possível exportar a chave, para que não é possível restaurar a cópia de segurança.

**Solução:** Utilize cópias de segurança automáticas e restauro de ponto no tempo, ou utilize [gerida pelo cliente (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) em vez disso. Também pode desativar a encriptação na base de dados.

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre as instâncias geridas, consulte [o que é uma instância gerida?](sql-database-managed-instance.md)
- Para funcionalidades e lista de comparação, veja [comparação de funcionalidades de base de dados do Azure SQL](sql-database-features.md).
- Para um início rápido mostra-lhe como criar uma nova instância gerida, veja [criar uma instância gerida](sql-database-managed-instance-get-started.md).
