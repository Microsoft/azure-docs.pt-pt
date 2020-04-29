---
title: Materiais PBR
description: Descreve o tipo de material PBR.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 64553506f75451c50a87932904f00a7275ea9286
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680261"
---
# <a name="pbr-materials"></a>Materiais PBR

*Os materiais PBR* são um dos tipos de [material](../../concepts/materials.md) suportado na renderização remota azure. São usados para [maquetes](../../concepts/meshes.md) que devem receber iluminação realista.

PBR significa **p**histicamente **B**ased **R**endering e significa que o material descreve as propriedades visuais de uma superfície de uma forma fisicamente plausível, de modo que os resultados realistas são possíveis em todas as condições de iluminação. A maioria dos motores de jogo modernos e ferramentas de criação de conteúdo suportam materiais PBR porque são considerados a melhor aproximação de cenários do mundo real para renderização em tempo real.

![Modelo de amostra de capacete glTF renderizado pela ARR](media/helmet.png)

No entanto, os materiais PBR não são uma solução universal. Existem materiais que refletem a cor de forma diferente dependendo do ângulo de visualização. Por exemplo, alguns tecidos ou tintas de carro. Este tipo de materiais não são manuseados pelo modelo PBR padrão, e atualmente não são suportados pela Renderização Remota Azure. Isto inclui extensões PBR, tais como *Thin-Film* (superfícies com várias camadas) e *Clear-Coat* (para tintas de carro).

## <a name="common-material-properties"></a>Propriedades materiais comuns

Estas propriedades são comuns a todos os materiais:

* **albedoColor:** Esta cor é multiplicada com outras cores, tais como as cores *albedoMap* ou *vertex*. Se a *transparência* for ativada num material, o canal alfa `1` é utilizado para `0` ajustar a opacidade, com um significado totalmente opaco e com um significado totalmente transparente. O padrão é branco.

  > [!NOTE]
  > Quando um material PBR é totalmente transparente, como um pedaço de vidro perfeitamente limpo, ainda reflete o ambiente. Manchas brilhantes como o sol ainda são visíveis no reflexo. Isto é diferente para materiais de [cor.](color-materials.md)

* **albedoMap:** Uma [textura 2D](../../concepts/textures.md) para valores de albedo por pixel.

* **alfaClipEnabled** e **alphaClipThreshold:** Se *alphaClipEnabled* for verdadeiro, todos os pixels onde o valor alfa albedo é inferior ao *alfaClipThreshold* não serão desenhados. O recorte alfa pode ser usado mesmo sem permitir a transparência e é muito mais rápido de renderizar. No entanto, os materiais recortados alfa são ainda mais lentos para renderizar do que materiais totalmente opacos. Por padrão, o recorte alfa é desativado.

* **textureCoordinateScale** e **textureCoordinateOffset:** A balança é multiplicada nas coordenadas de textura UV, a compensação é adicionada a ela. Pode ser usado para esticar e deslocar as texturas. A escala predefinida é (1, 1) e a compensação é (0,0).

* **useVertexColor:** Se a malha contiver cores vértices e esta opção estiver ativada, as cores vértice das malhas são multiplicadas no *albedoColor* e *albedoMap*. Por padrão, as cores vértices são desativadas.

* **isDoubleSided:** Se a dupla faceta for verdadeira, os triângulos com este material são renderizados mesmo que a câmara esteja a olhar para as suas faces traseiras. Para a iluminação de materiais PBR também é calculada corretamente para rostos traseiros. Por defeito, esta opção está desativada. Ver também [renderização unilateral](single-sided-rendering.md).

## <a name="pbr-material-properties"></a>Propriedades de materiais PBR

A ideia central da renderização baseada fisicamente é usar propriedades *BaseColor,* *Metalness*e *Roughness* para imitar uma vasta gama de materiais do mundo real. Uma descrição detalhada do PBR está fora do âmbito deste artigo. Para mais informações sobre o PBR, consulte [outras fontes](http://www.pbr-book.org). As seguintes propriedades são específicas dos materiais PBR:

* **BaseColor:** Nos materiais PBR, a *cor albedo* é referida como a *cor base*. Em Azure Remote Rendering a propriedade *de cores albedo* já está presente através das propriedades do material comum, por isso não há propriedade de cor base adicional.

* **rugosidade** e **rugosidadeMap:** A rugosidade define o quão áspera ou suave é a superfície. Superfícies ásperas espalham a luz em mais direções do que superfícies lisas, que tornam os reflexos turvas em vez de afiados. O intervalo de `0.0` `1.0`valor é de . Quando `roughness` é `0.0`igual, as reflexões serão afiadas. Quando `roughness` é `0.5`igual, as reflexões tornar-se-ão desfocadas.

  Se for fornecido um valor de aspereza e um mapa de rugosidade, o valor final será o produto dos dois.

* **metalidade** e **metalidadeMap:** Na física, esta propriedade corresponde ao facto de uma superfície ser condutiva ou dielétrica. Os materiais condutores têm diferentes propriedades reflexivas, e tendem a refletir sem cor albedo. Nos materiais PBR, esta propriedade afeta a quantidade de superfície que reflete o ambiente circundante. Os valores variam de `0.0` `1.0`. Quando a `0.0`metalidade é, a cor do albedo é totalmente visível e o material parece plástico ou cerâmica. Quando a `0.5`metalidade é, parece metal pintado. Quando a `1.0`metalidade é, a superfície perde quase por completo a sua cor albedo e só reflete o ambiente. Por exemplo, `metalness` `1.0` se `roughness` `0.0` é e é então uma superfície parece um espelho do mundo real.

  Se for fornecido um valor metálico e um mapa de metalino, o valor final será o produto dos dois.

  ![metalidade e rugosidade](./media/metalness-roughness.png)

  Na imagem acima, a esfera no canto inferior direito parece um material metálico real, o inferior-esquerdo parece cerâmica ou plástico. A cor do albedo também está a mudar de acordo com as propriedades físicas. Com o aumento da aspereza, o material perde a nitidez de reflexão.

* **mapa normal:** Para simular detalhes finos, pode ser fornecido um [mapa normal.](https://en.wikipedia.org/wiki/Normal_mapping)

* **oclusãoMap** e **aoScale:** [A oclusão ambiente](https://en.wikipedia.org/wiki/Ambient_occlusion) faz com que os objetos com fendas pareçam mais realistas adicionando sombras a áreas obstruídas. O valor da oclusão `0.0` `1.0`varia `0.0` de até, onde significa `1.0` escuridão (obstruída) e significa que não há oclusões. Se uma textura 2D for fornecida como um mapa de oclusão, o efeito é ativado e o *aoScale* atua como um multiplicador.

  ![Mapa de Oclusão](./media/boom-box-ao2.gif)

* **transparente:** Para os materiais PBR, existe apenas uma definição de transparência: está ativada ou não. A opacidade é definida pelo canal alfa da cor albedo. Quando ativado, é invocado um gasoduto de renderização mais complexo para desenhar superfícies semi-transparentes. A Renderização Remota Azure implementa [uma verdadeira transparência independente](https://en.wikipedia.org/wiki/Order-independent_transparency) da ordem (OIT).

  Geometria transparente é cara de renderizar. Se só necessitar de furos numa superfície, por exemplo, para as folhas de uma árvore, é melhor utilizar o recorte alfa.

  ![Transparência](./media/transparency.png) Aviso na imagem acima, como a esfera mais direita é totalmente transparente, mas a reflexão ainda é visível.

  > [!IMPORTANT]
  > Se qualquer material for suposto ser mudado de opaco para transparente no tempo de funcionamento, o renderizador deve utilizar o modo de [renderização](../../concepts/rendering-modes.md) *TileBasedComposition* . Esta limitação não se aplica a materiais que são convertidos como materiais transparentes para começar.

## <a name="technical-details"></a>Detalhes técnicos

A Renderização Remota Azure usa o BRDF micro-facet cook-Torrance com GGX NDF, Schlick Fresnel, e um termo de visibilidade correlacionado GGX Smith com um termo de difusão Lambert. Este modelo é, de facto, o padrão da indústria neste momento. Para mais detalhes aprofundados, consulte este artigo: [Renderização baseada fisicamente - Cook Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Uma alternativa ao modelo PBR *metalness-Roughness* utilizado na Renderização Remota Azure é o modelo PBR *Specular-Glossiness.* Este modelo pode representar uma gama mais ampla de materiais. No entanto, é mais caro e, normalmente, não funciona bem para casos em tempo real.
Nem sempre é possível *converter-se de Especular-Glossiness* para *Metalness-Roughness* como existem pares de valor *(Difuso, Specular)* que não podem ser convertidos para *(BaseColor, Metalness)*. A conversão na outra direção é mais simples e precisa, uma vez que todos os pares *(BaseColor, Metalness)* correspondem a pares bem definidos *(Difuso, Specular).*

## <a name="next-steps"></a>Passos seguintes

* [Materiais de cor](color-materials.md)
* [Texturas](../../concepts/textures.md)
* [Malhas](../../concepts/meshes.md)
