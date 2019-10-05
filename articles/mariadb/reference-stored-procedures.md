---
title: Banco de dados do Azure para procedimentos armazenados de gerenciamento do MariaDB
description: Saiba quais procedimentos armazenados no banco de dados do Azure para MySQL são úteis para ajudá-lo a configurar a replicação de dados, definir o fuso horário e eliminar as consultas.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 0a33edeac735502964427ddc3b05076fb9fac969
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973476"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>Banco de dados do Azure para procedimentos armazenados de gerenciamento do MariaDB

Os procedimentos armazenados estão disponíveis no banco de dados do Azure para servidores MariaDB para ajudar a gerenciar seu servidor MariaDB. Isso inclui o gerenciamento de conexões do seu servidor, consultas e configuração do Replicação de Dados.  

## <a name="data-in-replication-stored-procedures"></a>Replicação de Dados procedimentos armazenados

A Replicação de Dados de Entrada permite sincronizar dados de um servidor MariaDB em execução no local, em máquinas virtuais (VMs) ou serviços de bases de dados alojados por outros fornecedores de cloud para o serviço Azure Database for MariaDB.

Os procedimentos armazenados a seguir são usados para configurar ou remover Replicação de Dados entre um mestre e uma réplica.

|**Nome do procedimento armazenado**|**Parâmetros de Entrada**|**Parâmetros de saída**|**Nota de uso**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/A|Para transferir dados com o modo SSL, passe o contexto do certificado de autoridade de certificação para o parâmetro master_ssl_ca. </br><br>Para transferir dados sem SSL, transmita uma cadeia de caracteres vazia para o parâmetro master_ssl_ca.|
|*mysql.az_replication _start*|N/A|N/A|Inicia a replicação.|
|*mysql.az_replication _stop*|N/A|N/A|Para a replicação.|
|*mysql.az_replication _remove_master*|N/A|N/A|Remove a relação de replicação entre o mestre e a réplica.|
|*mysql.az_replication_skip_counter*|N/A|N/A|Ignora um erro de replicação.|

Para configurar Replicação de Dados entre um mestre e uma réplica no banco de dados do Azure para MariaDB, consulte [como configurar o replicação de dados](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

Os procedimentos armazenados a seguir estão disponíveis no banco de dados do Azure para MariaDB para gerenciar seu servidor.

|**Nome do procedimento armazenado**|**Parâmetros de Entrada**|**Parâmetros de saída**|**Nota de uso**|
|-----|-----|-----|-----|
|*MySQL. AZ _Kill*|processlist_id|N/A|Equivalente ao comando [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) . Encerrará a conexão associada ao processlist_id fornecido depois de encerrar qualquer instrução que a conexão estiver executando.|
|*MySQL. AZ _kill_query*|processlist_id|N/A|Equivalente ao comando [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) . Encerrará a instrução que a conexão está executando no momento. Deixa a própria conexão ativa.|
|*MySQL. AZ _load_timezone*|N/A|N/A|Carrega tabelas de fuso horário para permitir que o parâmetro `time_zone` seja definido para valores nomeados (ex.: "EUA/Pacífico").|

## <a name="next-steps"></a>Passos seguintes
- Saiba como configurar [replicação de dados](howto-data-in-replication.md)
- Saiba como usar as [tabelas de fuso horário](howto-server-parameters.md#working-with-the-time-zone-parameter)