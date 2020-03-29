---
title: Orientações para a implantação responsável da tecnologia de voz sintética
titleSuffix: Azure Cognitive Services
description: As diretrizes gerais de design da Microsoft para o uso de tecnologia de voz sintética. Estes foram desenvolvidos em estudos que a Microsoft conduziu com talento de voz, consumidores, bem como indivíduos com distúrbios da fala para orientar o desenvolvimento responsável da voz sintética.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836770"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Orientações para a implantação responsável da tecnologia de voz sintética
Aqui estão as diretrizes gerais de design da Microsoft para usar a tecnologia de voz sintética. Estes foram desenvolvidos em estudos que a Microsoft conduziu com talento de voz, consumidores, bem como indivíduos com distúrbios da fala para orientar o desenvolvimento responsável da voz sintética.

## <a name="general-considerations"></a>Considerações gerais
Para a implantação da tecnologia de fala sintética, as seguintes orientações aplicam-se na maioria dos cenários.

### <a name="disclose-when-the-voice-is-synthetic"></a>Divulgar quando a voz é sintética
Revelar que uma voz é gerada por computador não só minimiza o risco de resultados nocivos do engano, como também aumenta a confiança na organização que entrega a voz. Saiba mais sobre [como divulgar.](concepts-disclosure-guidelines.md)

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Selecione tipos de voz apropriados para o seu cenário
Considere cuidadosamente o contexto de utilização e os potenciais danos associados ao uso da voz sintética. Por exemplo, vozes sintéticas de alta fidelidade podem não ser adequadas em cenários de alto risco, tais como mensagens pessoais, transações financeiras ou situações complexas que requerem adaptabilidade humana ou empatia. Os utilizadores também podem ter expectativas diferentes para tipos de voz. Por exemplo, ao ouvir notícias sensíveis sendo lidas por uma voz sintética, alguns utilizadores preferem uma leitura mais empática e humana das notícias, enquanto outros preferiram uma voz mais monótona e imparcial. Considere testar a sua aplicação para entender melhor as preferências dos utilizadores.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Seja transparente sobre capacidades e limitações
Os utilizadores são mais propensos a ter expectativas mais altas quando interagem com agentes de voz sintéticos de alta fidelidade. Consequentemente, quando as capacidades do sistema não correspondem a essas expectativas, a confiança pode sofrer, e pode resultar em experiências desagradáveis, ou mesmo prejudiciais.

### <a name="provide-optional-human-support"></a>Fornecer apoio humano opcional
Em cenários ambíguos e transacionais (por exemplo, um call support center), os utilizadores nem sempre confiam num agente informático para responder adequadamente aos seus pedidos. O apoio humano pode ser necessário nestas situações, independentemente da qualidade realista da voz ou capacidade do sistema.

## <a name="considerations-for-voice-talent"></a>Considerações para o talento de voz
Ao trabalhar com o talento de voz, como os atores de voz, para criar vozes sintéticas, aplica-se a orientação abaixo.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Obtenha consentimento significativo do talento de voz
O talento de voz espera ter controlo sobre a sua fonte de voz (como e onde será usado) e ser compensado sempre que for usado. Por conseguinte, os proprietários do sistema devem obter autorização escrita explícita do talento de voz e ter especificações contratuais claras sobre os casos de utilização, duração da utilização, compensação, e assim por diante. Alguns talentos de voz desconhecem os potenciais usos maliciosos da tecnologia e devem ser educados pelos proprietários do sistema sobre as capacidades da tecnologia. Para mais informações sobre talento de voz e consentimento, leia a nossa [Divulgação de Talento](https://aka.ms/disclosure-voice-talent)sonoro.


## <a name="considerations-for-those-with-speech-disorders"></a>Considerações para aqueles com distúrbios da fala
Quando se trabalha com indivíduos com distúrbios da fala, para criar ou implementar tecnologia de voz sintética, aplicam-se as seguintes orientações.

### <a name="provide-guidelines-to-establish-contracts"></a>Fornecer orientações para estabelecer contratos
Fornecer orientações para estabelecer contratos com indivíduos que utilizem voz sintética para assistência na fala. O contrato deve considerar especificar as partes que possuem a voz, a duração da utilização, os critérios de transferência de propriedade, os procedimentos de abater o tipo de letra de voz e como impedir o acesso não autorizado. Além disso, permitir a transferência contratual da propriedade da fonte de voz após a morte para os membros da família se essa pessoa tiver dado permissão.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Contabilize inconsistências nos padrões de fala
Para indivíduos com distúrbios da fala que registam as suas próprias fontes de voz, inconsistências no seu padrão de fala (slurring ou incapacidade de pronunciar certas palavras) podem complicar o processo de gravação. Nestes casos, a tecnologia de voz sintética e as sessões de gravação devem acomodá-las (isto é, proporcionar pausas e um número adicional de sessões de gravação).

### <a name="allow-modification-over-time"></a>Permitir modificação ao longo do tempo
Indivíduos com distúrbios da fala desejam fazer atualizações à sua voz sintética para refletir o envelhecimento (por exemplo, uma criança chegando à puberdade). Os indivíduos também podem ter preferências estilísticas que mudam ao longo do tempo, e podem querer fazer alterações no tom, no sotaque ou noutras características de voz.


## <a name="reference-docs"></a>Doutorados de referência

* [Divulgação de Talento de Voz](https://aka.ms/disclosure-voice-talent)
* [Visão geral de Gating](concepts-gating-overview.md)
* [Como Divulgar](concepts-disclosure-guidelines.md)
* [Padrões de design de divulgação](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>Passos seguintes

* [Divulgação de Talento de Voz](https://aka.ms/disclosure-voice-talent)
* [Como Divulgar](concepts-disclosure-guidelines.md)
* [Padrões de design de divulgação](concepts-disclosure-patterns.md)
