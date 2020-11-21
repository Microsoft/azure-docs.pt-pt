---
title: Testar a sua aplicação de Comandos Personalizados
titleSuffix: Azure Cognitive Services
description: Neste artigo, aprende-se diferentes abordagens para testar uma aplicação de comandos personalizados.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c0bd21f55fee4d8487826deae23093ede293c8c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021818"
---
# <a name="test-your-custom-commands-application"></a>Teste a sua Aplicação de Comandos Personalizados

Neste artigo, aprende-se diferentes abordagens para testar uma aplicação de comandos personalizados.

## <a name="test-in-the-portal"></a>Teste no portal

O teste no portal é a forma mais simples e rápida de verificar se a aplicação de comando personalizada funciona como esperado. Depois de a aplicação ser treinada com sucesso, clique `Test` no botão para começar a testar.

> [!div class="mx-imgBorder"]
> ![Teste no portal](media/custom-commands/create-basic-test-chat.png)

## <a name="test-with-windows-voice-assistant-client"></a>Teste com cliente assistente de voz do Windows

O Cliente Assistente de Voz do Windows é uma aplicação da Fundação de Apresentação do Windows (WPF) em C# que facilita o teste de interações com o seu bot antes de criar uma aplicação personalizada para o cliente.

A ferramenta pode aceitar um ID de aplicação de comando personalizado. Permite-lhe testar o cenário de conclusão da tarefa ou de comando e controlo alojado no serviço Comandos Personalizados.

Para configurar o cliente, checkout [Cliente Assistente de Voz do Windows](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf).

> [!div class="mx-imgBorder"]
> ![WVAC Criar perfil](media/custom-commands/conversation.png)

## <a name="test-with-speech-sdk-enabled-client-applications"></a>Teste com aplicações de clientes ativadas por SDK de discurso 
O kit de desenvolvimento de software speech (SDK) expõe muitas das capacidades do serviço Speech, o que lhe permite desenvolver aplicações ativadas pela fala. Também está disponível em muitas linguagens de programação e em todas as plataformas.

Para configurar uma aplicação de cliente Universal Windows Platform (UWP) com a Speech SDK e integrá-la com a sua aplicação de comando personalizada:  
- [Como: Integrar-se com uma aplicação de cliente usando a Speech SDK](./how-to-custom-commands-setup-speech-sdk.md)
- [Como: Enviar atividade para a aplicação do cliente](./how-to-custom-commands-send-activity-to-client.md)
- [Como: Configurar pontos finais web](./how-to-custom-commands-setup-web-endpoints.md)

Para outras linguagens e plataformas de programação:
- [Linguagems de programação SDK de fala, plataformas, capacidades de cenário](./speech-sdk.md)
- [Códigos de amostra de assistente de voz](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Ver amostras no GitHub](https://aka.ms/speech/cc-samples)