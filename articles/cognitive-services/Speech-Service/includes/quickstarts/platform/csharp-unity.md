---
title: 'Quickstart: Speech SDK for C# Unidade plataforma configuração - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para configurar a sua plataforma para unidade C# com o serviço de discurso SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 58ce8dc67488c42485f2fac73e514c5639b11cf9
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552215"
---
Este guia mostra como instalar o [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) for [Unitity](https://unity3d.com/).

> [!NOTE]
> O Speech SDK for Unitity suporta o Windows Desktop (x86 e x64) ou a Plataforma Universal windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) e iOS (simulador x64, ARM32 e ARM64)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

- No Windows, precisa do [Microsoft Visual C++ Redistributable para Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) para a sua plataforma. A instalação desta situação pela primeira vez pode exigir um reinício.
- [Unidade 2018.3 ou mais tarde](https://store.unity.com/) com [Unidade 2019.1 adicionando suporte para UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). A versão 15.9 ou superior do Visual Studio 2017 também é aceitável.
- Para o suporte ao Windows ARM64, instale as [ferramentas de construção opcionais para ARM64 e o Windows 10 SDK para ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

Para instalar o Speech SDK for Unitity, siga estes passos:

1. Descarregue e abra o [Speech SDK for Unitity](https://aka.ms/csspeech/unitypackage), que é embalado como um pacote de ativos de unidade (.unitypackage), e já deve ser associado à Unidade. Quando o pacote de ativos é aberto, aparece a caixa de diálogo **do Pacote de Unidade de Importação.** Poderá ser necessário criar e abrir um projeto vazio para que este passo funcione.

   [![Caixa de diálogo do Pacote de Unidade de Importação no Editor de Unidade](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. Certifique-se de que todos os ficheiros estão selecionados e selecione **Import**. Após alguns momentos, o pacote de ativos da Unidade é importado para o seu projeto.

Para obter mais informações sobre a importação de pacotes de ativos para a Unidade, consulte a [documentação da Unidade.](https://docs.unity3d.com/Manual/AssetPackages.html)

Pode agora passar para os [próximos passos](#next-steps) abaixo.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [windows](../quickstart-list.md)]
