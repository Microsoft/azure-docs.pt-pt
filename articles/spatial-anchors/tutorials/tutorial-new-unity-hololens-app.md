---
title: 'Tutorial: Criar uma nova app HoloLens Unity'
description: Neste tutorial, aprende-se a criar uma nova app HoloLens Unity utilizando âncoras espaciais Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 07/05/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e1abb759c80e770f1e650c232b6b2e21232b7e6f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75457718"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Tutorial: Instruções passo a passo para criar uma nova app hololens unidade usando âncoras espaciais Azure

Este tutorial irá mostrar-lhe como criar uma nova app HoloLens Unity com Âncoras Espaciais Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem:

1. Uma máquina Windows com <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017+</a> instalada com a carga de trabalho de desenvolvimento da **Plataforma Universal Windows** e o componente Windows **10 SDK (10.0.18362.0 ou mais recente)** e <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a>.
2. A [extensão do estúdio visual C++/WinRT (VSIX)](https://aka.ms/cppwinrt/vsix) para Estúdio Visual deve ser instalada a partir do [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Um dispositivo HoloLens com [modo de desenvolvimento](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) ativado. Este artigo requer um dispositivo HoloLens com a [Atualização do Windows 10 de outubro de 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (também conhecida como RS5). Para atualizar a mais recente versão do HoloLens, abra a aplicação **Definições,** vá ao **Update & Security**e, em seguida, selecione o botão **'Verificar' para atualizações.**

## <a name="getting-started"></a>Introdução

Primeiro vamos preparar o nosso projeto e cena de unidade:
1. Inicie a Unidade.
2. Selecione **New**.
4. Certifique-se de que o **3D** está selecionado.
5. Nomeie o seu projeto e introduza um **local**de salvamento .
6. Clique em **Criar o projeto**.
7. Guarde a cena padrão vazia para um novo ficheiro utilizando: **Guardar ficheiros** > **Como**.
8. Nomeie a nova cena **Principal** e prima o botão **Guardar.**

**Configurar as definições do projeto**

Vamos agora definir algumas definições do projeto De unidade que nos ajudam a direcionar o SDK holográfico do Windows para o desenvolvimento.

Em primeiro lugar, permite definir definições de qualidade para a nossa aplicação.
1. **Selecione Editar** > **Qualidade** **de Definições** > de Projeto
2. Na coluna sob o logótipo da **Windows Store,** clique na seta na linha **Predefinido** e selecione **Very Low**. Saberá que a definição é aplicada corretamente quando a caixa na coluna **da Windows Store** e a linha Very **Low** estiverem verdes.

Precisamos que a Unidade saiba que a app que estamos a tentar exportar deve criar uma visão imersiva em vez de uma visão 2D. Criamos uma visão imersiva, permitindo o suporte de Realidade Virtual na Unidade visando o Windows 10 SDK.

1. Vá **editar** > **o leitor**de**definições** > de projeto .
2. No **Painel de Inspetor** **estoque**para definições de jogadores, selecione o ícone **da Loja do Windows.**
3. Expanda o grupo **de Definições XR.**
4. Na secção **renderização,** verifique a caixa de verificação **Suportada** pela Realidade Virtual para adicionar uma nova lista **do Virtual Reality SDK.**
5. Verifique se o **Windows Mixed Reality** aparece na lista. Caso contrário, **+** selecione o botão na parte inferior da lista e escolha **a Realidade Mista do Windows**.

> [!NOTE]
> Se não vir o ícone da Windows Store, verifique duas vezes se selecionou o Backend de Scripts Windows Store .NET antes da instalação. Caso contrário, poderá ter de reinstalar a Unidade com a instalação correta do Windows.

**Verifique a configuração do backend de script**
1. Vá para **editar** > **o Jogador** de**Definições** > de Projeto (ainda pode ter **o Jogador** aberto a partir do passo anterior).
2. No **Painel de Inspetor** **estoque**para definições de jogadores, selecione o ícone **da Loja do Windows.**
3. Na secção **De Configuração de Outras Definições,** certifique-se de que **o backend de scriptestá** definido para **IL2CPP**.

**Definir capacidades**
1. Vá para **editar** > **o Jogador** de**Definições** > de Projeto (ainda pode ter **o Jogador** aberto a partir do passo anterior).
2. No **Painel de Inspetor** **estoque**para definições de jogadores, selecione o ícone **da Loja do Windows.**
3. Na secção de configuração de **configurações de publicação,** verifique **o InternetClientServer** e **o SpatialPerception**.

**Configurar a câmara virtual principal**
1. No **Painel da Hierarquia, selecione** **Câmara Principal**.
2. No **Inspetor,** fixou a sua posição de transformação em **0,0,0**.
3. Encontre a propriedade **Clear Flags** e mude a queda da **Skybox** para **a Solid Color**.
4. Clique no campo **Background** para abrir um apanhador de cores.
5. Set **R, G, B e A** a **0**.
6. **Selecione Adicionar Componente** e procurar e adicionar o Collider de **Mapeamento Espacial**.

**Crie o nosso script**
1. No painel **do Projeto,** crie uma nova pasta, **Scripts,** sob a pasta **Assets.**
2. Clique na pasta e, em seguida, selecione **Criar >**, **C# Script**. Título-lo **AzureSpatialAnchorsScript**.
3. Vá ao **GameObject** -> **Create Empty**.
4. Selecione-o e no **Inspetor** mude o nome do **GameObject** para **o MixedRealityCloud**. **Selecione Adicionar Componente** e procurar e adicionar o **AzureSpatialAnchorsScript**.

**Criar a esfera prefab**
1. Ir para **GameObject** -> **3D Object** -> **Sphere**.
2. No **Inspetor,** fixou a sua escala para **0,25, 0,25, 0,25**.
3. Encontre o objeto **da Esfera** no painel da **Hierarquia.** Clique nele e arraste-o para a pasta **Assets** no painel **do Projeto.**
4. Clique à direita e **elimine** a esfera original que criou no painel da **Hierarquia.**

Agora deve ter um prefácio de esfera no seu painel **do Projeto.**

## <a name="trying-it-out"></a>Experimentando-o
Para testar que está tudo a funcionar, construa a sua app em **Unidade** e implemente-a no **Visual Studio**. Siga o Capítulo 6 do [ **MR Basics 100: Começar com** ](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) o curso de Unidade para o fazer. Devias ver o ecrã de início da Unidade e, em seguida, um ecrã claro.

## <a name="place-an-object-in-the-real-world"></a>Coloque um objeto no mundo real
Vamos criar & coloque um objeto usando a sua aplicação. Abra a solução Visual Studio que criamos quando [implementamos a nossa app.](#trying-it-out)

Em primeiro lugar, adicione `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`as seguintes importações no seu:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Em seguida, adicione as seguintes variáveis membros na sua `AzureSpatialAnchorsScript` classe:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-42,48-52,60-79)]

Antes de continuarmos, precisamos definir a esfera prefab que criamos na nossa esfera Variável membro Prefab. Volte para a **Unidade.**
1. Em **Unidade**, selecione o objeto **MixedRealityCloud** no painel **da Hierarquia.**
2. Clique no prefácio **da Esfera** que guardou no painel do **Projeto.** Arraste a **Esfera** em que clicou na área **prefab da Esfera** sob o Script de **Âncoras Espaciais Azure (Script)** no painel do **Inspetor.**

Agora devias ter a **Esfera** definida como o prefácio do teu guião. Construa a partir da **Unidade** e, em seguida, abra novamente a solução de **Estúdio Visual** resultante, como acabou de fazer em [Experimentá-lo](#trying-it-out).

No **Estúdio Visual,** reabre-se. `AzureSpatialAnchorsScript.cs` Adicione o seguinte `Start()` código no seu método. Este código irá `GestureRecognizer`ligar- que detetará quando houver `HandleTap`uma torneira de ar e chamada .

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-90,93&highlight=4-10)]

Temos agora de adicionar `HandleTap()` o `Update()`seguinte método abaixo . Fará um ray cast e obterá um ponto de sucesso para colocar uma esfera.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-277,299-300,304-312)]

Precisamos agora de criar a esfera. A esfera será inicialmente branca, mas este valor será ajustado mais tarde. Adicione o `CreateAndSaveSphere()` seguinte método:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-325,390)]

Execute a sua aplicação a partir do **Visual Studio** para validá-la mais uma vez. Desta vez, toque no ecrã para criar & coloque a sua esfera branca sobre a superfície à sua escolha.

## <a name="set-up-the-dispatcher-pattern"></a>Configurar o padrão do despachante

Ao trabalhar com a Unidade, todas as APIs de Unidade, por exemplo APIs que usa para fazer atualizações de UI, precisam de acontecer no fio principal. No código que escreveremos, no entanto, recebemos chamadas noutras linhas. Queremos atualizar a UI nestas chamadas, por isso precisamos de uma maneira de passar de um fio lateral para o fio principal. Para executar o código na linha principal a partir de um fio lateral, usaremos o padrão do despachante.

Vamos adicionar uma variável membro, dispatchQueue, que é uma fila de ações. Vamos empurrar as Ações para a fila, e depois desfilar e executar as Ações no fio principal.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=38-51&highlight=6-9)]

Em seguida, vamos adicionar uma maneira de adicionar uma Ação à Fila. Adicione `QueueOnUpdate()` logo `Update()` a seguir:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=107-117)]

Vamos agora utilizar o loop update() para verificar se há uma ação em fila. Em caso afirmativo, vamos adiar a ação e executá-la.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=95-105&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Obtenha o Azure Spatial Anchors SDK

## <a name="via-unity-package"></a>[Através do Pacote unidade](#tab/UnityPackage)

Vamos agora baixar o Azure Spatial Anchors SDK. Vá à página de [lançamentos Do Azure Spatial Anchors GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases). Em Ativos, descarregue o **pacote AzureSpatialAnchors.unitypackage**. Em Unidade, vá ao **Assets,** clique em Pacote Personalizado **de Pacote de Importação...** > **Custom Package...**. Navegue para o pacote e selecione **Open**.

Na nova janela **do Pacote de Unidade de Importação** que aparece, desmarque os **Plugins** e, em seguida, clique em **Importar** no canto inferior direito.

Precisamos agora restaurar os pacotes Nuget para obter o Azure Spatial Anchors SDK. Construir a partir da **Unidade** e, em seguida, abrir e construir novamente a solução de **Estúdio Visual** resultante, como detalhado em [Experimentá-lo](#trying-it-out).

## <a name="via-nugetforunity"></a>[Via NuGetForUnity](#tab/NuGetForUnity)

Primeiro precisamos de instalar o NuGetForUnity. Vá à página de [lançamentos nuGetForUnity GitHub](https://github.com/GlitchEnzo/NuGetForUnity/releases). Under Assets, baixe o último **pacote NuGetForUnity.unitypackage**. Em Unidade, vá ao **Assets,** clique em Pacote Personalizado **de Pacote de Importação...** > **Custom Package...**. Navegue para o pacote e selecione **Open**. A unidade vai agora instalar a NugetForUnity. Se não vir uma nova queda **nuGet** em Unidade, poderá ter de clicar no âmbito dos**Ativos dos** **Projetos** > . Em seguida, selecione **Reimport All**.

Assim que tiver nuGetForUnity instalado, selecione **NuGet** > **Manage NuGet Packages**. Em seguida, procure microsoft.Azure.SpatialAnchors.Unity e selecione **Instalar**.

Precisamos agora de construir para obter o atual Azure Spatial Anchors SDK, já que o pacote NuGet que acabamos de descarregar apenas contém scripts de ajuda. Construir a partir da **Unidade** e, em seguida, abrir e construir novamente a solução de **Estúdio Visual** resultante, como detalhado em [Experimentá-lo](#trying-it-out).

---

Na sua solução **Visual Studio,** `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`adicione a seguinte importação na sua:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Em seguida, adicione as seguintes variáveis membro na sua `AzureSpatialAnchorsScript` classe:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=48-63&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Prenda uma âncora espacial azure local à âncora local

Vamos preparar a CloudSpatialAnchorSession da Azure Spatial AnchorSession. Começaremos por adicionar o `InitializeSession()` seguinte `AzureSpatialAnchorsScript` método dentro da sua aula. Uma vez chamado, irá garantir que uma sessão de Âncoras Espaciais Azure seja criada e devidamente inicializada durante o arranque da sua app.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=174-202,205-209)]

Precisamos agora de escrever código para tratar das chamadas dos delegados. Vamos adicionar-lhes mais à medida que continuarmos.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=211-226)]

Agora, vamos ligar `initializeSession()` o seu `Start()` método ao seu método.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-93&highlight=12)]

Por fim, adicione o `CreateAndSaveSphere()` seguinte código no seu método. Vai anexar uma âncora espacial azure local à esfera que estamos colocando no mundo real.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-338,390&highlight=14-25)]

Antes de prosseguir mais, terá de criar uma conta Deâncoras Espaciais Azure Identifier e Key, se ainda não as tiver. Siga a seguinte secção para as obter.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Faça upload da sua âncora local para a nuvem

Assim que tiver a sua conta Deâncoras Espaciais Azure Identifier e Key, vá colar `Account Id` o `SpatialAnchorsAccountId` dentro e o `Account Key` em `SpatialAnchorsAccountKey`.

Finalmente, vamos juntar tudo. No `SpawnNewAnchoredObject()` seu método, adicione o seguinte código. Invocará o `CreateAnchorAsync()` método assim que a sua esfera for criada. Assim que o método voltar, o código abaixo realizará uma atualização final para a sua esfera, alterando a sua cor para azul.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-391&highlight=26-77)]

Execute a sua aplicação no **Visual Studio** mais uma vez. Mova-se em torno da sua cabeça e, em seguida, toque de ar para colocar a sua esfera. Assim que tivermos quadros suficientes, a esfera transformar-se-á em amarelo, e o carregamento da nuvem começará. Assim que o upload terminar, a tua esfera ficará azul. Opcionalmente, também pode utilizar a janela Output dentro do **Visual Studio** para monitorizar as mensagens de registo que a sua aplicação está a enviar. Poderá assistir ao recomendado para criar progresso, bem como o identificador de âncora que a nuvem retorna assim que o upload estiver concluído.

> [!NOTE]
> Se tiver "DllNotFoundException: Incapaz de carregar DLL 'AzureSpatialAnchors': O módulo especificado não foi encontrado.", deve **limpar** e **construir** novamente a sua solução.

## <a name="locate-your-cloud-spatial-anchor"></a>Localize a sua âncora espacial em nuvem

Uma a tua âncora está carregada para a nuvem, estamos prontos para tentar localizá-la outra vez. Vamos adicionar o seguinte código `HandleTap()` ao seu método. Este código irá:

* Ligue `ResetSession()`, que `CloudSpatialAnchorSession` irá parar a esfera azul existente do ecrã.
* Inicialize `CloudSpatialAnchorSession` de novo. Fazemos isto, por isso temos a certeza que a âncora que vamos localizar vem da nuvem em vez de ser a âncora local que criámos.
* Crie um **Observador** que procure a âncora que enviamos para âncoras espaciais Azure.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-305&highlight=13-31,35-36)]

Vamos agora adicionar `ResetSession()` os `CleanupObjects()` nossos e métodos. Pode colocá-los abaixo`QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=119-172)]

Precisamos ligar o código que será invocado quando a âncora que estamos consultando está localizada. No `InitializeSession()`interior, adicione as seguintes chamadas:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=200-206&highlight=4-5)]


Agora vamos adicionar código que irá criar & colocar uma esfera verde uma vez que o CloudSpatialAnchor está localizado. Também permitirá a escuta do ecrã novamente, para que possa repetir todo o cenário mais uma vez: criar outra âncora local, carregá-la e localizá-la novamente.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=228-265)]

Já está! Execute a sua aplicação a partir do **Visual Studio** uma última vez para experimentar todo o cenário de ponta a ponta. Mova-se em torno do seu dispositivo, e coloque a sua esfera branca. Em seguida, continue a mover a cabeça para capturar dados ambientais até que a esfera fique amarela. A tua âncora local será carregada, e a tua esfera ficará azul. Por fim, toque no ecrã mais uma vez, para que a sua âncora local seja removida, e depois vamos consultar a sua congénere de nuvem. Continue a mover o seu dispositivo até que a sua âncora espacial em nuvem esteja localizada. Uma esfera verde deve aparecer no local correto, e pode enxaguar & repetir todo o cenário novamente.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]