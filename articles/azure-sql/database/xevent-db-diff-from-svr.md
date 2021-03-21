---
title: Eventos expandidos
description: Descreve eventos estendidos (XEvents) na Base de Dados Azure SQL, e como as sessões de eventos diferem ligeiramente das sessões de eventos no Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 12/19/2018
ms.openlocfilehash: 139673e46421aa0dc19298697872fbff5fe587af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96501214"
---
# <a name="extended-events-in-azure-sql-database"></a>Eventos alargados na Base de Dados Azure SQL 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

O conjunto de eventos alargados na Base de Dados Azure SQL é um subconjunto robusto das funcionalidades no SQL Server e no Azure SQL Managed Instance.

*XEvents* é um apelido informal que às vezes é usado para "eventos estendidos" em blogs e outros locais informais.

Informações adicionais sobre eventos alargados estão disponíveis em:

- [Início Rápido: Eventos alargados no SQL Server](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
- [Extended Events (Eventos Expandidos)](/sql/relational-databases/extended-events/extended-events)

## <a name="prerequisites"></a>Pré-requisitos

Este tópico pressupõe que já tem algum conhecimento de:

- [Base de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/)
- [Eventos alargados](/sql/relational-databases/extended-events/extended-events)

- A maior parte da nossa documentação sobre eventos estendidos aplica-se ao SQL Server, Azure SQL Database e Azure SQL Managed Instance.

A exposição prévia aos seguintes itens é útil na escolha do Ficheiro de Eventos como [alvo:](#AzureXEventsTargets)

- [Serviço de Armazenamento Azure](https://azure.microsoft.com/services/storage/)

- [Azure PowerShell com armazenamento Azure](/powershell/module/az.storage/)

## <a name="code-samples"></a>Exemplos de código

Os tópicos relacionados fornecem duas amostras de código:

- [Código-alvo do tampão de anel para eventos prolongados na Base de Dados Azure SQL](xevent-code-ring-buffer.md)

  - Breve e simples script Transact-SQL.
  - Sublinhamos no tópico da amostra de código que, quando terminar com um alvo de tampão de anel, deve libertar os seus recursos executando uma declaração de `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` alter-drop. Mais tarde pode adicionar outro exemplo de Tampão de Anel por `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...` .

- [Código-alvo do Ficheiro de Eventos para eventos alargados na Base de Dados Azure SQL](xevent-code-event-file.md)

  - A fase 1 é powerShell para criar um recipiente de armazenamento Azure.
  - A fase 2 é a Transact-SQL que utiliza o recipiente de armazenamento Azure.

## <a name="transact-sql-differences"></a>Diferenças transact-SQL

- Quando executa o comando [CREATE EVENT SESSION](/sql/t-sql/statements/create-event-session-transact-sql) no SQL Server, utiliza a cláusula ON **SERVER.** Mas na Base de Dados Azure SQL utiliza a cláusula **ON DATABASE.**
- A cláusula **ON DATABASE** também se aplica aos comandos ALTER [EVENT SESSION](/sql/t-sql/statements/alter-event-session-transact-sql) e DROP [EVENT](/sql/t-sql/statements/drop-event-session-transact-sql) Transact-SQL.

- Uma boa prática é incluir a opção de sessão de eventos de **STARTUP_STATE = ON na** sua **Sessão DE EVENTO CREATE** ou em declarações DE **SESSÃO DE EVENTOS DE ALTERAÇÃO.**
  - O **valor = ON** suporta um reinício automático após uma reconfiguração da base de dados lógica devido a uma falha.

## <a name="new-catalog-views"></a>Novas vistas do catálogo

A funcionalidade de eventos alargados é suportada por várias [vistas de catálogo.](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) As vistas do catálogo dizem-lhe sobre *metadados ou definições* de sessões de eventos criadas pelo utilizador na base de dados atual. As opiniões não devolvem informações sobre casos de sessões de eventos ativos.

| Nome de<br/>vista de catálogo | Description |
|:--- |:--- |
| **sys.database_event_session_actions** |Retorna uma linha para cada ação em cada evento de uma sessão de eventos. |
| **sys.database_event_session_events** |Retorna uma linha para cada evento em uma sessão de evento. |
| **sys.database_event_session_fields** |Retorna uma linha para cada coluna personaliza-able que foi explicitamente definida em eventos e alvos. |
| **sys.database_event_session_targets** |Retorna uma linha para cada alvo de evento para uma sessão de evento. |
| **sys.database_event_sessions** |Retorna uma linha para cada sessão de eventos na base de dados. |

No Microsoft SQL Server, as vistas de catálogo semelhantes têm nomes que incluem *.server \_* em vez de *.database \_*. O padrão do nome é como **sys.server_event_%.**

## <a name="new-dynamic-management-views-dmvs"></a>Novas visões dinâmicas de gestão [(DMVs)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)

A Azure SQL Database tem [pontos de vista dinâmicos de gestão (DMVs)](/sql/relational-databases/system-dynamic-management-views/extended-events-dynamic-management-views) que suportam eventos alargados. Os DMVs falam-lhe sobre sessões *de eventos ativos.*

| Nome do DMV | Description |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Devolve informações sobre as ações da sessão de eventos. |
| **sys.dm_xe_database_session_events** |Devolve informações sobre eventos de sessão. |
| **sys.dm_xe_database_session_object_columns** |Mostra os valores de configuração para objetos que estão ligados a uma sessão. |
| **sys.dm_xe_database_session_targets** |Devolve informações sobre alvos de sessão. |
| **sys.dm_xe_database_sessions** |Retorna uma linha para cada sessão de evento que seja procurada na base de dados atual. |

No Microsoft SQL Server, as vistas de catálogo semelhantes são nomeadas sem a parte da base de *\_ dados* do nome, tais como:

- **sys.dm_xe_sessions**, em vez de nome<br/>**sys.dm_xe_database_sessions.**

### <a name="dmvs-common-to-both"></a>DMVs comuns a ambos

Para eventos prolongados existem DMVs adicionais que são comuns à Base de Dados Azure SQL, Azure SQL Managed Instance e Microsoft SQL Server:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

<a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Encontre os eventos, ações e metas estendidas disponíveis

Pode executar um SIMPLES SQL **SELECT** para obter uma lista dos eventos, ações e destino disponíveis.

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```

<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-azure-sql-database-event-sessions"></a>Alvos para as suas sessões de eventos Azure SQL Database

Aqui estão os alvos que podem capturar resultados das suas sessões de eventos na Base de Dados Azure SQL:

- [Alvo do tampão de](/previous-versions/sql/sql-server-2016/bb630339(v=sql.130)) anel - Contém brevemente dados de eventos na memória.
- [Alvo de Contador de Eventos](/previous-versions/sql/sql-server-2016/ff878025(v=sql.130)) - Conta todos os eventos que ocorrem durante uma sessão de eventos prolongados.
- [Alvo de ficheiro de evento](/previous-versions/sql/sql-server-2016/ff878115(v=sql.130)) - Escreve tampão completo para um recipiente de armazenamento Azure.

A API de [Rastreio de Eventos para Windows (ETW)](/dotnet/framework/wcf/samples/etw-tracing) não está disponível para eventos alargados na Base de Dados Azure SQL.

## <a name="restrictions"></a>Restrições

Existem algumas diferenças relacionadas com a segurança adequadas ao ambiente em nuvem da Base de Dados Azure SQL:

- Os eventos alargados são baseados no modelo de isolamento de um único inquilino. Uma sessão de eventos numa base de dados não pode aceder a dados ou eventos a partir de outra base de dados.
- Não é possível emitir uma declaração **DE SESSÃO DE EVENTOS CREATE** no contexto da base de dados **principal.**

## <a name="permission-model"></a>Modelo de permissão

Tem de ter permissão **de Controlo** na base de dados para emitir uma declaração **DE SESSÃO DE EVENTOS CREATE.** O proprietário da base de dados (dbo) tem permissão **de Controlo.**

### <a name="storage-container-authorizations"></a>Autorizações de contentores de armazenamento

O sinal SAS que gera para o seu recipiente de armazenamento Azure deve **especificar** as permissões. O **valor rwl** fornece as seguintes permissões:

- Leitura
- Escrita
- Lista

## <a name="performance-considerations"></a>Considerações de desempenho

Há cenários em que o uso intensivo de eventos prolongados pode acumular memória mais ativa do que é saudável para o sistema geral. Por isso, o Azure SQL Database define e ajusta os limites da quantidade de memória ativa que pode ser acumulada por uma sessão de evento. Muitos fatores entram no cálculo dinâmico.

Se receber uma mensagem de erro que diga que o máximo de memória foi aplicado, algumas ações corretivas que pode tomar são:

- Executar menos sessões de eventos simultâneos.
- Através das suas declarações **CREATE** e **ALTER** para sessões de eventos, reduza a quantidade de memória especificada na cláusula **MAX \_ MEMORY.**

### <a name="network-latency"></a>Latência de rede

O alvo **do Ficheiro de Eventos** pode experimentar latência ou falhas na rede enquanto persiste em dados para as bolhas de armazenamento Azure. Outros eventos na Base de Dados Azure SQL podem ser adiados enquanto aguardam que a comunicação da rede esteja concluída. Este atraso pode atrasar a sua carga de trabalho.

- Para mitigar este risco de desempenho, evite definir a opção **EVENT_RETENTION_MODE** para **NO_EVENT_LOSS** nas definições de sessão do evento.

## <a name="related-links"></a>Ligações relacionadas

- [Utilizando a Azure PowerShell com armazenamento Azure](/powershell/module/az.storage/).
- [Cmdlets de armazenamento Azure](/powershell/module/Azure.Storage)
- [Using Azure PowerShell with Azure Storage (Utilizar o Azure PowerShell com o Armazenamento do Azure)](/powershell/module/az.storage/)
- [Como utilizar o armazenamento blob a partir de .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [CREATE CREDENTIAL (Transact-SQL)](/sql/t-sql/statements/create-credential-transact-sql)
- [CREATE EVENT SESSION (Transact-SQL)](/sql/t-sql/statements/create-event-session-transact-sql)
- [Posts de blogue de Jonathan Kehayias sobre eventos alargados no Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)
- A página web Azure *Service Updates,* reduzida por parâmetro à Base de Dados Azure SQL:
  - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](/sql/relational-databases/extended-events/determine-which-queries-are-holding-locks)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](/sql/relational-databases/extended-events/find-the-objects-that-have-the-most-locks-taken-on-them)
-->