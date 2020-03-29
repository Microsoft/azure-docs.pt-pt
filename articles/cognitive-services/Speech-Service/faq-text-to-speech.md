---
title: Texto para Discurso frequentemente feito perguntas
titleSuffix: Azure Cognitive Services
description: Obtenha respostas às perguntas frequentes sobre o serviço Texto à Fala.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 19b8be83a3678164197ec0650b07091e941a04d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110507"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Texto para Discurso frequentemente feito perguntas

Se não encontrar respostas para as suas perguntas neste FAQ, confira [outras opções](support.md)de suporte .

## <a name="general"></a>Geral

**P: Qual é a diferença entre um modelo de voz padrão e um modelo de voz personalizado?**

**R**: O modelo de voz padrão (também chamado de fonte de _voz)_ foi treinado utilizando dados da Microsoft e já está implantado na nuvem. Pode utilizar um modelo de voz personalizado para adaptar um modelo médio e transferir o timbre e expressão do estilo de voz do altifalante ou treinar um modelo completo e novo com base nos dados de treino preparados pelo utilizador. Hoje, cada vez mais clientes querem uma voz única e marcada para os seus bots. A plataforma personalizada de construção de voz é a escolha certa para essa opção.

**P: Por onde começo se quero usar um modelo de voz padrão?**

**R**: Mais de 80 modelos de voz padrão em mais de 45 idiomas estão disponíveis através de pedidos HTTP. Primeiro, obtenha uma [chave de assinatura.](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started) Para fazer chamadas DE REPOUSO para os modelos de voz pré-implantados, consulte a [API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

**P: Se eu quiser usar um modelo de voz personalizado, a API é a mesma que é usada para vozes padrão?**

**R:** Quando um modelo de voz personalizado é criado e implantado, obtém-se um ponto final único para o seu modelo. Para utilizar a voz para falar nas suas apps, deve especificar o ponto final nos seus pedidos HTTP. A mesma funcionalidade disponível na API REST para o serviço De Texto para Discurso está disponível para o seu ponto final personalizado. Aprenda a [criar e usar o seu ponto final personalizado.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-voice-endpoint)

**P: Preciso preparar os dados de treino para criar modelos de voz personalizados por conta própria?**

**R:** Sim, deve preparar os dados de treino para um modelo de voz personalizado.

É necessária uma recolha de dados de fala para criar um modelo de voz personalizado. Esta coleção consiste num conjunto de ficheiros áudio de gravações de fala e num ficheiro de texto da transcrição de cada ficheiro áudio. O resultado da sua voz digital depende fortemente da qualidade dos seus dados de treino. Para produzir uma boa voz de texto para voz, é importante que as gravações sejam feitas numa sala tranquila com um microfone de alta qualidade e de pé. Um volume consistente, uma taxa de fala e um tom de fala, e até mesmo a consistência nos maneirismos expressivos da fala são essenciais para construir uma grande voz digital. Recomendamos vivamente gravar as vozes num estúdio de gravação.

Atualmente, não fornecemos suporte de gravação on-line ou temos quaisquer recomendações de estúdio de gravação. Para a exigência do formato, consulte [como preparar gravações e transcrições](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-create-voice).

**P: Que scripts devo usar para gravar os dados da fala para treino de voz personalizado?**

**R:** Não limitamos os scripts para gravação de voz. Podes usar os teus próprios guiões para gravar o discurso. Apenas certifique-se de que tem cobertura fonética suficiente nos dados da sua fala. Para treinar uma voz personalizada, pode começar com um pequeno volume de dados da fala, que podem ser 50 frases diferentes (cerca de 3-5 minutos de discurso). Quanto mais dados forneceres, mais natural será a tua voz. Pode começar a treinar uma fonte de voz completa quando fornecer gravações de mais de 2.000 frases (cerca de 3-4 horas de discurso). Para obter uma voz completa e de alta qualidade, é preciso preparar gravações de mais de 6.000 frases (cerca de 8-10 horas de discurso).

Fornecemos serviços adicionais para ajudá-lo a preparar scripts para gravação. Contacte o [suporte ao cliente custom Voice](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) para obter informações.

**P: E se eu precisar de uma moeda mais alta do que o valor padrão ou o que é oferecido no portal?**

**R:** Pode aumentar o seu modelo em incrementos de 20 pedidos simultâneos. Contacte o [suporte ao cliente custom Voice](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) para obter informações sobre escalas mais elevadas.

**P: Posso baixar o meu modelo e executá-lo localmente?**

**R:** Os modelos não podem ser descarregados e executados localmente.

**P: Os meus pedidos estão acelerados?**

**R:** O REST API limita os pedidos a 25 por 5 segundos. Os detalhes podem ser encontrados nas nossas páginas para [Texto à Fala](text-to-speech.md).

## <a name="next-steps"></a>Passos seguintes

- [Resolução de problemas](troubleshooting.md)
- [Notas de versão](releasenotes.md)
