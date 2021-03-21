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
ms.openlocfilehash: 9cd3fc9353c684ec6617761616b958477ca83ee3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183646"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integração de dados no Azure Synapse Analytics contra Azure Data Factory

No Azure Synapse Analytics, as capacidades de integração de dados, tais como os oleodutos synapse e os fluxos de dados, baseiam-se nas da Azure Data Factory. Para mais informações, veja [o que é a Azure Data Factory.](../../data-factory/introduction.md)


## <a name="available-features-in-adf--azure-synapse-analytics"></a>Funcionalidades disponíveis na ADF & Azure Synapse Analytics

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
| **Linhagem** | Suporta a publicação de dados de linhagem pipeline para a Purview  | ✓ | ✗ |  

> [!Note]
> **Time to Live** é uma definição de tempo de execução de integração Azure que permite ao cluster Spark *manter-se quente* por um período de tempo após uma execução do fluxo de dados.
>


## <a name="next-steps"></a>Passos seguintes

Inicie-se com a integração de dados no seu espaço de trabalho da Sinaapse aprendendo a [ingerir dados numa conta gen2 do Azure Data Lake Storage](data-integration-data-lake.md).
