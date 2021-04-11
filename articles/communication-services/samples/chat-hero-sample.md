---
title: Amostra de herói de chat de grupo
titleSuffix: An Azure Communication Services sample overview
description: Visão geral da amostra de herói de chat usando os Serviços de Comunicação Azure para permitir que os desenvolvedores aprendam mais sobre o funcionamento interno da amostra e aprendam a modificá-la.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5547e49cd7ea7f6c0cc97b5559564b64e0deda7c
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168846"
---
# <a name="get-started-with-the-group-chat-hero-sample"></a>Começa com a amostra de herói de chat de grupo

[!INCLUDE [Public Preview Notice](../includes/public-preview-include-chat.md)]

> [!IMPORTANT]
> [Esta amostra está disponível **no GitHub.**](https://github.com/Azure-Samples/communication-services-web-chat-hero)


A amostra de **chat hero do grupo de** serviços de comunicação Azure mostra como os serviços de comunicação Chat Web SDK podem ser usados para construir uma experiência de chamada de grupo.

Neste quickstart da amostra, vamos aprender como a amostra funciona antes de passarmos a amostra na sua máquina local. Em seguida, enviaremos a amostra para Azure usando os seus próprios recursos dos Serviços de Comunicação Azure.


## <a name="overview"></a>Descrição Geral

A amostra tem uma aplicação do lado do cliente e uma aplicação do lado do servidor. A **aplicação do lado do cliente** é uma aplicação web React/Redux que utiliza a estrutura fluente da Microsoft. Esta aplicação envia pedidos para uma **aplicação do ASP.NET** Core que ajuda a aplicação do lado do cliente a ligar-se ao Azure.

Aqui está o aspeto da amostra:

:::image type="content" source="./media/chat/landing-page.png" alt-text="Screenshot mostrando a página de aterragem da aplicação da amostra.":::

Quando carrega no botão "Iniciar um Chat", a aplicação web requer um token de acesso do utilizador a partir da aplicação do lado do servidor. Este token é então usado para ligar a app do cliente aos Serviços de Comunicação Azure. Assim que o sinal for recuperado, serão solicitados a especificar o seu nome e emoji que o representarão no chat.

:::image type="content" source="./media/chat/pre-chat.png" alt-text="Screenshot mostrando o ecrã pré-chat da aplicação.":::

Assim que configurar o nome do ecrã e o emoji, pode juntar-se à sessão de chat. Agora você verá a tela principal de chat onde vive a experiência de chat principal.

:::image type="content" source="./media/chat/main-app.png" alt-text="Screenshot mostrando o ecrã principal da aplicação da amostra.":::

Componentes do ecrã principal do chat:

- **Principal Área de Chat**: Esta é a experiência principal de chat onde os utilizadores podem enviar e receber mensagens. Para enviar mensagens, pode utilizar a área de entrada e premir a entrada (ou utilizar o botão enviar). As mensagens de chat recebidas são categorizadas pelo remetente com o nome e emoji corretos. Verá dois tipos de notificações na área do chat: 1) digitando notificações quando um utilizador está a escrever e 2) enviados e ler notificações para mensagens.
- **Cabeçalho**: É aqui que o utilizador verá o título do fio de chat e os controlos para toggling participante e definições barras laterais, e um botão de deixar para sair da sessão de chat.
- **Barra lateral**: É aqui que os participantes e a definição de informações são mostrados quando alternados utilizando os comandos no cabeçalho. A barra lateral dos participantes contém uma lista de participantes no chat e um link para convidar os participantes para a sessão de chat. A barra lateral das definições permite-lhe configurar o título do fio de conversação.

Abaixo encontrará mais informações sobre pré-requisitos e passos para configurar a amostra.

## <a name="prerequisites"></a>Pré-requisitos

- Crie uma conta Azure com uma subscrição ativa. Para mais detalhes, consulte [Criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js (8.11.2 ou superior)](https://nodejs.org/en/download/)
- [Estúdio Visual (2017 e acima)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (Certifique-se de instalar versão que corresponda à sua instância de estúdio visual, 32 vs 64 bit)
- Criar um recurso Azure Communication Services. Para mais detalhes, consulte [Criar um Recurso de Comunicação Azure](../quickstarts/create-communication-resource.md). Terá de gravar a **sua cadeia de ligação** de recursos para este arranque rápido.

## <a name="locally-deploying-the-service--client-app"></a>Implantação local do serviço & app de clientes

A amostra de chat roscada única é essencialmente duas "aplicações" uma aplicação de cliente e servidor.

Abra o Visual Studio no chat.csproj e corra no modo Debug, isto iniciará o serviço frontal de chat. Quando a aplicação do servidor é visitada a partir do navegador, irá redirecionar o tráfego para o serviço frontal de chat implantado localmente.

Pode testar a amostra localmente abrindo várias sessões de navegador com o URL do seu chat para simular um chat multiutilizador.

## <a name="before-running-the-sample-for-the-first-time"></a>Antes de executar a amostra pela primeira vez

1. Abra uma instância de PowerShell, Windows Terminal, Command Prompt ou equivalente e navegue para o diretório ao qual pretende clonar a amostra.
2. `git clone https://github.com/Azure-Samples/communication-services-web-chat-hero.git`
3. Pegue o `Connection String` do portal Azure. Para obter mais informações sobre as cadeias de ligação, consulte [Criar um Azure Communication Resources](../quickstarts/create-communication-resource.md)
4. Assim que tiver `Connection String` a , Adicione o fio de ligação ao **Chat/appsettings.jsno** ficheiro encontrado por baixo da pasta Chat. Insira a sua cadeia de ligação na variável: `ResourceConnectionString` .

### <a name="local-run"></a>Execução local

1. Vá à pasta Chat e abra a `Chat.csproj` solução no Visual Studio
2. Executar o projeto. O navegador será aberto na localidade:5000.

#### <a name="troubleshooting"></a>Resolução de problemas

- A solução não constrói, lança erros durante a instalação/construção do NPM

   Limpar/reconstruir a solução C#

## <a name="publish-the-sample-to-azure"></a>Publique a amostra para Azure

1. Clique no `Chat` projeto e selecione Publicar.
2. Crie um novo perfil de publicação e selecione a sua subscrição Azure.
3. Antes de publicar, adicione a sua cadeia de ligação com `Edit App Service Settings` , e preencha `ResourceConnectionString` como chave e forneça a sua cadeia de ligação (copiada a partir de appsettings.js) como o valor.

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma assinatura de Serviços de Comunicação, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados. Saiba mais sobre [a limpeza de recursos.](../quickstarts/create-communication-resource.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

>[!div class="nextstepaction"]
>[Faça o download da amostra do GitHub](https://github.com/Azure-Samples/communication-services-web-chat-hero)

Para obter mais informações, veja os seguintes artigos:

- Saiba mais [sobre conceitos de chat](../concepts/chat/concepts.md)
- Familiarize-se com o nosso [Chat SDK](../concepts/chat/sdk-features.md)
- Reveja a amostra da [App Contoso Med](https://github.com/Azure-Samples/communication-services-contoso-med-app)

## <a name="additional-reading"></a>Leitura adicional

- [Amostras](./overview.md) - Encontre mais amostras e exemplos na página geral das nossas amostras.
- [Redux](https://redux.js.org/) - Gestão do Estado do lado do cliente
- [FluentUI](https://aka.ms/fluent-ui) - Biblioteca de UI alimentada pela Microsoft
- [Reagir](https://reactjs.org/) - Biblioteca para construção de interfaces de utilizador
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) - Enquadramento para a construção de aplicações web
