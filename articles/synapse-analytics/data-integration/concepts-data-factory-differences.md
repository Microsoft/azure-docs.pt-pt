---
title: Diferenças em relação ao Azure Data Factory
description: Saiba como as capacidades de integração de dados da Azure Synapse Analytics diferem das da Azure Data Factory
services: synapse-analytics
author: shirleywangmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: shwang
ms.reviewer: jrasnick
ms.openlocfilehash: db5d05e1a211ce14926ee4031054669fff5110d9
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930216"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integração de dados no Azure Synapse Analytics contra Azure Data Factory

No Azure Synapse Analytics, as capacidades de integração de dados, tais como os oleodutos synapse e os fluxos de dados, baseiam-se nas da Azure Data Factory. Para mais informações, veja [o que é a Azure Data Factory.](../../data-factory/introduction.md) Quase todas as capacidades são idênticas ou similares e a documentação é partilhada entre os dois serviços. Este artigo destaca e identifica as diferenças atuais entre a Azure Data Factory e a Azure Synapse.

Para ver se uma funcionalidade ou artigo da Azure Data Factory se aplica à Azure Synapse, verifique o apelido no topo do artigo.

![Aplica-se ao apelido](../media/concepts-data-factory-differences/applies-to-moniker.png "Aplica-se ao apelido")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Características na Azure Data Factory não estão previstas para Azure Synapse

As seguintes funcionalidades estão disponíveis na Azure Data Factory, mas não estão previstas para a Azure Synapse.

* **Pacotes SSIS de vida e mudança:** Na Azure Data Factory, você tem a capacidade de levantar e deslocar pacotes SSIS usando o tempo de funcionamento da integração SSIS. Tanto o tempo de funcionação da integração SSIS como a atividade do Pacote SSIS executos não estão disponíveis em espaços de trabalho da Sinapse. 
* **Hora de viver:** O tempo de vida é uma configuração no tempo de integração do Azure que permite ao cluster de faíscas no mapeamento dos fluxos de dados para se manter *quente* por um período de tempo após a conclusão de um fluxo de dados. Esta funcionalidade não está disponível nos espaços de trabalho da Synapse.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Funcionalidades do Azure Synapse não suportadas na Azure Data Factory

As seguintes funcionalidades estão disponíveis no Azure Synapse, mas não estão previstas para a Azure Data Factory.

* **Monitorização de trabalho de faíscas dos fluxos de dados de mapeamento:** No Synapse, o motor Spark está contido na subscrição do utilizador para que os utilizadores possam ver registos de faíscas detalhados. Na Azure Data Factory, a execução de emprego ocorre num cluster de faíscas gerido pela Azure Data Factory e esta informação não está disponível. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Características da Azure Data Factory que se comportam de forma diferente na Sinapse

As seguintes funcionalidades comportam-se de forma diferente ou não existem atualmente em Azure Synapse. 

* **Fluxos de dados de estrangulamento:** A atividade de fluxo de dados de estrangulamento só está disponível na Azure Data Factory neste momento.
* **A galeria do modelo de solução:** Na Azure Data Factory, os utilizadores podem encontrar modelos de pipeline na galeria do modelo de solução. Nos espaços de trabalho da Synapse, o knowledge center contém um conjunto diferente de modelos, juntamente com conjuntos de dados adicionais e Scripts SQL. 
* **Integração com monitor Azure:** Os espaços de trabalho da Sinapse não se integram com o Azure Data Factory como faz a Azure Data Factory.
* **Configuração de tempo de execução de integração híbrida:** Dentro de um espaço de trabalho synapse, um utilizador não pode ter tanto um VNet IR gerido como um Azure IR. Esta capacidade é suportada na Azure Data Factory.
* **Partilha de tempo de integração:** Os tempos de integração auto-organizados não podem ser partilhados entre espaços de trabalho da Sinapse. Esta capacidade é suportada na Azure Data Factory.
* **Tempos de integração de regiões cruzadas para fluxos de dados:** Os fluxos de dados não podem funcionar em tempos de integração em diferentes regiões do que um espaço de trabalho da Sinapse. Esta capacidade é suportada na Azure Data Factory.

## <a name="next-steps"></a>Passos seguintes

Inicie-se com a integração de dados no seu espaço de trabalho da Sinaapse aprendendo a [ingerir dados numa conta gen2 do Azure Data Lake Storage](data-integration-data-lake.md).
