---
title: Procedimentos armazenados de base de dados do Azure para a replicação de dados de MariaDB
description: Este artigo apresenta todas as armazenadas procedimentos utilizados para a replicação de dados.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: reference
ms.date: 09/24/2018
ms.openlocfilehash: 75dc10ba3d95fd12ea99e10d321237560ee28171
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53535357"
---
# <a name="azure-database-for-mariadb-data-in-replication-stored-procedures"></a>Procedimentos armazenados de base de dados do Azure para a replicação de dados de MariaDB

Replicação de dados-in permite-lhe sincronizar dados de um servidor de MariaDB em execução no local, nas máquinas virtuais ou serviços de base de dados hospedados por outros fornecedores de cloud para a base de dados do Azure para MariaDB serviço.

Os seguintes procedimentos armazenados são utilizados para configurar ou remover a replicação de dados entre um mestre e de réplica.

|**Nome do procedimento armazenado**|**Parâmetros de entrada**|**Parâmetros de saída**|**Nota de utilização**|
|-----|-----|-----|-----|
|*MySQL.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/A|Para transferir dados com o modo SSL, passe o contexto do certificado de AC para o parâmetro master_ssl_ca. </br><br>Para transferir dados sem SSL, transmita uma cadeia vazia para o parâmetro master_ssl_ca.|
|*Iniciar de MySQL.az_replication*|N/A|N/A|Inicia a replicação.|
|*MySQL.az_replication _stop*|N/A|N/A|Para a replicação.|
|*MySQL.az_replication _remove_master*|N/A|N/A|Remove a relação de replicação entre o mestre e a réplica.|
|*MySQL.az_replication_skip_counter*|N/A|N/A|Ignora a um erro de replicação.|

Para configurar a dados na replicação entre um mestre e uma réplica na base de dados do Azure para MariaDB, consulte [como configurar a replicação de dados](howto-data-in-replication.md).
