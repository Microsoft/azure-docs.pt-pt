---
title: Resolução das diferenças T-SQL-migração
description: Instruções de Transact-SQL que são menos do que totalmente suportadas na Base de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: e0870ac9dc818ca07e149421b486136c76dd61a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208824"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Resolução das diferenças Transact-SQL durante a migração para a Base de Dados SQL

Ao [migrar a sua base](sql-database-single-database-migrate.md) de dados do SQL Server para o Azure SQL Server, poderá descobrir que a sua base de dados necessita de alguma reengenharia antes de o Servidor SQL poder ser migrado. Este artigo fornece orientações para ajudá-lo na realização desta reengenharia e na compreensão das razões subjacentes à necessidade da reengenharia. Para detetar incompatibilidades, utilize o Assistente de Migração de [Dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Descrição geral

A maioria das funcionalidades Transact-SQL que as aplicações utilizam são totalmente suportadas tanto no Microsoft SQL Server como na Base de Dados Azure SQL. Por exemplo, os componentes SQL fundamentais, tais como tipos de dados, operadores, funções de cadeia, aritmética, lógica e cursor, funcionam de forma idêntica no Servidor SQL e na Base de Dados SQL. Existem, no entanto, algumas diferenças T-SQL nos elementos DDL (linguagem de definição de dados) e DML (linguagem de manipulação de dados) que resultam em declarações e consultas T-SQL que só são parcialmente suportadas (que discutimos mais tarde neste artigo).

Além disso, existem algumas funcionalidades e sintaxe que não são suportadas de todo porque a Base de Dados Azure SQL foi concebida para isolar funcionalidades das dependências da base de dados principal e do sistema operativo. Como tal, a maioria das atividades ao nível do servidor são inadequadas para a Base de Dados SQL. As declarações e opções T-SQL não estão disponíveis se configurarem opções de nível de servidor, componentes do sistema operativo ou especificarem a configuração do sistema de ficheiros. Quando tais capacidades são necessárias, uma alternativa adequada está muitas vezes disponível de outra forma a partir da Base de Dados SQL ou de outra funcionalidade ou serviço Azure.

Por exemplo, a alta disponibilidade é incorporada na Base de Dados Azure SQL utilizando tecnologia semelhante a [Grupos De Disponibilidade Sempre Em Disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). As declarações T-SQL relacionadas com grupos de disponibilidade não são suportadas pela Base de Dados SQL, e as opiniões dinâmicas de gestão relacionadas com os Grupos De Disponibilidade Sempre Não são suportadas.

Para obter uma lista das funcionalidades suportadas e não suportadas pela Base de Dados SQL, consulte a [comparação de funcionalidades azure SQL](sql-database-features.md)Database . A lista nesta página complementa que orienta e apresenta artigo, e centra-se nas declarações da Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Declarações de sintaxe Transact-SQL com diferenças parciais

As declarações core DDL (linguagem de definição de dados) estão disponíveis, mas algumas declarações de DDL têm extensões relacionadas com a colocação do disco e funcionalidades não suportadas.

- As declarações create e ALTER DATABASE têm mais de três dezenas de opções. As declarações incluem colocação de ficheiros, FILESTREAM e opções de corretor de serviços que se aplicam apenas ao SQL Server. Isto pode não importar se criar bases de dados antes de migrar, mas se estiver a migrar o código T-SQL que cria bases de dados deve comparar a CREATE DATABASE (Base de [Dados Azure SQL)](https://msdn.microsoft.com/library/dn268335.aspx) com a sintaxe do Servidor SQL na [CREATE DATABASE (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) para garantir que todas as opções que utiliza são suportadas. Create DATABASE for Azure SQL Database também tem opções objetivas e elásticas de serviço que se aplicam apenas à Base de Dados SQL.
- As declarações CREATE e ALTER TABLE têm opções de FileTable que não podem ser utilizadas na Base de Dados SQL porque filestream não é suportado.
- As declarações de login CREATE e ALTER são suportadas, mas a Base de Dados SQL não oferece todas as opções. Para tornar a sua base de dados mais portátil, a Base de Dados SQL encoraja a utilização de utilizadores de bases de dados contidos em vez de logins sempre que possível. Para mais informações, consulte [CREATE/ALTER LOGIN](https://docs.microsoft.com/sql/t-sql/statements/alter-login-transact-sql) e [Gerencie logins e utilizadores](sql-database-manage-logins.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Sintaxe Transact-SQL não suportada na Base de Dados Azure SQL

Para além das declarações da Transact-SQL relacionadas com as funcionalidades não suportadas descritas na comparação de funcionalidades de base de [dados Azure SQL](sql-database-features.md), as seguintes declarações e grupos de declarações não são suportadas. Como tal, se a sua base de dados a migrar estiver a utilizar qualquer uma das seguintes funcionalidades, re-eengineerifique o seu T-SQL para eliminar estas funcionalidades e declarações t-SQL.

- Agrupamento de objetos de sistema
- Relacionado sinuoso: Declarações de ponto final. A Base de Dados SQL não suporta a autenticação do Windows, mas suporta a autenticação do Azure Active Directory semelhante. Alguns tipos de autenticação precisam da versão mais recente do SSMS. Para mais informações, consulte [Connecting to SQL Database or SQL Data Warehouse By Using Azure Active Directory Authentication (Ligar à Base de Dados SQL ou SQL Data Warehouse, utilizando a Autenticação do Azure Active Directory)](sql-database-aad-authentication.md).
- Consultas entre bases de dados com três ou quatro nomes de partes. (As consultas entre bases de dados só de leitura são suportadas através da [consulta de base de dados elástica](sql-database-elastic-query-overview.md).)
- Encadeamento de propriedade entre bases de dados, `TRUSTWORTHY` definição
- `EXECUTE AS LOGIN`Em vez disso, utilize "EXECUTAR COMO UTILIZADOR".
- A encriptação é suportada, exceto para a gestão de chaves extensível
- Eventos: Eventos, notificações de eventos, notificações de consulta
- Colocação de ficheiros: Syntax relacionado com a colocação de ficheiros, tamanho e ficheiros de base de dados que são geridos automaticamente pelo Microsoft Azure.
- Elevada disponibilidade: Sintaxe relacionada com elevada disponibilidade, que é gerida através da sua conta Microsoft Azure. Isto inclui a sintaxe da cópia de segurança, restauro, Always On, espelhamento da base de dados, envio de registos, modos de recuperação.
- Leitor de registo: Sintaxe que depende do leitor de registos, que não está disponível na Base de Dados SQL: Replicação push, alteração da captura de dados. A Base de Dados SQL pode ser um subscritor de um artigo de replicação push.
- Funções: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardware: Syntax relacionado com definições de servidor relacionados com hardware: tais como memória, fios de trabalhador, afinidade cpu, trace flags. Utilize níveis de serviço e tamanhos de cálculo.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET` `OPENDATASOURCE`, e nomes de quatro partes
- .QUADRO .NET: Integração CLR com Servidor SQL
- Pesquisa semântica
- Credenciais do servidor: Utilize [as credenciais de base de dados.](https://msdn.microsoft.com/library/mt270260.aspx)
- Itens ao nível do servidor: Funções do servidor, `sys.login_token`. `GRANT`, `REVOKE`, e `DENY` das permissões ao nível do servidor não estão disponíveis, apesar de algumas serem substituídas por permissões ao nível da base de dados. Algumas DMVs ao nível do servidor útil têm DMVs equivalentes ao nível da base de dados.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` opções e `RECONFIGURE`. Algumas opções estão disponíveis com [ALTERAR BASE DE DADOS NO ÂMBITO DA CONFIGURAÇÃO](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent: Syntax que depende do Agente servidor SQL ou da base de dados MSDB: alertas, operadores, servidores de gestão central. Ao invés, utilize o scripting, como o Azure PowerShell.
- Auditoria do Servidor SQL: Utilize a auditoria da Base de Dados SQL.
- Rastreio do SQL Server
- Trace flags: Alguns vestígios de bandeira foram movidos para modos de compatibilidade.
- Depuração do Transact-SQL
- Acionadores: no âmbito do servidor ou acionadores de início de sessão
- `USE` instrução: para alterar o contexto da base de dados para outra base de dados, tem de efetuar uma nova ligação para a nova base de dados.

## <a name="full-transact-sql-reference"></a>Referência do Transact-SQL completa

Para obter mais informações sobre gramática, uso e exemplos transact-SQL, consulte [Transact-SQL Reference (Database Engine)](https://msdn.microsoft.com/library/bb510741.aspx) em Livros de Servidores SQL Online.

### <a name="about-the-applies-to-tags"></a>Sobre as etiquetas "Aplica-se a"

A referência Transact-SQL inclui artigos relacionados com as versões SQL Server 2008 até ao presente. Abaixo o título do artigo há uma barra de ícones, listando as quatro plataformas SQL Server, e indicando aplicabilidade. Por exemplo, os grupos de disponibilidade foram introduzidos no SQL Server 2012. O artigo do [CREATE AVAILABILITY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) indica que a declaração se aplica ao **SQL Server (a partir de 2012)**. A instrução não é aplicável ao SQL Server 2008, ao SQL Server 2008 R2, à Base de Dados SQL do Azure, ao Azure SQL Data Warehouse ou ao Parallel Data Warehouse.

Em alguns casos, o tema geral de um artigo pode ser usado num produto, mas existem pequenas diferenças entre os produtos. As diferenças são indicadas a meio ponto do artigo, conforme adequado. Em alguns casos, o tema geral de um artigo pode ser usado num produto, mas existem pequenas diferenças entre os produtos. As diferenças são indicadas a meio ponto do artigo, conforme adequado. Por exemplo, o artigo CREATE TRIGGER está disponível na Base de Dados SQL. Mas a opção **ALL SERVER** para os gatilhos do nível do servidor indica que os gatilhos de nível de servidor não podem ser usados na Base de Dados SQL. Utilize os gatilhos do nível da base de dados.

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista das funcionalidades suportadas e não suportadas pela Base de Dados SQL, consulte a [comparação de funcionalidades azure SQL](sql-database-features.md)Database . A lista nesta página complementa que orienta e apresenta artigo, e centra-se nas declarações da Transact-SQL.
