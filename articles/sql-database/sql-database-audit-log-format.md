---
title: Formato do Registo de Auditoria
description: Entenda como os registos de auditoria da Base de Dados SQL são estruturados.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 13746b86eed75055ceb5203afafb2d27a78ce1d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76722089"
---
# <a name="sql-database-audit-log-format"></a>Formato de registo de auditoria de base de dados SQL

A auditoria da Base de [Dados Azure SQL](sql-database-auditing.md) rastreia os eventos da base de dados e escreve-os num registo de auditoria na sua conta de armazenamento Azure, ou envia-os para o Event Hub ou Log Analytics para processamento e análise a jusante.

## <a name="naming-conventions"></a>Convenções de Nomeação

### <a name="blob-audit"></a>Auditoria Blob

Os registos de auditoria armazenados no armazenamento `sqldbauditlogs` blob são armazenados num recipiente nomeado na conta de Armazenamento Azure. A hierarquia do diretório dentro do `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`contentor é da forma . O formato de `<CreationTime>_<FileNumberInSession>.xel`nome `CreationTime` de ficheiro `hh_mm_ss_ms` Blob `FileNumberInSession` é , onde está em formato UTC, e é um índice de execução no caso de os registos de sessão se estenderem por vários ficheiros Blob.

Por exemplo, `Database1` para `Server1` base de dados no seguinte é um possível caminho válido:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

[Réplicas apenas de leitura](sql-database-read-scale-out.md) Os registos de auditoria são armazenados no mesmo recipiente. A hierarquia do diretório dentro do `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/`contentor é da forma . O nome de ficheiro Blob partilha o mesmo formato. Os Registos de Auditoria das Réplicas apenas de Leitura são armazenados no mesmo recipiente.


### <a name="event-hub"></a>Hub de Eventos

Os eventos de auditoria são escritos para o espaço de nome e centro de eventos que foi definido durante a configuração de auditoria, e são capturados no corpo de eventos [Apache Avro](https://avro.apache.org/) e armazenados usando formatação JSON com codificação UTF-8. Para ler os registos de auditoria, pode utilizar [ferramentas Avro](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) ou ferramentas semelhantes que processem este formato.

### <a name="log-analytics"></a>Log Analytics

Os eventos de auditoria são escritos para log `AzureDiagnostics` Analytics espaço `SQLSecurityAuditEvents`de trabalho definido durante a configuração de auditoria, para a tabela com a categoria . Para obter informações adicionais úteis sobre o idioma e comandos de pesquisa do Log Analytics, consulte a [referência de pesquisa do Log Analytics](../log-analytics/log-analytics-log-search.md).

## <a name="audit-log-fields"></a><a id="subheading-1"></a>Campos de Registo de Auditoria

| Nome (Blob) | Nome (Centros de Eventos/Log Analytics) | Descrição | Tipo blob | Hubs de eventos/log analytics tipo |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID da ação | varchar(4) | string |
| action_name | action_name_s | Nome da ação | N/D | string |
| additional_information | additional_information_s | Qualquer informação adicional sobre o evento, armazenada como XML | nvarchar (4000) | string |
| affected_rows | affected_rows_d | Número de linhas afetadas pela consulta | bigint | int |
| application_name | application_name_s| Nome da aplicação do cliente | nvarchar(128) | string |
| audit_schema_version | audit_schema_version_d | Sempre 1 | int | int |
| class_type | class_type_s | Tipo de entidade auditável em que a auditoria ocorre | varchar(2) | string |
| class_type_desc | class_type_description_s | Descrição da entidade auditável em que a auditoria ocorre | N/D | string |
| client_ip | client_ip_s | Fonte IP da aplicação do cliente | nvarchar(128) | string |
| connection_id | N/D | ID da ligação no servidor | GUID | N/D |
| data_sensitivity_information | data_sensitivity_information_s | Tipos de informação e etiquetas de sensibilidade devolvidas pela consulta auditada, com base nas colunas classificadas na base de dados. Saiba mais sobre os dados da [Base de Dados Azure SQL descobrir e classificar](sql-database-data-discovery-and-classification.md) | nvarchar (4000) | string |
| database_name | database_name_s | O contexto da base de dados em que a ação ocorreu | sysname | string |
| database_principal_id | database_principal_id_d | ID do contexto do utilizador da base de dados em que a ação é realizada em | int | int |
| database_principal_name | database_principal_name_s | Nome do contexto do utilizador da base de dados em que a ação é realizada | sysname | string |
| duration_milliseconds | duration_milliseconds_d | Duração da execução da consulta em milissegundos | bigint | int |
| event_time | event_time_t | Data e hora em que a ação auditável é disparada | datetime2 | datetime |
| host_name | N/D | Nome do anfitrião do cliente | string | N/D |
| is_column_permission | is_column_permission_s | Bandeira indicando se esta é uma permissão de nível de coluna. 1 = verdadeiro, 0 = falso | bit | string |
| N/D | is_server_level_audit_s | Bandeira indicando se esta auditoria está ao nível do servidor | N/D | string |
| object_ id | object_id_d | A identificação da entidade em que ocorreu a auditoria. Isto inclui os objetos do servidor, bases de dados, objetos de base de dados e objetos de esquema. 0 se a entidade for o servidor em si ou se a auditoria não for realizada a um nível de objeto | int | int |
| object_name | object_name_s | O nome da entidade em que ocorreu a auditoria. Isto inclui os objetos do servidor, bases de dados, objetos de base de dados e objetos de esquema. 0 se a entidade for o servidor em si ou se a auditoria não for realizada a um nível de objeto | sysname | string |
| permission_bitmask | permission_bitmask_s | Quando aplicável, mostra as permissões que foram concedidas, negadas ou revogadas | varbinary(16) | string |
| response_rows | response_rows_d | Número de linhas devolvidas no conjunto de resultados | bigint | int |
| schema_name | schema_name_s | O contexto do esquema em que ocorreu a ação. NULO para auditorias que ocorram fora de um esquema | sysname | string |
| N/D | securable_class_type_s | Objeto securável que mapeia para o class_type a ser auditado | N/D | string |
| sequence_group_id | sequence_group_id_g | Identificador exclusivo | varbinary | GUID |
| sequence_number | sequence_number_d | Rastreia a sequência de registos dentro de um único registo de auditoria que era demasiado grande para caber no tampão de escrita para auditorias | int | int |
| server_instance_name | server_instance_name_s | Nome da instância do servidor onde ocorreu a auditoria | sysname | string |
| server_principal_id | server_principal_id_d | ID do contexto de login em que a ação é realizada | int | int |
| server_principal_name | server_principal_name_s | Login atual | sysname | string |
| server_principal_sid | server_principal_sid_s | Sid de login atual | varbinary | string |
| session_id | session_id_d | ID da sessão em que ocorreu o evento | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Diretor do servidor para sessão | sysname | string |
| declaração | statement_s | Declaração T-SQL que foi executada (se houver) | nvarchar (4000) | string |
| com êxito | succeeded_s | Indica se a ação que desencadeou o evento foi bem sucedida. Para eventos que não sejam login e lote, isto apenas informa se a verificação de permissão foi bem sucedida ou falhou, e não a operação. 1 = sucesso, 0 = falhar | bit | string |
| target_database_principal_id | target_database_principal_id_d | A base de dados principal da operação GRANT/DENY/REVOKE é executada. 0 se não aplicável | int | int |
| target_database_principal_name | target_database_principal_name_s | Alvo de ação. NULO se não aplicável | string | string |
| target_server_principal_id | target_server_principal_id_d | Diretor do servidor em que a operação GRANT/DENY/REVOKE é executada. Devoluções 0 se não aplicável | int | int |
| target_server_principal_name | target_server_principal_name_s | Início de ação do alvo. NULO se não aplicável | sysname | string |
| target_server_principal_sid | target_server_principal_sid_s | SID do login do alvo. NULO se não aplicável | varbinary | string |
| transaction_id | transaction_id_d | SQL Server apenas (a partir de 2016) - 0 para Azure SQL DB | bigint | int |
| user_defined_event_id | user_defined_event_id_d | O id de evento definido pelo utilizador passou como um argumento para sp_audit_write. NULO para eventos do sistema (predefinido) e não zero para evento definido pelo utilizador. Para mais informações, consulte [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | A informação definida pelo utilizador passou como um argumento para sp_audit_write. NULO para eventos do sistema (predefinido) e não zero para evento definido pelo utilizador. Para mais informações, consulte [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar (4000) | string |

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a auditoria da Base de [Dados Azure SQL.](sql-database-auditing.md)
