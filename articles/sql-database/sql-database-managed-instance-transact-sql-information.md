---
title: Diferenças do T-SQL de instância de gerida de base de dados SQL do Azure | Documentos da Microsoft
description: Este artigo aborda as diferenças do T-SQL entre uma instância gerida na base de dados do Azure SQL e SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 02/20/2019
ms.openlocfilehash: 98ca3478c3a8963c3bf57143354340d6ed14900e
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594343"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Diferenças de SQL da base de dados geridos instância T-SQL do Azure do SQL Server

A opção de implementação de instância gerida fornece compatibilidade com o motor de base de dados de servidor de SQL no local. A maioria das funcionalidades do motor de base de dados do SQL Server é suportada numa instância gerida.

![Migração](./media/sql-database-managed-instance/migration.png)

Uma vez que ainda existem algumas diferenças na sintaxe e o comportamento, este artigo resume e explica essas diferenças. <a name="Differences"></a>
- [Disponibilidade](#availability) incluindo as diferenças nos [Always-On](#always-on-availability) e [cópias de segurança](#backup),
- [Segurança](#security) incluindo as diferenças nos [auditoria](#auditing), [certificados](#certificates), [credenciais](#credential), [provedores criptográficos](#cryptographic-providers), [Inícios de sessão / utilizadores](#logins--users), [chave e a chave mestra de serviço do serviço](#service-key-and-service-master-key),
- [Configuração](#configuration) incluindo as diferenças nos [a extensão do conjunto da memória intermédia](#buffer-pool-extension), [agrupamento](#collation), [níveis de compatibilidade](#compatibility-levels),[base de dados espelhamento](#database-mirroring), [opções de base de dados](#database-options), [SQL Server Agent](#sql-server-agent), [opções da tabela](#tables),
- [Funcionalidades](#functionalities) incluindo [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [transações distribuídas](#distributed-transactions), [ Eventos expandidos](#extended-events), [bibliotecas externas](#external-libraries), [Filestream e Filetable](#filestream-and-filetable), [pesquisa semântica de texto completo](#full-text-semantic-search), [ligado servidores](#linked-servers), [Polybase](#polybase), [replicação](#replication), [restaurar](#restore-statement), [Service Broker](#service-broker), [ Os procedimentos armazenados, funções e disparadores](#stored-procedures-functions-triggers),
- [Recursos que têm um comportamento diferente em instâncias geridas](#Changes)
- [Limitações temporárias e problemas conhecidos](#Issues)

## <a name="availability"></a>Disponibilidade

### <a name="always-on-availability"></a>Always On

[Elevada disponibilidade](sql-database-high-availability.md) baseia-se para instância gerida e não pode ser controlado pelos utilizadores. Não são suportadas as seguintes instruções:

- [CRIE O PONTO FINAL... PARA DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CRIAR GRUPO DE DISPONIBILIDADE](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTERAR GRUPO DE DISPONIBILIDADE](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [GRUPO DE DISPONIBILIDADE DE SOLTAR](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) cláusula do [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) instrução

### <a name="backup"></a>Cópia de segurança

Instâncias geridas têm cópias de segurança automáticas e permitir que os utilizadores criar a base de dados completa `COPY_ONLY` cópias de segurança. Diferenciais, registo e de cópias de segurança de instantâneos de ficheiros não são suportadas.

- Com a instância gerida, pode fazer backup de uma base de dados de instância apenas a uma conta de armazenamento de Blobs do Azure:
  - Apenas `BACKUP TO URL` é suportada
  - `FILE`, `TAPE`, e os dispositivos de cópia de segurança não são suportados  
- A maioria de gerais `WITH` as opções são suportadas
  - `COPY_ONLY` é obrigatório
  - `FILE_SNAPSHOT` Não é suportado
  - As opções da banda: `REWIND`, `NOREWIND`, `UNLOAD`, e `NOUNLOAD` não são suportados
  - Opções específicas do registo: `NORECOVERY`, `STANDBY`, e `NO_TRUNCATE` não são suportados

Limitações:  

- Com a instância gerida, pode fazer backup de uma base de dados de instância para uma cópia de segurança com até 32 reparte, que é o suficiente para as bases de dados até 4 TB se for utilizada a compactação de backup.
- Tamanho máximo do stripe de cópia de segurança é 195 GB (tamanho máximo de BLOBs). Aumente o número de reparte no comando de cópia de segurança para reduzir o tamanho do stripe individuais e se manter dentro este limite.

> [!TIP]
> Para contornar esta limitação no local, cópia de segurança `DISK` em vez de cópia de segurança para `URL`, carregar o ficheiro de cópia de segurança para BLOBs, em seguida, restaurar. Restauro suporta ficheiros maiores porque é utilizado um tipo de blob diferente.  

Para obter informações sobre cópias de segurança com o T-SQL, consulte [cópia de segurança](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Segurança

### <a name="auditing"></a>Auditoria

As principais diferenças entre auditoria em bases de dados na base de dados do Azure SQL e bancos de dados no SQL Server são:

- Com a opção de implementação de instância gerida na base de dados do Azure SQL, a auditoria funciona no nível do servidor e arquivos `.xel` ficheiros de registo no armazenamento de Blobs do Azure.
- Com a base de dados individual e opções de implementação do conjunto elástico na base de dados do Azure SQL, a auditoria funciona ao nível da base de dados.
- No SQL Server no local / virtual máquinas, funciona de auditoria no servidor de nível, mas armazena os eventos nos registos de eventos do windows/sistema de ficheiros.
  
Auditoria de XEvent na instância gerida suporta destinos de armazenamento de Blobs do Azure. Registos de ficheiros e do windows não são suportados.

A chave de diferenças no `CREATE AUDIT` sintaxe para a auditoria para o armazenamento de Blobs do Azure são:

- Uma nova sintaxe `TO URL` é fornecido e permite-lhe especificar o URL do contentor de armazenamento de Blobs do Azure onde `.xel` serão colocados ficheiros
- A sintaxe `TO FILE` não é suportada uma vez que uma instância gerida não é possível aceder a partilhas de ficheiros do Windows.

Para obter mais informações, consulte:  

- [CRIAR A AUDITORIA DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Auditoria](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificados

Uma instância gerida não é possível aceder a partilhas de ficheiros e pastas do Windows, para que as seguintes restrições aplicam-se:

- `CREATE FROM`/`BACKUP TO` ficheiro não é suportado para certificados
- `CREATE`/`BACKUP` certificado do `FILE` / `ASSEMBLY` não é suportada. Não não possível utilizar os ficheiros de chave privados.  

Ver [Criar certificado](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) e [certificados de cópia de segurança](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql).  
  
**Solução**: chave privada do certificado do script, guarde como ficheiro. SQL e criar a partir do binário:

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

### <a name="logins--users"></a>Inícios de sessão / utilizadores

- Inícios de sessão SQL criados `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, e `FROM SID` são suportados. Ver [criar início de sessão](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Principais de servidor do Azure Active Directory (Azure AD) (inícios de sessão) criados com [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) sintaxe ou o [criar utilizador de início de sessão [início de sessão do Azure AD]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) sintaxe são suportados (**pré-visualização pública** ). Estes são criados ao nível do servidor de inícios de sessão.
    - Instância gerida suporta entidades de base de dados do Azure AD com a sintaxe `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Isso também é conhecido como os utilizadores de base de dados do Azure AD contido.
- Inícios de sessão do Windows criados com `CREATE LOGIN ... FROM WINDOWS` sintaxe não são suportados. Utilize inícios de sessão do Azure Active Directory e os utilizadores.
- Tem de utilizador do Azure AD que criou a instância [irrestrito privilégios de administrador](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Utilizadores de nível de base de dados do Azure Active Directory (Azure AD) não administradores podem ser criados usando `CREATE USER ... FROM EXTERNAL PROVIDER` sintaxe. Consulte [utilizador de criar... DO FORNECEDOR EXTERNO](sql-database-manage-logins.md#non-administrator-users).
- Principais de servidor do Azure AD (inícios de sessão) suportam a recursos SQL na instância de uma MI apenas. Funcionalidades que requerem a interação entre a instância, independentemente se o mesmo Azure AD dentro de inquilino ou inquilinos diferentes não são suportados para utilizadores do Azure AD. Exemplos de tais recursos são:
    - Replicação transacional do SQL e
    - Servidor de ligação
- Definir um início de sessão do AD do Azure mapeado para um grupo do Azure AD, como o proprietário da base de dados não é suportado.
- Representação de principais de ao nível do servidor do Azure AD com outras entidades de segurança do Azure AD é suportada, tal como o [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) cláusula. EXECUTE como limitação:
    - EXECUTE AS USER não é suportada para utilizadores do Azure AD quando o nome é diferente do nome de início de sessão. Por exemplo, quando o utilizador é criado através da sintaxe CREATE USER [myAadUser] de início de sessão [john@contoso.com], e representação é tentada por meio de EXEC como utilizador = _myAadUser_. Ao criar um **USUÁRIO** a partir de um principal do servidor do Azure AD (início de sessão), especificar o user_name como o mesmo login_name partir **início de sessão**.
    - Apenas os SQL server principais ao nível (inícios de sessão) que fazem parte do `sysadmin` função pode executar as seguintes operações de filtragem de entidades de segurança do Azure AD: 
        - EXECUTAR COMO UTILIZADOR
        - EXECUTAR COMO INÍCIO DE SESSÃO
- **Pré-visualização pública** limitações para principais de servidor do Azure AD (inícios de sessão):
    - Limitações de administração de diretório Active Directory para instância gerida:
        - O administrador do Azure AD utilizado para configurar a instância gerida não pode ser utilizado para criar um Azure AD principal do servidor (início de sessão) dentro da instância gerida. Tem de criar o primeiro Azure principal do servidor AD (início de sessão) a utilizar uma conta do SQL Server que é um `sysadmin`. Esta é uma limitação temporária que será removida depois de principais de servidor do Azure AD (inícios de sessão) tornam-se em GA. Verá o seguinte erro se tentar utilizar uma conta de administrador do Azure AD para criar o início de sessão: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
        - Atualmente, o início de sessão do Azure AD primeiro criado na BD principal tem de ser criado pela conta do SQL Server standard (não o Azure AD) é uma `sysadmin` utilizando o [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) do fornecedor externo. GA do POST, essa limitação serão removidas e um inicial início de sessão do Azure AD pode ser criado pelo administrador do Active Directory para instância gerida.
    - Utilizado com o SQL Server Management Studio (SSMS) ou SqlPackage DacFx (exportar/importar) não é suportada para inícios de sessão do Azure AD. Esta limitação será removida depois de principais de servidor do Azure AD (inícios de sessão) tornam-se em GA.
    - A utilização de principais de servidor do Azure AD (inícios de sessão) com o SSMS
        - Não é suportada a criação de scripts de inícios de sessão do Azure AD (com qualquer início de sessão autenticado).
        - O IntelliSense não reconhece os **criar início de sessão do fornecedor externo** instrução e mostrará um sublinhado em vermelho.
- Apenas ao nível do servidor de início de sessão principal (criado pela instância gerida, processo de aprovisionamento), os membros das funções de servidor (`securityadmin` ou `sysadmin`), ou outros inícios de sessão com a permissão ALTER ANY LOGIN ao nível do servidor podem criar servidor do Azure AD entidades de segurança (inícios de sessão) na base de dados mestra para a instância gerida.
- Se o início de sessão é uma entidade de segurança do SQL, apenas inícios de sessão que fazem parte do `sysadmin` função pode utilizar o comando create para criar inícios de sessão para uma conta do Azure AD.
- Início de sessão do Azure AD tem de ser um membro de um Azure AD no mesmo diretório utilizado para a instância gerida do SQL do Azure.
- Principais de servidor do Azure AD (inícios de sessão) são visíveis no object explorer lhe que comece com o SSMS 18.0 preview 5.
- É permitida a sobreposição de principais de servidor (inícios de sessão) do Azure AD com uma conta de administrador do Azure AD. Principais de servidor do Azure AD (inícios de sessão) têm precedência sobre administrador do Azure AD ao resolver as permissões do principal e a aplicar à instância gerida.
- Durante a autenticação, a seguir a sequência é aplicada para resolver o principal de autenticação:
    1. Se a conta do Azure AD existe como mapeadas diretamente para o Azure AD principal do servidor (início de sessão) (presente no sys.server_principals como tipo de "E"), conceder acesso e aplicar permissões do principal de servidor do Azure AD (início de sessão).
    2. Se a conta do Azure AD é um membro de um grupo do Azure AD que está mapeado para o principal do servidor do Azure AD (início de sessão) (presente no sys.server_principals como sendo do tipo 'X'), conceder acesso e aplicar permissões de início de sessão de grupo do Azure AD.
    3. Se a conta do Azure AD é um especial configurado o portal de administrador do Azure AD para a instância gerida (não existe nas vistas de sistema de instância gerida), aplicar permissões especiais de fixas de administrador do Azure AD para a instância gerida (modo Legado).
    4. Se a conta do Azure AD existe como diretamente mapeado para um utilizador numa base de dados (em database_principals como tipo de "E"), conceder acesso e aplicar permissões do utilizador de base de dados do Azure AD.
    5. Se a conta do Azure AD é membro de um grupo do Azure AD que está mapeado para um utilizador numa base de dados (em database_principals como 'X' de tipo), conceder acesso e aplicar permissões de início de sessão de grupo do Azure AD.
    6. Se houver um início de sessão do AD do Azure mapeado para uma conta de utilizador do Azure AD ou uma conta de grupo do Azure AD, a resolver para o utilizador a autenticar, todas as permissões deste início de sessão do Azure AD serão aplicadas.






### <a name="service-key-and-service-master-key"></a>Chave de mestre de chave e de serviço do serviço

- [Cópia de segurança da chave mestra](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) não é suportada (gerido pelo serviço de base de dados SQL)
- [Restauro da chave mestra](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) não é suportada (gerido pelo serviço de base de dados SQL)
- [Cópia de segurança de chave mestra de serviço](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) não é suportada (gerido pelo serviço de base de dados SQL)
- [Restauro da chave mestra de serviço](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) não é suportada (gerido pelo serviço de base de dados SQL)

## <a name="configuration"></a>Configuração

### <a name="buffer-pool-extension"></a>Extensão do conjunto de memória intermédia

- [A extensão do conjunto da memória intermédia](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) não é suportada.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` Não é suportada. Ver [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Agrupamento

O agrupamento de instância padrão é `SQL_Latin1_General_CP1_CI_AS` e pode ser especificado como um parâmetro de criação. Ver [agrupamentos](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Níveis de compatibilidade

- Níveis de compatibilidade suportados são: 100, 110, 120, 130, 140  
- Não são suportados os níveis de compatibilidade inferior a 100.
- O nível de compatibilidade predefinido para novos bancos de dados é 140. Bases de dados restaurada, nível de compatibilidade permanecerá inalterado caso tenha sido 100 e superior.

Ver [nível de compatibilidade de base de dados ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Espelhamento da base de dados

Espelhamento de banco de dados não é suportado.

- `ALTER DATABASE SET PARTNER` e `SET WITNESS` opções não são suportadas.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` Não é suportada.

Para obter mais informações, consulte [ALTER DATABASE SET PARTNER e SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) e [CREATE ENDPOINT... PARA DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Opções de base de dados

- Não são suportados vários ficheiros de registo.
- Objetos em memória não são suportados na camada de serviço de fins gerais.  
- Existe um limite de 280 ficheiros por instância, implicando 280 ficheiros máximos por base de dados. Ficheiros de registo e dados são contabilizados para este limite.  
- Base de dados não pode conter grupos de ficheiros que contêm dados filestream.  Restauro falhará se contiver. bak `FILESTREAM` dados.  
- Cada arquivo é colocado no armazenamento de Blobs do Azure. E/s e o débito por ficheiro dependem do tamanho de cada ficheiro individual.  

#### <a name="create-database-statement"></a>Instrução CREATE DATABASE

Seguem-se `CREATE DATABASE` limitações:

- Não não possível definir a arquivos e grupos de ficheiros.  
- `CONTAINMENT` opção não é suportada.  
- `WITH`as opções não são suportadas.  
   > [!TIP]
   > Como solução alternativa, utilize `ALTER DATABASE` depois de `CREATE DATABASE` para definir as opções de base de dados para adicionar ficheiros ou para definir a contenção.  

- `FOR ATTACH` opção não é suportada
- `AS SNAPSHOT OF` opção não é suportada

Para obter mais informações, consulte [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instrução ALTER DATABASE

Algumas propriedades de ficheiro não não possível definir ou alterar:

- Não é possível especificar o caminho do ficheiro no `ALTER DATABASE ADD FILE (FILENAME='path')` instrução T-SQL. Remover `FILENAME` de scripts porque uma gerida instância automaticamente coloca os ficheiros.  
- Nome de ficheiro não pode ser alterado usando `ALTER DATABASE` instrução.

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

- Definições do agente do SQL são só de leitura. Procedimento `sp_set_agent_properties` não é suportada na instância gerida.  
- Tarefas
  - Passos da tarefa de T-SQL são suportados.
  - São suportadas as seguintes tarefas de replicação:
    - Leitor de log de transação
    - Instantâneo
    - Distribuidor
  - Passos da tarefa SSIS são suportados
  - Outros tipos de passos da tarefa não são atualmente suportados, incluindo:
    - Passo de tarefa de replicação de intercalação não é suportado.  
    - Leitor de fila não é suportada.  
    - Shell de comandos ainda não é suportada.
  - Instâncias geridas não é possível aceder a recursos externos (por exemplo, compartilhamentos de rede através do robocopy).  
  - PowerShell ainda não é suportado.
  - Não é suportado do Analysis Services.
- Notificações parcialmente são suportadas.
- Notificação por correio eletrónico é suportado, necessita de configurar um perfil de correio de base de dados. É possível que o perfil de correio de base de dados apenas uma e tem de ser chamado `AzureManagedInstance_dbmail_profile` em pré-visualização pública (limitação temporária).  
  - Não é suportada paginação.  
  - NetSend não é suportada.
  - Alertas ainda não são suportados.
  - Proxies não são suportados.  
- Registo de eventos não é suportado.

As seguintes funcionalidades não são atualmente suportadas, mas serão ativadas no futuro:

- Proxies
- Agendamento de trabalhos em ocioso da CPU
- Agente de ativação/desativação
- Alertas

Para obter informações sobre o agente do SQL Server, consulte [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabelas

Não são suportadas as seguintes:

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED`  

Para obter informações sobre a criação e tabelas de alteração, consulte [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) e [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funcionalidades

### <a name="bulk-insert--openrowset"></a>Inserção em massa / openrowset

Uma instância gerida não é possível aceder a partilhas de ficheiros e pastas do Windows, para que os ficheiros têm de ser importados do armazenamento de Blobs do Azure:

- `DATASOURCE` é necessário em `BULK INSERT` comando ao importar ficheiros do armazenamento de Blobs do Azure. Ver [inserção em massa](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` é necessário em `OPENROWSET` funcionar quando ler um conteúdo de um ficheiro do armazenamento de Blobs do Azure. Ver [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR

Uma instância gerida não é possível aceder a partilhas de ficheiros e pastas do Windows, para que as seguintes restrições aplicam-se:

- Apenas `CREATE ASSEMBLY FROM BINARY` é suportada. Ver [CREATE ASSEMBLY de binário](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).  
- `CREATE ASSEMBLY FROM FILE` is't suportado. Ver [CREATE ASSEMBLY de ficheiro](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` não pode referenciar arquivos. Ver [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).


### <a name="dbcc"></a>DBCC

Declarações DBCC não documentadas que estão ativadas no SQL Server não são suportadas em instâncias geridas.

- `Trace Flags` não são suportados. Ver [sinalizadores de rastreio](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` Não é suportada. Ver [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` Não é suportada. Ver [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Transações distribuídas

Nenhuma das MSDTC nem [transações elásticas](sql-database-elastic-transactions-overview.md) atualmente são suportadas em instâncias geridas.

### <a name="extended-events"></a>Eventos Expandidos

Alguns destinos específicos do Windows para XEvents não são suportados:

- `etw_classic_sync target` Não é suportada. Store `.xel` armazenamento de BLOBs de ficheiros no Azure. Ver [etw_classic_sync destino](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- `event_file target` Não é suportada. Store `.xel` armazenamento de BLOBs de ficheiros no Azure. Ver [event_file destino](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Bibliotecas externas

Na base de dados R e Python bibliotecas externas ainda não são suportadas. Ver [serviços de aprendizagem de máquina do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream e Filetable

- dados FileStream não são suportados.
- Base de dados não pode conter grupos de ficheiros com `FILESTREAM` dados.
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

- Destinos suportados: SQL Server e base de dados SQL
- Não suportado destinos: arquivos, Analysis Services e outros RDBMS.

Operações

- Transações de escrita de instância para várias não são suportadas.
- `sp_dropserver` é suportada para remover um servidor ligado. See [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- `OPENROWSET` a função pode ser utilizada para executar consultas apenas em instâncias do SQL Server (qualquer um gerido, no local, ou em máquinas virtuais). Ver [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- `OPENDATASOURCE` a função pode ser utilizada para executar consultas apenas em instâncias do SQL Server (qualquer um gerido, no local, ou em máquinas virtuais). Apenas `SQLNCLI`, `SQLNCLI11`, e `SQLOLEDB` valores são suportados como o fornecedor. Por exemplo: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Ver [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="polybase"></a>Polybase

Tabelas externas que fazem referência os ficheiros no armazenamento do HDFS ou de Blobs do Azure não são suportadas. Para obter informações sobre o Polybase, veja [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replicação

A replicação está disponível para pré-visualização pública para a instância gerida. Para obter informações sobre a replicação, consulte [replicação do SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance).

### <a name="restore-statement"></a>RESTAURAR a instrução

- Sintaxe suportada
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Sintaxe não suportada
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Origem  
  - `FROM URL` (Armazenamento de Blobs do azure) é apenas suportada opção.
  - `FROM DISK`/`TAPE`/ dispositivo de cópia de segurança não é suportado.
  - Os conjuntos de cópia de segurança não são suportados.
- `WITH` as opções não são suportadas (não `DIFFERENTIAL`, `STATS`, etc.)
- `ASYNC RESTORE` -Restauro continua, mesmo que a quebra de ligação de cliente. Se sua conexão cair, pode verificar `sys.dm_operation_status` vista para o estado de uma operação de restauro (bem como para criar e LARGUE da base de dados). Ver [DM operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).  

As seguintes opções de base de dados estão a set/substituído e não não possível alterar mais tarde:  

- `NEW_BROKER` (se o mediador não está ativado no ficheiro. bak)  
- `ENABLE_BROKER` (se o mediador não está ativado no ficheiro. bak)  
- `AUTO_CLOSE=OFF` (se tiver uma base de dados no ficheiro. bak `AUTO_CLOSE=ON`)  
- `RECOVERY FULL` (se tiver uma base de dados no ficheiro. bak `SIMPLE` ou `BULK_LOGGED` modo de recuperação)
- Grupo de ficheiros com otimização de memória é adicionado e chamado XTP se não fosse no ficheiro. bak de origem  
- Qualquer grupo de ficheiros com otimização de memória foi mudado para XTP  
- `SINGLE_USER` e `RESTRICTED_USER` opções são convertidas em `MULTI_USER`

Limitações:  

- `.BAK` não não possível restaurar os ficheiros que contêm vários conjuntos de cópia de segurança.
- `.BAK` não não possível restaurar os ficheiros que contêm vários ficheiros de registo.
- Restauro falhará se contiver. bak `FILESTREAM` dados.
- Não não possível restaurar cópias de segurança que contêm bases de dados com objetos do Active Directory dentro da memória atualmente.  
- Não não possível restaurar cópias de segurança que contém as bases de dados em que, em algum momento objetos em memória atualmente existiam.
- Não não possível restaurar cópias de segurança que atualmente contém bases de dados no modo só de leitura. Esta limitação será removida em breve.

Para obter informações sobre instruções de restauro, veja [restaurar instruções](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Mediador de serviço

Instância de entre o service broker não é suportado:

- `sys.routes` -Pré-requisito: selecione o endereço de sys.routes. Endereço tem de ser LOCAL em cada rota. Ver [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE` -Não é possível usar `CREATE ROUTE` com `ADDRESS` diferente de `LOCAL`. Ver [criar rota](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE` Não é possível `ALTER ROUTE` com `ADDRESS` diferente de `LOCAL`. Ver [ALTER rota](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql).  

### <a name="stored-procedures-functions-triggers"></a>Procedimentos armazenados, funções, acionadores

- `NATIVE_COMPILATION` Não é suportada no escalão fins gerais.
- O seguinte procedimento [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) opções não são suportadas:
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` Não é suportada. Ver [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` Não é suportada. Ver [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` não são suportadas, incluindo `sp_addextendedproc`  e `sp_dropextendedproc`. Consulte [procedimentos armazenados expandidos](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)
- `sp_attach_db`, `sp_attach_single_file_db`, e `sp_detach_db` não são suportados. Ver [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql), e [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

## <a name="Changes"></a> Alterações de comportamento

As seguintes variáveis, funções e exibições devolvem resultados diferentes:

- `SERVERPROPERTY('EngineEdition')` Devolve a 8 de valor. Esta propriedade identifica exclusivamente uma instância gerida. Ver [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` Devolve nulo, porque o conceito de instância, pois existe para o SQL Server não se aplica para uma instância gerida. Ver [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Devolve nome DNS completo "conectável", por exemplo, meu instance.wcus17662feb9ce98.database.windows.net geridos. Ver [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` -Devolve completa de nome DNS "conectável", tais como `myinstance.domain.database.windows.net` para propriedades 'name' e 'data_source'. Consulte [SYS. SERVIDORES](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` Devolve nulo, porque o conceito de serviço, pois existe para o SQL Server não se aplica para uma instância gerida. Ver [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` é suportada. Devolve Nulo se o início de sessão do Azure AD não está a ser sys.syslogins. Ver [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql).  
- `SUSER_SID` Não é suportada. Devolve (problema conhecido temporário) de dados incorreto. Ver [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql).
- `GETDATE()` e outras funções de data/hora incorporado devolve sempre a hora no fuso horário UTC. Ver [GETDATE](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql).

## <a name="Issues"></a> Limitações e problemas conhecidos

### <a name="tempdb-size"></a>Tamanho TEMPDB

`tempdb` é dividido em 12 14 GB de tamanho de ficheiros com o máximo por ficheiro. Este tamanho máximo por ficheiro não pode ser alterado e novos ficheiros que podem ser adicionados a `tempdb`. Esta limitação será removida em breve. Algumas consultas podem devolver um erro se precisarem de mais de 168 GB no `tempdb`.

### <a name="exceeding-storage-space-with-small-database-files"></a>Que exceda o espaço de armazenamento com ficheiros de base de dados pequena

Cada instância gerida tem para o armazenamento de 35 TB reservado para o espaço em disco do Azure Premium e cada arquivo de banco de dados é colocado no disco físico separado. Tamanhos de disco podem ser 128 GB, 256 GB, 512 GB, 1 TB ou 4 TB. Não é cobrado o espaço não utilizado no disco, mas a soma total dos tamanhos de disco do Azure Premium não pode ter mais de 35 TB. Em alguns casos, uma instância gerida que não precisa de 8 TB no total pode ter mais de 35 TB Azure limite para o tamanho de armazenamento, devido à fragmentação interna.

Por exemplo, uma instância gerida pode ter um ficheiro 1,2 TB de tamanho que é colocado num disco de 4 TB e 248 ficheiros (cada 1 GB de tamanho), que são colocados em discos separados de 128 GB. Neste exemplo:

- O tamanho de armazenamento do disco alocado total é 1 x 4 TB + GB de 248 x 128 = 35 TB.
- total de espaço reservado para bases de dados na instância é 1 x 1.2 TB + GB de 248 1 = 1,4 TB.

Isso ilustra que, em determinadas circunstâncias, devido a uma distribuição específica de ficheiros, uma instância gerida atinja 35 TB reservado para o disco de Premium do Azure ligado quando pode esperar que ele não.

Neste exemplo, bancos de dados existentes continuarão a funcionar e cresça sem qualquer problema, desde que não são adicionados novos ficheiros. No entanto novas bases de dados não foi possível ser criados ou restaurados porque não existe espaço suficiente para novas unidades de disco, mesmo que o tamanho total de todas as bases de dados não atinja o limite de tamanho de instância. Nesse caso o erro devolvido não fica claro.

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>Restaurar a configuração incorreta da chave SAS durante a base de dados

`RESTORE DATABASE` ele lê o ficheiro. bak poderá ser constantemente Repetir para ler o ficheiro. bak e o erro devolvido após o longo período de tempo se assinatura de acesso partilhado no `CREDENTIAL` está incorreto. Execute RESTORE HEADERONLY antes de restaurar uma base de dados não se esqueça de que a chave SAS está correta.
Certifique-se de que remove o líder `?` da chave de SAS gerado com o portal do Azure.

### <a name="tooling"></a>Ferramentas

SQL Server Management Studio (SSMS) e o SQL Server Data Tools (SSDT) podem ter alguns problemas ao aceder a uma instância gerida.

- Utilizar principais de servidor do Azure AD (inícios de sessão) e os utilizadores (**pré-visualização pública**) com o SSDT não é atualmente suportada.
- Scripts para principais de servidor do Azure AD (inícios de sessão) e os utilizadores (**pré-visualização pública**) não são suportadas no SSMS.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Nomes de base de dados incorreto no algumas vistas, registos e as mensagens

Várias vistas de sistema, contadores de desempenho, mensagens de erro, XEvents e entradas de registo de erro ao apresentam identificadores de base de dados GUID, em vez dos nomes de banco de dados real. Não confie nestes identificadores GUID porque eles teriam de ser substituídos com nomes de banco de dados real no futuro.

### <a name="database-mail-profile"></a>Perfil de correio de base de dados

É possível que o perfil de correio de base de dados apenas uma e tem de ser chamado `AzureManagedInstance_dbmail_profile`.

### <a name="error-logs-are-not-persisted"></a>Registos de erros não são persistente

Registos de erros que estão disponíveis na instância gerida não são mantidos e seu tamanho não está incluído no limite de armazenamento máximo. Registos de erros podem ser apagados automaticamente em caso de ativação pós-falha.

### <a name="error-logs-are-verbose"></a>Registos de erros são detalhados

Uma instância gerida coloca informações verbosas em registos de erros e muitos deles não são relevantes. A quantidade de informações nos registos de erro é reduzida no futuro.

**Solução**: Utilize um procedimento personalizado para a leitura de registos de erros que algumas entradas não relevantes de filtro-out. Para obter detalhes, consulte [instância gerida – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Escopo da transação nas duas bases de dados dentro da instância do mesmo não é suportado

`TransactionScope` classe no .net não funciona se duas consultas são enviadas para duas bases de dados dentro da mesma instância sob o mesmo escopo de transação:

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

Embora esse código funciona com os dados dentro da instância do mesmo é necessária MSDTC.

**Solução**: Utilizar [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) para utilizar outro banco de dados no contexto de ligação em vez de usar duas conexões.

### <a name="clr-modules-and-linked-servers-sometime-cant-reference-local-ip-address"></a>Módulos CLR e algum tempo a servidores ligados não é possível referenciar o endereço IP local

Módulos CLR colocados numa instância gerida e consultas servidores ligados/distribuído que fazem referência a instância atual algum tempo não é possível resolver o IP da instância local. Este erro é um problema transitório.

**Solução**: Se for possível utilize ligações de contexto no módulo CLR.

### <a name="tde-encrypted-databases-dont-support-user-initiated-backups"></a>Bases de dados do TDE encriptado não suportam cópias de segurança iniciada pelo utilizador

Não é possível executar `BACKUP DATABASE ... WITH COPY_ONLY` numa base de dados que é encriptado com encriptação de dados transparente (TDE). TDE força seja encriptada com chaves TDE internas e não é possível exportar a chave, portanto, não será possível restaurar a cópia de segurança.

**Solução**: Utilize cópias de segurança automáticas e restauro de ponto no tempo, ou desativar a encriptação na base de dados.

## <a name="next-steps"></a>Passos Seguintes

- Para obter detalhes sobre as instâncias geridas, consulte [o que é uma instância gerida?](sql-database-managed-instance.md)
- Para funcionalidades e lista de comparação, veja [recursos comuns de SQL](sql-database-features.md).
- Para um início rápido mostra-lhe como criar uma nova instância gerida, veja [criar uma instância gerida](sql-database-managed-instance-get-started.md).
