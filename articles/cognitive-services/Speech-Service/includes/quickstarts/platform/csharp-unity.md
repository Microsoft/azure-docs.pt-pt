---
title: 'Início rápido: SDK de C# fala para a instalação da plataforma Unity-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para configurar sua plataforma para C# o Unity com o SDK dos serviços de fala.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: 6fa1024b68bae6a8353cc71f2749b221ab7e3bc4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502493"
---
Este guia mostra como instalar o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para o [Unity](https://unity3d.com/).

> [!NOTE]
> O SDK de fala para o Unity dá suporte à área de trabalho do Windows (x86 e x64) ou Plataforma Universal do Windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) e iOS (x64 Simulator, ARM32 e ARM64)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

- O [unity 2018,3 ou posterior](https://store.unity.com/) com o [Unity 2019,1 adicionando suporte para UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). A versão 15,9 ou superior do Visual Studio 2017 também é aceitável.
- Para obter suporte ao Windows ARM64, instale as [ferramentas de compilação opcionais para ARM64 e o SDK do Windows 10 para ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="install-the-speech-sdk"></a>Instalar o SDK de fala

Para instalar o SDK de fala para o Unity, siga estas etapas:

1. Baixe e abra o [SDK de fala para o Unity](https://aka.ms/csspeech/unitypackage), que é empacotado como um pacote de ativos de Unity (. unitypackage) e já deve estar associado ao Unity. Quando o pacote de ativos é aberto, a caixa de diálogo **Importar pacote do Unity** é exibida. Talvez seja necessário criar e abrir um projeto vazio para que essa etapa funcione.

   [caixa de diálogo ![importar pacote do Unity no editor do Unity](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. Verifique se todos os arquivos estão selecionados e selecione **importar**. Depois de alguns instantes, o pacote de ativos do Unity é importado para seu projeto.

Para obter mais informações sobre como importar pacotes de ativos para o Unity, consulte a [documentação do Unity](https://docs.unity3d.com/Manual/AssetPackages.html).

Agora você pode passar para [as próximas etapas](#next-steps) abaixo.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [windows](../quickstart-list.md)]
