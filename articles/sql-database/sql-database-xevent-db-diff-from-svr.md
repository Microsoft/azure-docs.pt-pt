---
title: Eventos expandidos
description: Descreve eventos estendidos (XEvents) na Base de Dados SQL do Azure, e como as sessões de eventos diferem ligeiramente das sessões de eventos no Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: cb4eb4474ad074a3e69dc146c97b48d54343595b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213951"
---
# <a name="extended-events-in-sql-database"></a>Eventos alargados na Base de Dados SQL
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Este tópico explica como a implementação de eventos alargados na Base de Dados SQL do Azure é ligeiramente diferente em comparação com eventos estendidos no Microsoft SQL Server.

- A Base de Dados SQL V12 ganhou a funcionalidade de eventos alargados na segunda metade do calendário de 2015.
- O SQL Server tem eventos alargados desde 2008.
- O conjunto de funcionalidades de eventos estendidos na Base de Dados SQL é um subconjunto robusto das funcionalidades no Servidor SQL.

*XEvents* é um apelido informal que às vezes é usado para "eventos prolongados" em blogs e outros locais informais.

Informações adicionais sobre eventos alargados, para a Base de Dados Azure SQL e para o Microsoft SQL Server, estão disponíveis em:

- [Início Rápido: Eventos alargados no Servidor SQL](https://msdn.microsoft.com/library/mt733217.aspx)
- [Extended Events (Eventos Expandidos)](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Pré-requisitos

Este tópico assume que já tem algum conhecimento de:

- Serviço de [base de dados Azure SQL](https://azure.microsoft.com/services/sql-database/).
- [Eventos alargados](https://msdn.microsoft.com/library/bb630282.aspx) no Microsoft SQL Server.

- A maior parte da nossa documentação sobre eventos alargados aplica-se tanto ao SQL Server como à Base de Dados SQL.

A exposição prévia aos seguintes itens é útil na escolha do Ficheiro de Eventos como [alvo:](#AzureXEventsTargets)

- [Serviço de Armazenamento Azure](https://azure.microsoft.com/services/storage/)


- PowerShell
    - Utilizando o [Azure PowerShell com o Armazenamento Azure](../storage/common/storage-powershell-guide-full.md) - fornece informações completas sobre a PowerShell e o serviço de armazenamento Azure.

## <a name="code-samples"></a>Exemplos de código

Tópicos relacionados fornecem duas amostras de código:


- [Código-alvo do tampão de anel para eventos alargados na Base de Dados SQL](sql-database-xevent-code-ring-buffer.md)
    - Curto simples script Transact-SQL.
    - Salientamos no tópico da amostra de código que, quando terminar com um alvo do Tampão anel, deve libertar os seus recursos executando uma `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` declaração de alteração. Mais tarde pode adicionar outra `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`instância de Buffer de Anel por .


- [Código-alvo do Ficheiro de Eventos para eventos alargados na Base de Dados SQL](sql-database-xevent-code-event-file.md)
    - A fase 1 é a PowerShell para criar um recipiente de armazenamento Azure.
    - A fase 2 é transact-SQL que utiliza o recipiente de armazenamento Azure.

## <a name="transact-sql-differences"></a>Diferenças Transact-SQL


- Quando executar o comando [CREATE EVENT SESSION](https://msdn.microsoft.com/library/bb677289.aspx) no Servidor SQL, utilize a cláusula ON **SERVER.** Mas na Base de Dados SQL, utiliza a cláusula **ON DATABASE.**


- A cláusula **ON DATABASE** também se aplica aos comandos ALTER [EVENT SESSION](https://msdn.microsoft.com/library/bb630368.aspx) e DROP [EVENT SESSION](https://msdn.microsoft.com/library/bb630257.aspx) Transact-SQL.


- A melhor prática é incluir a opção de sessão de **eventos** de STARTUP_STATE = ON na sua sessão de **eventos create** ou **alter event session.**
    - O valor **= ON** suporta um reinício automático após uma reconfiguração da base de dados lógica devido a uma falha.

## <a name="new-catalog-views"></a>Novas vistas para o catálogo

A funcionalidade de eventos alargados é suportada por várias vistas de [catálogo.](https://msdn.microsoft.com/library/ms174365.aspx) As opiniões do catálogo dizem-lhe sobre *metadados ou definições* de sessões de eventos criadas pelo utilizador na base de dados atual. As opiniões não devolvem informações sobre casos de sessões de eventos ativos.

| Nome de<br/>vista de catálogo | Descrição |
|:--- |:--- |
| **sys.database_event_session_actions** |Devolve uma linha para cada ação em cada evento de uma sessão de eventos. |
| **sys.database_event_session_events** |Devolve uma fila para cada evento numa sessão de eventos. |
| **sys.database_event_session_fields** |Devolve uma linha para cada coluna personalizada que foi explicitamente definida em eventos e alvos. |
| **sys.database_event_session_targets** |Devolve uma linha para cada alvo do evento para uma sessão de evento. |
| **sys.database_event_sessions** |Devolve uma linha para cada sessão de eventos na base de dados da Base de Dados SQL. |

No Microsoft SQL Server, as vistas de catálogo semelhantes têm nomes que incluem *.server\_ * em vez de *.database\_*. O padrão de nome é como **sys.server_event_%.**

## <a name="new-dynamic-management-views-dmvs"></a>Novas visões dinâmicas de gestão [(DMVs)](https://msdn.microsoft.com/library/ms188754.aspx)

A Base de Dados Azure SQL tem pontos de vista dinâmicos de [gestão (DMVs)](https://msdn.microsoft.com/library/bb677293.aspx) que suportam eventos alargados. DMVs fala-lhe sobre sessões *ativas* de eventos.

| Nome de DMV | Descrição |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Devolve informações sobre ações de sessão de eventos. |
| **sys.dm_xe_database_session_events** |Devolve informações sobre eventos de sessão. |
| **sys.dm_xe_database_session_object_columns** |Mostra os valores de configuração de objetos que estão ligados a uma sessão. |
| **sys.dm_xe_database_session_targets** |Devolve informações sobre os alvos da sessão. |
| **sys.dm_xe_database_sessions** |Devolve uma linha para cada sessão de eventos que é remepto à base de dados atual. |

No Microsoft SQL Server, as vistas * \_* de catálogo semelhantes são nomeadas sem a parte de base de dados do nome, tais como:

- **sys.dm_xe_sessions,** em vez de nome<br/>**sys.dm_xe_database_sessions.**

### <a name="dmvs-common-to-both"></a>DMVs comuns a ambos
Para eventos prolongados existem DMVs adicionais que são comuns tanto à Base de Dados Azure SQL como ao Microsoft SQL Server:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

  <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Encontre os eventos, ações e alvos alargados disponíveis

Pode executar um Simples SQL **SELECT** para obter uma lista dos eventos, ações e alvo disponíveis.

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

## <a name="targets-for-your-sql-database-event-sessions"></a>Alvos para as sessões de eventos da Base de Dados SQL

Aqui estão alvos que podem capturar resultados das suas sessões de eventos na Base de Dados SQL:

- [Alvo do Tampão do Anel](https://msdn.microsoft.com/library/ff878182.aspx) - Detém brevemente os dados do evento na memória.
- [Alvo do Contador de Eventos](https://msdn.microsoft.com/library/ff878025.aspx) - Conta todos os eventos que ocorrem durante uma sessão de eventos prolongados.
- Alvo de [Arquivo de Eventos](https://msdn.microsoft.com/library/ff878115.aspx) - Escreve tampão completo para um recipiente de armazenamento Azure.

O [Evento Rastreio para Windows (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) API não está disponível para eventos alargados na Base de Dados SQL.

## <a name="restrictions"></a>Restrições

Existem algumas diferenças relacionadas com a segurança adequadas ao ambiente em nuvem da Base de Dados SQL:

- Eventos alargados são baseados no modelo de isolamento de um único inquilino. Uma sessão de eventos numa base de dados não pode aceder a dados ou eventos de outra base de dados.
- Não é possível emitir uma declaração da **CREATE EVENT SESSION** no contexto da base de dados **principal.**

## <a name="permission-model"></a>Modelo de permissão

Tem de ter permissão de **Controlo** na base de dados para emitir uma declaração da **CREATE EVENT SESSION.** O proprietário da base de dados (dbo) tem permissão de **Controlo.**

### <a name="storage-container-authorizations"></a>Autorizações de contentores de armazenamento

O símbolo SAS que gera para o seu recipiente de armazenamento Azure deve especificar **as** permissões. O valor **da rwl** fornece as seguintes permissões:

- Leitura
- Escrita
- Lista

## <a name="performance-considerations"></a>Considerações de desempenho

Há cenários em que o uso intensivo de eventos prolongados pode acumular memória mais ativa do que é saudável para o sistema geral. Por isso, o sistema de base de dados Azure SQL define e ajusta dinamicamente os limites na quantidade de memória ativa que pode ser acumulada por uma sessão de eventos. Muitos fatores entram no cálculo dinâmico.

Se receber uma mensagem de erro que diga que o máximo de memória foi aplicado, algumas ações corretivas que pode tomar são:

- Executar menos sessões de eventos simultâneos.
- Através das suas declarações **CREATE** e **ALTER** para sessões de eventos, reduza a quantidade de memória que especifica na cláusula **MEMÓRIA MAX.\_**

### <a name="network-latency"></a>Latência de rede

O alvo **do Ficheiro de Eventos** pode experimentar latência ou falhas da rede, enquanto persiste dados para blobs de armazenamento de Azure. Outros eventos na Base de Dados SQL podem ser adiados enquanto aguardam que a comunicação da rede esteja concluída. Este atraso pode atrasar a sua carga de trabalho.

- Para mitigar este risco de desempenho, evite definir a **opção EVENT_RETENTION_MODE** para **NO_EVENT_LOSS** nas definições da sessão de eventos.

## <a name="related-links"></a>Ligações relacionadas

- [Utilização da Azure PowerShell com armazenamento Azure.](../storage/common/storage-powershell-guide-full.md)
- [Cmdlets de Armazenamento Azure](https://docs.microsoft.com/powershell/module/Azure.Storage)
- Utilizando o [Azure PowerShell com o Armazenamento Azure](../storage/common/storage-powershell-guide-full.md) - fornece informações completas sobre a PowerShell e o serviço de armazenamento Azure.
- [Como utilizar o armazenamento Blob a partir de .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [CREATE EVENT SESSION (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Posts de blogue de Jonathan Kehayias sobre eventos alargados no Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- A página web Azure *Service Updates,* limitada por parâmetro à Base de Dados Azure SQL:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Outros tópicos de amostra de código para eventos prolongados estão disponíveis nos seguintes links. No entanto, deve verificar rotineiramente qualquer amostra para ver se a amostra tem como alvo o Microsoft SQL Server versus Azure SQL Database. Em seguida, pode decidir se são necessárias pequenas alterações para executar a amostra.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
