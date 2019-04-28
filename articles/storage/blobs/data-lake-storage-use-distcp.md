---
title: Copiar dados para a geração 2 de armazenamento do Azure Data Lake, utilizar o DistCp | Documentos da Microsoft
description: Utilize a ferramenta de DistCp para copiar dados para e de geração 2 de armazenamento do Data Lake
services: storage
author: seguler
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: seguler
ms.openlocfilehash: 3b58dc8dabc55ba428ce6e35091a6947e5f4a824
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481807"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Utilizar o DistCp para copiar dados entre os Blobs de armazenamento do Azure e de geração 2 de armazenamento do Azure Data Lake

Pode usar [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) para copiar dados entre uma conta de armazenamento de fins gerais V2 e uma conta de armazenamento de fins gerais V2 com espaço de nomes hierárquico ativado. Este artigo fornece instruções sobre como utilizam a ferramenta de DistCp.

DistCp fornece uma variedade de parâmetros da linha de comandos e é altamente recomendável que leia este artigo para otimizar a utilização do mesmo. Este artigo mostra as funcionalidades básicas concentrando no seu uso para copiar dados para uma conta de espaço de nomes hierárquico ativada.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de armazenamento do Azure existente sem capacidades de geração 2 de armazenamento do Data Lake (espaço de nomes hierárquico) ativada**.
* **Uma conta de armazenamento do Azure com a funcionalidade de geração 2 de armazenamento do Data Lake ativada**. Para obter instruções sobre como criar um, consulte [criar uma conta de armazenamento de geração 2 de armazenamento do Azure Data Lake](data-lake-storage-quickstart-create-account.md)
* **Um sistema de ficheiros** que criou na conta de armazenamento com espaço de nomes hierárquico ativado.
* **Cluster de HDInsight do Azure** com acesso a uma conta de armazenamento com o Data Lake Storage Gen2 ativada. Ver [Use Azure Data Lake armazenamento Gen2 com o Azure HDInsight clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Certifique-se de que ativar o ambiente de trabalho remoto para o cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Utilizar o DistCp a partir de um cluster Linux de HDInsight

Um cluster do HDInsight é fornecido com o utilitário de DistCp, que pode ser usado para copiar dados de origens diferentes para um cluster do HDInsight. Se tiver configurado o cluster do HDInsight para utilizar o armazenamento de Blobs do Azure e o armazenamento do Azure Data Lake em conjunto, o utilitário de DistCp pode ser utilizado-de-pronta para copiar dados entre também. Nesta secção, vamos ver como usar o utilitário de DistCp.

1. Crie uma sessão SSH ao seu cluster do HDI. Ver [ligar a um cluster do HDInsight baseado em Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Certifique-se de que se pode aceder a sua conta V2 de fins gerais existentes (sem o espaço de nomes hierárquico ativado).

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    A saída deve fornecer uma lista de conteúdo no contentor.

3. Da mesma forma, verifique se pode aceder a conta de armazenamento com espaço de nomes hierárquico ativado do cluster. Execute o seguinte comando:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    A saída deve fornecer uma lista de ficheiros/pastas na conta de armazenamento do Data Lake.

4. Utilize o DistCp para copiar dados de WASB para uma conta de armazenamento do Data Lake.

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    O comando copia o conteúdo dos **/exemplo/dados/gutenberg/** pasta no armazenamento de BLOBs para **/myfolder** na conta de armazenamento do Data Lake.

5. Da mesma forma, utilize o DistCp para copiar dados da conta de armazenamento do Data Lake para armazenamento de BLOBs (WASB).

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    O comando copia o conteúdo do **/myfolder** na conta do Data Lake Store para **/exemplo/dados/gutenberg/** pasta no WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerações sobre o desempenho ao utilizar o DistCp

Como a granularidade de mais baixa do DistCp é um único arquivo, definir o número máximo de cópias simultâneas é o parâmetro mais importante para otimizá-lo contra o armazenamento do Data Lake. Número de cópias simultâneas é igual ao número de mapeadores (**m**) parâmetro na linha de comandos. Este parâmetro especifica o número máximo de mapeadores que são utilizados para copiar dados. Valor predefinido é 20.

**Exemplo**

    hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Como posso determinar o número de mapeadores utilizar?

Eis algumas orientações que poderá utilizar.

* **Passo 1: Determinar a memória total disponível para a fila de aplicação do YARN 'default'** -o primeiro passo é determinar a memória disponível para a fila de aplicação do YARN 'default'. Essas informações estão disponíveis no portal do Ambari associado ao cluster. Navegue para o YARN e veja a guia de configurações para ver a memória YARN disponível para a fila de aplicação 'default'. Esta é a memória total disponível para a sua tarefa de DistCp (que é, na verdade, uma tarefa de MapReduce).

* **Passo 2: Calcular o número de mapeadores** -o valor de **m** é igual ao quociente de memória YARN total dividido pelo tamanho do contentor YARN. As informações de tamanho de contentor do YARN estão disponíveis no portal do Ambari. Navegue para o YARN e ver a guia configurações. O tamanho de contentor do YARN é apresentado nesta janela. A equação a deparar-se com o número de mapeadores (**m**) é

        m = (number of nodes * YARN memory for each node) / YARN container size

**Exemplo**

Vamos assumir que tem um cluster de x D14v2s 4 e está a tentar transferir 10 TB de dados de 10 pastas diferentes. Cada uma das pastas contém diferentes quantidades de dados e os tamanhos de ficheiro em cada pasta são diferentes.

* **Total de memória YARN**: Partir do portal do Ambari é determinar se a memória YARN é 96 GB para um nó de D14. Por isso, é o total de memória YARN para cluster de quatro nós: 

        YARN memory = 4 * 96GB = 384GB

* **Número de mapeadores**: Partir do portal do Ambari determinar que o tamanho de contentor do YARN é 3,072 MB para um nó de cluster D14. Por isso, é o número de mapeadores:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Se outros aplicativos estiver usando a memória, em seguida, pode optar por utilizar apenas uma parte da memória YARN do seu cluster para DistCp.

### <a name="copying-large-datasets"></a>Copiar grandes conjuntos de dados

Quando o tamanho do conjunto de dados sejam movidos é grande (por exemplo, > 1 TB) ou se tiver várias pastas diferentes, deve considerar a utilização de várias tarefas de DistCp. Não é provável que nenhum ganho de desempenho, mas propaga terminar as tarefas, para que se qualquer tarefa falhar, apenas terá de reiniciar esse trabalho específico, em vez do trabalho inteiro.

### <a name="limitations"></a>Limitações

* DistCp tenta criar mapeadores que são semelhantes de tamanho para otimizar o desempenho. Aumentar o número de mapeadores pode não sempre aumentar o desempenho.

* DistCp está limitado a apenas um mapeador de pontos por ficheiro. Por conseguinte, não deve ter mais mapeadores que tiver ficheiros. Uma vez que o DistCp só pode atribuir um mapeador de pontos num arquivo, isso limita a quantidade de simultaneidade que pode ser utilizada para copiar ficheiros grandes.

* Se tiver um pequeno número de ficheiros grandes, em seguida, deve dividi-los em segmentos de ficheiro de 256 MB para lhe dar mais simultaneidade potencial.
