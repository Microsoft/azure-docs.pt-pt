---
title: Pontuação de confiança no Centro de Segurança Azure Microsoft Docs
description: O Security Center gera pontuações de confiança para ajudar a sua equipa a determinar se uma ameaça é legítima e como triagem e priorizar alertas.
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
ms.openlocfilehash: 8aa17f473c550c99d91862754e6a746575aca202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604378"
---
# <a name="alert-confidence-score-preview"></a>Pontuação de confiança de alerta (Pré-visualização)

O Azure Security Center proporciona-lhe visibilidade através dos recursos que executa em Azure, e alerta-o quando deteta potenciais problemas. O volume de alertas pode ser um desafio para uma equipa de operações de segurança abordar individualmente, e torna-se necessário priorizar quais os alertas a investigar. Investigar alertas pode ser complexo e demorado, e como resultado, alguns alertas são ignorados.

A pontuação de confiança (atualmente em pré-visualização) no Centro de Segurança pode ajudar a sua equipa a triagem e priorizar alertas. O Security Center aplica automaticamente as melhores práticas da indústria, algoritmos inteligentes e processos usados pelos analistas para determinar se uma ameaça é legítima e fornece-lhe informações significativas sob a forma de uma pontuação de confiança.

## <a name="how-the-confidence-score-is-triggered"></a>Como a pontuação de confiança é desencadeada

Os alertas são gerados quando são detetados processos suspeitos a funcionar nas suas máquinas virtuais. O Security Center analisa e analisa estes alertas em máquinas virtuais do Windows em funcionamento em Azure. Realiza verificações e correlações automatizadas usando algoritmos avançados em várias entidades e fontes de dados em toda a organização, e todos os seus recursos Azure, e apresenta uma pontuação de confiança que é uma medida de quão confiante o Centro de Segurança é que o alerta é genuíno e precisa de ser investigado.

## <a name="understanding-the-confidence-score"></a>Compreender a pontuação da confiança

A pontuação de confiança varia entre 1 e 100 e representa a confiança que o Centro de Segurança tem de que o alerta precisa de ser investigado. Quanto maior for a pontuação, mais confiante é o Centro de Segurança que o alerta indica atividade maliciosa genuína. A pontuação de confiança inclui uma lista das principais razões pelas quais o alerta recebeu a sua pontuação de confiança. A pontuação de confiança facilita que os analistas de segurança priorizem a sua resposta aos alertas e abordem primeiro os ataques mais prementes, reduzindo em última análise o tempo que leva para responder a ataques e violações.

Para ver a pontuação de confiança:
- No portal do Centro de Segurança, abra a lâmina de alertas de segurança.
-  Os alertas e incidentes são organizados do mais alto para o mais baixo, o que significa que o Centro de Segurança mais confiante é que um alerta representa uma ameaça, quanto mais perto está do topo da página. 


 ![Pontuação de confiança][1]

Para ver os dados que contribuíram para a confiança do Centro de Segurança num alerta:
- Na lâmina de alerta de Segurança, sob **confiança,** veja as observações que contribuíram para a pontuação de confiança e obtenha insights relacionados com o alerta. Isto fornece-lhe mais informações sobre a natureza das atividades que causaram o alerta.

  ![Pontuação de confiança suspeita][2]

Use a pontuação de confiança do Centro de Segurança para priorizar a triagem de alerta no seu ambiente. A pontuação de confiança poupa-lhe tempo e esforço, investigando automaticamente alertas, aplicando as melhores práticas da indústria e algoritmos inteligentes, e agindo como um analista virtual para determinar quais ameaças são reais e onde precisa concentrar a sua atenção.


<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
