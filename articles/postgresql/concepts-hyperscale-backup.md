---
title: Backup e restauro - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Proteger dados de corrupção ou supressão acidental
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 8dfc82ce79f33553be5220b52a1e415d99c26518
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483916"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Backup e restauro na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)

A Azure Database for PostgreSQL – Hyperscale (Citus) cria automaticamente cópias de segurança de cada nó e armazena-as num armazenamento localmente redundante. As cópias de segurança podem ser utilizadas para restaurar o cluster Hyperscale (Citus) num tempo especificado. A cópia de segurança e o restauro são uma parte essencial de qualquer estratégia de continuidade empresarial, uma vez que protegem os seus dados contra danos e a eliminação acidentais.

## <a name="backups"></a>Cópias de segurança

Pelo menos uma vez por dia, a Azure Database for PostgreSQL retira cópias de segurança instantâneas dos ficheiros de dados e do registo de transações de bases de dados. As cópias de segurança permitem restaurar um servidor a qualquer ponto no tempo dentro do período de retenção. (O período de retenção é atualmente de 35 dias para todos os clusters.) Todas as cópias de segurança são encriptadas utilizando encriptação AES de 256 bits.

Nas regiões de Azure que suportam zonas de disponibilidade, os instantâneos de backup são armazenados em três zonas de disponibilidade. Enquanto pelo menos uma zona de disponibilidade estiver on-line, o cluster Hyperscale (Citus) é ressaltável.

Os ficheiros de reserva não podem ser exportados. Só podem ser utilizados para operações de restauro na Base de Dados Azure para PostgreSQL.

### <a name="backup-storage-cost"></a>Custo de armazenamento de backup

Para obter preços de armazenamento de backup atuais, consulte a base de dados Azure para [a página de preços](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)PostgreSQL - Hyperscale (Citus).

## <a name="restore"></a>Restauro

Na Base de Dados Azure para PostgreSQL, restaurar um cluster de Hiperescala (Citus) cria um novo cluster a partir das cópias de segurança originais dos nós. 

> [!IMPORTANT]
>Só é possível restaurar o cluster Hyperscale (Citus) dentro do mesmo grupo de subscrição e recursos, e com um nome de cluster diferente.


> [!IMPORTANT]
> Os aglomerados de hiperescala (Citus) apagados não podem ser restaurados. Se eliminar o cluster, todos os nós que pertencem ao cluster são apagados e não podem ser recuperados. Para proteger os recursos de cluster, pós-implantação, de eliminação acidental ou alterações inesperadas, os administradores podem alavancar [os bloqueios de gestão](../azure-resource-manager/management/lock-resources.md).

### <a name="point-in-time-restore-pitr"></a>Restauro pontual (PITR)

Pode restaurar um aglomerado a qualquer ponto no tempo nos últimos 35 dias.
A restauração pontual é útil em vários cenários. Por exemplo, quando um utilizador elimina dados acidentalmente ou descarta uma tabela ou uma base de dados importante, ou se uma aplicação substituir acidentalmente dados corretos por dados incorretos.

O processo de restauro cria um novo cluster na mesma região de Azure, subscrição e grupo de recursos como o original. O cluster tem a configuração original: o mesmo número de nós, número de vCores, tamanho de armazenamento, funções de utilizador, versão PostgreSQL e versão da extensão Citus.

As definições de firewall e os parâmetros do servidor PostgreSQL não são preservados do grupo de servidor original, são reiniciados para valores predefinidos. A firewall evitará todas as ligações. Terá de ajustar manualmente estas definições após a restauração. Em geral, consulte a nossa lista de tarefas sugeridas [pós-restauro.](howto-hyperscale-restore-portal.md#post-restore-tasks)

## <a name="next-steps"></a>Passos seguintes

* Consulte os passos para [restaurar um grupo de servidores](howto-hyperscale-restore-portal.md) no portal Azure.
* Saiba mais sobre [as zonas de disponibilidade do Azure.](../availability-zones/az-overview.md)
