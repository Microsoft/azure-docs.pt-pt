---
title: Copiar dados de e para o WASB para Azure Data Lake Storage Gen1 usando DistCp
description: Use a ferramenta DistCp para copiar dados de e para os BLOBs do armazenamento do Azure para Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 455e73ece2d46a508b3077c13c8106fe53beb4de
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638838"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Use DistCp para copiar dados entre os blobs de armazenamento do Azure e Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Utilizar o DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Utilizar o AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Se você tiver um cluster HDInsight com acesso a Azure Data Lake Storage Gen1, poderá usar ferramentas de ecossistema do Hadoop como DistCp para copiar dados de e para um armazenamento de cluster HDInsight (WASB) em uma conta de Data Lake Storage Gen1. Este artigo mostra como usar a ferramenta DistCp.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de Azure data Lake Storage Gen1**. Para obter instruções sobre como criar uma, consulte Introdução [ao Azure data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Cluster HDInsight do Azure** com acesso a uma conta de data Lake Storage Gen1. Consulte [criar um cluster HDInsight com data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de habilitar Área de Trabalho Remota para o cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Usar o DistCp de um cluster HDInsight Linux

HDInsight An cluster é fornecido com a ferramenta DistCp, que pode ser usada para copiar dados de diferentes fontes para um cluster HDInsight. Se você tiver configurado o cluster HDInsight para usar Data Lake Storage Gen1 como armazenamento adicional, poderá usar o DistCp pronto para copiar dados de e para uma conta de Data Lake Storage Gen1. Nesta seção, veremos como usar a ferramenta DistCp.

1. Em sua área de trabalho, use o SSH para se conectar ao cluster. Consulte [conectar-se a um cluster HDInsight baseado em Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Execute os comandos no prompt do SSH.

1. Verifique se você pode acessar os blobs de armazenamento do Azure (WASB). Execute o seguinte comando:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   A saída fornece uma lista de conteúdos no blob de armazenamento.

1. Da mesma forma, verifique se você pode acessar a conta de Data Lake Storage Gen1 do cluster. Execute o seguinte comando:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    A saída fornece uma lista de arquivos e pastas na conta de Data Lake Storage Gen1.

1. Use DistCp para copiar dados do WASB para uma conta Data Lake Storage Gen1.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    O comando copia o conteúdo da pasta **/example/data/Gutenberg/** em WASB para **/myfolder** na conta data Lake Storage Gen1.

1. Da mesma forma, use DistCp para copiar dados de uma conta de Data Lake Storage Gen1 para o WASB.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    O comando copia o conteúdo de **/MyFolder** na conta de data Lake Storage Gen1 para a pasta **/example/data/Gutenberg/** em WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerações de desempenho ao usar o DistCp

Como a granularidade mais baixa da ferramenta DistCp é um arquivo único, definir o número máximo de cópias simultâneas é o parâmetro mais importante para otimizá-lo em relação à Data Lake Storage Gen1. Você pode controlar o número de cópias simultâneas definindo o parâmetro número de Mapeadores (' M') ' na linha de comando. Esse parâmetro especifica o número máximo de Mapeadores que são usados para copiar dados. O valor predefinido é 20.

Exemplo:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>Como determinar o número de Mapeadores a serem usados

Eis algumas orientações que poderá utilizar.

* **Etapa 1: determinar a memória total do yarn** -a primeira etapa é determinar a memória yarn disponível para o cluster em que você executa o trabalho do DistCp. Essas informações estão disponíveis no portal do Ambari associado ao cluster. Navegue até YARN e exiba a guia **configurações** para ver a memória yarn. Para obter a memória total do YARN, multiplique a memória YARN por nó pelo número de nós que você tem em seu cluster.

* **Etapa 2: calcular o número de Mapeadores** – o valor de **m** é igual ao quociente do total de memória yarn dividido pelo tamanho do contêiner yarn. As informações de tamanho do contêiner YARN também estão disponíveis no portal do Ambari. Navegue até YARN e exiba a guia **configurações** . O tamanho do contêiner YARN é exibido nesta janela. A equação para chegar ao número de Mapeadores (**m**) é:

   `m = (number of nodes * YARN memory for each node) / YARN container size`

Exemplo:

Vamos supor que você tenha quatro nós D14v2s no cluster e queira transferir 10 TB de dados de 10 pastas diferentes. Cada uma das pastas contém quantidades variadas de dados e os tamanhos de arquivo em cada pasta são diferentes.

* Memória YARN total – no portal do Ambari, você determina que a memória de YARN é de 96 GB para um nó D14. Portanto, a memória total do YARN para o cluster de quatro nós é:

   `YARN memory = 4 * 96GB = 384GB`

* Número de Mapeadores – no portal do Ambari, você determina que o tamanho do contêiner YARN é 3072 para um nó de cluster D14. Portanto, o número de Mapeadores é:

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

Se outros aplicativos estiverem usando a memória, você poderá optar por usar apenas uma parte da memória YARN do cluster para DistCp.

### <a name="copying-large-datasets"></a>Copiando conjuntos de grandes volumes

Quando o tamanho do conjunto de um para ser movido for grande (por exemplo, > 1 TB) ou se você tiver muitas pastas diferentes, considere o uso de vários trabalhos do DistCp. Provavelmente, não há nenhum lucro de desempenho, mas ele espalha os trabalhos para que, se algum trabalho falhar, você só precise reiniciar esse trabalho específico em vez de todo o trabalho.

### <a name="limitations"></a>Limitações

* O DistCp tenta criar Mapeadores que são semelhantes em tamanho para otimizar o desempenho. Aumentar o número de Mapeadores pode nem sempre aumentar o desempenho.

* DistCp é limitado a apenas um mapeador por arquivo. Portanto, você não deve ter mais Mapeadores do que os arquivos. Como DistCp pode atribuir apenas um mapeador a um arquivo, isso limita a quantidade de simultaneidade que pode ser usada para copiar arquivos grandes.

* Se você tiver um pequeno número de arquivos grandes, divida-os em partes de arquivo de 256 MB para fornecer uma simultaneidade mais potencial.

* Se você estiver copiando de uma conta de armazenamento de BLOBs do Azure, seu trabalho de cópia poderá ser limitado no lado do armazenamento de BLOBs. Isso degrada o desempenho do seu trabalho de cópia. Para saber mais sobre os limites do armazenamento de BLOBs do Azure, confira limites de armazenamento do Azure em [assinatura do Azure e limites de serviço](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="see-also"></a>Ver também

* [Copiar dados de blobs de armazenamento do Azure para Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Usar Azure Data Lake Analytics com Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
