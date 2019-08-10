---
title: Integração e implantação do Unity acústicas do projeto
titlesuffix: Azure Cognitive Services
description: Este "como" explica a integração do plug-in de Unity do acústica do projeto em seu projeto do Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 54bc98e0ddba0292c6a5dbb07f2bbdfce6a1cb45
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933149"
---
# <a name="project-acoustics-unity-integration"></a>Integração do Unity da acústica do projeto
Este "como" explica a integração do plug-in de Unity do acústica do projeto em seu projeto do Unity.

Requisitos de software:
* [Unity 2018.2 +](https://unity3d.com) para Windows
* [Pacote de Unity do projeto acústicos](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>Importar o plug-in
Importe o UnityPackage acústicos para o seu projeto. 
* No Unity, acesse **ativos > importar pacote > pacote personalizado...**

    ![Captura de tela do menu do pacote de importação do Unity](media/import-package.png)  

* Escolha **ProjectAcoustics. unitypackage**

* Clique no botão **importar** para integrar o pacote do Unity ao seu projeto  

    ![Captura de tela da caixa de diálogo Importar pacote do Unity](media/import-dialog.png)  

Se você estiver importando o plug-in em um projeto existente, seu projeto talvez já tenha um arquivo **MCS. rsp** na raiz do projeto, que C# especifica as opções para o compilador. Você precisará mesclar o conteúdo desse arquivo com o arquivo MCS. rsp que acompanha o plug-in de acústica do projeto.

## <a name="enable-the-plugin"></a>Habilitar o plug-in
A porção de distorta do kit de ferramentas acústicas requer a versão de tempo de execução de script do .NET 4. x. A importação de pacote atualizará as configurações do seu Unity Player. Reinicie o Unity para que essa configuração entre em vigor.

![Captura de tela do painel de configurações do Unity Player](media/player-settings.png)

![Captura de tela do painel de configurações do Unity Player com o .NET 4,5 selecionado](media/net45.png)

## <a name="set-up-audio-dsp"></a>Configurar o DSP de áudio
O projeto acústica inclui o DSP de tempo de execução de áudio que se integra à estrutura spatializer do mecanismo de áudio do Unity. Ele inclui a espaciaiização com base em HRTF e panorâmica. Habilite o DSP do projeto acústica abrindo as configurações de áudio do Unity usando **editar > configurações do projeto > áudio**e, em seguida, selecionando o **projeto acústica** como o **plug-in Spatializer** para o seu projeto. Verifique se o **tamanho do buffer do DSP** está definido como o melhor desempenho.

![Captura de tela do painel de configurações do projeto do Unity](media/project-settings.png)  

![Captura de tela do painel de configurações do Unity Spatializer com o projeto acústicos Spatializer selecionados](media/choose-spatializer.png)

Em seguida, abra o mixer de áudio (**janela > mixer de áudio**). Verifique se você tem pelo menos um mixer, com um grupo. Se não estiver, clique no botão ' + ' à direita dos **mixers**. Clique com o botão direito do mouse na parte inferior da faixa do canal na seção de efeitos e adicione o efeito de mixer do misturador acústicos do **projeto** . Observe que apenas um mixer de acústica do projeto tem suporte por vez.

![Captura de tela do mixer de áudio do Unity que hospeda o mixer acústicos do projeto](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Habilitar acústicos em fontes de som
Crie uma fonte de áudio. Clique na caixa de seleção na parte inferior do painel de inspetores da áudio, que diz espacialize. Verifique se a **mistura espacial** está definida como 3D completo.  

![Captura de tela do painel de origem de áudio do Unity](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Habilitar Design acústico
Anexe o script **AcousticsAdjust** a uma fonte de som em sua cena para habilitar parâmetros de design de origem adicionais, clicando em **Adicionar componente** e escolhendo **scripts > acústicos ajustar**:

![Captura de tela do script do Unity AcousticsAdjust](media/acoustics-adjust.png)

## <a name="next-steps"></a>Passos seguintes
* [Distortar sua cena com o projeto acústicos para o Unity](unity-baking.md)
* [Criar uma conta do lote do Azure](create-azure-account.md) para entortar sua cena na nuvem
* Explore o [processo de design da unidade acústica do projeto](unity-workflow.md).

