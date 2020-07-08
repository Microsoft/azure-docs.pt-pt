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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76764595"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Escritas Aceleradas do Azure HDInsight para o Apache HBase

Este artigo fornece o fundo sobre a funcionalidade **Escritas Aceleradas** para Apache HBase em Azure HDInsight, e como pode ser usado eficazmente para melhorar o desempenho da escrita. **A Accelerated Writes** utiliza [discos geridos Azure premium SSD](../../virtual-machines/linux/disks-types.md#premium-ssd) para melhorar o desempenho do Apache HBase Write Ahead Log (WAL). Para saber mais sobre o Apache HBase, consulte [o que é Apache HBase em HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Visão geral da arquitetura HBase

Na Base H, uma **linha** é constituída por uma ou mais **colunas** e é identificada por uma **chave de linha**. Várias filas compõem uma **mesa.** As colunas contêm **células,** que são versões com tempo do valor nessa coluna. As colunas são agrupadas em **famílias de colunas,** e todas as colunas de uma coluna-família são armazenadas juntas em ficheiros de armazenamento **chamados HFiles**.

**As regiões** da Base H são utilizadas para equilibrar a carga de processamento de dados. A HBase armazena primeiro as filas de uma mesa numa única região. As linhas estão espalhadas por várias regiões à medida que a quantidade de dados na tabela aumenta. **Os Servidores da Região** podem lidar com pedidos para várias regiões.

## <a name="write-ahead-log-for-apache-hbase"></a>Escreva antecipadamente para Apache HBase

O HBase escreve pela primeira vez atualizações de dados para um tipo de registo de compromisso chamado Write Ahead Log (WAL). Depois de a atualização ser armazenada no WAL, é escrita para a **MemStore**in-memory . Quando os dados na memória atingem a sua capacidade máxima, é escrito para o disco como um **HFile**.

Se um **RegiãoServer** falhar ou ficar indisponível antes da MemStore ser lavada, o Write Ahead Log pode ser utilizado para reproduzir atualizações. Sem o WAL, se um **RegionalServer** se despenhar antes de descarregar atualizações para um **HFile,** todas essas atualizações são perdidas.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Funcionalidade de Escritas Aceleradas em Azure HDInsight para Apache HBase

A funcionalidade Writes Acelerada resolve o problema das mais altas latências de escrita causadas pela utilização de Write Ahead Logs que estão no armazenamento em nuvem.  A funcionalidade De Escritas Aceleradas para clusters HDInsight Apache HBase, anexa discos geridos por SSD premium a todos os RegionServer (nó de trabalhador). Os Registos Antecipados são então escritos para o Sistema de Ficheiros Hadoop (HDFS) montado nestes discos geridos premium em vez de armazenamento em nuvem.  Os discos geridos premium utilizam discos de estado sólido (SSDs) e oferecem um excelente desempenho de I/S com tolerância a falhas.  Ao contrário dos discos não geridos, se uma unidade de armazenamento cair, não afetará outras unidades de armazenamento no mesmo conjunto de disponibilidade.  Como resultado, os discos geridos proporcionam baixa latência de escrita e melhor resiliência para as suas aplicações. Para saber mais sobre discos geridos pelo Azure, consulte [discos geridos introdução ao Azure](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Como ativar writes acelerados para HBase em HDInsight

Para criar um novo cluster HBase com a função Escritas Aceleradas, siga os passos em [Configurar clusters em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) até chegar ao **Passo 3, Armazenamento**. Em **Configurações de Metastore**, selecione a caixa de verificação ao lado **de Ativar as escritas aceleradas HBase**. Em seguida, continue com os passos restantes para a criação de clusters.

![Ativar a opção de escrita acelerada para HDInsight Apache HBase](./media/apache-hbase-accelerated-writes/azure-portal-cluster-storage-hbase.png)

## <a name="other-considerations"></a>Outras considerações

Para preservar a durabilidade dos dados, crie um cluster com um mínimo de três nós operários. Uma vez criado, não se pode reduzir o aglomerado para menos de três nós operários.

Lave ou desative as tabelas HBase antes de eliminar o cluster, para que não perca os dados do Write Ahead Log.

```
flush 'mytable'
```

```
disable 'mytable'
```

Siga passos semelhantes ao escalonar o seu cluster: lave as tabelas e desative as tabelas para impedir a entrada de dados. Não se pode reduzir o seu aglomerado para menos de três nós.

Seguindo estes passos, garantirá uma escala bem sucedida e evitará a possibilidade de um nome entrar em modo de segurança devido a ficheiros sub-replicados ou temporários.

Se o seu nome entrar em modo de segurança após uma escala baixa, utilize comandos hdfs para re-replicar os blocos sub-replicados e tirar os HDFs do modo de segurança. Esta re-replicação permitir-lhe-á reiniciar o HBase com sucesso.

## <a name="next-steps"></a>Próximos passos

* Documentação oficial do Apache HBase na [funcionalidade Write Ahead Log](https://hbase.apache.org/book.html#wal)
* Para atualizar o seu cluster HDInsight Apache HBase para utilizar Writes Acelerados, consulte [migrar um cluster Apache HBase para uma nova versão](apache-hbase-migrate-new-version.md).
