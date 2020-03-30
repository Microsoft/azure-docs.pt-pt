---
title: Copie dados no Azure Data Lake Storage Gen2 usando distCp/ Microsoft Docs
description: Utilize a ferramenta DistCp para copiar dados de e para data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3c09a95309e001def306698bbba4f6d0a1a2804d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255538"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Utilize o DistCp para copiar dados entre as Bolhas de Armazenamento de Azure e o Azure Data Lake Storage Gen2

Pode utilizar o [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) para copiar dados entre uma conta de armazenamento V2 de finalidade geral e uma conta de armazenamento V2 com espaço de nome hierárquico ativado. Este artigo fornece instruções sobre a utilização da ferramenta DistCp.

A DistCp fornece uma variedade de parâmetros de linha de comando e encorajamo-lo vivamente a ler este artigo de forma a otimizar o seu uso do mesmo. Este artigo mostra a funcionalidade básica ao mesmo tempo que se foca na sua utilização para copiar dados para uma conta hierárquica ativada pelo espaço de nome.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de armazenamento azure existente sem capacidades de Armazenamento de Data Lake Gen2 (espaço hierárquico) habilitado**.
* **Uma conta de armazenamento azure com recurso Data Lake Storage Gen2 ativada**. Para obter instruções sobre como criar uma, consulte Criar uma conta de armazenamento de armazenamento [de lago de dados Azure Gen2](data-lake-storage-quickstart-create-account.md)
* Um sistema de **ficheiros** que foi criado na conta de armazenamento com espaço de nome hierárquico habilitado.
* **Cluster Azure HDInsight** com acesso a uma conta de armazenamento com Data Lake Storage Gen2 ativado. Consulte a utilização do Armazenamento de [Lagos Azure Data Gen2 com clusters Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Certifique-se de que ativa o Ambiente de Trabalho Remoto para o cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Utilize distCp de um cluster HDInsight Linux

Um cluster HDInsight vem com o utilitário DistCp, que pode ser usado para copiar dados de diferentes fontes para um cluster HDInsight. Se configurar o cluster HDInsight para utilizar juntos o Armazenamento de Lagos Azure Blob e Azure Data Lake, o utilitário DistCp pode ser utilizado fora da caixa para copiar dados entre eles também. Nesta secção, olhamos para a forma de usar o utilitário DistCp.

1. Crie uma sessão sSH para o seu cluster HDI. Consulte [a Ligação a um cluster HDInsight baseado em Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Verifique se pode aceder à sua conta V2 de propósito geral existente (sem espaço de nome hierárquico ativado).

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    A saída deve fornecer uma lista de conteúdos no recipiente.

3. Da mesma forma, verifique se pode aceder à conta de armazenamento com espaço de nome hierárquico habilitado a partir do cluster. Execute o seguinte comando:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    A saída deve fornecer uma lista de ficheiros/pastas na conta data lake armazenamento.

4. Utilize o DistCp para copiar dados do WASB para uma conta de Armazenamento de Data Lake.

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    O comando copia o conteúdo da **pasta /exemplo/data/gutenberg/** no armazenamento blob para **/myfolder** na conta data Lake Storage.

5. Da mesma forma, utilize o DistCp para copiar dados da conta de Armazenamento de Data Lake para blob Storage (WASB).

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    O comando copia o conteúdo de **/myfolder** na conta Data Lake Store para **/exemplo/dados/gutenberg/** pasta em WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerações de desempenho ao utilizar o DistCp

Como a granularidade mais baixa da DistCp é um único ficheiro, definir o número máximo de cópias simultâneas é o parâmetro mais importante para o otimizar contra o Armazenamento do Lago de Dados. O número de cópias simultâneas é igual ao número de parâmetros **(m)** na linha de comando. Este parâmetro especifica o número máximo de mapeadores que são usados para copiar dados. O valor padrão é de 20.

**Exemplo**

    hadoop distcp -m 100 wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Como determino o número de mappers a usar?

Eis algumas orientações que poderá utilizar.

* **Passo 1: Determinar a memória total disponível para a fila da aplicação YARN 'padrão'** - O primeiro passo é determinar a memória disponível para a fila da aplicação YARN 'padrão'. Esta informação está disponível no portal Ambari associado ao cluster. Navegue para o YARN e veja o separador Configs para ver a memória YARN disponível para a fila da aplicação 'padrão'. Esta é a memória total disponível para o seu trabalho distCp (que na verdade é um trabalho MapReduce).

* **Passo 2: Calcular o número de mappers** - O valor de **m** é igual ao quociente da memória total do ARN dividida pelo tamanho do recipiente YARN. A informação sobre o tamanho do contentor YARN também está disponível no portal Ambari. Navegue para o YARN e veja o separador Configs. O tamanho do recipiente YARN é apresentado nesta janela. A equação para chegar ao número de mappers **(m**) é

        m = (number of nodes * YARN memory for each node) / YARN container size

**Exemplo**

Vamos supor que tem um cluster 4x D14v2s e está a tentar transferir 10 TB de dados de 10 pastas diferentes. Cada uma das pastas contém quantidades variadas de dados e os tamanhos dos ficheiros dentro de cada pasta são diferentes.

* **Memória total**do ARN : Do portal Ambari determina que a memória YARN é de 96 GB para um nó D14. Então, a memória total do ARN para quatro nós cluster é: 

        YARN memory = 4 * 96GB = 384GB

* **Número de mapeadores**: Do portal Ambari determina que o tamanho do contentor YARN é de 3.072 MB para um nó de cluster D14. Então, o número de mappers é:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Se outras aplicações estiverem a usar a memória, então pode optar por utilizar apenas uma parte da memória YARN do seu cluster para distCp.

### <a name="copying-large-datasets"></a>Copiar grandes conjuntos de dados

Quando o tamanho do conjunto de dados a mover é grande (por exemplo, >1 TB) ou se tiver muitas pastas diferentes, deve considerar a utilização de múltiplos trabalhos distCp. É provável que não haja ganho de desempenho, mas espalha os postos de trabalho para que, se algum emprego falhar, só precisa de reiniciar esse trabalho específico em vez de todo o trabalho.

### <a name="limitations"></a>Limitações

* A DistCp tenta criar mappers semelhantes em tamanho para otimizar o desempenho. Aumentar o número de mappers pode nem sempre aumentar o desempenho.

* O DistCp está limitado a apenas um mapper por ficheiro. Portanto, não deve ter mais mappers do que tem ficheiros. Uma vez que o DistCp só pode atribuir um mapper a um ficheiro, isto limita a quantidade de moeda que pode ser usada para copiar ficheiros grandes.

* Se tiver um pequeno número de ficheiros grandes, então deve dividi-los em pedaços de ficheiro sumo de 256 MB para lhe dar mais potencial concurrency.
