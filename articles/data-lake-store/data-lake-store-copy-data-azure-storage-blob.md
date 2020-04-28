---
title: Copiar dados das bolhas de armazenamento do Azure para data lake storage Gen1
description: Utilize a ferramenta AdlCopy para copiar dados de Blobs de Armazenamento Azure para O Armazenamento de Dados Azure Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: ad408df140be49da2e50ef810285dd850e9da6a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75638874"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Copiar dados de Blobs de Armazenamento Azure para Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Utilizar o DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Utilizar o AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Data Lake Storage Gen1 fornece uma ferramenta de linha de comando, [AdlCopy,](https://www.microsoft.com/download/details.aspx?id=50358)para copiar dados das seguintes fontes:

* Desde blobs de armazenamento azure até data lake storage Gen1. Não é possível utilizar o AdlCopy para copiar dados de Data Lake Storage Gen1 para blobs de armazenamento azure.
* Entre duas contas de Data Lake Storage Gen1.

Além disso, pode utilizar a ferramenta AdlCopy em dois modos diferentes:

* **Autónomo,** onde a ferramenta utiliza recursos da Gen1 de Armazenamento de Data Lake para executar a tarefa.
* **Utilizando uma conta Data Lake Analytics,** onde as unidades atribuídas à sua conta Data Lake Analytics são utilizadas para executar a operação de cópia. É possível que pretenda utilizar esta opção quando pretende executar as tarefas de cópia de forma previsível.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, tem de ter o seguinte:

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Recipiente de bolhas de armazenamento Azure** com alguns dados.
* **Uma conta Gen1 de Armazenamento de Lago**de Dados. Para obter instruções sobre como criar um, consulte [Começar com Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Conta Data Lake Analytics (opcional)** - Veja [Começar com o Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) para obter instruções sobre como criar uma conta Data Lake Analytics.
* **Ferramenta AdlCopy**. Instale a [ferramenta AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358).

## <a name="syntax-of-the-adlcopy-tool"></a>Sintaxe da ferramenta AdlCopy

Utilize a seguinte sintaxe para trabalhar com a ferramenta AdlCopy

    AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern

Os parâmetros da sintaxe são descritos abaixo:

| Opção | Descrição |
| --- | --- |
| Origem |Especifica a localização dos dados de origem na bolha de armazenamento Azure. A fonte pode ser um recipiente de bolhas, uma bolha ou outra conta gen1 de armazenamento de data lake. |
| Dest |Especifica o destino Data Lake Storage Gen1 para copiar. |
| FonteChave |Especifica a chave de acesso ao armazenamento para a fonte de armazenamento azure blob. Isto só é necessário se a fonte for um recipiente de bolhas ou uma bolha. |
| Conta |**Opcional.** Use isto se quiser utilizar a conta Azure Data Lake Analytics para executar o trabalho de cópia. Se utilizar a opção /Conta na sintaxe, mas não especificar uma conta Data Lake Analytics, a AdlCopy utiliza uma conta predefinida para executar o trabalho. Além disso, se utilizar esta opção, deve adicionar a fonte (Blob de Armazenamento Azure) e o destino (Azure Data Lake Storage Gen1) como fontes de dados para a sua conta Data Lake Analytics. |
| Unidades |Especifica o número de unidades data Lake Analytics que serão usadas para o trabalho de cópia. Esta opção é obrigatória se utilizar a opção **/Conta** para especificar a conta Data Lake Analytics. |
| Padrão |Especifica um padrão regex que indica quais bolhas ou ficheiros copiar. O AdlCopy utiliza correspondência sensível a casos. O padrão predefinido quando não é especificado nenhum padrão é copiar todos os itens. Especificar vários padrões de ficheiros não é suportado. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Utilize o AdlCopy (como autónomo) para copiar dados de uma bolha de armazenamento azure

1. Abra um pedido de comando e navegue para o diretório `%HOMEPATH%\Documents\adlcopy`onde o AdlCopy é instalado, tipicamente .
1. Executar o seguinte comando para copiar uma bolha específica do recipiente de origem para uma pasta Gen1 de Armazenamento de Data Lake:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Por exemplo:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[!NOTE]
    >A sintaxe acima especifica o ficheiro a ser copiado para uma pasta na conta Data Lake Storage Gen1. A ferramenta AdlCopy cria uma pasta se o nome da pasta especificado não existir.

    Será solicitado a introduzir as credenciais para a subscrição Azure sob a qual tem a sua conta Data Lake Storage Gen1. Verá uma saída semelhante à seguinte:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. Também pode copiar todas as bolhas de um recipiente para a conta Data Lake Storage Gen1 utilizando o seguinte comando:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Por exemplo:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>Considerações de desempenho

Se estiver a copiar a partir de uma conta de Armazenamento Azure Blob, poderá ser estrangulado durante a cópia do lado do armazenamento da bolha. Isto vai degradar o desempenho do teu trabalho de cópia. Para saber mais sobre os limites do Armazenamento De Blob Azure, consulte os limites de armazenamento do Azure nos limites de [subscrição e serviço do Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Utilize o AdlCopy (como autónomo) para copiar dados de outra conta gen1 de armazenamento de data Lake

Também pode utilizar o AdlCopy para copiar dados entre duas contas da Gen1 de Armazenamento de Data Lake.

1. Abra um pedido de comando e navegue para o diretório `%HOMEPATH%\Documents\adlcopy`onde o AdlCopy é instalado, tipicamente .
1. Execute o seguinte comando para copiar um ficheiro específico de uma conta Gen1 de Armazenamento de Data Lake para outra.

        AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/

    Por exemplo:

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > A sintaxe acima especifica o ficheiro a ser copiado para uma pasta na conta Destination Data Lake Storage Gen1. A ferramenta AdlCopy cria uma pasta se o nome da pasta especificado não existir.
   >
   >

    Será solicitado a introduzir as credenciais para a subscrição Azure sob a qual tem a sua conta Data Lake Storage Gen1. Verá uma saída semelhante à seguinte:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
1. O comando seguinte copia todos os ficheiros de uma pasta específica na conta Source Data Lake Storage Gen1 para uma pasta na conta Destination Data Lake Storage Gen1.

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>Considerações de desempenho

Ao utilizar o AdlCopy como uma ferramenta autónoma, a cópia é executada em recursos partilhados e geridos pelo Azure. O desempenho que você pode obter neste ambiente depende da carga do sistema e dos recursos disponíveis. Este modo é melhor utilizado para pequenas transferências numa base ad hoc. Não é necessário sintonizar os parâmetros ao utilizar o AdlCopy como ferramenta autónoma.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Utilize o AdlCopy (com conta Data Lake Analytics) para copiar dados

Também pode utilizar a sua conta Data Lake Analytics para executar o trabalho da AdlCopy para copiar dados de blobs de armazenamento Azure para Data Lake Storage Gen1. Normalmente, usaria esta opção quando os dados a serem movidos estão na gama de gigabytes e terabytes, e você quer uma melhor e previsível entrada de desempenho.

Para utilizar a sua conta Data Lake Analytics com a AdlCopy para copiar a partir de um Blob de Armazenamento Azure, a fonte (Blob de Armazenamento Azure) deve ser adicionada como fonte de dados para a sua conta Data Lake Analytics. Para obter instruções sobre a adição de fontes de dados adicionais à sua conta Data Lake Analytics, consulte [Gerir as fontes](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources)de dados da conta data Lake Analytics .

> [!NOTE]
> Se estiver a copiar a partir de uma conta Azure Data Lake Storage Gen1 como fonte que utiliza uma conta Data Lake Analytics, não precisa de associar a conta Data Lake Storage Gen1 à conta Data Lake Analytics. A exigência de associar a loja de origem à conta Data Lake Analytics é apenas quando a fonte é uma conta de Armazenamento Azure.
>
>

Executar o seguinte comando para copiar de uma bolha de armazenamento Azure para uma conta Gen1 de Armazenamento de Data Lake usando a conta Data Lake Analytics:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>

Por exemplo:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

Da mesma forma, execute o seguinte comando para copiar todos os ficheiros de uma pasta específica na conta source Data Lake Storage Gen1 para uma pasta na conta destination Data Lake Storage Gen1 usando a conta Data Lake Analytics:

    AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>Considerações de desempenho

Ao copiar dados na gama de terabytes, a utilização do AdlCopy com a sua própria conta Azure Data Lake Analytics proporciona um desempenho melhor e mais previsível. O parâmetro que deve ser afinado é o número de Unidades de Análise do Lago de Dados Azure para usar para o trabalho de cópia. Aumentar o número de unidades aumentará o desempenho do seu trabalho de cópia. Cada ficheiro a copiar pode utilizar uma unidade máxima. Especificar mais unidades do que o número de ficheiros copiados não aumentará o desempenho.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Utilize o AdlCopy para copiar dados usando a correspondência de padrões

Nesta secção, aprende-se a usar o AdlCopy para copiar dados de uma fonte (no nosso exemplo abaixo utilizamos o Azure Storage Blob) para uma conta de destino Data Lake Storage Gen1 utilizando a correspondência de padrões. Por exemplo, pode utilizar os passos abaixo para copiar todos os ficheiros com extensão .csv da bolha de origem para o destino.

1. Abra um pedido de comando e navegue para o diretório `%HOMEPATH%\Documents\adlcopy`onde o AdlCopy é instalado, tipicamente .
1. Executar o seguinte comando para copiar todos os ficheiros com extensão *.csv de uma bolha específica do recipiente de origem para uma pasta Gen1 de Armazenamento de Data Lake:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Por exemplo:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Faturação

* Se utilizar a ferramenta AdlCopy como autónoma, será cobrado pelos custos de saída para a movimentação de dados, se a conta de armazenamento de origem Azure não estiver na mesma região que a conta Data Lake Storage Gen1.
* Se utilizar a ferramenta AdlCopy com a sua conta Data Lake Analytics, aplicar-se-ão as taxas padrão de faturação do [Data Lake Analytics.](https://azure.microsoft.com/pricing/details/data-lake-analytics/)

## <a name="considerations-for-using-adlcopy"></a>Considerações para a utilização do AdlCopy

* O AdlCopy (para a versão 1.0.5), suporta a cópia de dados de fontes que colectivamente têm mais de milhares de ficheiros e pastas. No entanto, se encontrar problemas que copiem um grande conjunto de dados, pode distribuir os ficheiros/pastas em diferentes subpastas e utilizar o caminho para essas subpastas como fonte.

## <a name="performance-considerations-for-using-adlcopy"></a>Considerações de desempenho para a utilização do AdlCopy

O AdlCopy suporta a cópia de dados que contêm milhares de ficheiros e pastas. No entanto, se encontrar problemas que copiem um grande conjunto de dados, pode distribuir os ficheiros/pastas em subpastas mais pequenas. AdlCopy foi construída para cópias ad hoc. Se está a tentar copiar dados de forma recorrente, deve considerar a utilização [da Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) que fornece uma gestão completa em torno das operações de cópia.

## <a name="release-notes"></a>Notas de versão

* 1.0.13 - Se estiver a copiar dados para a mesma conta De armazenamento de dados Do Lago Azure Gen1 através de vários comandos de adlcopy, já não precisa de reintroduzir as suas credenciais para cada execução. A adlcopy irá agora cache essa informação em várias corridas.

## <a name="next-steps"></a>Passos seguintes

* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Use Azure Data Lake Analytics com Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
