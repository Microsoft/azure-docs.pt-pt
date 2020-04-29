---
title: Replicação de dados - Base de Dados Azure para MariaDB
description: Saiba utilizar a replicação de data-in para sincronizar a partir de um servidor externo para a Base de Dados Azure para o serviço MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 1fbcc1fb27d5e6df4641f79c0d634580f74000b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79532065"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replicar dados na Base de Dados Azure para MariaDB

A Replicação de Dados de Entrada permite sincronizar dados de um servidor MariaDB em execução no local, em máquinas virtuais (VMs) ou serviços de bases de dados alojados por outros fornecedores de cloud para o serviço Azure Database for MariaDB. A Replicação de Dados de Entrada é baseada na replicação baseada na posição dos ficheiros de registo binário (binlog) nativo para o MariaDB. Para saber mais sobre a replicação do binlog, consulte a visão geral da replicação do [binlog](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Quando utilizar a Replicação data-in
Os principais cenários a considerar na utilização da Replicação data-in são:

- **Sincronização de dados híbridos:** Com a Replicação data-in, pode manter os dados sincronizados entre os seus servidores no local e a Base de Dados Azure para MariaDB. Esta sincronização é útil para a criação de aplicações híbridas. Este método é apelativo quando se tem um servidor de base de dados local existente, mas pretende mover os dados para uma região mais próxima dos utilizadores finais.
- **Sincronização multi-nuvem:** Para soluções complexas em nuvem, utilize a Replicação data-in para sincronizar dados entre a Base de Dados Azure para ODDb e diferentes fornecedores de nuvem, incluindo máquinas virtuais e serviços de base de dados alojados nessas nuvens.

## <a name="limitations-and-considerations"></a>Limitações e considerações

### <a name="data-not-replicated"></a>Dados não replicados
A base de dados do [*sistema mysql*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) no servidor principal não é replicada. As alterações às contas e permissões no servidor principal não são replicadas. Se criar uma conta no servidor principal e esta conta precisar de aceder ao servidor de réplicas, então crie manualmente a mesma conta no lado do servidor de réplicas. Para entender quais as tabelas contidas na base de dados do sistema, consulte a [documentação mariaDB.](https://mariadb.com/kb/en/library/the-mysql-database-tables/)

### <a name="requirements"></a>Requisitos
- A versão master server deve ser pelo menos a versão 10.2 do MariaDB.
- As versões master e réplica do servidor devem ser as mesmas. Por exemplo, ambos devem ser a versão 10.2 do MariaDB.
- Cada mesa deve ter uma chave primária.
- O servidor principal deve utilizar o motor InnoDB.
- O utilizador deve ter permissões para configurar o registo binário e criar novos utilizadores no servidor principal.
- Se o servidor principal tiver ativado o SSL, certifique-se de que `mariadb.az_replication_change_master` o certificado SSL CA fornecido para o domínio foi incluído no procedimento armazenado. Consulte os [seguintes exemplos](https://docs.microsoft.com/azure/mariadb/howto-data-in-replication#link-the-master-and-replica-servers-to-start-data-in-replication) e o `master_ssl_ca` parâmetro.
- Confirme se o endereço IP do servidor mestre foi adicionado às regras de firewall do servidor de réplica do Azure Database for MariaDB. Atualize as regras de firewall com o [portal do Azure](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-portal) ou a [CLI do Azure](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-cli).
- Verifique se o computador que aloja o servidor mestre permite tráfego de entrada e de saída na porta 3306.
- Certifique-se de que o servidor principal tem um **endereço IP público,** o DNS é acessível ao público ou tem um nome de domínio totalmente qualificado (FQDN).

### <a name="other"></a>Outros
- A replicação de data-in só é suportada nos níveis de preços otimizados para fins gerais e memória.

## <a name="next-steps"></a>Passos seguintes
- Saiba como configurar a [replicação de dados.](howto-data-in-replication.md)
