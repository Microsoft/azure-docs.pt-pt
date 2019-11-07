---
title: Eventos estendidos no banco de dados SQL
description: Descreve os eventos estendidos (XEvents) no banco de dados SQL do Azure e como as sessões de evento diferem ligeiramente das sessões de evento no Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: 64cfcd9451416a6eb35301268b285bd00cf0cad4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686773"
---
# <a name="extended-events-in-sql-database"></a>Eventos estendidos no banco de dados SQL
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Este tópico explica como a implementação de eventos estendidos no banco de dados SQL do Azure é um pouco diferente em comparação com eventos estendidos no Microsoft SQL Server.

- O banco de dados SQL V12 ganhou o recurso de eventos estendidos na segunda metade do calendário 2015.
- SQL Server teve eventos estendidos desde 2008.
- O conjunto de recursos de eventos estendidos no banco de dados SQL é um subconjunto robusto dos recursos no SQL Server.

*XEvents* é um apelido informal que às vezes é usado para ' eventos estendidos ' em Blogs e outros locais informais.

Informações adicionais sobre eventos estendidos, para o banco de dados SQL do Azure e o Microsoft SQL Server, estão disponíveis em:

- [Início Rápido: eventos estendidos em SQL Server](https://msdn.microsoft.com/library/mt733217.aspx)
- [Extended Events (Eventos Expandidos)](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Pré-requisitos

Este tópico pressupõe que você já tem algum conhecimento sobre:

- [Serviço de banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/).
- [Eventos estendidos](https://msdn.microsoft.com/library/bb630282.aspx) em Microsoft SQL Server.

- A grande parte de nossa documentação sobre eventos estendidos se aplica tanto ao SQL Server quanto ao banco de dados SQL.

A exposição anterior aos seguintes itens é útil ao escolher o arquivo de evento como o [destino](#AzureXEventsTargets):

- [Serviço de armazenamento do Azure](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Usando o Azure PowerShell com o armazenamento do Azure](../storage/common/storage-powershell-guide-full.md) – fornece informações abrangentes sobre o PowerShell e o serviço de armazenamento do Azure.

## <a name="code-samples"></a>Exemplos de código

Os tópicos relacionados fornecem dois exemplos de código:


- [Código de destino do buffer de anéis para eventos estendidos no banco de dados SQL](sql-database-xevent-code-ring-buffer.md)
    - Pequeno script Transact-SQL simples.
    - Enfatizamos o tópico de exemplo de código que, quando você conclui um destino de buffer de anel, você deve liberar seus recursos executando uma instrução ALTER-DROP `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;`. Posteriormente, você pode adicionar outra instância do buffer de anéis por `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Código de destino do arquivo de evento para eventos estendidos no banco de dados SQL](sql-database-xevent-code-event-file.md)
    - A fase 1 é o PowerShell para criar um contêiner de armazenamento do Azure.
    - A fase 2 é o Transact-SQL que usa o contêiner de armazenamento do Azure.

## <a name="transact-sql-differences"></a>Diferenças do Transact-SQL


- Ao executar o comando [criar sessão de evento](https://msdn.microsoft.com/library/bb677289.aspx) em SQL Server, use a cláusula **on Server** . Porém, no banco de dados SQL, use a cláusula **on Database** .


- A cláusula **on Database** também se aplica aos comandos TRANSACT-SQL [ALTER Event Session](https://msdn.microsoft.com/library/bb630368.aspx) e [drop Event Session](https://msdn.microsoft.com/library/bb630257.aspx) .


- Uma prática recomendada é incluir a opção de sessão de evento de **STARTUP_STATE = on** em suas instruções **Create Event Session** ou **ALTER Event Session** .
    - O valor **= on** dá suporte a uma reinicialização automática após uma reconfiguração do banco de dados lógico devido a um failover.

## <a name="new-catalog-views"></a>Novas exibições de catálogo

O recurso de eventos estendidos tem suporte em várias [exibições de catálogo](https://msdn.microsoft.com/library/ms174365.aspx). Exibições de catálogo informam sobre *metadados ou definições* de sessões de evento criadas pelo usuário no banco de dados atual. As exibições não retornam informações sobre instâncias de sessões de evento ativas.

| Nome de<br/>exibição do catálogo | Descrição |
|:--- |:--- |
| **sys. database_event_session_actions** |Retorna uma linha para cada ação em cada evento de uma sessão de evento. |
| **sys. database_event_session_events** |Retorna uma linha para cada evento em uma sessão de evento. |
| **sys. database_event_session_fields** |Retorna uma linha para cada coluna que pode ser personalizada definida explicitamente em eventos e destinos. |
| **sys. database_event_session_targets** |Retorna uma linha para cada destino de evento para uma sessão de evento. |
| **sys. database_event_sessions** |Retorna uma linha para cada sessão de evento no banco de dados do banco de dados SQL. |

No Microsoft SQL Server, exibições de catálogo semelhantes têm nomes que incluem *. Server\_* em vez de *.\_de banco de dados* . O padrão de nome é como **Sys. server_event_%** .

## <a name="new-dynamic-management-views-dmvshttpsmsdnmicrosoftcomlibraryms188754aspx"></a>Novas [DMVs (](https://msdn.microsoft.com/library/ms188754.aspx) exibições de gerenciamento dinâmico)

O banco de dados SQL do Azure tem [DMVs (exibições de gerenciamento dinâmico)](https://msdn.microsoft.com/library/bb677293.aspx) que dão suporte a eventos estendidos. DMVs informam sobre sessões de evento *ativas* .

| Nome do DMV | Descrição |
|:--- |:--- |
| **sys. dm_xe_database_session_event_actions** |Retorna informações sobre ações de sessão de evento. |
| **sys. dm_xe_database_session_events** |Retorna informações sobre eventos de sessão. |
| **sys. dm_xe_database_session_object_columns** |Mostra os valores de configuração para objetos associados a uma sessão. |
| **sys. dm_xe_database_session_targets** |Retorna informações sobre destinos de sessão. |
| **sys. dm_xe_database_sessions** |Retorna uma linha para cada sessão de evento que tem o escopo do banco de dados atual. |

No Microsoft SQL Server, exibições de catálogo semelhantes são nomeadas sem a parte *\_banco de dados* do nome, como:

- **Sys. dm_xe_sessions**, em vez de nome<br/>**Sys. dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>DMVs comuns a ambos
Para eventos estendidos, há DMVs adicionais que são comuns ao banco de dados SQL do Azure e Microsoft SQL Server:

- **sys. dm_xe_map_values**
- **sys. dm_xe_object_columns**
- **sys. dm_xe_objects**
- **sys. dm_xe_packages**

  <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Localizar os eventos estendidos, as ações e os destinos disponíveis

Você pode executar um SQL simples **Select** para obter uma lista de eventos, ações e destino disponíveis.

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

## <a name="targets-for-your-sql-database-event-sessions"></a>Destinos para suas sessões de evento do banco de dados SQL

Aqui estão os destinos que podem capturar resultados de suas sessões de evento no banco de dados SQL:

- [Destino do buffer de anéis](https://msdn.microsoft.com/library/ff878182.aspx) – mantém brevemente os dados de evento na memória.
- [Destino do contador de eventos](https://msdn.microsoft.com/library/ff878025.aspx) – conta todos os eventos que ocorrem durante uma sessão de eventos estendidos.
- [Destino do arquivo de evento](https://msdn.microsoft.com/library/ff878115.aspx) -grava buffers completos em um contêiner de armazenamento do Azure.

A API do [ETW (rastreamento de eventos para Windows)](https://msdn.microsoft.com/library/ms751538.aspx) não está disponível para eventos estendidos no banco de dados SQL.

## <a name="restrictions"></a>Restrições

Há algumas diferenças relacionadas à segurança que se ajustam ao ambiente de nuvem do banco de dados SQL:

- Os eventos estendidos são fundados no modelo de isolamento de locatário único. Uma sessão de evento em um banco de dados não pode acessar ou eventos de outro banco de dado.
- Você não pode emitir uma instrução **Create Event Session** no contexto do banco de dados **mestre** .

## <a name="permission-model"></a>Modelo de permissão

Você deve ter a permissão **Control** no banco de dados para emitir uma instrução **Create Event Session** . O proprietário do banco de dados (dbo) tem a permissão **Control** .

### <a name="storage-container-authorizations"></a>Autorizações do contêiner de armazenamento

O token SAS que você gera para o contêiner de armazenamento do Azure deve especificar **rwl** para as permissões. O valor **rwl** fornece as seguintes permissões:

- Leitura
- Escrita
- Lista

## <a name="performance-considerations"></a>Considerações de desempenho

Há cenários em que o uso intensivo de eventos estendidos pode acumular mais memória ativa do que o que está íntegro para o sistema geral. Portanto, o sistema do banco de dados SQL do Azure define e ajusta dinamicamente os limites na quantidade de memória ativa que pode ser acumulada por uma sessão de evento. Muitos fatores vão para o cálculo dinâmico.

Se você receber uma mensagem de erro informando que um máximo de memória foi imposto, algumas ações corretivas que você pode executar são:

- Execute menos sessões de evento simultâneas.
- Por meio das instruções **Create** e **ALTER** para sessões de evento, reduza a quantidade de memória especificada na cláusula **Max\_Memory** .

### <a name="network-latency"></a>Latência de rede

O destino do **arquivo de evento** pode experimentar latência de rede ou falhas ao persistir dados para BLOBs de armazenamento do Azure. Outros eventos no banco de dados SQL podem ser atrasados enquanto esperam a conclusão da comunicação de rede. Esse atraso pode reduzir a carga de trabalho.

- Para atenuar esse risco de desempenho, evite definir a opção de **EVENT_RETENTION_MODE** para **NO_EVENT_LOSS** em suas definições de sessão de evento.

## <a name="related-links"></a>Ligações relacionadas

- [Usando Azure PowerShell com o armazenamento do Azure](../storage/common/storage-powershell-guide-full.md).
- [Cmdlets do armazenamento do Azure](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Usando o Azure PowerShell com o armazenamento do Azure](../storage/common/storage-powershell-guide-full.md) – fornece informações abrangentes sobre o PowerShell e o serviço de armazenamento do Azure.
- [Como usar o armazenamento de BLOBs do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [CRIAR sessão de evento (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Postagens do blog Jonathan Kehayias sobre eventos estendidos no Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- A página da Web *atualizações de serviço* do Azure, limitada por parâmetro para o banco de dados SQL do Azure:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Outros tópicos de exemplo de código para eventos estendidos estão disponíveis nos links a seguir. No entanto, você deve verificar rotineiramente qualquer exemplo para ver se o exemplo tem como alvo Microsoft SQL Server versus o banco de dados SQL do Azure. Em seguida, você pode decidir se são necessárias pequenas alterações para executar o exemplo.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
