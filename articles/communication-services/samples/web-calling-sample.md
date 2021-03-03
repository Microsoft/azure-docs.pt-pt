---
title: Serviços de Comunicação Azure - Amostra de chamada web
titleSuffix: An Azure Communication Services sample
description: Conheça a amostra de chamada web dos Serviços de Comunicação
author: chriswhilar
manager: mariusu-msft
services: azure-communication-services
ms.author: mariusu
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5752f20cb433a417a684076338214cd2ea80bc8b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658102"
---
# <a name="get-started-with-the-web-calling-sample"></a>Começar com a amostra de chamada web

A amostra de chamada web é uma aplicação web que serve como uma caminhada passo a passo através das várias capacidades fornecidas pela web services web call client library.

Esta amostra foi construída para desenvolvedores e torna muito fácil para você começar com serviços de comunicação. A sua interface de utilizador está dividida em várias secções, cada uma com um botão "Show code" que lhe permite copiar código diretamente do seu navegador para a sua própria aplicação de Serviços de Comunicação.

## <a name="get-started-with-the-web-calling-sample"></a>Começar com a amostra de chamada web

> [!IMPORTANT]
> [Esta amostra está disponível em Github.](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

Siga o /Project/readme.md para configurar o projeto e executá-lo localmente na sua máquina.
Assim que a [amostra de chamada](https://github.com/Azure-Samples/communication-services-web-calling-tutorial) web estiver a funcionar na sua máquina, verá a seguinte página de aterragem:

:::image type="content" source="./media/web-calling-tutorial-page-1.png" alt-text="Web chamando tutorial 1" lightbox="./media/web-calling-tutorial-page-1.png":::

:::image type="content" source="./media/web-calling-tutorial-page-2.png" alt-text="Web chamando tutorial 2" lightbox="./media/web-calling-tutorial-page-2.png":::

## <a name="user-provisioning-and-sdk-initialization"></a>Provisão de utilizadores e inicialização de SDK

Clique no "Utilizador de Provisionamento e inicialize o SDK" para rubricar o seu SDK utilizando um token provisionado pelo serviço de fornecimento de token backend. Este serviço de backend está em `/project/webpack.config.js` .

Clique no botão "Mostrar código" para ver o código de amostra que pode utilizar na sua própria solução.

Deve ver o seguinte assim que o seu SDK for inicializado:

:::image type="content" source="./media/user-provisioning.png" alt-text="Aprovisionamento de utilizadores" lightbox="./media/user-provisioning.png":::

Está agora pronto para começar a fazer chamadas usando o seu recurso de Serviços de Comunicação!

## <a name="placing-and-receiving-calls"></a>Colocação e receção de chamadas

A web dos Serviços de Comunicação SDK permite **1:1,** **1:N,** e chamada em **grupo.**

Para chamadas de saída 1:1 ou 1:N, pode especificar várias identidades dos utilizadores dos Serviços de Comunicação para ligar utilizando valores separados por vírgula. Também pode especificar os números de telefone tradicionais (PSTN) para ligar utilizando valores separados por vírgula.

Ao ligar para os números de telefone da PSTN, especifique o seu ID de chamada alternativo. Clique no botão "Fazer chamada" para fazer uma chamada de saída:

:::image type="content" source="./media/place-a-call.png" alt-text="Fazer uma chamada" lightbox="./media/place-a-call.png":::

Para se juntar a uma chamada de grupo, insira o GUID que identifica a chamada e clique no botão "Junte-se ao grupo":

:::image type="content" source="./media/join-a-group-call.png" alt-text="Junte-se a uma chamada de grupo" lightbox="./media/join-a-group-call.png":::

Clique no botão "Mostrar código" para ver o código de amostra para fazer chamadas, receber chamadas e juntar chamadas de grupo.

Uma chamada ativa é assim:

:::image type="content" source="./media/group-call.png" alt-text="Chamada de grupo" lightbox="./media/group-call.png":::

Esta amostra também fornece fragmentos de código para as seguintes capacidades:

  - Clicando no ícone de vídeo para ligar/desligar a sua câmara de vídeo
  - Clicando no ícone do microfone para ligar/desligar o microfone
  - Clicando no ícone de reprodução para segurar/destenção da chamada
  - Clicando no ícone do ecrã para iniciar/parar partilhar o seu ecrã
  - Clicando no ícone da pessoa para adicionar um participante à chamada
  - Clicando em "Remover o participante" na lista de participantes para remover um participante específico da chamada


## <a name="next-steps"></a>Passos seguintes

>[!div class="nextstepaction"]
>[Faça o download da amostra do GitHub](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

Para obter mais informações, veja os seguintes artigos:

- Familiarize-se com [a utilização da biblioteca de clientes de chamadas](../quickstarts/voice-video-calling/calling-client-samples.md)
- Saiba mais sobre [como funciona a chamada](../concepts/voice-video-calling/about-call-types.md)
- Rever os [docs de referência da API](/javascript/api/azure-communication-services/@azure/communication-calling/?view=azure-communication-services-js)
- Reveja a amostra da [App Contoso Med](https://github.com/Azure-Samples/communication-services-contoso-med-app)

## <a name="additional-reading"></a>Leitura adicional

- [Azure Communication GitHub](https://github.com/Azure/communication) - Encontre mais exemplos e informações na página oficial do GitHub
- [Redux](https://redux.js.org/) - Gestão do Estado do lado do cliente
- [FluentUI](https://aka.ms/fluent-ui) - Biblioteca de UI alimentada pela Microsoft
- [Reagir](https://reactjs.org/) - Biblioteca para construção de interfaces de utilizador
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) - Enquadramento para a construção de aplicações web
