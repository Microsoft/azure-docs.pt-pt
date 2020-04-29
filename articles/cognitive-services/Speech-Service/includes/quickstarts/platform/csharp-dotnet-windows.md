---
title: 'Quickstart: Speech SDK for .NET Framework (Windows) platform setup - Speech service'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para configurar a sua plataforma para C# em .NET Framework for Windows with the Speech service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: a09b969ee3e11aeb04f338cf035b21b5da9bd952
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78925736"
---
Este guia mostra como instalar o [SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) de Fala para .NET Framework (Windows). Se você só quer que o nome do `Install-Package Microsoft.CognitiveServices.Speech` pacote seja iniciado por conta própria, corra na consola NuGet.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Crie um projeto de Estúdio Visual e instale o Speech SDK

Terá de instalar o [pacote NuGet Do Discurso SDK](https://aka.ms/csspeech/nuget) para que possa remeti-lo no seu código. Para isso, pode primeiro precisar de criar um projeto **helloworld.** Se já tiver um projeto com a carga de trabalho de desenvolvimento do ambiente de **trabalho .NET** disponível, pode utilizar esse projeto e saltar para [utilizar o NuGet Package Manager para instalar o Speech SDK](#use-nuget-package-manager-to-install-the-speech-sdk).

### <a name="create-helloworld-project"></a>Criar projeto Helloworld

1. Open Visual Studio 2019.

1. Na janela Iniciar, selecione **Criar um novo projeto**. 

1. Na **janela Criar uma nova** janela de projeto, escolha **a App consola (.NET Framework)** e, em seguida, selecione **Next**.

1. Na **configuração** da sua nova janela de projeto, insira *o Helloworld* em **nome do Projeto,** escolha ou crie o caminho de diretório no **Local**, e, em seguida, selecione **Criar**.

1. A partir da barra de menus do Estúdio Visual, selecione **Tools** > **Get Tools and Features**, que abre o Instalador de Estúdios Visuais e exibe a caixa de diálogo **Modificador.**

1. Verifique se a carga de trabalho de desenvolvimento do ambiente de **trabalho .NET** está disponível. Se a carga de trabalho não tiver sido instalada, selecione a caixa de verificação ao lado e, em seguida, selecione **Modificar** para iniciar a instalação. Pode levar alguns minutos para descarregar e instalar.

   Se a caixa de verificação ao lado do desenvolvimento do ambiente de **trabalho .NET** já estiver selecionada, selecione **Close** to exit the dialog box.

   ![Ativar o desenvolvimento do ambiente de trabalho .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Instalador de estúdio visual próximo.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>Use nuGet Package Manager para instalar o SDK de fala

1. No Solution Explorer, clique no projeto **Helloworld** e, em seguida, selecione **Gerir pacotes NuGet** para mostrar o NuGet Package Manager.

   ![Gestor de Pacotes NuGet](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. No canto superior direito, encontre a caixa de entrega **`nuget.org`** da Fonte de **Embalagem** e certifique-se de que é selecionada.

1. No canto superior esquerdo, **selecione Browse**.

1. Na caixa de pesquisa, digite *Microsoft.CognitiveServices.Speech* e selecione **Enter**.

1. A partir dos resultados da pesquisa, selecione o pacote **Microsoft.CognitiveServices.Speech** e, em seguida, selecione **Instalar** para instalar a versão mais recente estável.

   ![Instale o pacote Microsoft.CognitiveServices.Speech NuGet](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Aceite todos os acordos e licenças para iniciar a instalação.

   Após a instalação da embalagem, aparece uma confirmação na janela da Consola do Gestor de **Pacotes.**

### <a name="choose-target-architecture"></a>Escolha a arquitetura-alvo

Para construir e executar a aplicação da consola, crie uma configuração de plataforma que combine com a arquitetura do seu computador.

1. A partir da barra de menus, selecione **Build** > **Configuration Manager**. Aparece a caixa de diálogo do Gestor de **Configuração.**

   ![Caixa de diálogo do Gestor de Configuração](~/articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Na caixa de drop-down da plataforma ative **solution,** selecione **New**. Aparece a caixa de diálogo new **solution Platform.**

1. No **Tipo ou selecione a nova** caixa de lançamento da plataforma:
   - Se estiver a executar o Windows de 64 bits, selecione **x64**.
   - Se estiver a executar o Windows de 32 bits, selecione **x86**.

1. Selecione **OK** **e,** em seguida, Feche .

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [windows](../quickstart-list.md)]
