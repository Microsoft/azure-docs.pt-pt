---
title: Problemas conhecidos com o plugin acústicos do projeto
titlesuffix: Azure Cognitive Services
description: Você pode encontrar os seguintes problemas conhecidos ao usar a visualização do designer para acústica do projeto.
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kylsto
ms.openlocfilehash: 53df981564eb177da66b86022ecfc80b25f1c763
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296540"
---
# <a name="project-acoustics-known-issues"></a>Problemas conhecidos do projeto acústicos
Você pode encontrar os seguintes problemas conhecidos ao usar a visualização do designer para acústica do projeto.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Os parâmetros acústicos são perdidos quando você renomeia uma cena

Se você renomear uma cena, todos os parâmetros acústicos que pertencem a essa cena não serão transferidos automaticamente para a nova cena. No entanto, eles ainda existirão no arquivo de ativo antigo. Procure o arquivo **SceneName_AcousticParameters. Asset** dentro do diretório do **Editor** ao lado do seu arquivo de cena. Renomeie o arquivo para refletir o novo nome da cena.

## <a name="unity-crashes-when-closing-project"></a>O Unity falha ao fechar o projeto

Nas versões mais recentes do Unity (2018.2 +), há um bug conhecido em que o Unity falhará quando você fechar o projeto. Isso é acompanhado por [esse problema do Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="deploying-to-android-from-some-unity-versions"></a>Implantando no Android de algumas versões do Unity

Algumas versões do Unity têm um bug com a implantação de plug-ins de áudio no Android. Verifique se você não está usando uma versão afetada por [esse bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Recebo um erro que ' não pôde localizar o arquivo de metadados System. Security. dll '

Verifique se a versão do tempo de execução de script no player está definida como **.NET 4. x equivalente**e reinicie o Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Estou tendo problemas de autenticação ao se conectar ao Azure

Verifique se você usou as credenciais corretas para sua conta do Azure, se sua conta dá suporte ao tipo de nó solicitado no torta e se o relógio do sistema está correto.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>Cancelar um distorta deixa a guia de distorta no estado "excluindo"
Os acústicos do projeto limparão todos os recursos do Azure para um trabalho após a conclusão ou cancelamento bem-sucedido. Isso pode levar até 5 minutos.

## <a name="next-steps"></a>Passos Seguintes
* Experimente o [Unity](unity-quickstart.md) ou o conteúdo de exemplo não [real](unreal-quickstart.md)

