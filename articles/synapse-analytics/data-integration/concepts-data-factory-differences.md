---
title: Diferenças em relação ao Azure Data Factory
description: Saiba como as capacidades de integração de dados da Azure Synapse Analytics diferem das da Azure Data Factory
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: a8fd0ef006b246e30c02cfb321c72b4e070f54de
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109152"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integração de dados no Azure Synapse Analytics contra Azure Data Factory

No Azure Synapse Analytics, as capacidades de integração de dados, tais como os oleodutos synapse e os fluxos de dados, baseiam-se nas da Azure Data Factory. Para mais informações, veja [o que é a Azure Data Factory.](../../data-factory/introduction.md)


## <a name="available-features-in-azure-data-factory-and-azure-synapse-analytics"></a>Funcionalidades disponíveis na Azure Data Factory e Azure Synapse Analytics

Consulte a tabela abaixo para obter disponibilidade de funcionalidades:

| Categoria                 | Funcionalidade    |  Azure Data Factory  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Tempo de execução de integração**  | Utilizando o tempo de funcionação da integração SSIS e SSIS | ✓ | ✗ |
|                          | Suporte para o tempo de execução da integração entre regiões (Fluxos de dados) | ✓ | ✗ |
|                          | Partilha de tempo de execução de integração | ✓<br><small>*Pode ser partilhado em diferentes fábricas de dados* | ✗ |
|                          | Hora de Viver | ✓ | ✗ |
| **Atividades de Gasodutos** | Atividade do pacote SSIS | ✓ | ✗ |
|                          | Suporte para atividade de consulta de energia | ✓ | ✓ |
| **Galeria de Modelos e Centro de Conhecimento** | Modelos de Soluções | ✓<br><small>*Galeria de Modelos de Fábrica de Dados Azure* | ✓<br><small>*Centro de Conhecimento do Espaço de Trabalho da Sinapse* |
| **Integração repositória do GIT** | Integração GIT | ✓ | ✓ |
| **Monitorização**           | Monitorização de empregos de faíscas para fluxo de dados | ✗ | ✓<br><small>*Aproveite as piscinas Synapse Spark* |
|                          | Integração com o Azure Monitor | ✓ | ✗ |

> [!Note]
> **Time to Live** é uma definição de tempo de execução de integração Azure que permite ao cluster Spark *manter-se quente* por um período de tempo após uma execução do fluxo de dados.
>


## <a name="next-steps"></a>Passos seguintes

Inicie-se com a integração de dados no seu espaço de trabalho da Sinaapse aprendendo a [ingerir dados numa conta gen2 do Azure Data Lake Storage](data-integration-data-lake.md).
