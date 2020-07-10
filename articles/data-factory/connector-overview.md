---
title: Visão geral do conector da Azure Data Factory
description: Aprenda os conectores suportados na Fábrica de Dados.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/08/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: d7a872121ca6560b8ede86abc35294ab8c9b0c1b
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86141995"
---
# <a name="azure-data-factory-connector-overview"></a>Visão geral do conector da Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Data Factory suporta as seguintes lojas e formatos de dados através de copy, Data Flow, Lookup, Get Metadata e Delete. Clique em cada loja de dados para saber as capacidades suportadas e as configurações correspondentes em detalhes.

## <a name="supported-data-stores"></a>Arquivos de dados suportados

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="supported-file-formats"></a>Formatos de ficheiro suportados

A Azure Data Factory suporta os seguintes formatos de ficheiros. Consulte cada artigo para obter definições baseadas em formato.

- [Formato Avro](format-avro.md)
- [Formato binário](format-binary.md)
- [Formato do Common Data Model](format-common-data-model.md)
- [Formato de texto delimitado](format-delimited-text.md)
- [Formato Excel](format-excel.md)
- [Formato JSON](format-json.md)
- [Formato ORC](format-orc.md)
- [Formato Parquet](format-parquet.md)

## <a name="next-steps"></a>Passos seguintes

- [Atividade Copiar](copy-activity-overview.md)
- [Fluxo de Dados de Mapeamento](concepts-data-flow-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Obter Atividade de Metadados](control-flow-get-metadata-activity.md)
- [Apagar Atividade](delete-activity.md)
