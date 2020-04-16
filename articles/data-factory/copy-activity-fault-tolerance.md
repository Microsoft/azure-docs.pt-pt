---
title: Tolerância a falhas da atividade de cópia no Azure Data Factory
description: Saiba como adicionar tolerância à falha para copiar atividade na Azure Data Factory, ignorando as linhas incompatíveis.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: yexu
ms.openlocfilehash: 766520fe44047eee76029adf8ee1683c7b8008a1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417866"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Tolerância a falhas da atividade de cópia no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Versão atual](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A atividade de cópia na Azure Data Factory oferece-lhe duas formas de lidar com linhas incompatíveis ao copiar dados entre as lojas de dados de origem e pia:

- Pode abortar e falhar a atividade da cópia quando forem encontrados dados incompatíveis (comportamento predefinido).
- Pode continuar a copiar todos os dados adicionando tolerância à falha e saltando linhas de dados incompatíveis. Além disso, pode registar as linhas incompatíveis no armazenamento Azure Blob ou na Azure Data Lake Store. Em seguida, pode examinar o registo para aprender a causa da falha, corrigir os dados na fonte de dados e voltar a tentar a atividade da cópia.

## <a name="supported-scenarios"></a>Cenários suportados
A Copy Activity suporta três cenários para detetar, saltar e registar dados incompatíveis:

- **Incompatibilidade entre o tipo de dados de origem e o tipo nativo da pia**. 

    Por exemplo: Copiar dados de um ficheiro CSV no armazenamento blob para uma base de dados SQL com uma definição de esquema que contém três colunas do tipo INT. As linhas de ficheiroCSV que contêm dados numéricos, tais como 123.456.789 são copiadas com sucesso para a loja de sumidouros. No entanto, as linhas que contêm valores não numéricos, como 123.456, o ABC são detetados como incompatíveis e são ignorados.

- **Incompatibilidade no número de colunas entre a fonte e o lavatório**.

    Por exemplo: Copiar dados de um ficheiro CSV no armazenamento blob para uma base de dados SQL com uma definição de esquema que contém seis colunas. As linhas de ficheiroCSV que contêm seis colunas são copiadas com sucesso para a pia. As linhas de ficheiro CSV que contêm mais de seis colunas são detetadas como incompatíveis e ignoradas.

- **Violação da chave primária ao escrever para SQL Server/Azure SQL Database/Azure Cosmos DB**.

    Por exemplo: Copiar dados de um servidor SQL para uma base de dados SQL. Uma chave primária é definida na base de dados SQL do lavatório, mas nenhuma chave primária é definida no servidor SQL de origem. As linhas duplicadas existentes na fonte não podem ser copiadas para a pia. A Copy Activity copia apenas a primeira linha dos dados de origem para o lavatório. As linhas de origem subsequentes que contêm o valor-chave primário duplicado são detetadas como incompatíveis e ignoradas.

>[!NOTE]
>- Para carregar dados no SQL Data Warehouse utilizando o PolyBase, configure as definições de tolerância à falha nativa da PolyBase, especificando as políticas de rejeição através de "[poliBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" na atividade de cópia. Ainda pode ativar o redirecionamento de linhas incompatíveis da PolyBase para Blob ou ADLS tão normal como mostrado abaixo.
>- Esta funcionalidade não se aplica quando a atividade de cópia é configurada para invocar o [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Esta funcionalidade não se aplica quando a atividade da cópia é configurada para invocar um [procedimento armazenado a partir de um lavatório SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink).

## <a name="configuration"></a>Configuração
O exemplo que se segue fornece uma definição JSON para configurar saltar as linhas incompatíveis na Atividade de Cópia:

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
enableSkipIncompatívelRow | Especifica se deve saltar linhas incompatíveis durante a cópia ou não. | Verdadeiro<br/>Falso (predefinição) | Não
redirecionamentoIncompatívelComDefinições RowSettings | Um grupo de propriedades que pode ser especificada quando pretende registar as linhas incompatíveis. | &nbsp; | Não
linkedServiceName | O serviço ligado do [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) ou [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) para armazenar o registo que contém as linhas ignoradas. | O nome `AzureStorage` de `AzureDataLakeStore` um ou tipo de serviço ligado, que se refere à instância que pretende utilizar para armazenar o ficheiro de registo. | Não
path | O caminho do ficheiro de registo que contém as linhas ignoradas. | Especifique o caminho que pretende utilizar para registar os dados incompatíveis. Se não fornecer um caminho, o serviço cria um recipiente para si. | Não

## <a name="monitor-skipped-rows"></a>Monitor linhas ignoradas
Após a execução da atividade de cópia, pode ver o número de linhas ignoradas na saída da atividade da cópia:

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
Se configurar para registar as linhas incompatíveis, pode encontrar o `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`ficheiro de registo neste caminho: . 

Os ficheiros de registo só podem ser os ficheiros CSV. Os dados originais que estão a ser ignorados serão registados com vírina como delimitador de coluna, se necessário. Adicionamos mais duas colunas "ErrorCode" e "ErrorMessage" em adicional aos dados originais de origem no ficheiro de registo, onde pode ver a causa principal da incompatibilidade. O ErrorCode e o ErrorMessage serão citados por duas cotações. 

Um exemplo do conteúdo do ficheiro de registo é o seguinte:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos da Atividade cópia:

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Desempenho da atividade de cópia](copy-activity-performance.md)


