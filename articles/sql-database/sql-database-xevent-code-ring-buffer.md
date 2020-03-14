---
title: Código tampão do anel xevento
description: Fornece uma amostra de código Transact-SQL que é facilitada e rápida pela utilização do alvo tampão de anel, na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: ad98b61d6339388551af93671b3d4d892942f4e4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79213974"
---
# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Código-alvo do tampão de anel para eventos alargados na Base de Dados SQL

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Você quer uma amostra de código completa para a maneira rápida mais fácil de capturar e reportar informações para um evento prolongado durante um teste. O alvo mais fácil para dados de eventos estendidos é o [alvo do Tampão](https://msdn.microsoft.com/library/ff878182.aspx)de Anel .

Este tópico apresenta uma amostra de código Transact-SQL que:

1. Cria uma tabela com dados para demonstrar.
2. Cria uma sessão para um evento alargado existente, nomeadamente **sqlserver.sql_statement_starting**.
   
   * O evento limita-se a declarações SQL que contenham uma determinada cadeia de atualização: **declaração LIKE '%UPDATE tabEmployee%'** .
   * Opta por enviar a saída do evento para um alvo do tipo Ring Buffer, nomeadamente **pacote0.ring_buffer**.
3. Começa a sessão do evento.
4. Emite algumas simples declarações de ATUALIZAÇÃO SQL.
5. Emite uma declaração SQL SELECT para recuperar a saída do evento a partir do Tampão de Anel.
   
   * **sys.dm_xe_database_session_targets** e outras visões dinâmicas de gestão (DMVs) são unidas.
6. Interrompe a sessão do evento.
7. Larga o alvo do Tampão anelar, para libertar os seus recursos.
8. Deixa cair a sessão do evento e a mesa de demonstração.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Pode inscrever-se para obter uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Qualquer base de dados onde possa criar uma tabela.
  
  * Opcionalmente, pode criar uma base de dados de [demonstração **AdventureWorksLT** ](sql-database-get-started.md) em minutos.
* SQL Server Management Studio (ssms.exe), idealmente a sua mais recente versão mensal de atualização. 
  Você pode baixar os mais recentes ssms.exe de:
  
  * Tópico intitulado [Download SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  * [Um link direto para o download.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Exemplo de código

Com uma modificação muito pequena, a seguinte amostra de código tampão de anel pode ser executada na Base de Dados Azure SQL ou no Microsoft SQL Server. A diferença é a presença do nó '_database' em nome de algumas visões dinâmicas de gestão (DMVs), utilizadas na cláusula FROM no Passo 5. Por exemplo:

* sys.dm_xe<strong>_database</strong>_session_targets
* sys.dm_xe_session_targets

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

## <a name="ring-buffer-contents"></a>Conteúdo do Tampão de Anel

Usamos ssms.exe para executar a amostra de código.

Para ver os resultados, clicamos na célula sob o cabeçalho da coluna **target_data_XML**.

Em seguida, no painel de resultados, clicamos na célula sob o cabeçalho da coluna **target_data_XML**. Este clique criou outro separador de ficheiros em ssms.exe no qual o conteúdo da célula resultado foi apresentado, como XML.

A saída é mostrada no seguinte bloco. Parece longo, mas são apenas dois **\<evento>** elementos.

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


#### <a name="release-resources-held-by-your-ring-buffer"></a>Recursos de libertação detidos pelo seu Tampão anelar

Quando terminar com o seu Tampão de Anel, pode removê-lo e libertar os seus recursos emitindo um **ALTER** como o seguinte:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


A definição da sua sessão de eventos está atualizada, mas não abandonada. Mais tarde, pode adicionar outra instância do Tampão de Anel à sua sessão de eventos:

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

O tema principal para eventos alargados na Base de Dados Azure SQL é:

* [Considerações de eventos alargados na Base de Dados SQL,](sql-database-xevent-db-diff-from-svr.md)que contrasta alguns aspetos de eventos estendidos que diferem entre a Base de Dados Azure SQL e o Microsoft SQL Server.

Outros tópicos de amostra de código para eventos prolongados estão disponíveis nos seguintes links. No entanto, deve verificar rotineiramente qualquer amostra para ver se a amostra tem como alvo o Microsoft SQL Server versus Azure SQL Database. Em seguida, pode decidir se são necessárias pequenas alterações para executar a amostra.

* Amostra de código para Base de Dados SQL Azure: [Código-alvo do Ficheiro de Eventos para eventos alargados na Base de Dados SQL](sql-database-xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
