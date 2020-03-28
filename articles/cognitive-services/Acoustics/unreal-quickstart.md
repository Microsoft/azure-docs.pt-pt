---
title: Projeto Acústica quickstart com Unreal
titlesuffix: Azure Cognitive Services
description: Utilize conteúdo sonoro para experimentar os controlos de design do Project Acoustics em Unreal e Wwise e implemente para o ambiente de trabalho do Windows.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d3afcded894f72626a4f24bcbe85c34ac1329c29
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72242911"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Projeto Acústico Unreal/Wwise quickstart
Neste arranque rápido, você irá experimentar com os controlos de design do Projeto Acústica usando conteúdo de amostra para o Motor Irreal e Wwise.

Requisitos de software para a utilização do conteúdo da amostra:
* [Motor Irreal](https://www.unrealengine.com/) 4.22
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>Descarregue o pacote de amostras
Descarregue o pacote de [amostras Project Acoustics Unreal e Wwise](https://www.microsoft.com/download/details.aspx?id=58090). O pacote de amostras contém:
- Projeto motor irreal
- Projeto Wwise para o projeto Irreal
- Project Acoustics Wwise plug-in

## <a name="set-up-the-project-acoustics-sample-project"></a>Configurar o projeto de amostra do Projeto Acústica
Primeiro, instale o plug-in do Projeto Acoustics na Wwise. Em seguida, desloque os binários Wwise para o projeto Irreal. Em seguida, ajuste o plug-in Wwise Unreal para apoiar o Project Acoustics.

### <a name="install-the-project-acoustics-wwise-plug-in"></a>Instale o plug-in Wwise do Projeto Acoustics
Abra o Lançador Wwise. No separador **Plugins,** em **instalação de novos plug-ins,** selecione **Adicionar a partir do diretório**. Escolha o diretório *AcousticsWwisePlugin\ProjectAcoustics* que foi incluído no pacote que descarregou.

![A opção de instalar o plug-in Wwise no Lançador Wwise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Adicione binários Wwise ao projeto de amostra acústica irreal
1. No Lançador Wwise, selecione o separador **Unreal Engine.** 
1. Selecione o menu "hamburger" (ícone) ao lado de **Recentes Projetos de Motores Não Reais,** e, em seguida, selecione **Browse para projeto**. Abra a amostra Ficheiro Projeto Unreal *.uproject* no pacote *AcousticsSample\AcousticsGame\AcousticsGame.uproject*.

   ![O separador Irreal no Lançador Wwise](media/wwise-unreal-tab.png)

3. Ao lado do projeto de amostra de acústica do projeto, **selecione Integrar Wwise no Projeto**.

   ![O Wwise Launcher mostra o projeto Jogo Da Acústica Irreal com a opção Integração em destaque.](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Alargar a funcionalidade de plug-in Wwise Unreal
O plug-in Project Acoustics Unreal necessita de comportamento adicional exposto a partir da Wwise Unreal plug-in API. Executar o ficheiro de lote que veio com o project Acoustics Unreal plug-in para automatizar estas modificações.
* Inside *AcousticsGame\Plugins\ProjectAcoustics\Resources,* run *PatchWwise.bat*.

    ![O script para remendar o projeto Wwise realçado numa janela do Windows Explorer](media/patch-wwise-script.png)

* Se não tiver o DirectX SDK instalado: Dependendo da versão da Wwise que está a usar, `DXSDK_DIR` poderá ter de comentar a linha que contém no *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![O editor de código que mostra "DXSDK" comentou](media/directx-sdk-comment.png)

* Se compilar utilizando o Visual Studio 2019: Para contornar um erro de ligação com wwise, altere manualmente o valor padrão *VSVersion* em *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* para **vc150**:

    ![O editor de código com VSVersion mudou para "vc150"](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Abra o Projeto Irreal 
Quando abrir o projeto Unreal, irá instá-lo a reconstruir módulos. Selecione **Sim**.

Se a abertura do projeto falhar devido a falhas de construção, verifique se instalou o Project Acoustics Wwise plug-in para a mesma versão de Wwise que foi usada no projeto de amostra do Projeto Acústica.

Se utilizar uma versão do [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) mais cedo do que a versão 2019.1, não pode gerar bancos de som utilizando o projeto de amostra do Project Acoustics. Precisa integrar a versão Wwise 2019.1 no projeto da amostra.

## <a name="experiment-with-project-acoustics-design-controls"></a>Experimente os controlos de design do Projeto Acústica
Ouça como a cena soa selecionando o botão de reprodução no editor da Unreal. Use as teclas W, A, S e D e o rato para se mover. Para ver atalhos de teclado para controlos adicionais, selecione F1.

As seguintes informações descrevem algumas atividades de design para experimentar.

### <a name="modify-occlusion-and-transmission"></a>Modificar a oclusão e a transmissão
Existem controlos de design por fonte do Projeto Acústica em cada ator sonoro irreal.

![Os controlos de design de acústica unreal Editor](media/demo-scene-sound-source-design-controls.png)

Se o multiplicador de **oclusão** for superior a 1 (o padrão é 1), a oclusão é exagerada. Um ajuste inferior a 1 torna o efeito de oclusão mais subtil.

Para ativar a transmissão através da parede, desloque o deslize de **transmissão (dB)** para longe do seu nível mais baixo.

### <a name="modify-wetness-for-a-source"></a>Modificar a molhada para uma fonte
Para alterar a rapidez com que a molhada muda com a distância, utilize a Dobra da **Distância Percetual**. Projeto Acústica calcula níveis húmidos em todo o espaço através de simulação. Os níveis variam suavemente com a distância e fornecem pistas de distância percetuais. Para exagerar este efeito, aumente os níveis húmidos relacionados com a distância para aumentar a dobra à distância. Os valores de distorção de menos de 1 tornam a reverberação baseada na distância mais subtil. Também pode fazer ajustes mais finos para este efeito através da definição **wetness (dB).**

Para aumentar o tempo de decomposição em todo o espaço, ajuste a **Escala de Tempo de Decadência**. Considere um caso em que o resultado da simulação é um tempo de decomposição de 1,5 segundos. A definição da escala de tempo de **deterioração** para 2 resulta num tempo de decomposição de 3 segundos aplicado à fonte.

### <a name="modify-distance-based-attenuation"></a>Modificar a atenuação à distância
O Project Acoustics Wwise mixer plug-in respeita a atenuação por fonte baseada na distância que é incorporada em Wwise. Mudar esta curva altera o nível do caminho seco. O plug-in Project Acoustics ajustará o nível húmido para manter a mistura húmida/seca especificada pelos controlos de simulação e design.

![O painel de curva de atenuação Wwise mostrando atenuação indo para 0 antes do limite de simulação](media/demo-sounds-attenuation.png)

Project Acoustics computa numa caixa de "região de simulação" centrada em torno de cada localização simulada do jogador. Os ativos da acústica no pacote de amostras foram cozidos com um raio de região de simulação de 45 metros. Atenuações foram projetadas para cair a 0 antes de 45 metros. Embora esta queda não seja um requisito rigoroso, carrega a ressalva de que apenas a geometria a menos de 45 metros do ouvinte oclusa sons.

## <a name="next-steps"></a>Passos seguintes
* [Integre o plug-in do Project Acoustics](unreal-integration.md) no seu projeto Unreal.
* [Crie uma conta Azure](create-azure-account.md) para os seus próprios bolos.
