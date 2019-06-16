---
title: Replicar os dados na base de dados do Azure para MySQL.
description: Este artigo descreve os dados na replicação da base de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: f91a6da9a305c6620e4e01ab7aa3c554374cb5d7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60996828"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replicar os dados na base de dados do Azure para MySQL

Replicação de dados-in permite-lhe sincronizar dados de um servidor externo do MySQL na base de dados do Azure para o serviço MySQL. O servidor externo pode estar no local, nas máquinas virtuais, ou um serviço de base de dados hospedado por outros fornecedores de cloud. Replicação de dados baseia-se o registo binário (binlog) com base na posição replicação de ficheiros nativa ao MySQL. Para saber mais sobre a replicação de binlog, consulte a [descrição geral de replicação do MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Quando utilizar a replicação de dados
Os principais cenários para considerar a utilização de replicação de dados são:

- **Sincronização de dados híbrida:** Com a replicação de dados, pode manter os dados sincronizados entre os servidores no local e a base de dados do Azure para MySQL. Esta sincronização é útil para criar aplicações híbridas. Este método é atraente quando tiver um servidor de banco de dados local existente, mas quiser mover os dados para uma região mais perto dos utilizadores finais.
- **Sincronização de várias Cloud:** Para soluções de cloud complexas, utilize a replicação de dados para sincronizar dados entre a base de dados do Azure para MySQL e fornecedores de cloud diferentes, incluindo máquinas virtuais e serviços de base de dados hospedados nessas nuvens.

## <a name="limitations-and-considerations"></a>Limitações e considerações

### <a name="data-not-replicated"></a>Dados replicados não
O [ *base de dados de sistema de mysql* ](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) no servidor principal não é replicado. Não são replicadas as alterações às contas e permissões no servidor principal. Se criar uma conta no servidor principal e esta conta tem de aceder ao servidor de réplica, crie manualmente a mesma conta no lado do servidor de réplica. Para compreender quais tabelas estão contidas na base de dados do sistema, consulte a [MySQL manual](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

### <a name="requirements"></a>Requisitos
- A versão do servidor principal tem de ser, pelo menos, versão 5.6 do MySQL. 
- As versões de servidor mestre e de réplica tem de ser o mesmo. Por exemplo, ambos têm de ser MySQL versão 5.6 ou ambos têm de ser MySQL versão 5.7.
- Cada tabela tem de ter uma chave primária.
- Servidor mestre deve utilizar o motor MySQL InnoDB.
- Utilizador tem de ter permissões para configurar o registo binário e criar novos utilizadores no servidor principal.

### <a name="other"></a>Outros
- Replicação de dados é apenas suportado, em geral, objetivo e com a otimização de memória escalões de preço.
- Identificadores de transação global (GTID) não são suportadas.

## <a name="next-steps"></a>Passos Seguintes
- Saiba como [configurar a replicação de dados-in](howto-data-in-replication.md)
- Saiba mais sobre [replicar no Azure com a leitura a réplicas](concepts-read-replicas.md)
