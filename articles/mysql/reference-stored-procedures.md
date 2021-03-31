---
title: Procedimentos armazenados de gestão - Azure Database for MySQL
description: Saiba quais os procedimentos armazenados na Base de Dados Azure para o MySQL são úteis para ajudá-lo a configurar a replicação de dados, definir o fuso horário e matar consultas.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 7a1aa061bb8c8be3a676e0e5bb690b2a9749b6c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94536137"
---
# <a name="azure-database-for-mysql-management-stored-procedures"></a>Azure Database para procedimentos armazenados de gestão mySQL

Os procedimentos armazenados estão disponíveis na Base de Dados Azure para servidores MySQL para ajudar a gerir o seu servidor MySQL. Isto inclui gerir as ligações, consultas e configurar a replicação de dados.  

## <a name="data-in-replication-stored-procedures"></a>Procedimentos de replicação de dados armazenados

A Replicação de Dados de Entrada permite sincronizar dados de um servidor MySQL em execução no local, em máquinas virtuais (VMs) ou serviços de bases de dados alojados por outros fornecedores de cloud para o serviço da Base de Dados do Azure para MySQL.

Os seguintes procedimentos armazenados são utilizados para configurar ou remover a replicação de dados entre uma fonte e uma réplica.

|**Nome do procedimento armazenado**|**Parâmetros de Entrada**|**Parâmetros de saída**|**Nota de utilização**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/D|Para transferir dados com o modo SSL, passe no contexto do certificado de CA para o parâmetro master_ssl_ca. </br><br>Para transferir dados sem SSL, passe uma corda vazia para o parâmetro master_ssl_ca.|
|*mysql.az_replication _start*|N/D|N/D|Começa a replicação.|
|*mysql.az_replication _stop*|N/D|N/D|Para a replicação.|
|*mysql.az_replication _remove_master*|N/D|N/D|Remove a relação de replicação entre a fonte e a réplica.|
|*mysql.az_replication_skip_counter*|N/D|N/D|Salta um erro de replicação.|

Para configurar a replicação de dados entre uma fonte e uma réplica na Base de Dados Azure para o MySQL, consulte [como configurar a replicação de dados.](howto-data-in-replication.md)

## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

Os seguintes procedimentos armazenados estão disponíveis na Base de Dados Azure para o MySQL gerir o seu servidor.

|**Nome do procedimento armazenado**|**Parâmetros de Entrada**|**Parâmetros de saída**|**Nota de utilização**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|N/D|Equivalente ao [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) comando. Terminará a ligação associada à processlist_id fornecida após terminar qualquer declaração que a ligação esteja a executar.|
|*mysql.az_kill_query*|processlist_id|N/D|Equivalente ao [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) comando. Terminará a declaração que a ligação está atualmente a executar. Deixa a ligação viva.|
|*mysql.az_load_timezone*|N/D|N/D|Cargas tabelas de fuso horário para permitir que o `time_zone` parâmetro seja definido para valores nomeados (ex. "EUA/Pacífico").|

## <a name="next-steps"></a>Passos seguintes
- Saiba como configurar a [replicação de dados](howto-data-in-replication.md)
- Saiba como usar as tabelas de [fuso horário](howto-server-parameters.md#working-with-the-time-zone-parameter)
