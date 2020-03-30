---
title: Copiar dados de e para o WASB em Azure Data Lake Storage Gen1 utilizando distCp
description: Utilize a ferramenta DistCp para copiar dados de e para as bolhas de armazenamento azure para o Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 455e73ece2d46a508b3077c13c8106fe53beb4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638838"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Utilize o DistCp para copiar dados entre as bolhas de armazenamento de Azure e o Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Utilizar o DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Utilizar o AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Se tiver um cluster HDInsight com acesso ao Azure Data Lake Storage Gen1, pode utilizar ferramentas do ecossistema Hadoop como o DistCp para copiar dados de e para um armazenamento de cluster HDInsight (WASB) numa conta gen1 de armazenamento de data Lake. Este artigo mostra como usar a ferramenta DistCp.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Uma conta De Armazenamento de **Lago Azure Gen1.** Para obter instruções sobre como criar um, consulte Iniciar com [Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Cluster Azure HDInsight** com acesso a uma conta Gen1 de Armazenamento de Data Lake. Consulte [Criar um cluster HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que ativa o Ambiente de Trabalho Remoto para o cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Utilize distCp de um cluster HDInsight Linux

Um cluster HDInsight vem com a ferramenta DistCp, que pode ser usada para copiar dados de diferentes fontes para um cluster HDInsight. Se configurar o cluster HDInsight para utilizar data Lake Storage Gen1 como armazenamento adicional, pode usar o DistCp fora da caixa para copiar dados de e para uma conta data Lake Storage Gen1. Nesta secção, olhamos para a forma de utilizar a ferramenta DistCp.

1. A partir do seu ambiente de trabalho, utilize o SSH para se ligar ao cluster. Consulte [a Ligação a um cluster HDInsight baseado em Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Executar os comandos a partir do ssh rápido.

1. Verifique se pode aceder às bolhas de armazenamento azure (WASB). Execute o seguinte comando:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   A saída fornece uma lista de conteúdos na bolha de armazenamento.

1. Da mesma forma, verifique se pode aceder à conta Data Lake Storage Gen1 a partir do cluster. Execute o seguinte comando:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    A saída fornece uma lista de ficheiros e pastas na conta Data Lake Storage Gen1.

1. Utilize o DistCp para copiar dados do WASB para uma conta Gen1 de Armazenamento de Data Lake.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    O comando copia o conteúdo da **pasta /exemplo/data/gutenberg/** em WASB para **/myfolder** na conta Data Lake Storage Gen1.

1. Da mesma forma, utilize o DistCp para copiar dados de uma conta Gen1 de Armazenamento de Data Lake para WASB.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    O comando copia o conteúdo de **/myfolder** na conta Data Lake Storage Gen1 para **/exemplo/data/gutenberg/** pasta em WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerações de desempenho ao utilizar o DistCp

Como a granularidade mais baixa da ferramenta DistCp é um único ficheiro, definir o número máximo de cópias simultâneas é o parâmetro mais importante para otimizá-lo contra data Lake Storage Gen1. Pode controlar o número de cópias simultâneas, definindo o número de parâmetros ('m') na linha de comando. Este parâmetro especifica o número máximo de mapeadores que são usados para copiar dados. O valor padrão é de 20.

Exemplo:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>Como determinar o número de mappers a utilizar

Eis algumas orientações que poderá utilizar.

* **Passo 1: Determinar** a memória total do ARN - O primeiro passo é determinar a memória YARN disponível para o cluster onde executa o trabalho distCp. Esta informação está disponível no portal Ambari associado ao cluster. Navegue para o YARN e veja o separador **Configs** para ver a memória yARN. Para obter a memória total do ARN, multiplique a memória YARN por nó com o número de nós que tem no seu cluster.

* **Passo 2: Calcular o número de mappers** - O valor de **m** é igual ao quociente da memória total do ARN dividida pelo tamanho do recipiente YARN. A informação sobre o tamanho do contentor YARN também está disponível no portal Ambari. Navegue para o YARN e veja o separador **Configs.** O tamanho do recipiente YARN é apresentado nesta janela. A equação para chegar ao número de mappers **(m**) é:

   `m = (number of nodes * YARN memory for each node) / YARN container size`

Exemplo:

Vamos supor que tem quatro nós D14v2 no cluster e quer transferir 10 TB de dados de 10 pastas diferentes. Cada uma das pastas contém quantidades variadas de dados e os tamanhos dos ficheiros dentro de cada pasta são diferentes.

* Memória total do ARN - Do portal Ambari determina que a memória YARN é de 96 GB para um nó D14. Então, a memória total do ARN para quatro nós cluster é:

   `YARN memory = 4 * 96GB = 384GB`

* Número de mappers - Do portal Ambari determina que o tamanho do contentor YARN é 3072 para um nó de cluster D14. Então, o número de mappers é:

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

Se outras aplicações estiverem a utilizar a memória, pode optar por utilizar apenas uma parte da memória YARN do seu cluster para distCp.

### <a name="copying-large-datasets"></a>Copiar grandes conjuntos de dados

Quando o tamanho do conjunto de dados a mover for grande (por exemplo, > 1 TB) ou se tiver muitas pastas diferentes, considere utilizar vários trabalhos distCp. Provavelmente não há ganho de desempenho, mas espalha os empregos para que, se algum trabalho falhar, só precisa reiniciar esse trabalho específico em vez de todo o trabalho.

### <a name="limitations"></a>Limitações

* A DistCp tenta criar mappers semelhantes em tamanho para otimizar o desempenho. Aumentar o número de mappers pode nem sempre aumentar o desempenho.

* O DistCp está limitado a apenas um mapper por ficheiro. Portanto, não devias ter mais mappers do que ficheiros. Uma vez que o DistCp pode atribuir apenas um mapper a um ficheiro, isto limita a quantidade de moeda que pode ser usada para copiar ficheiros grandes.

* Se tiver um pequeno número de ficheiros grandes, divida-os em pedaços de ficheiro sumo de 256 MB para lhe dar mais potencial concurrency.

* Se estiver a copiar de uma conta de armazenamento do Azure Blob, o seu trabalho de cópia pode ser estrangulado no lado do armazenamento blob. Isto degrada o desempenho do teu trabalho de cópia. Para saber mais sobre os limites do armazenamento do Azure Blob, consulte os limites de armazenamento do Azure nos limites de subscrição e serviço do [Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="see-also"></a>Consulte também

* [Copiar dados das bolhas de armazenamento do Azure para data lake storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Use Azure Data Lake Analytics com Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
