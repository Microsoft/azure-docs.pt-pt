---
title: Tutorial - instruções passo a passo para criar uma nova aplicação de HoloLens Unity com âncoras espaciais do Azure | Documentos da Microsoft
description: Neste tutorial, saiba como criar uma nova aplicação de HoloLens Unity com âncoras espaciais do Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/14/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: c831e8fdacf5103619374605dd980ab1f6735047
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135281"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Tutorial: Instruções passo a passo para criar uma nova aplicação de HoloLens Unity com âncoras espaciais do Azure

Neste tutorial mostram-lhe como criar uma nova aplicação HoloLens Unity com âncoras espaciais do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem:

1. Uma máquina Windows com <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> instalada com o **desenvolvimento da plataforma Universal do Windows** carga de trabalho e o **Windows 10 SDK (10.0.17763.0 ou mais recente)** componente, e <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a>.
2. O [C++ c++ /CLI WinRT Visual Studio extensão (VSIX)](https://aka.ms/cppwinrt/vsix) para o Visual Studio deve ser instalada a partir do [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Um dispositivo de HoloLens com [modo de programador](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) ativada. Este artigo requer um dispositivo de HoloLens com o [atualização do Windows 10 de Outubro de 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (também conhecido como RS5). Para atualizar para a versão mais recente no HoloLens, abra a **definições** aplicação, aceda à **atualização e segurança**, em seguida, selecione o **procurar atualizações** botão.

## <a name="getting-started"></a>Introdução

Iremos primeiro configurar nosso projeto e a cena do Unity:
1. Inicie o Unity.
2. Selecione **Novo**.
4. Certifique-se **3D** está selecionada.
5. Nomeie o projeto e introduza uma gravação **localização**.
6. Clique em **criar projeto**.
7. Guarde a cena vazio predefinido para um ficheiro novo com: **Arquivo** > **guardar como**.
8. Dê um nome novo cena **Main** e prima a **guardar** botão.

**Configurar as definições do projeto**

Vamos agora definir algumas configurações de projeto do Unity que ajudam-no SDK do Windows Holographic para o desenvolvimento de destino. 

Em primeiro lugar, permite configurar as definições de qualidade para a nossa aplicação. 
1. Selecione **edite** > **definições do projeto** > **qualidade**
2. Na coluna sob o **Windows Store** logótipo, clique na seta na **predefinido** da linha e selecione **muito baixa**. Saberá a configuração é aplicada corretamente quando a caixa no **Windows Store** coluna e **muito baixa** linha está verde.

É necessário permitir que o Unity, sabe que a aplicação que estamos a tentar exportar deve criar uma vista de imersão em vez de uma exibição em 2D. Vamos criar uma vista de imersão ao ativar o suporte de realidade Virtual do Unity visando o SDK do Windows 10.

1. Aceda a **edite** > **definições do projeto** > **Player**.
2. Na **painel de Inspetor** para **as definições do leitor**, selecione o **Windows Store** ícone.
3. Expanda a **XR definições** grupo.
4. Na **composição** secção, verifique o **suportado de realidade Virtual** caixa de verificação para adicionar um novo **Virtual SDK de realidade** lista.
5. Certifique-se de que **realidade mista do Windows** aparece na lista. Se não estiver, selecione o **+** botão na parte inferior da lista e escolha **realidade mista do Windows**.
 
> [!NOTE]
> Se não vir o ícone da Windows Store, faça duplo Certifique-se de que selecionou a Windows Store scripts back-end .NET antes da instalação. Caso contrário, poderá ter de reinstalar o Unity com a instalação correta do Windows.

**verificar a configuração do .NET**
1. Aceda a **edite** > **configurações de projeto** > **Player** (que possa ter **Player** abrir a partir do anterior passo).
2. Na **painel de Inspetor** para **as definições do leitor**, selecione o **Windows Store** ícone.
3. Na **outras definições** configuração secção, certifique-se de que **scripts back-end** está definida como **.NET**.

**conjunto de capacidades**
1. Aceda a **edite** > **configurações de projeto** > **Player** (que possa ter **Player** abrir a partir do anterior passo).
2. Na **painel de Inspetor** para **as definições do leitor**, selecione o **Windows Store** ícone.
3. Na **definições de publicação** seção de configuração, verificação **InternetClientServer** e **SpatialPerception**.

**configurar a câmera virtual principal**
1. Na **painel de hierarquia**, selecione **Main câmara**.
2. Na **Inspetor**, defina sua posição de transformação **0,0,0**.
3. Encontrar o **sinalizadores clara** propriedade e altere a lista pendente de **Skybox** para **cor sólida**.
4. Clique nas **em segundo plano** campo para abrir um selecionador de cores.
5. Definir **R, G, B e A** ao **0**.
6. Selecione **Add Component** e procure **Collider mapeamento geográfico**.

**criar o nosso script**
1. Na **projeto** painel, crie uma nova pasta **Scripts**, no **ativos** pasta. 
2. Clique com o botão direito do rato na pasta, em seguida, selecione **criar >** ,  **C# Script**. Título-lo **AzureSpatialAnchorsScript**. 
3. Aceda a **GameObject** -> **criar vazio**. 
4. Selecioná-la e, no **Inspetor** renomeá-lo a partir de **GameObject** para **MixedRealityCloud**. Selecione **Add Component** e procure e adicione o **AzureSpatialAnchorsScript**.

## <a name="trying-it-out"></a>Experimentá-lo
Para testar se tudo o que está a funcionar, crie a sua aplicação **Unity** e implementá-la a partir de **Visual Studio**. Siga o capítulo 6 a partir do [ **MR Noções básicas de 100: Introdução ao Unity** curso](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) para fazer isso. Deverá ver o Unity iniciar o ecrã e, em seguida, uma exibição clara.

## <a name="place-an-object-in-the-real-world"></a>Colocar um objeto no mundo real
Vamos criar & colocar um objeto com a sua aplicação. Abra a solução do Visual Studio que criamos quando estamos [implementado a nossa aplicação](#trying-it-out). 

Em primeiro lugar, adicione as seguintes importações em sua `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Em seguida, adicione as seguintes variáveis de membros em sua `AzureSpatialAnchorsScript` classe: 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-37,43-47,55-74)]

Em seguida, adicione o código seguinte para sua `Start()` método. Esse código serão conectados pelas `GestureRecognizer`, que irá detetar quando existe um toque no ar e chamada `HandleTap`.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=76-85,88&highlight=4-10)]

Agora temos de adicionar o seguinte procedimento `HandleTap()` método abaixo `Update()`. Ele fará um lançamento de ray e obter um ponto de acessos no qual pretende colocar uma esfera. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=264-274,295-297,301-309)]

Agora, precisamos criar esfera. A sphere inicialmente serão branco, mas este valor será ajustado mais tarde. Adicione o seguinte `CreateAndSaveSphere()` método:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-324,389)]

Executar a aplicação a partir **Visual Studio** para validá-la mais uma vez. Desta vez, toque no ecrã para criar e colocar seu esfera branca sobre a superfície da sua preferência.

## <a name="set-up-the-dispatcher-pattern"></a>Configurar o padrão de dispatcher

Ao trabalhar com o Unity, todas as APIs do Unity, por exemplo os APIs que usa para fazer atualizações da interface do Usuário, têm de ocorrer no thread principal. O código que escreveremos no entanto, obtemos retornos de chamada em outros threads. Queremos atualizar a interface do Usuário nesses retornos de chamada, portanto, precisamos de uma forma de aceder a partir de um thread de lado no thread principal. Para executar código no thread principal de um thread de lado, vamos utilizar o padrão de dispatcher. 

Vamos adicionar uma variável de membro, dispatchQueue, que é uma fila de ações. Iremos enviar por push ações para a fila e, em seguida, remover da fila e executar as ações no thread principal. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=33-46&highlight=6-9)]

Em seguida, vamos adicionar uma forma de adicionar uma ação para a fila. Adicione `QueueOnUpdate()` logo após `Update()` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=102-112)]

Vamos agora utilizar o loop Update () para verificar se existe uma ação em fila. Nesse caso, iremos remover da fila a ação e executá-lo.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=90-100&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Obter as âncoras de geográficos SDK do Azure

Agora, irá transferir o Azure geográficos âncoras SDK. Aceda a [página de lançamentos do Azure geográficos âncoras GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases). Em ativos, transfira o **AzureSpatialAnchors.unitypackage** ficheiro. 

No Unity, aceda a **ativos**, clique em **importar pacote** > **pacote personalizado...** . Navegue para o pacote e selecione **aberto**.

No novo **importar pacote do Unity** janela que é exibido, selecione **nenhum** na parte inferior esquerda. Em seguida, em **AzureSpatialAnchorsPlugin** > **plug-ins**, selecione **comuns**, **Editor**, e  **HoloLens**. Clique em **importação** no canto inferior direito.

Agora, precisamos restaurar pacotes Nuget para obter o SDK do Azure geográficos âncoras. Incorporar a partir **Unity** e, em seguida, abrir e criar resultante **Visual Studio** solução novamente, conforme detalhado no [experimentá-lo](#trying-it-out). 

No seu **Visual Studio** solução, adicione a seguinte importação em sua `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Em seguida, adicione as seguintes variáveis de membro em sua `AzureSpatialAnchorsScript` classe:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=43-58&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Anexar uma âncora de geográficos Azure local para a âncora de local

Vamos configurar CloudSpatialAnchorSession da âncora espaciais do Azure. Vamos começar adicionando o seguinte `InitializeSession()` método dentro de seu `AzureSpatialAnchorsScript` classe. Quando chamado, ele garantirá uma sessão de âncoras espaciais do Azure é criada e corretamente inicializada durante o arranque da sua aplicação.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=169-197,200-204)]

Agora, precisamos escrever código para processar chamadas de delegado. Vamos adicionar mais às mesmas à medida que Continuamos.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=206-221)]

Agora, vamos vincular sua `initializeSession()` método em seu `Start()` método.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=76-88&highlight=12)]

Por fim, adicione o código seguinte para sua `CreateAndSaveSphere()` método. Ele se ligará uma âncora de geográficos Azure local esfera que podemos estiver colocando no mundo real.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-337,390&highlight=16-31)]

Antes de prosseguir qualquer, terá de criar um âncoras espaciais do Azure conta identificador e a chave, se ainda não tivê-los. Siga a secção seguinte para obtê-las.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Carregar sua âncora local para a cloud

Assim que tiver o identificador de conta de âncoras espaciais do Azure e a chave, aceda e cole o `Account Id` numa `SpatialAnchorsAccountId` e o `Account Key` em `SpatialAnchorsAccountKey`.

Por fim, vamos conectar tudo. No seu `SpawnNewAnchoredObject()` método, adicione o seguinte código. Ele invocará o `CreateAnchorAsync()` método assim que a esfera é criada. Assim que o método retorna, o código abaixo irá efetuar uma atualização final para sua esfera, alterar a cor como azul.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=311-389&highlight=28-78)]

Executar a aplicação a partir **Visual Studio** mais uma vez. Mover-se a cabeça e, em seguida, utilizar este gesto para colocar a esfera. Assim que tivermos suficiente quadros, esfera irá transformar em amarelo e o carregamento de nuvem é iniciado. Depois do carregamento estiver concluído, seu esfera ativará azul. Opcionalmente, também pode usar a janela de saída dentro **Visual Studio** para monitorizar a sua aplicação está a enviar as mensagens de registo. Poderá observar o recomendado de progresso de criar, bem como o identificador de âncora cloud devolve quando o carregamento estiver concluído.

> [!NOTE]
> Se obtiver "DllNotFoundException: Não é possível carregar a DLL "AzureSpatialAnchors": Não foi possível encontrar o módulo especificado. ", deve **Clean** e **criar** novamente a sua solução.

## <a name="locate-your-cloud-spatial-anchor"></a>Localize sua âncora geográficos de cloud

Um sua âncora é carregado para a cloud, estamos prontos para tentar localizá-lo novamente. Vamos adicionar o código seguinte para sua `HandleTap()` método. Este código poderá:

* Chamar `ResetSession()`, que irá parar a `CloudSpatialAnchorSession` e remover nossa esfera azul existente a partir do ecrã.
* Inicializar `CloudSpatialAnchorSession` novamente. Fazemos isso então estamos-se de que a âncora, vamos localizar vem da cloud em vez de ser a âncora de local que criámos.
* Criar uma **observador** que irá procurar a âncora de nós carregamos a âncoras espaciais do Azure.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=264-302&highlight=13-31,34-36)]

Vamos agora adicionar nosso `ResetSession()` e `CleanupObjects()` métodos. Pode colocá-los abaixo `QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=114-167)]

Agora, precisamos conectar o código que será chamado quando a âncora que estiver a consultar para está localizada. Dentro do `InitializeSession()`, adicione as seguintes chamadas de retorno:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=195-201&highlight=4-5)]

 
Agora, vamos adicionar um código que irá criar e colocar uma esfera verde assim que o CloudSpatialAnchor for localizado. Ele também ativará o ecrã tocar novamente, para que pode repetir o cenário completo de uma só mais uma vez: criar outro âncora local, carregá-la e localizá-la novamente.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=223-262)]

Já está! Executar a aplicação a partir **Visual Studio** pela última vez para experimentar o cenário completo ponta a ponta. Mover o seu dispositivo e colocar seu esfera branca. Em seguida, mantenha mover sua cabeça para capturar os dados de ambiente até que a esfera fica amarela. Sua âncora local será carregada, e seu esfera ativará azul. Por fim, toque em sua tela mais uma vez, para que sua âncora local é removida e, em seguida, podemos vai consultar para o respetivo homólogo na cloud. Continue a movimentar o dispositivo até que a sua âncora geográficos de cloud está localizada. Uma esfera verde deve aparecer na localização correta, e pode gravá & Repetir novamente o cenário completo.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]