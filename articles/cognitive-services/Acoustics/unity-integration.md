---
title: Integração e implantação da Unidade Acústica do Projeto
titlesuffix: Azure Cognitive Services
description: Este artigo descreve como integrar o project Acoustics Unitity plug-in no seu projeto de Unidade.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243003"
---
# <a name="project-acoustics-unity-integration"></a>Integração da Unidade acústica do Projeto
Este artigo descreve como integrar o project Acoustics Unitity plug-in no seu projeto de Unidade.

Requisitos de software:
* [Unidade 2018.2+](https://unity3d.com) para Windows
* [Pacote de Unidade Acústica do Projeto](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>Importar o plug-in
1. Importe a acústica UnityPackage para o seu projeto. 
 Em Unidade, vá ao**pacote personalizado**de**pacote** > de importação de **ativos.** > 

    ![O menu do Pacote de Importação de Unidade](media/import-package.png)  

1. Escolha **projectAcoustics.unitypackage**.

1. Selecione o botão **Import** para integrar o pacote Unidade no seu projeto.

    ![A caixa de diálogo do Pacote de Importação de Unidade](media/import-dialog.png)  

Se estiver a importar o plug-in para um projeto existente, o seu projeto pode já ter um ficheiro *MCS.rsp* na raiz do projeto. Este ficheiro especifica opções para o compilador C#. Misture o conteúdo desse ficheiro com o ficheiro mcs.rsp que vem com o plug-in do Project Acoustics.

## <a name="enable-the-plug-in"></a>Ativar o plug-in
A parte de cozedura do kit de ferramentas acústica requer o .NET 4. *x* versão de execução de script. A importação de pacoteactualiza as definições do seu jogador de Unidade. Reinicie a Unidade para que esta definição faça efeito.

![O painel de definições do jogador de unidade](media/player-settings.png)

![O painel de definições do jogador de unidade com .NET 4.5 selecionado](media/net45.png)

## <a name="set-up-audio-dsp"></a>Configurar dSP áudio
O Project Acoustics inclui dSP de tempo de execução áudio que se integra na estrutura espacial do motor de áudio Unity. Inclui a espacialização baseada em HRTF e panorâmica. Para ativar o Project Acoustics DSP, vá a **Editar** > **Definições** > de Projeto**Áudio** para abrir as definições de áudio da Unidade. Selecione **Project Acoustics** como o **Spatializer Plugin** para o seu projeto. Certifique-se de que o tamanho do **tampão DSP** está definido para *melhor desempenho*.

![O menu de definições do projeto de unidade](media/project-settings.png)  

![O painel de definições do Spatializer unity com o espacial do projeto Acoustics selecionado](media/choose-spatializer.png)

Em seguida, abra o Misturador de Áudio **(Misturador** > de**Áudio**da Janela). Certifique-se de que tem pelo menos uma batedeira, com um grupo. Se não tiver um, selecione o **+** botão à direita dos **Mixers**. Clique na parte inferior da tira do canal na secção de efeitos e adicione o efeito **Microsoft Acoustics Mixer.** Apenas uma batedeira do Projeto Acústica é suportada de cada vez.

![O Unity Audio Mixer que acolhe a misturadora de Project Acoustics](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Ativar a acústica em fontes sonoras
Criar uma fonte de áudio: Selecione a caixa de verificação **Spatialize** na parte inferior do painel de inspectoraudiosource. Certifique-se de que a **Mistura Espacial** está definida para o *3D*completo .  

![O painel Unity Audio Source](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Ativar o design acústico
Fixe o script *Acústico A* uma fonte de som na sua cena para permitir parâmetros adicionais de design de fonte: Selecione **Adicionar Componente** e escolha **scripts** > **Acústicoajustar**.

![O roteiro De Unidade AcústicaAdjust](media/acoustics-adjust.png)

## <a name="next-steps"></a>Passos seguintes
* [Asse a sua cena com o Projeto Acústica para A Unidade.](unity-baking.md)
* [Crie uma conta Azure Batch](create-azure-account.md) para assar a sua cena na nuvem.
* Explore o processo de design do [Projeto Acoustics Unitity.](unity-workflow.md)
