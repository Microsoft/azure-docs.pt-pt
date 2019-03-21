---
title: Guia de introdução do projeto Acoustics com Unreal
titlesuffix: Azure Cognitive Services
description: Usando o conteúdo de exemplo, experimente projeto Acoustics controles de design do Unreal e Wwise e implementar a área de trabalho do Windows.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 1314e393d292145ef112e700abf6ab1ef199db7d
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58138188"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Guia de introdução do projeto Acoustics Unreal/Wwise
Neste início rápido, irá experimentar projeto Acoustics controles de design usando o conteúdo de exemplo fornecido para o Unreal Engine e Wwise.

Requisitos de software:
* [Unreal Engine 4.21](https://www.unrealengine.com/)
* [Wwise 2018.1.6](https://www.audiokinetic.com/products/wwise/)

## <a name="download-the-sample-package"></a>Transferir o pacote de exemplo
Transfira o [pacote de exemplo de projeto Acoustics Unreal + Wwise](http://www.microsoft.com/downloads/details.aspx?FamilyID=f03dff5a-5780-462e-87ef-e6d039d0748d). O pacote de exemplo contém um projeto de Unreal Engine, o projeto de Wwise para esse projeto Unreal e o plug-in do projeto Acoustics Wwise.

## <a name="set-up-the-project-acoustics-sample-project"></a>Configurar o projeto de exemplo de projeto Acoustics
Para configurar o projeto de exemplo de projeto Acoustics Unreal/Wwise, terá primeiro de instalar o plug-in do projeto Acoustics em Wwise. Em seguida, implantar os binários de Wwise ao projeto Unreal e ajustar Unreal Plug-in o Wwise para suportar Acoustics de projeto.

### <a name="install-the-project-acoustics-wwise-plugin"></a>Instalar o plug-in do projeto Acoustics Wwise
Abra o iniciador de Wwise, em seguida, no **plug-ins** separador, em **instalar novo plug-ins**, selecione **adicionar a partir do diretório**. Escolha o `AcousticsWwisePlugin\ProjectAcoustics` diretório que foi incluído no pacote que transferiu.

![Instalar o plug-in do Wwise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Adicionar Wwise binários para o projeto Acoustics Unreal projeto de exemplo
A partir do iniciador de Wwise, clique nas **Unreal Engine** separador, em seguida, clique no menu de hambúrguer junto a **projetos recentes de motor Unreal** e selecione **navegue para o projeto**. Abra o projeto Unreal de exemplo `.uproject` ficheiro do pacote `AcousticsSample\AcousticsGame\AcousticsGame.uproject`.

![Separador Wwise Unreal](media/wwise-unreal-tab.png)

Em seguida, junto ao projeto de exemplo Acoustics de projeto, clique em **Wwise integrar no projeto**.

![Projeto do jogo Wwise Acoustics Unreal](media/wwise-acoustics-game-project.png)

### <a name="extend-wwises-unreal-plugin-functionality"></a>Expandir a funcionalidade de plug-in Unreal do Wwise
O projeto Acoustics Unreal Plug-in requer comportamento adicional seja exposto a partir do plug-in do Wwise Unreal API. Execute o ficheiro de batch fornecido com o projeto Acoustics Unreal Plug-in para automatizar essas modificações:
* Dentro `AcousticsGame\Plugins\ProjectAcoustics\Resources`, execute `PatchWwise.bat`.

    ![Script de Wwise de patch](media/patch-wwise-script.png)

* Se não tiver instalado o SDK do DirectX, precisará comentar a linha que contém DXSDK_DIR no `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Comente DXSDK](media/directx-sdk-comment.png)

### <a name="open-the-unreal-project"></a>Abra o projeto Unreal. 
Ele irá pedir-lhe para reconstruir módulos; Clique em Sim.

Se abrir o projeto falhar em falhas de compilação, verifique se instalou o plug-in do projeto Acoustics Wwise para a mesma versão do Wwise utilizada no projeto de exemplo de projeto Acoustics.

## <a name="experiment-with-project-acoustics-design-controls"></a>Experimente com controles de design de projeto Acoustics
Ouça a forma como a cena parece clicando no botão de reprodução no editor de Unreal. Na área de trabalho, utilize W, S, D e o mouse para mover-se. Para ver os atalhos de teclado para obter mais controlos, prima **F1**. Aqui estão algumas atividades de design para experimentar:

### <a name="modify-occlusion-and-transmission"></a>Modificar oclusão e de transmissão
Existem controlos de design de Acoustics de projeto de código-fonte no cada ator som Unreal:

![DemoSceneSoundSourceDesignControls](media/demo-scene-sound-source-design-controls.png)

Se o **oclusão** multiplicador for superior a 1 (a predefinição é 1), irá ser exagerada oclusão. Defini-la a torna menos de 1 oclusão efeitos mais sutil.

Para ativar a transmissão através de parede, mover o **transmissão (dB)** controlo de deslize desativar seu nível mais baixo. 

### <a name="modify-wetness-for-a-source"></a>Modificar a umidade para uma origem
Para alterar o ritmo umidade é alterado com a distância, utilize o **Warp de distância Percetual**. Projeto Acoustics calcula wet níveis por todo o espaço de simulação, que variam sem problemas com a distância e proporcionam indicações de percepção de distância. Aumentando a forma de muito distância exaggerates esse efeito, aumentando os níveis de wet relacionados à distância. Distorcendo valores menores do que 1 certifique o reverberation com base na distância alterar mais sutil. Também pode ser ajustado esse efeito em detalhes mais refinado ao ajustar a **Umidade (dB)**.

Aumentar o tempo de Win32/decay por todo o espaço ao ajustar **escala de tempo de Win32/decay**. Considere o caso em que o resultado de simulação é um tempo de Win32/decay de 1,5 s. Definindo a **escala de tempo de Win32/decay** para 2 irá resultar num tempo Win32/decay aplicado para a origem de 3 s.

### <a name="modify-distance-based-attenuation"></a>Modificar a atenuação com base na distância
O plug-in do projeto Acoustics Wwise mixer respeita a atenuação de com base na distância do código-fonte criada na Wwise. A alteração de nessa curva irá alterar o nível de caminho de dry. O plug-in do projeto Acoustics irá ajustar o nível wet para manter a mistura de wet dry especificada pelos controles de simulação e design.

![DemoSoundsAttenuation](media/demo-sounds-attenuation.png)

Projeto Acoustics faz a computação numa caixa "região de simulação" centrada em torno de cada localização de player simulado. Os ativos de acoustics no pacote de exemplo foram integrados com um raio de região de simulação de 45 m e os attenuations foram projetados para ser de 0 antes de 45 m. Embora este falloff não seja um requisito rigoroso, transporta a limitação que apenas a geometria dentro de 45 m do serviço de escuta será occlude sons.

## <a name="next-steps"></a>Passos Seguintes
* [Integrar o projeto Acoustics](unreal-integration.md) Plug-in para o seu projeto Unreal
* [Criar uma conta do Azure](create-azure-account.md) para as suas próprias criações


