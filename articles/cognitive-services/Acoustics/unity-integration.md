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
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 7d8edd7b092ee1ed4f953d753b2bbe864e075378
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137737"
---
# <a name="project-acoustics-unity-integration"></a>Integração do projeto Acoustics Unity
Nesta explicação de procedimento explica a integração do plug-in do projeto Acoustics Unity em seu projeto Unity.

Requisitos de software:
* [Unity 2018.2 +](http://unity3d.com) para Windows
* [Pacote do Acoustics Unity de projeto](https://www.microsoft.com/en-us/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>Importar o plug-in
Importe o acoustics UnityPackage ao seu projeto. 
* No Unity, aceda a **ativos > Importar pacote > pacote personalizado...**

    ![Importar pacote](media/import-package.png)  

* Escolha **ProjectAcoustics.unitypackage**

Se estiver a importar o plug-in num projeto existente, o seu projeto pode já ter uma **mcs.rsp** ficheiro na raiz do projeto, que especifica as opções para o compilador c#. Terá de mesclar o conteúdo desse ficheiro com o arquivo de mcs.rsp que vem com o plug-in do projeto Acoustics.

## <a name="enable-the-plugin"></a>Ativar o plug-in
A parte de criar o Kit de ferramentas de acoustics requer a versão de tempo de execução do script 4.x do .NET. Importação do pacote irá atualizar as definições de leitor de Unity. Reinicie o Unity para esta definição tenha efeito.

![Definições do leitor](media/player-settings.png)

![.NET 4.5](media/net45.png)

## <a name="set-up-audio-dsp"></a>Configurar DSP áudio
Projeto Acoustics inclui o tempo de execução áudio DSP que integra o Framework de spatializer de mecanismo de áudio do Unity. Ele inclui spatialization com base em HRTF tanto de movimento panorâmico. Ativar o DSP Acoustics de projeto abrindo as definições de áudio de Unity através de **Editar > definições do projeto > áudio**, selecionando **Acoustics de projeto** como o **Plug-in do Spatializer** para o seu projeto. Certifique-se de que o **tamanho da memória intermédia DSP** está definido para melhor desempenho.

![Definições do projeto](media/project-settings.png)  

![Projeto Acoustics Spatializer](media/choose-spatializer.png)

Em seguida, abra o Mixer de áudio (**janela > áudio Mixer**). Certifique-se de que tem pelo menos um Mixer, com um grupo. Se não o fizer, clique no botão "+" à direita da **Mixers**. Com o botão direito na parte inferior da strip canal na secção efeitos e adicione a **projeto Acoustics Mixer** efeito. Tenha em atenção que apenas um Mixer de Acoustics de projeto é suportado por vez.

![Áudio Mixer](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Ativar acoustics em origens de som
Crie uma origem de áudio. Clique na caixa de verificação na parte inferior do painel de um inspetor do AudioSource que diz **Spatialize**. Certifique-se **geográficos Blend** está definido como 3D completa.  

![Origem de áudio](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Ativar o design acústico
Anexar o script **AcousticsAdjust** a uma origem de som em seu cenário para habilitar os parâmetros de design de origem adicionais, clicando em **Add Component** e escolhendo **Scripts > Acoustics ajustar** :

![AcousticsAdjust](media/acoustics-adjust.png)

## <a name="next-steps"></a>Passos Seguintes
* [Inserir sua cena com Acoustics de projeto para Unity](unity-baking.md)
* [Criar uma conta do Azure Batch](create-azure-account.md) para implantar sua cena na cloud
* Explore os [processo de design de projeto Acoustics Unity](unity-workflow.md).

