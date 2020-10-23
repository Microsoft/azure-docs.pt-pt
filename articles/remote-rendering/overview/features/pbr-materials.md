---
title: Materiais PBR
description: Descreve o tipo de material PBR.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 76e7b3d0b0dd514feb7d16a6bc23d1b908be683f
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207211"
---
# <a name="pbr-materials"></a>Materiais PBR

*Os materiais PBR* são um dos tipos de [material](../../concepts/materials.md) suportado na renderização remota Azure. São utilizados para [malhas](../../concepts/meshes.md) que devem receber iluminação realista.

PBR significa **P**hysicamente **B**ased **R**endering e significa que o material descreve as propriedades visuais de uma superfície de uma forma fisicamente plausível, de modo que os resultados realistas são possíveis em todas as condições de iluminação. A maioria dos motores de jogo modernos e ferramentas de criação de conteúdo suportam materiais PBR porque são considerados a melhor aproximação de cenários do mundo real para renderização em tempo real.

![Modelo de amostra de capacete glTF renderizado por ARR](media/helmet.png)

No entanto, os materiais PBR não são uma solução universal. Existem materiais que refletem a cor de forma diferente dependendo do ângulo de visualização. Por exemplo, alguns tecidos ou tintas de carro. Este tipo de materiais não são manuseados pelo modelo PBR padrão, e atualmente não são suportados pela Azure Remote Rendering. Isto inclui extensões de PBR, tais como *Thin-Film* (superfícies de várias camadas) e *Clear-Coat* (para pinturas de automóveis).

## <a name="common-material-properties"></a>Propriedades materiais comuns

Estas propriedades são comuns a todos os materiais:

* **albedoColor:** Esta cor é multiplicada com outras cores, como o *albedoMap* ou * :::no-loc text="vertex "::: as cores.* Se *a transparência* for ativada num material, o canal alfa é utilizado para ajustar a opacidade, com significado totalmente `1` opaco e `0` significado totalmente transparente. O padrão é branco.

  > [!NOTE]
  > Quando um material PBR é totalmente transparente, como um pedaço de vidro perfeitamente limpo, ainda reflete o ambiente. Pontos brilhantes como o sol ainda são visíveis no reflexo. Isto é diferente para [materiais de cor.](color-materials.md)

* **albedoMap:** Uma [textura 2D](../../concepts/textures.md) para valores de albedo por pixel.

* **alphaClipEnabled** e **alphaClipThreshold:** Se *alfaClipEnabled* for verdadeiro, todos os pixels onde o valor alfa albedo é inferior ao *alphaClipThreshold* não serão desenhados. O recorte alfa pode ser usado mesmo sem permitir a transparência e é muito mais rápido de renderizar. No entanto, os materiais alfa cortados são ainda mais lentos a renderizar do que materiais totalmente opacos. Por defeito, o recorte alfa é desativado.

* **textureCoordinateScale** e **textureCoordinateOffset:** A escala é multiplicada nas coordenadas de textura UV, o offset é adicionado a ele. Pode ser usado para esticar e deslocar as texturas. A escala predefinida é (1, 1) e a offset é (0,0).

* **useVertexColor:** Se a malha contiver :::no-loc text="vertex"::: cores e esta opção estiver ativada, a cor das malhas :::no-loc text="vertex"::: é multiplicada no *albedoColor* e *albedoMap.* Por *utilização predefinidaVertexColor* é desativado.

* **isDoubleSided:** Se a dupla faceta for verdadeira, os triângulos com este material são renderizados mesmo que a câmara esteja a olhar para as suas faces traseiras. Para a iluminação de materiais PBR também é computada adequadamente para faces traseiras. Por predefinição, esta opção é desativada. Ver também [ :::no-loc text="Single-sided"::: renderização.](single-sided-rendering.md)

* **TransparênciaDesseepth:** Se a bandeira TransparencyWritesDepth for colocada no material e o material for transparente, os objetos que utilizam este material também contribuirão para o tampão de profundidade final. Consulte a bandeira de material PBR *transparente* na secção seguinte. Para ativar esta funcionalidade é recomendado se o seu caso de utilização necessitar de uma [reprojecção tardia](late-stage-reprojection.md) mais plausível de cenas totalmente transparentes. Para cenas opacas/transparentes mistas, este cenário pode introduzir comportamentos de reprojecção implausíveis ou artefactos de reprojecção. Por esta razão, a definição predefinida e recomendada para o caso de utilização geral é para desativar esta bandeira. Os valores de profundidade escrita são retirados da camada de profundidade por pixel do objeto mais próximo da câmara.

## <a name="pbr-material-properties"></a>Propriedades de materiais PBR

A ideia central da renderização baseada fisicamente é usar propriedades *BaseColor,* *Metalness*e *Aspereza* para imitar uma ampla gama de materiais do mundo real. Uma descrição detalhada do PBR está fora do âmbito deste artigo. Para mais informações sobre o PBR, consulte [outras fontes.](http://www.pbr-book.org) As seguintes propriedades são específicas dos materiais PBR:

* **baseColor:** Em materiais PBR, a *cor albedo* é referida como a *cor base.* Em Azure Remote Rendering a propriedade *de cor albedo* já está presente através das propriedades de material comum, pelo que não existe nenhuma propriedade de cor base adicional.

* **aspereza** e **asperezaMap:** Aspereza define o quão áspera ou lisa é a superfície. Superfícies ásperas espalham a luz em mais direções do que superfícies lisas, que fazem os reflexos borrar em vez de afiados. A gama de valores é `0.0` `1.0` de. Quando `roughness` for `0.0` igual, os reflexos serão afiados. Quando `roughness` é `0.5` igual, os reflexos tornar-se-ão desfocados.

  Se for fornecido um valor de aspereza e um mapa de aspereza, o valor final será o produto dos dois.

* **metalomecão** e **metalnessAp:** Na física, esta propriedade corresponde a se uma superfície é condutora ou dielétrica. Os materiais condutivos têm diferentes propriedades refletoras, e tendem a refletir sem cor albedo. Em materiais PBR, esta propriedade afeta o quanto uma superfície reflete o ambiente circundante. Os valores variam de `0.0` `1.0` . Quando a metalidade `0.0` é, a cor do albedo é totalmente visível e o material parece plástico ou cerâmica. Quando a metalidade `0.5` é, parece metal pintado. Quando a metalidade `1.0` é, a superfície quase perde completamente a sua cor de albedo e apenas reflete o ambiente. Por exemplo, se `metalness` é `1.0` e é `roughness` `0.0` então uma superfície parece um espelho do mundo real.

  Se for fornecido um valor de metal e um mapa de metal, o valor final será o produto dos dois.

  ![Esferas renderizadas com diferentes metais e valores de aspereza](./media/metalness-roughness.png)

  Na imagem acima, a esfera no canto inferior direito parece um material metálico real, o inferior esquerdo parece cerâmica ou plástico. A cor do albedo também está a mudar de acordo com as propriedades físicas. Com o aumento da aspereza, o material perde a nitidez da reflexão.

* **mapa normal:** Para simular detalhes finos, pode ser fornecido um [mapa normal.](https://en.wikipedia.org/wiki/Normal_mapping)

* **oclusionMap** e **aoScale:** [A oclusão ambiente](https://en.wikipedia.org/wiki/Ambient_occlusion) faz com que os objetos com fendas pareçam mais realistas adicionando sombras a áreas obstruídas. O valor da oclusão varia `0.0` `1.0` de, onde `0.0` significa escuridão (obstruída) e `1.0` significa que não há oclusões. Se uma textura 2D for fornecida como um mapa de oclusão, o efeito é ativado e *o aoScale* age como um multiplicador.

  ![Um objeto renderizado com e sem oclusão ambiente](./media/boom-box-ao2.gif)

* **transparente:** Para os materiais PBR, existe apenas um ambiente de transparência: está ativado ou não. A opacidade é definida pelo canal alfa da cor albedo. Quando ativado, é invocado um gasoduto de renderização mais complexo para desenhar superfícies semi-transparentes. A Azure Remote Rendering implementa uma verdadeira [transparência independente de ordem](https://en.wikipedia.org/wiki/Order-independent_transparency) (OIT).

  Geometria transparente é dispendiosa para renderizar. Se você só precisa de buracos numa superfície, por exemplo para as folhas de uma árvore, é melhor usar o recorte alfa em vez disso.

  ![Esferas renderizadas com zero a total transparência ](./media/transparency.png) Aviso na imagem acima, como a esfera mais direita é totalmente transparente, mas a reflexão ainda é visível.

  > [!IMPORTANT]
  > Se qualquer material for trocado de opaco para transparente no tempo de [funcionamento,](../../concepts/rendering-modes.md)o renderizador deve utilizar o modo de renderização *TileBasedComposition* . Esta limitação não se aplica aos materiais que são convertidos como materiais transparentes para começar.

## <a name="technical-details"></a>Detalhes técnicos

A renderização remota Azure usa o Cook-Torrance microfacet BRDF com GGX NDF, Schlick Fresnel, e um termo de visibilidade GGX Smith correlacionado com um termo difuso Lambert. Este modelo é o padrão da indústria de facto neste momento. Para mais detalhes aprofundados, consulte este artigo: [Rendering fisicamente baseado - Cook Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Uma alternativa ao modelo *PBR Metalness-Asperness* utilizado na Renderização Remota Azure é o modelo *Specular-Glossiness* PBR. Este modelo pode representar uma gama mais ampla de materiais. No entanto, é mais caro e normalmente não funciona bem em casos em tempo real.
Nem sempre é possível converter *de Specular-Glossiness* para *Metalness-Aspereza,* uma vez que existem pares de valor *(Difuso, Specular)* que não podem ser convertidos para *(BaseColor, Metalness)*. A conversão na outra direção é mais simples e precisa, uma vez que todos os pares *(BaseColor, Metalness)* correspondem a pares bem definidos *(Difuso, Specular).*

## <a name="api-documentation"></a>Documentação da API

* [Classe PbrMaterial](/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C# RemoteManager.CreateMaterial()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.creatematerial)
* [Classe PbrMaterial C++](/cpp/api/remote-rendering/pbrmaterial)
* [C++ RemoteManager::CreateMaterial()](/cpp/api/remote-rendering/remotemanager#creatematerial)

## <a name="next-steps"></a>Passos seguintes

* [Materiais de cor](color-materials.md)
* [Texturas](../../concepts/textures.md)
* [Malhas](../../concepts/meshes.md)