---
title: Projeto Acoustics Unreal e integração de Wwise
titlesuffix: Azure Cognitive Services
description: Nesta explicação de procedimento descreve a integração do plug-ins de Wwise e projeto Acoustics Unreal em seu projeto.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 0baaf31386e1155dee6ca2bbfda6827ca3fc36fe
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313452"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Projeto Acoustics Unreal e integração de Wwise
Nesta explicação de procedimento fornece os passos de integração detalhadas do pacote de plug-in do projeto Acoustics em seu projeto jogo existente Unreal e Wwise. 

Requisitos de software:
* [Unreal Engine](https://www.unrealengine.com/) 4.21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1. +
* [Plug-in do Wwise para Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Se estiver a utilizar uma integração direta do Wwise SDK em vez de utilizar os plug-ins Wwise Unreal, consulte o plug-in do projeto Acoustics Unreal e ajustar as chamadas à API de Wwise.

Se gostaria de utilizar Acoustics de projeto com um mecanismo de áudio que não seja Wwise, contacte-nos sobre o [fóruns de projeto Acoustics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics). Pode utilizar o plug-in do projeto Acoustics Unreal para consultar dados de acoustics e, em seguida, fazer chamadas de API para o motor.

## <a name="download-project-acoustics"></a>Transferir o projeto Acoustics
Se ainda não o fez, transfira o [pacote de plug-in do projeto Acoustics Unreal & Wwise](https://www.microsoft.com/download/details.aspx?id=58090)). 

Incluímos um plug-in do Unreal Engine e um plug-in do Wwise mixer no pacote. O plug-in Unreal fornece integração do editor e do tempo de execução. Durante o jogo, o plug-in do projeto Acoustics Unreal calcula os parâmetros como oclusão para cada objeto jogo cada quadro. Esses parâmetros são traduzidos em chamadas à API de Wwise.

## <a name="review-integration-steps"></a>Reveja os passos de integração

Existem estes passos principais para instalar o pacote e implementá-lo em seu jogo.
1. Instalar o plug-in do projeto Acoustics Wwise mixer
2. (Re) implemente Wwise seu jogo. Este passo propaga o plug-in do mixer em seu projeto do jogo.
3. Adicionar o projeto Acoustics Unreal Plug-in para o seu jogo
4. Expandir a funcionalidade de plug-in Unreal do Wwise
5. Criar jogo e verifique Python está ativado
6. Configurar o seu projeto de Wwise utilizar Acoustics do projeto
7. Configuração de áudio em Unreal

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1. Instalar o plug-in do projeto Acoustics mixer
* Abra o iniciador de Wwise, em seguida, no **plug-ins** separador, em **instalar novo plug-ins**, selecione **adicionar a partir do diretório**. 

    ![Captura de ecrã de instalação de um plug-in no iniciador de Wwise](media/wwise-install-new-plugin.png)

* Escolha o `AcousticsWwisePlugin\ProjectAcoustics` diretório que foi incluído no pacote que transferiu. Ela contém o pacote de plug-in do mixer Wwise.

* Wwise irá instalar o plug-in. Projeto Acoustics agora deve aparecer na lista de plug-ins instalados no Wwise.
![Lista de plug-ins de captura de ecrã de Wwise instalado após a instalação de projeto Acoustics](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2. (Re) implementar Wwise em seu jogo
Voltar a implementar Wwise ao seu jogo, mesmo que já se integraram Wwise. Isto seleciona o plug-in do projeto Acoustics Wwise.

* **Plug-in do motor:** Se tiver Wwise instalado como um plug-in jogo num projeto de Unreal C++, ignore este passo. Se estiver instalado em vez disso, como um plug-in do motor, para a instância como o seu projeto Unreal é implementação Wwise apenas, de esquema com nossos Plug-in do mixer é mais complexa. Criar um projeto de Unreal C++ fictício, vazio, fechá-lo se Unreal editor é aberto e siga o procedimento restante para implementar Wwise para este projeto fictício. Em seguida, copie o plug-in de Wwise implementado.
 
* A partir do iniciador de Wwise, clique nas **Unreal Engine** separador, em seguida, clique no menu de hambúrguer junto a **projetos recentes de motor Unreal** e selecione **navegue para o projeto**. Abrir projeto Unreal do seu jogo `.uproject` ficheiro.

    ![Separador do captura de ecrã de Wwise iniciador Unreal](media/wwise-unreal-tab.png)

* Em seguida, clique em **Wwise integrar no projeto** ou **modificar Wwise no projeto**. Este passo (re) integra Wwise binários no seu projeto, incluindo o plug-in do projeto Acoustics mixer agora.

* **Plug-in do motor:** Se estiver a utilizar o Wwise como um plug-in do mecanismo e criou o projeto fictício conforme apresentado acima, copie a pasta Wwise implementado: `[DummyUProject]\Plugins\Wwise` e cole-o em `[UESource]\Engine\Plugins\Wwise`. `[DummyUProject]` é o caminho de projeto vazio do Unreal C++, e `[UESource]` é onde tem as origens de Unreal Engine instaladas. Quando tiver terminado a copiar, pode eliminar o projeto fictício.

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3. Adicionar o projeto Acoustics Unreal Plug-in para o seu jogo
 
* Cópia a `Unreal\ProjectAcoustics` pasta em que o plug-in do pacote e crie uma nova pasta `[UProjectDir]\Plugins\ProjectAcoustics`, onde `UProjectDir` é pasta de projeto do seu jogo que contém o `.uproject` ficheiro.
  * **Plug-in do motor**: Se estiver a utilizar Wwise como um plug-in do motor, deve usar Acoustics do projeto como um plug-in do Unreal engine também. Em vez do diretório de destino acima, utilize: `[UESource]\Engine\Plugins\ProjectAcoustics`.

* Confirme que vê um `Wwise` pasta juntamente com o `ProjectAcoustics` pasta. Ela contém o plug-in de Wwise juntamente com os binários para o plug-in do mixer que (re-) implementado no passo 2 acima.

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4. Expandir a funcionalidade de plug-in Unreal do Wwise
* O projeto Acoustics Unreal Plug-in requer comportamento adicional seja exposto a partir do Wwise Unreal Plug-in de API por [essas diretrizes](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Incluímos um arquivo em lotes para automatizar o procedimento de aplicação de patches. 
* Dentro `Plugins\ProjectAcoustics\Resources`, execute `PatchWwise.bat`. A imagem de exemplo abaixo utiliza o nosso projeto de exemplo AcousticsGame.

    ![Realce da janela de captura de ecrã do Explorador do Windows fornecido o script de correção Wwise](media/patch-wwise-script.png)

* Se não tiver instalado o SDK do DirectX, precisará comentar a linha que contém DXSDK_DIR no `[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Captura de ecrã do editor de código que mostra DXSDK comentado](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5. Criar jogo e verifique Python está ativado

* Compilar seu jogo e certifique-se de que ele cria corretamente. Caso contrário, verifique os passos anteriores cuidadosamente antes de continuar. 
* Abra o projeto no Editor de Unreal. 
* **Plug-in do motor:** Se utilizar ProjectAcoustics como plug-in do motor, certifique-se também que está ativada, listado sob o plug-ins "interno".
* Deverá ver um novo modo, o que indica o que projeto Acoustics foi integrado.

    ![Captura de ecrã de Unreal mostrando Acoustics modo completo](media/acoustics-mode-full.png)

* Certifique-se que tiver o plug-in do Python para Unreal ativada. Isto é necessário para a integração de editor funcionar corretamente.

    ![Captura de ecrã de ativação de extensões de Python no Unreal editor](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6. Definição do projeto Wwise

Um projeto de Wwise de exemplo está incluído no download de exemplos. Recomendamos olhar juntamente com estas instruções. As capturas de ecrã abaixo são obtidas a partir deste projeto.

### <a name="bus-setup"></a>Configuração do barramento
* O plug-in do projeto Acoustics Unreal irá procurar o plug-in do mixer associado num barramento nisso ***exata*** name: `Project Acoustics Bus`. Crie um novo barramento de áudio com este nome. O plug-in do mixer pode funcionar em diversas configurações, mas por enquanto presumimos que será utilizado para apenas reverberação processamento. Esse barramento incluirão o sinal de reverberação misto para todas as origens que utilizam Acoustics. Pode combinar a montante para qualquer bus misturar estrutura, é mostrado um exemplo abaixo, retirado do nosso projeto de exemplo Wwise, incluído no download de exemplo.

    ![Barramentos de captura de ecrã de Wwise que mostra o projeto Acoustics Bus](media/acoustics-bus.png)

* A configuração de canal no barramento precisa ser definida para um dos: `1.0, 2.0, 4.0, 5.1 or 7.1`. Outras configurações fará nenhuma saída nesse barramento.

    ![Captura de ecrã das opções de configuração do canal para o projeto Acoustics Bus](media/acoustics-bus-channel-config.png)

* Agora, para o projeto Acoustics detalhes de barramento e certifique-se de que pode ver o separador de plug-in do Mixer

    ![Captura de ecrã de Wwise que mostra como ativar o separador de plug-in do Mixer para o barramento de Acoustics do projeto](media/mixer-tab-enable.png)

* Em seguida, vá para o separador de plug-in do Mixer e adicione o mixer de acoustics do projeto, plug-in no barramento

    ![Barramento de Screenshow de Wwise que mostra como adicionar o plug-in do projeto Acoustics Mixer](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>Configuração da hierarquia de ator mixer
* Por motivos de desempenho, projeto Acoustics aplica-se DSP áudio a todas as origens em simultâneo. Isto requer que o plug-in para operar como um plug-in do mixer. Wwise requer mixer plug-ins de estar no barramento de saída, embora o barramento de saída, normalmente, carrega o sinal de saída de dry. Projeto Acoustics requer o sinal de dry ser encaminhada através de ônibus aux enquanto o sinal molhado é transmitido no envio o `Project Acoustics Bus`. O seguinte processo suporta a migração gradual para este fluxo de sinal.

* Digamos que tenha um projeto existente com uma hierarquia de ator mixer contendo as Pisadas, armas e outras pessoas na parte superior do nível. Cada um tem correspondente de saída de barramento para sua mistura de dry. Permite que Digamos que queira migrar as Pisadas utilizar acoustics. Em primeiro lugar, crie um barramento aux correspondente para realizar suas submix dry, que é um filho do barramento de saída Pisadas de. Por exemplo, utilizámos um prefixo "Dry" na imagem abaixo para organizar, embora o nome exato não é importante. Qualquer medidores ou efeitos que tinha no barramento Pisadas continuarão a funcionar como antes.

    ![Captura de ecrã do recomendada Wwise Dry mistura a configuração](media/wwise-dry-mix-setup.png)

* Em seguida, modificar a estrutura de saída de barramento para o ator-mixer Pisadas da seguinte forma, com o projeto Acoustics Bus definir como Bus de saída e Dry_Footsteps definido como um barramento aux definidas pelo utilizador.

    ![Captura de ecrã do programa de configuração do recomendada Wwise Ator Mixer Bus](media/actor-mixer-bus-settings.png)

* Agora todas as pisadas recebe o tratamento de acoustics e seus reverberação no projeto Acoustics barramento de saída. O sinal de dry é encaminhado através de Dry_Footsteps e spatialized como de costume.

* Projeto Acoustics só se aplica a sons que têm uma localização de 3D no mundo. A seguir [Wwise documentação](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), o posicionamento propriedades tem de ser definido como mostrado. A definição de "3D Spatialization" pode ser "Posição" ou "Posição + orientação", conforme necessário.

    ![Captura de ecrã de definições de posicionamento de Ator de Wwise recomendadas](media/wwise-positioning.png)

* O barramento de saída na definição de algumas outro bus que combine a montante para **projeto Acoustics Bus** não funcionará. Wwise impõe este requisito no plug-ins do mixer.

* Se pretender que um elemento subordinado na hierarquia de ator mixer Pisadas para não utilizar acoustics, pode sempre utilizar "principal" substituição no mesmo recusá-lo.

* Se estiver a utilizar o jogo - ou -definido pelo utilizador envia para reverberação em qualquer mixer ator no jogo, transformá-los no mixer ator, para evitar a aplicar reverberação duas vezes.

### <a name="spatialization"></a>Spatialization
Por predefinição, o plug-in do projeto Acoustics Wwise mixer aplica-se reverberação convolution, deixando Wwise fazer spatialization movimento panorâmico. Ao utilizar esta configuração predefinida de só de reverberação Acoustics de projeto, tem liberdade para utilizar qualquer método de configuração e spatialization do canal sua combinação de dry, permitindo-lhe combinar e corresponder a quase qualquer spatializer com reverberação dos Acoustics do projeto. As opções incluem [com base em Ambisonics binaural spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) e [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Projeto Acoustics inclui um spatializer opcional que suporta o processamento de HRTF de alta resolução baseado em objeto e o movimento panorâmico. Marque a caixa de verificação "Spatialization executar" nas definições do plug-in do mixer e escolha entre HRTF ou Panning e desativar envia aux definidas pelo utilizador configurar acima para todos os barramentos dry para evitar spatializing duas vezes, tanto com o plug-in do projeto Acoustics mixer e Wwise. Não é possível alterar o modo de spatialization em tempo real, porque é necessária uma nova geração de som banco. Tem de reiniciar Unreal, em seguida, voltar a gerar soundbanks antes de atingir play as alterações à configuração de plug-in de mixer, como a caixa de verificação 'Spatialization executar'.

![Definições de captura de ecrã de Wwise Mixer Plug-in Spatialization](media/mixer-spatial-settings.png)

Infelizmente, outros plug-ins de spatializer baseada em objetos não podem ser suportado neste momento à medida que eles são implementados como plug-ins do mixer e Wwise não permite atualmente que vários mixer plug-ins atribuídos a um único mixer de ator.  

## <a name="7-audio-setup-in-unreal"></a>7. Configuração de áudio em Unreal
* Em primeiro lugar, terá de inserir o seu nível de jogo para produzir um ativo de acoustics, que vai ser colocado `Content\Acoustics`. Consulte a [Unreal Tutorial inserir](unreal-baking.md) e retomar aqui. Alguns níveis de previamente incorporadas são incluídos no pacote de exemplo.
* Crie um ator Acoustics espaço em seu cenário. Apenas crie um destes atores num nível de como ele representa o acoustics para o nível de todo. 

    ![Captura de ecrã de Unreal editor, que mostra a criação de ator Acoustics espaço](media/create-acoustics-space.png)

* Agora, atribua o elemento de dados acústicos incorporadas para o bloco de dados de Acoustics no ator Acoustics espaço. Sua cena tem agora acoustics!

    ![Captura de ecrã de Unreal acoustics de howing editor s atribuição de recurso](media/acoustics-asset-assign.png)

* Agora, adicione um ator vazio e faça o seguinte:

    ![Captura de ecrã de Unreal editor, que mostra a utilização de Acoustics componente num ator vazio](media/acoustics-component-usage.png)

1. Adicione um componente de áudio Acoustics para o ator. Este componente estende o componente de áudio Wwise com a funcionalidade para Acoustics do projeto.
2. A Play na caixa de início é marcada por padrão, o que irá disparar o evento Wwise associado no arranque de nível.
3. Utilize a caixa de verificação Mostrar parâmetros de Acoustics para imprimir na tela informações sobre a origem de depuração.
    ![Captura de ecrã de Unreal painel de Acoustics editor na origem de som com valores de depuração ativada](media/debug-values.png)
4. Atribuir um evento de Wwise por fluxo de trabalho de Wwise habitual
5. Certifique-se de que a utilização de áudio geográficos está desativado. Neste momento, se utilizar Acoustics de projeto para um determinado componente de áudio, não poderá usar simultaneamente motor de áudio espaciais do Wwise para acoustics.

Está tudo pronto. Mover-se a cena e explore os efeitos acústicos!

## <a name="next-steps"></a>Passos Seguintes
* [Design](unreal-workflow.md) para Acoustics de projeto na Unreal/Wwise
* [Saiba como fazer incorpora](unreal-baking.md) para seu plano de jogos 
