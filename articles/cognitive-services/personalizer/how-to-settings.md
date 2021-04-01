---
title: Configurar o Personalizador
description: A configuração do serviço inclui como o serviço trata as recompensas, a frequência com que o serviço explora, quantas vezes o modelo é retreinado e quantos dados são armazenados.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 9b7347cb98bcbf2e1d92f115d404197083acef4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "91292613"
---
# <a name="configure-personalizer-learning-loop"></a>Configurar o ciclo de aprendizagem personalizador

A configuração do serviço inclui como o serviço trata as recompensas, a frequência com que o serviço explora, quantas vezes o modelo é retreinado e quantos dados são armazenados.

Configure o ciclo de aprendizagem na página **de Configuração,** no portal Azure para esse recurso Personalizer.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="planning-configuration-changes"></a>Alterações de configuração de planeamento

Como algumas alterações de configuração [reiniciam o seu modelo,](#settings-that-include-resetting-the-model)deve planear as alterações de configuração.

Se pretender utilizar o [modo Apprentice,](concept-apprentice-mode.md)certifique-se de rever a sua configuração Personaler antes de mudar para o modo Aprendiz.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>Definições que incluem a reposição do modelo

As seguintes ações desencadeiam uma reconversão do modelo utilizando dados disponíveis até aos últimos 2 dias.

* Recompensa
* Exploração

Para [limpar](how-to-manage-model.md) todos os seus dados, utilize a página **de definições de Modelo e aprendizagem.**

## <a name="configure-rewards-for-the-feedback-loop"></a>Configure recompensas para o ciclo de feedback

Configuure o serviço para o uso de recompensas do seu ciclo de aprendizagem. As alterações aos seguintes valores irão redefinir o modelo atual do Personalizer e reforçá-lo-á com os últimos 2 dias de dados.

> [!div class="mx-imgBorder"]
> ![Configure os valores de recompensa para o ciclo de feedback](media/settings/configure-model-reward-settings.png)

|Valor|Objetivo|
|--|--|
|Recompensar o tempo de espera|Define o período de tempo durante o qual o Personaler irá recolher valores de recompensa para uma chamada rank, a partir do momento em que a chamada Rank acontece. Este valor é definido perguntando: "Quanto tempo deve o Personaler esperar por chamadas de recompensas?" Qualquer recompensa que chegue depois desta janela será registada, mas não usada para aprender.|
|Recompensa por defeito|Se nenhuma chamada de recompensa for recebida pelo Personalizador durante a janela tempo de espera de recompensa associada a uma chamada rank, o Personalizer atribuirá a Recompensa Padrão. Por padrão, e na maioria dos cenários, a Recompensa Padrão é zero (0).|
|Agregação de recompensas|Se forem recebidas várias recompensas para a mesma chamada da API do Rank, este método de agregação é utilizado: **soma** ou **mais cedo**. O mais cedo escolhe a pontuação mais antiga recebida e descarta o resto. Isto é útil se você quiser uma recompensa única entre possíveis chamadas duplicadas. |

Depois de alterar estes valores, certifique-se de selecionar **Guardar**.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>Configurar a exploração para permitir que o ciclo de aprendizagem se adapte

A personalização é capaz de descobrir novos padrões e adaptar-se às mudanças de comportamento do utilizador ao longo do tempo, explorando alternativas em vez de usar a previsão do modelo treinado. O valor **de Exploração** determina qual a percentagem de chamadas rank respondidas com exploração.

As alterações a este valor irão reiniciar o modelo personalizador atual e reforçá-lo-á com os últimos 2 dias de dados.

![O valor de exploração determina qual a percentagem de chamadas rank respondidas com exploração](media/settings/configure-exploration-setting.png)

Depois de alterar este valor, certifique-se de selecionar **Guardar**.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>Configure a frequência de atualização do modelo para a formação de modelos

A **frequência de atualização do Modelo** define a frequência com que o modelo é treinado.

|Definição de frequência|Objetivo|
|--|--|
|1 minuto|As frequências de atualização de um minuto são úteis ao **depurar o** código de uma aplicação usando o Personalizer, fazendo demonstrações ou testando interativamente aspetos de aprendizagem automática.|
|15 minutos|As frequências de atualização de modelos elevados são úteis para situações em que pretende **acompanhar de perto as mudanças** nos comportamentos dos utilizadores. Exemplos incluem sites que funcionam em notícias ao vivo, conteúdo viral ou licitação de produtos ao vivo. Pode usar uma frequência de 15 minutos nestes cenários. |
|Uma hora|Para a maioria dos casos de utilização, uma frequência de atualização mais baixa é eficaz.|

![A frequência de atualização do modelo define a frequência com que um novo modelo Personalizer é retreinado.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Depois de alterar este valor, certifique-se de selecionar **Guardar**.

## <a name="data-retention"></a>Retenção de dados

**O período de retenção de dados** define quantos dias o Personaler mantém registos de dados. Registos de dados anteriores são necessários para realizar [avaliações offline,](concepts-offline-evaluation.md)que são usadas para medir a eficácia do Personalizer e otimizar a Política de Aprendizagem.

Depois de alterar este valor, certifique-se de selecionar **Guardar**.



## <a name="next-steps"></a>Passos seguintes

[Saiba como gerir o seu modelo](how-to-manage-model.md)
