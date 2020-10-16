---
title: Diferenças em relação ao Azure Data Factory
description: Saiba como as capacidades de integração de dados da Azure Synapse Analytics diferem das da Azure Data Factory
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: be098977ba51e529aaecfb5dc3b7a03444f55a7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91345167"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integração de dados no Azure Synapse Analytics contra Azure Data Factory

No Azure Synapse Analytics, as capacidades de integração de dados, tais como os oleodutos synapse e os fluxos de dados, baseiam-se nas da Azure Data Factory. Para mais informações, veja [o que é a Azure Data Factory.](../../data-factory/introduction.md) Quase todas as capacidades são idênticas ou similares e a documentação é partilhada entre os dois serviços. Este artigo destaca e identifica as diferenças atuais entre a Azure Data Factory e a Azure Synapse.

Para ver se uma funcionalidade ou artigo da Azure Data Factory se aplica à Azure Synapse, verifique o apelido no topo do artigo.

![Aplica-se ao apelido](../media/concepts-data-factory-differences/applies-to-moniker.png "Aplica-se ao apelido")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Características na Azure Data Factory não estão previstas para Azure Synapse

As seguintes funcionalidades estão disponíveis na Azure Data Factory, mas não estão previstas para a Azure Synapse.

* A capacidade de levantar e deslocar pacotes SSIS.
* Floco de neve como pia na atividade da cópia e fluxo de dados de mapeamento.
* O tempo de fluxo de dados de mapeamento para a configuração ao vivo do tempo de execução da integração do Azure.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Funcionalidades do Azure Synapse não suportadas na Azure Data Factory

As seguintes funcionalidades estão disponíveis no Azure Synapse, mas não estão previstas para a Azure Data Factory.

* A monitorização do trabalho de faísca dos fluxos de dados de mapeamento só está disponível no Synapse. No Synapse, o motor Spark está contido na subscrição do utilizador para que os utilizadores possam ver registos de faíscas detalhados. Na Azure Data Factory, a execução de emprego ocorre num cluster de faíscas gerido pela Azure Data Factory. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Características da Azure Data Factory que se comportam de forma diferente na Sinapse

As seguintes funcionalidades comportam-se de forma diferente ou não existem atualmente em Azure Synapse. 

* Fluxos de dados de estrangulamento
* A galeria de modelo de solução
* Integração de Git e uma solução nativa de CI/CD
* Integração com monitor Azure
* Renomeação de recursos após publicação
* Configuração de tempo de execução de integração híbrida dentro de um espaço de trabalho Synapse. Um utilizador não pode ter um VNet IR gerido e um Azure IR.
* Integração partilha de tempo de execução entre espaços de trabalho da Sinapse

## <a name="next-steps"></a>Passos seguintes

Inicie-se com a integração de dados no seu espaço de trabalho da Sinaapse aprendendo a [ingerir dados numa conta gen2 do Azure Data Lake Storage](data-integration-data-lake.md).
