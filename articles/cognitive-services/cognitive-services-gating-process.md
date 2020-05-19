---
title: Processo de marcha dos Serviços Cognitivos
titleSuffix: Azure Cognitive Services
description: Saiba como se candidatar ao acesso antecipado a novos recipientes e APIs de Serviços Cognitivos.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: a161cc0675eedee50608b8d6c288c57147fd6d23
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599515"
---
# <a name="gating-process-for-azure-cognitive-services"></a>Processo de gating para serviços cognitivos azure

> [!NOTE]
> Depois de uma oferta de serviço completar uma pré-visualização fechada, entra numa pré-visualização pública "ungated" que não requer um pedido de acesso. Após o processo de pré-visualização, é lançado como Geralmente Disponível (GA).

À medida que são introduzidas novas ofertas dos Serviços Cognitivos Azure, passam por uma pré-visualização fechada onde os clientes têm de solicitar acesso através de uma aplicação. Este processo de gating ajuda a identificar oportunidades para melhorias nas ofertas de serviços antes de estarem amplamente disponíveis. 

Este artigo irá guiá-lo através do processo de candidatura para ofertas de Serviços Cognitivos fechados.

## <a name="eligibility-and-approval-process"></a>Processo de elegibilidade e aprovação

O processo de marcha está em vigor para ajudar a avaliar o interesse e a compreender melhor as necessidades do cliente. A equipa da Microsoft aceita [aplicações](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u) de clientes comerciais da Microsoft com uma subscrição Azure válida e um cenário de negócio válido. Os clientes terão potencialmente as suas aplicações negadas quando:

 - Não estão associados a nenhuma organização.
 - Não têm uma subscrição Azure válida
 - O pedido foi submetido por e-mail pessoal @hotmail.com , @gmail.com @yahoo.com
 - Não havia justificação adequada ou cenário de negócio fornecido

Dada a procura de diferentes segmentos de clientes, estamos a tentar acelerar o processo de aprovação. No entanto, não podemos comprometer-nos com uma linha temporal. Uma vez tomada uma decisão, a equipa da Microsoft entrará em contacto consigo e com a sua equipa de gestão de contas para os próximos passos. Agradecemos a sua compreensão e paciência.

Se a aplicação for aprovada, a equipa da Microsoft enviará um e-mail com detalhes, documentação e orientação. Detalhes de preços dos Serviços Cognitivos disponíveis [aqui.](https://azure.microsoft.com/pricing/details/cognitive-services/)


Atualmente, os serviços abaixo são oferecidos através do processo de marcha:

## <a name="gated-web-apis"></a>APIs web fechados

|Serviço  |
|---------|
|Detetor de Anomaliav2     | 

## <a name="gated-online-containers"></a>Contentores on-line fechados

| Serviço                             | Recipientes                                                                  |
|-------------------------------------|-------------------------------------------------------------------------------|
| [Imagem Digitalizada][cv-containers]    | Leitura                                                                          |
| [Face][fa-containers]               | Face                                                                          |
| [Reconhecedor de Formato][fr-containers]    | Reconhecedor de Formato                                                               |
| [API de Serviço de Voz][sp-containers] | Discurso a texto (Personalizado e Normal) e Texto-a-Fala (Personalizado e Normal) |
| [Tradução de Texto][tt-containers]    | Tradução de Texto                                                               |

> [!IMPORTANT]
> Se uma oferta de serviço ou contentorizado não estiver listada, não está fechada ou indisponível.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Inscreva-se para serviços fechados](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)

[ad-containers]: ./anomaly-detector/anomaly-detector-container-howto.md
[cv-containers]: ./computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ./face/face-how-to-install-containers.md
[fr-containers]: ./form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ./luis/luis-container-howto.md
[sp-containers]: ./speech-service/speech-container-howto.md
[ta-containers]: ./text-analytics/how-tos/text-analytics-how-to-install-containers.md
[tt-containers]: ./translator/how-to-install-containers.md
