---
title: Perguntas mais frequentes sobre o serviço de texto em voz no Azure
titleSuffix: Azure Cognitive Services
description: Obtenha respostas às perguntas mais populares sobre o serviço de texto em voz.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: fd8362748c39389139e8384d0bad7e84d20128a4
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66751623"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Perguntas mais frequentes sobre o texto em voz

Se não conseguir encontrar respostas para suas perguntas neste FAQ, confira [outras opções de suporte](support.md).

## <a name="general"></a>Geral

**P: O que é a diferença entre um modelo de voz padrão e um modelo de voz personalizada?**

**A**: O modelo de voz standard (também chamado de um *tipo de voz*) foi treinado, utilizando dados pertencentes à Microsoft e já estiver implementado na cloud. Pode utilizar um modelo de voz personalizada para adaptar um modelo de média e transferir o timbre e a expressão de estilo de voz do orador ou preparar um modelo completo, novo com base nos dados de treinamento preparados pelo utilizador. Atualmente, cada vez mais clientes desejam uma voz um do-única, com marca corporativa para os respetivos bots. A plataforma de criação de voz personalizada é a escolha certa para essa opção.

**P: Por onde começar se eu quiser usar um modelo de voz padrão?**

**A**: Mais de 80 modelos de voz padrão em mais de 45 idiomas estão disponíveis por meio de solicitações HTTP. Primeiro, obtenha uma [chave de subscrição](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started). Para fazer chamadas REST para os modelos de voz predeployed, consulte a [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

**P: Se quiser utilizar um modelo de voz personalizadas, é a API igual ao que é utilizado para vozes padrão?**

**A**: Quando um modelo de voz personalizada é criado e implementado, receberá um ponto de extremidade exclusivo para o seu modelo. Para utilizar a voz para falar nas suas aplicações, tem de especificar o ponto de extremidade nos seus pedidos HTTP. A mesma funcionalidade que está disponível na API REST para o serviço de texto em voz está disponível para o seu ponto final personalizado. Saiba como [criar e utilizar o seu ponto final personalizado](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-voice-endpoint).

**P: É necessário preparar os dados de treinamento para criar modelos de voz personalizada no meu próprio?**

**A**: Sim, tem de preparar os dados de treinamento-se para um modelo de voz personalizada.

Uma coleção de dados de voz é necessário para criar um modelo de voz personalizada. Esta coleção é composta por um conjunto de arquivos de áudio de gravações de voz e um arquivo de texto da transcrição de cada arquivo de áudio. O resultado da sua voz a digital depende muito da qualidade dos seus dados de treinamento. Para produzir uma voz de texto para discurso bom, é importante que as gravações sejam feitas num ambiente silencioso com um microfone de pé, de alta qualidade. Um volume consistente, taxa de fala e fala pitch e consistência, mesmo em mannerisms expressivas de voz são essenciais para criar uma voz digital excelente. É altamente recomendável a gravar as vozes num estúdio de gravação.

Atualmente, não fornecem suporte de gravação online nem ter quaisquer recomendações de studio de gravação. Para o requisito de formato, consulte [como preparar as gravações e transcrições](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-create-voice).

**P: Que scripts posso usar para gravar os dados de voz para a formação de voz personalizada?**

**A**: Não limitamos os scripts para gravação de voz. Pode utilizar os seus próprios scripts para registrar a conversão de voz. Certifique-se apenas de que tem cobertura fonética suficiente nos seus dados de voz. Para preparar uma voz personalizada, pode começar com um pequeno volume de dados de voz, o que podem ser 50 frases diferentes (cerca de 3 a 5 minutos de voz). Quanto mais dados fornecer, quanto mais natural será sua voz. Pode começar a preparar um tipo de voz completa, quando o utilizador fornecer gravações de mais de 2.000 frases (cerca de 3 a 4 horas de voz). Para obter uma alta qualidade, voz completa, terá de preparar gravações de mais de 6.000 frases (cerca de 8 a 10 horas de voz).

Podemos fornecer serviços adicionais para o ajudar a preparar os scripts para gravação. Contacte [suporte ao cliente de voz personalizada](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) para consultas.

**P: E se eu precisar de simultaneidade mais elevada do que o valor predefinido ou o que está disponível no portal?**

**A**: Pode aumentar verticalmente o seu modelo em incrementos de 20 pedidos simultâneos. Contacte [suporte ao cliente de voz personalizada](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) para consultas sobre o dimensionamento superior.

**P: Pode transferir o meu modelo e executá-lo localmente?**

**A**: Modelos não podem ser transferidos e executados localmente.

**P: Os meus pedidos limitados?**

**A**: A API REST limita os pedidos para 25 por 5 segundos. Pode encontrar detalhes em nossas páginas para [texto em voz](text-to-speech.md). 

## <a name="next-steps"></a>Passos Seguintes

* [Resolução de problemas](troubleshooting.md)
* [Notas de versão](releasenotes.md)
