---
title: Início rápido do projeto acústicos com inreal
titlesuffix: Azure Cognitive Services
description: Usando o conteúdo de exemplo, experimente os controles de design acústicos do projeto em inreal e WWise e implante no Windows desktop.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 8809c80f2ecba4ea2b3e3d280be0c4ad81d78d37
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854837"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Início rápido do projeto acústicos inreals/WWise
Neste guia de início rápido, você experimentará os controles de design acústicos do projeto usando o conteúdo de exemplo fornecido para o mecanismo inreal e o WWise.

Requisitos de software:
* [Mecanismo inreal](https://www.unrealengine.com/) 4,21
* [AudioKinetic WWise](https://www.audiokinetic.com/products/wwise/) 2018.1.6

## <a name="download-the-sample-package"></a>Baixar o pacote de exemplo
Baixe o [pacote de exemplo de acústica do projeto acústicos não reais + WWise](https://www.microsoft.com/download/details.aspx?id=58090). O pacote de exemplo contém um projeto de mecanismo inreal, o projeto WWise para esse projeto inreal e o plug-in acústica WWise do projeto.

## <a name="set-up-the-project-acoustics-sample-project"></a>Configurar o projeto de exemplo acústicos do projeto
Para configurar o projeto de exemplo acústicos inreals/WWise do projeto, você precisará primeiro instalar o plug-in acústica do projeto no WWise. Em seguida, implante os binários do WWise no projeto inreal e ajuste o plug-in inreal do WWise para dar suporte a acústicas do projeto.

### <a name="install-the-project-acoustics-wwise-plugin"></a>Instalar o plugin acústicos WWise do projeto
Abra o iniciador do WWise e, na guia **plug-ins** , em **instalar novos plug-ins**, selecione **Adicionar do diretório**. Escolha o `AcousticsWwisePlugin\ProjectAcoustics` diretório que foi incluído no pacote que você baixou.

![Captura de tela do iniciador WWise mostrando a opção instalar plug-in WWise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Adicionar binários WWise ao projeto de exemplo irreal do projeto acústica
No iniciador do WWise, clique na guia **mecanismo inreal** e, em seguida, clique no menu de documentos ao lado de **projetos de mecanismo não reais recentes** e selecione **procurar projeto**. Abra o arquivo de projeto `.uproject` não real de exemplo no pacote. `AcousticsSample\AcousticsGame\AcousticsGame.uproject`

![Captura de tela da guia inreal do iniciador WWise](media/wwise-unreal-tab.png)

Em seguida, ao lado do projeto de exemplo acústicas do projeto, clique em **integrar WWise no projeto**.

![Captura de tela do iniciador WWise mostrando projeto não real de jogo acústicos](media/wwise-acoustics-game-project.png)

### <a name="extend-wwises-unreal-plugin-functionality"></a>Estender a funcionalidade de plug-in inreal do WWise
O plug-in acústico inreal do projeto requer um comportamento adicional exposto da API de plug-in WWise inreal. Execute o arquivo em lotes fornecido com o plug-in acústica inreal do projeto para automatizar essas modificações:
* Dentro `AcousticsGame\Plugins\ProjectAcoustics\Resources`do, `PatchWwise.bat`execute.

    ![Captura de tela da janela do Windows Explorer mostrando script para aplicar patch ao projeto WWise](media/patch-wwise-script.png)

* Se você não tiver o SDK do DirectX instalado, dependendo da versão do WWise que você está usando, talvez seja necessário comentar a linha que contém `DXSDK_DIR` em: `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Captura de tela do editor de código mostrando DXSDK comentado](media/directx-sdk-comment.png)

### <a name="open-the-unreal-project"></a>Abra o projeto inreal. 
Ele solicitará que você reconstrua módulos; clique em Sim.

>Se a abertura do projeto falhar em falhas de compilação, verifique se você instalou o plug-in acústica WWise do projeto para a mesma versão do WWise usada no projeto de exemplo acústica do projeto.

>Se não estiver usando [AudioKinetic WWise](https://www.audiokinetic.com/products/wwise/) 2018.1.6, será necessário regenerar os bancos de som antes que o áudio seja reproduzido no projeto de exemplo.

## <a name="experiment-with-project-acoustics-design-controls"></a>Experimentar com controles de design acústicos do projeto
Ouça o som da cena clicando no botão reproduzir no editor inreal. Na área de trabalho, use W, A, S, D e o mouse para percorrer. Para ver os atalhos de teclado para obter mais controlos, prima **F1**. Aqui estão algumas atividades de design para experimentar:

### <a name="modify-occlusion-and-transmission"></a>Modificar oclusão e transmissão
Há controles de design acústicos de projeto por origem em cada ator de som não real:

![Captura de tela de controles de design acústicos de editor inreal](media/demo-scene-sound-source-design-controls.png)

Se o multiplicador **oclusão** for maior que 1 (o padrão é 1), oclusão será exagerado. Configurá-lo com menos de 1 torna o efeito oclusão mais sutil.

Para habilitar a transmissão por parede, mova o controle deslizante de **transmissão (DB)** para fora do nível mais baixo. 

### <a name="modify-wetness-for-a-source"></a>Modificar umidade para uma origem
Para alterar a rapidez com que o umidade muda com a distância, use a detorção de **distância perceptiva**. O projeto acústico computa níveis úmida em todo o espaço da simulação, que varia de maneira tranqüila e fornece indicações de distância perceptiva. Aumentar o efeito de distância exagera o impacto ao aumentar os níveis úmida relacionados à distância. Valores de detorção menores que 1 tornam a alteração reverberation baseada em distância mais sutil. Esse efeito também pode ser ajustado em detalhes mais refinados ajustando o **umidade (DB)** .

Aumente o tempo de decaimento ao longo do espaço ajustando a **escala de tempo decaimento**. Considere o caso em que o resultado da simulação é um tempo de decaimento de 1,5 s. Definir a **escala de tempo de decaimento** como 2 resultará em um tempo de decaimento aplicado à fonte de 3 s.

### <a name="modify-distance-based-attenuation"></a>Modificar atenuação baseada em distância
O plug-in do mixer do WWise do projeto acústicos respeita a atenuação baseada em distância por fonte criada em WWise. A alteração dessa curva alterará o nível de caminho seco. O plug-in acústico do projeto ajustará o nível úmida para manter a mistura úmida molhado especificada por controles de simulação e design.

![Captura de tela do painel de curva de atenuação de WWise com atenuação indo para zero antes do limite de simulação](media/demo-sounds-attenuation.png)

O projeto acústicos faz a computação em uma caixa de "região de simulação" centralizada em todo o local do Player simulado. Os ativos acústicos no pacote de exemplo foram inclusas com um raio de região de simulação de 45 m, e as atenuações foram projetadas para se enquadrar em 0 antes de 45 m. Embora essa queda não seja um requisito estrito, ela transporta a advertência de que apenas a geometria dentro de 45 m do ouvinte irá occlude sons.

## <a name="next-steps"></a>Passos Seguintes
* [Integrar o plug-in acústica do projeto](unreal-integration.md) ao seu projeto não real
* [Criar uma conta do Azure](create-azure-account.md) para as suas próprias criações


