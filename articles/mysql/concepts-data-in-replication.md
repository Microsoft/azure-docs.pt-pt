---
title: Replicação de dados - Base de Dados Azure para MySQL
description: Saiba usar a replicação de data-in para sincronizar a partir de um servidor externo para a Base de Dados Azure para o serviço MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 20be34191355e6ade40e0f3b218818bfa5345a28
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79533237"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replicar dados na Base de Dados Azure para mySQL

A Replicação de Dados permite sincronizar dados de um servidor MySQL externo para a Base de Dados Azure para o serviço MySQL. O servidor externo pode estar no local, em máquinas virtuais, ou num serviço de base de dados hospedado por outros fornecedores de nuvem. A Replicação de Dados de Entrada é baseada na replicação baseada na posição dos ficheiros de registo binário (binlog) nativo para MySQL. Para saber mais sobre a replicação do binlog, consulte a visão geral da replicação do [binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Quando utilizar a Replicação data-in
Os principais cenários a considerar na utilização da Replicação data-in são:

- **Sincronização de dados híbridos:** Com a Replicação data-in, pode manter os dados sincronizados entre os seus servidores no local e a Base de Dados Azure para o MySQL. Esta sincronização é útil para a criação de aplicações híbridas. Este método é apelativo quando se tem um servidor de base de dados local existente, mas pretende mover os dados para uma região mais próxima dos utilizadores finais.
- **Sincronização multi-nuvem:** Para soluções complexas em nuvem, utilize a Replicação data-in para sincronizar dados entre a Base de Dados Azure para o MySQL e diferentes fornecedores de nuvem, incluindo máquinas virtuais e serviços de base de dados alojados nessas nuvens.
 
Para cenários de migração, utilize o Serviço de Migração de Bases de [Dados Azure](https://azure.microsoft.com/services/database-migration/)(DMS).

## <a name="limitations-and-considerations"></a>Limitações e considerações

### <a name="data-not-replicated"></a>Dados não replicados
A base de dados do [*sistema Mysql*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) no servidor principal não é replicada. As alterações nas contas e permissões no servidor principal não são replicadas. Se criar uma conta no servidor principal e esta conta precisar de aceder ao servidor de réplicas, crie manualmente a mesma conta no lado do servidor de réplicas. Para entender quais as tabelas contidas na base de dados do sistema, consulte o [manual MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="requirements"></a>Requisitos
- A versão master server deve ser pelo menos a versão MySQL 5.6. 
- As versões master e réplica do servidor devem ser as mesmas. Por exemplo, ambos devem ser a versão MySQL 5.6 ou ambos devem ser a versão MySQL 5.7.
- Cada mesa deve ter uma chave primária.
- O servidor principal deve utilizar o motor MySQL InnoDB.
- O utilizador deve ter permissões para configurar o registo binário e criar novos utilizadores no servidor principal.
- Se o servidor principal tiver ativado o SSL, certifique-se de que `mysql.az_replication_change_master` o certificado SSL CA fornecido para o domínio foi incluído no procedimento armazenado. Consulte os [seguintes exemplos](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) e o `master_ssl_ca` parâmetro.
- Confirme se o endereço IP do servidor mestre foi adicionado às regras de firewall do servidor de réplica da Base de Dados do Azure para MySQL. Atualize as regras de firewall com o [portal do Azure](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) ou a [CLI do Azure](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli).
- Verifique se o computador que aloja o servidor mestre permite tráfego de entrada e de saída na porta 3306.
- Certifique-se de que o servidor principal tem um **endereço IP público,** o DNS é acessível ao público ou tem um nome de domínio totalmente qualificado (FQDN).

### <a name="other"></a>Outros
- A replicação de data-in só é suportada nos níveis de preços otimizados para fins gerais e memória.
- Os identificadores globais de transações (GTID) não são suportados.

## <a name="next-steps"></a>Passos seguintes
- Saiba como configurar a [replicação de dados](howto-data-in-replication.md)
- Saiba replicar [em Azure com réplicas de leitura](concepts-read-replicas.md)
- Saiba como migrar dados com o mínimo de [tempo de inatividade usando DMS](howto-migrate-online.md)