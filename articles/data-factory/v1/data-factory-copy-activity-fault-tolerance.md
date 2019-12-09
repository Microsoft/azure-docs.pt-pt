---
title: Adicionar tolerância a falhas em Azure Data Factory atividade de cópia ignorando linhas incompatíveis
description: Saiba como adicionar tolerância a falhas em Azure Data Factory atividade de cópia ignorando linhas incompatíveis durante a cópia
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8ffaee75154fd5fe025bdb683c89f16799d6e86b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926157"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Adicionar tolerância a falhas na atividade de cópia ignorando linhas incompatíveis

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-copy-activity-fault-tolerance.md)
> * [Versão 2 (versão atual)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [tolerância a falhas na atividade de cópia do data Factory](../copy-activity-fault-tolerance.md).

Azure Data Factory [atividade de cópia](data-factory-data-movement-activities.md) oferece duas maneiras de lidar com linhas incompatíveis ao copiar dados entre armazenamentos de dados de origem e de coletor:

- Você pode anular e reprovar a atividade de cópia quando dados incompatíveis forem encontrados (comportamento padrão).
- Você pode continuar copiando todos os dados adicionando tolerância a falhas e ignorando linhas de dados incompatíveis. Além disso, você pode registrar em log as linhas incompatíveis no armazenamento de BLOBs do Azure. Em seguida, você pode examinar o log para saber a causa da falha, corrigir os dados na fonte de dados e repetir a atividade de cópia.

## <a name="supported-scenarios"></a>Cenários suportados
A atividade de cópia dá suporte a três cenários para detectar, ignorar e registrar em log dados incompatíveis:

- **Incompatibilidade entre o tipo de dados de origem e o tipo nativo do coletor**

    Por exemplo: copiar dados de um arquivo CSV no armazenamento de BLOBs para um banco de dado SQL com uma definição de esquema que contém três colunas de tipo **int** . As linhas do arquivo CSV que contêm dados numéricos, como `123,456,789`, são copiadas com êxito no repositório de coletor. No entanto, as linhas que contêm valores não numéricos, como `123,456,abc`, são detectadas como incompatíveis e ignoradas.

- **Incompatibilidade no número de colunas entre a origem e o coletor**

    Por exemplo: copiar dados de um arquivo CSV em um armazenamento de BLOBs para um banco de dados SQL com uma definição de esquema que contém seis colunas. As linhas do arquivo CSV que contêm seis colunas são copiadas com êxito no repositório de coletor. As linhas do arquivo CSV que contêm mais ou menos de seis colunas são detectadas como incompatíveis e ignoradas.

- **Violação de chave primária ao gravar no SQL Server/banco de dados SQL do Azure/Azure Cosmos DB**

    Por exemplo: copiar dados de um SQL Server para um banco de dados SQL. Uma chave primária é definida no banco de dados SQL do coletor, mas nenhuma chave primária é definida no SQL Server de origem. As linhas duplicadas que existem na fonte não podem ser copiadas para o coletor. A atividade de cópia copia apenas a primeira linha dos dados de origem no coletor. As linhas de origem subsequentes que contêm o valor duplicado da chave primária são detectadas como incompatíveis e ignoradas.

>[!NOTE]
>Este recurso não se aplica quando a atividade de cópia está configurada para invocar o mecanismo de carregamento de dados externos, incluindo o [Azure SQL data warehouse polybase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) ou o [Amazon redshift Unload](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift). Para carregar dados em SQL Data Warehouse usando o polybase, use o suporte nativo a tolerância a falhas do polybase especificando "[polyBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" na atividade de cópia.

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
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Habilitar a ignorando linhas incompatíveis durante a cópia ou não. | Verdadeiro<br/>False (padrão) | Não |
| **redirectIncompatibleRowSettings** | Um grupo de propriedades que pode ser especificado quando você deseja registrar em log as linhas incompatíveis. | &nbsp; | Não |
| **linkedServiceName** | O serviço vinculado do armazenamento do Azure para armazenar o log que contém as linhas ignoradas. | O nome de um serviço vinculado [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) , que se refere à instância de armazenamento que você deseja usar para armazenar o arquivo de log. | Não |
| **path** | O caminho do arquivo de log que contém as linhas ignoradas. | Especifique o caminho de armazenamento de BLOBs que você deseja usar para registrar os dados incompatíveis. Se você não fornecer um caminho, o serviço criará um contêiner para você. | Não |

## <a name="monitoring"></a>Monitorização
Depois que a execução da atividade de cópia for concluída, você poderá ver o número de linhas ignoradas na seção monitoramento:

![Monitorar linhas incompatíveis ignoradas](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Se você configurar o para registrar em log as linhas incompatíveis, poderá encontrar o arquivo de log neste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` no arquivo de log, você poderá ver as linhas que foram ignoradas e a causa raiz da incompatibilidade.

Os dados originais e o erro correspondente são registrados no arquivo. Um exemplo do conteúdo do arquivo de log é o seguinte:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre Azure Data Factory atividade de cópia, consulte [mover dados usando a atividade de cópia](data-factory-data-movement-activities.md).
