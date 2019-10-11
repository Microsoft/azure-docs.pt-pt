---
title: Início rápido do projeto acústicos com inreal
titlesuffix: Azure Cognitive Services
description: Use o conteúdo de exemplo para experimentar com controles de design acústicos do projeto em inreal e WWise e implantar no Windows desktop.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d3afcded894f72626a4f24bcbe85c34ac1329c29
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242911"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Início rápido do projeto acústicos inreals/WWise
Neste guia de início rápido, você experimentará os controles de design acústicos do projeto usando o conteúdo de exemplo para o mecanismo inreal e o WWise.

Requisitos de software para usar o conteúdo de exemplo:
* [Mecanismo inreal](https://www.unrealengine.com/) 4,22
* [AudioKinetic WWise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>Baixar o pacote de exemplo
Baixe o [pacote de amostra do projeto acústica inreal e WWise](https://www.microsoft.com/download/details.aspx?id=58090). O pacote de exemplo contém:
- Projeto de mecanismo inreal
- Projeto WWise para o projeto inreal
- Plug-in WWise acústica do projeto

## <a name="set-up-the-project-acoustics-sample-project"></a>Configurar o projeto de exemplo acústicos do projeto
Primeiro, instale o plug-in acústica do projeto no WWise. Em seguida, implante os binários WWise no projeto inreal. Em seguida, ajuste o plug-in WWise inreal para dar suporte a acústicas do projeto.

### <a name="install-the-project-acoustics-wwise-plug-in"></a>Instalar o plug-in WWise acústica do projeto
Abra o iniciador WWise. Na guia **plugins** , em **instalar novos plug-ins**, selecione **Adicionar do diretório**. Escolha o diretório *AcousticsWwisePlugin\ProjectAcoustics* que foi incluído no pacote que você baixou.

![A opção de instalar o plug-in WWise no iniciador do WWise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Adicionar binários WWise ao projeto de exemplo irreal do projeto acústica
1. No iniciador do WWise, selecione a guia **mecanismo inreal** . 
1. Selecione o menu ""/"(ícone) ao lado de **projetos de mecanismo recentes não reais**e selecione **procurar projeto**. Abra o arquivo Project *. uproject* de exemplo inreal no pacote *AcousticsSample\AcousticsGame\AcousticsGame.uproject*.

   ![A guia inreal no iniciador WWise](media/wwise-unreal-tab.png)

3. Ao lado do projeto de exemplo acústica do projeto, selecione **integrar WWise no projeto**.

   ![O iniciador WWise mostra o projeto não real de jogo acústicos com a opção integrar realçada.](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Estender a funcionalidade de plug-in WWise inreal
O plug-in do projeto acústica não real precisa de comportamento adicional exposto da API de plug-in WWise inreal. Execute o arquivo em lotes que veio com o plug-in acústicos irreais do projeto para automatizar essas modificações.
* Dentro de *AcousticsGame\Plugins\ProjectAcoustics\Resources*, execute *PatchWwise. bat*.

    ![O script para corrigir o projeto WWise realçado em uma janela do Windows Explorer](media/patch-wwise-script.png)

* Se você não tiver o SDK do DirectX instalado: dependendo da versão do WWise que você está usando, talvez seja necessário comentar a linha que contém `DXSDK_DIR` em *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![O editor de código mostrando "DXSDK" comentado](media/directx-sdk-comment.png)

* Se você compilar usando o Visual Studio 2019: para contornar um erro de vinculação com o WWise, altere manualmente o valor padrão *VSVersion* em *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* para **vc150**:

    ![O editor de código com VSVersion foi alterado para "vc150"](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Abra o projeto inreal 
Quando você abrir o projeto inreal, ele solicitará que você reconstrua os módulos. Selecione **Sim**.

Se a abertura do projeto falhar devido a falhas de compilação, verifique se você instalou o plug-in WWise acústica do projeto na mesma versão do WWise que foi usada no projeto de exemplo acústicos do projeto.

Se você usar uma versão do [AudioKinetic WWise](https://www.audiokinetic.com/products/wwise/) anterior à versão 2019,1, não poderá gerar bancos de som usando o projeto de exemplo acústica do projeto. Você precisa integrar o WWise versão 2019,1 ao projeto de exemplo.

## <a name="experiment-with-project-acoustics-design-controls"></a>Experimentar com controles de design acústicos do projeto
Ouça como a cena soa selecionando o botão reproduzir no editor inreal. Use as teclas W, A, S e D e o mouse para se movimentar. Para ver os atalhos de teclado para controles adicionais, selecione F1.

As informações a seguir descrevem algumas atividades de design a serem experimentadas.

### <a name="modify-occlusion-and-transmission"></a>Modificar oclusão e transmissão
Há controles de design acústicos de projeto por origem em cada ator de som não real.

![Os controles de design acústicos do editor inreal](media/demo-scene-sound-source-design-controls.png)

Se o multiplicador **oclusão** for maior que 1 (o padrão é 1), oclusão será exagerado. Uma configuração menor que 1 torna o efeito oclusão mais sutil.

Para habilitar a transmissão por parede, mova o controle deslizante de **transmissão (DB)** para longe do seu nível mais baixo.

### <a name="modify-wetness-for-a-source"></a>Modificar umidade para uma origem
Para alterar a rapidez com que umidade alterações com distância, use a **detorção de distância perceptiva**. O projeto acústico computa níveis úmida em todo o espaço por meio da simulação. Os níveis variam sem problemas com distância e fornecem indicações de distância perceptiva. Para exagerar esse efeito, aumente os níveis úmida relacionados à distância para aumentar a distorção de distância. Valores de detorção menores que 1 tornam a alteração reverberation baseada em distância mais sutil. Você também pode fazer ajustes mais precisos a esse efeito por meio da configuração **umidade (DB)** .

Para aumentar o tempo de decaimento ao longo do espaço, ajuste **decaimento escala de tempo**. Considere um caso em que o resultado da simulação é um tempo de decaimento de 1,5 segundos. Definir a **escala de tempo de decaimento** como 2 resulta em um tempo de decaimento de 3 segundos aplicado à origem.

### <a name="modify-distance-based-attenuation"></a>Modificar atenuação baseada em distância
O plug-in do mixer do WWise do projeto acústica respeita a atenuação baseada em distância por fonte que é incorporada ao WWise. Alterar essa curva altera o nível de caminho seco. O plug-in de acústica do projeto ajustará o nível úmida para manter a mistura úmida/seca especificada pelos controles de simulação e design.

![O painel de curva de atenuação de WWise mostrando a atenuação vai para 0 antes do limite de simulação](media/demo-sounds-attenuation.png)

O projeto acústica as computações em uma caixa de "região de simulação" centralizada em todo o local do jogador simulado. Os ativos acústicos no pacote de exemplo foram inclusas com um raio de região de simulação de 45 metros. As atenuações foram projetadas para se ajustarem a 0 antes de 45 metros. Embora essa queda não seja um requisito estrito, ela transporta a advertência de que apenas a geometria dentro de 45 metros do ouvinte occlude sons.

## <a name="next-steps"></a>Passos seguintes
* [Integre o plug-in acústica do projeto](unreal-integration.md) em seu projeto inreal.
* [Crie uma conta do Azure](create-azure-account.md) para seu próprio prepara.
