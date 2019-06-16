---
title: Procedimentos armazenados de base de dados do Azure para a replicação de dados de MariaDB
description: Este artigo apresenta todas as armazenadas procedimentos utilizados para a replicação de dados.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: reference
ms.date: 09/24/2018
ms.openlocfilehash: 75dc10ba3d95fd12ea99e10d321237560ee28171
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125870"
---
# <a name="azure-database-for-mariadb-data-in-replication-stored-procedures"></a>Procedimentos armazenados de base de dados do Azure para a replicação de dados de MariaDB

Replicação de dados-in permite-lhe sincronizar dados de um servidor de MariaDB em execução no local, nas máquinas virtuais ou serviços de base de dados hospedados por outros fornecedores de cloud para a base de dados do Azure para MariaDB serviço.

Os seguintes procedimentos armazenados são utilizados para configurar ou remover a replicação de dados entre um mestre e de réplica.

|**Nome do procedimento armazenado**|**Parâmetros de entrada**|**Parâmetros de saída**|**Nota de utilização**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/A|Para transferir dados com o modo SSL, passe o contexto do certificado de AC para o parâmetro master_ssl_ca. </br><br>Para transferir dados sem SSL, transmita uma cadeia vazia para o parâmetro master_ssl_ca.|
|*mysql.az_replication _start*|N/A|N/A|Inicia a replicação.|
|*mysql.az_replication _stop*|N/A|N/A|Para a replicação.|
|*mysql.az_replication _remove_master*|N/A|N/A|Remove a relação de replicação entre o mestre e a réplica.|
|*mysql.az_replication_skip_counter*|N/A|N/A|Ignora a um erro de replicação.|

Para configurar a dados na replicação entre um mestre e uma réplica na base de dados do Azure para MariaDB, consulte [como configurar a replicação de dados](howto-data-in-replication.md).
