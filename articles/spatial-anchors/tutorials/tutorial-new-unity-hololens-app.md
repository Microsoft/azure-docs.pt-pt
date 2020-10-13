---
title: 'Tutorial: Criar uma nova app HoloLens Unitity'
description: Neste tutorial, aprende-se a criar uma nova aplicação HoloLens Unitity utilizando âncoras espaciais Azure.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 08/17/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: ee7a0ca1abedd2f80cd8f5fe66e603315a10966f
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939533"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Tutorial: Instruções passo a passo para criar uma nova app HoloLens Unitity usando âncoras espaciais Azure

Este tutorial irá mostrar-lhe como criar uma nova aplicação HoloLens Unitity com âncoras espaciais Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem:

1. Uma máquina Windows com <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017+</a> instalada com a carga de **trabalho de desenvolvimento** da Plataforma Universal Windows e o componente Windows **10 SDK (10.0.18362.0 ou mais recente)** e <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a>.
2. A [extensão do estúdio visual C++/WinRT (VSIX)](https://aka.ms/cppwinrt/vsix) para Visual Studio deve ser instalada a partir do [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Um dispositivo HoloLens com [modo de desenvolvimento](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) ativado. Este artigo requer um dispositivo HoloLens com a [atualização do Windows 10 May 2020](https://docs.microsoft.com/windows/mixed-reality/whats-new/release-notes-may-2020 ). Para atualizar a versão mais recente do HoloLens, abra a aplicação **Definições,** vá a **Update & Security**, selecione o botão Verificar para **atualizações.**

## <a name="getting-started"></a>Introdução

Primeiro vamos preparar o nosso projeto e a cena da unidade:
1. Iniciar a Unidade.
2. Selecione **New** (Novo).
4. Certifique-se de que o **3D** está selecionado.
5. Nomeie o seu projeto e introduza uma **localização**de salvamento.
6. Selecione **Create project** (Criar projeto).
7. Guarde a cena padrão vazia para um novo ficheiro utilizando: **Guardar**  >  **ficheiros Como**.
8. Nomeie a nova cena **Principal** e pressione o botão **Guardar.**

**Configurar as definições do projeto**

Vamos agora definir algumas definições de projeto de Unidade que nos ajudam a direcionar o Windows Holographic SDK para o desenvolvimento.

Primeiro, vamos definir definições de qualidade para a nossa aplicação.
1. **Selecione a**  >  qualidade das**definições do projeto de**  >  **Quality** edição
2. Na coluna sob o logótipo da **Windows Store,** clique na seta na linha **Predefinido** e selecione **Very Low**. Saberá que a definição é aplicada corretamente quando a caixa na coluna **Windows Store** e na linha **Very Low** estiver verde.

Precisamos configurar a nossa app Unidade com uma visão imersiva, em vez de uma vista 2D. Podemos criar uma visão imersiva, permitindo o suporte de Realidade Virtual na Unidade direcionando o Windows 10 SDK.
1. Ir para **editar**  >  **o leitor de definições de**  >  **projeto**.
2. No **Painel de Inspetor** para **Definições de Jogadores,** selecione o ícone **Windows.**
3. Expandir o grupo **definições XR.**
4. Na secção **de renderização,** consulte a caixa de verificação **suportada por realidade virtual** para adicionar uma nova lista de **SDKs de realidade virtual.**
5. Verifique se **a Realidade Mista do Windows** aparece na lista. Caso contrário, selecione o **+** botão na parte inferior da lista e escolha a **Realidade Mista do Windows**.

> [!NOTE]
> Se não vir o ícone do Windows, verifique duas vezes para se certificar de que selecionou o Backend script do Windows .NET antes da instalação. Caso contrário, poderá ser necessário reinstalar a Unidade com a instalação correta do Windows.

**Verificar configuração de backend de script**
1. Ir para **editar**  >  **o Leitor de Definições de**  >  **Projeto** (ainda pode ter o **Jogador** aberto a partir do passo anterior).
2. No **Painel de Inspetor** para **Definições de Jogadores,** selecione o ícone **Windows Store.**
3. Na secção de Configuração **de Outras Definições,** certifique-se de que **o Backend scripting** está definido para **IL2CPP**.

**Definir capacidades**
1. Ir para **editar**  >  **o Leitor de Definições de**  >  **Projeto** (ainda pode ter o **Jogador** aberto a partir do passo anterior).
2. No **Painel de Inspetor** para **Definições de Jogadores,** selecione o ícone **Windows Store.**
3. Na secção de Configuração **de Definições de Publicação,** consulte **InternetClientServer** e **SpatialPerception**.

**Configurar a câmara virtual principal**
1. No **Painel Hierárquico,** selecione **Câmara Principal**.
2. No **Inspetor,** fixou a sua posição de transformação em **0,0,0**.
3. Encontre a propriedade **Clear Flags** e mude o dropdown da **Skybox** para **a Cor Sólida.**
4. Clique no campo **de fundo** para abrir um apanhador de cores.
5. Set **R, G, B e A** a **0**.
6. **Selecione Adicionar Componente** e procure e adicione o **Colisor de Mapeamento Espacial**.

**Criar o nosso script**
1. No **painel de projeto,** crie uma nova pasta, **Scripts,** sob a pasta **Ativos.**
2. Clique com o botão direito na pasta e, em seguida, **selecione Criar >**, **C# Script**. Título-lo **AzureSpatialAnchorsScript**.
3. Ir para **o GameObject**  ->  **Criar Vazio.**
4. Selecione-o, e no **Inspetor** rebate-o de **GameObject** para **MixedRealityCloud**. **Selecione Adicionar Componente** e procure e adicione o **AzureSpatialAnchorsScript**.

**Criar a esfera pré-fabricada**
1. Vá para a Esfera de Objeto 3D **do GameObject.**  ->  **3D Object**  ->  **Sphere**
2. No **Inspetor,** fixou a sua escala para **0,25, 0,25, 0,25**.
3. Encontre o objeto **Esfera** no painel **da hierarquia.** Clique nele e arraste-o para a pasta **Ativos** no painel **de projeto.**
4. Clique à direita e **elimine** a esfera original que criou no painel **de hierarquia.**

Deve agora ter um prefácio de esfera no painel do **projeto.**

## <a name="trying-it-out"></a>Experimentando
Para testar que está tudo a funcionar, construa a sua app em **Unidade** e implemente-a a partir do **Visual Studio.** Siga o Capítulo 6 do [ **MR Basics 100: Começar com** ](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) o curso de Unidade para o fazer. Devias ver o ecrã de partida da Unidade e, em seguida, um ecrã claro.

## <a name="place-an-object-in-the-real-world"></a>Coloque um objeto no mundo real
Vamos criar & colocar um objeto usando a sua aplicação. Abra a solução Visual Studio que criamos quando [implementamos a nossa aplicação.](#trying-it-out)

Em primeiro lugar, adicione as seguintes importações na `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs` sua:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Em seguida, adicione as seguintes variáveis de membros na sua `AzureSpatialAnchorsScript` classe:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-47,53-57,65-84)]

Antes de continuarmos, precisamos definir a esfera pré-fabricada que criamos na nossa esfera Variável membroprefab. Volta para a **Unidade.**
1. In **Unitity**, selecione o objeto **MixedRealityCloud** no painel **de hierarquia.**
2. Clique no prefácio **da Esfera** que guardou no painel **de projeto.** Arraste a **Esfera** em que clicou na área **do Prefab da Esfera** sob o Script de **Âncoras Espaciais de Azure (Script)** no painel do **Inspetor.**

Agora deve ter a **Esfera** definida como o pré-fabricado no seu script. Construa a partir da **Unidade** e, em seguida, abra novamente a solução **de Estúdio Visual** resultante, como acabou de fazer em [Experimentá-la.](#trying-it-out)

No **Estúdio Visual,** abra-se `AzureSpatialAnchorsScript.cs` novamente. Adicione o seguinte código no seu `Start()` método. Este código liga-se `GestureRecognizer` , que ligará `HandleTap` quando detetar uma torneira de ar.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-95,98&highlight=4-10)]

Temos agora de adicionar o seguinte `HandleTap()` método `Update()` abaixo. Fará um elenco de raios e terá um ponto de sucesso para colocar uma esfera.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-283,305-306,310-318)]

Agora precisamos de criar a esfera. A esfera será inicialmente branca, mas este valor será ajustado mais tarde. Adicione o seguinte `CreateAndSaveSphere()` método:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-331,396)]

Execute a sua aplicação no **Visual Studio** para validá-la mais uma vez. Desta vez, toque no ecrã para criar & coloque a sua esfera branca sobre a superfície à sua escolha.

## <a name="set-up-the-dispatcher-pattern"></a>Configurar o padrão do despachante

Ao trabalhar com a Unidade, todas as APIs de Unidade (por exemplo, APIs que usa para fazer atualizações de UI) precisam de acontecer no fio principal. No código que escreveremos, no entanto, recebemos chamadas noutras linhas. Queremos atualizar a UI nestas chamadas, por isso precisamos de uma maneira de passar de um fio lateral para o fio principal. Para executar o código na linha principal a partir de um fio lateral, usaremos o padrão do despachante.

Vamos adicionar uma variável de membro, `dispatchQueue` que é uma Fila de Ações. Vamos empurrar as ações para a fila, e depois dequear e executar as Ações no fio principal.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=43-56&highlight=6-9)]

Em seguida, vamos adicionar uma forma de adicionar uma Ação à Fila. Adicione `QueueOnUpdate()` logo a `Update()` seguir:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=112-122)]

Podemos utilizar o loop Update() para verificar se existe uma fila de ação. Se assim for, descodionaremos a Ação e a executaremos.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=100-110&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Obtenha o Azure Spatial Anchors SDK

## <a name="via-unity-package-manager-upm-package"></a>[Pacote via Gestor de Pacotes de Unidade (UPM)](#tab/UPMPackage)

Este método é compatível com as versões Unidade 2019.1+.

### <a name="add-the-registry-to-your-unity-project"></a>Adicione o registo ao seu projeto de Unidade

1. Num explorador de ficheiros, navegue para a pasta do seu projeto `Packages` Unidade. Abra o arquivo manifesto do `manifest.json` projeto, em um editor de texto.
2. No topo do ficheiro, ao mesmo nível da `dependencies` secção, adicione a seguinte entrada para incluir o registo Azure Spatial Anchors ao seu projeto. A `scopedRegistries` entrada diz à Unidade onde procurar os pacotes Azure Spatial Anchors SDK.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-19&highlight=2-10)]

### <a name="add-the-sdk-package-to-your-unity-project"></a>Adicione o pacote SDK ao seu projeto Unidade

1. Adicione uma entrada com o nome do pacote Azure Spatial Anchors Windows SDK ( `com.microsoft.azure.spatial-anchors-sdk.windows` ) e a versão do pacote na secção do manifesto do seu `dependencies` projeto. Veja um exemplo abaixo.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-20&highlight=12)]

2. Guarde e feche o `manifest.json` ficheiro. Quando regressar à Unidade, a Unidade deve detetar automaticamente a alteração manifesto do projeto e recuperar os pacotes especificados. Pode expandir a `Packages` pasta na sua visão do Projeto para verificar se os pacotes certos foram importados.

## <a name="via-unity-asset-package"></a>[Pacote de ativos de unidade](#tab/UnityAssetPackage)

> [!WARNING]
> A distribuição do Pacote de Ativos de Unidade do Azure Spatial Anchors SDK será depreciada após a versão SDK 2.5.0.

Vamos descarregar o Azure Spatial Anchors SDK. Aceda à página de lançamentos do [Azure Spatial Anchors GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases). Under **Assets**, descarregue o **AzureSpatialAnchors.unitypackage**. Em Unidade, vá a **Ativos,** selecione Pacote Personalizado **de Pacote de**  >  **Importação...**. Navegue para o pacote e selecione **Open**.

Na nova janela **do Pacote de Unidade de Importação** que aparece, desescolh os **Plugins** e, em seguida, selecione **Import** no canto inferior direito.

---

Na sua solução **Visual Studio,** adicione a seguinte importação na `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs` sua:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Em seguida, adicione as seguintes variáveis de membro na sua `AzureSpatialAnchorsScript` classe:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=53-68&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Prenda uma âncora espacial Azure local à âncora local

Vamos preparar a CloudSpatial Anchor da Azure. Começaremos por adicionar o seguinte método dentro da `InitializeSession()` sua `AzureSpatialAnchorsScript` aula. Uma vez convocado, irá garantir que uma sessão de Âncoras Espaciais Azure seja criada e devidamente inicializada durante o arranque da sua app.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=179-208,211-215)]

Agora precisamos escrever código para lidar com as chamadas dos delegados. Vamos dar-lhes mais à medida que continuamos.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=217-232)]

Agora, vamos ligar o seu `initializeSession()` método ao seu `Start()` método.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-98&highlight=12)]

Por fim, adicione o seguinte código ao seu `CreateAndSaveSphere()` método. Vai ligar uma âncora espacial local a Azure à esfera que estamos colocando no mundo real.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-344,396&highlight=14-25)]

Antes de prosseguir, terá de criar uma conta Azure Spatial Anchors para obter o identificador, chave e domínio da conta. Se ainda não tiver esses valores, siga a secção seguinte para os obter.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Faça o upload da sua âncora local para a nuvem

Assim que tiver a sua conta Azure Spatial Anchors Identifier, Key e Domain, vá colar o `Account Id` , o in , e o pol `SpatialAnchorsAccountId` `Account Key` `SpatialAnchorsAccountKey` `Account Domain` `SpatialAnchorsAccountDomain` .

Finalmente, vamos juntar tudo. No seu `CreateAndSaveSphere()` método, adicione o seguinte código. Invocará o `CreateAnchorAsync()` método assim que a sua esfera for criada. Uma vez que o método retorna, o código abaixo atualizará a sua esfera uma última vez, alterando a sua cor para azul.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-397&highlight=26-77)]

Execute o seu aplicativo a partir do **Visual Studio** mais uma vez. Mova-se à volta da cabeça e toque de ar para colocar a esfera. Assim que tivermos quadros suficientes, a esfera ficará amarela, e o upload da nuvem começará. Assim que o upload terminar, a sua esfera ficará azul. Opcionalmente, também pode utilizar a [janela Saída](https://docs.microsoft.com/visualstudio/ide/reference/output-window) enquanto desormes no **Estúdio Visual** para monitorizar as mensagens de registo que a sua aplicação está a enviar. Certifique-se de que implementa a `Debug` configuração da sua aplicação a partir do Visual Studio para ver as mensagens de registo. Pode ver o `RecommendedForCreateProgress` , e assim que o upload estiver completo, poderá ver o identificador de âncora devolvido da nuvem.

> [!NOTE]
> Se obtém "DllNotFoundException: Incapaz de carregar DLL 'AzureSpatialAnchors": O módulo especificado não foi encontrado.» deve **limpar** e construir novamente **a** sua solução.

## <a name="locate-your-cloud-spatial-anchor"></a>Localize a sua âncora espacial em nuvem

Uma âncora é enviada para a nuvem, estamos prontos para tentar localizá-la novamente. Vamos adicionar o seguinte código no seu `HandleTap()` método. Este código:

* Chamada `ResetSession()` , que irá parar e remover a nossa esfera azul existente do `CloudSpatialAnchorSession` ecrã.
* Inicializar `CloudSpatialAnchorSession` novamente. Fazemos isto para termos a certeza que a âncora que vamos localizar vem da nuvem em vez de ser a âncora local que criámos.
* Crie um **Observador** que procure a âncora que enviamos para Azure Spatial Anchors.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-311&highlight=13-31,35-36)]

Vamos agora adicionar os `ResetSession()` nossos e `CleanupObjects()` métodos. Pode colocá-los abaixo `QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=124-177)]

Agora precisamos ligar o código que será invocado quando a âncora que estamos a pedir está localizada. No interior `InitializeSession()` , adicione as seguintes chamadas:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=206-212&highlight=4-5)]


Agora permite adicionar código que irá criar & colocar uma esfera verde uma vez que o CloudSpatialAnchor está localizado. Também irá permitir a toscagem do ecrã novamente, para que possa repetir todo o cenário mais uma vez: criar outra âncora local, carregá-la e localizá-la novamente.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=234-271)]

Já está. Execute a sua aplicação a partir do **Visual Studio** uma última vez para experimentar todo o cenário de ponta a ponta. Mova-se em torno do seu dispositivo, e coloque a sua esfera branca. Então, continue a mover a cabeça para capturar dados ambientais até que a esfera fique amarela. A tua âncora local será carregada, e a tua esfera ficará azul. Por fim, toque mais uma vez no ecrã para remover a âncora local e inicie uma consulta para a sua contraparte na nuvem. Continue a mover o seu dispositivo até que a sua âncora espacial em nuvem esteja localizada. Uma esfera verde deve aparecer na localização correta, e pode repetir todo o cenário novamente.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]
