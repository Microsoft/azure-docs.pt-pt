---
title: Perguntas mais frequentes sobre a conversão de voz de linha direta
titleSuffix: Azure Cognitive Services
description: Obtenha respostas às perguntas mais populares sobre assistentes de virtual de voz em primeiro lugar usando o canal direto de linha de voz.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.openlocfilehash: 16e4bcbb1514cfd5bbddc22b663d636292095231
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025899"
---
# <a name="voice-first-virtual-assistants-preview-frequently-asked-questions"></a>Voz em primeiro lugar virtual assistentes pré-visualização: Perguntas mais frequentes

Se não conseguir encontrar respostas para suas perguntas neste documento, confira [outras opções de suporte](support.md).

## <a name="general"></a>Geral

**P: O que é direta de linha de fala?**

**R:** O `SpeechBotConnector` desde o SDK de voz fornece bidirecional, comunicação assíncrona com bots que estão ligados para o canal de voz de linha direta no Bot Framework. Este canal fornece um acesso coordenado a conversão de voz em texto e voz dos serviços de voz do Azure que permitem que os bots tornar-se totalmente voice no, as experiências de conversação de voz. Com o suporte para palavras de reativação e a verificação do Word de reativação, esta solução torna possível criar altamente personalizáveis assistentes de virtual de voz em primeiro lugar para sua marca ou o produto.

**P: Como posso começar a utilizar?**

**R:** A melhor forma para começar a criar um Assistente de virtual de voz em primeiro lugar é começar com [criação de um bot de Bot Framework básico](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0). Em seguida, ligar o seu bot para o [canal direto de linha de voz](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech.md).

## <a name="debugging"></a>Depurar

**P: Recebo um erro 401 quando se liga e nada funciona. Eu sei a que minha chave de subscrição de voz é válido. O que está acontecendo?**

**R:** Em pré-visualização, o direta de linha de voz tem limitações muito específicas sobre a subscrição utilizada. Certifique-se de que está a utilizar o **voz** recursos (Microsoft.CognitiveServicesSpeechServices, "Fala") e *não* o **serviços cognitivos** (de recursos Microsoft.CognitiveServicesAllInOne, "Todos os serviços cognitivos"). Além disso, tenha em atenção que apenas os **westus2** região é atualmente suportada.

![Corrija a subscrição para a conversão de voz de linha direta](media/voice-first-virtual-assistants/faq-supported-subscription.png "exemplo de uma subscrição de voz compatível")

**P: Posso obter o texto de reconhecimento de voz de linha direta, mas posso ver um erro de "1011" e nada do meu bot. Porquê?**

**R:** Este erro indica um problema de comunicação entre o bot e direto de linha de voz. Certifique-se de que [ligado o canal direto de linha de voz](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech.md), [foi adicionado suporte de protocolo de transmissão em fluxo](https://aka.ms/botframework/addstreamingprotocolsupport) para o seu bot (com suporte de Web Socket relacionado) e, em seguida, verifique se seu bot está a responder à entrada pedidos do canal.

**P: Isso não funciona e/ou estou recebendo um erro diferente ao utilizar um SpeechBotConnector e não está claro o que devemos fazer. O que *deve* devo fazer?**

**R:** Com base no ficheiro de registo fornece substancialmente mais detalhes e pode ajudar a acelerar os pedidos de suporte. Para ativar esta opção, veja [como utilizar o registo de ficheiros](how-to-use-logging.md).

## <a name="next-steps"></a>Passos Seguintes

* [Resolução de problemas](troubleshooting.md)
* [Notas de versão](releasenotes.md)
