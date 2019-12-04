---
title: Replicação de dados-para MariaDB
description: Saiba mais sobre como usar a replicação de dados para sincronizar de um servidor externo no banco de dados do Azure para o serviço MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: e98f0dffe1ae004905c2b0969d825a1bca89014a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772643"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replicar dados para o Azure Database para MariaDB

A Replicação de Dados de Entrada permite sincronizar dados de um servidor MariaDB em execução no local, em máquinas virtuais (VMs) ou serviços de bases de dados alojados por outros fornecedores de cloud para o serviço Azure Database for MariaDB. A Replicação de Dados de Entrada é baseada na replicação baseada na posição dos ficheiros de registo binário (binlog) nativo para o MariaDB. Para saber mais sobre a replicação do binlog, consulte a [visão geral da replicação do binlog](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Quando usar Replicação de Dados
Os principais cenários a serem considerados no uso de Replicação de Dados são:

- **Sincronização de dados híbridos:** Com o Replicação de Dados, você pode manter os dados sincronizados entre os servidores locais e o banco de dados do Azure para MariaDB. Essa sincronização é útil para a criação de aplicativos híbridos. Esse método é atraente quando você tem um servidor de banco de dados local existente, mas deseja mover os dados para uma região mais próxima dos usuários finais.
- **Sincronização de várias nuvens:** Para soluções de nuvem complexas, use Replicação de Dados para sincronizar os dados entre o Azure Database para MariaDB e provedores de nuvem diferentes, incluindo máquinas virtuais e serviços de banco de dados hospedados nessas nuvens.

## <a name="limitations-and-considerations"></a>Limitações e considerações

### <a name="data-not-replicated"></a>Dados não replicados
O [*banco de dados do sistema MySQL*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) no servidor mestre não é replicado. As alterações nas contas e permissões no servidor mestre não são replicadas. Se você criar uma conta no servidor mestre e essa conta precisar acessar o servidor de réplica, crie manualmente a mesma conta no lado do servidor de réplica. Para entender quais tabelas estão contidas no banco de dados do sistema, consulte a [documentação do MariaDB](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Requisitos
- A versão do servidor mestre deve ser pelo menos MariaDB versão 10,2.
- As versões do servidor mestre e de réplica devem ser as mesmas. Por exemplo, ambos devem ser MariaDB versão 10,2.
- Cada tabela deve ter uma chave primária.
- O servidor mestre deve usar o mecanismo InnoDB.
- O usuário deve ter permissões para configurar o log binário e criar novos usuários no servidor mestre.
- Se o servidor mestre tiver o SSL habilitado, verifique se o certificado de autoridade de certificação SSL fornecido para o domínio foi incluído no procedimento armazenado `mariadb.az_replication_change_master`. Consulte os [exemplos](https://docs.microsoft.com/azure/mariadb/howto-data-in-replication#link-the-master-and-replica-servers-to-start-data-in-replication) a seguir e o parâmetro `master_ssl_ca`.
- Confirme se o endereço IP do servidor mestre foi adicionado às regras de firewall do servidor de réplica do Azure Database for MariaDB. Atualize as regras de firewall com o [portal do Azure](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-portal) ou a [CLI do Azure](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-cli).
- Verifique se o computador que aloja o servidor mestre permite tráfego de entrada e de saída na porta 3306.
- Verifique se o servidor mestre tem um **endereço IP público**, se o DNS está acessível publicamente ou se tem um FQDN (nome de domínio totalmente qualificado).

### <a name="other"></a>Outros
- Só há suporte para replicação de dados em Uso Geral e tipos de preço com otimização de memória.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [Configurar a replicação de dados](howto-data-in-replication.md).
