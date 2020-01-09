---
title: Copiar dados de blobs de armazenamento do Azure para Data Lake Storage Gen1
description: Use a ferramenta AdlCopy para copiar dados de blobs de armazenamento do Azure para Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: ad408df140be49da2e50ef810285dd850e9da6a1
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638874"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Copiar dados de blobs de armazenamento do Azure para Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Utilizar o DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Utilizar o AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Data Lake Storage Gen1 fornece uma ferramenta de linha de comando, [AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358), para copiar dados das seguintes fontes:

* De blobs de armazenamento do Azure para Data Lake Storage Gen1. Você não pode usar AdlCopy para copiar dados de Data Lake Storage Gen1 para os blobs de armazenamento do Azure.
* Entre duas contas de Data Lake Storage Gen1.

Além disso, você pode usar a ferramenta AdlCopy em dois modos diferentes:

* **Autônomo**, em que a ferramenta usa data Lake Storage Gen1 recursos para executar a tarefa.
* **Usando uma conta de data Lake Analytics**, em que as unidades atribuídas à sua conta de data Lake Analytics são usadas para executar a operação de cópia. Talvez você queira usar essa opção quando estiver procurando executar as tarefas de cópia de maneira previsível.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Contêiner de **blobs de armazenamento do Azure** com alguns dados.
* **Uma conta de data Lake Storage Gen1**. Para obter instruções sobre como criar uma, consulte Introdução [ao Azure data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Conta de data Lake Analytics (opcional)** -consulte [introdução ao Azure data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) para obter instruções sobre como criar uma conta de data Lake Analytics.
* **Ferramenta AdlCopy**. Instale a [ferramenta AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358).

## <a name="syntax-of-the-adlcopy-tool"></a>Sintaxe da ferramenta AdlCopy

Use a sintaxe a seguir para trabalhar com a ferramenta AdlCopy

    AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern

Os parâmetros na sintaxe são descritos abaixo:

| Opção | Descrição |
| --- | --- |
| Origem |Especifica o local dos dados de origem no blob de armazenamento do Azure. A origem pode ser um contêiner de BLOB, um BLOB ou outra conta de Data Lake Storage Gen1. |
| Dest |Especifica o destino do Data Lake Storage Gen1 para copiar. |
| SourceKey |Especifica a chave de acesso de armazenamento para a origem do blob de armazenamento do Azure. Isso será necessário apenas se a origem for um contêiner de BLOB ou um blob. |
| Conta |**Opcional**. Use essa função se você quiser usar Azure Data Lake Analytics conta para executar o trabalho de cópia. Se você usar a opção/Account na sintaxe, mas não especificar uma conta de Data Lake Analytics, o AdlCopy usará uma conta padrão para executar o trabalho. Além disso, se você usar essa opção, deverá adicionar a origem (Azure Storage Blob) e o destino (Azure Data Lake Storage Gen1) como fontes de dados para sua conta de Data Lake Analytics. |
| Unidades |Especifica o número de unidades de Data Lake Analytics que serão usadas para o trabalho de cópia. Essa opção será obrigatória se você usar a opção **/Account** para especificar a conta de data Lake Analytics. |
| Padrão |Especifica um padrão Regex que indica quais BLOBs ou arquivos a serem copiados. AdlCopy usa correspondência que diferencia maiúsculas de minúsculas. O padrão quando nenhum padrão é especificado é copiar todos os itens. Não há suporte para a especificação de vários padrões de arquivo. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Usar AdlCopy (como autônomo) para copiar dados de um blob de armazenamento do Azure

1. Abra um prompt de comando e navegue até o diretório em que o AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.
1. Execute o seguinte comando para copiar um blob específico do contêiner de origem para uma pasta Data Lake Storage Gen1:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Por exemplo:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[!NOTE]
    >A sintaxe acima Especifica o arquivo a ser copiado para uma pasta na conta de Data Lake Storage Gen1. A ferramenta AdlCopy criará uma pasta se o nome de pasta especificado não existir.

    Você será solicitado a inserir as credenciais para a assinatura do Azure sob a qual você tem sua conta de Data Lake Storage Gen1. Você verá uma saída semelhante à seguinte:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. Você também pode copiar todos os blobs de um contêiner para a conta de Data Lake Storage Gen1 usando o seguinte comando:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Por exemplo:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>Considerações de desempenho

Se você estiver copiando de uma conta de armazenamento de BLOBs do Azure, você poderá ser limitado durante a cópia no lado do armazenamento de BLOBs. Isso diminuirá o desempenho do seu trabalho de cópia. Para saber mais sobre os limites do armazenamento de BLOBs do Azure, confira limites de armazenamento do Azure em [assinatura do Azure e limites de serviço](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Usar AdlCopy (como autônomo) para copiar dados de outra conta de Data Lake Storage Gen1

Você também pode usar AdlCopy para copiar dados entre duas contas de Data Lake Storage Gen1.

1. Abra um prompt de comando e navegue até o diretório em que o AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.
1. Execute o comando a seguir para copiar um arquivo específico de uma conta de Data Lake Storage Gen1 para outra.

        AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/

    Por exemplo:

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > A sintaxe acima Especifica o arquivo a ser copiado para uma pasta na conta de Data Lake Storage Gen1 de destino. A ferramenta AdlCopy criará uma pasta se o nome de pasta especificado não existir.
   >
   >

    Você será solicitado a inserir as credenciais para a assinatura do Azure sob a qual você tem sua conta de Data Lake Storage Gen1. Você verá uma saída semelhante à seguinte:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
1. O comando a seguir copia todos os arquivos de uma pasta específica na conta de Data Lake Storage Gen1 de origem para uma pasta na conta de Data Lake Storage Gen1 de destino.

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>Considerações de desempenho

Ao usar o AdlCopy como uma ferramenta autônoma, a cópia é executada em recursos compartilhados e gerenciados pelo Azure. O desempenho que você pode obter nesse ambiente depende da carga do sistema e dos recursos disponíveis. Esse modo é melhor usado para transferências pequenas em uma base ad hoc. Nenhum parâmetro precisa ser ajustado ao usar o AdlCopy como uma ferramenta autônoma.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Usar AdlCopy (com a conta de Data Lake Analytics) para copiar dados

Você também pode usar sua conta de Data Lake Analytics para executar o trabalho AdlCopy para copiar dados de blobs de armazenamento do Azure para Data Lake Storage Gen1. Normalmente, você usaria essa opção quando os dados a serem movidos estiverem no intervalo de gigabytes e terabytes e você quiser uma taxa de transferência de desempenho melhor e previsível.

Para usar sua conta de Data Lake Analytics com AdlCopy para copiar de um Azure Storage Blob, a origem (Azure Storage Blob) deve ser adicionada como uma fonte de dados para sua conta de Data Lake Analytics. Para obter instruções sobre como adicionar fontes de dados adicionais à sua conta do Data Lake Analytics, consulte [gerenciar Data Lake Analytics fontes de dados da conta](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Se você estiver copiando de uma conta de Azure Data Lake Storage Gen1 como a origem usando uma conta de Data Lake Analytics, não será necessário associar a conta de Data Lake Storage Gen1 à conta de Data Lake Analytics. O requisito para associar o repositório de origem ao Data Lake Analytics conta é apenas quando a origem é uma conta de armazenamento do Azure.
>
>

Execute o seguinte comando para copiar de um blob de armazenamento do Azure para uma conta de Data Lake Storage Gen1 usando a conta Data Lake Analytics:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>

Por exemplo:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

Da mesma forma, execute o seguinte comando para copiar todos os arquivos de uma pasta específica na conta de Data Lake Storage Gen1 de origem para uma pasta na conta de Data Lake Storage Gen1 de destino usando a conta de Data Lake Analytics:

    AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>Considerações de desempenho

Ao copiar dados no intervalo de terabytes, usar o AdlCopy com sua própria conta de Azure Data Lake Analytics fornece um desempenho melhor e mais previsível. O parâmetro que deve ser ajustado é o número de unidades de Azure Data Lake Analytics a ser usado para o trabalho de cópia. Aumentar o número de unidades aumentará o desempenho do seu trabalho de cópia. Cada arquivo a ser copiado pode usar no máximo uma unidade. A especificação de mais unidades do que o número de arquivos que estão sendo copiados não aumentará o desempenho.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Usar AdlCopy para copiar dados usando correspondência de padrões

Nesta seção, você aprenderá a usar o AdlCopy para copiar dados de uma fonte (em nosso exemplo abaixo, usamos Azure Storage Blob) para uma conta de Data Lake Storage Gen1 de destino usando a correspondência de padrões. Por exemplo, você pode usar as etapas abaixo para copiar todos os arquivos com a extensão. csv do blob de origem para o destino.

1. Abra um prompt de comando e navegue até o diretório em que o AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.
1. Execute o comando a seguir para copiar todos os arquivos com a extensão *. csv de um blob específico do contêiner de origem para uma pasta Data Lake Storage Gen1:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Por exemplo:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Faturação

* Se você usar a ferramenta AdlCopy como autônoma, você será cobrado pelos custos de egresso para mover os dados, se a conta de armazenamento do Azure de origem não estiver na mesma região que a conta de Data Lake Storage Gen1.
* Se você usar a ferramenta AdlCopy com sua conta do Data Lake Analytics, as [tarifas de cobrança padrão data Lake Analytics](https://azure.microsoft.com/pricing/details/data-lake-analytics/) serão aplicadas.

## <a name="considerations-for-using-adlcopy"></a>Considerações sobre o uso de AdlCopy

* AdlCopy (para a versão 1.0.5), o oferece suporte à cópia de dados de fontes que coletivamente têm mais de milhares de arquivos e pastas. No entanto, se você encontrar problemas ao copiar um grande conjunto de grandes, poderá distribuir os arquivos/pastas em diferentes subpastas e usar o caminho para essas subpastas como a origem.

## <a name="performance-considerations-for-using-adlcopy"></a>Considerações sobre desempenho para usar o AdlCopy

O AdlCopy dá suporte à cópia de dados que contém milhares de arquivos e pastas. No entanto, se você encontrar problemas ao copiar um grande conjunto de grandes, poderá distribuir os arquivos/pastas em subpastas menores. O AdlCopy foi criado para cópias ad hoc. Se você estiver tentando copiar dados de forma recorrente, considere o uso de [Azure data Factory](../data-factory/connector-azure-data-lake-store.md) que fornece gerenciamento completo em relação às operações de cópia.

## <a name="release-notes"></a>Notas de versão

* 1.0.13 – se você estiver copiando dados para a mesma conta de Azure Data Lake Storage Gen1 em vários comandos do adlcopy, não será necessário reinserir suas credenciais para cada execução. Agora, o Adlcopy armazenará em cache essas informações em várias execuções.

## <a name="next-steps"></a>Passos seguintes

* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Usar Azure Data Lake Analytics com Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
