---
title: Adicionar a tolerância a falhas na atividade de cópia de fábrica de dados do Azure por ignorar linhas incompatíveis | Documentos da Microsoft
description: Saiba como adicionar a tolerância a falhas na atividade de cópia de fábrica de dados do Azure por ignorar linhas incompatíveis durante a cópia
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3a255b21e8bfd7d78954603e9aa6e5ca39cee95b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60566080"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Adicionar a tolerância a falhas na atividade de cópia, Ignorar linhas incompatíveis

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory, que está a utilizar:"]
> * [Versão 1](data-factory-copy-activity-fault-tolerance.md)
> * [Versão 2 (versão atual)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [tolerância a falhas na atividade de cópia do Data Factory](../copy-activity-fault-tolerance.md).

O Azure Data Factory [atividade de cópia](data-factory-data-movement-activities.md) oferece-lhe duas formas de lidar com linhas incompatíveis durante a cópia de dados entre arquivos de dados de origem e sink:

- Pode abortar e falhar a cópia de atividade, quando os dados incompatíveis encontrou (comportamento predefinido).
- Pode continuar copiar todos os dados ao adicionar a tolerância a falhas e Ignorar linhas de dados incompatíveis. Além disso, pode iniciar as linhas incompatíveis no armazenamento de Blobs do Azure. Em seguida, pode examinar o registo para obter a causa da falha, corrigir os dados na origem de dados e repita a atividade de cópia.

## <a name="supported-scenarios"></a>Cenários suportados
Atividade de cópia suporta três cenários para detetar, a ignorar e registo de dados incompatíveis:

- **Incompatibilidade entre o tipo de dados de origem e o tipo nativo de sink**

    Por exemplo: Copiar dados de um ficheiro CSV no armazenamento de BLOBs para base de dados SQL com uma definição de esquema que contém três **INT** colunas de tipo. As linhas do ficheiro CSV que contenham dados numéricos, como `123,456,789` são copiados com êxito para o arquivo sink. No entanto, as linhas que contêm valores não numéricos, como `123,456,abc` são detetados como incompatíveis e são ignorados.

- **Erro de correspondência no número de colunas entre a origem e o sink**

    Por exemplo: Copie dados de um ficheiro CSV no armazenamento de BLOBs para base de dados SQL com uma definição de esquema que contém seis colunas. As linhas do ficheiro CSV que contêm seis colunas são copiadas com êxito para o arquivo sink. As linhas do ficheiro CSV que contêm mais ou menos seis colunas são detetadas como incompatíveis e são ignoradas.

- **Violação de chave primária ao escrever para o SQL Server/Azure SQL da base de dados/do Azure Cosmos DB**

    Por exemplo: Copie dados de um SQL server para uma base de dados SQL. Uma chave primária é definida na base de dados do SQL sink, mas a chave não principal está definido no servidor SQL de origem. Não não possível copiar as linhas duplicadas que existam na origem para o sink. Atividade de cópia copia apenas a primeira linha dos dados de origem para o sink. As linhas de origem subsequentes que contêm o valor da chave primário duplicado são detetadas como incompatíveis e são ignoradas.

>[!NOTE]
>Esta funcionalidade não se aplica quando a atividade de cópia está configurada para invocar incluindo o mecanismo de carregamento de dados externos [do Azure SQL Data Warehouse PolyBase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) ou [descarregamento do Amazon Redshift](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift). Para carregar dados para o SQL Data Warehouse com o PolyBase, utilizar o suporte de tolerância de falhas nativo do PolyBase, especificando "[polyBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" na atividade de cópia.

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
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Ative a ignorar linhas incompatíveis durante a cópia ou não. | Verdadeiro<br/>FALSE (predefinição) | Não |
| **redirectIncompatibleRowSettings** | Um grupo de propriedades que podem ser especificados quando pretende registar linhas incompatíveis. | &nbsp; | Não |
| **linkedServiceName** | O serviço ligado do armazenamento do Azure para armazenar o registo que contém as linhas ignoradas. | O nome de um [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) serviço, que se refere à instância do armazenamento que pretende utilizar para armazenar o ficheiro de registo ligado. | Não |
| **path** | O caminho do ficheiro de registo que contém as linhas ignoradas. | Especifique o caminho de armazenamento de BLOBs que pretende utilizar para iniciar sessão os dados incompatíveis. Se não fornecer um caminho, o serviço cria um contentor para. | Não |

## <a name="monitoring"></a>Monitorização
Depois de concluída a execução de atividade de cópia, pode ver o número de linhas ignorados na secção monitorização:

![Monitor ignorada linhas incompatíveis](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Se configurar para iniciar as linhas incompatíveis, pode encontrar o ficheiro de registo neste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` No ficheiro de registo, pode ver as linhas que foram ignoradas e a causa da incompatibilidade.

Os dados originais e o erro correspondente são registadas no ficheiro. Um exemplo do conteúdo do ficheiro de registo é o seguinte:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre a atividade de cópia de fábrica de dados do Azure, veja [mover dados com a atividade de cópia](data-factory-data-movement-activities.md).
