---
title: Visão geral do conector da Azure Data Factory
description: Aprenda os conectores suportados na Fábrica de Dados.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: jingwang
ms.openlocfilehash: cfd3376174ec0f7789389988245f7377b9896a00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015930"
---
# <a name="azure-data-factory-connector-overview"></a>Visão geral do conector da Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Data Factory suporta as seguintes lojas e formatos de dados através de copy, Data Flow, Look up, Get Metadata e Delete. Clique em cada loja de dados para saber as capacidades suportadas e as configurações correspondentes em detalhes.

## <a name="supported-data-stores"></a>Arquivos de dados suportados

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="integrate-with-more-data-stores"></a>Integrar-se com mais lojas de dados

A Azure Data Factory pode alcançar um conjunto mais amplo de lojas de dados do que a lista acima mencionada. Se precisar de mover dados de/para uma loja de dados que não esteja na lista de conector incorporado da Azure Data Factory, aqui ficam algumas opções extensíveis:
- Para base de dados e armazém de dados, normalmente pode encontrar um controlador ODBC correspondente, com o qual pode utilizar [o conector ODBC genérico.](connector-odbc.md)
- Para aplicações SaaS:
    - Se fornecer APIs RESTful, pode utilizar [o conector genérico REST](connector-rest.md).
    - Se tiver alimentação OData, pode utilizar [o conector OData genérico.](connector-odata.md)
    - Se fornecer APIs de SABÃO, pode utilizar [o conector HTTP genérico](connector-http.md).
    - Se tiver controlador ODBC, pode utilizar [o conector ODBC genérico.](connector-odbc.md)
- Para outros, verifique se pode carregar dados ou expor dados como qualquer loja de dados suportada pela ADF, por exemplo, Azure Blob/File/FTP/SFTP/etc, e depois deixe a ADF recolher a partir daí. Pode invocar mecanismo de carregamento de dados personalizado através da [Função Azure,](control-flow-azure-function-activity.md) [atividade personalizada,](transform-data-using-dotnet-custom-activity.md) [Databricks](transform-data-databricks-notebook.md) / [HDInsight,](transform-data-using-hadoop-hive.md) [atividade web,](control-flow-web-activity.md)etc.

## <a name="supported-file-formats"></a>Formatos de ficheiro suportados

A Azure Data Factory suporta os seguintes formatos de ficheiros. Consulte cada artigo para obter definições baseadas em formato.

- [Formato Avro](format-avro.md)
- [Formato binário](format-binary.md)
- [Formato do Common Data Model](format-common-data-model.md)
- [Formato de texto delimitado](format-delimited-text.md)
- [Formato Delta](format-delta.md)
- [Formato Excel](format-excel.md)
- [Formato JSON](format-json.md)
- [Formato ORC](format-orc.md)
- [Formato Parquet](format-parquet.md)
- [Formato XML](format-xml.md)

## <a name="next-steps"></a>Passos seguintes

- [Atividade Copiar](copy-activity-overview.md)
- [Fluxo de Dados de Mapeamento](concepts-data-flow-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Obter Atividade de Metadados](control-flow-get-metadata-activity.md)
- [Apagar Atividade](delete-activity.md)
