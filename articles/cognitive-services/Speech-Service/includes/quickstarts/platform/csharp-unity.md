---
title: 'Quickstart: Speech SDK for C# Unity platform setup - Speech service'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para configurar a sua plataforma para C# Unidade com o serviço de fala SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: 8d9e05b5d8105cce8355d217cc6922c25bbe1730
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75469743"
---
Este guia mostra como instalar o [SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) de Discurso para [a Unidade](https://unity3d.com/).

> [!NOTE]
> O Speech SDK for Unitity suporta windows desktop (x86 e x64) ou Universal Windows Platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) e iOS (simulador x64, ARM32 e ARM64)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

- [Unidade 2018.3 ou mais tarde](https://store.unity.com/) com [unidade 2019.1 adicionando apoio ao UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Estúdio Visual 2019.](https://visualstudio.microsoft.com/downloads/) A versão 15.9 ou superior do Visual Studio 2017 também é aceitável.
- Para suporte ao Windows ARM64, instale as [ferramentas de construção opcionais para ARM64 e o Windows 10 SDK para ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="install-the-speech-sdk"></a>Instale o SDK do Discurso

Para instalar o SDK de Discurso para a Unidade, siga estes passos:

1. Descarregue e abra o [Speech SDK for Unitity](https://aka.ms/csspeech/unitypackage), que é embalado como um pacote de ativos da Unidade (.unitypackage), e já deve estar associado à Unidade. Quando o pacote de ativos é aberto, aparece a caixa de diálogo **do Pacote de Unidade de Importação.** Poderá ter de criar e abrir um projeto vazio para que este passo funcione.

   [![Caixa de diálogo do Pacote de Unidade de Importação no Editor da Unidade](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. Certifique-se de que todos os ficheiros são selecionados e selecione **Import**. Após alguns momentos, o pacote de ativos da Unidade é importado para o seu projeto.

Para obter mais informações sobre a importação de pacotes de ativos para a Unidade, consulte a [documentação da Unidade.](https://docs.unity3d.com/Manual/AssetPackages.html)

Agora pode passar para [os próximos passos](#next-steps) abaixo.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [windows](../quickstart-list.md)]
