---
title: Pontuação de confiança na central de segurança do Azure | Microsoft Docs
description: " Saiba como trabalhar com a pontuação de confiança da central de segurança do Azure. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: memildin
ms.openlocfilehash: 0f0380ca4a285d9cb4a8472a40c77130a8f63035
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202375"
---
# <a name="alert-confidence-score"></a>Pontuação de confiança de alerta 

A central de segurança do Azure fornece visibilidade sobre os recursos que você executa no Azure e o alerta quando detecta problemas em potencial. O volume de alertas pode ser desafiador para uma equipe de operações de segurança para endereçar individualmente e é necessário priorizar quais alertas investigar. A investigação de alertas pode ser complexa e demorada e, consequentemente, alguns alertas são ignorados.

A pontuação de confiança na central de segurança pode ajudar sua equipe a fazer a triagem e priorizar alertas. A central de segurança aplica automaticamente práticas recomendadas do setor, algoritmos inteligentes e processos usados por analistas para determinar se uma ameaça é legítima e fornece informações significativas na forma de uma pontuação de confiança.

## <a name="how-the-confidence-score-is-triggered"></a>Como a pontuação de confiança é disparada

Os alertas são gerados quando processos suspeitos são detectados em execução em suas máquinas virtuais. A central de segurança revisa e analisa esses alertas em máquinas virtuais do Windows em execução no Azure. Ele executa verificações e correlações automatizadas usando algoritmos avançados em várias entidades e fontes de dados em toda a organização, além de todos os recursos do Azure e apresenta uma pontuação de confiança que é uma medida de quão confiante é a central de segurança Se o alerta é original e precisa ser investigado.

## <a name="understanding-the-confidence-score"></a>Compreendendo a pontuação de confiança

A pontuação de confiança varia entre 1 e 100 e representa a central de segurança de confiança que o alerta precisa ser investigado. Quanto mais alta for a pontuação, mais segura será a central de segurança que o alerta indicará atividades mal-intencionadas autênticas. A pontuação de confiança inclui uma lista dos principais motivos pelos quais o alerta recebeu sua pontuação de confiança. A pontuação de confiança torna mais fácil para os analistas de segurança priorizar sua resposta a alertas e resolver os ataques mais urgentes primeiro, reduzindo, por fim, a quantidade de tempo que leva para responder a ataques e violações.

Para exibir a pontuação de confiança:
- No portal da central de segurança, abra a folha alertas de segurança.
-  Os alertas e incidentes são organizados do mais alto para o mais baixo, o que significa que a central de segurança mais segura é que um alerta representa uma ameaça, quanto mais próximo ele está na parte superior da página. 


 ![Pontuação de confiança][1]

Para exibir os dados que contribuíram para a confiança da central de segurança em um alerta:
- Na folha alerta de segurança, em **confiança**, exiba as observações que contribuíram para a pontuação de confiança e obter informações relacionadas ao alerta. Isso fornece mais informações sobre a natureza das atividades que causaram o alerta.

  ![Pontuação de confiança suspeita][2]

Use a pontuação de confiança da central de segurança para priorizar a triagem de alerta em seu ambiente. A pontuação de confiança poupa tempo e esforço ao investigar automaticamente os alertas, aplicar práticas recomendadas do setor e algoritmos inteligentes e agir como analista virtual para determinar quais ameaças são reais e onde você precisa concentrar sua atenção.


## <a name="next-steps"></a>Passos seguintes
Este artigo explicou como usar a pontuação de confiança para priorizar a investigação de alertas. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
