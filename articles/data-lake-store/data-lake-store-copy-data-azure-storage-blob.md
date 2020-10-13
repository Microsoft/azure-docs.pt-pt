---
title: Copiar dados das bolhas de armazenamento Azure para data lake storage gen1
description: Utilize a ferramenta AdlCopy para copiar dados de Azure Storage Blobs para Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 84ee65b05af4393f49696875bda41df39e283d5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85980094"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Copiar dados de Azure Storage Blobs para Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Utilizar o DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Utilizar o AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Data Lake Storage Gen1 fornece uma ferramenta de linha de comando, [AdlCopy,](https://www.microsoft.com/download/details.aspx?id=50358)para copiar dados das seguintes fontes:

* Das bolhas de armazenamento Azure para a Data Lake Storage Gen1. Não é possível utilizar o AdlCopy para copiar dados da Data Lake Storage Gen1 para as bolhas de armazenamento Azure.
* Entre duas contas da Data Lake Storage Gen1.

Além disso, pode utilizar a ferramenta AdlCopy em dois modos diferentes:

* **Autónomo**, onde a ferramenta utiliza recursos da Data Lake Storage Gen1 para executar a tarefa.
* **Utilizando uma conta Data Lake Analytics,** onde as unidades atribuídas à sua conta Data Lake Analytics são utilizadas para realizar a operação de cópia. É possível que queira utilizar esta opção quando pretender executar as tarefas de cópia de forma previsível.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, tem de ter o seguinte:

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Recipiente de bolhas de armazenamento Azure** com alguns dados.
* **Uma conta gen1 de armazenamento de data lake.** Para instruções sobre como criar um, consulte [Começar com a Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Conta Data Lake Analytics (opcional)** - Consulte [Começar com o Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) para obter instruções sobre como criar uma conta Data Lake Analytics.
* **Ferramenta AdlCopy**. Instale a [ferramenta AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358).

## <a name="syntax-of-the-adlcopy-tool"></a>Sintaxe da ferramenta AdlCopy

Utilize a seguinte sintaxe para trabalhar com a ferramenta AdlCopy

```console
AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern
```

Os parâmetros da sintaxe são descritos abaixo:

| Opção | Descrição |
| --- | --- |
| Origem |Especifica a localização dos dados de origem na bolha de armazenamento Azure. A fonte pode ser um recipiente de bolhas, uma bolha, ou outra conta da Data Lake Storage Gen1. |
| Dest |Especifica o destino Data Lake Storage Gen1 para copiar. |
| FonteKey |Especifica a chave de acesso ao armazenamento para a fonte de bolha de armazenamento Azure. Isto só é necessário se a fonte for um recipiente de bolhas ou uma bolha. |
| Conta |**Opcional.** Use isto se quiser utilizar a conta Azure Data Lake Analytics para executar o trabalho de cópia. Se utilizar a opção /Conta na sintaxe mas não especificar uma conta Data Lake Analytics, a AdlCopy utiliza uma conta predefinitiva para executar o trabalho. Além disso, se utilizar esta opção, deve adicionar a fonte (Azure Storage Blob) e o destino (Azure Data Lake Storage Gen1) como fontes de dados para a sua conta Data Lake Analytics. |
| Unidades |Especifica o número de unidades de Data Lake Analytics que serão usadas para o trabalho de cópia. Esta opção é obrigatória se utilizar a opção **/Conta** para especificar a conta Data Lake Analytics. |
| Padrão |Especifica um padrão regex que indica quais as bolhas ou ficheiros a copiar. A AdlCopy usa correspondência sensível a casos. O padrão padrão quando nenhum padrão é especificado é copiar todos os itens. Especificar vários padrões de ficheiros não é suportado. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Utilize a AdlCopy (como autónoma) para copiar dados de uma bolha de armazenamento Azure

1. Abra uma solicitação de comando e navegue para o diretório onde o AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy` .
1. Executar o seguinte comando para copiar uma bolha específica do recipiente de origem para uma pasta Gen1 de armazenamento de dados:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>
    ```

    Por exemplo:

    ```console
    AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

    >[!NOTE]
    >A sintaxe acima especifica o ficheiro a ser copiado para uma pasta na conta Da Gen1 de Armazenamento de Data Lake. A ferramenta AdlCopy cria uma pasta se o nome da pasta especificado não existir.

    Será solicitado que introduza as credenciais para a subscrição do Azure sob a qual tem a sua conta Desarrumação Gen1. Verá uma saída semelhante à seguinte:

    ```output
    Initializing Copy.
    Copy Started.
    100% data copied.
    Finishing Copy.
    Copy Completed. 1 file copied.
    ```

1. Também pode copiar todas as bolhas de um recipiente para a conta Desebos de Armazenamento de Dados Gen1 utilizando o seguinte comando:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>  
    ```      

    Por exemplo:

    ```console
    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

### <a name="performance-considerations"></a>Considerações de desempenho

Se estiver a copiar a partir de uma conta de Armazenamento Azure Blob, poderá ser estrangulado durante a cópia no lado do armazenamento da bolha. Isto irá degradar o desempenho do seu trabalho de cópia. Para saber mais sobre os limites do Azure Blob Storage, consulte os limites de armazenamento do Azure em [limites de subscrição e serviço da Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Use a AdlCopy (como autónoma) para copiar dados de outra conta gen1 de armazenamento de data lake

Também pode usar o AdlCopy para copiar dados entre duas contas da Data Lake Storage Gen1.

1. Abra uma solicitação de comando e navegue para o diretório onde o AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy` .
1. Executar o seguinte comando para copiar um ficheiro específico de uma conta Gen1 de armazenamento de dados para outra.

    ```console
    AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/
    ```

    Por exemplo:

    ```console
    AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/
    ```

   > [!NOTE]
   > A sintaxe acima especifica o ficheiro a ser copiado para uma pasta na conta de Data Lake Storage Gen1 do destino. A ferramenta AdlCopy cria uma pasta se o nome da pasta especificado não existir.
   >
   >

    Será solicitado que introduza as credenciais para a subscrição do Azure sob a qual tem a sua conta Desarrumação Gen1. Verá uma saída semelhante à seguinte:

    ```output
    Initializing Copy.
    Copy Started.|
    100% data copied.
    Finishing Copy.
    Copy Completed. 1 file copied.
    ```
1. O seguinte comando copia todos os ficheiros de uma pasta específica na conta de Data Lake Storage Gen1 para uma pasta na conta de Data Lake Storage Gen1.

    ```console
    AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/
    ```

### <a name="performance-considerations"></a>Considerações de desempenho

Ao utilizar o AdlCopy como uma ferramenta autónoma, a cópia é executada em recursos partilhados, geridos pelo Azure. O desempenho que você pode obter neste ambiente depende da carga do sistema e dos recursos disponíveis. Este modo é melhor utilizado para pequenas transferências numa base ad hoc. Não é necessário sintonizar parâmetros quando se utiliza o AdlCopy como uma ferramenta autónoma.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Utilize o AdlCopy (com conta Data Lake Analytics) para copiar dados

Também pode utilizar a sua conta Data Lake Analytics para executar o trabalho da AdlCopy para copiar dados de bolhas de armazenamento Azure para Data Lake Storage Gen1. Normalmente, usaria esta opção quando os dados a serem movidos estão na gama de gigabytes e terabytes, e você quer uma produção melhor e previsível.

Para utilizar a sua conta Data Lake Analytics com a AdlCopy para copiar a partir de uma Bolha de Armazenamento Azure, a fonte (Azure Storage Blob) deve ser adicionada como fonte de dados para a sua conta Data Lake Analytics. Para obter instruções sobre a adição de fontes de dados adicionais à sua conta Data Lake Analytics, consulte [fontes de dados da conta Do Lago de Dados](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Se estiver a copiar a partir de uma conta Azure Data Lake Storage Gen1 como fonte utilizando uma conta Data Lake Analytics, não precisa de associar a conta de Data Lake Storage Gen1 à conta Data Lake Analytics. A exigência de associar a loja de origem à conta Data Lake Analytics é apenas quando a fonte é uma conta de Armazenamento Azure.
>
>

Executar o seguinte comando para copiar de uma bolha de armazenamento Azure para uma conta Gen1 de armazenamento de data lake usando conta Data Lake Analytics:

```console
AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>
```

Por exemplo:

```console
AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2
```

Da mesma forma, executar o seguinte comando para copiar todos os ficheiros de uma pasta específica na conta de Data Lake Storage Gen1 para uma pasta na conta de armazenamento de data lake de destino Gen1 usando a conta Data Lake Analytics: Data Lake Analytics:

```console
AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2
```

### <a name="performance-considerations"></a>Considerações de desempenho

Ao copiar dados na gama de terabytes, usar o AdlCopy com a sua própria conta Azure Data Lake Analytics proporciona um desempenho melhor e mais previsível. O parâmetro que deve ser afinado é o número de Unidades de Análise do Lago de Dados Azure para utilizar para o trabalho de cópia. Aumentar o número de unidades aumentará o desempenho do seu trabalho de cópia. Cada ficheiro a copiar pode utilizar uma unidade máxima. Especificar mais unidades do que o número de ficheiros copiados não aumentará o desempenho.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Use o AdlCopy para copiar dados usando a correspondência de padrões

Nesta secção, você aprende a usar a AdlCopy para copiar dados de uma fonte (no nosso exemplo abaixo usamos Azure Storage Blob) para uma conta de armazenamento de data lake de destino Gen1 usando padrão correspondente. Por exemplo, pode utilizar os passos abaixo para copiar todos os ficheiros com extensão .csv da bolha de origem para o destino.

1. Abra uma solicitação de comando e navegue para o diretório onde o AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy` .
1. Executar o seguinte comando para copiar todos os ficheiros com extensão *.csv de uma bolha específica do recipiente de origem para uma pasta Gen1 de armazenamento de dados:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv
    ```

    Por exemplo:

    ```console
    AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv
    ```

## <a name="billing"></a>Faturação

* Se utilizar a ferramenta AdlCopy como autónoma, será cobrado os custos de saída para a movimentação de dados, se a conta de armazenamento de fonte Azure não estiver na mesma região que a conta de Data Lake Storage Gen1.
* Se utilizar a ferramenta AdlCopy com a sua conta Data Lake Analytics, [aplicar-se-ão as taxas](https://azure.microsoft.com/pricing/details/data-lake-analytics/) de faturação padrão do Data Lake Analytics.

## <a name="considerations-for-using-adlcopy"></a>Considerações para a utilização do AdlCopy

* O AdlCopy (para a versão 1.0.5), suporta a cópia de dados de fontes que, coletivamente, têm mais de milhares de ficheiros e pastas. No entanto, se encontrar problemas que copiem um conjunto de dados grandes, pode distribuir os ficheiros/pastas em diferentes sub-dobradeiras e utilizar o caminho para essas sub-dobradeiras como fonte.

## <a name="performance-considerations-for-using-adlcopy"></a>Considerações de desempenho para a utilização do AdlCopy

A AdlCopy suporta a cópia de dados contendo milhares de ficheiros e pastas. No entanto, se encontrar problemas que copiem um conjunto de dados grandes, pode distribuir os ficheiros/pastas em sub-dobradeiras mais pequenas. AdlCopy foi construída para cópias ad hoc. Se estiver a tentar copiar dados numa base recorrente, deve considerar a utilização da [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) que fornece uma gestão completa em torno das operações de cópia.

## <a name="release-notes"></a>Notas de versão

* 1.0.13 - Se estiver a copiar dados para a mesma conta Azure Data Lake Storage Gen1 através de vários comandos de adlcopia, já não precisa de reentrar nas suas credenciais para cada execução. A adlcopia irá agora cache essa informação em várias corridas.

## <a name="next-steps"></a>Passos seguintes

* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Use Azure Data Lake Analytics com data lake storage gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight com data lake storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
