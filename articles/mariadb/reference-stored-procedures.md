---
title: Procedimentos armazenados de gestão - Base de Dados Azure para MariaDB
description: Saiba quais os procedimentos armazenados na Base de Dados Azure para O MariaDB são úteis para ajudá-lo a configurar a replicação de dados, definir o fuso horário e eliminar consultas.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2f6d1e20db64cb0c2a64771ea26b971b22031fd9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79529995"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>Base de Dados Azure para procedimentos armazenados de gestão MariaDB

Os procedimentos armazenados estão disponíveis na Base de Dados Azure para servidores MariaDB para ajudar a gerir o seu servidor MariaDB. Isto inclui gerir as ligações, consultas e configuração da Replicação data-in do seu servidor.  

## <a name="data-in-replication-stored-procedures"></a>Procedimentos armazenados de replicação de dados

A Replicação de Dados de Entrada permite sincronizar dados de um servidor MariaDB em execução no local, em máquinas virtuais (VMs) ou serviços de bases de dados alojados por outros fornecedores de cloud para o serviço Azure Database for MariaDB.

Os seguintes procedimentos armazenados são utilizados para configurar ou remover a Replicação data-in entre um mestre e uma réplica.

|**Nome do procedimento armazenado**|**Parâmetros de Entrada**|**Parâmetros de saída**|**Nota de utilização**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/D|Para transferir dados com o modo SSL, passe no contexto do certificado CA para o parâmetro master_ssl_ca. </br><br>Para transferir dados sem SSL, passe uma corda vazia para o parâmetro master_ssl_ca.|
|*mysql.az_replication _start*|N/D|N/D|Começa a replicação.|
|*mysql.az_replication _stop*|N/D|N/D|Para a replicação.|
|*mysql.az_replication _remove_master*|N/D|N/D|Remove a relação de replicação entre o mestre e a réplica.|
|*mysql.az_replication_skip_counter*|N/D|N/D|Ignora um erro de replicação.|

Para configurar a Replicação de Dados entre um mestre e uma réplica na Base de Dados Azure para OMariaDB, consulte [como configurar a Replicação de Dados](howto-data-in-replication.md)em Conjunto .

## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

Os seguintes procedimentos armazenados estão disponíveis na Base de Dados Azure para que o MariaDB gere o seu servidor.

|**Nome do procedimento armazenado**|**Parâmetros de Entrada**|**Parâmetros de saída**|**Nota de utilização**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|N/D|Equivalente [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) ao comando. Terminará a ligação associada à processlist_id fornecida após a cessação de qualquer declaração que a ligação está a executar.|
|*mysql.az_kill_query*|processlist_id|N/D|Equivalente [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) ao comando. Terminará a declaração que a ligação está a executar. Deixa a ligação em si viva.|
|*mysql.az_load_timezone*|N/D|N/D|Monte sipice `time_zone` as tabelas do fuso horário para permitir que o parâmetro seja definido para valores nomeados (ex. "EUA/Pacífico").|

## <a name="next-steps"></a>Passos seguintes
- Saiba como configurar a [Replicação de Dados](howto-data-in-replication.md)
- Aprenda a usar as tabelas de [fusos horários](howto-server-parameters.md#working-with-the-time-zone-parameter)