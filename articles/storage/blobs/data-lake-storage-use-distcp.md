---
title: Copie os dados para o Azure Data Lake Storage Gen2 usando o DistCp| Microsoft Docs
description: Copie os dados de e para a Azure Data Lake Storage Gen2 utilizando a ferramenta de cópia distribuída Apache Hadoop (DistCp).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e69a97a86a357fb36dde572f292b5cac7963d14a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95912489"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Utilize o DistCp para copiar dados entre as bolhas de armazenamento Azure e a Azure Data Lake Storage Gen2

Pode utilizar [o DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) para copiar dados entre uma conta de armazenamento V2 de finalidade geral e uma conta de armazenamento V2 de finalidade geral com espaço hierárquico ativado. Este artigo fornece instruções sobre como utilizar a ferramenta DistCp.

O DistCp fornece uma variedade de parâmetros de linha de comando e encorajamo-lo a ler este artigo de forma a otimizar o seu uso do mesmo. Este artigo mostra a funcionalidade básica, focando-se na sua utilização para copiar dados para uma conta de nome hierárquica ativada.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Uma conta de armazenamento Azure existente sem capacidades de Armazenamento de Data Lake Gen2 (espaço hierárquico) ativada.
* Uma conta de armazenamento Azure com capacidades de armazenamento de dados Gen2 (espaço hierárquico) ativada. Para obter instruções sobre como criar uma, consulte [Criar uma conta de Armazenamento Azure](../common/storage-account-create.md)
* Um recipiente que foi criado na conta de armazenamento com espaço hierárquico habilitado.
* Um cluster Azure HDInsight com acesso a uma conta de armazenamento com a funcionalidade hierárquica do espaço de identificação ativada. Consulte [a Utilização Azure Data Lake Storage Gen2 com clusters Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Certifique-se de que ativa o Ambiente de Trabalho Remoto para o cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Use DistCp de um cluster HDInsight Linux

Um cluster HDInsight vem com o utilitário DistCp, que pode ser usado para copiar dados de diferentes fontes para um cluster HDInsight. Se tiver configurado o cluster HDInsight para utilizar o Azure Blob Storage e o Azure Data Lake Storage juntos, o utilitário DistCp pode ser usado fora da caixa para copiar dados entre eles também. Nesta secção, olhamos para como usar a utilidade do DistCp.

1. Crie uma sessão SSH para o seu cluster HDI. Consulte [Connect a um cluster HDInsight baseado em Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Verifique se pode aceder à sua conta V2 de finalidade geral existente (sem espaço hierárquico ativado).

    ```bash
    hdfs dfs –ls wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/
    ```

   A saída deve fornecer uma lista de conteúdos no recipiente.

3. Da mesma forma, verifique se pode aceder à conta de armazenamento com espaço hierárquico ativado a partir do cluster. Execute o seguinte comando:

    ```bash
    hdfs dfs -ls abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/
    ```

    A saída deve fornecer uma lista de ficheiros/pastas na conta de armazenamento do Data Lake.

4. Utilize o DistCp para copiar dados do WASB para uma conta de Armazenamento de Data Lake.

    ```bash
    hadoop distcp wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder
    ```

    O comando copia o conteúdo da pasta **/exemplo/data/gutenberg/** em armazenamento Blob para **/myfolder** na conta de Armazenamento do Lago de Dados.

5. Da mesma forma, utilize o DistCp para copiar dados da conta de armazenamento do Data Lake para o Blob Storage (WASB).

    ```bash
    hadoop distcp abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg
    ```

    O comando copia o conteúdo de **/myfolder** na conta Data Lake Store para **/exemplo/data/gutenberg/** pasta em WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerações de desempenho durante a utilização do DistCp

Como a granularidade mais baixa do DistCp é um único ficheiro, definir o número máximo de cópias simultâneas é o parâmetro mais importante para otimizá-lo contra o armazenamento do Data Lake. O número de cópias simultâneas é igual ao parâmetro do número de mappers **(m)** na linha de comando. Este parâmetro especifica o número máximo de mappers que são usados para copiar dados. O valor predefinido é de 20.

**Exemplo**

```bash
hadoop distcp -m 100 wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder
```

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Como determino o número de mappers a utilizar?

Eis algumas orientações que poderá utilizar.

* **Passo 1: Determinar a memória total disponível para a fila de aplicações 'predefinida' YARN** - O primeiro passo é determinar a memória disponível para a fila da aplicação 'predefinida' YARN. Esta informação está disponível no portal Ambari associado ao cluster. Navegue até YARN e veja o separador Configs para ver a memória YARN disponível na fila de aplicações 'predefinida'. Esta é a memória total disponível para o seu trabalho DistCp (que na verdade é um trabalho MapReduce).

* **Passo 2: Calcular o número de mappers** - O valor de **m** é igual ao quociente da memória total de YARN dividida pelo tamanho do recipiente YARN. A informação sobre o tamanho do contentor YARN também está disponível no portal Ambari. Navegue até YARN e veja o separador Configs. O tamanho do recipiente YARN é apresentado nesta janela. A equação para chegar ao número de mappers **(m)** é

    m = (número de nós * memória YARN para cada nó) / tamanho do recipiente YARN

**Exemplo**

Vamos supor que tem um cluster 4x D14v2s e está a tentar transferir 10 TB de dados de 10 pastas diferentes. Cada uma das pastas contém quantidades variadas de dados e os tamanhos dos ficheiros dentro de cada pasta são diferentes.

* **Memória total do YARN**: A partir do portal Ambari, determina-se que a memória YARN é de 96 GB para um nó D14. Então, a memória total do YARN para quatro aglomerados de nó é: 

    Memória YARN = 4 * 96GB = 384GB

* **Número de mappers**: A partir do portal Ambari, determina-se que o tamanho do contentor YARN é de 3.072 MB para um nó de cluster D14. Então, o número de mappers é:

    m = (4 nós * 96GB) / 3072MB = 128 mappers

Se outras aplicações estiverem a usar memória, então pode optar por utilizar apenas uma parte da memória YARN do seu cluster para o DistCp.

### <a name="copying-large-datasets"></a>Copiar grandes conjuntos de dados

Quando o tamanho do conjunto de dados a ser movido é grande (por exemplo, >1 TB) ou se tiver muitas pastas diferentes, deve considerar a utilização de vários trabalhos DistCp. É provável que não haja ganhos de desempenho, mas espalha os postos de trabalho para que, se algum trabalho falhar, só precise de reiniciar esse trabalho específico em vez de todo o trabalho.

### <a name="limitations"></a>Limitações

* A DistCp tenta criar mappers que sejam semelhantes em tamanho para otimizar o desempenho. Aumentar o número de mappers pode nem sempre aumentar o desempenho.

* O DistCp está limitado a apenas um mapper por ficheiro. Portanto, não deve ter mais mappers do que tem ficheiros. Uma vez que o DistCp só pode atribuir um mapper a um ficheiro, isto limita a quantidade de concordância que pode ser usada para copiar ficheiros grandes.

* Se tiver um pequeno número de ficheiros grandes, então deve dividi-los em pedaços de ficheiro de 256 MB para lhe dar mais conúnquidade potencial.