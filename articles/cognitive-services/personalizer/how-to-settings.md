---
title: Configurar definições
titleSuffix: Azure Cognitive Services
description: Configuração do serviço inclui como o serviço trata de remunerações, a frequência com que o serviço explora, a frequência com que o modelo é reestruturar e a quantidade de dados é armazenado.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 4aeb9f924ba9af55485dce8babfacdbb0d0110f4
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520285"
---
# <a name="personalizer-settings"></a>Definições de personalizer

Configuração do serviço inclui como o serviço trata de remunerações, a frequência com que o serviço explora, a frequência com que o modelo é reestruturar e a quantidade de dados é armazenado.

## <a name="create-personalizer-resource"></a>Criar recurso Personalizer

Crie um recurso de Personalizer para cada ciclo de comentários. 

1. Inicie sessão no [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). A ligação anterior leva-o para o **criar** página para o serviço de Personalizer. 
1. Introduza o nome do serviço, selecione uma subscrição, localização, grupo de recursos e escalão de preço.
1. Selecione a confirmação e selecione **criar**.

## <a name="configure-service-settings-in-the-azure-portal"></a>Configurar definições de serviço no portal do Azure

1. Inicie sessão no [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Localize o recurso de Personalizer. 
1. Na **gestão de recursos** secção, selecione **definições**.

    Antes de sair do portal do Azure, copie uma das suas chaves de recurso a partir da **chaves** página. Irá precisar de utilizar o [Personalizer SDK](https://go.microsoft.com/fwlink/?linkid=2092353).

### <a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>Configurar as definições de recompensa para que o loop de comentários com base no caso de utilização

Configure definições do serviço para utilização do seu ciclo de comentários de remunerações. Alterações às seguintes definições irão repor o modelo de Personalizer atual e voltar a preparar com os últimos 2 dias de dados:

![Configurar as definições de recompensa para o ciclo de comentários](media/settings/configure-model-reward-settings.png)

|Definição|Objetivo|
|--|--|
|Tempo de espera de recompensa|Define o período de tempo durante o qual Personalizer recolherá os valores de remuneração para uma chamada de classificação, a partir do momento em que a chamada de classificação acontece. Este valor é definido por perguntar: "Quanto devem Personalizer esperar para chamadas de remunerações?" Qualquer recompensa que chegam após esta janela irá ser registada, mas não utilizada para a aprendizagem automática.|
|Padrão de recompensa|Se não for recebida nenhuma chamada de recompensa Personalizer durante a janela de tempo de espera de recompensa associada a uma classificação chamar, Personalizer atribuirá a recompensa predefinido. Por predefinição e na maioria dos cenários, a recompensa padrão é zero.|
|Agregação de recompensa|Se forem recebidas vários remunerações para a mesma classificação API chamar, este método de agregação é utilizado: **soma** ou **mais antigo**. Mais antiga escolhe a classificação mais antiga recebida e descarta o resto. Isso é útil se desejar recompensa exclusiva entre chamadas possivelmente duplicadas. |

Depois de alterar estas definições, verifique se seleciona **guardar**.

### <a name="exploration-setting"></a>Definição de exploração 

Personalização é capaz de descobrir novos padrões e adaptar-se às alterações de comportamento do utilizador ao longo do tempo ao explorar alternativas. O **exploração** definição determina qual é a porcentagem de chamadas de classificação são respondidas com exploração. 

Alterações a esta definição irão repor o modelo de Personalizer atual e voltar a preparar com os últimos 2 dias de dados.

![A definição de exploração determina qual é a porcentagem de chamadas de classificação são respondidas com exploração](media/settings/configure-exploration-setting.png)

Depois de alterar esta definição, verifique se seleciona **guardar**.

### <a name="model-update-frequency"></a>Frequência de atualização do modelo

**Frequência de atualização de modelo** define a frequência com que um novo modelo de Personalizer é reestruturar. 

![Frequência de atualização do modelo define a frequência com que um novo modelo de Personalizer é reestruturar.](media/settings/configure-model-update-frequency-settings.png)

Depois de alterar esta definição, verifique se seleciona **guardar**.

### <a name="data-retention"></a>Retenção de dados

**Período de retenção de dados** define o número de dias Personalizer mantém registos de dados. Últimos dados registos são necessários para efetuar [avaliações offline](concepts-offline-evaluation.md), que é utilizado para avaliar a eficiência dos Personalizer e otimizar a política de Aprendizado.

Depois de alterar esta definição, verifique se seleciona **guardar**.

## <a name="export-the-personalizer-model"></a>Exportar o modelo de Personalizer

Na secção da gestão de recursos para **modelo e a política de**, reveja a criação de um modelo e a data da última atualização e exportar o modelo atual.

![Exportar modelo de Personalizer atual](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Importar e exportar a política de aprendizagem

Na secção da gestão de recursos para **modelo e a política de**, importar uma nova política de aprendizado ou exportar a política de aprendizado atual.

## <a name="next-steps"></a>Passos Seguintes

[Como utilizar o contentor de Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
