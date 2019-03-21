---
title: Copiar dados de e para WASB em Gen1 de armazenamento do Azure Data Lake, utilizar o Distcp | Documentos da Microsoft
description: Utilize a ferramenta de Distcp para copiar dados de e para Blobs de armazenamento do Azure para a geração 1 de armazenamento do Azure Data Lake
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 5c3f393278d3d51dd3a53cd19335cd242c15c6d8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58168712"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Utilizar o Distcp para copiar dados entre os Blobs de armazenamento do Azure e de geração 1 de armazenamento do Azure Data Lake
> [!div class="op_single_selector"]
> * [Utilizar o DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Utilizar o AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Se tiver um cluster do HDInsight com acesso à geração 1 de armazenamento do Azure Data Lake, pode utilizar ferramentas de ecossistema do Hadoop, como o Distcp para copiar dados **de e para** um armazenamento de cluster do HDInsight (WASB) para uma conta de geração 1 de armazenamento do Data Lake. Este artigo fornece instruções sobre como utilizam a ferramenta de Distcp.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de geração 1 de armazenamento do Azure Data Lake**. Para obter instruções sobre como criar um, consulte [introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster de HDInsight do Azure** com acesso a uma conta de geração 1 de armazenamento do Data Lake. Ver [criar um cluster do HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que ativar o ambiente de trabalho remoto para o cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Utilizar o Distcp a partir de um cluster Linux de HDInsight

Um cluster do HDInsight é fornecido com o utilitário de Distcp, que pode ser usado para copiar dados de origens diferentes para um cluster do HDInsight. Se tiver configurado o cluster do HDInsight para utilizar o Data Lake Storage Gen1 como armazenamento adicional, o utilitário de Distcp pode ser utilizado-de-pronta para copiar dados de e para uma conta de geração 1 de armazenamento do Data Lake também. Nesta secção, vamos ver como usar o utilitário de Distcp.

1. A partir do seu ambiente de trabalho, utilize SSH para ligar ao cluster. Ver [ligar a um cluster do HDInsight baseado em Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Execute os comandos da linha de comandos SSH.

2. Certifique-se de que se pode aceder a Blobs de armazenamento do Azure (WASB). Execute o seguinte comando:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    A saída deve fornecer uma lista de conteúdo no blob de armazenamento.

3. Da mesma forma, verifique se pode aceder a conta de geração 1 de armazenamento do Data Lake do cluster. Execute o seguinte comando:

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/

    A saída deve fornecer uma lista de ficheiros/pastas na conta de geração 1 de armazenamento do Data Lake.

4. Utilize o Distcp para copiar dados de WASB para uma conta de geração 1 de armazenamento do Data Lake.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder

    O comando copia o conteúdo dos **/exemplo/dados/gutenberg/** pasta no WASB para **/myfolder** na conta de geração 1 de armazenamento do Data Lake.

5. Da mesma forma, utilize o Distcp para copiar dados de conta de geração 1 de armazenamento do Data Lake para WASB.

        hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    O comando copia o conteúdo do **/myfolder** na conta de geração 1 de armazenamento do Data Lake para **/exemplo/dados/gutenberg/** pasta no WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerações sobre o desempenho ao utilizar o DistCp

Como a granularidade de mais baixa do DistCp é um único arquivo, definir o número máximo de cópias simultâneas é o parâmetro mais importante para otimizá-lo em relação a geração 1 de armazenamento do Data Lake. Número de cópias simultâneas é controlado ao definir o número de mapeadores (Estou ') parâmetro na linha de comandos. Este parâmetro especifica o número máximo de mapeadores que são utilizados para copiar dados. Valor predefinido é 20.

**Exemplo**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Como posso determinar o número de mapeadores utilizar?

Eis algumas orientações que poderá utilizar.

* **Passo 1: Determinar o total de memória YARN** -o primeiro passo é determinar a memória YARN disponível para o cluster em que executou a tarefa de DistCp. Essas informações estão disponíveis no portal do Ambari associado ao cluster. Navegue para o YARN e veja a guia de configurações para ver a memória YARN. Para obter a memória YARN total, multiplique a memória YARN por nó com o número de nós que tem no seu cluster.

* **Passo 2: Calcular o número de mapeadores** -o valor de **m** é igual ao quociente de memória YARN total dividido pelo tamanho do contentor YARN. As informações de tamanho de contentor do YARN estão disponíveis no portal do Ambari. Navegue para o YARN e ver a guia configurações. O tamanho de contentor do YARN é apresentado nesta janela. A equação a deparar-se com o número de mapeadores (**m**) é

        m = (number of nodes * YARN memory for each node) / YARN container size

**Exemplo**

Vamos supor que tem um 4 D14v2s nós do cluster e está a tentar transferir 10 TB de dados de 10 pastas diferentes. Cada uma das pastas contém diferentes quantidades de dados e os tamanhos de ficheiro em cada pasta são diferentes.

* Total de memória YARN - portal do Ambari, determinar se a memória YARN é 96 GB para um nó de D14. Por isso, é o total de memória YARN para cluster de quatro nós: 

        YARN memory = 4 * 96GB = 384GB

* Número de mapeadores - portal do Ambari, determinar que o tamanho de contentor do YARN é 3072 para um nó de cluster D14. Por isso, é o número de mapeadores:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Se outros aplicativos estiver usando a memória, em seguida, pode optar por utilizar apenas uma parte da memória YARN do seu cluster para DistCp.

### <a name="copying-large-datasets"></a>Copiar grandes conjuntos de dados

Quando o tamanho do conjunto de dados sejam movidos é grande (por exemplo, > 1 TB) ou se tiver várias pastas diferentes, deve considerar a utilização de várias tarefas de DistCp. Não é provável que nenhum ganho de desempenho, mas propaga terminar as tarefas, para que se qualquer tarefa falhar, apenas terá de reiniciar esse trabalho específico, em vez do trabalho inteiro.

### <a name="limitations"></a>Limitações

* DistCp tenta criar mapeadores que são semelhantes de tamanho para otimizar o desempenho. Aumentar o número de mapeadores pode não sempre aumentar o desempenho.

* DistCp está limitado a apenas um mapeador de pontos por ficheiro. Por conseguinte, não deve ter mais mapeadores que tiver ficheiros. Uma vez que o DistCp só pode atribuir um mapeador de pontos num arquivo, isso limita a quantidade de simultaneidade que pode ser utilizada para copiar ficheiros grandes.

* Se tiver um pequeno número de ficheiros grandes, em seguida, deve dividi-los em segmentos de ficheiro de 256 MB para lhe dar mais simultaneidade potencial. 
 
* Se estiver a copiar a partir de uma conta de armazenamento de Blobs do Azure, a tarefa de cópia pode ser limitada no lado do armazenamento de Blobs. Isso degrada o desempenho da sua tarefa de cópia. Para saber mais sobre os limites do armazenamento de Blobs do Azure, veja os limites de armazenamento do Azure na [subscrição do Azure e limites do serviço](../azure-subscription-service-limits.md).

## <a name="see-also"></a>Consulte também
* [Copiar dados dos Blobs de armazenamento do Azure para a geração 1 de armazenamento do Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utilizar o Azure HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
