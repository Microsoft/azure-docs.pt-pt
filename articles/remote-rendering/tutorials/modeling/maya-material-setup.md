---
title: Configurar materiais de renderização fisicamente em Maia
description: Explica como configurar materiais de renderização fisicamente em Maya e exportá-los para o formato FBX.
author: FlorianBorn71
ms.author: flborn
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 56aa0d91372ac2d21a20f28b1044f0811c716b0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358037"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-maya"></a>Tutorial: Configurar materiais de renderização fisicamente em Maia

## <a name="overview"></a>Descrição geral
Neste tutorial, irá aprender a:

> [!div class="checklist"]
>
> * Atribua materiais com iluminação avançada a objetos na cena.
> * Manuseie a instalação de objetos e materiais.
> * Exporte uma cena para o formato FBX e selecione opções importantes.

Criar [materiais de renderização física (PBR)](../../overview/features/pbr-materials.md) em Maya é uma tarefa relativamente simples. É semelhante em muitas formas à configuração do PBR em outras aplicações de criação de conteúdo como o 3DS Max. Este tutorial é um guia para a configuração básica do sombreador PBR e exportação de FBX para projetos de renderização remota Azure. 

A cena da amostra neste tutorial contém uma série de objetos de caixa de polígono. São-lhes atribuídos diferentes materiais, como madeira, metal, metal pintado, plástico e borracha. Em termos gerais, cada material contém todas ou a maioria das seguintes texturas:

* **Albedo,** que é o mapa de cores dos materiais e também é chamado **de Difusão** ou **BaseColor.**
* **Metalidade**, que determina se um material é metálico e quais as partes metálicas. 
* **Aspereza**, que determina o quão áspera ou lisa uma superfície é e afeta a nitidez ou borrão dos reflexos e destaques numa superfície.
* **Normal,** que adiciona detalhes a uma superfície sem ter que adicionar mais polígonos. Exemplos de detalhes podem ser de acaso e amolgadelas numa superfície metálica ou grãos em madeira.
* **Oclusão ambiente,** que é usado para adicionar sombras suaves e sombras de contacto a um modelo. É um mapa em tons de cinza que indica quais as áreas de um modelo que recebem iluminação completa (branca) ou sombra completa (preto). 

## <a name="prerequisites"></a>Pré-requisitos
* Autodesk Maya 2017 ou mais recente

## <a name="set-up-materials-in-the-scene"></a>Configurar materiais na cena
Eis como montar um material PBR em Maya.

Como podem ver na cena da amostra, criámos uma série de objetos de caixa. Cada objeto representa um tipo diferente de material. Como mostrado na imagem, cada um destes objetos recebeu o seu próprio nome apropriado.

A renderização remota Azure utiliza medidores para medição e a direção ascendente é o eixo Y. Antes de começar a criar ativos, recomendamos que coloque as suas unidades de cena em Maya em metros. Para exportar, coloque as unidades a metros nas definições de exportação FBX.

> [!TIP]
> Forneça aos seus modelos os nomes adequados com base na peça ou tipo de material relevante. Nomes significativos facilitam a navegação de cenas pesadas de objetos.

![Nomes de objetos](media/object-names.jpg)

Depois de criar ou adquirir algumas texturas, também pode criar texturas únicas. Você pode usar aplicativos de texturismo como Quixel Suite, PhotoShop ou Substance Suite ou obter texturas genéricas de inclinação de outras fontes.

Para aplicar texturas ao seu modelo:

1. No seu viewport de cena, selecione o seu modelo ou geometria e clique com a direita. No menu que aparece, selecione **Atribuir Novo Material**.
1. Na caixa de diálogo **de novos materiais,** vá à **Maya**  >  **Stingray PBS**. Esta ação atribui um material PBR ao seu modelo. 

Em maio de 2020, estão disponíveis vários shaders PBR diferentes. Eles incluem **Maya Standard Surface,** **Arnold Standard Surface,** e **Stingray PBR**. O **Maya Standard Surface Shader** ainda não é exportável através do plug-in FBX 2020. O **Arnold Standard Surface Shader** pode ser exportado com ficheiros FBX. Na maioria dos outros aspetos, é idêntico ao **Maya Standard Surface Shader.** É análogo ao **Material Físico** no Estúdio 3D Max.

O **Stingray PBR Shader** é compatível com muitas outras aplicações e corresponde mais estreitamente aos requisitos da Renderização Remota Azure. É apoiado desde Maya 2017. Quando este tipo de material é visualizado no viewport, é semelhante ao que é visualizado mais tarde na Renderização Remota Azure.

![Material stingray](media/stingray-material.jpg)

Com o seu material atribuído ao seu ativo e nomeado adequadamente, pode agora atribuir as suas várias texturas. As imagens que se seguem mostram onde cada tipo de textura se encaixa no material PBR. O material Stingray PBR permite-lhe selecionar quais os atributos que pode ativar. Antes de ligar os seus mapas de textura, precisa de ativar os atributos relevantes.

![Configuração de material](media/material-setup.jpg)

Nomeie os seus materiais adequadamente, tendo em conta a sua utilização ou tipo. Um material que é usado numa parte única pode ser nomeado por essa parte. Um material que é usado numa ampla gama de áreas pode ser nomeado pelas suas propriedades ou tipo.

Atribua as suas texturas como mostrado na imagem.

![Configuração de textura](media/texture-setup.jpg)

Com os seus materiais PBR criados e configurados, considere [a instalação de objetos](../../how-tos/conversion/configure-model-conversion.md#instancing) na sua cena. A instalação de objetos semelhantes na sua cena, tais como porcas, parafusos, parafusos e anilhas, produz poupanças significativas no tamanho do ficheiro. Os casos de um objeto mestre podem ter a sua própria escala, rotação e transformações para que possam ser colocados como necessário na sua cena. 

Em Maya, o processo de instancing é simples.

1. No menu **Editar,** vá ao **Duplicado Special** para abrir opções.
1. Na caixa de diálogo **Duplicado Special Options,** para **o tipo geometria** selecione a opção **Instância.** 
1. Selecione **Duplicado Especial**.

   ![A screenshot mostra uma janela Maia com a caixa de diálogo duplicado das opções especiais aberta e a Duplicate Special selecionada.](media/instancing.jpg)

Esta ação cria um exemplo do seu objeto. Pode mover-se, rodar ou escaloná-lo independentemente do seu progenitor e de outros casos desse progenitor. 

Quaisquer alterações que faça a uma instância enquanto estiver em modos componentes são transmitidas a todas as instâncias do seu objeto. Por exemplo, pode trabalhar com componentes de um objeto, como vértices e rostos de polígono. Certifique-se de que deseja que quaisquer alterações que tenha feito para afetar todas estas instâncias. 

Na cena da amostra, cada objeto de caixa individual foi identificado. Esta ação terá relevância quando exportarmos a cena para o formato FBX.

![Visão geral da cena](media/scene-overview.jpg)

> [!TIP]
> Crie casos na sua cena à medida que avança. Substituir cópias por objetos posteriores é extremamente difícil. 

## <a name="fbx-export-process"></a>Processo de exportação de FBX

Vamos passar à exportação fBX da sua cena ou dos seus ativos de cena. Ao exportar ativos, faz sentido selecionar apenas os objetos ou ativos da sua cena que pretende exportar. Por exemplo, pode ter 100 objetos numa cena. Se queres usar apenas 30, não vale a pena exportar toda a cena. 

Para fazer a sua seleção:

1. Vá à Seleção de Exportação de **Ficheiros**para abrir a caixa de diálogo seleção de  >  **Export Selection** **exportação.**
1. Nos **Ficheiros da** caixa de tipo, selecione **a exportação de FBX** para exibir as definições de exportação FBX. As definições primárias para a exportação de FBX são destacadas a vermelho na imagem.

   ![Exportação FBX](media/FBX-exporting.jpg)

Dependendo dos seus requisitos, por exemplo, talvez queira enviar um ativo a um cliente. Talvez não queira enviar um grande número de ficheiros de textura com o ativo. Pode optar por incorporar as texturas dentro do ficheiro FBX exportado. Esta opção significa que tem apenas um ficheiro para embalar, mas o tamanho desse ativo FBX aumenta significativamente. Pode ativar a opção de incorporar texturas selecionando a opção **Embed Media** como mostrado.

> [!TIP]
> Nesta amostra, o ficheiro foi nomeado para refletir esta condição. Este estilo de nomeação é uma boa maneira de acompanhar os seus bens. 

Depois de definir a sua configuração para exportação, selecione **Seleção de Exportação** no direito inferior.

![Meios de incorporação](media/embedding-media.jpg)

## <a name="conclusion"></a>Conclusão

Em geral, este tipo de material parece mais realista porque é baseado na física da luz do mundo real. Cria um efeito imersivo adicional para que a cena pareça existir no mundo real.

## <a name="next-steps"></a>Passos seguintes

Agora sabe como configurar materiais com iluminação avançada para objetos numa cena. Também sabe exportar os objetos para o formato FBX que é suportado pela Azure Remote Rendering. O próximo passo é converter o ficheiro FBX e visualizá-lo em Renderização Remota Azure.

> [!div class="nextstepaction"]
> [Quickstart: Converter um modelo para renderização](../../quickstarts\convert-model.md)