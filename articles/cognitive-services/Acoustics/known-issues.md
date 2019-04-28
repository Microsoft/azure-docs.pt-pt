---
title: Problemas conhecidos com o plug-in do projeto Acoustics
titlesuffix: Azure Cognitive Services
description: Poderá encontrar os seguintes problemas conhecidos ao utilizar a pré-visualização do Designer para Acoustics do projeto.
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 03/20/2019
ms.author: kylestorck
ms.openlocfilehash: 50de4d983ed24440d655cf5b9ba3fb5e33d8d7cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61335352"
---
# <a name="project-acoustics-known-issues"></a>Problemas conhecidos do projeto Acoustics
Poderá encontrar os seguintes problemas conhecidos ao utilizar a pré-visualização do Designer para Acoustics do projeto.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Parâmetros acústicos são perdidos quando mudar o nome de uma cena

Se renomear uma cena, todos os parâmetros acústicos que pertencem a essa cena não automaticamente transferir para a cena nova. Ainda existam no ficheiro de elemento antigo no entanto. Procure o **SceneName_AcousticParameters.asset** dentro do ficheiro a **Editor** diretório junto ao seu ficheiro de cena. Mudar o nome do ficheiro para refletir o novo nome de cena.

## <a name="unity-crashes-when-closing-project"></a>Unity é interrompida ao fechar o projeto

Nas versões mais recentes do Unity (2018.2 +), há um bug conhecido onde Unity falhará ao fechar seu projeto. Isso é controlado [nesta edição do Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="deploying-to-android-from-some-unity-versions"></a>Implementar para o Android a partir de algumas versões do Unity

Algumas versões do Unity tem um bug com a implantação de plug-ins de áudio para o Android. Certifique-se de que não está a utilizar uma versão afetada por [este bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Recebo uma mensagem de erro que "não foi possível localizar o ficheiro de metadados System.Security.dll'

Certifique-se de que a versão de tempo de execução de scripts nas definições de Player está definida como **.NET 4.x equivalente**e reinicie o Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Estou a ter problemas de autenticação ao ligar ao Azure

Verificar novamente já utilizou as credenciais corretas para a sua conta do Azure, que a sua conta suporta o tipo de nó solicitado na criar e que o relógio do seu sistema está correta.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>A cancelar uma criar deixa o separador de criar no estado de "eliminação"
Projeto Acoustics limpará todos os recursos do Azure para uma tarefa na conclusão com êxito ou a um cancelamento. Esta ação pode demorar até 5 minutos.

## <a name="next-steps"></a>Passos Seguintes
* Experimente o [Unity](unity-quickstart.md) ou [Unreal](unreal-quickstart.md) conteúdo de exemplo

