---
title: Integração e implantação do Unity acústicas do projeto
titlesuffix: Azure Cognitive Services
description: Este artigo descreve como integrar o plug-in de Unity do acústica do projeto em seu projeto do Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a8ddb0e4ca2ee4396a25a70c8b60b653aebb72d8
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243003"
---
# <a name="project-acoustics-unity-integration"></a>Integração do Unity da acústica do projeto
Este artigo descreve como integrar o plug-in de Unity do acústica do projeto em seu projeto do Unity.

Requisitos de software:
* [Unity 2018.2 +](https://unity3d.com) para Windows
* [Pacote de Unity do projeto acústicos](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>Importar o plug-in
1. Importe os UnityPackage acústicos em seu projeto. 
 No Unity, acesse **ativos** > **Importar pacote** > **pacote personalizado**.

    ![O menu do pacote de importação do Unity](media/import-package.png)  

1. Escolha **ProjectAcoustics. unitypackage**.

1. Selecione o botão **importar** para integrar o pacote do Unity ao seu projeto.

    ![A caixa de diálogo pacote de importação do Unity](media/import-dialog.png)  

Se você estiver importando o plug-in em um projeto existente, seu projeto talvez já tenha um arquivo *MCS. rsp* na raiz do projeto. Esse arquivo especifica as opções para C# o compilador. Mescle o conteúdo desse arquivo com o arquivo MCS. rsp que vem com o plug-in de acústica do projeto.

## <a name="enable-the-plug-in"></a>Habilitar o plug-in
A porção de sobreposição do kit de ferramentas acústicas requer o .NET 4. versão de tempo de execução de script *x* . A importação de pacote atualiza as configurações do seu Unity Player. Reinicie o Unity para que essa configuração entre em vigor.

![O painel de configurações do Player do Unity](media/player-settings.png)

![O painel de configurações do Player do Unity com o .NET 4,5 selecionado](media/net45.png)

## <a name="set-up-audio-dsp"></a>Configurar o DSP de áudio
O projeto acústica inclui o DSP de tempo de execução de áudio que se integra à estrutura spatializer do mecanismo de áudio do Unity. Ele inclui a espaciaiização com base em HRTF e panorâmica. Para habilitar o DSP do projeto acústicos, vá para **Editar**configurações do**projeto** >   > **áudio** para abrir as configurações de áudio do Unity. Selecione o **projeto acústica** como o **plug-in Spatializer** para seu projeto. Verifique se o **tamanho do buffer do DSP** está definido como o *melhor desempenho*.

![O menu de configurações do projeto do Unity](media/project-settings.png)  

![O painel de configurações do Unity Spatializer com o projeto acústicos Spatializer selecionados](media/choose-spatializer.png)

Em seguida, abra o mixer de áudio (**janela** > **mixer de áudio**). Verifique se você tem pelo menos um mixer, com um grupo. Se você não tiver um, selecione o botão **+** à direita dos **mixers**. Clique com o botão direito do mouse na parte inferior da faixa de canal na seção de efeitos e adicione o efeito de **mixer do Microsoft acústicos** . Somente um mixer de acústica do projeto tem suporte por vez.

![O mixer de áudio do Unity que hospeda o mixer de acústica do projeto](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Habilitar acústicos em fontes de som
Criar uma fonte de áudio: marque a caixa de seleção **espaciale** na parte inferior do painel Inspetor de áudio. Verifique se a **mistura espacial** está definida como *3D*completo.  

![O painel de origem de áudio do Unity](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Habilitar Design acústico
Anexe o script *AcousticsAdjust* a uma fonte de som em sua cena para habilitar parâmetros de design de origem adicionais: selecione **Adicionar componente** e escolha **scripts** > **ajuste acústicos**.

![O script do Unity AcousticsAdjust](media/acoustics-adjust.png)

## <a name="next-steps"></a>Passos seguintes
* [Distortar sua cena com acústicas de projetos para o Unity](unity-baking.md).
* [Crie uma conta do lote do Azure](create-azure-account.md) para entortar sua cena na nuvem.
* Explore o [processo de design da unidade acústica do projeto](unity-workflow.md).
