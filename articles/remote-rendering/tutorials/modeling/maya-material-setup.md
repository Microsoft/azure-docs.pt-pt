---
title: Configurar materiais de Renderização Baseada Fisicamente no Maya
description: Explica como configurar materiais de renderização baseados fisicamente em Maias e exportá-lo para o formato FBX
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 5579994b0746a2de4b0f2ca927027ac709940024
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977851"
---
# <a name="tutorial-setting-up-physically-based-rendering-materials-in-maya"></a>Tutorial: Criação de materiais de renderização fisicamente baseados em Maias

## <a name="overview"></a>Descrição geral
Neste tutorial, vai aprender a:

> [!div class="checklist"]
>
> * atribuir materiais com modelo avançado de iluminação a objetos na cena.
> * manusear a instalação de objetos e materiais.
> * exportar uma cena para o formato FBX e opções importantes para selecionar.

A criação [de materiais de renderização baseadas fisicamente (PBR)](../../overview/features/pbr-materials.md) `Maya` é uma tarefa relativamente simples, semelhante em muitas formas à configuração de PBR em outras apps de criação de conteúdo como `3DS Max` . O seguinte tutorial é um guia para a configuração básica do sombreador PBR e exportação de FBX para projetos ARR. 

A cena da amostra neste tutorial contém uma série de `Polygon Box` objetos que foram atribuídos a vários materiais diferentes - madeira, metal, metal pintado, plástico e borracha. Em termos gerais, cada material contém todas ou a maioria das seguintes texturas 

* `Albedo`, é o mapa de cores dos materiais, também `Diffuse` chamado, ou`BaseColor`
* `Metalness`, que determina se um material é metálico e quais as partes metálicas. 
* `Roughness`, que determina o quão áspera ou lisa é uma superfície, afetando a nitidez ou a desfocagem dos reflexos e destaques numa superfície.
* `Normals`, que adiciona detalhes a uma superfície, por exemplo, acasalamentos e amassados numa superfície metálica ou grãos em madeira sem ter de adicionar mais polígonos.
* `Ambient Occlusion`, que é usado para adicionar sombras suaves e sombras de contacto a um modelo. É um mapa em escala de cinza que indica quais as áreas de um modelo que recebem iluminação completa (branco) ou sombra completa (Preto). 

## <a name="prerequisites"></a>Pré-requisitos
* `Autodesk Maya 2017`ou mais recente

## <a name="setting-up-materials-in-the-scene"></a>Instalação de materiais na cena
Em maio, o processo de criação de um material PBR é o seguinte:

Para começar, como pode ver na cena da amostra, criámos uma série de objetos Box, cada um dos quais representa um tipo diferente de material. Note, como mostrado na imagem abaixo, que cada um destes objetos foi dado o seu próprio nome apropriado 

> Vale a pena notar antes de começar a criar ativos para renderização remota Azure (ARR) que utiliza contadores para medição e que a direção ascendente é Y-Axis. Portanto, é aconselhável definir a sua cena Unidades em Maya a metros. Além disso, é aconselhável quando exportar para definir unidades para metros nas definições de exportação FBX. 

> [!TIP]
É uma boa prática nomear os seus ativos de modelo de forma adequada, geralmente com a parte relevante ou tipo de material, uma vez que os nomes facilitarão a navegação em cenas de objetos pesados.

![Nomes de objetos](media/object-names.jpg)

Uma vez que tenha criado/adquirido as suas texturas - dependendo das suas necessidades, poderá querer criar texturas únicas para um modelo em aplicações de texturagem como `Quixel Suite` , ou pode estar a usar `Photoshop` `Substance Suite` texturas genéricas de inclinação de outras fontes, pode aplicá-las ao seu modelo da seguinte forma:

* No seu viewport de cena, selecione o seu modelo/geometria e clique com o botão direito nele. No menu que aparece clique`Assign New Material`
* Nas `Assign New Material` opções, vá `Maya` > `Stingray PBS` a. Esta ação atribuirá um material PBR ao seu modelo. 

Em `Maya 2020` , há uma série de diferentes shaders PBR disponíveis - , e `Maya Standard Surface` `Arnold Standard Surface` `Stingray PBR` . O `Maya Standard Surface Shader` ainda não é exportável através do , `FBX plugin 2020` enquanto que o pode ser `Arnold Standard Surface Shader` exportado com ficheiros FBX. Na maioria dos outros aspetos, é idêntico ao `Maya Standard Surface Shader` e é análogo ao `Physical Material` in `3D Studio Max` .

**`The Stingray PBR Shader`** é compatível com muitas outras aplicações e mais próxima corresponde aos requisitos `ARR` de e é suportado desde `Maya 2017` então . Também é conveniente que este tipo de materiais visualizados em vistaport semelhante ao que será visualizado mais tarde em ARR.

![Material 'Stingray'](media/stingray-material.jpg)

Com o seu material atribuído ao seu ativo e nomeado adequadamente, pode agora proceder à atribuição das suas várias texturas. As imagens que se seguem detalham onde cada tipo de textura se encaixa no material PBR. O `Stingray PBR` material permite-lhe selecionar quais os atributos que pode ativar, para que antes de poder `plug in` os seus mapas de textura precise de ativar os atributos relevantes: 

![Configuração de material](media/material-setup.jpg)

> [!TIP]
É uma boa prática nomear os seus materiais de forma adequada, tendo em conta o seu uso e/ou tipo. Um material que deve ser usado numa parte única pode ser nomeado para essa parte, enquanto um material que pode ser usado em um número mais alargado de áreas pode ser nomeado pelas suas propriedades ou tipo.

Atribua as suas texturas da seguinte forma:

![Configuração de textura](media/texture-setup.jpg)

Com os seus Materiais PBR criados e configurados, vale a pena pensar [em instalar objetos](../../how-tos/conversion/configure-model-conversion.md#instancing) na sua cena. A instalação de objetos semelhantes na sua cena - como porcas, parafusos, anilhas de parafusos - essencialmente quaisquer objetos que sejam iguais podem gerar poupanças significativas em termos de tamanho do ficheiro. Os casos de um objeto mestre podem ter a sua própria escala, rotação e transformações para que possam ser colocados como necessário na sua cena. Em Maya, o processo de Instancing é simples.

* No `Edit` menu, vá `Duplicate Special` e abra `Options` o, 
* Nas `Duplicate Special` opções mudam `Geometry Type` de `Copy` `Instance` para, 
* Clique `Duplicate Special`

![Instancing](media/instancing.jpg)

Esta ação criará uma instância do seu objeto que pode ser movido rotativo ou escalado independentemente do seu progenitor e de outros casos desse progenitor. 
>No entanto - quaisquer alterações que faça a uma instância durante o modo componente serão transmitidas a todas as instâncias do seu objeto, por isso, se estiver a trabalhar com componentes de objetos de instância - vértices, rostos de polígono, etc. certifique-se primeiro de que pretende que quaisquer alterações que faça para afetar todos estes casos.

Na cena da amostra, cada objeto de caixa individual foi identificado. Esta ação terá relevância quando exportarmos a cena para o formato FBX.

![Visão geral da cena](media/scene-overview.jpg)

>As melhores práticas em relação à instancing na sua cena é criá-las à medida que avança, já que substituir 'Cópias' por objetos Instanced mais tarde é extremamente difícil. 

## <a name="fbx-export-process"></a>Processo de exportação de FBX

Podemos agora passar para a exportação FBX da sua cena ou dos seus ativos de cena. De um modo geral, faz sentido ao exportar ativos apenas para exportar esses objetos/ativos da sua cena que deseja. Se tens 100 objetos numa cena, mas só queres usar 30 deles, não vale a pena exportar toda a cena. Portanto, a menos que você esteja feliz em exportar toda a cena, faça a sua seleção e vá para:

* `File` > `Export Selection`e no diálogo de exportação ir para o fundo e definir `Files of Type` para `FBX Export` . Esta janela exporá as definições de Exportação FBX. As definições primárias para a FBX Export são destacadas a vermelho na imagem abaixo.

![Exportação FBX](media/FBX-exporting.jpg)

Dependendo dos seus requisitos - por exemplo, pode querer enviar um ativo a um cliente, mas não quer enviar um grande número de ficheiros de textura com o ativo, pode optar por incorporar as texturas dentro do ficheiro FBX exportado. Esta opção significa que só tem um ficheiro para embalar, mas irá aumentar significativamente o tamanho desse ativo FBX. Pode ativar a opção de incorporar texturas, mudando a `Embed Media` opção como mostrado abaixo.

> [!TIP]
> Note que o ficheiro neste caso foi nomeado para refletir esta condição. Esta é uma boa prática para acompanhar os seus bens. 

Depois de ter terminado de definir a sua configuração para Exportação, clique no botão 'Seleção de Exportação' no canto inferior direito.

![Meios de incorporação](media/embedding-media.jpg)

## <a name="conclusion"></a>Conclusão

Em geral, este tipo de materiais parece mais realista, uma vez que é baseado na física da luz do mundo real. Cria um efeito imersivo adicional que a cena existe no mundo real.

## <a name="next-steps"></a>Passos seguintes

Agora conhece a funcionalidade mais importante para configurar materiais com iluminação avançada para objetos numa cena e exportá-lo para o formato FBX que é suportado pelo ARR. O próximo passo seria converter o ficheiro FBX e visualizar em ARR.

> [!div class="nextstepaction"]
> [Quickstart: Converter um modelo para renderização](../../quickstarts\convert-model.md)