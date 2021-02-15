---
title: Adicione tolerância a falhas na atividade de cópia da fábrica de dados Azure, ignorando linhas incompatíveis
description: Saiba como adicionar tolerância a falhas na Atividade de Cópia da Fábrica de Dados Azure, ignorando linhas incompatíveis durante a cópia
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 10e4bedae5b7c429152a3503fff2cb2769d66eb5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377181"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Adicione a tolerância à falha na Atividade de Cópia saltando linhas incompatíveis

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-copy-activity-fault-tolerance.md)
> * [Versão 2 (versão atual)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [a tolerância à falha na atividade de cópia da Data Factory.](../copy-activity-fault-tolerance.md)

A Azure Data Factory [Copy Activity](data-factory-data-movement-activities.md) oferece-lhe duas formas de lidar com linhas incompatíveis ao copiar dados entre lojas de dados de origem e pia:

- Pode abortar e falhar a atividade de cópia quando são encontrados dados incompatíveis (comportamento predefinido).
- Pode continuar a copiar todos os dados adicionando tolerância a falhas e ignorando linhas de dados incompatíveis. Além disso, pode registar as linhas incompatíveis no armazenamento Azure Blob. Em seguida, pode examinar o registo para saber a causa da falha, corrigir os dados na fonte de dados e voltar a tentar a atividade da cópia.

## <a name="supported-scenarios"></a>Cenários suportados
A Copy Activity suporta três cenários para detetar, saltar e registar dados incompatíveis:

- **Incompatibilidade entre o tipo de dados de origem e o tipo nativo de sink**

    Por exemplo: Copiar dados de um ficheiro CSV no armazenamento Blob para uma base de dados SQL com uma definição de esquema que contém três colunas do tipo **INT.** As linhas de ficheiro CSV que contêm dados numéricos, tais como `123,456,789` são copiadas com sucesso para a loja de pias. No entanto, as linhas que contêm valores não numéricos, tais como `123,456,abc` são detetadas como incompatíveis e são ignoradas.

- **Erro de correspondência no número de colunas entre a origem e o sink**

    Por exemplo: Copiar dados de um ficheiro CSV no armazenamento Blob para uma base de dados SQL com uma definição de esquema que contém seis colunas. As linhas de ficheiro CSV que contêm seis colunas são copiadas com sucesso para a loja de pias. As linhas de ficheiro CSV que contenham mais ou menos de seis colunas são detetadas como incompatíveis e são ignoradas.

- **Violação da chave primária ao escrever no SQL Server/Base de Dados SQL do Azure/Azure Cosmos DB**

    Por exemplo: Copiar dados de um servidor SQL para uma base de dados SQL. Uma chave primária é definida na base de dados SQL do lavatório, mas nenhuma chave primária é definida no servidor SQL de origem. As linhas duplicadas existentes na fonte não podem ser copiadas para a pia. Copiar A Atividade copia apenas a primeira linha dos dados de origem na pia. As linhas de origem subsequentes que contêm o valor da chave primária duplicada são detetadas como incompatíveis e ignoradas.

>[!NOTE]
>Esta funcionalidade não se aplica quando a atividade de cópia é configurada para invocar mecanismos externos de carregamento de dados, incluindo [a Azure Synapse Analytics PolyBase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) ou [Amazon Redshift Unload](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift). Para carregar dados no Azure Synapse Analytics utilizando o PolyBase, utilize o suporte nativo de tolerância à falha da PolyBase especificando "[poliBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" na atividade da cópia.

## <a name="configuration"></a>Configuração
O exemplo a seguir fornece uma definição JSON para configurar saltar as linhas incompatíveis na Atividade de Cópia:

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
| **enableSkipIncompatibleRow** | Ativar o salto de linhas incompatíveis durante a cópia ou não. | Verdadeiro<br/>Falso (predefinição) | No |
| **redireccionamentosIncompatíveis** | Um grupo de propriedades que podem ser especificadas quando pretende registar as linhas incompatíveis. | &nbsp; | No |
| **linkedServiceName** | O serviço ligado do Azure Storage para armazenar o registo que contém as linhas ignoradas. | O nome de um serviço ligado [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [AzureStorageSas,](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) que se refere à instância de armazenamento que pretende utilizar para armazenar o ficheiro de registo. | No |
| **caminho** | O caminho do ficheiro de registo que contém as linhas ignoradas. | Especifique a trajetória de armazenamento Blob que pretende utilizar para registar os dados incompatíveis. Se não providenciar um caminho, o serviço cria um recipiente para si. | No |

## <a name="monitoring"></a>Monitorização
Após o funcionamento da atividade da cópia, pode ver o número de linhas ignoradas na secção de monitorização:

![Monitor saltou linhas incompatíveis](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Se configurar para registar as linhas incompatíveis, pode encontrar o ficheiro de registo neste caminho: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` No ficheiro de registo, pode ver as linhas que foram ignoradas e a causa principal da incompatibilidade.

Tanto os dados originais como os erros correspondentes estão registados no ficheiro. Um exemplo do conteúdo do ficheiro de registo é o seguinte:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a atividade de cópia da fábrica de dados Azure, consulte [os dados do Movimento utilizando a Copy Activity](data-factory-data-movement-activities.md).
