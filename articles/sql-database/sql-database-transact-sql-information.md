---
title: Resolvendo diferenças de T-SQL-migração-banco de dados SQL do Azure | Microsoft Docs
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
ms.openlocfilehash: 0f64642d04504770415c0d2243ec77b44bde05f2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566289"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Resolvendo diferenças de Transact-SQL durante a migração para o banco de dados SQL

Ao [migrar seu banco de dados](sql-database-single-database-migrate.md) do SQL Server para o SQL Server do Azure, você pode descobrir que seu banco de dados requer alguma reengenharia antes que a SQL Server possa ser migrada. Este artigo fornece orientação para ajudá-lo a executar essa reengenharia e entender os motivos subjacentes pelos quais a reengenharia é necessária. Para detectar incompatibilidades, use o [Assistente de migração de dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Descrição geral

A maioria dos recursos do Transact-SQL usados por aplicativos tem suporte total em Microsoft SQL Server e no banco de dados SQL do Azure. Por exemplo, os componentes principais do SQL, como tipos de dados, operadores, Cadeia de caracteres, aritmética, lógica e funções de cursor, funcionam de forma idêntica no SQL Server e no banco de dados SQL. No entanto, há algumas diferenças de T-SQL nos elementos DDL (linguagem de definição de dados) e DML (linguagem de manipulação de dados), resultando em instruções T-SQL e consultas que só têm suporte parcial (o que discutiremos mais adiante neste artigo).

Além disso, há alguns recursos e sintaxe que não têm suporte porque o banco de dados SQL do Azure foi projetado para isolar recursos de dependências no banco de dados mestre e no sistema operacional. Assim, a maioria das atividades de nível de servidor são inadequadas para o banco de dados SQL. As instruções e as opções do T-SQL não estarão disponíveis se configurarem opções no nível do servidor, componentes do sistema operacional ou especificarão a configuração do sistema de arquivos. Quando esses recursos são necessários, uma alternativa apropriada geralmente está disponível de alguma forma do banco de dados SQL ou de outro recurso ou serviço do Azure.

Por exemplo, a alta disponibilidade é incorporada ao banco de dados SQL do Azure usando uma tecnologia semelhante a [Always on grupos de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Não há suporte para instruções T-SQL relacionadas a grupos de disponibilidade no banco de dados SQL, e as exibições de gerenciamento dinâmico relacionadas a grupos de disponibilidade Always On também não têm suporte.

Para obter uma lista dos recursos com suporte e sem suporte pelo banco de dados SQL, consulte [comparação de recursos do banco de dados SQL do Azure](sql-database-features.md). A lista nesta página complementa o artigo diretrizes e recursos e se concentra nas instruções Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Instruções de sintaxe Transact-SQL com diferenças parciais

As instruções principais de DDL (linguagem de definição de dados) estão disponíveis, mas algumas instruções DDL têm extensões relacionadas ao posicionamento do disco e recursos sem suporte.

- As instruções CREATE e ALTER DATABASE têm mais de três dúzias de opções. As instruções incluem o posicionamento do arquivo, FILESTREAM e opções do Service Broker que se aplicam somente ao SQL Server. Isso pode não importar se você criar bancos de dados antes de migrar, mas se estiver migrando código T-SQL que cria bancos de dados, você deve comparar [CREATE DATABASE (banco de dados SQL do Azure)](https://msdn.microsoft.com/library/dn268335.aspx) com a sintaxe SQL Server em [create database (SQL Server Transact-SQL) ](https://msdn.microsoft.com/library/ms176061.aspx)para ter certeza de que todas as opções usadas têm suporte. CRIAR banco de dados para banco de dados SQL do Azure também tem objetivos de serviço e opções de escala elástica que se aplicam somente ao banco de dados SQL.
- As instruções CREATE e ALTER TABLE têm opções de Filetable que não podem ser usadas no banco de dados SQL porque não há suporte para FILESTREAM.
- Há suporte para instruções CREATE e ALTER login, mas o banco de dados SQL não oferece todas as opções. Para tornar seu banco de dados mais portátil, o banco de dados SQL incentiva o uso de usuários de banco de dados independente em vez de logons sempre que possível Para obter mais informações, consulte [criar/alterar logon](https://msdn.microsoft.com/library/ms189828.aspx) e [controlar e conceder acesso ao banco de dados](sql-database-manage-logins.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Sintaxe Transact-SQL sem suporte no banco de dados SQL do Azure

Além das instruções Transact-SQL relacionadas aos recursos sem suporte descritos na [comparação de recursos do banco de dados SQL do Azure](sql-database-features.md), não há suporte para as seguintes instruções e grupos de instruções. Dessa forma, se o banco de dados a ser migrado estiver usando qualquer um dos recursos a seguir, recrie o T-SQL para eliminar esses recursos e instruções do T-SQL.

- Agrupamento de objetos de sistema
- Conexão relacionada: Instruções de ponto de extremidade. A Base de Dados SQL não suporta a autenticação do Windows, mas suporta a autenticação do Azure Active Directory semelhante. Alguns tipos de autenticação precisam da versão mais recente do SSMS. Para mais informações, consulte [Connecting to SQL Database or SQL Data Warehouse By Using Azure Active Directory Authentication (Ligar à Base de Dados SQL ou SQL Data Warehouse, utilizando a Autenticação do Azure Active Directory)](sql-database-aad-authentication.md).
- Consultas entre bases de dados com três ou quatro nomes de partes. (As consultas entre bases de dados só de leitura são suportadas através da [consulta de base de dados elástica](sql-database-elastic-query-overview.md).)
- Encadeamento de propriedade entre bases de dados, `TRUSTWORTHY` definição
- `EXECUTE AS LOGIN`Em vez disso, utilize "EXECUTAR COMO UTILIZADOR".
- A encriptação é suportada, exceto para a gestão de chaves extensível
- Eventos Eventos, notificações de eventos, notificações de consulta
- Posicionamento do arquivo: Sintaxe relacionada com a colocação de ficheiros de base de dados, tamanho e ficheiros de base de dados que são geridas automaticamente pelo Microsoft Azure.
- Alta disponibilidade: Sintaxe relacionada com disponibilidade elevada, que é gerida através da sua conta do Microsoft Azure. Isto inclui a sintaxe da cópia de segurança, restauro, Always On, espelhamento da base de dados, envio de registos, modos de recuperação.
- Leitor de log: Sintaxe que depende do leitor de log, que não está disponível no banco de dados SQL: Replicação por push, captura de dados de alterações. A Base de Dados SQL pode ser um subscritor de um artigo de replicação push.
- Funções: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardware: Sintaxe relacionada a configurações de servidor relacionadas a hardware: como memória, threads de trabalho, afinidade de CPU, sinalizadores de rastreamento. Use camadas de serviço e tamanhos de computação em vez disso.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`,e nomes de quatro partes `OPENDATASOURCE`
- .NET Framework: Integração CLR com o SQL Server
- Pesquisa semântica
- Credenciais do servidor: Em vez disso, use [as credenciais no escopo do banco de dados](https://msdn.microsoft.com/library/mt270260.aspx) .
- Itens de nível de servidor: Funções de servidor `sys.login_token`,. `GRANT`, `REVOKE`, e `DENY` das permissões ao nível do servidor não estão disponíveis, apesar de algumas serem substituídas por permissões ao nível da base de dados. Algumas DMVs ao nível do servidor útil têm DMVs equivalentes ao nível da base de dados.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` opções e `RECONFIGURE`. Algumas opções estão disponíveis com [ALTERAR BASE DE DADOS NO ÂMBITO DA CONFIGURAÇÃO](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent: Sintaxe que se baseia no Agente do SQL Server ou na base de dados do MSDB: alertas, operadores, servidores de gestão central. Ao invés, utilize o scripting, como o Azure PowerShell.
- Auditoria de SQL Server: Ao invés, utilize a auditoria da Base de Dados SQL.
- Rastreio do SQL Server
- Sinalizadores de rastreamento: Alguns itens do sinalizador de rastreio foram movidos para modos de compatibilidade.
- Depuração do Transact-SQL
- Gatilhos Gatilhos no escopo do servidor ou no logon
- `USE`privacidade Para alterar o contexto do banco de dados para um banco de dados diferente, você deve fazer uma nova conexão com o novo banco de dados.

## <a name="full-transact-sql-reference"></a>Referência do Transact-SQL completa

Para obter mais informações sobre a gramática, o uso e os exemplos do Transact-SQL, consulte [referência de Transact-SQL (mecanismo de banco de dados)](https://msdn.microsoft.com/library/bb510741.aspx) em manuais online do SQL Server.

### <a name="about-the-applies-to-tags"></a>Sobre as etiquetas "Aplica-se a"

A referência do Transact-SQL inclui artigos relacionados a SQL Server versões 2008 para o presente. Abaixo do título do artigo há uma barra de ícones, listando as quatro plataformas SQL Server e indicando aplicabilidade. Por exemplo, os grupos de disponibilidade foram introduzidos no SQL Server 2012. O artigo [Criar grupo](https://msdn.microsoft.com/library/ff878399.aspx) de disponibilidade indica que a instrução se aplica a **SQL Server (começando com 2012)** . A instrução não é aplicável ao SQL Server 2008, ao SQL Server 2008 R2, à Base de Dados SQL do Azure, ao Azure SQL Data Warehouse ou ao Parallel Data Warehouse.

Em alguns casos, o assunto geral de um artigo pode ser usado em um produto, mas há pequenas diferenças entre os produtos. As diferenças são indicadas em pontos médios no artigo, conforme apropriado. Em alguns casos, o assunto geral de um artigo pode ser usado em um produto, mas há pequenas diferenças entre os produtos. As diferenças são indicadas em pontos médios no artigo, conforme apropriado. Por exemplo, o artigo criar gatilho está disponível no banco de dados SQL. Mas a opção **All Server** para gatilhos no nível do servidor indica que os gatilhos no nível do servidor não podem ser usados no banco de dados SQL. Em vez disso, use gatilhos de nível de banco de dados.

## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista dos recursos com suporte e sem suporte pelo banco de dados SQL, consulte [comparação de recursos do banco de dados SQL do Azure](sql-database-features.md). A lista nesta página complementa o artigo diretrizes e recursos e se concentra nas instruções Transact-SQL.
