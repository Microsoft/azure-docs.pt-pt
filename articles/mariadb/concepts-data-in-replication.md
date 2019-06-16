---
title: Replicar os dados na base de dados do Azure para MariaDB.
description: Este artigo descreve os dados na replicação da base de dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0a1ead1580f6764fec7d1d18daa38bf093f242f2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61364443"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replicar os dados na base de dados do Azure para MariaDB

Replicação de dados-in permite-lhe sincronizar dados de um servidor de MariaDB em execução no local, nas máquinas virtuais ou serviços de base de dados hospedados por outros fornecedores de cloud para a base de dados do Azure para MariaDB serviço. Replicação de dados baseia-se o registo binário (binlog) com base na posição replicação de ficheiros nativa para MariaDB. Para saber mais sobre a replicação de binlog, consulte a [descrição geral da replicação de binlog](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Quando utilizar a replicação de dados
Os principais cenários para considerar a utilização de replicação de dados são:

- **Sincronização de dados híbrida:** Com a replicação de dados, pode manter os dados sincronizados entre os servidores no local e a base de dados do Azure para MariaDB. Esta sincronização é útil para criar aplicações híbridas. Este método é atraente quando existir um servidor da base de dados local, mas quiser mover os dados para uma região mais perto dos utilizadores finais.
- **Sincronização de várias Cloud:** Para soluções de cloud complexas, utilize a replicação de dados para sincronizar dados entre a base de dados do Azure para MariaDB e fornecedores de cloud diferentes, incluindo máquinas virtuais e serviços de base de dados hospedados nessas nuvens.

## <a name="limitations-and-considerations"></a>Limitações e considerações

### <a name="data-not-replicated"></a>Dados replicados não
O [ *base de dados de sistema de mysql* ](https://mariadb.com/kb/en/library/the-mysql-database-tables/) no servidor principal não é replicado. Alterações às contas e permissões no servidor principal não são replicadas. Se criar uma conta no servidor principal e esta conta tem de aceder ao servidor de réplica, em seguida, crie manualmente a mesma conta no lado do servidor de réplica. Para compreender quais tabelas estão contidas na base de dados do sistema, consulte a [MariaDB documentação](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Requisitos
- A versão do servidor principal tem de ser, pelo menos, MariaDB versão 10.2.
- As versões de servidor mestre e de réplica tem de ser o mesmo. Por exemplo, ambos tem de ser MariaDB versão 10.2.
- Cada tabela tem de ter uma chave primária.
- Servidor mestre deve utilizar o motor InnoDB.
- Utilizador tem de ter permissões para configurar o registo binário e criar novos utilizadores no servidor principal.

### <a name="other"></a>Outros
- Replicação de dados é apenas suportado, em geral, objetivo e com a otimização de memória escalões de preço.
- Identificadores de transação global (GTID) não são suportadas.

## <a name="next-steps"></a>Passos Seguintes
- Saiba como [configurar a replicação de dados-in](howto-data-in-replication.md).
