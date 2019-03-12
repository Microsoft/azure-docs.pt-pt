---
title: Introdução ao projeto Acoustics
titlesuffix: Azure Cognitive Services
description: Este guia de início rápido mostram como integrar o plug-in no seu projeto Unity, inserir sua cena e aplicar o acoustics a origens de som.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 9569b161dd5c6c79c5333d585f9ab4fcdb55fa97
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533991"
---
# <a name="getting-started-with-project-acoustics"></a>Introdução ao projeto Acoustics
Este guia de início rápido mostram como integrar o plug-in no seu projeto Unity, inserir sua cena e aplicar o acoustics a origens de som. Neste início rápido, terá primeiro de criar uma [conta do Azure batch](create-azure-account.md). Este guia assume um pouco familiarizado com o Unity.

## <a name="download-the-plugin"></a>Transfira o plug-in
Registe-se [aqui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) para aderir à pré-visualização do Designer.

## <a name="supported-platforms-for-quickstart"></a>Plataformas suportadas para o início rápido
* [Unity 2018.2 +](https://www.unity3d.com)
  * Requer a definição de seu projeto o **.NET 4.x equivalente** versão de tempo de execução de scripts 
  * Requer o editor do Unity baseado no Windows

## <a name="import-the-plugin"></a>Importar o plug-in
Importe o acoustics UnityPackage ao seu projeto. 
* No Unity, aceda a **ativos > Importar pacote > pacote personalizado...**

![Importar pacote](media/ImportPackage.png)  

* Escolha **MicrosoftAcoustics.unitypackage**

Se estiver a importar o plug-in num projeto existente, o seu projeto pode já ter uma **mcs.rsp** ficheiro na raiz do projeto, que especifica as opções para o compilador c#. Terá de mesclar o conteúdo desse ficheiro com o arquivo de mcs.rsp que vem com o plug-in do projeto Acoustics.

## <a name="enable-the-plugin"></a>Ativar o plug-in
A parte de criar o Kit de ferramentas de acoustics requer a versão de tempo de execução do script 4.x do .NET. Importação do pacote irá atualizar as definições de leitor de Unity. Reinicie o Unity para esta definição tenha efeito.

![Definições do leitor](media/PlayerSettings.png)

![.NET 4.5](media/Net45.png)

## <a name="create-a-navigation-mesh"></a>Criar uma malha de navegação
Utilize a norma [fluxo de trabalho do Unity](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html) para criar uma malha de navegação para o seu projeto. Para obter informações sobre como utilizar o seu próprio malhas de navegação, consulte a [criar a interface do Usuário percorrer](bake-ui-walkthrough.md).

## <a name="mark-static-meshes-for-acoustics"></a>Marcar as malhas estáticas para acoustics
Abrir a janela de acoustics usando **janela > Acoustics** no Unity. Esta janela pode ser encaixada ao lado o Inspetor de.

![Janela de Acoustics aberto](media/WindowAcoustics.png)

Na janela de hierarquia do Unity, anule a seleção todos os itens selecionados. Na acoustics **objeto** separador, clique na caixa de verificação "Acoustics Geometry" para marcar todos os entrelaçamentos e terrains em seu cenário como geometria acoustics.

Sobre o **materiais** separador, atribuir os materiais acústicos a materiais usados em seu cenário. O **predefinido** material tem absorption equivalente a concreto. Para obter mais informações sobre como especificar as suas propriedades de materiais, consulte a [página do processo de design](design-process.md).

![Separador de materiais](media/MaterialsTab.png)

## <a name="preview-the-probes"></a>As sondas de pré-visualização
Sobre o **sondas** separador, clique em **Calculate**. Esse cálculo poderá demorar alguns minutos, dependendo do tamanho de cena. Quando o cálculo é concluído, verá mais na vista de cena, que marcar as localizações para a simulação de acoustics, chamado "pontos de sonda" de vírgula flutuante. Se obtiver perto o bastante para um objeto na janela de cena, também pode ver o voxelization de cena. O verde voxels deve alinhar com os objetos que marcou como geometria. Os pontos de sonda e apresenta voxel pode ser ativada no menu Gizmos no canto superior direito da vista de cena.

![GizmosPreview](media/BakePreviewWithGizmos.png)

## <a name="bake-the-scene"></a>Inserir a cena
Na **colocá** separador, introduza as credenciais do Azure e clique em **inserir**. Se não tiver uma conta do Azure Batch, veja [estas instruções para a nossa configuração de conta recomendada](create-azure-account.md).
Quando o criar estiver concluída, o ficheiro de dados serão automaticamente transferido para o **ativos/AcousticsData** diretório no seu projeto.

## <a name="set-up-audio-runtime-dsp"></a>Configurar o tempo de execução do áudio DSP
Vamos incorporar o tempo de execução áudio DSP para acoustics no framework de spatializer do Unity e integrá-la com base em HRTF spatialization. Para ativar o processamento de acoustics, mude para o **Microsoft Acoustics** spatializer indo até **Editar > definições do projeto > áudio**e selecione **Microsoft Acoustics** como o **Plug-in do Spatializer** para o seu projeto. Além disso, certifique-se de que o **tamanho da memória intermédia DSP** está definido para melhor desempenho.

![Definições do projeto](media/ProjectSettings.png)  

![Projeto Acoustics Spatializer](media/ChooseSpatializer.png)

Abra o Mixer áudio (**janela > Mixer áudio**). Certifique-se de que tem pelo menos um Mixer, com um grupo. Se não o fizer, clique no botão "+" à direita da **Mixers**. Com o botão direito na parte inferior da strip canal na secção efeitos e adicione a **Microsoft Acoustics Mixer** efeito. Tenha em atenção que apenas um Mixer de Acoustics de projeto é suportado por vez.

![Áudio Mixer](media/AudioMixer.png)

## <a name="set-up-the-acoustics-lookup-table"></a>Configurar a tabela de pesquisa acoustics
Arraste e largue os **Microsoft Acoustics** prefab a partir do painel de projeto em sua cena:

![Acoustics Prefab](media/AcousticsPrefab.png)

Clique nas **ProjectAcoustics** objeto do jogo e passa para o seu painel de inspetor. Especifique a localização do seu resultado de criar (o. ACE arquivo, na **ativos/AcousticsData**) ao arrastar-e-removê-lo para o script de Acoustics Manager ou ao clicar no botão círculo junto à caixa de texto.

![Acoustics Manager](media/AcousticsManager.png)  

## <a name="apply-acoustics-to-sound-sources"></a>Aplicar acoustics a origens de som
Crie uma origem de áudio. Clique na caixa de verificação na parte inferior do painel de um inspetor do AudioSource que diz **Spatialize**. Certifique-se **geográficos Blend** está definido como 3D completa.  

![Origem de áudio](media/AudioSource.png)

## <a name="apply-post-bake-design"></a>Aplicar o design de pós-criar
Pode anexar o script **AcousticsAdjust** a uma origem de som em seu cenário para habilitar os parâmetros de design de origem adicionais, clicando em **Add Component** e escolhendo **Scripts > Acoustics Ajustar**:

![AcousticsAdjust](media/AcousticsAdjust.png)

Também há parâmetros nos **Microsoft Acoustics Mixer**. Para obter mais informações sobre o design de pós-criar, ver [parâmetros de design](design-process.md).

## <a name="next-steps"></a>Passos Seguintes
* Experimente o [cena de exemplo](sample-walkthrough.md)
* Saiba mais sobre o conjunto completo de [inserir funcionalidades](bake-ui-walkthrough.md)
* Explore mais detalhadas [parâmetros de design](design-process.md)

