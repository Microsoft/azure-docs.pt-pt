---
title: Texto para Discurso frequentemente perguntas
titleSuffix: Azure Cognitive Services
description: Obtenha respostas às perguntas frequentes sobre o serviço Texto à Fala.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.openlocfilehash: 89ddbca7a216421aad7240890a697f861d14fd1f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026357"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Texto para Discurso frequentemente perguntas

Se não conseguir encontrar respostas para as suas perguntas nestas PERGUNTAS, consulte [outras opções de suporte.](../cognitive-services-support-options.md?context=%252fazure%252fcognitive-services%252fspeech-service%252fcontext%252fcontext%253fcontext%253d%252fazure%252fcognitive-services%252fspeech-service%252fcontext%252fcontext)

## <a name="general"></a>Geral

**P: Qual é a diferença entre um modelo de voz padrão e um modelo de voz personalizado?**

**R**: O modelo de voz padrão (também chamado de _fonte de voz_) foi treinado utilizando dados da Microsoft e já está implantado na nuvem. Pode utilizar um modelo de voz personalizado para adaptar um modelo médio e transferir o timbre e expressão do estilo de voz do orador ou formar um modelo completo e novo com base nos dados de treino preparados pelo utilizador. Hoje em dia, cada vez mais clientes querem uma voz única e marcada para os seus bots. A plataforma de construção de voz personalizada é a escolha certa para esta opção.

**P: Por onde começo se quero usar um modelo de voz padrão?**

**R**: Mais de 80 modelos de voz padrão em mais de 45 idiomas estão disponíveis através de pedidos HTTP. Primeiro, obtenha uma [chave de subscrição.](./overview.md#try-the-speech-service-for-free) Para fazer chamadas REST para os modelos de voz pré-deplorados, consulte a [API REST](./overview.md#reference-docs).

**P: Se eu quiser usar um modelo de voz personalizado, a API é a mesma que é usada para vozes padrão?**

**R**: Quando um modelo de voz personalizado é criado e implantado, obtém-se um ponto final único para o seu modelo. Para utilizar a voz para falar nas suas apps, tem de especificar o ponto final nos seus pedidos HTTP. A mesma funcionalidade que está disponível na API REST para o serviço Texto à Fala está disponível para o seu ponto final personalizado. Saiba como [criar e utilizar o seu ponto de terminação personalizado.](./how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint)

**P: Preciso de preparar os dados de treino para criar modelos de voz personalizados por conta própria?**

**R:** Sim, deve preparar os dados de treino para um modelo de voz personalizado.

É necessária uma recolha de dados de fala para criar um modelo de voz personalizado. Esta coleção consiste num conjunto de ficheiros áudio de gravações de fala e um ficheiro de texto da transcrição de cada ficheiro áudio. O resultado da sua voz digital depende fortemente da qualidade dos seus dados de treino. Para produzir uma boa voz de texto-a-voz, é importante que as gravações sejam feitas numa sala tranquila com um microfone de alta qualidade e de pé. Um volume consistente, uma taxa de fala e um tom de fala, e até mesmo a consistência nos maneirismos expressivos da fala são essenciais para a construção de uma grande voz digital. Recomendamos vivamente gravar as vozes num estúdio de gravação.

Atualmente, não fornecemos suporte de gravação on-line ou temos quaisquer recomendações de estúdio de gravação. Para a exigência do formato, consulte [como preparar gravações e transcrições](./how-to-custom-voice-create-voice.md).

**P: Que scripts devo usar para gravar os dados da fala para treino de voz personalizado?**

**R**: Não limitamos os scripts para gravação de voz. Podes usar os teus próprios guiões para gravar o discurso. Certifique-se apenas de que tem cobertura fonética suficiente nos seus dados de fala. Para treinar uma voz personalizada, pode começar com um pequeno volume de dados de fala, que podem ser 50 frases diferentes (cerca de 3-5 minutos de discurso). Quanto mais dados forneceres, mais natural será a tua voz. Pode começar a treinar uma fonte de voz completa quando fornecer gravações de mais de 2.000 frases (cerca de 3-4 horas de discurso). Para obter uma voz de alta qualidade e plena, você precisa preparar gravações de mais de 6.000 frases (cerca de 8-10 horas de discurso).

Fornecemos serviços adicionais para ajudá-lo a preparar scripts para a gravação. Contacte [o suporte ao cliente da Custom Voice](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) para obter informações.

**P: E se eu precisar de maior concordância do que o valor padrão ou o que é oferecido no portal?**

**R**: Pode escalar o seu modelo em incrementos de 20 pedidos simultâneos. Contacte [o suporte do cliente Da Voz Personalizada](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) para obter informações sobre o dimensionamento mais elevado.

**P: Posso descarregar o meu modelo e executá-lo localmente?**

**R:** Os modelos não podem ser descarregados e executados localmente.

**P: Os meus pedidos são acelerados?**

**R**: Ver [Quotas e Limites de Serviços de Fala.](speech-services-quotas-and-limits.md)

## <a name="next-steps"></a>Próximos passos

- [Resolução de problemas](troubleshooting.md)
- [Notas de versão](releasenotes.md)