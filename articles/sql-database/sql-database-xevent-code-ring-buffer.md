---
title: Código do buffer de anéis do XEvent
description: Fornece um exemplo de código Transact-SQL que é facilitado e rápido pelo uso do destino do buffer de anéis, no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: 7adffac045ddb2ba369993b1b805e3ce2304fb38
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822319"
---
# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Código de destino do buffer de anéis para eventos estendidos no banco de dados SQL

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Você deseja um exemplo de código completo para a maneira mais fácil de capturar e relatar informações para um evento estendido durante um teste. O destino mais fácil para dados de eventos estendidos é o [destino do buffer de anéis](https://msdn.microsoft.com/library/ff878182.aspx).

Este tópico apresenta um exemplo de código Transact-SQL que:

1. Cria uma tabela com dados para demonstrar com.
2. Cria uma sessão para um evento estendido existente, ou seja, **SqlServer. sql_statement_starting**.
   
   * O evento é limitado a instruções SQL que contêm uma cadeia de atualização específica: **instrução como '% Update tabEmployee% '** .
   * Opta por enviar a saída do evento para um destino do tipo buffer de anel, a saber **package0. ring_buffer**.
3. Inicia a sessão de evento.
4. Emite algumas instruções simples de atualização do SQL.
5. Emite uma instrução SQL SELECT para recuperar a saída do evento do buffer de anéis.
   
   * **Sys. dm_xe_database_session_targets** e outras DMVs (exibições de gerenciamento dinâmico) são unidas.
6. Interrompe a sessão de evento.
7. Descarta o destino do buffer de anéis para liberar seus recursos.
8. Descarta a sessão de evento e a tabela de demonstração.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Pode inscrever-se para obter uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Qualquer banco de dados no qual você possa criar uma tabela.
  
  * Opcionalmente, você pode [criar um banco de dados de demonstração do **AdventureWorksLT** ](sql-database-get-started.md) em minutos.
* SQL Server Management Studio (SSMS. exe), idealmente sua última versão de atualização mensal. 
  Você pode baixar o SSMS. exe mais recente de:
  
  * Tópico intitulado [Download SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  * [Um link direto para o download.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Exemplo de código

Com uma modificação muito pequena, o exemplo de código de buffer de anéis a seguir pode ser executado no banco de dados SQL do Azure ou Microsoft SQL Server. A diferença é a presença do nó ' _database ' no nome de algumas DMVs (exibições de gerenciamento dinâmico), usadas na cláusula FROM na etapa 5. Por exemplo:

* <strong>_database</strong>sys. dm_xe _session_targets
* sys. dm_xe_session_targets

&nbsp;

```sql
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```


&nbsp;

## <a name="ring-buffer-contents"></a>Conteúdo do buffer de anéis

Usamos o SSMS. exe para executar o exemplo de código.

Para exibir os resultados, clicamos na célula sob o cabeçalho da coluna **target_data_XML**.

Em seguida, no painel de resultados, clicamos na célula sob o cabeçalho da coluna **target_data_XML**. Esse clique criou outra guia de arquivo no SSMS. exe, na qual o conteúdo da célula de resultado foi exibido, como XML.

A saída é mostrada no bloco a seguir. Parece longo, mas é apenas duas\<elementos de **> de evento** .

&nbsp;

```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```


#### <a name="release-resources-held-by-your-ring-buffer"></a>Liberar recursos mantidos pelo buffer de anéis

Quando terminar o buffer de anéis, você poderá removê-lo e liberar seus recursos que emitem uma **alteração** semelhante à seguinte:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


A definição da sessão de evento é atualizada, mas não descartada. Posteriormente, você pode adicionar outra instância do buffer de anéis à sua sessão de evento:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```


## <a name="more-information"></a>Mais informações

O tópico primário para eventos estendidos no banco de dados SQL do Azure é:

* [Considerações sobre eventos estendidos no banco de dados SQL](sql-database-xevent-db-diff-from-svr.md), que contrasta alguns aspectos dos eventos estendidos que diferem entre o banco de dados SQL do Azure versus Microsoft SQL Server.

Outros tópicos de exemplo de código para eventos estendidos estão disponíveis nos links a seguir. No entanto, você deve verificar rotineiramente qualquer exemplo para ver se o exemplo tem como alvo Microsoft SQL Server versus o banco de dados SQL do Azure. Em seguida, você pode decidir se são necessárias pequenas alterações para executar o exemplo.

* Exemplo de código para banco de dados SQL do Azure: [código de destino do arquivo de evento para eventos estendidos no banco de dados SQL](sql-database-xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
