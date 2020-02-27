---
title: Configurar o Personalizador
description: A configuração do serviço inclui como o serviço trata recompensas, quantas vezes o serviço explora, quantas vezes o modelo é retreinado e quanto dados são armazenados.
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: ac31a9f907defeb44dbd4748a4395d3aec34d30c
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623708"
---
# <a name="configure-personalizer-learning-loop"></a>Configure ciclo de aprendizagem personalizador

A configuração do serviço inclui como o serviço trata recompensas, quantas vezes o serviço explora, quantas vezes o modelo é retreinado e quanto dados são armazenados.

Configure o ciclo de aprendizagem na página **de Configuração,** no portal Azure para esse recurso Personalizer.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="configure-rewards-for-the-feedback-loop"></a>Configure recompensas para o ciclo de feedback

Configure o serviço para o uso de recompensas do seu ciclo de aprendizagem. As alterações aos seguintes valores redefinirão o atual modelo Personalizer e retreiná-lo-ão com os últimos 2 dias de dados.

> [!div class="mx-imgBorder"]
> ![Configure os valores de recompensa para o ciclo de feedback](media/settings/configure-model-reward-settings.png)

|Valor|Objetivo|
|--|--|
|Tempo de espera de recompensa|Define o tempo durante o qual o Personalizer irá recolher valores de recompensa para uma chamada de Rank, a partir do momento em que a chamada de Rank acontece. Este valor é definido perguntando: "Quanto tempo deve personalizar esperar por chamadas de recompensas?" Qualquer recompensa que chegue depois desta janela será registada, mas não usada para aprender.|
|Recompensa por defeito|Se nenhuma chamada de recompensa for recebida pelo Personalizer durante a janela Reward Wait Time associada a uma chamada Rank, o Personalizer atribuirá a Recompensa Padrão. Por padrão, e na maioria dos cenários, a Recompensa Padrão é zero (0).|
|Agregação de recompensas|Se forem recebidas várias recompensas para a mesma chamada API de rank, este método de agregação é utilizado: **soma** ou **mais cedo**. As primeiras escolhas da partitura mais antiga recebida e descartam o resto. Isto é útil se você quiser uma recompensa única entre chamadas possivelmente duplicadas. |

Depois de alterar estes valores, certifique-se de selecionar **Guardar**.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>Configure a exploração para permitir que o ciclo de aprendizagem se adapte

A personalização é capaz de descobrir novos padrões e adaptar-se às mudanças de comportamento do utilizador ao longo do tempo, explorando alternativas em vez de usar a previsão do modelo treinado. O valor **de Exploração** determina qual a percentagem de chamadas de Rank que são respondidas com a exploração.

As alterações a este valor redefinirão o atual modelo Personalizer e retreiná-lo-ão com os últimos 2 dias de dados.

![O valor de exploração determina que percentagem de chamadas de Rank são respondidas com exploração](media/settings/configure-exploration-setting.png)

Depois de alterar este valor, certifique-se de selecionar **Guardar**.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>Configure a frequência de atualização do modelo para o treino de modelos

A frequência de **atualização** do Modelo define a frequência com que o modelo é treinado.

|Definição de frequência|Objetivo|
|--|--|
|1 minuto|As frequências de atualização de um minuto são úteis ao **depurar** o código de uma aplicação usando o Personalizer, fazendo demos ou testando interativamente aspetos de aprendizagem automática.|
|15 minutos|As frequências de atualização de modelos elevados são úteis para situações em que pretende **acompanhar de perto as mudanças** nos comportamentos dos utilizadores. Exemplos incluem sites que funcionam em notícias ao vivo, conteúdo viral ou licitação de produtos ao vivo. Precisa de uma frequência de 15 minutos nestes cenários. |
|1 hora|Para a maioria dos casos de utilização, uma frequência de atualização mais baixa é eficaz.|

![A frequência de atualização do modelo define a frequência com que um novo modelo Personalizer é retreinado.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Depois de alterar este valor, certifique-se de selecionar **Guardar**.

## <a name="data-retention"></a>Retenção de dados

**O período de retenção** de dados define quantos dias o Personalizer mantém os registos de dados. Os registos de dados anteriores são necessários para realizar [avaliações offline](concepts-offline-evaluation.md), que são usadas para medir a eficácia do Personalizer e otimizar a Política de Aprendizagem.

Depois de alterar este valor, certifique-se de selecionar **Guardar**.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>Definições que incluem redefinir o modelo

As seguintes ações incluem uma reconversão imediata do modelo com os últimos 2 dias de dados.

* Recompensa
* Exploração

Para [limpar](how-to-manage-model.md) todos os seus dados, utilize a página **Model e learning **

## <a name="next-steps"></a>Passos seguintes

[Saiba como gerir o seu modelo](how-to-manage-model.md)
