---
title: Eventos na base de dados SQL expandidos | Documentos da Microsoft
description: Descreve os eventos expandidos (XEvents) na base de dados do Azure SQL, e como as sessões de eventos ligeiramente diferem das sessões de evento no Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 2dd3b9f0d1d8d61b2311977774c8b0f7267caa9e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090615"
---
# <a name="extended-events-in-sql-database"></a>Eventos expandidos na base de dados SQL
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Este tópico explica como a implementação de eventos expandidos na base de dados do Azure SQL é um pouco diferente em comparação com eventos expandidos no Microsoft SQL Server.

- SQL Database V12 ganhou a funcionalidade de eventos expandidos na segunda metade do calendário de 2015.
- SQL Server tenha tido eventos expandidos desde 2008.
- O conjunto de recursos de eventos expandidos na base de dados SQL é um subconjunto eficiente dos recursos no SQL Server.

*XEvents* é uma alcunha informal, às vezes, utilizado para "eventos expandidos" blogs e outros locais informais.

Informações adicionais sobre eventos expandidos, para a base de dados do Azure SQL e o Microsoft SQL Server, estão disponíveis em:

- [Início rápido: Eventos expandidos no SQL Server](https://msdn.microsoft.com/library/mt733217.aspx)
- [Extended Events (Eventos Expandidos)](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Pré-requisitos

Este tópico pressupõe que já tenha algum conhecimento de:

- [O serviço de base de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/).
- [Eventos expandidos](https://msdn.microsoft.com/library/bb630282.aspx) no Microsoft SQL Server.

- A maior parte da nossa documentação sobre eventos expandidos aplica-se para o SQL Server e base de dados SQL.

Exposição anterior para os seguintes itens é útil ao escolher o ficheiro de evento como a [destino](#AzureXEventsTargets):

- [Serviço de armazenamento do Azure](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Com o Azure PowerShell com armazenamento do Azure](../storage/common/storage-powershell-guide-full.md) -oferece informações abrangentes sobre o PowerShell e o serviço de armazenamento do Azure.

## <a name="code-samples"></a>Exemplos de código

Tópicos relacionados fornecem dois exemplos de código:


- [Código de destino de memória intermédia para eventos expandidos na base de dados SQL em anel](sql-database-xevent-code-ring-buffer.md)
    - Script de Transact-SQL simple curto.
    - Vamos enfatizar o tópico de exemplo de código que, quando tiver terminado com um destino de memória intermédia em anel, deve liberar seus próprios recursos, executando um alter largar `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` instrução. Mais tarde pode adicionar outra instância de memória intermédia em anel por `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Código de destino de ficheiro de evento para eventos expandidos na base de dados SQL](sql-database-xevent-code-event-file.md)
    - Fase 1 é o PowerShell para criar um contentor de armazenamento do Azure.
    - Fase 2 é Transact-SQL que usa o contêiner de armazenamento do Azure.

## <a name="transact-sql-differences"></a>Diferenças do Transact-SQL


- Quando executar o [CREATE EVENT SESSION](https://msdn.microsoft.com/library/bb677289.aspx) comando no SQL Server, utilize o **ON SERVER** cláusula. Mas, na base de dados SQL utiliza a **no banco de dados** cláusula em vez disso.


- O **no banco de dados** cláusula também se aplica ao [eventos ALTER sessão](https://msdn.microsoft.com/library/bb630368.aspx) e [DROP EVENT SESSION](https://msdn.microsoft.com/library/bb630257.aspx) comandos Transact-SQL.


- Uma prática recomendada é incluir a opção de sessão do evento de **STARTUP_STATE = ON** no seu **CREATE EVENT SESSION** ou **eventos ALTER sessão** instruções.
    - O **= ON** valor suporta um reinício automático após uma reconfiguração da base de dados lógico devido a uma ativação pós-falha.

## <a name="new-catalog-views"></a>Novas exibições de catálogo

A funcionalidade de eventos expandidos é suportada por várias [modos de exibição de catálogo](https://msdn.microsoft.com/library/ms174365.aspx). Exibições de catálogo falar sobre *metadados ou definições* de sessões de eventos criados pelo utilizador na base de dados atual. As vistas não devolver informações sobre as instâncias de sessões de evento do Active Directory.

| Nome de<br/>Vista de catálogo | Descrição |
|:--- |:--- |
| **sys.database_event_session_actions** |Devolve uma linha para cada ação em cada evento de uma sessão de eventos. |
| **sys.database_event_session_events** |Devolve uma linha para cada evento numa sessão de eventos. |
| **sys.database_event_session_fields** |Devolve uma linha para cada personalizar coluna que foi explicitamente definida em eventos e destinos. |
| **sys.database_event_session_targets** |Devolve uma linha para cada alvo de evento para uma sessão de eventos. |
| **sys.database_event_sessions** |Devolve uma linha para cada sessão de evento na base de dados de base de dados SQL. |

No Microsoft SQL Server, semelhante exibições de catálogo têm nomes que incluem *.server\_*  em vez de *.database\_*. O padrão de nome é como **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvshttpsmsdnmicrosoftcomlibraryms188754aspx"></a>Novas exibições de gerenciamento dinâmico [(DMVs)](https://msdn.microsoft.com/library/ms188754.aspx)

Base de dados SQL do Azure tem [vistas de gestão dinâmica (DMVs)](https://msdn.microsoft.com/library/bb677293.aspx) que suportam eventos expandidos. DMVs falar sobre *Active Directory* sessões de eventos.

| Nome da DMV | Descrição |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Devolve informações sobre as ações de sessão do evento. |
| **sys.dm_xe_database_session_events** |Devolve informações sobre eventos de sessão. |
| **sys.dm_xe_database_session_object_columns** |Mostra os valores de configuração para objetos que estão vinculados a uma sessão. |
| **sys.dm_xe_database_session_targets** |Devolve informações sobre os destinos de sessão. |
| **sys.dm_xe_database_sessions** |Devolve uma linha para cada sessão de evento que tem um âmbito para a base de dados atual. |

No Microsoft SQL Server, semelhante exibições de catálogo são nomeadas sem o  *\_base de dados* parte do nome, tais como:

- **sys.dm_xe_sessions**, em vez do nome<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>DMVs comuns para ambos
Para eventos expandidos há DMVs adicionais que são comuns a base de dados do Azure SQL e o Microsoft SQL Server:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

  <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Encontrar o disponíveis estendido de eventos, ações e destinos

Pode executar o SQL simple **SELECIONE** para obter uma lista dos eventos disponíveis, ações e destino.

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

## <a name="targets-for-your-sql-database-event-sessions"></a>Destinos de suas sessões de eventos da base de dados SQL

Seguem-se os destinos que podem capturar os resultados de suas sessões de evento na base de dados SQL:

- [Destino de memória intermédia de anel](https://msdn.microsoft.com/library/ff878182.aspx) -brevemente contém dados de eventos na memória.
- [Destino de contador do evento](https://msdn.microsoft.com/library/ff878025.aspx) -contagens de todos os eventos que ocorrem durante uma sessão de eventos expandidos.
- [Destino de ficheiro de evento](https://msdn.microsoft.com/library/ff878115.aspx) -escreve buffers completas para um contentor de armazenamento do Azure.

O [rastreio de eventos para Windows (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) API não está disponível para eventos expandidos na base de dados SQL.

## <a name="restrictions"></a>Restrições

Existem algumas diferenças relacionadas à segurança, assumindo o ambiente de nuvem de base de dados SQL:

- Eventos expandidos são fundou o modelo de isolamento de inquilino único. Uma sessão de eventos numa base de dados não é possível aceder a dados ou eventos de outra base de dados.
- Não é possível emitir uma **CREATE EVENT SESSION** instrução no contexto da **mestre** base de dados.

## <a name="permission-model"></a>Modelo de permissão

Tem de ter **controlo** permissão na base de dados para emitir uma **CREATE EVENT SESSION** instrução. O proprietário de base de dados (dbo) tem **controle** permissão.

### <a name="storage-container-authorizations"></a>Autorizações do contentor de armazenamento

O token SAS é gerar para o contentor de armazenamento do Azure tem de especificar **rwl** para as permissões. O **rwl** valor fornece as seguintes permissões:

- Leitura
- Escrita
- Lista

## <a name="performance-considerations"></a>Considerações de desempenho

Existem cenários onde a memória mais ativa que está em bom estado de todo o sistema pode se acumular uso intensivo de eventos expandidos. Por conseguinte o sistema de base de dados do Azure SQL dinamicamente define e ajusta limites sobre a quantidade de memória de Active Directory que pode ser acumulada por uma sessão de eventos. Muitos fatores passam ao cálculo dinâmico.

Se receber uma mensagem de erro que diz que uma máxima de memória tivesse sido imposta, algumas ações corretivas que pode realizar são:

- Execute menos sessões de eventos em simultâneo.
- Por meio de sua **CREATE** e **ALTER** instruções para sessões de evento, reduzir a quantidade de memória que especificar no **máx.\_memória** cláusula.

### <a name="network-latency"></a>Latência de rede

O **o ficheiro de evento** destino poderá deparar-se a latência de rede ou falhas ao persistir os dados para blobs de armazenamento do Azure. Outros eventos na base de dados SQL podem ser atrasados enquanto esperam que a comunicação de rede concluir. Este atraso pode abrandar a sua carga de trabalho.

- Para mitigar este risco de desempenho, evite a definição do **EVENT_RETENTION_MODE** a opção de **NO_EVENT_LOSS** nas suas definições de sessão de evento.

## <a name="related-links"></a>Ligações relacionadas

- [Utilizar o Azure PowerShell com o armazenamento do Azure](../storage/common/storage-powershell-guide-full.md).
- [Cmdlets de armazenamento do Azure](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Com o Azure PowerShell com armazenamento do Azure](../storage/common/storage-powershell-guide-full.md) -oferece informações abrangentes sobre o PowerShell e o serviço de armazenamento do Azure.
- [Como utilizar o armazenamento de Blobs do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [CREATE EVENT SESSION (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Mensagens do blogue de Jonathan Kehayias ' Acerca de eventos expandidos no Microsoft SQL Server](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- O Azure *atualizações de serviço* página da Web, reduzida pelo parâmetro para a base de dados do Azure SQL:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Outros tópicos de exemplo de código para eventos expandidos estão disponíveis nas seguintes ligações. No entanto, tem de verificar regularmente qualquer amostra para ver se o exemplo destina-se Microsoft SQL Server em comparação com a base de dados do Azure SQL. Em seguida, pode decidir se as alterações secundárias são necessários para executar o exemplo.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
