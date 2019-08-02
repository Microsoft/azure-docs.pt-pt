---
title: Integração do projeto acústica inreal e WWise
titlesuffix: Azure Cognitive Services
description: Este "como" descreve a integração do projeto acústica os plug-ins inreais e inWwises em seu projeto.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 5511dd6b9a7d77c0988a94fef747a30d25bb4fc3
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706631"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Integração do projeto acústica inreal e WWise
Este "como" fornece etapas de integração detalhadas do pacote de plug-ins acústicos do projeto em seu projeto de jogo inreal e WWise existente. 

Requisitos de software:
* [Mecanismo inreal](https://www.unrealengine.com/) 4,20 ou 4,21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018,1.\*
* [Plug-in WWise para inreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Se você estiver usando uma integração direta do SDK do WWise em vez de usar os plug-ins inreals do WWise, consulte o projeto acústica o plugin inreal e ajuste as chamadas à API do WWise.

Se você quiser usar acústicas de projeto com um mecanismo de áudio diferente de WWise, faça uma solicitação de aprimoramento no [Fórum de discussão acústica do projeto](https://github.com/microsoft/ProjectAcoustics/issues). Você pode usar o plug-in acústico inreal do projeto para consultar dados acústicos e, em seguida, fazer chamadas à API para seu mecanismo.

## <a name="download-project-acoustics"></a>Baixar acústicas do projeto
Se você ainda não fez isso, baixe o [projeto acústica inreal & pacote de plug-in WWise](https://www.microsoft.com/download/details.aspx?id=58090)). 

Incluímos um plug-in de mecanismo não real e um plug-in de mixer WWise no pacote. O plug-in inreal fornece integração de editor e tempo de execução. Durante o jogo, o projeto acústica o plugin não real computa parâmetros como oclusão para cada objeto de jogo em cada quadro. Esses parâmetros são convertidos em chamadas à API WWise.

## <a name="review-integration-steps"></a>Examinar as etapas de integração

Há estas etapas principais para instalar o pacote e implantá-lo em seu jogo.
1. Instalar o plug-in do mixer do WWise do projeto acústicos
2. (Re) implante o WWise em seu jogo. Esta etapa propaga o plug-in do mixer para seu projeto de jogo.
3. Adicionar o plug-in acústico inreal do projeto ao seu jogo
4. Estender a funcionalidade de plug-in inreal do WWise
5. Criar jogo e verificar se o Python está habilitado
6. Configurar seu projeto WWise para usar acústicas de projeto
7. Configuração de áudio em não real

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1. Instalar o plugin de mixer de acústica do projeto
* Abra o iniciador do WWise e, na guia **plug-ins** , em **instalar novos plug-ins**, selecione **Adicionar do diretório**. 

    ![Captura de tela da instalação de um plug-in no iniciador WWise](media/wwise-install-new-plugin.png)

* Escolha o `AcousticsWwisePlugin\ProjectAcoustics` diretório que foi incluído no pacote que você baixou. Ele contém o pacote de plug-in do mixer WWise.

* O WWise instalará o plug-in. Os acústicos do projeto agora devem aparecer na lista plug-ins instalados no WWise.
![Captura de tela da lista de plugins instalados do WWise após a instalação acústica do projeto](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2. (Re) implante o WWise em seu jogo
Reimplante o WWise em seu jogo mesmo se você já tiver integrado o WWise. Isso escolhe o plug-in de WWise do projeto acústicos.

* **Plug-in do mecanismo:** Se você tiver o WWise instalado como um plug-in de jogo C++ em um projeto inreal, ignore esta etapa. Se ele estiver instalado como um plug-in de mecanismo, por exemplo, porque seu projeto inreal é apenas Blueprint, a implantação WWise com nosso plug-in de Mixer é mais complexa. Crie um projeto não real C++ fictício e vazio, feche-o se o editor inreal for aberto e siga o procedimento restante para implantar o WWise nesse projeto fictício. Em seguida, copie o plug-in WWise implantado.
 
* No iniciador do WWise, clique na guia **mecanismo inreal** e, em seguida, clique no menu de documentos ao lado de **projetos de mecanismo não reais recentes** e selecione **procurar projeto**. Abra o arquivo de projeto `.uproject` inreal do jogo.

    ![Captura de tela da guia não real do WWise Launcher](media/wwise-unreal-tab.png)

* Em seguida, clique em **integrar WWise no projeto** ou **Modificar WWise no projeto**. Esta etapa (re) integra os binários do WWise ao seu projeto, agora incluindo o plug-in de mixer do projeto acústicos.

* **Plug-in do mecanismo:** Se você estiver usando WWise como um plug-in de mecanismo e criou o projeto fictício como acima, copie a pasta `[DummyUProject]\Plugins\Wwise` WWise implantada `[UESource]\Engine\Plugins\Wwise`: e cole-a. `[DummyUProject]`é o caminho de projeto C++ inreal vazio e `[UESource]` é onde você tem as fontes de mecanismo não reais instaladas. Quando terminar de copiar, você poderá excluir o projeto fictício.

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3. Adicionar o plug-in acústico inreal do projeto ao seu jogo
 
* Copie a `Unreal\ProjectAcoustics` pasta no pacote de plug-in e crie uma `[UProjectDir]\Plugins\ProjectAcoustics`nova pasta `UProjectDir` , em que é a pasta do projeto `.uproject` do jogo que contém o arquivo.
  * **Plug-in do mecanismo**: Se você estiver usando o WWise como um plug-in de mecanismo, deverá usar o projeto acústicos como um plug-in de mecanismo não real também. Em vez do diretório de destino acima, use `[UESource]\Engine\Plugins\ProjectAcoustics`:.

* Confirme que você vê `Wwise` uma pasta ao `ProjectAcoustics` lado da pasta. Ele contém o plug-in WWise juntamente com binários para o plug-in do mixer que você (renovo) implantou na etapa 2 acima.

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4. Estender a funcionalidade de plug-in inreal do WWise
* O plug-in acústico inreal do projeto requer um comportamento adicional exposto da API de plug-in WWise inreal por [essas diretrizes](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Incluímos um arquivo em lotes para automatizar o procedimento de aplicação de patch. 
* Dentro `Plugins\ProjectAcoustics\Resources`do, `PatchWwise.bat`execute. A imagem de exemplo abaixo usa nosso projeto de exemplo AcousticsGame.

    ![Captura de tela da janela do Windows Explorer realçando o script fornecido para o patch WWise](media/patch-wwise-script.png)

* Se você não tiver o SDK do DirectX instalado, será necessário comentar a linha que contém DXSDK_DIR em`[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Captura de tela do editor de código mostrando DXSDK comentado](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5. Criar jogo e verificar se o Python está habilitado

* Compile seu jogo e verifique se ele foi criado corretamente. Caso contrário, verifique as etapas anteriores cuidadosamente antes de continuar. 
* Abra seu projeto no editor inreal. 
* **Plug-in do mecanismo:** Se estiver usando ProjectAcoustics como plug-in de mecanismo, verifique também se ele está habilitado, listado em plug-ins "internos".
* Você deve ver um novo modo, que indica que os acústicos do projeto foram integrados.

    ![Captura de tela de inreal mostrando o modo acústicos completo](media/acoustics-mode-full.png)

* Confirme se você tem o plug-in Python para não real habilitado. Isso é necessário para que a integração do editor funcione corretamente.

    ![Captura de tela de habilitação das extensões do Python no editor inreal](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6. Configuração do projeto WWise

Um projeto WWise de exemplo está incluído no download de exemplos. Recomendamos que você examine isso ao lado dessas instruções. As capturas de tela abaixo são tiradas deste projeto.

### <a name="bus-setup"></a>Configuração do barramento
* O plug-in acústico inreal do projeto procurará o plug-in do mixer associado em ****** um barramento com `Project Acoustics Bus`esse nome exato:. Crie um novo barramento de áudio com esse nome. O plug-in do mixer pode funcionar em várias configurações, mas, por enquanto, supomos que ele será usado para fazer apenas o processamento de reverberação. Esse barramento carregará o sinal de reverberação misto para todas as fontes que usam acústicos. Ele pode misturar upstream em qualquer estrutura de mixagem de barramento, um exemplo é mostrado abaixo, extraído do nosso projeto de exemplo WWise incluído no download de exemplo.

    ![Captura de tela dos ônibus WWise mostrando o barramento acústicos do projeto](media/acoustics-bus.png)

* A configuração de canal no barramento precisa ser definida como um de: `1.0, 2.0, 4.0, 5.1 or 7.1`. Outras configurações não resultarão em nenhuma saída nesse barramento.

    ![Captura de tela das opções de configuração de canal para o barramento acústicos do projeto](media/acoustics-bus-channel-config.png)

* Agora, vá para os detalhes do barramento acústicos do projeto e verifique se você pode ver a guia plug-in do mixer

    ![Captura de tela de WWise mostrando como habilitar a guia de plug-in do mixer para o barramento acústico do projeto](media/mixer-tab-enable.png)

* Em seguida, vá para a guia plug-in do mixer e adicione o plug-in do mixer de acústica do projeto ao barramento

    ![Screenshow do barramento WWise mostrando como adicionar o plug-in de mixer do projeto acústicos](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>Ator-configuração da hierarquia do mixer
* Por motivos de desempenho, o projeto acústica aplica o DSP de áudio a todas as fontes simultaneamente. Isso requer que o plug-in opere como um plug-in de mixer. O WWise exige que os plug-ins do mixer estejam no barramento de saída, embora o barramento de saída geralmente carregue o sinal de saída seca. Os acústicos do projeto exigem que o sinal seco seja roteado por meio de barramentos auxiliares `Project Acoustics Bus`enquanto o sinal molhado é executado no. O processo a seguir dá suporte à migração gradual para esse fluxo de sinal.

* Digamos que você tenha um projeto existente com uma hierarquia de ator-mixer contendo trilha, armas e outros no nível superior. Cada um tem um barramento de saída correspondente para sua mistura seca. Digamos que você deseja migrar trilha para usar acústicos. Primeiro, crie um barramento auxiliar correspondente para transportar seu submix seco que seja filho do barramento de saída trilha. Por exemplo, usamos um prefixo "seco" na imagem abaixo para organizá-los, embora o nome exato não seja importante. Os medidores ou efeitos que você tinha no barramento trilha ainda funcionarão como antes.

    ![Captura de tela de configuração de mixagem seca WWise recomendada](media/wwise-dry-mix-setup.png)

* Em seguida, modifique a estrutura de saída do barramento do trilha actor-mixer da seguinte maneira, com o conjunto de barramento acústicos do projeto definido como barramento de saída e Dry_Footsteps definido como um barramento auxiliar definido pelo usuário.

    ![Captura de tela da configuração recomendada do barramento do mixer de ator WWise](media/actor-mixer-bus-settings.png)

* Agora todos os trilhas obtêm o tratamento acústico e geram seus reverberadores no barramento acústico do projeto. O sinal seco é roteado por meio de Dry_Footsteps e espacial como de costume.

* Os acústicos do projeto só se aplicam a sons que têm um local 3D no mundo. Após a [documentação do WWise](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), as propriedades de posicionamento devem ser definidas conforme mostrado. A configuração "espacial 3D" pode ser "posição" ou "posição + orientação", conforme necessário.

    ![Captura de tela das configurações de posicionamento do ator WWise recomendado](media/wwise-positioning.png)

* Definir o barramento de saída para outro barramento que combina upstream no **barramento acústico do projeto** não funcionará. O WWise impõe esse requisito nos plug-ins do mixer.

* Se você quiser que um filho na hierarquia de ator-mixer de trilha não use acústicas, você sempre poderá usar "substituir pai" nele para recusá-lo.

* Se você estiver usando envios de jogos ou definidos pelo usuário para o reverberador de qualquer mixer de ator no jogo, desative-os nesse mixer de ator para evitar a aplicação do reverberador duas vezes.

### <a name="spatialization"></a>Espacialização
Por padrão, o plug-in de mixer do WWise do projeto acústica aplica o reverberação de convolução, deixando WWise para fazer a verba panorâmica. Ao usar acústicas de projeto nesta configuração de somente reverberação padrão, você está livre para usar qualquer configuração de canal e método de concessão em sua mistura seca, permitindo misturar e corresponder quase qualquer spatializer com o reverberador acústicos do projeto. Suas opções incluem Binaural spatializers e [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound) [baseados em Ambisonics](https://www.audiokinetic.com/products/ambisonics-in-wwise/) .
 
A acústica do projeto inclui um spatializer opcional que dá suporte à renderização HRTF de alta resolução baseada em objeto e à panorâmica. Marque a caixa de seleção "executar a espacial" nas configurações do plug-in do mixer e escolha entre HRTF ou panorâmica e desabilite os envios de aux definidos pelo usuário configurados acima para todos os ônibus seco para evitar a espacial duas vezes, tanto com o plug-in do mixer do projeto acústicos quanto com WWise. O modo de espacialização não pode ser alterado em tempo real, pois requer uma nova geração bancária de som. Você deve reiniciar inreal e, em seguida, regenerar soundbanks antes de pressionar reproduzir para obter as alterações de configuração do plug-in do mixer, como a caixa de seleção "executar a espacial".

![Captura de tela das configurações de Spatial do plugin do mixer WWise](media/mixer-spatial-settings.png)

Infelizmente, não há suporte para outros plug-ins spatializer baseados em objeto no momento em que são implementados como plug-ins de mixer, e WWise atualmente não permite vários plug-ins de mixer atribuídos a um único ator-mixer.  

## <a name="7-audio-setup-in-unreal"></a>7. Configuração de áudio em não real
* Primeiro, você precisará inserir seu nível de jogo para produzir um ativo acústico, que será colocado no `Content\Acoustics`. Consulte o [tutorial](unreal-baking.md) de cotorta inreal e retome aqui. Alguns níveis inclusas estão incluídos no pacote de exemplo.
* Crie um ator de espaço acústica em sua cena. Crie apenas um desses atores em um nível, uma vez que ele representa os acústicos para o nível inteiro. 

    ![Captura de tela de editor inreal mostrando a criação de ator de espaço acústicos](media/create-acoustics-space.png)

* Agora, atribua o ativo de dados acústicos inclusas ao slot de dados acústicos no ator do espaço acústico. Sua cena agora tem acústicas!

    ![Captura de tela de atribuição de ativo de inhowing editores acústicos inreals](media/acoustics-asset-assign.png)

* Agora, adicione um ator vazio e faça o seguinte:

    ![Captura de tela de editor inreal que mostra o uso de componentes acústicos em um ator vazio](media/acoustics-component-usage.png)

1. Adicione um componente de áudio acústicos ao ator. Esse componente estende o componente de áudio WWise com a funcionalidade para os acústicas do projeto.
2. A caixa reproduzir na inicialização é marcada por padrão, o que irá disparar o evento WWise associado na inicialização do nível.
3. Use a caixa de seleção Mostrar parâmetros acústicos para imprimir informações de depuração na tela sobre a origem.
    ![Captura de tela do painel acústicos de editor inreal na fonte de som com valores de depuração habilitados](media/debug-values.png)
4. Atribuir um evento WWise de acordo com o fluxo de trabalho WWise usual
5. Verifique se a opção usar áudio espacial está desativada. Neste momento, se você usar acústicas do projeto para um componente de áudio específico, não poderá usar simultaneamente o mecanismo de áudio espacial do WWise para acústicas.

Está tudo pronto. Mova-se para a cena e explore os efeitos acústicos!

## <a name="next-steps"></a>Passos Seguintes
* Tutorial de [design](unreal-workflow.md) para acústicas do projeto em inreal/WWise
* [Saiba como fazer prepara](unreal-baking.md) para seus bastidores de jogos 
