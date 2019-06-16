---
title: Tolerância a atividade de cópia no Azure Data Factory falhas | Documentos da Microsoft
description: Saiba mais sobre como adicionar a tolerância a falhas para a atividade de cópia no Azure Data Factory por ignorar as linhas incompatíveis.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: yexu
ms.openlocfilehash: ef0bb3716a32a0f25b90e74bc44d7291c146b431
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60808824"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Tolerância a falhas da atividade de cópia no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory, que está a utilizar:"]
> * [Versão 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Versão atual](copy-activity-fault-tolerance.md)

A atividade de cópia no Azure Data Factory oferece duas formas de lidar com linhas incompatíveis durante a cópia de dados entre arquivos de dados de origem e sink:

- Pode abortar e falhar a cópia de atividade, quando os dados incompatíveis encontrou (comportamento predefinido).
- Pode continuar copiar todos os dados ao adicionar a tolerância a falhas e Ignorar linhas de dados incompatíveis. Além disso, pode iniciar as linhas incompatíveis no armazenamento de Blobs do Azure ou no Azure Data Lake Store. Em seguida, pode examinar o registo para obter a causa da falha, corrigir os dados na origem de dados e repita a atividade de cópia.

## <a name="supported-scenarios"></a>Cenários suportados
Atividade de cópia suporta três cenários para detetar, a ignorar e registo de dados incompatíveis:

- **Incompatibilidade entre o tipo de dados de origem e sink obsahující nativní typ**. 

    Por exemplo: Copie dados de um ficheiro CSV no armazenamento de BLOBs para base de dados SQL com uma definição de esquema que contém três colunas de tipo INT. As linhas do ficheiro CSV que contêm dados numéricos, como 123,456,789 são copiadas com êxito para o arquivo sink. No entanto, as linhas que contêm valores não numéricos, como 123,456, abc são detetados como incompatíveis e são ignorados.

- **Erro de correspondência no número de colunas entre a origem e o sink**.

    Por exemplo: Copie dados de um ficheiro CSV no armazenamento de BLOBs para base de dados SQL com uma definição de esquema que contém seis colunas. As linhas do ficheiro CSV que contêm seis colunas são copiadas com êxito para o arquivo sink. As linhas do ficheiro CSV que contêm mais ou menos seis colunas são detetadas como incompatíveis e são ignoradas.

- **Violação de chave primária ao escrever para o SQL Server/Azure SQL da base de dados/do Azure Cosmos DB**.

    Por exemplo: Copie dados de um SQL server para uma base de dados SQL. Uma chave primária é definida na base de dados do SQL sink, mas a chave não principal está definido no servidor SQL de origem. Não não possível copiar as linhas duplicadas que existam na origem para o sink. Atividade de cópia copia apenas a primeira linha dos dados de origem para o sink. As linhas de origem subsequentes que contêm o valor da chave primário duplicado são detetadas como incompatíveis e são ignoradas.

>[!NOTE]
>- Para carregar dados para o SQL Data Warehouse com o PolyBase, configurar as definições de tolerância de falhas nativo do PolyBase ao especificar políticas de rejeitar através de "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" na atividade de cópia. Ainda pode ativar o redirecionamento PolyBase de linhas incompatíveis para Blob ou ADLS como normal, conforme mostrado abaixo.
>- Esta funcionalidade não se aplica quando a atividade de cópia está configurada para invocar [descarregamento do Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).


## <a name="configuration"></a>Configuração
O exemplo a seguir fornece uma definição de JSON para configurar a ignorar as linhas incompatíveis na atividade de cópia:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Especifica se pretende ignorar linhas incompatíveis durante a cópia ou não. | Verdadeiro<br/>FALSE (predefinição) | Não
redirectIncompatibleRowSettings | Um grupo de propriedades que podem ser especificados quando pretende registar linhas incompatíveis. | &nbsp; | Não
linkedServiceName | Serviço ligado do [armazenamento do Azure](connector-azure-blob-storage.md#linked-service-properties) ou [do Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) para armazenar o registo que contém as linhas ignoradas. | O nome de um `AzureStorage` ou `AzureDataLakeStore` tipo de serviço ligado, o que se refere à instância que pretende utilizar para armazenar o ficheiro de registo. | Não
caminho | O caminho do ficheiro de registo que contém as linhas ignoradas. | Especifique o caminho que pretende utilizar para iniciar sessão os dados incompatíveis. Se não fornecer um caminho, o serviço cria um contentor para. | Não

## <a name="monitor-skipped-rows"></a>Linhas de monitor ignorada
Depois de concluída a execução de atividade de cópia, pode ver o número de linhas ignorados na saída da atividade de cópia:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Se o configurar para iniciar as linhas incompatíveis, pode encontrar o ficheiro de registo neste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Os ficheiros de registo só podem ser os ficheiros csv. Os dados originais, que está a ser ignorados serão registados com vírgula como delimitador de colunas se for necessário. Iremos adicionar mais duas colunas "ErrorCode" e "ErrorMessage" no adicionais para a origem de dados original no ficheiro de registo, onde pode ver a raiz causa da incompatibilidade. O código de erro e ErrorMessage irão estar escritos entre aspas por aspas duplas. 

Um exemplo do conteúdo do ficheiro de registo é o seguinte:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Passos Seguintes
Consulte os outros artigos de atividade de cópia:

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Desempenho de atividade de cópia](copy-activity-performance.md)


