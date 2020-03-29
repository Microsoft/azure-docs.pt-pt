---
title: Projeto Acústica Integração Irreal e Wwise
titlesuffix: Azure Cognitive Services
description: Este artigo descreve a integração dos plug-ins Do Projeto Acústica Irreal e Wwise no seu projeto.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: e57212a3002390754aaebc5f2aa9ffb10af230a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243049"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Projeto Acústica Integração Irreal e Wwise
Este artigo descreve como integrar o pacote plug-in project Acoustics no seu projeto de jogo Unreal e Wwise existente.

Requisitos de software:
* [Motor Irreal](https://www.unrealengine.com/) 4.20+
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1
* [Wwise plug-in para Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Se estiver a utilizar uma integração direta do Wwise SDK em vez do plug-in Wwise Unreal, consulte o plug-in Unreal do Projeto Acoustics Unreal e ajuste as chamadas Wwise API.

Para utilizar o Project Acoustics com um motor de áudio que não seja wwise, faça um pedido de melhoria no fórum de [discussão](https://github.com/microsoft/ProjectAcoustics/issues)do Projeto Acústica . Pode utilizar o plug-in Do Projeto Acústica Unreal para consultar dados de acústica e fazer chamadas aPI para o seu motor.

## <a name="download-project-acoustics"></a>Baixar Projeto Acústica
Descarregue o [pacote plug-in Project Acoustics Unreal e Wwise](https://www.microsoft.com/download/details.aspx?id=58090) se ainda não o fez.

Um plug-in unreal engine e um plug-in wwise mixer estão incluídos na embalagem. O plug-in Unreal fornece ao editor e à integração em tempo de execução. Durante a jogabilidade, o Project Acoustics Unreal computação plug-in parâmetros tais como oclusão para cada objeto de jogo para cada quadro. Estes parâmetros são traduzidos em chamadas Wwise API.

## <a name="integration-steps"></a>Etapas de integração

Siga estes passos para instalar a embalagem e implementá-la no seu jogo.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>Instale o plug-in da batedeira Project Acoustics
1. Abra o lançador Wwise. No separador **Plug-ins,** em **instalação de novos plug-ins,** selecione **Adicionar a partir do Diretório**.

    ![Instale um plug-in no lançador Wwise](media/wwise-install-new-plugin.png)

1. Selecione o diretório *AcousticsWwisePlugin\ProjectAcoustics* que está no pacote de descarregamento. Contém o pacote plug-in da misturadora Wwise.

   Wwise vai instalar o plug-in. O Projeto Acústica deve aparecer na lista de plug-ins instalados em Wwise.  
![A lista de plug-ins da Wwise instalada após a instalação do Project Acoustics](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>Descoloque Wwise no seu jogo
Recoloque wwise no seu jogo mesmo que já tenha integrado Wwise. Este passo integra o project Acoustics Wwise plug-in.

   > [!NOTE]
   > **Plug-in do motor:** Se tiver o Wwise instalado como um plug-in de jogo num projeto Unreal C++, ignore este passo. Se for instalado como um plug-in do motor, por exemplo porque o seu projeto Unreal é apenas Blueprint, a implementação wwise com o nosso plug-in misturador é mais complexo. Crie um projeto Unreal C++ vazio. Feche o editor da Unreal se abrir e siga o procedimento restante para implementar Wwise no projeto falso. Em seguida, copie o plug-in Wwise implantado.
 
1. A partir do lançador Wwise, selecione o separador **Unreal Engine.** Selecione o menu "hambúrguer" (ícone) ao lado de **Recentes Projetos de Motores Irreais** e, em seguida, selecione **Browse para projeto**. Abra o ficheiro do projeto Irreal do seu *jogo.projeto.*

    ![O separador Wwise Launcher Unreal](media/wwise-unreal-tab.png)

1. Selecione **Integrar wwise no Projeto** ou modificar **Wwise no Projeto**. Este passo integra binários Wwise no seu projeto, incluindo o plug-in da misturadora Project Acoustics.

   > [!NOTE]
   > **Plug-in do motor:** Se estiver a usar o Wwise como plug-in do motor e tiver criado o projeto de manequim como descrito anteriormente, copie a pasta que wwise implementou: *[DummyUProject]\Plugins\Wwise*. Colá-lo sobre *[UESource]\Motor\Plugins\Wwise*. *[DummyUProject]* é o caminho vazio do projeto Unreal C++ e *[UESource]* é onde as fontes do Motor Irreal são instaladas. Depois de copiar a pasta, pode eliminar o projeto de boneco.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>Adicione o plug-in unreal do Projeto Acústica ao seu jogo
 
1. Copie a pasta *Unreal\ProjectAcoustics* no pacote plug-in. Crie uma nova pasta *[UProjectDir]\Plugins\ProjectAcoustics,* onde *[UProjectDir]* é a pasta do projeto do seu jogo que contém o ficheiro *.uproject.*

   > [!NOTE]
   > **Plug-in**do motor : Se estiver a utilizar o Wwise como plug-in do motor, deve utilizar o Project Acoustics como plug-in do motor Unreal. Em vez do diretório de destino citado anteriormente, use *[UESource]\Engine\Plugins\ProjectAcoustics*.

1. Confirme que vê uma pasta *Wwise* ao lado da pasta *ProjectAcoustics.* Contém o plug-in Wwise juntamente com binários para o plug-in misturador que implementou anteriormente.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Alargar a funcionalidade de plug-in Wwise Unreal
O plug-in Project Acoustics Unreal requer comportamentos adicionais expostos a partir da Wwise Unreal plug-in API por [estas diretrizes](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Incluímos um ficheiro de lote para automatizar o procedimento de remendo.

* Inside *Plugins\ProjectAcoustics\Resources,* executar *PatchWwise.bat*. A imagem de exemplo seguinte utiliza o nosso projeto de amostra AcústicaGame.

    ![Uma janela do Windows Explorer com o script para remendar Wwise em destaque](media/patch-wwise-script.png)

* Se não tiver o DirectX SDK instalado: Dependendo da versão da Wwise que está a usar, `DXSDK_DIR` poderá ter de comentar a linha que contém no *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![O editor de código que mostra 'DXSDK' comentou](media/directx-sdk-comment.png)

* Se compilar utilizando o Visual Studio 2019: Para contornar um erro de `VSVersion` ligação com wwise, altere manualmente o valor padrão em *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* para **vc150**:

    ![O editor de código que mostra "VSVersion" mudou para "vc150"](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>Construa o jogo e verifique se Python está ativado

1. Compila o teu jogo e certifica-te que ele constrói corretamente. Se não for construído, verifique cuidadosamente os passos anteriores antes de continuar.

1. Abra o seu projeto em Editor Irreal.

    > [!NOTE]
    > **Plug-in do motor:** Se estiver a utilizar o ProjectAcoustics como plug-in do motor, certifique-se também de que está ativado em plug-ins "incorporados".

    Deve ver um novo modo, que indica que o Project Acoustics foi integrado.

    ![Modo acústico cheio em Irreal](media/acoustics-mode-full.png)

1. Confirme que o plug-in Python para Unreal está ativado de modo a que a integração do editor funcione corretamente.

    ![As extensões Python no editor irreal habilitado](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>Configurar o seu projeto Wwise para usar o Project Acoustics

Um exemplo do projeto Wwise está incluído no download de amostras. Recomendamos que o veja juntamente com estas instruções. As imagens mais tarde neste artigo são deste projeto.

#### <a name="bus-setup"></a>Configuração de ônibus
O plug-in Project Acoustics Unreal procurará o plug-in da batedeira `Project Acoustics Bus`associada num autocarro que tenha o nome exato . Crie um novo autocarro áudio com o mesmo nome. O plug-in da batedeira pode funcionar em várias configurações. Mas por agora, assumimos que será usado apenas para o processamento de reverberação. Este ônibus transportará o sinal de reverberação mista para todas as fontes que usam acústica. Pode misturar-se a montante em qualquer estrutura de mistura de autocarros. Um exemplo é mostrado aqui a partir do projeto de amostra Wwise que está incluído no download da amostra.

![Autocarros Wwise mostrando ônibus do Projeto Acoustics](media/acoustics-bus.png)

1. Detete a configuração do canal no autocarro para *1.0*, *2.0*, *4.0*, *5.1*ou *7.1*. Qualquer outra definição não resultará em saída sem saída no autocarro.

    ![Opções de config do canal para project Acoustics Bus](media/acoustics-bus-channel-config.png)

1. Entre nos detalhes do Project Acoustics Bus e certifique-se de que pode ver o separador **Mixer Plug-in.**

    ![Wwise com o separador Mixer Plug-in para o Project Acoustics Bus habilitado](media/mixer-tab-enable.png)

1. Vá ao separador **Mixer Plug-in** e adicione o plug-in da mixer acústica do projeto ao autocarro.

    ![Diagrama de como adicionar o Project Acoustics Mixer Plug-in ao autocarro Wwise](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>Configuração da hierarquia do actor-misturador
Para um melhor desempenho, o Project Acoustics aplica o processamento de sinal digital áudio a todas as fontes simultaneamente. Assim, o plug-in deve funcionar como um plug-in misturador. Wwise requer plug-ins misturadores no ônibus de saída, embora o ônibus de saída geralmente transporte o sinal de saída seco. O Projeto Acústica requer que o sinal seco seja encaminhado através de autocarros aux, enquanto o sinal molhado é transportado no `Project Acoustics Bus`. O processo seguinte apoia a migração gradual para este fluxo de sinal.

Digamos que tem um projeto existente com uma hierarquia actor-mixer que contém *passos, armas,* e outros ao mais alto nível. *footsteps* Cada um tem um autocarro de saída correspondente para a sua mistura seca. Digamos que quer migrar passos para usar acústica. Primeiro, crie um autocarro aux correspondente para transportar o submix seco que é uma criança do ônibus de saída de passos. Por exemplo, usamos um prefixo "Dry" na seguinte imagem para organizar os autocarros, embora o nome exato não seja importante. Quaisquer metros ou efeitos que tenha sofrido no autocarro de passos ainda funcionarão como antes.

![Configuração recomendada de mistura seca Wwise](media/wwise-dry-mix-setup.png)

Em seguida, modifique a estrutura de saída do autocarro para o actor-mixer footsteps da seguinte forma, com *project Acoustics Bus* definido como o **Output Bus**, e *Dry_Footsteps* definido como um autocarro aux definido pelo utilizador.

![Configuração de ônibus de mixer de ator Wwise recomendado](media/actor-mixer-bus-settings.png)

Agora todos os passos recebem tratamento de acústica e produzem o seu reverso no Project Acoustics Bus. O sinal seco é encaminhado através de Dry_Footsteps e espacializado como de costume.

O Projeto Acústica só se aplica a sons que tenham uma localização 3D no mundo. Seguindo a [documentação wwise,](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)as propriedades de posicionamento devem ser definidas como mostrado. A definição **3D de espacialização** pode ser *posição* ou *posição + Orientação* conforme necessário.

![Definições de posicionamento recomendadas do Ator Wwise](media/wwise-positioning.png)

Não é possível definir o **Output Bus** para outro autocarro que se mistura a montante em *Project Acoustics Bus*. Wwise impõe este requisito aos plug-ins misturadores.

Se quiser que uma criança na hierarquia de actor-mixer não use acústica, pode usar "overover parent" nela para optá-la.

Se estiver a usar mensagens definidas pelo jogo ou definidas pelo utilizador para reverência em qualquer actor-mixer do jogo, desligue-os do actor-mixer para evitar aplicar o reverbo duas vezes.

#### <a name="spatialization"></a>Espacialização
O plug-in da misturadora Project Acoustics Wwise aplica reverberação de convolução por padrão, deixando Wwise a fazer a espacialização. Quando utilizar o Project Acoustics nesta configuração padrão apenas reverb, pode utilizar qualquer método de configuração e espacialização do canal na sua mistura seca. Assim, pode misturar e combinar quase qualquer espacializador com o reverso do Projeto Acústica. As suas opções incluem [espacializadores binaural baseados em Ambisonics](https://www.audiokinetic.com/products/ambisonics-in-wwise/) e [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
O Project Acoustics inclui um espacializador opcional que suporta a renderização hrtf de alta resolução baseada em objetos e panorâmica. Selecione a caixa de **verificação de spaceização executar** nas definições de plug-in da batedeira e escolha entre *HRTF* ou *Panning*. Além disso, desative o aux definido pelo utilizador envia para todos os autocarros secos para evitar ser espacializado duas vezes pelo plug-in da misturadora Project Acoustics e wwise. O modo de espacialização não pode ser alterado em tempo real porque requer uma regeneração de banco sonoro. Reinicie o Unreal e, em seguida, regenerar os bancos de som antes de selecionar o reprodução para integrar alterações de config plug-in mixer, como a definição de caixa de **verificação perform spatialização.**

![Definições de espacialização plug-in do Misturador Wwise](media/mixer-spatial-settings.png)

Infelizmente, outros plug-ins espaciais baseados em objetos não são suportados atualmente. São implementados como plug-ins misturadores, e Wwise não permite que vários plug-ins de mixer sejam atribuídos a um único actor-mixer.  

### <a name="audio-setup-in-unreal"></a>Configuração de áudio em Unreal
1. Primeiro, tens de assar o teu nível de jogo para produzir um ativo de acústica, que será colocado em *Content\Acoustics*. Consulte o [Tutorial de Cozedura Irreal.](unreal-baking.md) Alguns níveis pré-cozidos estão incluídos no pacote de amostras.

1. Crie um ator espacial da Acústica na sua cena. Só crie um destes atores a um nível, porque representa a acústica para todo o nível.

    ![Criação de um ator espacial da Acústica no editor irreal](media/create-acoustics-space.png)

1. Atribuir o ativo de dados acústicos assados à ranhura de dados da Acústica no ator espacial da Acústica. A tua cena agora tem acústica!

    ![Atribuição de ativos de acústica no editor irreal](media/acoustics-asset-assign.png)

1. Adicione um ator vazio. Configure-o da seguinte forma.

    ![O editor irreal mostra o uso de componentes da Acústica num ator vazio](media/acoustics-component-usage.png)

       
    <sup>1</sup> Adicione um componente áudio acústico ao ator. Este componente adiciona a funcionalidade Do Projeto Acústica ao componente áudio Wwise.
        
    <sup>2</sup> A caixa **de reprodução no início** é selecionada por defeito. Esta definição desencadeia um evento Wwise associado a nível de arranque.</li>
         
    <sup>3</sup> Utilize a caixa de verificação de **parâmetros** de acústica para imprimir informações sobre o depurado no ecrã sobre a fonte.

    ![O painel De acústica do editor irreal sobre a fonte sonora com valores de depuração habilitados](media/debug-values.png)

    <sup>4</sup> Atribuir um evento Wwise de acordo com o habitual fluxo de trabalho Wwise.
       
    <sup>5</sup> Certifique-se de que **a utilização** de áudio espacial está desligada. Se utilizar o Project Acoustics para um determinado componente áudio, não pode utilizar simultaneamente o motor Áudio Espacial da Wwise para acústica.</li>
       
Está tudo pronto. Mova-se ao redor da cena e explore os efeitos acústicos!

## <a name="next-steps"></a>Passos seguintes
* Experimente o [Project Acoustics Unreal/Wwise Design Tutorial](unreal-workflow.md).
* Aprenda [a fazer bolos](unreal-baking.md) para as suas cenas de jogo.
