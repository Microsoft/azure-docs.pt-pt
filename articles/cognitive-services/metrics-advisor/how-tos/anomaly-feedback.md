---
title: Fornecer feedback de anomalia ao serviço Métrica Advisor
titleSuffix: Azure Cognitive Services
description: Saiba como enviar feedback sobre anomalias encontradas pelo seu exemplo de Metrics Advisor e afinar os resultados.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 50d422edf1a4b45132d0b86eac9d4947cef5e5bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90938046"
---
# <a name="adjust-anomaly-detection-using-feedback"></a>Ajustar a deteção de anomalias através de comentários

Se não estiver satisfeito com alguns dos resultados de deteção de anomalias fornecidos pelo Metrics Monitor, pode adicionar manualmente feedback que irá afetar o modelo aplicado aos seus dados. 

Utilize os botões no canto superior direito da página para ativar o modo de anotação de feedback.

:::image type="content" source="../media/feedback/annotation-mode.png" alt-text="Modo de anotação de feedback.":::

Após o modo de anotação de feedback ser ativado, pode dar feedback por um ponto ou vários pontos contínuos.

## <a name="give-feedback-for-one-point"></a>Dar feedback por um ponto 

Com o modo de anotação de feedback ativado, clique num ponto para abrir um painel **de feedback Add.** Pode definir o tipo de feedback que pretende aplicar. Este feedback será incorporado na deteção de pontos futuros.  

* Selecione **Anomalia** se achar que o ponto foi incorretamente rotulado pelo Metrics Monitor. Pode especificar se um ponto deve ou não ser uma anomalia. 
* Selecione **ChangePoint** se achar que o ponto indica o início de uma mudança de tendência.
* Selecione **Período** para indicar sazonalidade. O Monitor métrico pode detetar automaticamente intervalos de sazonalidade, ou pode especificar manualmente. 

Considere deixar um comentário na caixa de texto **do Comentário** ao mesmo tempo e clique em **Guardar** para guardar o seu feedback.

:::image type="content" source="../media/feedback/feedback-menu.png" alt-text="Modo de anotação de feedback.":::

## <a name="give-feedback-for-multiple-continuous-points"></a>Dar feedback para vários pontos contínuos

Pode dar feedback para vários pontos contínuos ao mesmo tempo clicando e arrastando o rato nos pontos que deseja anotar. Verá o mesmo menu de feedback acima. O mesmo feedback será aplicado em todos os pontos escolhidos depois de clicar em **Guardar**.

:::image type="content" source="../media/feedback/continuous-points.png" alt-text="Modo de anotação de feedback.":::

## <a name="how-to-view-my-feedback"></a>Como ver o meu feedback

Para ver se a deteção de anomalias de um ponto mudou, paire sobre o ponto. A ponta da ferramenta mostrará **afetada pelo feedback: verdadeiro** se a deteção foi alterada. Se apresentar **Falso,** então o cálculo do feedback foi feito no ponto, mas o resultado da deteção de anomalias não foi alterado.

:::image type="content" source="../media/feedback/affected-point.png" alt-text="Modo de anotação de feedback.":::

## <a name="when-should-i-annotate-an-anomaly-as-normal"></a>Quando devo anotar uma anomalia como "normal"

Há muitas razões para considerar que uma anomalia é um alarme falso. Considere utilizar as seguintes funcionalidades do Metrics Advisor se um dos seguintes cenários se aplicar:


|Cenário  |Recomendação |
|---------|---------|
|A anomalia é causada por uma alteração conhecida da fonte de dados, por exemplo, uma mudança de sistema.     | Não anota esta anomalia se este cenário não for esperado regularmente.        |
|A anomalia é causada por férias.     | Utilize [eventos predefinidos](configure-metrics.md#preset-events) para deteção de anomalias de bandeira em horas especificadas.       |
|Existe um padrão regular para detetar anomalias (por exemplo, aos fins de semana) e não devem ser anomalias.      |Utilize a função de feedback ou eventos predefinidos.        |

## <a name="next-steps"></a>Passos seguintes
- [Diagnosticar um incidente.](diagnose-incident.md)
- [Configurar métricas e otimizar a configuração da deteção](configure-metrics.md)
- [Configurar alertas e obter notificações através de um alerta](../how-tos/alerts.md)