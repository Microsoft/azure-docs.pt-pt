---
title: Perguntas frequentes sobre o serviço de Conversão de Texto em Fala no Azure
titleSuffix: Azure Cognitive Services
description: Obtenha respostas para as perguntas frequentes sobre o serviço de Conversão de Texto em Fala.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 0ec9b9729ea93a0685179559bf58392944f37ba6
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075823"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Conversão de Texto em Fala perguntas frequentes

Se você não encontrar respostas para suas perguntas nestas perguntas frequentes, confira [outras opções de suporte](support.md).

## <a name="general"></a>Geral

**P: Qual é a diferença entre um modelo de voz padrão e um modelo de voz personalizado?**

**R**: o modelo de voz padrão (também chamado de _fonte de voz_) foi treinado com o uso de dados de propriedade da Microsoft e já está implantado na nuvem. Você pode usar um modelo de voz personalizado para adaptar um modelo médio e transferir o timbre e a expressão do estilo de voz do orador ou treinar um novo modelo completo com base nos dados de treinamento preparados pelo usuário. Hoje em dia, cada vez mais clientes querem uma voz com marca única para seus bots. A plataforma de criação de voz personalizada é a escolha certa para essa opção.

**P: onde devo iniciar se quiser usar um modelo de voz padrão?**

**R**: mais de 80 modelos de voz padrão em mais de 45 idiomas estão disponíveis por meio de solicitações HTTP. Primeiro, obtenha uma [chave de assinatura](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started). Para fazer chamadas REST para os modelos de voz preimplantados, consulte a [API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

**P: se eu quiser usar um modelo de voz personalizado, a API será a mesma usada para as vozes padrão?**

**R**: quando um modelo de voz personalizado é criado e implantado, você obtém um ponto de extremidade exclusivo para seu modelo. Para usar a voz para falar em seus aplicativos, você deve especificar o ponto de extremidade em suas solicitações HTTP. A mesma funcionalidade que está disponível na API REST para o serviço Conversão de Texto em Fala está disponível para seu ponto de extremidade personalizado. Saiba como [criar e usar seu ponto de extremidade personalizado](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-voice-endpoint).

**P: preciso preparar os dados de treinamento para criar modelos de voz personalizados por conta própria?**

**R**: Sim, você deve preparar os dados de treinamento por conta própria para um modelo de voz personalizado.

Uma coleção de dados de fala é necessária para criar um modelo de voz personalizado. Essa coleção consiste em um conjunto de arquivos de áudio de gravações de fala e um arquivo de texto da transcrição de cada arquivo de áudio. O resultado da sua voz digital depende muito da qualidade dos seus dados de treinamento. Para produzir uma boa voz de conversão de texto em fala, é importante que as gravações sejam feitas em uma sala silenciosa com um microfone de alta qualidade e em pé. Um volume consistente, uma taxa de fala e um tom de fala, e até mesmo a consistência em mannerisms expressivas de fala são essenciais para a criação de uma ótima voz digital. É altamente recomendável gravar as vozes em um estúdio de gravação.

No momento, não fornecemos suporte à gravação online nem têm nenhuma recomendação do Recording Studio. Para o requisito de formato, consulte [como preparar gravações e transcrições](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-create-voice).

**P: quais scripts devo usar para registrar os dados de fala para treinamento de voz personalizado?**

**R**: não limitamos os scripts para gravação de voz. Você pode usar seus próprios scripts para registrar a fala. Apenas verifique se você tem cobertura fonética suficiente em seus dados de fala. Para treinar uma voz personalizada, você pode começar com um pequeno volume de dados de fala, que pode ser 50 sentenças diferentes (cerca de 3-5 minutos de fala). Quanto mais dados você fornecer, mais natural sua voz será. Você pode começar a treinar uma fonte de voz completa ao fornecer gravações de mais de 2.000 sentenças (cerca de 3-4 horas de fala). Para obter uma voz completa de alta qualidade, você precisa preparar gravações de mais de 6.000 sentenças (cerca de 8-10 horas de fala).

Fornecemos serviços adicionais para ajudá-lo a preparar scripts para gravação. Contate o atendimento [ao cliente de voz personalizado](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) para obter consultas.

**P: e se eu precisar de uma simultaneidade maior do que o valor padrão ou o que é oferecido no portal?**

**R**: você pode escalar verticalmente seu modelo em incrementos de 20 solicitações simultâneas. Contate o [suporte ao cliente de voz personalizada](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) para obter consultas sobre o dimensionamento mais alto.

**P: posso baixar meu modelo e executá-lo localmente?**

**R**: os modelos não podem ser baixados e executados localmente.

**P: minhas solicitações estão limitadas?**

**R**: a API REST limita as solicitações a 25 por 5 segundos. Os detalhes podem ser encontrados em nossas páginas para [conversão de texto em fala](text-to-speech.md).

## <a name="next-steps"></a>Passos seguintes

- [Resolução de problemas](troubleshooting.md)
- [Notas de versão](releasenotes.md)
