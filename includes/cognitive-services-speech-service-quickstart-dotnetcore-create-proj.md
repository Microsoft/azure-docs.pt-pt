---
author: erhopf
ms.service: cognitive-services
ms.topic: include
origin.date: 12/12/2018
ms.date: 04/01/2019
ms.author: v-biyu
ms.openlocfilehash: 6e49db90aa9e9f933a190425afbafd15e0057fca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621140"
---
1. Inicie o Visual Studio 2017.

1. Certifique-se de que a carga de trabalho de **desenvolvimento para várias plataformas .NET**está disponível. Escolha **Ferramentas** > **Obter Ferramentas e Funcionalidades** na barra de menus do Visual Studio para abrir o instalador do Visual Studio. Se esta carga de trabalho já estiver ativada, feche a caixa de diálogo.

   ![Captura de ecrã do instalador do Visual Studio, com o separador Cargas de Trabalho realçado](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   Caso contrário, selecione a caixa junto a **desenvolvimento para várias plataformas .NET Core,** e selecione **Modificar** no canto inferior direito da caixa de diálogo. A instalação da nova funcionalidade demora alguns minutos.

1. Crie uma nova Aplicação de Consola de Visual C# .NET Core. Na caixa de diálogo **Novo Projeto**, no painel à esquerda, expanda **Instalado** > **Visual C#** > **.NET Core**. Em seguida, selecione **Aplicação de Consola (.NET Core)**. No nome do projeto, introduza *helloworld*.

   ![Captura de ecrã da caixa de diálogo Novo Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Criar Aplicação da Consola Visual C# (.NET Core)")

1. Instale e faça referência ao [pacote NuGet do SDK de Voz](https://aka.ms/csspeech/nuget). No Explorador de Soluções, clique com o botão direito do rato na solução e selecione **Gerir Pacotes NuGet para Solução**.

   ![Captura de ecrã do Explorador de Soluções, com Gerir Pacotes NuGet para Solução realçado](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Gerir Pacotes NuGet para Solução")

1. No canto superior direito, no campo **Origem do Pacote**, selecione **nuget.org**. Procure o pacote `Microsoft.CognitiveServices.Speech` e instale-o no projeto **helloworld**.

   ![Captura de ecrã da caixa de diálogo Gerir Pacotes para Solução](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Instalar pacote NuGet")

1. Aceite a licença apresentada para iniciar a instalação do pacote NuGet.

   ![Captura de ecrã da caixa de diálogo Aceitação da Licença](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Aceitar a Licença")

Depois de o pacote ser instalado, será apresentada uma mensagem de confirmação na consola do Gestor de Pacotes.
