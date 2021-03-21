---
title: Afinação de desempenho - Colmeia no Azure Data Lake Storage Gen1
description: Saiba mais sobre afinação de desempenho para a Hive em HdInsight e Azure Data Lake Storage Gen1. Para consultas intensivas de I/O, afina a Hive para obter um melhor desempenho.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 082d684ed0a29cb6bf2de9c506886b6d98cf174a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97723786"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Orientação de afinação de desempenho para a Colmeia em HDInsight e Azure Data Lake Storage Gen1

As definições predefinição foram definidas para proporcionar um bom desempenho em muitos casos de utilização diferentes.  Para consultas intensivas de I/O, a Hive pode ser sintonizada para obter um melhor desempenho com a Azure Data Lake Storage Gen1.  

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta gen1 de armazenamento de data lake.** Para instruções sobre como criar um, consulte [Começar com a Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** com acesso a uma conta Gen1 de armazenamento de data lake. Consulte [Criar um cluster HDInsight com data lake storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que ativa o Ambiente de Trabalho Remoto para o cluster.
* **A executar colmeia em HDInsight**.  Para aprender sobre como gerir os trabalhos de Colmeia em HDInsight, consulte [Use Hive on HDInsight](../hdinsight/hadoop/hdinsight-use-hive.md)
* **Diretrizes de afinação de desempenho sobre data lake storage gen1**.  Para conceitos de desempenho geral, consulte [data lake storage Gen1 Performance Afinação de Afinação](./data-lake-store-performance-tuning-guidance.md)

## <a name="parameters"></a>Parâmetros

Aqui estão as configurações mais importantes para sintonizar para melhorar o desempenho da Data Lake Storage Gen1:

* **hive.tez.container.size** – a quantidade de memória utilizada por cada tarefa

* **tez.grouping.min-size** – tamanho mínimo de cada mapper

* **tez.grouping.max-size** – tamanho máximo de cada mapper

* **hive.exec.reducer.bytes.per.reducer** – tamanho de cada redutor

**hive.tez.container.size** - O tamanho do recipiente determina a quantidade de memória disponível para cada tarefa.  Esta é a principal entrada para controlar a concordância na Colmeia.  

**tez.grouping.min-size** – Este parâmetro permite definir o tamanho mínimo de cada mapper.  Se o número de mappers que tez escolhe for menor do que o valor deste parâmetro, então tez usará o valor definido aqui.

**tez.grouping.max-size** – O parâmetro permite definir o tamanho máximo de cada mapper.  Se o número de mappers que tez escolhe for maior do que o valor deste parâmetro, então tez usará o valor definido aqui.

**hive.exec.reducer.bytes.per.redutor** – Este parâmetro define o tamanho de cada redutor.  Por padrão, cada redutor é de 256MB.  

## <a name="guidance"></a>Orientação

**Desaprova hive.exec.reducer.bytes.per.reducer** – O valor predefinido funciona bem quando os dados não são reprimidos.  Para os dados que são comprimidos, deve reduzir o tamanho do redutor.  

**Definir hive.tez.container.size** - Em cada nó, a memória é especificada por yarn.nodemanager.resource.memory-mb e deve ser corretamente definida no cluster HDI por predefinição.  Para obter informações adicionais sobre a definição da memória apropriada em YARN, consulte este [post](../hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom.md).

As cargas de trabalho intensivas de I/O podem beneficiar de um maior paralelismo diminuindo o tamanho do recipiente Tez. Isto dá ao utilizador mais recipientes que aumentam a concordância.  No entanto, algumas consultas de Colmeia requerem uma quantidade significativa de memória (por exemplo, MapJoin).  Se a tarefa não tiver memória suficiente, obterá uma exceção da memória durante o tempo de funcionação.  Se receber exceções de memória, então deve aumentar a memória.   

O número simultâneo de tarefas em execução ou paralelismo será limitado pela memória total do YARN.  O número de contentores DE FIOS ditará quantas tarefas simultâneas podem ser executadas.  Para encontrar a memória yarn por nó, você pode ir a Ambari.  Navegue até YARN e veja o separador Configs.  A memória YARN é apresentada nesta janela.  

> Total da memória yarn = nós * memória YARN por nó Número de contentores YARN = Total memória YARN / Tamanho do recipiente Tez

A chave para melhorar o desempenho usando a Data Lake Storage Gen1 é aumentar a conúnquidade o mais possível.  O Tez calcula automaticamente o número de tarefas que devem ser criadas para que não seja necessário defini-lo.   

## <a name="example-calculation"></a>Cálculo de exemplo

Digamos que tem um aglomerado de 8 nó D14.  

> Total da memória YARN = nós * memória YARN por nó Memória TOTAL YARN = 8 nós * 96GB = Número de 768GB de contentores DE FIOS = 768GB / 3072MB = 256

## <a name="limitations"></a>Limitações

**Estrangulamento da Gen1 de armazenamento de dados** 

Se atingir os limites de largura de banda fornecidos pela Data Lake Storage Gen1, começará a ver falhas de tarefa. Isto pode ser identificado observando erros de estrangulamento em registos de tarefas.  Pode diminuir o paralelismo aumentando o tamanho do recipiente Tez.  Se precisar de mais concordância para o seu trabalho, contacte-nos.

Para verificar se está a ser estrangulado, tem de ativar o registo de depuragem do lado do cliente. Eis como podes fazer isso:

1. Coloque a seguinte propriedade nas propriedades log4j em Hive config. Isto pode ser feito a partir da vista de Ambari: log4j.logger.com.microsoft.azure.datalake.store=DEBUG Reinicie todos os nós/serviço para que o config entre em vigor.

2. Se estiver a ser estrangulado, verá o código de erro HTTP 429 no ficheiro de registo da colmeia. O ficheiro de registo da colmeia está em /tmp/ &lt; utilizador &gt; /colmeia.log

## <a name="further-information-on-hive-tuning"></a>Mais informações sobre a afinação da Colmeia

Aqui estão alguns blogs que ajudarão a afinar as suas consultas de Hive:
* [Otimizar consultas de Colmeia para Hadoop em HDInsight](../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [Codificação do ficheiro de consulta de Colmeia em Azure HDInsight](/archive/blogs/bigdatasupport/encoding-the-hive-query-file-in-azure-hdinsight)
* [Ignite talk on otimize Hive on HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)