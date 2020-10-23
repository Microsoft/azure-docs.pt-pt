---
title: Exemplo de destaque das chamadas em grupo
titleSuffix: An Azure Communication Services sample overview
description: Visão geral da amostra de herói chamando a amostra de herói usando os Serviços de Comunicação Azure para permitir que os desenvolvedores aprendam mais sobre o funcionamento interno da amostra.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e6cbf840fa27755fa43eb2a81e752415562ce41f
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461146"
---
# <a name="get-started-with-the-group-calling-hero-sample"></a>Começa com o grupo a chamar amostra de herói

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> Add links to our Hero Sample repo when the sample is publicly available.
---->

> [!IMPORTANT]
> [Esta amostra está disponível no GitHub.](https://github.com/Azure-Samples/communication-services-web-calling-hero)

O Grupo de Serviços de Comunicação Azure **Call Hero Sample** demonstra como a biblioteca de clientes web dos Serviços de Comunicação pode ser usada para construir uma experiência de chamada de grupo.

Neste quickstart da amostra, vamos aprender como a amostra funciona antes de passarmos a amostra na sua máquina local. Em seguida, enviaremos a amostra para Azure usando os seus próprios recursos dos Serviços de Comunicação Azure.

## <a name="overview"></a>Descrição geral

A amostra tem uma aplicação do lado do cliente e uma aplicação do lado do servidor. A **aplicação do lado do cliente** é uma aplicação web React/Redux que utiliza a estrutura fluente da Microsoft. Esta aplicação envia pedidos para uma **aplicação do ASP.NET** Core que ajuda a aplicação do lado do cliente a ligar-se ao Azure. 

Aqui está o aspeto da amostra:

:::image type="content" source="./media/calling/landing-page.png" alt-text="Screenshot mostrando a página de aterragem da aplicação da amostra.":::

Quando carrega no botão "Iniciar uma chamada", a aplicação web requer um token de acesso do utilizador a partir da aplicação do lado do servidor. Este token é então usado para ligar a app do cliente aos Serviços de Comunicação Azure. Assim que o token for recuperado, ser-lhe-á solicitado que especifique a câmara e o microfone que pretende utilizar. Poderá desativar/ativar os seus dispositivos com controlos de alternância:

:::image type="content" source="./media/calling/pre-call.png" alt-text="Screenshot mostrando a página de aterragem da aplicação da amostra.":::

Assim que configurar o nome e os dispositivos do ecrã, pode juntar-se à sessão de chamadas. Agora você verá a tela principal de chamada onde vive a experiência principal de chamada.

:::image type="content" source="./media/calling/main-app.png" alt-text="Screenshot mostrando a página de aterragem da aplicação da amostra.":::

Componentes do ecrã principal de chamada:

1. **Media Gallery**: O palco principal onde os participantes são mostrados. Se um participante tiver a sua câmara ativada, o seu feed de vídeo é mostrado aqui. Cada participante tem um azulejo individual que mostra o seu nome de exibição e fluxo de vídeo (quando há um)
2. **Cabeçalho**: É aqui que se encontram os controlos de chamada primário para alternar as definições e a barra lateral do participante, ligar o vídeo e misturar,partilhar o ecrã e deixar a chamada.
3. **Barra lateral**: É aqui que são mostradas informações sobre os participantes e as definições quando alternadas utilizando os controlos do cabeçalho. O componente pode ser descartado utilizando o 'X' no canto superior direito. A barra lateral dos participantes mostrará uma lista de participantes e um link para convidar mais utilizadores a conversar. A barra lateral permite configurar as definições do microfone e da câmara.

Abaixo encontrará mais informações sobre pré-requisitos e passos para configurar a amostra.

## <a name="prerequisites"></a>Pré-requisitos

- Crie uma conta Azure com uma subscrição ativa. Para mais detalhes, consulte [Criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js (12.18.4 ou mais)](https://nodejs.org/en/download/)
- [Estúdio Visual (2019 e acima)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (Certifique-se de instalar versão que corresponda à sua instância de estúdio visual, 32 vs 64 bit)
- Criar um recurso Azure Communication Services. Para mais detalhes, consulte [Criar um Recurso de Comunicação Azure](../quickstarts/create-communication-resource.md). Terá de gravar a **sua cadeia de ligação** de recursos para este arranque rápido.

## <a name="locally-deploy-the-service--client-applications"></a>Implementar localmente o serviço & aplicações de clientes

A amostra de chamada de grupo é essencialmente duas aplicações: o ClientApp e a app Service.NET.

Quando queremos implantar localmente, precisamos de iniciar ambas as aplicações. Quando a aplicação do servidor é visitada a partir do navegador, utilizará o ClientApp implementado localmente para a experiência do utilizador.

Pode testar a amostra localmente abrindo várias sessões de navegador com o URL da sua chamada para simular uma chamada multiutilizador.

## <a name="before-running-the-sample-for-the-first-time"></a>Antes de executar a amostra pela primeira vez

1. Abra uma instância de PowerShell, Windows Terminal, Command Prompt ou equivalente e navegue para o diretório ao qual pretende clonar a amostra.
2. `git clone https://github.com/Azure/Communication.git`
3. Pegue o `Connection String` do portal Azure. Para obter mais informações sobre as cadeias de ligação, consulte [Criar um Azure Communication Resources](../quickstarts/create-communication-resource.md)
4. Assim que tiver a `Connection String` ficha , adicione o fio de ligação à **chamada/appsetting.jsno** ficheiro encontrado na pasta Serviço .NET. Insira a sua cadeia de ligação na variável: `ResourceConnectionString` .

### <a name="local-run"></a>Corrida Local

1. Vá à pasta Call e abra `Calling.csproj` solução no Visual Studio
2. Executar `Calling` projeto. O navegador será aberto na localidade:5001

#### <a name="troubleshooting"></a>Resolução de problemas

- A solução não constrói; lança erros durante a instalação/construção de NPM.

   Tente limpar/reconstruir os projetos.

## <a name="publish-the-sample-to-azure"></a>Publique a amostra para Azure

1. Clique no `Calling` projeto e selecione Publicar.
2. Crie um novo perfil de publicação e selecione a sua subscrição Azure.
3. Antes de publicar, adicione a sua cadeia de ligação com `Edit App Service Settings` , e preencha `ResourceConnectionString` como chave e forneça a sua cadeia de ligação (copiada a partir de appsettings.js) como o valor.

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma assinatura de Serviços de Comunicação, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados. Saiba mais sobre [a limpeza de recursos.](../quickstarts/create-communication-resource.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

>[!div class="nextstepaction"] 
>[Faça o download da amostra do GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero)

Para obter mais informações, veja os seguintes artigos:

- Familiarize-se com [a utilização da biblioteca de clientes de chamadas](../quickstarts/voice-video-calling/calling-client-samples.md)
- Saiba mais sobre [como funciona a chamada](../concepts/voice-video-calling/about-call-types.md)

## <a name="additional-reading"></a>Leitura adicional

- [Azure Communication GitHub](https://github.com/Azure/communication) - Encontre mais exemplos e informações na página oficial do GitHub
- [Redux](https://redux.js.org/) - Gestão do Estado do lado do cliente
- [FluentUI](https://aka.ms/fluent-ui) - Biblioteca de UI alimentada pela Microsoft
- [Reagir](https://reactjs.org/) - Biblioteca para construção de interfaces de utilizador
- [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) - Enquadramento para a construção de aplicações web
