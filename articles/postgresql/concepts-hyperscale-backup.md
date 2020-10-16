---
title: Backup e restauro - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Proteger dados de corrupção ou supressão acidental
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 2d781ca7293d4bd95ae62eadc50295ca14c2d381
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91314935"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Backup e restauro na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)

A Azure Database for PostgreSQL – Hyperscale (Citus) cria automaticamente cópias de segurança de cada nó e armazena-as num armazenamento localmente redundante. As cópias de segurança podem ser utilizadas para restaurar o cluster Hyperscale (Citus) num tempo especificado. Backup e restauro são uma parte essencial de qualquer estratégia de continuidade do negócio porque protegem os seus dados de corrupção acidental ou eliminação.

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
> Os aglomerados de hiperescala (Citus) apagados não podem ser restaurados. Se eliminar o cluster, todos os nós que pertencem ao cluster são apagados e não podem ser recuperados. Para proteger os recursos de cluster, pós-implantação, de eliminação acidental ou alterações inesperadas, os administradores podem alavancar [os bloqueios de gestão](/azure/azure-resource-manager/management/lock-resources).

### <a name="point-in-time-restore-pitr"></a>Restauro pontual (PITR)

Pode restaurar um aglomerado a qualquer ponto no tempo nos últimos 35 dias.
A restauração pontual é útil em vários cenários. Por exemplo, quando um utilizador elimina acidentalmente dados, deixa cair uma tabela ou base de dados importantes, ou se uma aplicação acidentalmente substitui bons dados com dados ruins.

O processo de restauro cria um novo cluster na mesma região de Azure, subscrição e grupo de recursos como o original. O cluster tem a configuração original: o mesmo número de nós, número de vCores, tamanho de armazenamento, funções de utilizador, versão PostgreSQL e versão da extensão Citus.

As definições de firewall e os parâmetros do servidor PostgreSQL não são preservados do grupo de servidor original, são reiniciados para valores predefinidos. A firewall evitará todas as ligações. Terá de ajustar manualmente estas definições após a restauração.

> [!IMPORTANT]
> Terá de abrir um pedido de apoio para efetuar a restauração pontual do seu cluster Hyperscale (Citus).

### <a name="post-restore-tasks"></a>Post-restore tasks

Após uma restauração de qualquer mecanismo de recuperação, deve fazer o seguinte para que os seus utilizadores e aplicações voltem a funcionar:

* Se o novo servidor pretende substituir o servidor original, redirecione clientes e aplicações de clientes para o novo servidor
* Certifique-se de que existem regras adequadas ao nível do servidor e das regras VNet para que os utilizadores se conectem. Estas regras não são copiadas do grupo de servidores originais.
* Ajuste os parâmetros do servidor PostgreSQL conforme necessário. Os parâmetros não são copiados do grupo de servidor original.
* Certifique-se de que estão em vigor logins e permissões de nível de base de dados apropriados
* Configurar alertas, conforme adequado

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [as zonas de disponibilidade do Azure.](/azure/availability-zones/az-overview)
*  [Desenhe alertas sugeridos](/azure/postgresql/howto-hyperscale-alert-on-metric#suggested-alerts) em grupos de servidores Hyperscale (Citus).
