---
title: Copiar dados dos Blobs de armazenamento do Azure para a geração 1 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Utilize a ferramenta de AdlCopy para copiar dados dos Blobs de armazenamento do Azure para geração 1 de armazenamento do Azure Data Lake
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: dc273ef8-96ef-47a6-b831-98e8a777a5c1
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 5049e280b65a9f223ea577ee4fde973657682b4c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544651"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Copiar dados dos Blobs de armazenamento do Azure para a geração 1 de armazenamento do Azure Data Lake
> [!div class="op_single_selector"]
> * [Utilizar o DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Utilizar o AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Geração de armazenamento 1 do Azure Data Lake fornece uma ferramenta de linha de comandos [AdlCopy](https://aka.ms/downloadadlcopy)para copiar os dados das seguintes origens:

* Dos Blobs do armazenamento do Azure para o Data Lake Storage Gen1. Não é possível utilizar o AdlCopy para copiar dados de geração 1 do Data Lake armazenamento para blobs de armazenamento do Azure.
* Entre duas contas de geração 1 de armazenamento do Azure Data Lake.

Além disso, pode utilizar a ferramenta de AdlCopy em dois modos diferentes:

* **Autónomo**, onde a ferramenta utiliza recursos de geração 1 de armazenamento do Data Lake para executar a tarefa.
* **Utilizar uma conta do Data Lake Analytics**, onde as unidades atribuídas à sua conta do Data Lake Analytics são utilizadas para efetuar a operação de cópia. Pode querer utilizar esta opção quando quiser para executar as tarefas de cópia de maneira previsível.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Os Blobs de armazenamento do Azure** contentor com alguns dados.
* **Uma conta de geração 1 de armazenamento do Azure Data Lake**. Para obter instruções sobre como criar um, consulte [introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **(Opcional) da conta do Azure Data Lake Analytics** -veja [introdução ao Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) para obter instruções sobre como criar uma conta do Data Lake Analytics.
* **Ferramenta de AdlCopy**. Instalar a ferramenta AdlCopy [ https://aka.ms/downloadadlcopy ](https://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Sintaxe da ferramenta AdlCopy
Utilize a seguinte sintaxe para trabalhar com a ferramenta de AdlCopy

    AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern

Os parâmetros na sintaxe são descritos abaixo:

| Opção | Descrição |
| --- | --- |
| Origem |Especifica a localização dos dados de origem no blob de armazenamento do Azure. A origem pode ser um contentor de BLOBs, um blob ou outra conta de geração 1 de armazenamento do Data Lake. |
| Dest |Especifica o destino de geração 1 de armazenamento do Data Lake para copiar para. |
| SourceKey |Especifica a chave de acesso de armazenamento para a origem do blob de armazenamento do Azure. Isto é necessário apenas se a origem é um contentor de BLOBs ou um blob. |
| Conta |**Opcional**. Utilize esta opção se pretender utilizar a conta do Azure Data Lake Analytics para executar a tarefa de cópia. Se utilizar a opção de /Account na sintaxe, mas não especificar uma conta do Data Lake Analytics, AdlCopy utiliza uma conta predefinida para executar a tarefa. Além disso, se utilizar esta opção, tem de adicionar a origem (Blob de armazenamento do Azure) e o destino (Azure Data Lake Storage Gen1) como fontes de dados para a sua conta do Data Lake Analytics. |
| Unidades |Especifica o número de unidades do Data Lake Analytics, que será utilizado para a tarefa de cópia. Esta opção é obrigatória se utilizar o **/conta** opção para especificar a conta do Data Lake Analytics. |
| Padrão |Especifica um padrão de regex que indica que blobs ou ficheiros para copiar. AdlCopy utiliza a correspondência de maiúsculas e minúsculas. O padrão predefinido utilizado quando não é especificado nenhum padrão é copiar todos os itens. Especificar vários padrões de ficheiro não é suportado. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Utilizar o AdlCopy para copiar dados de um blob de armazenamento do Azure (como autónomo)
1. Abra uma linha de comandos e navegue para o diretório onde o AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.
2. Execute o seguinte comando para copiar um blob específico do contentor de origem para uma pasta de geração 1 de armazenamento do Data Lake:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Por exemplo:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[!NOTE] 
    >A sintaxe acima Especifica o ficheiro ser copiados para uma pasta na conta de geração 1 de armazenamento do Data Lake. Ferramenta de AdlCopy cria uma pasta, se o nome da pasta especificado não existe.

    Será solicitado para introduzir as credenciais para a subscrição do Azure sob a qual tem a conta de geração 1 de armazenamento do Data Lake. Verá um resultado semelhante ao seguinte:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. Também pode copiar todos os blobs a partir de um contentor para a conta de geração 1 de armazenamento do Data Lake com o seguinte comando:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Por exemplo:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>Considerações de desempenho

Se estiver a copiar a partir de uma conta de armazenamento de Blobs do Azure, poderá ser limitada durante a cópia do lado do armazenamento de Blobs. Isto irá degradar o desempenho da sua tarefa de cópia. Para saber mais sobre os limites do armazenamento de Blobs do Azure, veja os limites de armazenamento do Azure na [subscrição do Azure e limites do serviço](../azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Utilizar o AdlCopy para copiar dados a partir de outra conta do Data Lake Storage Gen1 (como autónomo)
Também pode utilizar o AdlCopy para copiar dados entre duas contas de geração 1 de armazenamento do Data Lake.

1. Abra uma linha de comandos e navegue para o diretório onde o AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.
2. Execute o seguinte comando para copiar um ficheiro específico de uma conta de geração 1 de armazenamento do Data Lake para outro.

        AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/

    Por exemplo:

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > A sintaxe acima Especifica o ficheiro ser copiados para uma pasta no destino da conta de geração 1 de armazenamento do Data Lake. Ferramenta de AdlCopy cria uma pasta, se o nome da pasta especificado não existe.
   >
   >

    Será solicitado para introduzir as credenciais para a subscrição do Azure sob a qual tem a conta de geração 1 de armazenamento do Data Lake. Verá um resultado semelhante ao seguinte:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
3. O seguinte comando copia todos os ficheiros de uma pasta específica na origem de conta de geração 1 de armazenamento do Data Lake para uma pasta no destino da conta de geração 1 de armazenamento do Data Lake.

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>Considerações de desempenho

Quando utilizar o AdlCopy como uma ferramenta autônoma, a cópia é executada no partilhado, recursos gerenciados do Azure. O desempenho que poderá receber neste ambiente depende da carga de sistema e os recursos disponíveis. Este modo melhor é usado para transferências pequeno num ad hoc com. Sem parâmetros têm de ser ajustado quando utilizar o AdlCopy como uma ferramenta autônoma.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Utilizar o AdlCopy para copiar dados (com a conta do Data Lake Analytics)
Também pode utilizar a conta do Data Lake Analytics para executar a tarefa de AdlCopy para copiar dados de blobs de armazenamento do Azure para a geração 1 de armazenamento do Data Lake. Normalmente usaria esta opção quando os dados sejam movidos estão no intervalo de gigabytes e terabytes e pretende que o débito de um desempenho previsível e melhor.

Para utilizar a sua conta do Data Lake Analytics com o AdlCopy para copiar de um Blob de armazenamento do Azure, a origem (Blob de armazenamento do Azure) tem de ser adicionada como uma origem de dados para a sua conta do Data Lake Analytics. Para obter instruções sobre como adicionar origens de dados adicionais à sua conta do Data Lake Analytics, consulte [origens de dados de contas a gerir o Data Lake Analytics](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Se estiver a copiar de uma conta de geração 1 de armazenamento do Azure Data Lake como a origem com uma conta do Data Lake Analytics, não é necessário associar a conta de geração 1 de armazenamento do Data Lake com a conta do Data Lake Analytics. O requisito para associar o arquivo de origem com a conta do Data Lake Analytics é apenas quando a origem é uma conta de armazenamento do Azure.
>
>

Execute o seguinte comando para copiar de um blob de armazenamento do Azure para uma conta de geração 1 de armazenamento do Data Lake com a conta do Data Lake Analytics:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>

Por exemplo:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

Da mesma forma, execute o seguinte comando para copiar todos os ficheiros de uma pasta específica na origem de conta de geração 1 de armazenamento do Data Lake para uma pasta na conta de geração 1 do Data Lake armazenamento de destino usando a conta do Data Lake Analytics:

    AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>Considerações de desempenho

Quando se copiam dados na ordem dos terabytes, utilizar o AdlCopy com a sua própria conta do Azure Data Lake Analytics proporciona um desempenho melhor e mais previsível. O parâmetro que deve ser ajustado é o número de unidades do Azure Data Lake Analytics a utilizar para a tarefa de cópia. Aumentar o número de unidades irá aumentar o desempenho da sua tarefa de cópia. Cada ficheiro a ser copiado, pode utilizar uma unidade de máxima. Especificação de mais unidades de que o número de ficheiros que está a ser copiados não irá aumentar o desempenho.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Utilizar o AdlCopy para copiar dados através da correspondência de padrões
Nesta secção, irá aprender a utilizar o AdlCopy para copiar dados de uma origem (no nosso exemplo abaixo, utilize BLOBs de armazenamento do Azure) para uma conta de destino Gen1 de armazenamento do Data Lake com a correspondência de padrões. Por exemplo, pode utilizar os passos abaixo para copiar todos os ficheiros com extensão. csv de blob de origem para o destino.

1. Abra uma linha de comandos e navegue para o diretório onde o AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.
2. Execute o seguinte comando para copiar todos os ficheiros com a extensão *. csv de um blob específico a partir do contentor de origem para uma pasta de geração 1 de armazenamento do Data Lake:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Por exemplo:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Faturação
* Se usar a ferramenta de AdlCopy como autónoma, será cobrada os custos de saída para mover dados, se a conta de armazenamento do Azure de origem não estiver na mesma região que a conta de geração 1 de armazenamento do Data Lake.
* Se usar a ferramenta de AdlCopy com o Data Lake Analytics de contas, standard [preços de cobrança do Data Lake Analytics](https://azure.microsoft.com/pricing/details/data-lake-analytics/) será aplicada.

## <a name="considerations-for-using-adlcopy"></a>Considerações para utilizar o AdlCopy
* AdlCopy (para a versão 1.0.5), oferece suporte a cópia de dados de origens coletivamente com mais de milhares de arquivos e pastas. No entanto, se tiver problemas com copiar um conjunto de dados grandes, pode distribuir as pastas/ficheiros em subpastas diferentes e utilizar o caminho para as subpastas como a origem em vez disso.

## <a name="performance-considerations-for-using-adlcopy"></a>Considerações de desempenho para utilizar o AdlCopy

AdlCopy suporta a cópia de dados que contêm milhares de arquivos e pastas. No entanto, se tiver problemas com copiar um conjunto de dados grandes, pode distribuir as pastas/ficheiros em subpastas menores. AdlCopy foi criado para cópias de ad hoc. Se estiver a tentar copiar dados de forma recorrente, deve considerar o uso [do Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) que fornece uma gestão completa em torno as operações de cópia.

## <a name="release-notes"></a>Notas de versão
* 1.0.13 - se estiver a copiar dados para a mesma conta de geração 1 de armazenamento do Azure Data Lake em vários comandos de adlcopy, não terá de reintroduzir as suas credenciais para cada execução deixa de poder. Adlcopy agora colocará em cache essas informações nas várias execuções.

## <a name="next-steps"></a>Passos Seguintes
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utilizar o Azure HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
