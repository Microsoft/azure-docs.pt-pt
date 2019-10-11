---
title: Problemas conhecidos do plug-in acústica do projeto
titlesuffix: Azure Cognitive Services
description: Você pode enfrentar os seguintes problemas conhecidos na acústica do projeto.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243031"
---
# <a name="project-acoustics-known-issues"></a>Problemas conhecidos do projeto acústicos
Este artigo descreve os problemas que podem ocorrer ao usar o projeto acústicos.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Os parâmetros acústicos são perdidos quando você renomeia uma cena

Se você renomear uma cena, todos os parâmetros acústicos que pertencem a essa cena não são transferidos automaticamente para a nova cena. Mas eles ainda existem no arquivo de ativo antigo. Procure o arquivo *[scenename] _AcousticParameters. Asset* no diretório do *Editor* ao lado do seu arquivo de cena. Renomeie o arquivo para refletir o novo nome da cena.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Bug do Deploy-to-Android de algumas versões do Unity

Algumas versões do Unity têm um [bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) no modo como implantam plug-ins de áudio no Android. Certifique-se de que você não está usando uma versão afetada por esse bug.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>Erro "não foi possível encontrar o arquivo de metadados System. Security. dll"

Certifique-se de que a **versão de tempo de execução de script** nas configurações do **Player** seja *.NET 4. x equivalente*e reinicie o Unity.

## <a name="authentication-problems-when-connecting-to-azure"></a>Problemas de autenticação ao se conectar ao Azure

Verifique se:
- Você usou as credenciais corretas para sua conta do Azure.
- Sua conta dá suporte ao tipo de nó que você solicitou no cotorta.
- O relógio do sistema está definido corretamente.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>A guia distorta ainda mostra "excluindo" depois de cancelar
O projeto acústica limpa todos os recursos do Azure para um trabalho após a conclusão ou cancelamento bem-sucedido. Esse processo pode levar até 5 minutos.

## <a name="next-steps"></a>Passos seguintes
* Experimente o conteúdo de exemplo do [Unity](unity-quickstart.md) ou [inreal](unreal-quickstart.md) .
