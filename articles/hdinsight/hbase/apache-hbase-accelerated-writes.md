---
title: Gravações aceleradas do Azure HDInsight para o Apache HBase
description: Fornece uma visão geral do recurso de gravações aceleradas do Azure HDInsight, que usa o Premium Managed disks para melhorar o desempenho do log write ahead do Apache HBase.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: bcc9736280b144a77bca57b4f4df1303f4b54796
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179086"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Gravações aceleradas do Azure HDInsight para o Apache HBase

Este artigo fornece informações sobre o recurso de **gravações aceleradas** para o Apache HBase no Azure HDInsight e como ele pode ser usado com eficiência para melhorar o desempenho de gravação. As **gravações aceleradas** usam o [Managed disks SSD do Azure Premium](../../virtual-machines/linux/disks-types.md#premium-ssd) para melhorar o desempenho do log write ahead do Apache HBase (WAL). Para saber mais sobre o Apache HBase, confira [o que é o Apache HBase no HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Visão geral da arquitetura do HBase

No HBase, uma **linha** consiste em uma ou mais **colunas** e é identificada por uma **chave de linha**. Várias linhas compõem uma **tabela**. As colunas contêm **células**, que são versões com carimbo de data/hora do valor nessa coluna. As colunas são agrupadas em **famílias de colunas**e todas as colunas em uma família de colunas são armazenadas juntas em arquivos de armazenamento chamados **HFiles**.

As **regiões** no HBase são usadas para balancear a carga de processamento de dados. O HBase armazena primeiro as linhas de uma tabela em uma única região. As linhas são distribuídas entre várias regiões à medida que a quantidade de dados na tabela aumenta. Os **servidores de região** podem lidar com solicitações para várias regiões.

## <a name="write-ahead-log-for-apache-hbase"></a>Log write-ahead para o Apache HBase

O HBase primeiro grava as atualizações de dados em um tipo de log de confirmação chamado log write ahead (WAL). Depois que a atualização é armazenada no WAL, ela é gravada no **MemStore**na memória. Quando os dados na memória atingem sua capacidade máxima, eles são gravados em disco como um **HFile**.

Se um **RegionServer** falhar ou ficar indisponível antes de o MemStore ser liberado, o log write-ahead poderá ser usado para reproduzir atualizações. Sem o WAL, se um **RegionServer** falhar antes de liberar atualizações para um **HFile**, todas essas atualizações serão perdidas.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Recurso de gravações aceleradas no Azure HDInsight para Apache HBase

O recurso de gravações aceleradas resolve o problema de latências de gravação mais altas causadas pelo uso de logs write-ahead que estão no armazenamento em nuvem.  O recurso de gravações aceleradas para clusters do Apache HBase do HDInsight, anexa discos gerenciados por SSD Premium a cada RegionServer (nó de trabalho). Os logs write-ahead são gravados no sistema de arquivos do Hadoop (HDFS) montado nesses discos gerenciados Premium em vez do armazenamento em nuvem.  Os discos gerenciados Premium usam SSDs (discos de estado sólido) e oferecem excelente desempenho de e/s com tolerância a falhas.  Diferentemente dos discos não gerenciados, se uma unidade de armazenamento ficar inativa, ela não afetará outras unidades de armazenamento no mesmo conjunto de disponibilidade.  Como resultado, os discos gerenciados fornecem baixa latência de gravação e melhor resiliência para seus aplicativos. Para saber mais sobre os discos gerenciados do Azure, confira [introdução aos Azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Como habilitar gravações aceleradas para o HBase no HDInsight

Para criar um novo cluster HBase com o recurso de gravações aceleradas, siga as etapas em [Configurar clusters no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) até chegar à **etapa 3, armazenamento**. Em **configurações do metastore**, clique na caixa de seleção ao lado de **habilitar gravações aceleradas (versão prévia)** . Em seguida, continue com as etapas restantes para a criação do cluster.

![Habilitar a opção de gravações aceleradas para o Apache HBase do HDInsight](./media/apache-hbase-accelerated-writes/accelerated-writes-cluster-creation.png)

## <a name="other-considerations"></a>Outras considerações

Para preservar a durabilidade dos dados, crie um cluster com um mínimo de três nós de trabalho. Depois de criado, você não pode reduzir o cluster para menos de três nós de trabalho.

Libere ou desabilite as tabelas do HBase antes de excluir o cluster, para que você não perca os dados de log de gravação antecipada.

```
flush 'mytable'
```

```
disable 'mytable'
```

Siga etapas semelhantes ao reduzir verticalmente o cluster: Libere as tabelas e desabilite as tabelas para interromper os dados de entrada. Não é possível reduzir verticalmente o cluster para menos de três nós.

Seguir essas etapas garantirá uma redução bem-sucedida da escala e evitará a possibilidade de um namenode entrar no modo de segurança devido a arquivos temporários ou replicados.

Se o seu namenode entrar em modo de segurança após uma redução para baixo, use os comandos do HDFS para replicar novamente os blocos em replicação e obtenha o HDFS fora do modo seguro. Essa rereplicação permitirá que você reinicie o HBase com êxito.

## <a name="next-steps"></a>Passos seguintes

* Documentação oficial do Apache HBase no [recurso log de write-ahead](https://hbase.apache.org/book.html#wal)
* Para atualizar o cluster do Apache HBase do HDInsight para usar gravações aceleradas, consulte [migrar um cluster do Apache HBase para uma nova versão](apache-hbase-migrate-new-version.md).
