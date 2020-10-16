---
title: 'Quickstart: Speech SDK for .NET Framework (Windows) platform setup - Speech service'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para configurar a sua plataforma para C# em .NET Framework for Windows com o serviço de voz SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-dotnet
ms.openlocfilehash: f63c97fd23d56ff508de8d5953eefac6b15ae1aa
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097070"
---
Este guia mostra como instalar o [SDK de voz](~/articles/cognitive-services/speech-service/speech-sdk.md) para .NET Framework (Windows). Se só quer que o nome do pacote seja iniciado por conta própria, corra `Install-Package Microsoft.CognitiveServices.Speech` na consola NuGet.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Crie um projeto de Estúdio Visual e instale o Speech SDK

Terá de instalar o [pacote Speech SDK NuGet](https://aka.ms/csspeech/nuget) para que possa fazê-lo referência no seu código. Para isso, pode primeiro ter de criar um projeto **helloworld.** Se já tiver um projeto com a carga de trabalho de **desenvolvimento do ambiente de trabalho .NET** disponível, pode utilizar esse projeto e saltar para utilizar o [NuGet Package Manager para instalar o Speech SDK](#use-nuget-package-manager-to-install-the-speech-sdk).

### <a name="create-helloworld-project"></a>Criar projeto Helloworld

1. Open Visual Studio 2019.

1. Na janela Iniciar, **selecione Criar um novo projeto.** 

1. Na **janela criar uma nova** janela de projeto, escolha a App consola **(.NET Framework)** e, em seguida, selecione **Next**.

1. Na **configuração** da sua nova janela do projeto, insira *helloworld* em **nome do Projeto,** escolha ou crie o caminho do diretório em **Localização,** e, em seguida, selecione **Criar**.

1. A partir da barra de menus Visual Studio, selecione **Tools**  >  **Get Tools and Features**, que abre o Instalador de Estúdio Visual e exibe a caixa de diálogo **modificante.**

1. Verifique se está disponível a carga de trabalho **de desenvolvimento do ambiente de trabalho .NET.** Se a carga de trabalho não tiver sido instalada, selecione a caixa de verificação ao lado e, em seguida, selecione **Modificar** para iniciar a instalação. Pode levar alguns minutos para descarregar e instalar.

   Se a caixa de verificação ao lado **do desenvolvimento do ambiente de trabalho .NET** já estiver selecionada, selecione **Close** to exit the dialog box.

   ![Ativar o desenvolvimento do ambiente de trabalho .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Feche o instalador do estúdio visual.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>Use o Gestor de Pacotes NuGet para instalar o SDK de discurso

1. No Solution Explorer, clique com o botão direito no projeto **Helloworld** e, em seguida, **selecione Gerir Pacotes NuGet** para mostrar o Gestor de Pacotes NuGet.

   ![Gestor de Pacotes NuGet](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. No canto superior direito, encontre a caixa de entrega **de Fonte** de Pacote e certifique-se de que **`nuget.org`** está selecionada.

1. No canto superior esquerdo, **selecione Procurar**.

1. Na caixa de pesquisa, digite *Microsoft.CognitiveServices.Speech* e selecione **Enter**.

1. A partir dos resultados da pesquisa, selecione o pacote **Microsoft.CognitiveServices.Speech** e, em seguida, **selecione Instalar** para instalar a versão mais recente estável.

   ![Instale o pacote Microsoft.CognitiveServices.Speech NuGet](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Aceite todos os contratos e licenças para iniciar a instalação.

   Após a instalação da embalagem, aparece uma confirmação na janela **da consola do gestor de pacotes.**

### <a name="choose-target-architecture"></a>Escolha arquitetura-alvo

Para construir e executar a aplicação da consola, crie uma configuração de plataforma que combine com a arquitetura do seu computador.

1. A partir da barra de menu, selecione **Build**  >  **Configuration Manager**. Aparece a caixa de diálogo **do Gestor de Configuração.**

   ![Caixa de diálogo do gestor de configuração](~/articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Na caixa de lançamento da plataforma de **solução Ative,** selecione **New**. Aparece a caixa de diálogo **da Nova Plataforma de Solução.**

1. No **Tipo ou selecione a nova** caixa de entrega da plataforma:
   - Se estiver a executar o Windows de 64 bits, selecione **x64**.
   - Se estiver a executar o Windows de 32 bits, selecione **x86**.

1. Selecione **OK** e, em seguida, **Feche**.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [windows](../quickstart-list.md)]
