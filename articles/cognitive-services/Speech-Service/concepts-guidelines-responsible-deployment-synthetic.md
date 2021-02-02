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
ms.openlocfilehash: 715c09ef65358b21e78cfde204b4819db0c7875d
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428424"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Orientações para a implantação responsável da tecnologia de voz sintética

## <a name="general-considerations-to-keep-in-mind-when-implementing-ai-systems"></a>Considerações gerais a ter em mente na implementação de sistemas de IA 

Este artigo fala especificamente sobre a fala sintética e a Voz Neural Personalizada e as principais considerações para fazer uso desta tecnologia de forma responsável. Em geral, no entanto, há várias coisas que deve considerar cuidadosamente ao decidir como usar e implementar produtos e funcionalidades movidos a IA: 

* Este produto ou recurso terá um bom desempenho no meu cenário? Antes de colocar a IA no seu cenário, teste como executa usando dados da vida real e certifique-se de que pode fornecer a precisão de que necessita. 
* Estamos equipados para identificar e responder a erros? Os produtos e funcionalidades alimentados pela IA nem sempre serão 100% precisos, por isso considere como irá identificar e responder a quaisquer erros que possam ocorrer. 

## <a name="general-guidelines-for-using-synthetic-voice-technology"></a>Diretrizes gerais para o uso de tecnologia de voz sintética 
Aqui estão as diretrizes gerais de design da Microsoft para o uso de tecnologia de voz sintética. Estes foram desenvolvidos em estudos que a Microsoft conduziu com talento de voz, consumidores, bem como indivíduos com distúrbios da fala para orientar o desenvolvimento responsável da voz sintética.

Para a implementação da tecnologia de fala sintética, as seguintes diretrizes aplicam-se na maioria dos cenários.

### <a name="disclose-when-the-voice-is-synthetic"></a>Revele quando a voz é sintética
A divulgação de que uma voz é gerada por computador não só minimiza o risco de resultados nocivos da deceção, como também aumenta a confiança na organização que entrega a voz. Saiba mais sobre [como divulgar.](concepts-disclosure-guidelines.md)

A Microsoft exige que os seus clientes revelem a natureza sintética da voz neural personalizada aos seus utilizadores. 
* Certifique-se de fornecer uma divulgação adequada ao público, especialmente quando se usa a voz de uma pessoa conhecida - As pessoas fazem o seu julgamento sobre informações baseadas na pessoa que a entrega, quer o façam consciente ou inconscientemente.  Por exemplo, a divulgação poderia ser partilhada verbalmente no início de uma transmissão. Para mais informações visite os [padrões de divulgação.](concepts-disclosure-patterns.md)   
* Considere a divulgação adequada aos pais ou outras partes com casos de uso destinados a menores e crianças - Se o seu caso de uso for destinado a menores ou crianças, terá de garantir que os pais ou tutores legais sejam capazes de compreender a divulgação sobre o uso de meios sintéticos e tomar a decisão certa para os menores ou crianças sobre se devem ou não utilizar a experiência. 

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Selecione tipos de voz apropriados para o seu cenário
Considere cuidadosamente o contexto de utilização e os potenciais danos associados à utilização da voz sintética. Por exemplo, vozes sintéticas de alta fidelidade podem não ser adequadas em cenários de alto risco, tais como mensagens pessoais, transações financeiras ou situações complexas que requerem adaptabilidade humana ou empatia. Os utilizadores também podem ter expectativas diferentes para tipos de voz. Por exemplo, ao ouvir notícias sensíveis sendo lidas por uma voz sintética, alguns utilizadores preferem uma leitura mais empática e humana das notícias, enquanto outros preferiram uma voz mais monótona e imparcial. Considere testar a sua aplicação para entender melhor as preferências dos utilizadores.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Seja transparente sobre capacidades e limitações
Os utilizadores são mais propensos a ter expectativas mais altas quando interagem com agentes de voz sintéticos de alta fidelidade. Consequentemente, quando as capacidades do sistema não correspondem a essas expectativas, a confiança pode sofrer, e pode resultar em experiências desagradáveis, ou mesmo prejudiciais.

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

## <a name="next-steps"></a>Passos seguintes

* [Divulgação de Talento de Voz](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Como Divulgar](concepts-disclosure-guidelines.md)
* [Padrões de design de divulgação](concepts-disclosure-patterns.md)
