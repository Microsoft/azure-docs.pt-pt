---
title: Integração do projeto acústica inreal e WWise
titlesuffix: Azure Cognitive Services
description: Este artigo descreve a integração do projeto acústica os plug-ins WWise e inreais em seu projeto.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243049"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Integração do projeto acústica inreal e WWise
Este artigo descreve como integrar o pacote de plug-ins do projeto acústicos ao seu projeto de jogo inreal e WWise existente.

Requisitos de software:
* 4\.20 de [mecanismo inreal](https://www.unrealengine.com/) +
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018,1
* [Plug-in WWise para inreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Se você estiver usando uma integração direta do SDK do WWise em vez do plug-in do WWise inreal, consulte o plug-in acústica inreal do projeto e ajuste as chamadas à API do WWise.

Para usar o projeto acústicos com um mecanismo de áudio diferente de WWise, faça uma solicitação de aprimoramento no [Fórum de discussão acústica do projeto](https://github.com/microsoft/ProjectAcoustics/issues). Você pode usar o plug-in acústico inreal do projeto para consultar dados acústicos e fazer chamadas à API para seu mecanismo.

## <a name="download-project-acoustics"></a>Baixar acústicas do projeto
Baixe o [projeto acústica o pacote de plug-ins não real e WWise](https://www.microsoft.com/download/details.aspx?id=58090) , se ainda não tiver feito isso.

Um plug-in de mecanismo não real e um plug-in do mixer WWise estão incluídos no pacote. O plug-in inreal fornece integração de editor e tempo de execução. Durante o jogo, o plug-in do projeto acústicos não reais computa parâmetros como oclusão para cada objeto de jogo para cada quadro. Esses parâmetros são convertidos em chamadas à API WWise.

## <a name="integration-steps"></a>Etapas de integração

Siga estas etapas para instalar o pacote e implantá-lo em seu jogo.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>Instalar o plug-in do mixer de acústica do projeto
1. Abra o iniciador WWise. Na guia **plug-ins** , em **instalar novos plug-ins**, selecione **Adicionar do diretório**.

    ![Instalar um plug-in no iniciador do WWise](media/wwise-install-new-plugin.png)

1. Selecione o diretório *AcousticsWwisePlugin\ProjectAcoustics* que está no pacote de download. Ele contém o pacote de plug-in do mixer WWise.

   O WWise instalará o plug-in do. Os acústicos do projeto devem aparecer na lista de plug-ins instalados em WWise.  
![A lista de plug-ins instalados do WWise após a instalação acústica do projeto](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>Desimplantar WWise em seu jogo
Reimplante o WWise em seu jogo mesmo se você já tiver integrado o WWise. Esta etapa integra o plug-in WWise do projeto acústicos.

   > [!NOTE]
   > **Plug-in de mecanismo:** Se você tiver o WWise instalado como um plug-in de jogo em um C++ projeto inreal, ignore esta etapa. Se ele estiver instalado como um plug-in de mecanismo, por exemplo, porque seu projeto inreal é apenas Blueprint, a implantação do WWise com nosso plug-in do mixer é mais complexa. Crie um projeto não real C++ fictício e vazio. Feche o editor inreal se ele abrir e siga o procedimento restante para implantar o WWise no projeto fictício. Em seguida, copie o plug-in WWise implantado.
 
1. No iniciador do WWise, selecione a guia **mecanismo inreal** . Selecione o menu ""/. "(ícone) ao lado de **projetos de mecanismo não reais recentes** e selecione **procurar projeto**. Abra o arquivo inreal Project *. Project* de seu jogo.

    ![A guia inreal do iniciador WWise](media/wwise-unreal-tab.png)

1. Selecione **integrar WWise no projeto** ou **Modificar WWise no projeto**. Esta etapa integra os binários do WWise ao seu projeto, incluindo o plug-in do mixer de acústica do projeto.

   > [!NOTE]
   > **Plug-in de mecanismo:** Se você estiver usando o WWise como um plug-in de mecanismo e tiver criado o projeto fictício conforme descrito anteriormente, copie a pasta que WWise implantou: *[DummyUProject] \Plugins\Wwise*. Cole-o sobre *[UESource] \Engine\Plugins\Wwise*. *[DummyUProject]* é o caminho de projeto C++ inreal vazio e *[UESource]* é onde as fontes de mecanismo não reais são instaladas. Depois de copiar a pasta, você pode excluir o projeto fictício.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>Adicionar o plug-in do projeto acústicos inreals ao jogo
 
1. Copie a pasta *Unreal\ProjectAcoustics* no pacote de plug-ins. Crie uma nova pasta *[UProjectDir] \Plugins\ProjectAcoustics*, em que *[UProjectDir]* é a pasta do projeto do jogo que contém o arquivo *. uproject* .

   > [!NOTE]
   > **Plug-in de mecanismo**: se você estiver usando o WWise como um plug-in de mecanismo, deverá usar o projeto acústicos como um plug-in de mecanismo não real também. Em vez do diretório de destino citado anteriormente, use *[UESource] \Engine\Plugins\ProjectAcoustics*.

1. Confirme que você vê uma pasta *WWise* junto com a pasta *ProjectAcoustics* . Ele contém o plug-in WWise junto com binários para o plug-in do mixer que você implantou anteriormente.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Estender a funcionalidade de plug-in WWise inreal
O plug-in do projeto acústica não real requer comportamento adicional exposto da API de plug-in WWise inreal por [essas diretrizes](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Incluímos um arquivo em lotes para automatizar o procedimento de aplicação de patch.

* Dentro de *Plugins\ProjectAcoustics\Resources*, execute *PatchWwise. bat*. A imagem de exemplo a seguir usa nosso projeto de exemplo AcousticsGame.

    ![Uma janela do Windows Explorer com o script para o patch WWise realçado](media/patch-wwise-script.png)

* Se você não tiver o SDK do DirectX instalado: dependendo da versão do WWise que você está usando, talvez seja necessário comentar a linha que contém `DXSDK_DIR` em *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![O editor de código que mostra ' DXSDK ' comentou](media/directx-sdk-comment.png)

* Se você compilar usando o Visual Studio 2019: para contornar um erro de vinculação com o WWise, altere manualmente o valor padrão de `VSVersion` em *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* para **vc150**:

    ![O editor de código que mostra "VSVersion" foi alterado para "vc150"](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>Compile o jogo e verifique se o Python está habilitado

1. Compile seu jogo e certifique-se de que ele seja criado corretamente. Se não for criado, verifique as etapas anteriores cuidadosamente antes de continuar.

1. Abra seu projeto no editor inreal.

    > [!NOTE]
    > **Plug-in de mecanismo:** Se você estiver usando o ProjectAcoustics como um plug-in de mecanismo, verifique também se ele está habilitado em plug-ins "internos".

    Você deverá ver um novo modo, que indica que os acústicos do projeto foram integrados.

    ![Modo acústicos completo em não real](media/acoustics-mode-full.png)

1. Confirme se o plug-in do Python para inreal está habilitado para que a integração do editor funcione corretamente.

    ![As extensões do Python no editor inreal está habilitada](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>Configurar seu projeto WWise para usar acústicas de projeto

Um projeto WWise de exemplo está incluído no download de exemplos. É recomendável exibi-lo junto com estas instruções. As capturas de tela mais adiante neste artigo são deste projeto.

#### <a name="bus-setup"></a>Configuração do barramento
O plug-in do projeto acústica não real procurará o plug-in do mixer associado em um barramento com o nome exato `Project Acoustics Bus`. Crie um novo barramento de áudio com esse mesmo nome. O plug-in do mixer pode funcionar em várias configurações. Mas, por enquanto, presumimos que ele será usado apenas para o processamento de reverberação. Esse barramento carregará o sinal de reverberação misto para todas as fontes que usam acústicos. Ele pode misturar upstream em qualquer estrutura de mixagem de barramento. Um exemplo é mostrado aqui do projeto de exemplo WWise que está incluído no download de exemplo.

![Barramentos Wwises mostrando o barramento acústicos do projeto](media/acoustics-bus.png)

1. Defina a configuração de canal no barramento como *1,0*, *2,0*, *4,0*, *5,1*ou *7,1*. Qualquer outra configuração resultará em nenhuma saída no barramento.

    ![Opções de configuração de canal para o barramento acústicos do projeto](media/acoustics-bus-channel-config.png)

1. Vá para os detalhes do barramento acústica do projeto e verifique se você pode ver a guia **plug-in do mixer** .

    ![WWise com a guia plug-in do mixer para o barramento acústicos do projeto habilitado](media/mixer-tab-enable.png)

1. Vá para a guia **plug-in do mixer** e adicione o plug-in do mixer de acústica do projeto ao barramento.

    ![Diagrama de como adicionar o plug-in do mixer de acústica do projeto ao barramento WWise](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>Ator-configuração da hierarquia do mixer
Para obter o melhor desempenho, o projeto acústica aplica o processamento de sinal digital de áudio a todas as fontes simultaneamente. Portanto, o plug-in deve operar como um plug-in de mixer. O WWise exige que plug-ins de mixer estejam no barramento de saída, embora o barramento de saída geralmente carregue o sinal de saída seca. O projeto acústicos requer que o sinal seco seja roteado por meio de barramentos auxiliares, enquanto o sinal molhado é executado no `Project Acoustics Bus`. O processo a seguir dá suporte à migração gradual para esse fluxo de sinal.

Digamos que você tenha um projeto existente com uma hierarquia ator-mixer que contenha *trilha*, *armas*e outras no nível superior. Cada um tem um barramento de saída correspondente para sua mistura seca. Digamos que você deseja migrar trilha para usar acústicos. Primeiro, crie um barramento auxiliar correspondente para transportar o submix seco que é filho do barramento de saída trilha. Por exemplo, usamos um prefixo "seco" na imagem a seguir para organizar os barramentos, embora o nome exato não seja importante. Os medidores ou efeitos que você tinha no barramento trilha ainda funcionarão como antes.

![Configuração de mixagem seca WWise recomendada](media/wwise-dry-mix-setup.png)

Em seguida, modifique a estrutura de saída do barramento do trilha actor-mixer da seguinte maneira, com o conjunto de *barramento acústicos do projeto* definido como o **barramento de saída**e *Dry_Footsteps* definido como um barramento auxiliar definido pelo usuário.

![Configuração recomendada do barramento do mixer de ator WWise](media/actor-mixer-bus-settings.png)

Agora todos os trilhas obtêm o tratamento acústico e geram seus reverberadores no barramento acústico do projeto. O sinal seco é roteado por meio de Dry_Footsteps e espacial como de costume.

Os acústicos do projeto só se aplicam a sons que têm um local 3D no mundo. Após a [documentação do WWise](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), as propriedades de posicionamento devem ser definidas conforme mostrado. A configuração de **espacial 3D** pode ser uma *posição* ou *posição + orientação* conforme necessário.

![Configurações de posicionamento de ator WWise recomendadas](media/wwise-positioning.png)

Não é possível definir o **barramento de saída** para outro barramento que combina upstream no *barramento acústico do projeto*. O WWise impõe esse requisito nos plug-ins do mixer.

Se você quiser que um filho na hierarquia de ator-mixer de trilha não use acústica, você poderá usar "substituir pai" para desfazê-lo.

Se você estiver usando envios definidos por jogos ou definidos pelo usuário para reverberação em qualquer mixer de ator no jogo, desative-os nesse mixer de ator para evitar a aplicação de um reverberador duas vezes.

#### <a name="spatialization"></a>Espacialização
O plug-in do mixer do WWise do projeto acústica aplica o reverberação de convolução por padrão, deixando o WWise de fazer a espacial panorâmica. Quando você usa o projeto acústicos nessa configuração de somente reverberação padrão, você pode usar qualquer método de configuração de canal e de espacial em sua mistura seca. Portanto, você pode misturar e corresponder quase qualquer spatializer com o reverberador acústicos do projeto. Suas opções incluem Binaural spatializers e [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound) [baseados em Ambisonics](https://www.audiokinetic.com/products/ambisonics-in-wwise/) .
 
O projeto acústicos inclui um spatializer opcional que dá suporte à renderização de HRTF de alta resolução e à visão panorâmica com base em objeto. Marque a caixa de seleção **executar a espacial** nas configurações de plug-in do mixer e escolha entre *HRTF* ou *panorâmica*. Além disso, desabilite os envios de aux definidos pelo usuário para todos os barramentos de secabilidade para evitar a espacial duas vezes pelo plug-in do mixer de acústica do projeto e WWise. O modo de espacialização não pode ser alterado em tempo real porque requer uma nova geração bancária de som. Reinicie o inreal e, em seguida, gere novamente o soundbanks antes de selecionar reproduzir para integrar as alterações de configuração de plug-in do mixer, como a configuração da caixa de seleção **executar a espacial** .

![Configurações de espacial do plug-in do mixer WWise](media/mixer-spatial-settings.png)

Infelizmente, não há suporte atualmente para outros plug-ins spatializer baseados em objeto. Eles são implementados como plug-ins do mixer, e WWise não permite que vários plug-ins de mixer sejam atribuídos a um único mixer de ator.  

### <a name="audio-setup-in-unreal"></a>Configuração de áudio em não real
1. Primeiro, você precisa inserir seu nível de jogo para produzir um ativo acústico, que será colocado em *Content\Acoustics*. Consulte o [tutorial de Cotortas inreal](unreal-baking.md). Alguns níveis inclusas estão incluídos no pacote de exemplo.

1. Crie um ator de espaço acústica em sua cena. Crie apenas um desses atores em um nível, pois ele representa os acústicos para o nível inteiro.

    ![Criação de um ator de espaço acústicos no editor inreal](media/create-acoustics-space.png)

1. Atribua o ativo de dados acústicos inclusas ao slot de dados acústicos no ator do espaço acústica. Sua cena agora tem acústicas!

    ![Atribuição de ativo acústicos no editor inreal](media/acoustics-asset-assign.png)

1. Adicione um ator vazio. Configure-o da seguinte maneira.

    ![O editor inreal mostra o uso de componentes acústicos em um ator vazio](media/acoustics-component-usage.png)

       
    <sup>1</sup> adicione um componente de áudio acústico ao ator. Esse componente adiciona a funcionalidade acústica do projeto ao componente de áudio WWise.
        
    <sup>2</sup> a caixa **reproduzir em Iniciar** é marcada por padrão. Essa configuração dispara um evento WWise associado na inicialização de nível.</li>
         
    <sup>3</sup> use a caixa de seleção **Mostrar parâmetros acústicos** para imprimir informações de depuração na tela sobre a origem.

    ![O painel acústicos do editor inreal na fonte de som com valores de depuração habilitados](media/debug-values.png)

    <sup>4</sup> atribua um evento WWise de acordo com o fluxo de trabalho WWise usual.
       
    <sup>5</sup> Verifique se a opção **usar áudio espacial** está desativada. Se você usar acústicas de projeto para um componente de áudio específico, não poderá usar simultaneamente o mecanismo de áudio espacial do WWise para acústicas.</li>
       
Está tudo pronto. Mova-se para a cena e explore os efeitos acústicos!

## <a name="next-steps"></a>Passos seguintes
* Experimente o [tutorial de design do projeto acústica não real/WWise](unreal-workflow.md).
* Saiba [como fazer prepara](unreal-baking.md) para seus bastidores de jogos.
