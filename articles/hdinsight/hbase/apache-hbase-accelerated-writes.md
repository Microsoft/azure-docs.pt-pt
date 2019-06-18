---
title: O Azure HDInsight acelerada escritas para o Apache HBase
description: Proporcione uma descrição geral da funcionalidade do Azure HDInsight acelerada escreve, que utiliza discos geridos premium para melhorar o desempenho do Apache HBase escrever em frente Log.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 219899c2e336f544ff6572589cc79f84f555490d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233848"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>O Azure HDInsight acelerada escritas para o Apache HBase

Este artigo fornece em segundo plano sobre o **acelerada escreve** funcionalidade para o Apache HBase no HDInsight do Azure e como ele pode ser usado com eficiência para melhorar o desempenho de escrita. **Accelerated escritas** usa [discos geridos premium do Azure SSD](../../virtual-machines/linux/disks-types.md#premium-ssd) para melhorar o desempenho do Apache HBase escrever em frente Log (WAL). Para saber mais sobre o Apache HBase, consulte [o que é o Apache HBase no HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Visão geral da arquitetura de HBase

No HBase, uma **linha** consiste em um ou mais **colunas** e é identificado por uma **chave de linha**. Várias linhas constituem uma **tabela**. Colunas contêm **células**, que são versões de timestamped do valor nessa coluna. Colunas são agrupadas em **famílias de colunas**, e todas as colunas na família de colunas são armazenadas em ficheiros de armazenamento chamados **HFiles**.

**Regiões** no HBase são usados para balancear a carga de processamento de dados. HBase primeiro armazena as linhas de uma tabela numa única região. As linhas são distribuídas por várias regiões como a quantidade de dados nos aumentos de tabela. **Servidores de região** pode processar pedidos de várias regiões.

## <a name="write-ahead-log-for-apache-hbase"></a>Registo de escrita prévia para o Apache HBase

HBase guarda primeiro a atualizações de dados a um tipo de registo de consolidação chamado um escrever em frente Log (WAL). Após a atualização é armazenada no WAL, ele é escrito para na memória **MemStore**. Quando os dados na memória atingem a capacidade máxima, escrever no disco como um **HFile**.

Se um **RegionServer** falhar ou ficar indisponível antes do MemStore é descarregado, o registo de escrita em frente pode ser utilizado para reproduzir as atualizações. Sem WAL, se um **RegionServer** falhar antes de liberar atualizações a um **HFile**, todas essas atualizações são perdidas.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Acelerado de escritas de recurso no Azure HDInsight para o Apache HBase

A funcionalidade acelerada escreve resolve o problema de maior latências de escrita pelo uso de escrever em frente registos que estão no armazenamento na cloud.  A funcionalidade acelerada escreve para o Apache HBase do HDInsight clusters, anexa SSD-managed disks premium para cada RegionServer (nó de trabalho). Escreva que registos em frente, em seguida, são escritos para o Hadoop File System (HDFS) montado nestes premium-discos geridos, em vez de armazenamento na cloud.  Premium-discos geridos utilizam discos de Solid-State (SSDs) e oferecem excelente desempenho de e/s com tolerância a falhas.  Ao contrário dos discos não geridos, se uma unidade de armazenamento ficar inativo, não vai afetar outras unidades de armazenamento no mesmo conjunto de disponibilidade.  Como resultado, os discos geridos fornecem escrita-latência baixa e uma melhor resiliência para as suas aplicações. Para saber mais sobre os discos geridos pelo Azure, veja [discos geridos de introdução ao Azure](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Como ativar o Accelerated escreve para o HBase no HDInsight

Para criar um novo cluster do HBase com o recurso acelerada escreve, siga os passos em [configurar clusters no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) até chegar **passo 3, o armazenamento**. Sob **definições de Metastore**, clique na caixa de verificação junto a **ativar acelerada escreve (pré-visualização)** . Em seguida, continue com os restantes passos para criação do cluster.

![Ativar a opção de acelerado escritas para o Apache HBase do HDInsight](./media/apache-hbase-accelerated-writes/accelerated-writes-cluster-creation.png)

## <a name="other-considerations"></a>Outras considerações

Para preservar a durabilidade dos dados, crie um cluster com um mínimo de três nós de trabalho. Depois de criado, não pode reduzir verticalmente o cluster para menos de três nós de trabalho.

Remoção da cache ou desativar as suas tabelas de HBase antes de eliminar o cluster, para que não perca dados de escrever o registo em frente.

```
flush 'mytable'
```

```
disable 'mytable'
```

## <a name="next-steps"></a>Passos Seguintes

* Documentação oficial do Apache HBase no [funcionalidade de escrever o registo em frente](https://hbase.apache.org/book.html#wal)
* Para atualizar o cluster de HDInsight Apache HBase para utilizar o Accelerated escreve, consulte [migrar um cluster do Apache HBase para uma nova versão](apache-hbase-migrate-new-version.md).
