---
title: Replicação de dados - Base de Dados Azure para MariaDB
description: Saiba como utilizar a replicação de dados para sincronizar a partir de um servidor externo na Base de Dados Azure para o serviço MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 331e064bcf11af31a778cb8dd06c463712421b7c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533434"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replicar dados na Base de Dados Azure para MariaDB

A Replicação de Dados de Entrada permite sincronizar dados de um servidor MariaDB em execução no local, em máquinas virtuais (VMs) ou serviços de bases de dados alojados por outros fornecedores de cloud para o serviço Azure Database for MariaDB. A Replicação de Dados de Entrada é baseada na replicação baseada na posição dos ficheiros de registo binário (binlog) nativo para o MariaDB. Para saber mais sobre a replicação do binlog, consulte a [visão geral](https://mariadb.com/kb/en/library/replication-overview/)da replicação do binlog .

## <a name="when-to-use-data-in-replication"></a>Quando utilizar a replicação de dados
Os principais cenários a considerar usando a replicação de dados são:

- **Sincronização de dados híbridos:** Com a replicação de dados, pode manter os dados sincronizados entre os seus servidores no local e a Base de Dados Azure para o MariaDB. Esta sincronização é útil para criar aplicações híbridas. Este método é apelativo quando se tem um servidor de base de dados local existente, mas pretende mover os dados para uma região mais próxima dos utilizadores finais.
- **Sincronização multi-nuvem:** Para soluções complexas em nuvem, utilize a Replicação de Dados para sincronizar dados entre a Base de Dados Azure para MariaDB e diferentes fornecedores de nuvem, incluindo máquinas virtuais e serviços de base de dados alojados nessas nuvens.

## <a name="limitations-and-considerations"></a>Limitações e considerações

### <a name="data-not-replicated"></a>Dados não replicados
A base de dados do [*sistema mysql*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) no servidor de origem não é replicada. As alterações nas contas e permissões no servidor de origem não são replicadas. Se criar uma conta no servidor de origem e esta conta precisar de aceder ao servidor de réplicas, então crie manualmente a mesma conta no lado do servidor de réplica. Para entender quais as tabelas contidas na base de dados do sistema, consulte a [documentação mariaDB](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Requisitos
- A versão do servidor de origem deve ser, pelo menos, a versão 10.2 do MariaDB.
- As versões de servidor de origem e réplica devem ser as mesmas. Por exemplo, ambos devem ser a versão MariaDB 10.2.
- Cada mesa deve ter uma chave primária.
- O servidor de origem deve utilizar o motor InnoDB.
- O utilizador deve ter permissões para configurar a sessão de registo binário e criar novos utilizadores no servidor de origem.
- Se o servidor de origem tiver SSL ativado, certifique-se de que o certificado SSL CA fornecido para o domínio foi incluído no `mariadb.az_replication_change_master` procedimento armazenado. Consulte os [seguintes exemplos](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication) e o `master_ssl_ca` parâmetro.
- Certifique-se de que o endereço IP do servidor de origem foi adicionado à Base de Dados Azure para as regras de firewall do servidor de réplicas MariaDB. Atualize as regras de firewall com o [portal do Azure](howto-manage-firewall-portal.md) ou a [CLI do Azure](howto-manage-firewall-cli.md).
- Certifique-se de que a máquina que hospeda o servidor de origem permite o tráfego de entrada e saída na porta 3306.
- Certifique-se de que o servidor de origem tem um **endereço IP público,** o DNS é acessível ao público ou tem um nome de domínio totalmente qualificado (FQDN).

### <a name="other"></a>Outro
- A replicação de dados só é suportada nos níveis de preços otimizados para fins gerais e memória.

## <a name="next-steps"></a>Passos seguintes
- Saiba como configurar a [replicação de dados.](howto-data-in-replication.md)
