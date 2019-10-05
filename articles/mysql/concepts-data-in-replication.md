---
title: Replicar dados para o Azure Database para MySQL
description: Saiba mais sobre como usar a replicação de dados para sincronizar de um servidor externo no banco de dados do Azure para o serviço MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: b501a1f1ea54aff5617932dc5085d6d19f86976c
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970355"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replicar dados para o Azure Database para MySQL

Replicação de Dados permite sincronizar dados de um servidor MySQL externo no banco de dados do Azure para o serviço MySQL. O servidor externo pode ser local, em máquinas virtuais ou em um serviço de banco de dados hospedado por outros provedores de nuvem. A Replicação de Dados de Entrada é baseada na replicação baseada na posição dos ficheiros de registo binário (binlog) nativo para MySQL. Para saber mais sobre a replicação do binlog, confira a [visão geral da replicação do MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Quando usar Replicação de Dados
Os principais cenários a serem considerados no uso de Replicação de Dados são:

- **Sincronização de dados híbridos:** Com o Replicação de Dados, você pode manter os dados sincronizados entre os servidores locais e o banco de dados do Azure para MySQL. Essa sincronização é útil para a criação de aplicativos híbridos. Esse método é atraente quando você tem um servidor de banco de dados local existente, mas deseja mover os dados para uma região mais próxima dos usuários finais.
- **Sincronização de várias nuvens:** Para soluções de nuvem complexas, use Replicação de Dados para sincronizar os dados entre o Azure Database para MySQL e diferentes provedores de nuvem, incluindo máquinas virtuais e serviços de banco de dados hospedados nessas nuvens.
 
Para cenários de migração, use o [serviço de migração de banco de dados do Azure](https://azure.microsoft.com/services/database-migration/)(DMS).

## <a name="limitations-and-considerations"></a>Limitações e considerações

### <a name="data-not-replicated"></a>Dados não replicados
O [*banco de dados do sistema MySQL*](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) no servidor mestre não está replicado. As alterações nas contas e permissões no servidor mestre não são replicadas. Se você criar uma conta no servidor mestre e essa conta precisar acessar o servidor de réplica, crie manualmente a mesma conta no lado do servidor de réplica. Para entender quais tabelas estão contidas no banco de dados do sistema, consulte o [manual do MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

### <a name="requirements"></a>Requisitos
- A versão do servidor mestre deve ser pelo menos a versão 5,6 do MySQL. 
- As versões do servidor mestre e de réplica devem ser as mesmas. Por exemplo, ambos devem ser o MySQL versão 5,6 ou ambos devem ser do MySQL versão 5,7.
- Cada tabela tem de ter uma chave primária.
- O servidor mestre deve usar o mecanismo InnoDB do MySQL.
- O usuário deve ter permissões para configurar o log binário e criar novos usuários no servidor mestre.
- Se o servidor mestre tiver o SSL habilitado, verifique se o certificado de autoridade de certificação SSL fornecido para o domínio foi incluído no procedimento armazenado `mysql.az_replication_change_master`. Consulte os [exemplos](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) a seguir e o parâmetro `master_ssl_ca`.
- Confirme se o endereço IP do servidor mestre foi adicionado às regras de firewall do servidor de réplica da Base de Dados do Azure para MySQL. Atualize as regras de firewall com o [portal do Azure](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) ou a [CLI do Azure](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli).
- Verifique se o computador que aloja o servidor mestre permite tráfego de entrada e de saída na porta 3306.
- Verifique se o servidor mestre tem um **endereço IP público**, se o DNS está acessível publicamente ou se tem um FQDN (nome de domínio totalmente qualificado).

### <a name="other"></a>Outros
- Só há suporte para replicação de dados em Uso Geral e tipos de preço com otimização de memória.
- Não há suporte para GTID (identificadores de transação global).

## <a name="next-steps"></a>Passos seguintes
- Saiba como [Configurar a replicação de dados em](howto-data-in-replication.md)
- Saiba mais sobre como [replicar no Azure com réplicas de leitura](concepts-read-replicas.md)
- Saiba mais sobre como [migrar dados com tempo de inatividade mínimo usando DMS](howto-migrate-online.md)