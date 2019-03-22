---
title: Formato de registo de auditoria de base de dados SQL | Documentos da Microsoft
description: Compreenda a forma como os registos de auditoria de base de dados SQL são estruturados.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 0fefe01e413e30e4aa3c1fa90de77cbdece39c38
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58001682"
---
# <a name="sql-database-audit-log-format"></a>Formato de registo de auditoria de base de dados SQL

[A auditoria de base de dados SQL do Azure](sql-database-auditing.md) controla os eventos de base de dados e escreve-os para uma auditoria registo na sua conta de armazenamento do Azure ou envia para o Hub de eventos ou o Log Analytics para análise e processamento a jusante.

## <a name="naming-conventions"></a>Convenções de nomenclatura

### <a name="blob-audit"></a>Auditoria de BLOBs

Registos de auditoria armazenados no armazenamento de BLOBs são armazenados num contentor com o nome `sqldbauditlogs` na conta de armazenamento do Azure. A hierarquia de diretório dentro do contentor é o formato `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`. O formato de nome de ficheiro Blob é `<CreationTime>_<FileNumberInSession>.xel`, onde `CreationTime` está em UTC `hh_mm_ss_ms` formato, e `FileNumberInSession` é uma execução de índice no caso de sessão regista distribuições em vários arquivos de Blobs.

Por exemplo, para a base de dados `Database1` no `Server1` segue-se um caminho válido possíveis:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>Hub de Eventos

Eventos de auditoria são escritos para o hub de eventos e de espaço de nomes que foi definido durante a configuração de auditoria e é capturado no corpo da [Apache Avro](https://avro.apache.org/) eventos e armazenados através de formatação do JSON com codificação UTF-8. Para ler os registos de auditoria, pode usar [Avro ferramentas](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) ou ferramentas similares que processam neste formato.

### <a name="log-analytics"></a>Log Analytics

Eventos de auditoria são gravados a área de trabalho do Log Analytics definida durante a configuração de auditoria, para o `AzureDiagnostics` tabela com a categoria `SQLSecurityAuditEvents`. Para obter informações adicionais úteis sobre comandos e linguagem de pesquisa do Log Analytics, consulte [referência de pesquisa do Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search).

## <a id="subheading-1"></a>Campos de registo de auditoria

| Nome (Blob) | Nome (análise da Hubs/do registo de eventos) | Descrição | Tipo de blob | Tipo de análise de Hubs/do registo de eventos |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID da ação | varchar(4) | string |
| action_name | action_name_s | Nome da ação | N/A | string |
| additional_information | additional_information_s | Informações adicionais sobre o evento, armazenada como XML | nvarchar(4000) | string |
| affected_rows | affected_rows_d | Número de linhas afetadas pela consulta | bigint | int |
| application_name | application_name_s| Nome da aplicação de cliente | nvarchar(128) | string |
| audit_schema_version | audit_schema_version_d | Sempre 1 | int | int |
| class_type | class_type_s | Tipo de entidade auditável que a auditoria ocorre no | varchar(2) | string |
| class_type_desc | class_type_description_s | Descrição da entidade auditável que a auditoria ocorre em | N/A | string |
| client_ip | client_ip_s | IP da aplicação cliente de origem | nvarchar(128) | string |
| connection_id | N/A | ID da ligação no servidor | GUID | N/A |
| data_sensitivity_information | data_sensitivity_information_s | Tipos de informações e as etiquetas de sensibilidade devolvidas pela consulta auditada, com base nas colunas classificadas na base de dados. Saiba mais sobre [classificação e a base de dados do SQL Azure detetar dados](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | string |
| database_name | database_name_s | O contexto de base de dados em que ocorreu a ação | sysname | string |
| database_principal_id | database_principal_id_d | ID do contexto de utilizador de base de dados que a ação é executada no | int | int |
| database_principal_name | database_principal_name_s | Nome do contexto de utilizador de base de dados no qual a ação é realizada | sysname | string |
| duration_milliseconds | duration_milliseconds_d | Duração de execução de consulta em milissegundos | bigint | int |
| event_time | event_time_t | Data e hora quando a ação auditável é acionada | datetime2 | datetime |
| host_name | N/A | Nome de anfitrião do cliente | string | N/A |
| is_column_permission | is_column_permission_s | Sinalizador que indica se esta é uma permissão de nível de coluna. 1 = true, 0 = false | bit | string |
| N/A | is_server_level_audit_s | Sinalizador que indica se esta auditoria ao nível do servidor | N/A | string |
| id de object_ | object_id_d | O ID da entidade em que ocorreu a auditoria. Isto inclui o: objetos de servidor, bases de dados, os objetos de base de dados e objetos de esquema. 0 se a entidade é o próprio servidor ou se a auditoria não é realizada ao nível do objeto | int | int |
| object_name | object_name_s | O nome da entidade em que ocorreu a auditoria. Isto inclui o: objetos de servidor, bases de dados, os objetos de base de dados e objetos de esquema. 0 se a entidade é o próprio servidor ou se a auditoria não é realizada ao nível do objeto | sysname | string |
| permission_bitmask | permission_bitmask_s | Quando aplicável, mostra as permissões que foram concedidos, negadas ou revogadas | varbinary(16) | string |
| response_rows | response_rows_d | Número de linhas devolvidas no conjunto de resultados | bigint | int |
| schema_name | schema_name_s | O contexto de esquema no qual a ação ocorreu. NULL para ocorrer fora de um esquema de auditorias | sysname | string |
| N/A | securable_class_type_s | Objeto com capacidade de segurança que mapeia para o class_type estão sendo auditados | N/A | string |
| sequence_group_id | sequence_group_id_g | Identificador exclusivo | varbinary | GUID |
| sequence_number | sequence_number_d | Controla a sequência de registos num registo de auditoria único que era demasiado extensa para caber na memória intermédia de escrita para auditorias de | int | int |
| server_instance_name | server_instance_name_s | Nome da instância de servidor em que ocorreu a auditoria | sysname | string |
| server_principal_id | server_principal_id_d | ID do contexto de início de sessão em que a ação é realizada | int | int |
| server_principal_name | server_principal_name_s | Início de sessão atual | sysname | string |
| server_principal_sid | server_principal_sid_s | Início de sessão atual do SID | varbinary | string |
| session_id | session_id_d | ID da sessão em que ocorreu o evento | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Principal do servidor para a sessão | sysname | string |
| Instrução | statement_s | Instrução de T-SQL que foi executada (se houver) | nvarchar(4000) | string |
| Foi efetuada com êxito | succeeded_s | Indica se a ação que acionou o evento foi bem-sucedida. Para eventos que não sejam o início de sessão e em lote, isto apenas indica se a verificação de permissões foi efetuada com êxito ou falha, não a operação. 1 = êxito, 0 = falha | bit | string |
| target_database_principal_id | target_database_principal_id_d | O principal de base de dados é efetuar a operação de GRANT/DENY/REVOKE no. 0 se não aplicável | int | int |
| target_database_principal_name | target_database_principal_name_s | Utilizador de destino da ação. NULO se não aplicável | string | string |
| target_server_principal_id | target_server_principal_id_d | Principal do servidor que a operação de GRANT/DENY/REVOKE é efetuada. Devolve 0 se não for aplicável | int | int |
| target_server_principal_name | target_server_principal_name_s | Início de sessão de destino da ação. NULO se não aplicável | sysname | string |
| target_server_principal_sid | target_server_principal_sid_s | SID de início de sessão de destino. NULO se não aplicável | varbinary | string |
| transaction_id | transaction_id_d | Apenas SQL Server (a partir de 2016) - 0 para a BD SQL do Azure | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Id de evento transmitido como argumento para sp_audit_write definido pelo utilizador. NULO para eventos do sistema (predefinição) e diferente de zero para evento definido pelo utilizador. Para obter mais informações, consulte [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | As informações passadas como um argumento ao sp_audit_write definido pelo utilizador. NULO para eventos do sistema (predefinição) e diferente de zero para evento definido pelo utilizador. Para obter mais informações, consulte [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | string |

## <a name="next-steps"></a>Próximos Passos

Saiba mais sobre [auditoria de base de dados do Azure SQL](sql-database-auditing.md).