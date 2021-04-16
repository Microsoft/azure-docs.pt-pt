---
title: Backup e restauro - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Proteger dados de corrupção ou supressão acidental
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/14/2021
ms.openlocfilehash: 7681e9c28bbbbcec06bcc1cf2bf469f1b4189d79
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520178"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Backup e restauro na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)

A Azure Database for PostgreSQL – Hyperscale (Citus) cria automaticamente cópias de segurança de cada nó e armazena-as num armazenamento localmente redundante. As cópias de segurança podem ser utilizadas para restaurar o seu grupo de servidorEs Hyperscale (Citus) a uma hora especificada.
A cópia de segurança e o restauro são uma parte essencial de qualquer estratégia de continuidade empresarial, uma vez que protegem os seus dados contra danos e a eliminação acidentais.

## <a name="backups"></a>Cópias de segurança

Pelo menos uma vez por dia, a Azure Database for PostgreSQL retira cópias de segurança instantâneas dos ficheiros de dados e do registo de transações de bases de dados. As cópias de segurança permitem restaurar um servidor a qualquer ponto no tempo dentro do período de retenção. (O período de retenção é atualmente de 35 dias para todos os grupos de servidores.) Todas as cópias de segurança são encriptadas utilizando encriptação AES de 256 bits.

Nas regiões de Azure que suportam zonas de disponibilidade, os instantâneos de backup são armazenados em três zonas de disponibilidade. Enquanto pelo menos uma zona de disponibilidade estiver on-line, o grupo de servidor Hyperscale (Citus) é restaurador.

Os ficheiros de reserva não podem ser exportados. Só podem ser utilizados para operações de restauro na Base de Dados Azure para PostgreSQL.

### <a name="backup-storage-cost"></a>Custo de armazenamento de backup

Para obter preços de armazenamento de backup atuais, consulte a base de dados Azure para [a página de preços](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)PostgreSQL - Hyperscale (Citus).

## <a name="restore"></a>Restauro

Pode restaurar um grupo de servidor Hyperscale (Citus) a qualquer ponto no tempo nos últimos 35 dias.  A restauração pontual é útil em vários cenários. Por exemplo, quando um utilizador elimina dados acidentalmente ou descarta uma tabela ou uma base de dados importante, ou se uma aplicação substituir acidentalmente dados corretos por dados incorretos.

> [!IMPORTANT]
> Os grupos de servidores de Hiperescala (Citus) apagados não podem ser restaurados. Se eliminar o grupo de servidores, todos os nós que pertencem ao grupo do servidor são eliminados e não podem ser recuperados. Para proteger os recursos do grupo do servidor, a implantação pós-implantação, contra a eliminação acidental ou alterações inesperadas, os administradores podem alavancar [os bloqueios de gestão](../azure-resource-manager/management/lock-resources.md).

O processo de restauro cria um novo grupo de servidores na mesma região de Azure, subscrição e grupo de recursos como o original. O grupo de servidores tem a configuração original: o mesmo número de nós, número de vCores, tamanho de armazenamento, funções de utilizador, versão PostgreSQL e versão da extensão Citus.

As definições de firewall e os parâmetros do servidor PostgreSQL não são preservados do grupo de servidor original, são reiniciados para valores predefinidos. A firewall evitará todas as ligações. Terá de ajustar manualmente estas definições após a restauração. Em geral, consulte a nossa lista de tarefas sugeridas [pós-restauro.](howto-hyperscale-restore-portal.md#post-restore-tasks)

## <a name="next-steps"></a>Passos seguintes

* Consulte os passos para [restaurar um grupo de servidores](howto-hyperscale-restore-portal.md) no portal Azure.
* Saiba mais sobre [as zonas de disponibilidade do Azure.](../availability-zones/az-overview.md)
