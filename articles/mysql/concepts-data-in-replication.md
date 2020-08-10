---
title: Replicação de dados - Base de Dados Azure para o MySQL
description: Saiba como utilizar a replicação de dados para sincronizar a partir de um servidor externo na Base de Dados Azure para o serviço MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 8/7/2020
ms.openlocfilehash: a9d6c1b2438f20a06062842b96b147e094760238
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031222"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replicar dados na Base de Dados Azure para o MySQL

A replicação de dados permite sincronizar dados de um servidor MySQL externo na Base de Dados Azure para o serviço MySQL. O servidor externo pode estar no local, em máquinas virtuais ou num serviço de base de dados hospedado por outros fornecedores de nuvem. A Replicação de Dados de Entrada é baseada na replicação baseada na posição dos ficheiros de registo binário (binlog) nativo para MySQL. Para saber mais sobre a replicação do binlog, consulte a visão geral da [replicação do binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Quando utilizar a replicação de dados
Os principais cenários a considerar usando a replicação de dados são:

- **Sincronização de dados híbridos:** Com a replicação de dados, pode manter os dados sincronizados entre os servidores no local e a Base de Dados Azure para o MySQL. Esta sincronização é útil para criar aplicações híbridas. Este método é apelativo quando se tem um servidor de base de dados local existente, mas pretende mover os dados para uma região mais próxima dos utilizadores finais.
- **Sincronização multi-nuvem:** Para soluções complexas em nuvem, utilize a Replicação de Dados para sincronizar dados entre a Base de Dados Azure para o MySQL e diferentes fornecedores de nuvem, incluindo máquinas virtuais e serviços de base de dados alojados nessas nuvens.
 
Para cenários de migração, utilize o [Serviço de Migração de Bases de Dados Azure](https://azure.microsoft.com/services/database-migration/)(DMS).

## <a name="limitations-and-considerations"></a>Limitações e considerações

### <a name="data-not-replicated"></a>Dados não replicados
A base de dados do [*sistema mysql*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) no servidor principal não é replicada. As alterações nas contas e permissões no servidor principal não são replicadas. Se criar uma conta no servidor principal e esta conta precisar de aceder ao servidor de réplica, crie manualmente a mesma conta no lado do servidor de réplica. Para entender quais as tabelas contidas na base de dados do sistema, consulte o [manual MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="filtering"></a>Filtragem
Para saltar as tabelas de replicação do seu servidor principal (hospedados no local, em máquinas virtuais ou num serviço de base de dados hospedado por outros fornecedores de nuvem), o `replicate_wild_ignore_table` parâmetro é suportado. Opcionalmente, atualize este parâmetro no servidor de réplica alojado no Azure utilizando o [portal Azure](howto-server-parameters.md) ou [O Azure CLI](howto-configure-server-parameters-using-cli.md).

Reveja a documentação do [MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) para saber mais sobre este parâmetro.

### <a name="requirements"></a>Requirements
- A versão principal do servidor deve ser pelo menos a versão 5.6 do MySQL. 
- As versões do servidor principal e réplica devem ser as mesmas. Por exemplo, ambos devem ser a versão 5.6 do MySQL ou ambas devem ser a versão 5.7 do MySQL.
- Cada mesa deve ter uma chave primária.
- O servidor master deve utilizar o motor MySQL InnoDB.
- O utilizador deve ter permissões para configurar a madeira binária e criar novos utilizadores no servidor principal.
- Se o servidor principal tiver SSL ativado, certifique-se de que o certificado SSL CA fornecido para o domínio foi incluído no `mysql.az_replication_change_master` procedimento armazenado. Consulte os [seguintes exemplos](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) e o `master_ssl_ca` parâmetro.
- Confirme se o endereço IP do servidor mestre foi adicionado às regras de firewall do servidor de réplica da Base de Dados do Azure para MySQL. Atualize as regras de firewall com o [portal do Azure](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) ou a [CLI do Azure](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli).
- Verifique se o computador que aloja o servidor mestre permite tráfego de entrada e de saída na porta 3306.
- Certifique-se de que o servidor principal tem um **endereço IP público,** o DNS é acessível ao público ou tem um nome de domínio totalmente qualificado (FQDN).

### <a name="other"></a>Outros
- A replicação de dados só é suportada nos níveis de preços otimizados para fins gerais e memória.
- Os identificadores globais de transações (GTID) não são suportados.

## <a name="next-steps"></a>Passos seguintes
- Saiba como configurar a [replicação de dados](howto-data-in-replication.md)
- Saiba mais [sobre a replicação em Azure com réplicas de leitura](concepts-read-replicas.md)
- Saiba como [migrar dados com tempo mínimo de inatividade usando DMS](howto-migrate-online.md)