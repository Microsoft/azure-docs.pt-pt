---
title: Tolerância a falhas da atividade de cópia no Azure Data Factory
description: Saiba mais sobre como adicionar tolerância a falhas à atividade de cópia no Azure Data Factory ignorando as linhas incompatíveis.
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
ms.openlocfilehash: a60cafd529db1c6726a15db2c442af8d097411cc
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678164"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Tolerância a falhas da atividade de cópia no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Versão atual](copy-activity-fault-tolerance.md)

A atividade de cópia no Azure Data Factory oferece duas maneiras de lidar com linhas incompatíveis ao copiar dados entre armazenamentos de dados de origem e de coletor:

- Você pode anular e reprovar a atividade de cópia quando dados incompatíveis forem encontrados (comportamento padrão).
- Você pode continuar copiando todos os dados adicionando tolerância a falhas e ignorando linhas de dados incompatíveis. Além disso, você pode registrar em log as linhas incompatíveis no armazenamento de BLOBs do Azure ou Azure Data Lake Store. Em seguida, você pode examinar o log para saber a causa da falha, corrigir os dados na fonte de dados e repetir a atividade de cópia.

## <a name="supported-scenarios"></a>Cenários suportados
A atividade de cópia dá suporte a três cenários para detectar, ignorar e registrar em log dados incompatíveis:

- **Incompatibilidade entre o tipo de dados de origem e o tipo nativo do coletor**. 

    Por exemplo: copiar dados de um arquivo CSV no armazenamento de BLOBs para um banco de dado SQL com uma definição de esquema que contém três colunas de tipo INT. As linhas do arquivo CSV que contêm dados numéricos, como 123.456.789, são copiadas com êxito no repositório de coletor. No entanto, as linhas que contêm valores não numéricos, como 123.456, ABC são detectadas como incompatíveis e ignoradas.

- **Incompatibilidade no número de colunas entre a origem e o coletor**.

    Por exemplo: copiar dados de um arquivo CSV em um armazenamento de BLOBs para um banco de dados SQL com uma definição de esquema que contém seis colunas. As linhas do arquivo CSV que contêm seis colunas são copiadas com êxito no repositório de coletor. As linhas do arquivo CSV que contêm mais ou menos de seis colunas são detectadas como incompatíveis e ignoradas.

- **Violação de chave primária ao gravar em SQL Server/banco de dados SQL/Azure Cosmos DB do Azure**.

    Por exemplo: copiar dados de um SQL Server para um banco de dados SQL. Uma chave primária é definida no banco de dados SQL do coletor, mas nenhuma chave primária é definida no SQL Server de origem. As linhas duplicadas que existem na fonte não podem ser copiadas para o coletor. A atividade de cópia copia apenas a primeira linha dos dados de origem no coletor. As linhas de origem subsequentes que contêm o valor duplicado da chave primária são detectadas como incompatíveis e ignoradas.

>[!NOTE]
>- Para carregar dados em SQL Data Warehouse usando o polybase, defina as configurações nativas de tolerância a falhas do polybase especificando políticas de rejeição por meio de "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" na atividade de cópia. Você ainda pode habilitar o redirecionamento de linhas incompatíveis do polybase para BLOB ou ADLS normalmente, conforme mostrado abaixo.
>- Este recurso não se aplica quando a atividade de cópia está configurada para invocar o [descarregamento do Amazon redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Esse recurso não se aplica quando a atividade de cópia está configurada para invocar um [procedimento armazenado de um coletor SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink).

## <a name="configuration"></a>Configuração
O exemplo a seguir fornece uma definição de JSON para configurar ignorando as linhas incompatíveis na atividade de cópia:

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
enableSkipIncompatibleRow | Especifica se as linhas incompatíveis devem ser ignoradas durante a cópia ou não. | Verdadeiro<br/>False (padrão) | Não
redirectIncompatibleRowSettings | Um grupo de propriedades que pode ser especificado quando você deseja registrar em log as linhas incompatíveis. | &nbsp; | Não
linkedServiceName | O serviço vinculado do [armazenamento do Azure](connector-azure-blob-storage.md#linked-service-properties) ou [Azure data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) para armazenar o log que contém as linhas ignoradas. | O nome de um serviço vinculado de tipo `AzureStorage` ou `AzureDataLakeStore`, que se refere à instância que você deseja usar para armazenar o arquivo de log. | Não
Multi-Path | O caminho do arquivo de log que contém as linhas ignoradas. | Especifique o caminho que você deseja usar para registrar em log os dados incompatíveis. Se você não fornecer um caminho, o serviço criará um contêiner para você. | Não

## <a name="monitor-skipped-rows"></a>Monitorar linhas ignoradas
Depois que a execução da atividade de cópia for concluída, você poderá ver o número de linhas ignoradas na saída da atividade de cópia:

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
Se você configurar o para registrar em log as linhas incompatíveis, poderá encontrar o arquivo de log neste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Os arquivos de log só podem ser arquivos CSV. Os dados originais que estão sendo ignorados serão registrados com vírgula como delimitador de coluna, se necessário. Adicionamos mais duas colunas "ErrorCode" e "ErrorMessage" em adicionais aos dados de origem originais no arquivo de log, onde você pode ver a causa raiz da incompatibilidade. O ErrorCode e ErrorMessage serão colocados entre aspas duplas. 

Um exemplo do conteúdo do arquivo de log é o seguinte:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos de atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Desempenho da atividade de cópia](copy-activity-performance.md)


