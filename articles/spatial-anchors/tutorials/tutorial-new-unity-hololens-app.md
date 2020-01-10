---
title: 'Tutorial: criar um novo aplicativo de Unity do HoloLens'
description: Neste tutorial, você aprenderá a criar um novo aplicativo de Unity do HoloLens usando âncoras espaciais do Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 07/05/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e1abb759c80e770f1e650c232b6b2e21232b7e6f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457718"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Tutorial: instruções passo a passo para criar um novo aplicativo de Unity do HoloLens usando âncoras espaciais do Azure

Este tutorial mostrará como criar um novo aplicativo de Unity do HoloLens com âncoras espaciais do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem:

1. Um computador Windows com o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> instalado com a carga de trabalho de **desenvolvimento plataforma universal do Windows** e o componente **SDK do Windows 10 (10.0.18362.0 ou mais recente)** e <a href="https://git-scm.com/download/win" target="_blank">git para Windows</a>.
2. A [ C++extensão do/WinRT Visual Studio (VSIX)](https://aka.ms/cppwinrt/vsix) para Visual Studio deve ser instalada no [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Um dispositivo HoloLens com o [modo de desenvolvedor](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) habilitado. Este artigo requer um dispositivo HoloLens com a [atualização de outubro de 2018 do Windows 10](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (também conhecida como RS5). Para atualizar para a versão mais recente no HoloLens, abra o aplicativo **configurações** , acesse **Atualizar & segurança**e, em seguida, selecione o botão **verificar atualizações** .

## <a name="getting-started"></a>Introdução

Primeiro, vamos configurar nossa cena de projeto e Unity:
1. Inicie o Unity.
2. Selecione **Novo**.
4. Verifique se **3D** está selecionado.
5. Nomeie seu projeto e insira um **local**para salvar.
6. Clique em **criar projeto**.
7. Salve a cena padrão vazia em um novo arquivo usando: **arquivo** > **salvar como**.
8. Nomeie a nova cena **Main** e pressione o botão **salvar** .

**Definir as configurações do projeto**

Agora, vamos definir algumas configurações de projeto do Unity que nos ajudam a direcionar o SDK do Windows Holographic para desenvolvimento.

Primeiro, vamos definir as configurações de qualidade para nosso aplicativo.
1. Selecione **Editar** **configurações do projeto** >  > **qualidade**
2. Na coluna sob o logotipo da **Windows Store** , clique na seta na linha **padrão** e selecione **muito baixo**. Você saberá que a configuração é aplicada corretamente quando a caixa na coluna da **Windows Store** e a linha **muito baixa** estiverem verdes.

Precisamos deixar que o Unity saiba que o aplicativo que estamos tentando exportar deve criar uma exibição imersiva em vez de uma exibição 2D. Criamos uma exibição de imersão habilitando o suporte de realidade virtual no Unity direcionando o SDK do Windows 10.

1. Vá para **Editar** **configurações de projeto** >  > **Player**.
2. No **painel Inspetor** para **configurações do Player**, selecione o ícone **Windows Store** .
3. Expanda o grupo de **configurações XR** .
4. Na seção **renderização** , marque a caixa de seleção **suporte à realidade virtual** para adicionar uma nova lista **do SDK da realidade virtual** .
5. Verifique se a **realidade mista do Windows** aparece na lista. Caso contrário, selecione o botão **+** na parte inferior da lista e escolha **realidade mista do Windows**.

> [!NOTE]
> Se você não vir o ícone Windows Store, verifique se selecionou o back-end de script do .NET da Windows Store antes da instalação. Caso contrário, talvez seja necessário reinstalar o Unity com a instalação correta do Windows.

**Verificar a configuração de back-end de script**
1. Vá para **Editar** **configurações do projeto** >  > **Player** (talvez você ainda tenha o **Player** aberto na etapa anterior).
2. No **painel Inspetor** para **configurações do Player**, selecione o ícone **Windows Store** .
3. Na seção configuração de **outras configurações** , verifique se o **back-end de script** está definido como **IL2CPP**.

**Definir recursos**
1. Vá para **Editar** **configurações do projeto** >  > **Player** (talvez você ainda tenha o **Player** aberto na etapa anterior).
2. No **painel Inspetor** para **configurações do Player**, selecione o ícone **Windows Store** .
3. Na seção configuração de **configurações de publicação** , verifique **InternetClientServer** e **SpatialPerception**.

**Configurar a câmera virtual principal**
1. No **painel hierarquia**, selecione **câmera principal**.
2. No **Inspetor**, defina sua posição de transformação como **0, 0, 0**.
3. Localize a propriedade **limpar sinalizadores** e altere a lista suspensa de **Skybox** para **cor sólida**.
4. Clique no campo **plano de fundo** para abrir um seletor de cores.
5. Defina **R, G, B e** a como **0**.
6. Selecione **Adicionar componente** e pesquise e adicione o **Colisor de mapeamento espacial**.

**Criar nosso script**
1. No painel **projeto** , crie uma nova pasta, **scripts**, na pasta **ativos** .
2. Clique com o botão direito do mouse na pasta e, em seguida, selecione **criar >** ,  **C# script**. Title it **AzureSpatialAnchorsScript**.
3. Vá para **gameobject** -> **criar vazio**.
4. Selecione-o e, no **Inspetor** , renomeie-o de **gameobject** para **MixedRealityCloud**. Selecione **Adicionar componente** e pesquise e adicione o **AzureSpatialAnchorsScript**.

**Criar o pré-fabricado de esfera**
1. Vá para **gameobject** -> **objeto 3D** -> **Sphere**.
2. No **Inspetor**, defina sua escala como **0,25, 0,25, 0,25**.
3. Localize o objeto **Sphere** no painel **hierarquia** . Clique nele e arraste-o para a pasta **ativos** no painel **projeto** .
4. Clique com o botão direito do mouse e **exclua** a esfera original criada no painel **hierarquia** .

Agora você deve ter um pré-fabricado de esfera no painel do **projeto** .

## <a name="trying-it-out"></a>Experimentando
Para testar se tudo está funcionando, crie seu aplicativo no **Unity** e implante-o no **Visual Studio**. Siga o capítulo 6 do [ **Sr noções básicas 100: introdução** ](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) ao módulo do Unity para fazer isso. Você deve ver a tela inicial do Unity e, em seguida, uma exibição clara.

## <a name="place-an-object-in-the-real-world"></a>Coloque um objeto no mundo real
Vamos criar & Coloque um objeto usando seu aplicativo. Abra a solução do Visual Studio que criamos quando [implantamos nosso aplicativo](#trying-it-out).

Primeiro, adicione as seguintes importações em seu `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Em seguida, adicione as seguintes variáveis de membros em sua classe `AzureSpatialAnchorsScript`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-42,48-52,60-79)]

Antes de continuarmos, precisamos definir o pré-fabricado de esfera que criamos em nossa variável de membro spherePrefab. Volte para o **Unity**.
1. No **Unity**, selecione o objeto **MixedRealityCloud** no painel **hierarquia** .
2. Clique no pré-fabricado de **esfera** que você salvou no painel **projeto** . Arraste a **esfera** em que você clicou na área **pré-fabricado de esfera** no **script de âncoras espaciais do Azure (script)** no painel **Inspetor** .

Agora você deve ter o **Sphere** definido como o pré-fabricado em seu script. Crie a partir do **Unity** e, em seguida, abra a solução do **Visual Studio** resultante novamente, como você acabou de [experimentá-la](#trying-it-out).

No **Visual Studio**, abra `AzureSpatialAnchorsScript.cs` novamente. Adicione o código a seguir ao seu método de `Start()`. Esse código será conectado `GestureRecognizer`, que detectará quando há um toque de ar e chama `HandleTap`.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-90,93&highlight=4-10)]

Agora, precisamos adicionar o seguinte método de `HandleTap()` abaixo `Update()`. Ele fará um raio cast e obterá um ponto de acesso no qual colocar uma esfera.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-277,299-300,304-312)]

Agora, precisamos criar a esfera. A esfera inicialmente será branca, mas esse valor será ajustado posteriormente. Adicione o seguinte método de `CreateAndSaveSphere()`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-325,390)]

Execute seu aplicativo do **Visual Studio** para validá-lo mais uma vez. Desta vez, toque na tela para criar & Coloque sua esfera branca sobre a superfície de sua escolha.

## <a name="set-up-the-dispatcher-pattern"></a>Configurar o padrão de Dispatcher

Ao trabalhar com o Unity, todas as APIs do Unity, por exemplo, as APIs usadas para fazer atualizações da interface do usuário, precisam acontecer no thread principal. No código que escreveremos no entanto, obteremos retornos de chamada em outros threads. Queremos atualizar a interface do usuário nesses retornos de chamada, portanto, precisamos de uma maneira de passar de um thread do lado para o thread principal. Para executar o código no thread principal de um thread do lado, usaremos o padrão Dispatcher.

Vamos adicionar uma variável de membro, dispatchQueue, que é uma fila de ações. Enviaremos ações por push para a fila e, em seguida, removerei a fila e executarei as ações no thread principal.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=38-51&highlight=6-9)]

Em seguida, vamos adicionar uma maneira de adicionar uma ação à fila. Adicionar `QueueOnUpdate()` logo após `Update()`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=107-117)]

Agora, vamos usar o loop Update () para verificar se há uma ação na fila. Nesse caso, vamos remover a ação da fila e executá-la.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=95-105&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Obter o SDK de âncoras espaciais do Azure

## <a name="via-unity-packagetabunitypackage"></a>[Por meio do pacote do Unity](#tab/UnityPackage)

Agora, Baixaremos o SDK de âncoras espaciais do Azure. Vá para a [página de versões do GitHub de âncoras espaciais do Azure](https://github.com/Azure/azure-spatial-anchors-samples/releases). Em ativos, baixe o **AzureSpatialAnchors. unitypackage**. No Unity, vá para **ativos**, clique em **Importar pacote** > **pacote personalizado...** . Navegue até o pacote e selecione **abrir**.

Na nova janela **Importar pacote de Unity** que aparece, desmarque **plug-ins** e clique em **importar** no canto inferior direito.

Agora, precisamos restaurar os pacotes NuGet para obter o SDK de âncoras espaciais do Azure. Crie a partir do **Unity** e, em seguida, abra e Compile a solução resultante do **Visual Studio** novamente, conforme detalhado em [experimentando](#trying-it-out).

## <a name="via-nugetforunitytabnugetforunity"></a>[Via NuGetForUnity](#tab/NuGetForUnity)

Primeiro, precisamos instalar o NuGetForUnity. Vá para a [página de versões do GitHub do NuGetForUnity](https://github.com/GlitchEnzo/NuGetForUnity/releases). Em ativos, baixe o **NuGetForUnity. unitypackage**mais recente. No Unity, vá para **ativos**, clique em **Importar pacote** > **pacote personalizado...** . Navegue até o pacote e selecione **abrir**. Agora, o Unity instalará o NugetForUnity. Se você não vir uma nova lista suspensa do **NuGet** no Unity, talvez seja necessário clicar com o botão direito em **projetos** > **ativos**. Em seguida, selecione **reimportar tudo**.

Depois de instalar o NuGetForUnity, selecione **NuGet** > **gerenciar pacotes NuGet**. Em seguida, pesquise Microsoft. Azure. SpatialAnchors. Unity e selecione **instalar**.

Agora, precisamos criar para obter o SDK real de âncoras espaciais do Azure, pois o pacote NuGet que acabamos de baixar apenas contém scripts auxiliares. Crie a partir do **Unity** e, em seguida, abra e Compile a solução resultante do **Visual Studio** novamente, conforme detalhado em [experimentando](#trying-it-out).

---

Em sua solução do **Visual Studio** , adicione a seguinte importação em seu `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Em seguida, adicione as seguintes variáveis de membro à sua classe `AzureSpatialAnchorsScript`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=48-63&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Anexar uma âncora espacial local do Azure à âncora local

Vamos configurar o CloudSpatialAnchorSession da âncora espacial do Azure. Vamos começar adicionando o seguinte método `InitializeSession()` dentro de sua classe `AzureSpatialAnchorsScript`. Uma vez chamado, ele garantirá que uma sessão de âncoras espaciais do Azure seja criada e inicializada corretamente durante a inicialização do seu aplicativo.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=174-202,205-209)]

Agora, precisamos escrever código para lidar com chamadas delegadas. Vamos adicionar mais a eles à medida que continuarmos.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=211-226)]

Agora, vamos conectar seu método `initializeSession()` ao seu método `Start()`.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-93&highlight=12)]

Por fim, adicione o código a seguir ao seu método `CreateAndSaveSphere()`. Ele anexará uma âncora espacial local do Azure à esfera que estamos colocando no mundo real.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-338,390&highlight=14-25)]

Antes de continuar, você precisará criar um identificador e uma chave de conta de âncoras espaciais do Azure, se você ainda não os tiver. Siga a seção a seguir para obtê-los.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Carregue sua âncora local na nuvem

Depois que você tiver o identificador e a chave da conta das âncoras espaciais do Azure, vá e cole o `Account Id` em `SpatialAnchorsAccountId` e no `Account Key` em `SpatialAnchorsAccountKey`.

Por fim, vamos conectar tudo isso. Em seu método de `SpawnNewAnchoredObject()`, adicione o código a seguir. Ele invocará o método `CreateAnchorAsync()` assim que sua esfera for criada. Depois que o método retornar, o código a seguir executará uma atualização final para sua esfera, alterando sua cor para azul.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-391&highlight=26-77)]

Execute seu aplicativo do **Visual Studio** mais uma vez. Vá em frente à sua cabeça e toque em ar para posicionar a esfera. Assim que tivermos quadros suficientes, a esfera será transformada em amarelo e o carregamento da nuvem será iniciado. Quando o carregamento for concluído, sua esfera ficará azul. Opcionalmente, você também pode usar a janela saída dentro do **Visual Studio** para monitorar as mensagens de log que seu aplicativo está enviando. Você poderá observar o recomendado para criar o progresso, bem como o identificador de âncora que a nuvem retorna quando o carregamento é concluído.

> [!NOTE]
> Se você receber "DllNotFoundException: não é possível carregar a DLL ' AzureSpatialAnchors ': o módulo especificado não pôde ser encontrado.", você deve **limpar** e **Compilar** sua solução novamente.

## <a name="locate-your-cloud-spatial-anchor"></a>Localize sua âncora espacial de nuvem

Uma sua âncora é carregada na nuvem, estamos prontos para tentar localizá-la novamente. Vamos adicionar o código a seguir ao seu método `HandleTap()`. Esse código irá:

* Chame `ResetSession()`, que irá parar o `CloudSpatialAnchorSession` e remover nossa esfera azul existente da tela.
* Inicialize `CloudSpatialAnchorSession` novamente. Fazemos isso para que tenhamos certeza de que a âncora que vamos localizar vem da nuvem, em vez de ser a âncora local que criamos.
* Crie um **observador** que procurará a âncora que carregamos para âncoras espaciais do Azure.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-305&highlight=13-31,35-36)]

Agora, vamos adicionar nossos `ResetSession()` e `CleanupObjects()` métodos. Você pode colocá-los abaixo `QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=119-172)]

Agora, precisamos conectar o código que será invocado quando a âncora que estamos consultando estiver localizada. Dentro de `InitializeSession()`, adicione os seguintes retornos de chamada:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=200-206&highlight=4-5)]


Agora, vamos adicionar o código que criará & posicionar uma esfera verde quando o CloudSpatialAnchor estiver localizado. Ele também habilitará o toque da tela novamente, para que você possa repetir o cenário inteiro mais uma vez: crie outra âncora local, carregue-a e localize-a novamente.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=228-265)]

Já está! Execute seu aplicativo do **Visual Studio** uma última vez para experimentar todo o cenário de ponta a ponta. Mova-se pelo dispositivo e coloque sua esfera branca. Em seguida, continue movendo sua cabeça para capturar dados de ambiente até que a esfera fique amarela. Sua âncora local será carregada e sua esfera ficará azul. Por fim, toque na tela mais uma vez, para que sua âncora local seja removida e, em seguida, consultaremos seu equivalente em nuvem. Continue movendo seu dispositivo até que a âncora espacial da nuvem esteja localizada. Uma esfera verde deve aparecer no local correto e você pode aplique & repetir o cenário inteiro novamente.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]