---
title: Resolução das diferenças T-SQL-migração
description: Declarações Transact-SQL que são menos do que totalmente suportadas na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: 1e286b2329cb98d580bbf64071ff8767db304a00
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461880"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Resolução de diferenças transact-SQL durante a migração para a Base de Dados SQL

Ao [migrar a sua base](migrate-to-database-from-sql-server.md) de dados do SQL Server para a Base de Dados Azure SQL, poderá descobrir que a base de dados do SQL Server requer alguma reengenharia antes de poder ser migrada. Este artigo fornece orientação para ajudá-lo a realizar esta reengenharia e compreender as razões subjacentes pelas quais a reengenharia é necessária. Para detetar incompatibilidades, utilize o [Assistente de Migração de Dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Descrição geral

A maioria das funcionalidades Transact-SQL que as aplicações utilizam são totalmente suportadas tanto no Microsoft SQL Server como no Azure SQL Database. Por exemplo, os componentes SQL de núcleo, tais como tipos de dados, operadores, cordas, aritmética, lógica e funções de cursor funcionam de forma idêntica no SQL Server e na SQL Database. Existem, no entanto, algumas diferenças de T-SQL em elementos DDL (linguagem de definição de dados) e DML (linguagem de manipulação de dados) que resultam em declarações e consultas T-SQL que só são parcialmente suportadas (que discutimos mais tarde neste artigo).

Além disso, existem algumas funcionalidades e sintaxe que não são suportadas de todo porque a Base de Dados Azure SQL foi projetada para isolar funcionalidades das dependências da base de dados principal e do sistema operativo. Como tal, a maioria das atividades ao nível do servidor são inadequadas para a Base de Dados SQL. As declarações e opções T-SQL não estão disponíveis se configurarem opções de nível do servidor, componentes do sistema operativo ou especificarem a configuração do sistema de ficheiros. Quando tais capacidades são necessárias, uma alternativa apropriada está frequentemente disponível de outra forma a partir da Base de Dados SQL ou de outra funcionalidade ou serviço Azure.

Por exemplo, a alta disponibilidade é incorporada na Base de Dados Azure SQL utilizando tecnologia semelhante a [Always On Availability Groups](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). As declarações T-SQL relacionadas com grupos de disponibilidade não são suportadas pela Base de Dados SQL, e as opiniões dinâmicas de gestão relacionadas com os Grupos Always On Availability também não são suportadas.

Para obter uma lista das funcionalidades suportadas e não suportadas pela Base de Dados SQL, consulte [a comparação de funcionalidades da Base de Dados Azure SQL](features-comparison.md). A lista nesta página complementa as diretrizes e apresenta artigos, e foca-se nas declarações da Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Declarações de sintaxe Transact-SQL com diferenças parciais

As declarações de DDL (linguagem de definição de dados) estão disponíveis, mas algumas declarações de DDL têm extensões relacionadas com a colocação do disco e funcionalidades não apoiadas.

- As declarações CREATE e ALTER DATABASE têm mais de três dezenas de opções. As declarações incluem colocação de ficheiros, FILESTREAM e opções de corretor de serviço que se aplicam apenas ao SQL Server. Isto pode não importar se criar bases de dados antes de migrar, mas se estiver a migrar o código T-SQL que cria bases de dados deve comparar [a BASE DE DADOS CREATE (Base de Dados Azure SQL)](/sql/t-sql/statements/create-database-transact-sql) com a sintaxe do SQL Server na [CREATE DATABASE (SQL Server Transact-SQL)](/sql/t-sql/statements/create-database-transact-sql) para garantir que todas as opções que utiliza são suportadas. CREATE DATABASE for Azure SQL Database também tem opções de escala objetiva e elástica de serviço que se aplicam apenas à Base de Dados SQL.
- As declarações de TABELA CREATE e ALTER têm opções de Tabela de Ficheiros que não podem ser utilizadas na Base de Dados SQL porque o FILESTREAM não é suportado.
- As declarações de login CREATE e ALTER são suportadas, mas a SQL Database não oferece todas as opções. Para tornar a sua base de dados mais portátil, a SQL Database incentiva a utilização de utilizadores de bases de dados contidos em vez de logins sempre que possível. Para obter mais informações, consulte [CREATE/ALTER LOGIN](/sql/t-sql/statements/alter-login-transact-sql) e [Gerir logins e utilizadores.](logins-create-manage.md)

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Sintaxe Transact-SQL não suportada na Base de Dados Azure SQL

Para além das declarações transact-SQL relacionadas com as funcionalidades não apoiadas descritas na [comparação de funcionalidades da Base de Dados Azure SQL,](features-comparison.md)as seguintes declarações e grupos de declarações não são suportados. Como tal, se a sua base de dados a ser migrada estiver a utilizar qualquer uma das seguintes funcionalidades, re-engendrar o seu T-SQL para eliminar estas funcionalidades e declarações T-SQL.

- Agrupamento de objetos de sistema
- Ligação: Declarações de ponto final. A BASE de Dados SQL não suporta a autenticação do Windows, mas suporta a autenticação semelhante do Azure Ative Directory. Alguns tipos de autenticação precisam da versão mais recente do SSMS. Para obter mais informações, consulte [Connecting to SQL Database ou Azure Azure Synapse Analytics Utilizando a autenticação do Diretório Ativo Azure](authentication-aad-overview.md).
- Consultas entre bases de dados com três ou quatro nomes de partes. (As consultas entre bases de dados só de leitura são suportadas através da [consulta de base de dados elástica](elastic-query-overview.md).)
- Encadeamento de propriedade entre bases de dados, `TRUSTWORTHY` definição
- `EXECUTE AS LOGIN`Em vez disso, utilize "EXECUTAR COMO UTILIZADOR".
- A encriptação é suportada, exceto para a gestão de chaves extensível
- Eventos: Eventos, notificações de eventos, notificações de consulta
- Colocação de ficheiros: Sintaxe relacionada com a colocação, tamanho e ficheiros de base de dados de base de dados que são geridos automaticamente pelo Microsoft Azure.
- Alta disponibilidade: Sintaxe relacionada com alta disponibilidade, que é gerida através da sua conta Microsoft Azure. Isto inclui a sintaxe da cópia de segurança, restauro, Always On, espelhamento da base de dados, envio de registos, modos de recuperação.
- Leitor de registo: Sintaxe que se baseia no leitor de registos, que não está disponível na Base de Dados SQL: Replicação push, Change Data Capture. A Base de Dados SQL pode ser um subscritor de um artigo de replicação push.
- Funções: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardware: Sintaxe relacionada com configurações de servidor relacionadas com hardware: tais como memória, fios de trabalhador, afinidade do CPU, bandeiras de traços. Em vez disso, utilize os níveis de serviço e os tamanhos de cálculo.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET` `OPENDATASOURCE` e nomes de quatro partes
- .Net Framework: Integração CLR com SQL Server
- Pesquisa semântica
- Credenciais do servidor: Utilize [credenciais de âmbito de base de dados.](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
- Itens ao nível do servidor: funções do servidor, `sys.login_token` . `GRANT`, `REVOKE` e `DENY` as permissões de nível do servidor não estão disponíveis, embora algumas sejam substituídas por permissões de nível de base de dados. Algumas DMVs ao nível do servidor útil têm DMVs equivalentes ao nível da base de dados.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` opções e `RECONFIGURE`. Algumas opções estão disponíveis com [ALTERAR BASE DE DADOS NO ÂMBITO DA CONFIGURAÇÃO](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- Sql Server Agent: Sintaxe que se baseia no Agente do Servidor SQL ou na base de dados MSDB: alertas, operadores, servidores de gestão central. Ao invés, utilize o scripting, como o Azure PowerShell.
- Auditoria do SQL Server: Utilize a auditoria da BASE de Dados SQL.
- Rastreio do SQL Server
- Traços de bandeira: Alguns itens de bandeira foram movidos para modos de compatibilidade.
- Depuração do Transact-SQL
- Acionadores: no âmbito do servidor ou acionadores de início de sessão
- `USE` instrução: para alterar o contexto da base de dados para outra base de dados, tem de efetuar uma nova ligação para a nova base de dados.

## <a name="full-transact-sql-reference"></a>Referência do Transact-SQL completa

Para obter mais informações sobre a gramática, utilização e exemplos do Transact-SQL, consulte [Transact-SQL Reference (Database Engine) (Referência do Transact-SQL (Motor de Base de Dados))](/sql/t-sql/language-reference) na Documentação Online do Microsoft SQL Server.

### <a name="about-the-applies-to-tags"></a>Sobre as etiquetas "Aplica-se a"

A referência Transact-SQL inclui artigos relacionados com as versões SQL Server 2008 até ao presente. Abaixo do título do artigo há uma barra de ícones, listando as quatro plataformas SQL Server, e indicando a aplicabilidade. Por exemplo, os grupos de disponibilidade foram introduzidos no SQL Server 2012. O artigo [do GRUPO CREATE AVAILABILITY](/sql/t-sql/statements/create-availability-group-transact-sql) indica que a declaração se aplica ao **SQL Server (a partir de 2012)**. A declaração não se aplica ao SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure Azure Synapse Analytics ou Parallel Data Warehouse.

Em alguns casos, o objeto geral de um artigo pode ser utilizado num produto, mas existem pequenas diferenças entre os produtos. As diferenças são indicadas em pontos médios do artigo, conforme adequado. Em alguns casos, o objeto geral de um artigo pode ser utilizado num produto, mas existem pequenas diferenças entre os produtos. As diferenças são indicadas em pontos médios do artigo, conforme adequado. Por exemplo, o artigo CREATE TRIGGER está disponível na Base de Dados SQL. Mas a opção **ALL SERVER** para gatilhos de nível de servidor, indica que os gatilhos de nível do servidor não podem ser usados na Base de Dados SQL. Em vez disso, utilize gatilhos de nível de base.

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista das funcionalidades suportadas e não suportadas pela Base de Dados SQL, consulte [a comparação de funcionalidades da Base de Dados Azure SQL](features-comparison.md). A lista nesta página complementa as diretrizes e apresenta artigos, e foca-se nas declarações da Transact-SQL.