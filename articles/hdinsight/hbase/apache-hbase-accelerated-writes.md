---
title: Escritas Aceleradas do Azure HDInsight para o Apache HBase
description: Dá uma visão geral da funcionalidade Azure HDInsight Accelerated Writes, que utiliza discos geridos premium para melhorar o desempenho do Apache HBase Write Ahead Log.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 7165bab96d037f6782bc9aa6767cadd9b35f058c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764595"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Escritas Aceleradas do Azure HDInsight para o Apache HBase

Este artigo fornece antecedentes na funcionalidade **Accelerated Writes** para Apache HBase em Azure HDInsight, e como pode ser usado eficazmente para melhorar o desempenho da escrita. **A Accelerated Writes** utiliza [discos SSD premium Azure](../../virtual-machines/linux/disks-types.md#premium-ssd) para melhorar o desempenho do Apache HBase Write Ahead Log (WAL). Para saber mais sobre apache HBase, veja [o que é Apache HBase em HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Visão geral da arquitetura HBase

Em HBase, uma **linha** é constituída por uma ou mais **colunas** e é identificada por uma tecla de **linha**. Várias filas compõem uma **mesa.** As colunas contêm **células,** que são versões estampadas no tempo do valor naquela coluna. As colunas são agrunadas em famílias de **colunas**, e todas as colunas de uma família de colunas são armazenadas juntas em ficheiros de armazenamento chamados **HFiles**.

**As regiões** da Base H são utilizadas para equilibrar a carga de processamento de dados. A HBase armazena primeiro as filas de uma mesa numa única região. As linhas são espalhadas por várias regiões à medida que a quantidade de dados na tabela aumenta. **Os Servidores da Região** podem lidar com pedidos para várias regiões.

## <a name="write-ahead-log-for-apache-hbase"></a>Escreva log à frente para Apache HBase

A HBase escreve primeiro atualizações de dados para um tipo de registo de compromisso chamado Write Ahead Log (WAL). Depois de a atualização ser armazenada no WAL, está escrita para a **MemStore**em memória . Quando os dados na memória atingem a sua capacidade máxima, é escrito para disco como um **HFile**.

Se um **RegionServer** falhar ou ficar indisponível antes de o MemStore ser descarregado, o Registo Write Ahead pode ser usado para reproduzir atualizações. Sem o WAL, se um **RegionServer** falhar antes de descarregar atualizações para um **HFile,** todas essas atualizações estão perdidas.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Funcionalidade de Escritas Aceleradas no Azure HDInsight para Apache HBase

A funcionalidade Accelerated Writes resolve o problema das mais elevadas tardios de escrita causadas pela utilização de Registos Write Ahead que se encontram no armazenamento em nuvem.  A função Accelerated Writes para clusters HDInsight Apache HBase, anexa discos geridos por SSD premium a cada RegiãoServer (nó de trabalhador). Write Ahead Logs são então escritos para o Sistema de Ficheiros Hadoop (HDFS) montado nestes discos geridos premium em vez de armazenamento em nuvem.  Os discos geridos premium utilizam discos sólidos (SSDs) e oferecem excelente desempenho em I/S com tolerância a falhas.  Ao contrário dos discos não geridos, se uma unidade de armazenamento cair, não afetará outras unidades de armazenamento no mesmo conjunto de disponibilidade.  Como resultado, os discos geridos proporcionam baixa latência escrita e melhor resiliência para as suas aplicações. Para saber mais sobre discos geridos pelo Azure, consulte introdução aos discos geridos pelo [Azure.](../../virtual-machines/windows/managed-disks-overview.md)

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Como ativar writes acelerados para HBase no HDInsight

Para criar um novo cluster HBase com a funcionalidade Accelerated Writes, siga os passos em [conjunto em clusters em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) até chegar ao **Passo 3, Armazenamento**. Em **definições de Metastore,** selecione a caixa de verificação ao lado do **Enable HBase acelerada.** Em seguida, continue com os passos restantes para a criação de cluster.

![Ativar a opção de escrita acelerada para HDInsight Apache HBase](./media/apache-hbase-accelerated-writes/azure-portal-cluster-storage-hbase.png)

## <a name="other-considerations"></a>Outras considerações

Para preservar a durabilidade dos dados, crie um cluster com um mínimo de três nós de trabalhador. Uma vez criado, não se pode reduzir o aglomerado para menos de três nós operários.

Lave ou desative as tabelas HBase antes de eliminar o cluster, para que não perca os dados do Registo De Gravação.

```
flush 'mytable'
```

```
disable 'mytable'
```

Siga passos semelhantes ao escalonar o seu cluster: descarregue as mesas e desative as tabelas para impedir que os dados de entrada. Não podes reduzir o teu aglomerado para menos de três nós.

Seguir estes passos garantirá uma redução de escala bem sucedida e evitará a possibilidade de um nó de nome entrar em modo de segurança devido a ficheiros sub-replicados ou temporários.

Se o seu nó de nome entrar em modo de segurança após uma descida de escala, utilize comandos hdfs para replicar os blocos sub-replicados e tirar hdfs do modo de segurança. Esta re-replicação permitir-lhe-á reiniciar a HBase com sucesso.

## <a name="next-steps"></a>Passos seguintes

* Documentação oficial do Apache HBase na [funcionalidade Write Ahead Log](https://hbase.apache.org/book.html#wal)
* Para atualizar o seu cluster HDInsight Apache HBase para utilizar Writes Acelerados, consulte [Migrate um cluster Apache HBase para uma nova versão](apache-hbase-migrate-new-version.md).
