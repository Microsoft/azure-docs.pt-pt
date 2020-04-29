---
title: Backup e restauro – Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Proteger dados de corrupção acidental ou eliminação
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: bcc94b62812f1668bf8c5e5abb268fddf3da1fa5
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515446"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Backup e restauro na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)

A Base de Dados Azure para PostgreSQL – Hyperscale (Citus) cria automaticamente cópias de segurança de cada nó e armazena-as em armazenamento localmente redundante. As cópias de segurança podem ser utilizadas para restaurar o seu cluster de hiperescala (Citus) a um determinado tempo. Backup e restauro são uma parte essencial de qualquer estratégia de continuidade do negócio porque protegem os seus dados de corrupção acidental ou eliminação.

## <a name="backups"></a>Cópias de segurança

Pelo menos uma vez por dia, a Base de Dados Azure para PostgreSQL retira cópias de segurança instantâneas de ficheiros de dados e o registo de transações da base de dados. As cópias de segurança permitem restaurar um servidor a qualquer momento dentro do período de retenção. (O período de retenção é atualmente de 35 dias para todos os clusters.) Todas as cópias de segurança são encriptadas utilizando encriptação AES de 256 bits.

Nas regiões de Azure que suportam zonas de disponibilidade, os instantâneos de backup são armazenados em três zonas de disponibilidade. Desde que pelo menos uma zona de disponibilidade esteja on-line, o cluster Hyperscale (Citus) é rebatível.

Os ficheiros de reserva não podem ser exportados. Só podem ser utilizados para restaurar as operações na Base de Dados Azure para postgreSQL.

### <a name="backup-storage-cost"></a>Custo de armazenamento de reserva

Para obter preços de armazenamento de backup atuais, consulte a base de dados Azure para postgreSQL - página de [preços](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)de hiperescala (Citus).

## <a name="restore"></a>Restauro

Na Base de Dados Azure para PostgreSQL, restaurar um cluster de hiperescala (Citus) cria um novo cluster a partir das cópias de segurança dos nós originais.

> [!IMPORTANT]
> Os aglomerados de hiperescala eliminada (Citus) não podem ser restaurados. Se apagar o cluster, todos os nós que pertencem ao cluster são eliminados e não podem ser recuperados. Para proteger os recursos do cluster, após a implantação, de eliminação acidental ou alterações inesperadas, os administradores podem alavancar fechaduras de [gestão](/azure/azure-resource-manager/management/lock-resources).

### <a name="point-in-time-restore-pitr"></a>Restauro pontual (PITR)

Pode restaurar um aglomerado a qualquer momento nos últimos 35 dias.
A restauração pontual é útil em vários cenários. Por exemplo, quando um utilizador acidentalmente elimina dados, deixa cair uma tabela ou base de dados importantes, ou se uma aplicação acidentalmente substitui bons dados com dados maus.

O processo de restauro cria um novo cluster na mesma região azure, subscrição e grupo de recursos como o original. O cluster tem a configuração original: o mesmo número de nós, número de vCores, tamanho de armazenamento, funções de utilizador, versão PostgreSQL e versão da extensão Citus.

As definições de firewall e os parâmetros do servidor PostgreSQL não são preservados do grupo de servidor original, são repostos para valores padrão. A firewall evitará todas as ligações. Terá de ajustar manualmente estas definições após a restauração.

> [!IMPORTANT]
> Você precisará abrir um pedido de suporte para realizar restauro ponto-a-tempo do seu cluster Hyperscale (Citus).

### <a name="post-restore-tasks"></a>Post-restore tasks

Após um restauro de qualquer mecanismo de recuperação, deve fazer o seguinte para que os seus utilizadores e aplicações voltem a funcionar:

* Se o novo servidor se destina restar o servidor original, redirecione os clientes e as aplicações do cliente para o novo servidor
* Certifique-se de que estão em vigor as regras adequadas para a firewall de nível de servidor e as regras VNet para os utilizadores se conectarem. Estas regras não são copiadas do grupo de servidores originais.
* Ajuste os parâmetros do servidor PostgreSQL conforme necessário. Os parâmetros não são copiados do grupo de servidores originais.
* Certifique-se de que estão em vigor logins apropriados e permissões de nível de base de dados
* Configurar alertas, conforme adequado

## <a name="next-steps"></a>Passos seguintes

* Conheça [as zonas de disponibilidade do Azure.](/azure/availability-zones/az-overview)
* Deteto [alertas sugeridos](/azure/postgresql/howto-hyperscale-alert-on-metric#suggested-alerts) em grupos de servidores de hiperescala (Citus).
