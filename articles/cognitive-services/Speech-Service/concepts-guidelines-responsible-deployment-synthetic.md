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
ms.openlocfilehash: 9a7a8868497433ea0de8f2f8b32f8e8fbaa497eb
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537189"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Orientações para a implantação responsável da tecnologia de voz sintética

Neste artigo, você aprende sobre as diretrizes gerais de design da Microsoft para usar a tecnologia de voz sintética. Estas diretrizes foram desenvolvidas em estudos que a Microsoft conduziu com talento de voz, consumidores e indivíduos com distúrbios da fala para orientar o desenvolvimento responsável de vozes sintéticas.

Para a implementação da tecnologia de fala sintética, as seguintes diretrizes aplicam-se na maioria dos cenários.

### <a name="disclose-when-the-voice-is-synthetic"></a>Revele quando a voz é sintética
A divulgação de que uma voz é gerada por computador não só minimiza o risco de resultados nocivos da deceção, como também aumenta a confiança na organização que entrega a voz. Saiba mais sobre [como divulgar.](concepts-disclosure-guidelines.md)

A Microsoft exige que os seus clientes revelem a natureza sintética da voz neural personalizada aos seus utilizadores. 
* Certifique-se de fornecer uma divulgação adequada ao público, especialmente quando se usa a voz de uma pessoa conhecida - As pessoas fazem o seu julgamento sobre informações baseadas na pessoa que a entrega, quer o façam consciente ou inconscientemente.  Por exemplo, a divulgação poderia ser partilhada verbalmente no início de uma transmissão. Para mais informações visite os [padrões de divulgação.](concepts-disclosure-patterns.md)   
* Considere a divulgação adequada aos pais ou outras partes com casos de uso destinados a menores e crianças - Se o seu caso de uso for destinado a menores ou crianças, terá de garantir que os pais ou tutores legais sejam capazes de compreender a divulgação sobre o uso de meios sintéticos e tomar a decisão certa para os menores ou crianças sobre se devem ou não utilizar a experiência. 

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Selecione tipos de voz apropriados para o seu cenário
Considere cuidadosamente o contexto de utilização e os potenciais danos associados à utilização da voz sintética. Por exemplo, vozes sintéticas de alta fidelidade podem não ser adequadas em cenários de alto risco, tais como mensagens pessoais, transações financeiras ou situações complexas que requerem adaptabilidade humana ou empatia. 

Os utilizadores também podem ter expectativas diferentes para tipos de voz. Por exemplo, ao ouvir notícias sensíveis lidas por uma voz sintética, alguns utilizadores preferem um tom mais empático e humano, enquanto outros preferem uma voz imparcial. Considere testar a sua aplicação para entender melhor as preferências dos utilizadores.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Seja transparente sobre capacidades e limitações
Os utilizadores são mais propensos a ter expectativas mais altas quando interagem com agentes de voz sintéticos de alta fidelidade. Quando as capacidades do sistema não correspondem a essas expectativas, a confiança pode sofrer, e pode resultar em experiências desagradáveis, ou mesmo prejudiciais.

### <a name="provide-optional-human-support"></a>Fornecer apoio humano opcional
Em cenários ambíguos e transacionais (por exemplo, um centro de suporte a chamadas), os utilizadores nem sempre confiam num agente informático para responder adequadamente aos seus pedidos. O apoio humano pode ser necessário nestas situações, independentemente da qualidade realista da voz ou da capacidade do sistema.

## <a name="considerations-for-voice-talent"></a>Considerações para o talento de voz
Ao trabalhar com o talento da voz, como os atores de voz, para criar vozes sintéticas, a orientação abaixo aplica-se.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Obtenha consentimento significativo do talento de voz
Os talentos de voz devem ter controlo sobre o seu modelo de voz (como e onde será usado) e ser compensados pelo seu uso. A Microsoft exige que os clientes de voz personalizados obtenham uma permissão escrita explícita do seu talento de voz para criar uma voz sintética e o seu acordo com os talentos de voz contemplam a duração, uso e quaisquer limitações de conteúdo.  Se estiver a criar uma voz sintética de uma pessoa conhecida, deve fornecer uma forma de a pessoa por trás da voz editar ou aprovar o conteúdo.

Alguns talentos de voz desconhecem os potenciais usos maliciosos da tecnologia e devem ser educados pelos proprietários do sistema sobre as capacidades da tecnologia. A Microsoft exige que os clientes partilhem a [Divulgação](/legal/cognitive-services/speech-service/disclosure-voice-talent) de Talentos de Voz da Microsoft com o Voice Talent diretamente ou através do representante autorizado da Voice Talent que descreve como as vozes sintéticas são desenvolvidas e operam em conjunto com os serviços de texto para a fala.

## <a name="considerations-for-those-with-speech-disorders"></a>Considerações para aqueles com distúrbios da fala
Ao trabalhar com indivíduos com distúrbios da fala, criar ou implementar tecnologia de voz sintética, aplicam-se as seguintes orientações.

### <a name="provide-guidelines-to-establish-contracts"></a>Fornecer orientações para estabelecer contratos
Fornecer orientações para o estabelecimento de contratos com indivíduos que usam a voz sintética para assistência na fala. O contrato deve considerar especificar as partes proprietárias da voz, duração da utilização, critérios de transferência de propriedade, procedimentos para a eliminação do tipo de voz e como impedir o acesso não autorizado. Adicionalmente, permitir a transferência da propriedade do tipo de letra de voz após a morte para os membros da família, se tiver sido dada permissão.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Contabilizar inconsistências nos padrões de fala
Para indivíduos com distúrbios da fala que registam as suas próprias fontes de voz, inconsistências no seu padrão de fala (arrastamento ou incapacidade de pronunciar certas palavras) podem complicar o processo de gravação. Nestes casos, a tecnologia de voz sintética e as sessões de gravação devem acomodá-las (isto é, proporcionar pausas e número adicional de sessões de gravação).

### <a name="allow-modification-over-time"></a>Permitir modificações ao longo do tempo
Os indivíduos com distúrbios da fala desejam fazer atualizações à sua voz sintética para refletir o envelhecimento (por exemplo, uma criança que chega à puberdade). Os indivíduos também podem ter preferências estilísticas que mudam ao longo do tempo, e podem querer fazer alterações no tom, sotaque ou outras características de voz.


## <a name="see-also"></a>Ver também

* [Divulgação de Talento de Voz](https://docs.microsoft.com/legal/cognitive-services/speech-service/disclosure-voice-talent?context=/azure/cognitive-services/speech-service/context/context)
* [Como Divulgar](concepts-disclosure-guidelines.md)
* [Padrões de design de divulgação](concepts-disclosure-patterns.md)