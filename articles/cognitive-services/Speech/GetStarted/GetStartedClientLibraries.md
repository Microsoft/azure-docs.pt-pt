---
title: Introdução à API de reconhecimento de Fala do Bing usando bibliotecas de cliente | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Use as bibliotecas de cliente Fala do Bing no Microsoft cognitiva Services para desenvolver aplicativos que convertidam áudio falado em texto.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 8ea0c9e7104b29456749c7f1af2a671b32a8fde2
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966851"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>Introdução às bibliotecas de cliente do serviço Fala do Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Além de fazer solicitações HTTP diretas por meio de uma API REST, o serviço de Fala do Bing fornece aos desenvolvedores bibliotecas de cliente de fala em diferentes idiomas. As bibliotecas de cliente de fala:

- Dê suporte a recursos mais avançados no reconhecimento de fala, como resultados intermediários em tempo real, fluxo de áudio longo (até 10 minutos) e reconhecimento contínuo.
- Forneça uma API simples e idiomática no idioma de sua preferência.
- Ocultar detalhes de comunicação de baixo nível.

Atualmente, as seguintes Fala do Bing bibliotecas de cliente estão disponíveis:

- [C#biblioteca de desktops](GetStartedCSharpDesktop.md)
- [C#biblioteca de serviços](GetStartedCSharpServiceLibrary.md)
- [Biblioteca JavaScript](GetStartedJSWebsockets.md)
- [Biblioteca Java para Android](GetStartedJavaAndroid.md)
- [Biblioteca Objective-C para iOS](Get-Started-ObjectiveC-iOS.md)

## <a name="additional-resources"></a>Recursos adicionais

- A página de [exemplos](../samples.md) fornece amostras completas para usar as bibliotecas de cliente de fala.
- Se precisar de uma biblioteca de cliente que ainda não tenha suporte, você poderá criar seu próprio SDK. Implemente o [protocolo de WebSocket de fala](../API-Reference-REST/websocketprotocol.md) na plataforma e use a linguagem de sua escolha.

## <a name="license"></a>Licença

Todos os SDKs e exemplos de serviços cognitivas são licenciados com a licença MIT. Para obter mais informações, consulte [License](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
