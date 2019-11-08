---
title: Formato do Registo de Auditoria
description: Entenda como os logs de auditoria do banco de dados SQL são estruturados.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 389404f59b5a0cba1acd7aa097ddd3dd929d8082
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821999"
---
# <a name="sql-database-audit-log-format"></a>Formato do log de auditoria do banco de dados SQL

A [auditoria do banco de dados SQL do Azure](sql-database-auditing.md) rastreia eventos de banco de dados e os grava em um log de auditoria em sua conta de armazenamento do Azure ou os envia ao Hub de eventos ou log Analytics para análise e processamento de downstream.

## <a name="naming-conventions"></a>Convenções de nomenclatura

### <a name="blob-audit"></a>Auditoria de BLOB

Os logs de auditoria armazenados no armazenamento de BLOBs são armazenados em um contêiner chamado `sqldbauditlogs` na conta de armazenamento do Azure. A hierarquia de diretórios dentro do contêiner está no formato `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`. O formato de nome de arquivo de blob é `<CreationTime>_<FileNumberInSession>.xel`, em que `CreationTime` está no formato UTC `hh_mm_ss_ms` e `FileNumberInSession` é um índice em execução no caso de logs de sessão se estendem por vários arquivos de BLOB.

Por exemplo, para o banco de dados `Database1` em `Server1` o seguinte caminho válido é possível:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>Hub de Eventos

Os eventos de auditoria são gravados no namespace e no Hub de eventos que foi definido durante a configuração de auditoria e são capturados no corpo de eventos do [Apache Avro](https://avro.apache.org/) e armazenados usando a formatação JSON com codificação UTF-8. Para ler os logs de auditoria, você pode usar [Ferramentas do Avro](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) ou ferramentas semelhantes que processam esse formato.

### <a name="log-analytics"></a>Log Analytics

Os eventos de auditoria são gravados no espaço de trabalho Log Analytics definido durante a configuração de auditoria, na tabela `AzureDiagnostics` com a categoria `SQLSecurityAuditEvents`. Para obter mais informações úteis sobre Log Analytics idioma e comandos de pesquisa, consulte [log Analytics referência de pesquisa](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search).

## <a id="subheading-1"></a>Campos do log de auditoria

| Nome (BLOB) | Nome (hubs de eventos/Log Analytics) | Descrição | Tipo de BLOB | Tipo de Log Analytics/hubs de eventos |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID da ação | varchar (4) | string |
| action_name | action_name_s | Nome da ação | N/D | string |
| additional_information | additional_information_s | Quaisquer informações adicionais sobre o evento, armazenadas como XML | nvarchar (4000) | string |
| affected_rows | affected_rows_d | Número de linhas afetadas pela consulta | bigint | int |
| application_name | application_name_s| Nome do aplicativo cliente | nvarchar(128 | string |
| audit_schema_version | audit_schema_version_d | Sempre 1 | int | int |
| class_type | class_type_s | Tipo de entidade auditável na qual a auditoria ocorre | varchar (2) | string |
| class_type_desc | class_type_description_s | Descrição da entidade auditável na qual a auditoria ocorre | N/D | string |
| client_ip | client_ip_s | IP de origem do aplicativo cliente | nvarchar(128 | string |
| connection_id | N/D | ID da conexão no servidor | VOLUME | N/D |
| data_sensitivity_information | data_sensitivity_information_s | Tipos de informações e rótulos de sensibilidade retornados pela consulta auditada, com base nas colunas classificadas no banco de dados. Saiba mais sobre a [descoberta e a classificação de dados do banco SQL do Azure](sql-database-data-discovery-and-classification.md) | nvarchar (4000) | string |
| database_name | database_name_s | O contexto do banco de dados no qual a ação ocorreu | sysname | string |
| database_principal_id | database_principal_id_d | ID do contexto de usuário do banco de dados no qual a ação é executada | int | int |
| database_principal_name | database_principal_name_s | Nome do contexto de usuário do banco de dados no qual a ação é executada | sysname | string |
| duration_milliseconds | duration_milliseconds_d | Duração da execução da consulta em milissegundos | bigint | int |
| event_time | event_time_t | Data e hora em que a ação auditável é acionada | datetime2 | datetime |
| host_name | N/D | Nome do host do cliente | string | N/D |
| is_column_permission | is_column_permission_s | Sinalizador que indica se esta é uma permissão de nível de coluna. 1 = true, 0 = false | parte | string |
| N/D | is_server_level_audit_s | Sinalizador indicando se essa auditoria está no nível do servidor | N/D | string |
| ID de object_ | object_id_d | A ID da entidade na qual a auditoria ocorreu. Isso inclui os objetos de servidor:, bancos de dados, objetos de banco e objetos de esquema. 0 se a entidade for o próprio servidor ou se a auditoria não for executada em um nível de objeto | int | int |
| object_name | object_name_s | O nome da entidade na qual a auditoria ocorreu. Isso inclui os objetos de servidor:, bancos de dados, objetos de banco e objetos de esquema. 0 se a entidade for o próprio servidor ou se a auditoria não for executada em um nível de objeto | sysname | string |
| permission_bitmask | permission_bitmask_s | Quando aplicável, mostra as permissões que foram concedidas, negadas ou revogadas | varbinary (16) | string |
| response_rows | response_rows_d | Número de linhas retornadas no conjunto de resultados | bigint | int |
| schema_name | schema_name_s | O contexto de esquema no qual a ação ocorreu. NULL para auditorias que ocorrem fora de um esquema | sysname | string |
| N/D | securable_class_type_s | Objeto protegível que mapeia para o class_type que está sendo auditado | N/D | string |
| sequence_group_id | sequence_group_id_g | Identificador exclusivo | varbinary | VOLUME |
| sequence_number | sequence_number_d | Controla a sequência de registros em um único registro de auditoria que era muito grande para caber no buffer de gravação para auditorias | int | int |
| server_instance_name | server_instance_name_s | Nome da instância do servidor em que ocorreu a auditoria | sysname | string |
| server_principal_id | server_principal_id_d | ID do contexto de logon no qual a ação é executada | int | int |
| server_principal_name | server_principal_name_s | Logon atual | sysname | string |
| server_principal_sid | server_principal_sid_s | SID de logon atual | varbinary | string |
| session_id | session_id_d | ID da sessão na qual o evento ocorreu | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Entidade de segurança do servidor para sessão | sysname | string |
| privacidade | statement_s | Instrução T-SQL que foi executada (se houver) | nvarchar (4000) | string |
| Foi | succeeded_s | Indica se a ação que disparou o evento foi bem-sucedida. Para eventos diferentes de logon e lote, isso apenas relata se a verificação de permissão foi bem-sucedida ou falhou, não a operação. 1 = êxito, 0 = falha | parte | string |
| target_database_principal_id | target_database_principal_id_d | A entidade de banco de dados que a operação GRANT/DENY/REVOKE é executada em. 0 se não for aplicável | int | int |
| target_database_principal_name | target_database_principal_name_s | Usuário de destino da ação. NULL se não for aplicável | string | string |
| target_server_principal_id | target_server_principal_id_d | Entidade de segurança do servidor na qual a operação de concessão/NEGAção/REVOGAção é executada. Retorna 0 se não aplicável | int | int |
| target_server_principal_name | target_server_principal_name_s | Logon de destino da ação. NULL se não for aplicável | sysname | string |
| target_server_principal_sid | target_server_principal_sid_s | SID do logon de destino. NULL se não for aplicável | varbinary | string |
| transaction_id | transaction_id_d | Somente SQL Server (começando com 2016)-0 para BD SQL do Azure | bigint | int |
| user_defined_event_id | user_defined_event_id_d | ID do evento definido pelo usuário passada como um argumento para sp_audit_write. NULL para eventos do sistema (padrão) e diferente de zero para o evento definido pelo usuário. Para obter mais informações, consulte [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | Informações definidas pelo usuário passadas como um argumento para sp_audit_write. NULL para eventos do sistema (padrão) e diferente de zero para o evento definido pelo usuário. Para obter mais informações, consulte [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar (4000) | string |

## <a name="next-steps"></a>Próximos Passos

Saiba mais sobre a [auditoria do banco de dados SQL do Azure](sql-database-auditing.md).