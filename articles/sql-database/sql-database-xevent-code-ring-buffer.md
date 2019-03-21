---
title: Código de memória intermédia em anel de XEvent para base de dados SQL | Documentos da Microsoft
description: Fornece um exemplo de código Transact-SQL que é feito de forma fácil e rápido através do destino de memória intermédia em anel, na base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: f47c6b63ce5f18aa896b086ec884b9a3020cadd2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993265"
---
# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Código de destino de memória intermédia para eventos expandidos na base de dados SQL em anel

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Pretende um exemplo de código completo para a forma mais fácil rápida para coletar e reportar informações para um evento expandido durante um teste. A meta mais fácil para os dados de evento expandido é o [destino de memória intermédia em anel](https://msdn.microsoft.com/library/ff878182.aspx).

Este tópico apresenta um exemplo de código Transact-SQL que:

1. Cria uma tabela com dados para demonstrar com.
2. Cria uma sessão para um evento expandido existente, ou seja **sqlserver.sql_statement_starting**.
   
   * O evento está limitado a instruções SQL que contêm uma determinada cadeia de caracteres de atualização: **instrução como "% ATUALIZAÇÃO tabEmployee %"**.
   * Escolher para enviar a saída do evento para o destino de tipo de memória intermédia em anel, ou seja **package0.ring_buffer**.
3. Inicia a sessão do evento.
4. Problemas de algumas instruções de ATUALIZAÇÃO SQL simples.
5. Problemas de uma instrução SQL SELECT para obter o resultado do evento da Buffer em anel.
   
   * **sys.dm_xe_database_session_targets** e estão associadas a outras vistas de gestão dinâmica (DMVs).
6. Parar a sessão do evento.
7. Remove o destino de memória intermédia em anel, liberar seus próprios recursos.
8. Ignora a sessão do evento e a tabela de demonstração.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Pode inscrever-se para obter uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Qualquer base de dados que pode criar uma tabela.
  
  * Opcionalmente, pode [crie uma **AdventureWorksLT** base de dados de demonstração](sql-database-get-started.md) em minutos.
* SQL Server Management Studio (ssms.exe), o ideal é que a última versão atualização mensal. 
  Pode baixar o ssms.exe mais recentes do:
  
  * Tópico intitulado [transferir o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  * [Uma ligação direta para o download.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Exemplo de código

Muito pequenas modificações, o seguinte exemplo de código de memória intermédia em anel pode ser executado na base de dados do Azure SQL ou Microsoft SQL Server. A diferença é a presença do nó base de dados"no nome de algumas vistas de gestão dinâmica (DMVs), usado na cláusula FROM no passo 5. Por exemplo:

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

## <a name="ring-buffer-contents"></a>Conteúdo da memória intermédia de anel

Nós usamos ssms.exe para executar o exemplo de código.

Para ver os resultados, clicar na célula sob o cabeçalho da coluna **target_data_XML**.

Em seguida, no painel de resultados clicar na célula sob o cabeçalho da coluna **target_data_XML**. Esse clique criado noutro separador do ficheiro ssms.exe em que o conteúdo da célula resultado fosse exibido, como XML.

O resultado é mostrado no bloco seguinte. Ele se parece muito, mas é apenas dois **<event>** elementos.

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


#### <a name="release-resources-held-by-your-ring-buffer"></a>Libertar recursos mantidos pela sua memória intermédia em anel

Quando tiver terminado com a memória intermédia em anel, pode removê-lo e liberar seus próprios recursos emitir uma **ALTER** a seguinte aparência:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


A definição da sua sessão do evento é atualizada, mas não removida. Mais tarde pode adicionar outra instância do Buffer em anel à sua sessão do evento:

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

O tópico principal para eventos expandidos na base de dados do Azure SQL é:

* [Expandido considerações de evento na base de dados SQL](sql-database-xevent-db-diff-from-svr.md), que contrasta alguns aspetos dos eventos expandidos que diferem entre a base de dados do Azure SQL em comparação com o Microsoft SQL Server.

Outros tópicos de exemplo de código para eventos expandidos estão disponíveis nas seguintes ligações. No entanto, tem de verificar regularmente qualquer amostra para ver se o exemplo destina-se Microsoft SQL Server em comparação com a base de dados do Azure SQL. Em seguida, pode decidir se as alterações secundárias são necessários para executar o exemplo.

* Exemplo de código para a base de dados do Azure SQL: [Código de destino de ficheiro de evento para eventos expandidos na base de dados SQL](sql-database-xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
