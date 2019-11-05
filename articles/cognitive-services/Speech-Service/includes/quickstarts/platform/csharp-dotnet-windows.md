---
title: 'Guia de início rápido: SDK de fala para instalação de plataforma do .NET Framework (Windows)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para configurar sua plataforma para C# o em .NET Framework para Windows com o SDK dos serviços de fala.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: 2caa5124144146e2286b102bf6ded9c5d120f904
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502514"
---
Este guia mostra como instalar o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para .NET Framework (Windows).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Criar um projeto do Visual Studio e instalar o SDK de fala

Você precisará instalar o [pacote NuGet do SDK de fala](https://aka.ms/csspeech/nuget) para que possa referenciá-lo em seu código. Para fazer isso, você pode primeiro precisar criar um projeto **HelloWorld** . Se você já tiver um projeto com a carga de **trabalho de desenvolvimento do .net desktop** disponível, poderá usar esse projeto e ignorar [o uso do Gerenciador de pacotes NuGet para instalar o SDK de fala](#use-nuget-package-manager-to-install-the-speech-sdk).

### <a name="create-helloworld-project"></a>Criar projeto HelloWorld

1. Abra o Visual Studio 2019.

1. Na janela iniciar, selecione **criar um novo projeto**. 

1. Na janela **criar um novo projeto** , escolha **aplicativo de console (.NET Framework)** e, em seguida, selecione **Avançar**.

1. Na janela **configurar seu novo projeto** , digite *HelloWorld* no **nome do projeto**, escolha ou crie o caminho do diretório em **local**e, em seguida, selecione **criar**.

1. Na barra de menus do Visual Studio, selecione **ferramentas** > **obter ferramentas e recursos**, que abre instalador do Visual Studio e exibe a caixa de diálogo **modificando** .

1. Verifique se a carga de **trabalho do .net desktop Development** está disponível. Se a carga de trabalho não tiver sido instalada, marque a caixa de seleção ao lado dela e, em seguida, selecione **Modificar** para iniciar a instalação. Pode levar alguns minutos para baixar e instalar.

   Se a caixa de seleção ao lado de **desenvolvimento de área de trabalho .net** já estiver selecionada, selecione **fechar** para sair da caixa de diálogo.

   ![Ativar o desenvolvimento do ambiente de trabalho .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Feche Instalador do Visual Studio.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>Usar o Gerenciador de pacotes NuGet para instalar o SDK de fala

1. Na Gerenciador de Soluções, clique com o botão direito do mouse no projeto **HelloWorld** e selecione **gerenciar pacotes NuGet** para mostrar o Gerenciador de pacotes NuGet.

   ![Gerenciador de pacotes NuGet](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. No canto superior direito, localize a caixa suspensa **origem do pacote** e verifique se **`nuget.org`** está selecionada.

1. No canto superior esquerdo, selecione **procurar**.

1. Na caixa de pesquisa, digite *Microsoft. cognitivaservices. Speech* e selecione **Enter**.

1. Nos resultados da pesquisa, selecione o pacote **Microsoft. cognitivaservices. fala** e, em seguida, selecione **instalar** para instalar a versão estável mais recente.

   ![Instale o pacote NuGet Microsoft. Cognitivaservices. fala](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Aceite todos os contratos e licenças para iniciar a instalação.

   Depois que o pacote é instalado, uma confirmação é exibida na janela do **console do Gerenciador de pacotes** .

Agora você pode passar para [as próximas etapas](#next-steps) abaixo.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [windows](../quickstart-list.md)]
