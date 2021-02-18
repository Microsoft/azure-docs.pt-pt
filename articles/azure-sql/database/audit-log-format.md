---
title: Formato de registo de auditoria de base de dados SQL
description: Compreenda como os registos de auditoria da Base de Dados Azure SQL são estruturados.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: reference
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 06/03/2020
ms.openlocfilehash: f5c176db4f679c79bb42c6ceb46b3588e9440874
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572216"
---
# <a name="sql-database-audit-log-format"></a>Formato de registo de auditoria de base de dados SQL

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[A Azure SQL Database auditando](auditing-overview.md) rastreia eventos de base de dados e escreve-os para um registo de auditoria na sua conta de armazenamento Azure, ou envia-os para o Event Hub ou Log Analytics para processamento e análise a jusante.

## <a name="naming-conventions"></a>Convenções de nomenclatura

### <a name="blob-audit"></a>Auditoria blob

Os registos de auditoria armazenados no armazém da Azure Blob são armazenados num contentor nomeado `sqldbauditlogs` na conta de armazenamento Azure. A hierarquia do diretório dentro do contentor é do formulário `<ServerName>/<DatabaseName>/<AuditName>/<Date>/` . O formato de nome de ficheiro Blob é `<CreationTime>_<FileNumberInSession>.xel` , onde está no formato `CreationTime` `hh_mm_ss_ms` UTC, e é um índice de `FileNumberInSession` execução no caso de os registos de sessão se estenderem por vários ficheiros Blob.

Por exemplo, para a base de dados `Database1` sobre o seguinte é um caminho válido `Server1` possível:

`Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel`

[Os registos de auditoria de réplicas apenas](read-scale-out.md) de leitura são armazenados no mesmo recipiente. A hierarquia do diretório dentro do contentor é do formulário `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/` . O nome do ficheiro Blob partilha o mesmo formato. Os Registos de Auditoria de Réplicas só de Leitura são armazenados no mesmo recipiente.


### <a name="event-hub"></a>Hub de Eventos

Os eventos de auditoria são escritos para o namespace e centro de eventos que foi definido durante a configuração de auditoria, e são capturados no corpo de eventos [Apache Avro](https://avro.apache.org/) e armazenados usando formatação JSON com codificação UTF-8. Para ler os registos de auditoria, pode utilizar [Ferramentas Avro](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) ou ferramentas semelhantes que processam este formato.

### <a name="log-analytics"></a>Log Analytics

Os eventos de auditoria são escritos para log analytics espaço de trabalho definido durante a configuração de auditoria, para a `AzureDiagnostics` tabela com a categoria `SQLSecurityAuditEvents` . Para obter informações úteis adicionais sobre o idioma e comandos de pesquisa do Log Analytics, consulte [a referência de pesquisa do Log Analytics](../../azure-monitor/logs/log-query-overview.md).

## <a name="audit-log-fields"></a><a id="subheading-1"></a>Campos de registo de auditoria

| Nome (blob) | Nome (Centros de Eventos/Análise de Registo) | Description | Tipo de blob | Hubs de eventos/tipo de Analítica de Log |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID da ação | varchar(4) | string |
| action_name | action_name_s | Nome da ação | N/D | string |
| additional_information | additional_information_s | Qualquer informação adicional sobre o evento, armazenada como XML | nvarchar(4000) | string |
| affected_rows | affected_rows_d | Número de linhas afetadas pela consulta | bigint | int |
| application_name | application_name_s| Nome da aplicação do cliente | nvarchar(128) | string |
| audit_schema_version | audit_schema_version_d | Sempre 1 | int | int |
| class_type | class_type_s | Tipo de entidade auditável em que a auditoria ocorre | varchar(2) | string |
| class_type_desc | class_type_description_s | Descrição da entidade auditável em que a auditoria ocorre | N/D | string |
| client_ip | client_ip_s | IP de origem da aplicação do cliente | nvarchar(128) | string |
| connection_id | N/D | ID da ligação no servidor | GUID | N/D |
| data_sensitivity_information | data_sensitivity_information_s | Tipos de informação e etiquetas de sensibilidade devolvidas pela consulta auditada, com base nas colunas classificadas na base de dados. Saiba mais sobre [os dados da Base de Dados Azure SQL Descubra e classificatione](data-discovery-and-classification-overview.md) | nvarchar(4000) | string |
| database_name | database_name_s | O contexto da base de dados em que a ação ocorreu | sysname | string |
| database_principal_id | database_principal_id_d | ID do contexto do utilizador da base de dados em que a ação é realizada em | int | int |
| database_principal_name | database_principal_name_s | Nome do contexto do utilizador da base de dados em que a ação é realizada | sysname | string |
| duration_milliseconds | duration_milliseconds_d | Duração da execução de consultas em milissegundos | bigint | int |
| event_time | event_time_t | Data e hora em que a ação auditável é disparada | datetime2 | datetime |
| host_name | N/D | Nome do anfitrião do cliente | string | N/D |
| is_column_permission | is_column_permission_s | Sinalização indicando se esta é uma permissão de nível de coluna. 1 = verdadeiro, 0 = falso | bit | string |
| N/D | is_server_level_audit_s | Bandeira indicando se esta auditoria está ao nível do servidor | N/D | string |
| object_ id | object_id_d | A identificação da entidade em que ocorreu a auditoria. Isto inclui: objetos de servidor, bases de dados, objetos de base de dados e objetos de esquema. 0 se a entidade for o próprio servidor ou se a auditoria não for realizada a nível de objetos | int | int |
| object_name | object_name_s | O nome da entidade em que ocorreu a auditoria. Isto inclui: objetos de servidor, bases de dados, objetos de base de dados e objetos de esquema. 0 se a entidade for o próprio servidor ou se a auditoria não for realizada a nível de objetos | sysname | string |
| permission_bitmask | permission_bitmask_s | Quando aplicável, mostra as permissões que foram concedidas, negadas ou revogadas | varbinário(16) | string |
| response_rows | response_rows_d | Número de linhas devolvidas no conjunto de resultados | bigint | int |
| schema_name | schema_name_s | O contexto do esquema em que a ação ocorreu. NULO para auditorias que ocorram fora de um esquema | sysname | string |
| N/D | securable_class_type_s | Objeto securável que mapeia para o class_type a ser auditado | N/D | string |
| sequence_group_id | sequence_group_id_g | Identificador exclusivo | varbinário | GUID |
| sequence_number | sequence_number_d | Rastreia a sequência de registos dentro de um único registo de auditoria que era demasiado grande para caber no tampão de escrita para auditorias | int | int |
| server_instance_name | server_instance_name_s | Nome da instância do servidor onde ocorreu a auditoria | sysname | string |
| server_principal_id | server_principal_id_d | ID do contexto de login em que a ação é realizada | int | int |
| server_principal_name | server_principal_name_s | Login atual | sysname | string |
| server_principal_sid | server_principal_sid_s | SID de login atual | varbinário | string |
| session_id | session_id_d | ID da sessão em que ocorreu o evento | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Diretor do servidor para sessão | sysname | string |
| declaração | statement_s | Declaração T-SQL que foi executada (se houver) | nvarchar(4000) | string |
| com êxito | succeeded_s | Indica se a ação que desencadeou o evento foi bem sucedida. Para eventos que não sejam login e lote, este apenas informa se a verificação de permissão foi bem sucedida ou falhou, não a operação. 1 = sucesso, 0 = falhar | bit | string |
| target_database_principal_id | target_database_principal_id_d | A base de dados principal da operação GRANT/DENY/REVOKE é realizada. 0 se não aplicável | int | int |
| target_database_principal_name | target_database_principal_name_s | Utilizador-alvo de ação. NULO se não aplicável | string | string |
| target_server_principal_id | target_server_principal_id_d | O principal do servidor em que a operação GRANT/DENY/REVOKE é realizada. Devoluções 0 se não aplicável | int | int |
| target_server_principal_name | target_server_principal_name_s | Login de ação. NULO se não aplicável | sysname | string |
| target_server_principal_sid | target_server_principal_sid_s | SID do login do alvo. NULO se não aplicável | varbinário | string |
| transaction_id | transaction_id_d | SQL Server (a partir de 2016) - 0 para Azure SQL Database | bigint | int |
| user_defined_event_id | user_defined_event_id_d | O ID do evento definido pelo utilizador passou como um argumento para sp_audit_write. NULO para eventos do sistema (padrão) e não zero para evento definido pelo utilizador. Para mais informações, consulte [sp_audit_write (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | A informação definida pelo utilizador passou como um argumento para sp_audit_write. NULO para eventos do sistema (padrão) e não zero para evento definido pelo utilizador. Para mais informações, consulte [sp_audit_write (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | string |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a auditoria da Base de Dados Azure SQL.](auditing-overview.md)