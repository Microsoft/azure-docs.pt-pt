---
title: Configurar o comportamento de aprendizagem
description: O modo aprendiz dá-lhe confiança no serviço Personalizer e nas suas capacidades de machine learning, e fornece métricas de que o serviço é enviado informação que pode ser aprendida – sem arriscar o tráfego online.
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: af38500fc439964f9928cdd08aae2380ee0d0a8a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599482"
---
# <a name="configure-the-personalizer-learning-behavior"></a>Configure o comportamento de aprendizagem personalizador

[O modo aprendiz](concept-apprentice-mode.md) dá-lhe confiança e confiança no serviço Personalizer e nas suas capacidades de aprendizagem automática, e fornece garantias de que o serviço é enviado informação que pode ser aprendida – sem correr o risco de tráfego online.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>Modo Aprendiz de Configurar

1. Inscreva-se no [portal Azure,](https://portal.azure.com)para o seu recurso Personalizer.

1. Na página **de Configuração,** no separador **de comportamento de Aprendizagem,** selecione A ação de base de **retorno, aprenda como aprendiz** e, em seguida, selecione **Guardar**.

> [!div class="mx-imgBorder"]
> ![Screenshot de configurar comportamento de aprendizagem de modo aprendiz no portal Azure](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>Alterações à aplicação existente

A sua aplicação existente não deve alterar a forma como seleciona atualmente ações para exibir ou como a aplicação determina o valor, **recompensa** dessa ação. A única alteração na aplicação pode ser a ordem das ações enviadas para a API de Rank Rank da Personalizer. A ação que a sua aplicação exibe atualmente é enviada como a _primeira ação_ na lista de ação. A [Rank API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) utiliza esta primeira ação para treinar o seu modelo Personalizer.

### <a name="configure-your-application-to-call-the-rank-api"></a>Configure a sua aplicação para chamar a API de Rank

Para adicionar personalizer à sua aplicação, precisa de ligar para as APIs de Rank e Reward.

1. Adicione a chamada [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) de Rank após o ponto na sua lógica de aplicação existente onde determina a lista de ações e suas funcionalidades. A primeira ação na lista de ações tem de ser a ação selecionada pela sua lógica existente.

1. Configure o seu código para exibir a ação associada ao ID de Ação de **Recompensa**da resposta rank API .

### <a name="configure-your-application-to-call-reward-api"></a>Configure a sua aplicação para chamar API de Recompensa

1. Use a sua lógica de negócio existente para calcular a **recompensa** da ação exibida. O valor tem de estar na gama de 0 a 1. Envie esta recompensa ao Personalizer utilizando a [Reward API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward). O valor da recompensa não é esperado imediatamente e pode ser adiado por um período de tempo - dependendo da sua lógica de negócio.

1. Se não devolver a recompensa dentro do tempo de espera configurado **reward,** a recompensa padrão será usada em vez disso.

## <a name="evaluate-apprentice-mode"></a>Avaliar o modo Aprendiz

No portal Azure, na página **de Avaliações** para o seu recurso Personalizer, reveja o **desempenho do comportamento de aprendizagem atual.**

> [!div class="mx-imgBorder"]
> ![Screenshot de revisão da avaliação do comportamento de aprendizagem do modo aprendiz no portal Azure](media/settings/evaluate-apprentice-mode.png)

O modo aprendiz fornece as **seguintes métricas de avaliação:**
* **Linha de base – recompensa média**: Recompensas médias do padrão da aplicação (linha de base).
* **Personalizer – recompensa média**– Média de recompensas totais que o Personalizer teria potencialmente atingido.
* Rácio de realização de recompensas ao longo dos **1000 eventos mais recentes**: Ratio of Baseline e Personalizer reward – normalizado nos mais recentes 1000 eventos.

## <a name="evaluate-apprentice-mode-features"></a>Avaliar as funcionalidades do modo Aprendiz

Avaliar as funcionalidades utilizando uma [avaliação offline.](how-to-offline-evaluation.md)

## <a name="switch-behavior-to-online-mode"></a>Mude o comportamento para o modo Online

Quando determina que o Personalizer é treinado com uma média de 75-85% de média de rolamento, o modelo está pronto para mudar para o modo Online.

No portal Azure para o seu recurso Personalizer, na página **de Configuração,** no separador **de comportamento de Aprendizagem,** selecione **Devolver a melhor ação** e, em seguida, selecione **Guardar**.

Não precisa de fazer alterações nas chamadas de API de Rank e Reward.

## <a name="next-steps"></a>Passos seguintes

* [Gerir modelos e definições de aprendizagem](how-to-manage-model.md)
