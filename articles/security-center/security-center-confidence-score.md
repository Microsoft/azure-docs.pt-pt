---
title: Pontuação de confiança no Centro de segurança do Azure | Documentos da Microsoft
description: " Saiba como trabalhar com a pontuação de confiança do Centro de segurança do Azure. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rkarlin
ms.openlocfilehash: 64bab5c1b99720eecb189834d7a11802cf919ca3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58106785"
---
# <a name="alert-confidence-score"></a>Pontuação de confiança de alerta 

Centro de segurança do Azure fornece-lhe visibilidade entre os recursos que executa no Azure e o alerta quando Deteta potenciais problemas. O volume de alertas pode ser um desafio para uma equipe de operações de segurança abordar individualmente e, se torna necessário priorizar os alertas para investigar. Investigar alertas pode ser complexa e demorada, e, consequentemente, alguns alertas são ignoradas.

A pontuação de confiança no Centro de segurança pode ajudar a triagem de sua equipe e dar prioridade a alertas. Centro de segurança aplica automaticamente as práticas recomendadas do setor, algoritmos inteligentes e processos utilizados pelos analistas para determinar se uma ameaça é legítima e fornece-lhe informações significativas na forma de uma pontuação de confiança.

## <a name="how-the-confidence-score-is-triggered"></a>Como a pontuação de confiança é acionada

Os alertas são gerados quando processos suspeitos são detetados em execução nas suas máquinas virtuais. Centro de segurança analisa e analisa estes alertas em máquinas virtuais do Windows em execução no Azure. Ele realiza verificações automatizadas e correlações utilizando algoritmos avançados em várias entidades e fontes de dados em toda a organização e todos os seus recursos do Azure e apresenta-se com uma pontuação que é uma medida confiante do Centro de segurança é de confiança que o alerta é genuíno e tem de ser investigado.

## <a name="understanding-the-confidence-score"></a>Noções básicas sobre a pontuação de confiança

A pontuação de confiança intervalos entre 1 e 100 e representa a confiança de que Centro de segurança tem de que o alerta tem de ser investigado. Quanto maior for a pontuação é, o Centro de segurança mais confiantes é que o alerta indica atividade maliciosa original. A pontuação de confiança inclui uma lista dos principais motivos por que o alerta recebido sua pontuação de confiança. A pontuação de confiança torna mais fácil para os analistas de segurança atribuir prioridades a respetiva resposta a alertas e o máximo de premir ataques pela primeira vez, em última análise, reduzindo a quantidade de tempo que demora a responder a ataques e violações de endereços.

Para ver a pontuação de confiança:
- No portal do Centro de segurança, abra o painel de alertas de segurança.
-  Os alertas e incidentes são organizados a partir mais alto ao mais baixo, que significa que o Centro de segurança mais confiantes é que um alerta representa uma ameaça, o mais próximo é na parte superior da página. 


 ![Pontuação de confiança][1]

Para ver os dados que contribuíram para confiança do Centro de segurança num alerta:
- Na segurança de alerta painel, em **confiança**, ver as observações que contribuíram para a classificação de confiança e obter informações relacionadas com o alerta. Isso fornece mais informações sobre a natureza das atividades que causou o alerta.

  ![Pontuação de confiança suspeita][2]

Pontuação de confiança do Centro de segurança de utilização para atribuir prioridades a triagem de alerta no seu ambiente. A pontuação de confiança economiza tempo e esforço por automaticamente para investigar alertas, aplicar práticas recomendadas do setor e algoritmos inteligentes e a funcionar como uma analista virtual para determinar quais ameaças têm real e em que precisa se concentrar sua atenção.


## <a name="next-steps"></a>Passos Seguintes
Este artigo explicou como usar a pontuação de confiança para priorizar a investigação de alerta. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
