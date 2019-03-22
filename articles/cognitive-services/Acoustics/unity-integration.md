---
title: Projeto Acoustics Unity integração e implementação
titlesuffix: Azure Cognitive Services
description: Nesta explicação de procedimento explica a integração do plug-in do projeto Acoustics Unity em seu projeto Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: aa7c5c513d65310bf9bffab29c1d18e7e7a85b49
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316291"
---
# <a name="project-acoustics-unity-integration"></a>Integração do projeto Acoustics Unity
Nesta explicação de procedimento explica a integração do plug-in do projeto Acoustics Unity em seu projeto Unity.

Requisitos de software:
* [Unity 2018.2 +](http://unity3d.com) para Windows
* [Pacote do Acoustics Unity de projeto](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>Importar o plug-in
Importe o acoustics UnityPackage ao seu projeto. 
* No Unity, aceda a **ativos > Importar pacote > pacote personalizado...**

    ![Menu de captura de ecrã do Unity importar pacote](media/import-package.png)  

* Escolha **ProjectAcoustics.unitypackage**

Se estiver a importar o plug-in num projeto existente, o seu projeto pode já ter uma **mcs.rsp** ficheiro na raiz do projeto, que especifica as opções para o compilador c#. Terá de mesclar o conteúdo desse ficheiro com o arquivo de mcs.rsp que vem com o plug-in do projeto Acoustics.

## <a name="enable-the-plugin"></a>Ativar o plug-in
A parte de criar o Kit de ferramentas de acoustics requer a versão de tempo de execução do script 4.x do .NET. Importação do pacote irá atualizar as definições de leitor de Unity. Reinicie o Unity para esta definição tenha efeito.

![Painel de definições de leitor captura de ecrã do Unity](media/player-settings.png)

![Painel de captura de ecrã do Unity as definições do leitor com o .NET 4.5 selecionado](media/net45.png)

## <a name="set-up-audio-dsp"></a>Configurar DSP áudio
Projeto Acoustics inclui o tempo de execução áudio DSP que integra o Framework de spatializer de mecanismo de áudio do Unity. Ele inclui spatialization com base em HRTF tanto de movimento panorâmico. Ativar o DSP Acoustics de projeto abrindo as definições de áudio de Unity através de **Editar > definições do projeto > áudio**, selecionando **Acoustics de projeto** como o **Plug-in do Spatializer** para o seu projeto. Certifique-se de que o **tamanho da memória intermédia DSP** está definido para melhor desempenho.

![Painel de captura de ecrã de definições do projeto Unity](media/project-settings.png)  

![Painel de definições de captura de ecrã do Unity Spatializer com spatializer Acoustics de projeto selecionado](media/choose-spatializer.png)

Em seguida, abra o Mixer de áudio (**janela > áudio Mixer**). Certifique-se de que tem pelo menos um Mixer, com um grupo. Se não o fizer, clique no botão "+" à direita da **Mixers**. Com o botão direito na parte inferior da strip canal na secção efeitos e adicione a **projeto Acoustics Mixer** efeito. Tenha em atenção que apenas um Mixer de Acoustics de projeto é suportado por vez.

![Captura de ecrã do Unity áudio Mixer mixer Acoustics de projeto de alojamento](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Ativar acoustics em origens de som
Crie uma origem de áudio. Clique na caixa de verificação na parte inferior do painel de um inspetor do AudioSource que diz **Spatialize**. Certifique-se **geográficos Blend** está definido como 3D completa.  

![Painel de origem de áudio captura de ecrã do Unity](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Ativar o design acústico
Anexar o script **AcousticsAdjust** a uma origem de som em seu cenário para habilitar os parâmetros de design de origem adicionais, clicando em **Add Component** e escolhendo **Scripts > Acoustics ajustar** :

![Captura de ecrã do Unity AcousticsAdjust script](media/acoustics-adjust.png)

## <a name="next-steps"></a>Passos Seguintes
* [Inserir sua cena com Acoustics de projeto para Unity](unity-baking.md)
* [Criar uma conta do Azure Batch](create-azure-account.md) para implantar sua cena na cloud
* Explore os [processo de design de projeto Acoustics Unity](unity-workflow.md).

