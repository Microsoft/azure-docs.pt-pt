---
title: Configurar comportamento de aprendizagem
description: O modo aprendiz dá-lhe confiança no serviço Personalizar e nas suas capacidades de machine learning, e fornece métricas de que o serviço é enviado informação que pode ser aprendida – sem arriscar o tráfego online.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: 57a03b107678f83200b11f408784f6455cbceffd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "94579296"
---
# <a name="configure-the-personalizer-learning-behavior"></a>Configure o comportamento de aprendizagem do Personaler

[O modo aprendiz](concept-apprentice-mode.md) dá-lhe confiança e confiança no serviço Personalizer e nas suas capacidades de machine learning, e oferece garantias de que o serviço é enviado informação que pode ser aprendida – sem arriscar o tráfego online.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>Configurar modo aprendiz

1. Inscreva-se no [portal Azure,](https://portal.azure.com)para o seu recurso Personalizer.

1. Na página **Configuração,** no **separador Comportamento de Aprendizagem,** selecione **Return baseline action, learn as a apprentice** then select **Save**.

> [!div class="mx-imgBorder"]
> ![Screenshot de configurar comportamento de aprendizagem do modo de aprendizagem no portal Azure](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>Alterações à aplicação existente

A aplicação existente não deve alterar a forma como seleciona atualmente as ações para exibir ou como a aplicação determina o valor, **recompensa** dessa ação. A única alteração ao pedido pode ser a ordem das ações enviadas para a API do Ranking Personaler. A ação que a sua aplicação apresenta atualmente é enviada como a _primeira ação_ na lista de ação. A [Rank API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) utiliza esta primeira ação para treinar o seu modelo Personalizer.

### <a name="configure-your-application-to-call-the-rank-api"></a>Configure a sua candidatura para ligar para a API rank

Para adicionar Personalizer à sua aplicação, precisa de ligar para as APIs de Classificação e Recompensa.

1. Adicione a chamada [de API de classificação](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) após o ponto na sua lógica de aplicação existente onde determina a lista de ações e suas funcionalidades. A primeira ação na lista de ações tem de ser a ação selecionada pela sua lógica existente.

1. Configure o seu código para exibir a ação associada ao ID de Ação de Recompensa da API de **classificação**.

### <a name="configure-your-application-to-call-reward-api"></a>Configure a sua aplicação para ligar para a Reward API

1. Use a sua lógica de negócio existente para calcular a **recompensa** da ação exibida. O valor tem de estar no intervalo de 0 a 1. Envie esta recompensa ao Personaler utilizando a [API Reward.](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) O valor da recompensa não é esperado imediatamente e pode ser adiado durante um período de tempo - dependendo da lógica do seu negócio.

1. Se não devolver a recompensa dentro do **tempo** de espera do Reward configurado, a recompensa por defeito será utilizada.

## <a name="evaluate-apprentice-mode"></a>Avaliar o modo aprendiz

No portal Azure, na página **avaliações** para o seu recurso Personalizer, reveja o **desempenho do comportamento de aprendizagem atual**.

> [!div class="mx-imgBorder"]
> ![Screenshot de revisão da avaliação do comportamento de aprendizagem do modo de aprendiz no portal Azure](media/settings/evaluate-apprentice-mode.png)

O modo aprendiz fornece as seguintes **métricas de avaliação:**
* **Linha de base – recompensa média**: Recompensas médias do padrão da aplicação (linha de base).
* **Personalização – recompensa média**: Média de recompensas totais O Personalizador teria potencialmente atingido.
* **Rácio de realização de recompensas ao longo dos mais recentes 1000 eventos**- Rácio de Base e Recompensa Personalizada - normalizado ao longo dos mais recentes 1000 eventos.

## <a name="switch-behavior-to-online-mode"></a>Mude o comportamento para o modo Online

Quando determina que o Personaler é treinado com uma média de 75-85% de rolamento médio, o modelo está pronto para mudar para o modo Online.

No portal Azure para o seu recurso Personalizer, na página **Configuração,** no **separador Comportamento de Aprendizagem,** selecione **Devolver a melhor ação** e selecione **Guardar**.

Não precisa de fazer alterações nas chamadas da API rank e do Reward.

## <a name="next-steps"></a>Passos seguintes

* [Gerir modelos e definições de aprendizagem](how-to-manage-model.md)
