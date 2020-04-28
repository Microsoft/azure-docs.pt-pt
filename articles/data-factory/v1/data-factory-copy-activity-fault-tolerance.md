---
title: Adicione tolerância à falha na Atividade de Cópia de Cópia de Fábrica de Dados Do Azure, saltando linhas incompatíveis
description: Saiba como adicionar tolerância à falha na Atividade de Cópia de Cópia de Cópia de Dados do Azure, saltando linhas incompatíveis durante a cópia
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74926157"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Adicionar tolerância à falha na Atividade de Cópia saltando linhas incompatíveis

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-copy-activity-fault-tolerance.md)
> * [Versão 2 (versão atual)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte a tolerância à [falha na atividade de cópia da Data Factory](../copy-activity-fault-tolerance.md).

A [Azure](data-factory-data-movement-activities.md) Data Factory Copy Activity oferece-lhe duas formas de lidar com linhas incompatíveis ao copiar dados entre as lojas de dados de origem e pia:

- Pode abortar e falhar a atividade da cópia quando forem encontrados dados incompatíveis (comportamento predefinido).
- Pode continuar a copiar todos os dados adicionando tolerância à falha e saltando linhas de dados incompatíveis. Além disso, pode registar as linhas incompatíveis no armazenamento azure Blob. Em seguida, pode examinar o registo para aprender a causa da falha, corrigir os dados na fonte de dados e voltar a tentar a atividade da cópia.

## <a name="supported-scenarios"></a>Cenários suportados
A Copy Activity suporta três cenários para detetar, saltar e registar dados incompatíveis:

- **Incompatibilidade entre o tipo de dados de origem e o tipo nativo de sink**

    Por exemplo: Copiar dados de um ficheiro CSV no armazenamento blob para uma base de dados SQL com uma definição de esquema que contém três colunas do tipo **INT.** As linhas de ficheiroCSV que contêm `123,456,789` dados numéricos, tais como são copiadas com sucesso para a loja de sumidouros. No entanto, as linhas que contêm valores não numéricos, tais como `123,456,abc` são detetadas como incompatíveis e ignoradas.

- **Erro de correspondência no número de colunas entre a origem e o sink**

    Por exemplo: Copiar dados de um ficheiro CSV no armazenamento blob para uma base de dados SQL com uma definição de esquema que contém seis colunas. As linhas de ficheiroCSV que contêm seis colunas são copiadas com sucesso para a pia. As linhas de ficheiro CSV que contêm mais ou menos de seis colunas são detetadas como incompatíveis e ignoradas.

- **Violação da chave primária ao escrever no SQL Server/Base de Dados SQL do Azure/Azure Cosmos DB**

    Por exemplo: Copiar dados de um servidor SQL para uma base de dados SQL. Uma chave primária é definida na base de dados SQL do lavatório, mas nenhuma chave primária é definida no servidor SQL de origem. As linhas duplicadas existentes na fonte não podem ser copiadas para a pia. A Copy Activity copia apenas a primeira linha dos dados de origem para o lavatório. As linhas de origem subsequentes que contêm o valor-chave primário duplicado são detetadas como incompatíveis e ignoradas.

>[!NOTE]
>Esta funcionalidade não se aplica quando a atividade de cópia é configurada para invocar mecanismo externo de carregamento de dados, incluindo [o Azure SQL Data Warehouse PolyBase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) ou [amazon Redshift Unload](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift). Para carregar dados no SQL Data Warehouse utilizando o PolyBase, utilize o suporte de tolerância à falha nativa da PolyBase, especificando "[poliBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" na atividade de cópia.

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
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| **enableSkipIncompatívelRow** | Ativar a falta de linhas incompatíveis durante a cópia ou não. | Verdadeiro<br/>Falso (predefinição) | Não |
| **redirecionamentoIncompatívelComDefinições RowSettings** | Um grupo de propriedades que pode ser especificada quando pretende registar as linhas incompatíveis. | &nbsp; | Não |
| **linkedServiceName** | O serviço ligado do Armazenamento Azure para armazenar o registo que contém as linhas ignoradas. | O nome de um serviço ligado [azureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [AzureStorageSas,](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) que se refere à instância de armazenamento que pretende utilizar para armazenar o ficheiro de registo. | Não |
| **caminho** | O caminho do ficheiro de registo que contém as linhas ignoradas. | Especifique o caminho de armazenamento Blob que pretende utilizar para registar os dados incompatíveis. Se não fornecer um caminho, o serviço cria um recipiente para si. | Não |

## <a name="monitoring"></a>Monitorização
Após a execução da atividade da cópia, pode ver o número de linhas ignoradas na secção de monitorização:

![Monitor saltou linhas incompatíveis](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Se configurar para registar as linhas incompatíveis, pode encontrar o `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` ficheiro de registo neste caminho: No ficheiro de registo, pode ver as linhas que foram ignoradas e a causa principal da incompatibilidade.

Tanto os dados originais como o erro correspondente estão registados no ficheiro. Um exemplo do conteúdo do ficheiro de registo é o seguinte:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a Atividade de Cópia da Fábrica de Dados Azure, consulte [mover dados utilizando a Atividade de Cópia](data-factory-data-movement-activities.md).
