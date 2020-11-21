---
title: Orientações para a implantação responsável da tecnologia de voz sintética
titleSuffix: Azure Cognitive Services
description: Diretrizes gerais de design da Microsoft para o uso de tecnologia de voz sintética. Estes foram desenvolvidos em estudos que a Microsoft conduziu com talento de voz, consumidores, bem como indivíduos com distúrbios da fala para orientar o desenvolvimento responsável da voz sintética.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: 7d80ffb575c6aa15695279584b58288cbc16be43
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024981"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Orientações para a implantação responsável da tecnologia de voz sintética
Aqui estão as diretrizes gerais de design da Microsoft para o uso de tecnologia de voz sintética. Estes foram desenvolvidos em estudos que a Microsoft conduziu com talento de voz, consumidores, bem como indivíduos com distúrbios da fala para orientar o desenvolvimento responsável da voz sintética.

## <a name="general-considerations"></a>Considerações gerais
Para a implementação da tecnologia de fala sintética, as seguintes diretrizes aplicam-se na maioria dos cenários.

### <a name="disclose-when-the-voice-is-synthetic"></a>Revele quando a voz é sintética
A divulgação de que uma voz é gerada por computador não só minimiza o risco de resultados nocivos da deceção, como também aumenta a confiança na organização que entrega a voz. Saiba mais sobre [como divulgar.](concepts-disclosure-guidelines.md)

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Selecione tipos de voz apropriados para o seu cenário
Considere cuidadosamente o contexto de utilização e os potenciais danos associados à utilização da voz sintética. Por exemplo, vozes sintéticas de alta fidelidade podem não ser adequadas em cenários de alto risco, tais como mensagens pessoais, transações financeiras ou situações complexas que requerem adaptabilidade humana ou empatia. Os utilizadores também podem ter expectativas diferentes para tipos de voz. Por exemplo, ao ouvir notícias sensíveis sendo lidas por uma voz sintética, alguns utilizadores preferem uma leitura mais empática e humana das notícias, enquanto outros preferiram uma voz mais monótona e imparcial. Considere testar a sua aplicação para entender melhor as preferências dos utilizadores.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Seja transparente sobre capacidades e limitações
Os utilizadores são mais propensos a ter expectativas mais altas quando interagem com agentes de voz sintéticos de alta fidelidade. Consequentemente, quando as capacidades do sistema não correspondem a essas expectativas, a confiança pode sofrer, e pode resultar em experiências desagradáveis, ou mesmo prejudiciais.

### <a name="provide-optional-human-support"></a>Fornecer apoio humano opcional
Em cenários ambíguos e transacionais (por exemplo, um centro de suporte a chamadas), os utilizadores nem sempre confiam num agente informático para responder adequadamente aos seus pedidos. O apoio humano pode ser necessário nestas situações, independentemente da qualidade realista da voz ou da capacidade do sistema.

## <a name="considerations-for-voice-talent"></a>Considerações para o talento de voz
Ao trabalhar com o talento da voz, como os atores de voz, para criar vozes sintéticas, a orientação abaixo aplica-se.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Obtenha consentimento significativo do talento de voz
Os talentos de voz esperam ter controlo sobre a sua fonte de voz (como e onde será usado) e ser compensado sempre que for usado. Os proprietários do sistema devem, portanto, obter uma autorização escrita explícita do talento de voz e ter especificações contratuais claras sobre casos de utilização, duração de utilização, compensação, e assim por diante. Alguns talentos de voz desconhecem os potenciais usos maliciosos da tecnologia e devem ser educados pelos proprietários do sistema sobre as capacidades da tecnologia. Para mais informações sobre o talento e consentimento da voz, leia a nossa [Divulgação para o Voice Talent.](/legal/cognitive-services/speech-service/disclosure-voice-talent)


## <a name="considerations-for-those-with-speech-disorders"></a>Considerações para aqueles com distúrbios da fala
Ao trabalhar com indivíduos com distúrbios da fala, criar ou implementar tecnologia de voz sintética, aplicam-se as seguintes orientações.

### <a name="provide-guidelines-to-establish-contracts"></a>Fornecer orientações para estabelecer contratos
Fornecer orientações para o estabelecimento de contratos com indivíduos que usam a voz sintética para assistência na fala. O contrato deve considerar especificar as partes proprietárias da voz, duração da utilização, critérios de transferência de propriedade, procedimentos para a eliminação do tipo de voz e como impedir o acesso não autorizado. Adicionalmente, permitir a transferência contratual da propriedade de fonte de voz após a morte para os membros da família se essa pessoa tiver dado permissão.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Contabilizar inconsistências nos padrões de fala
Para indivíduos com distúrbios da fala que registam as suas próprias fontes de voz, inconsistências no seu padrão de fala (arrastamento ou incapacidade de pronunciar certas palavras) podem complicar o processo de gravação. Nestes casos, a tecnologia de voz sintética e as sessões de gravação devem acomodá-las (isto é, proporcionar pausas e número adicional de sessões de gravação).

### <a name="allow-modification-over-time"></a>Permitir modificações ao longo do tempo
Os indivíduos com distúrbios da fala desejam fazer atualizações à sua voz sintética para refletir o envelhecimento (por exemplo, uma criança que chega à puberdade). Os indivíduos também podem ter preferências estilísticas que mudam ao longo do tempo, e podem querer fazer alterações no tom, sotaque ou outras características de voz.


## <a name="reference-docs"></a>Documentos de referência

* [Divulgação de Talento de Voz](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Visão geral de Gating](concepts-gating-overview.md)
* [Como Divulgar](concepts-disclosure-guidelines.md)
* [Padrões de design de divulgação](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>Próximos passos

* [Divulgação de Talento de Voz](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Como Divulgar](concepts-disclosure-guidelines.md)
* [Padrões de design de divulgação](concepts-disclosure-patterns.md)