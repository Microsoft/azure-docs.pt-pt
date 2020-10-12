---
title: Procedimentos armazenados de gestão - Azure Database for MariaDB
description: Saiba quais os procedimentos armazenados na Base de Dados Azure para MariaDB são úteis para ajudá-lo a configurar a replicação de dados, definir o fuso horário e matar consultas.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 453cb28b3053ee2fd2706a5537dc71b6cdca4174
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91539848"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>Azure Database para procedimentos armazenados de gestão MariaDB

Os procedimentos armazenados estão disponíveis na Base de Dados Azure para servidores MariaDB para ajudar a gerir o seu servidor MariaDB. Isto inclui gerir as ligações, consultas e configurar a replicação de dados.  

## <a name="data-in-replication-stored-procedures"></a>Procedimentos de replicação de dados armazenados

A Replicação de Dados de Entrada permite sincronizar dados de um servidor MariaDB em execução no local, em máquinas virtuais (VMs) ou serviços de bases de dados alojados por outros fornecedores de cloud para o serviço Azure Database for MariaDB.

Os seguintes procedimentos armazenados são utilizados para configurar ou remover a replicação de dados entre uma fonte e uma réplica.

|**Nome do procedimento armazenado**|**Parâmetros de Entrada**|**Parâmetros de saída**|**Nota de utilização**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/D|Para transferir dados com o modo SSL, passe no contexto do certificado de CA para o parâmetro master_ssl_ca. </br><br>Para transferir dados sem SSL, passe uma corda vazia para o parâmetro master_ssl_ca.|
|*mysql.az_replication _start*|N/D|N/D|Começa a replicação.|
|*mysql.az_replication _stop*|N/D|N/D|Para a replicação.|
|*mysql.az_replication _remove_master*|N/D|N/D|Remove a relação de replicação entre a fonte e a réplica.|
|*mysql.az_replication_skip_counter*|N/D|N/D|Salta um erro de replicação.|

Para configurar a replicação de dados entre uma fonte e uma réplica na Base de Dados Azure para MariaDB, consulte [como configurar a replicação de dados.](howto-data-in-replication.md)

## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

Os seguintes procedimentos armazenados estão disponíveis na Base de Dados Azure para mariaDB gerir o seu servidor.

|**Nome do procedimento armazenado**|**Parâmetros de Entrada**|**Parâmetros de saída**|**Nota de utilização**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|N/D|Equivalente ao [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) comando. Terminará a ligação associada à processlist_id fornecida após terminar qualquer declaração que a ligação esteja a executar.|
|*mysql.az_kill_query*|processlist_id|N/D|Equivalente ao [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) comando. Terminará a declaração que a ligação está atualmente a executar. Deixa a ligação viva.|
|*mysql.az_load_timezone*|N/D|N/D|Cargas tabelas de fuso horário para permitir que o `time_zone` parâmetro seja definido para valores nomeados (ex. "EUA/Pacífico").|

## <a name="next-steps"></a>Passos seguintes
- Saiba como configurar a [replicação de dados](howto-data-in-replication.md)
- Saiba como usar as tabelas de [fuso horário](howto-server-parameters.md#working-with-the-time-zone-parameter)