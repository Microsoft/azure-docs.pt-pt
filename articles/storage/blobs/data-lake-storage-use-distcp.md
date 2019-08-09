---
title: Copiar dados em Azure Data Lake Storage Gen2 usando o DistCp | Microsoft Docs
description: Use a ferramenta DistCp para copiar dados de e para Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: d33518c7dc82f8af61fef02ecabb7ac7f42e28fb
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847088"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Use DistCp para copiar dados entre os blobs de armazenamento do Azure e Azure Data Lake Storage Gen2

Você pode usar [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) para copiar dados entre uma conta de armazenamento de uso geral V2 e uma conta de armazenamento de uso geral V2 com namespace hierárquico habilitado. Este artigo fornece instruções sobre como usar a ferramenta DistCp.

O DistCp fornece uma variedade de parâmetros de linha de comando e incentiva enfaticamente que você leia este artigo para otimizar seu uso. Este artigo mostra a funcionalidade básica ao se concentrar em seu uso para copiar dados para uma conta do namespace hierárquico habilitada.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de armazenamento do Azure existente sem data Lake Storage Gen2 recursos (namespace hierárquico) habilitada**.
* **Uma conta de armazenamento do Azure com o recurso Data Lake Storage Gen2 habilitado**. Para obter instruções sobre como criar uma, consulte [criar uma conta de armazenamento Azure data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)
* **Um sistema de arquivos** que foi criado na conta de armazenamento com o namespace hierárquico habilitado.
* **Cluster HDInsight do Azure** com acesso a uma conta de armazenamento com data Lake Storage Gen2 habilitado. Consulte [usar Azure data Lake Storage Gen2 com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Certifique-se de habilitar Área de Trabalho Remota para o cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Usar o DistCp de um cluster HDInsight Linux

HDInsight An cluster é fornecido com o utilitário DistCp, que pode ser usado para copiar dados de diferentes fontes para um cluster HDInsight. Se você tiver configurado o cluster HDInsight para usar o armazenamento de BLOBs do Azure e Azure Data Lake Storage juntos, o utilitário DistCp poderá ser usado prontamente para copiar dados entre eles. Nesta seção, veremos como usar o utilitário DistCp.

1. Crie uma sessão SSH para o cluster HDI. Consulte [conectar-se a um cluster HDInsight baseado em Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Verifique se você pode acessar sua conta de uso geral v2 existente (sem namespace hierárquico habilitado).

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    A saída deve fornecer uma lista de conteúdos no contêiner.

3. Da mesma forma, verifique se você pode acessar a conta de armazenamento com o namespace hierárquico habilitado no cluster. Execute o seguinte comando:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    A saída deve fornecer uma lista de arquivos/pastas na conta Data Lake Storage.

4. Use DistCp para copiar dados do WASB para uma conta Data Lake Storage.

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    O comando copia o conteúdo da pasta **/example/data/Gutenberg/** no armazenamento de BLOBs para **/myfolder** na conta data Lake Storage.

5. Da mesma forma, use DistCp para copiar dados da conta Data Lake Storage para o armazenamento de BLOBs (WASB).

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    O comando copia o conteúdo de **/MyFolder** na conta de data Lake Store para a pasta **/example/data/Gutenberg/** em WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerações de desempenho ao usar o DistCp

Como a granularidade mais baixa do DistCp é um único arquivo, definir o número máximo de cópias simultâneas é o parâmetro mais importante para otimizá-lo em relação à Data Lake Storage. O número de cópias simultâneas é igual ao parâmetro número de Mapeadores (**m**) na linha de comando. Esse parâmetro especifica o número máximo de Mapeadores que são usados para copiar dados. O valor padrão é 20.

**Exemplo**

    hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Como fazer determinar o número de Mapeadores a serem usados?

Eis algumas orientações que poderá utilizar.

* **Etapa 1: Determinar a memória total disponível para a fila** de aplicativos yarn ' padrão '-a primeira etapa é determinar a memória disponível para a fila de aplicativos yarn ' default '. Essas informações estão disponíveis no portal do Ambari associado ao cluster. Navegue até YARN e exiba a guia Configurações para ver a memória YARN disponível para a fila de aplicativo ' padrão '. Esta é a memória total disponível para seu trabalho do DistCp (que é, na verdade, um trabalho MapReduce).

* **Etapa 2: Calcular o número de Mapeadores** -o valor de **m** é igual ao quociente do total de memória yarn dividido pelo tamanho do contêiner yarn. As informações de tamanho do contêiner YARN também estão disponíveis no portal do Ambari. Navegue até YARN e exiba a guia Configurações. O tamanho do contêiner YARN é exibido nesta janela. A equação para chegar ao número de Mapeadores (**m**) é

        m = (number of nodes * YARN memory for each node) / YARN container size

**Exemplo**

Vamos supor que você tenha um cluster 4x D14v2s e esteja tentando transferir 10 TB de dados de 10 pastas diferentes. Cada uma das pastas contém quantidades variadas de dados e os tamanhos de arquivo em cada pasta são diferentes.

* **Memória yarn total**: No portal do Ambari, você determina que a memória YARN é de 96 GB para um nó D14. Portanto, a memória total do YARN para o cluster de quatro nós é: 

        YARN memory = 4 * 96GB = 384GB

* **Número de Mapeadores**: No portal do Ambari, você determina que o tamanho do contêiner YARN é 3.072 MB para um nó de cluster D14. Portanto, o número de Mapeadores é:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Se outros aplicativos estiverem usando a memória, você poderá optar por usar apenas uma parte da memória YARN do cluster para DistCp.

### <a name="copying-large-datasets"></a>Copiando conjuntos de grandes volumes

Quando o tamanho do conjunto de um para ser movido for grande (por exemplo, > 1 TB) ou se você tiver muitas pastas diferentes, considere o uso de vários trabalhos do DistCp. Provavelmente, não há nenhum lucro de desempenho, mas ele espalha os trabalhos para que, se algum trabalho falhar, você só precise reiniciar esse trabalho específico em vez de todo o trabalho.

### <a name="limitations"></a>Limitações

* O DistCp tenta criar Mapeadores que são semelhantes em tamanho para otimizar o desempenho. Aumentar o número de Mapeadores pode nem sempre aumentar o desempenho.

* DistCp é limitado a apenas um mapeador por arquivo. Portanto, você não deve ter mais Mapeadores do que os arquivos. Como DistCp pode atribuir apenas um mapeador a um arquivo, isso limita a quantidade de simultaneidade que pode ser usada para copiar arquivos grandes.

* Se você tiver um pequeno número de arquivos grandes, você deve dividi-los em partes de arquivo de 256 MB para fornecer uma simultaneidade mais potencial.
