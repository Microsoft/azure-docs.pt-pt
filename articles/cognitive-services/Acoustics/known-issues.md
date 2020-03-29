---
title: Questões conhecidas do Project Acoustics
titlesuffix: Azure Cognitive Services
description: Pode experimentar os seguintes problemas conhecidos no Projeto Acústica.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: b71b93f271608d946d964f70dae9eefbef77e87b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243031"
---
# <a name="project-acoustics-known-issues"></a>Projeto Acústica conhecida questões
Este artigo descreve questões que pode experimentar quando utiliza o Project Acoustics.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Os parâmetros acústicos perdem-se quando se rebatiza uma cena

Se mudares o nome de uma cena, todos os parâmetros acústicos que pertencem a essa cena não se transferem automaticamente para a nova cena. Mas ainda existem no antigo ficheiro de ativos. Procure o ficheiro *[SceneName]_AcousticParameters.asset* no diretório do *Editor* ao lado do seu ficheiro de cena. Mude o nome do ficheiro para refletir o novo nome da cena.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Bug implementa-para-Android a partir de algumas versões Da Unidade

Algumas versões da Unidade têm um [bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) na forma como implementam plug-ins de áudio para Android. Certifique-se de que não está a usar uma versão que seja afetada por este bug.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>Erro "Não consegui encontrar ficheiros de metadados System.Security.dll" erro

Certifique-se de que a versão de execução de **scripts** nas definições do **Jogador** é *.NET 4.x Equivalente*, e reiniciar a Unidade.

## <a name="authentication-problems-when-connecting-to-azure"></a>Problemas de autenticação na ligação ao Azure

Verifique se:
- Usou as credenciais corretas para a sua conta Azure.
- A sua conta suporta o tipo de nó que pediu no assado.
- O relógio do sistema está corretamente definido.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>O separador Bake ainda mostra "apagar" depois de cancelar
O Project Acoustics limpa todos os recursos azure para um trabalho após a conclusão ou cancelamento bem sucedidos. Este processo pode demorar até 5 minutos.

## <a name="next-steps"></a>Passos seguintes
* Experimente o conteúdo da [amostra Unidade](unity-quickstart.md) ou [Irreal.](unreal-quickstart.md)
