---
title: Substituição do estado hierárquico
description: Explica o conceito de estado hierárquico sobrepor componentes.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 40857e83457222365e61a224ead19bd1d1d31ae7
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758984"
---
# <a name="hierarchical-state-override"></a>Substituição do estado hierárquico

Em muitos casos, é necessário alterar dinamicamente a aparência de partes de um [modelo](../../concepts/models.md), por exemplo, ocultar subgrafias ou mudar peças para renderização transparente. A alteração dos materiais de cada peça envolvida não é prática, uma vez que requer iterar em todo o gráfico de cena, e gerir a clonagem e atribuição de materiais em cada nó.

Para realizar este caso de utilização com o mínimo possível de sobrecarga, utilize o `HierarchicalStateOverrideComponent` . Este componente implementa atualizações hierárquicas do estado em ramos arbitrários do gráfico de cena. Isto significa que um estado pode ser definido em qualquer nível no gráfico de cena e escorre pela hierarquia até que seja ultrapassado por um estado novo, ou aplicado a um objeto de folha.

Como exemplo, considere o modelo de um carro e quer mudar todo o carro para ser transparente, exceto para a parte interna do motor. Este caso de utilização envolve apenas duas instâncias do componente:

* O primeiro componente é atribuído ao nó raiz do modelo e liga a renderização transparente para todo o carro.
* O segundo componente é atribuído ao nó raiz do motor e substitui novamente o estado desligando explicitamente o modo de controlo.

## <a name="features"></a>Funcionalidades

O conjunto fixo de estados que podem ser ultrapassados são:

* **Escondido**: As respetivas malsheas no gráfico de cena são ocultas ou mostradas.
* **Cor de tonalidade**: Um objeto renderizado pode ser colorido com a sua cor de tonalidade individual e peso de tonalidade. A imagem abaixo mostra colorting da borda de uma roda.
  
  ![Cor Tint](./media/color-tint.png)

* Transparente : A geometria **torna-se**semi-transparente, por exemplo, para revelar as partes internas de um objeto. A imagem seguinte mostra todo o carro a ser renderizado em modo de perverso, com exceção do pinça de travão vermelho:

  ![Ver-Through](./media/see-through.png)

  > [!IMPORTANT]
  > O efeito "see-through" só funciona quando é utilizado o modo de [renderização](../../concepts/rendering-modes.md) *TileBasedComposition.*

* **Selecionado**: A geometria é renderizada com um contorno de [seleção](outlines.md).

  ![Contorno de seleção](./media/selection-outline.png)

* **DisableColisão**: A geometria está isenta de [consultas espaciais](spatial-queries.md). A bandeira **escondida** não desliga as colisões, por isso estas duas bandeiras são muitas vezes juntas.

## <a name="hierarchical-overrides"></a>Sobreposições hierárquicas

O `HierarchicalStateOverrideComponent` pode ser ligado em vários níveis de uma hierarquia de objetos. Uma vez que só pode haver um componente de cada tipo numa entidade, cada um `HierarchicalStateOverrideComponent` gere os estados para oculto, see-through, selecionado, cor e colisão.

Portanto, cada estado pode ser definido para um dos:

* `ForceOn`- o Estado está habilitado para toda a malha sobre e abaixo deste nó
* `ForceOff`- o Estado está incapacitado para todas as malshes abaixo deste nó
* `InheritFromParent`- o Estado não é afetado por este componente de sobreposição

Pode alterar estados diretamente ou através da `SetState` função:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

```cpp
ApiHandle<HierarchicalStateOverrideComponent> component = ...;

// set one state directly
component->HiddenState(HierarchicalEnableState::ForceOn);

// set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>Cor de tonalidade

A sobreposição da cor da tonalidade é ligeiramente especial na medida em que há um estado on/off/herdado e uma propriedade de cor de tonalidade. A porção alfa da cor da tonalidade define o peso do efeito de coloração: Se definido para 0.0, não é visível nenhuma cor de tonalidade e se definido para 1.0 o objeto será renderizado com cor de tonalidade pura. Para valores intermédios, a cor final será misturada com a cor da tonalidade. A cor da tonalidade pode ser alterada numa base por quadro para alcançar uma animação a cores.

## <a name="performance-considerations"></a>Considerações de desempenho

Um exemplo de si mesmo não acrescenta muito tempo de `HierarchicalStateOverrideComponent` execução. No entanto, é sempre uma boa prática manter o número de componentes ativos baixos. Por exemplo, ao implementar um sistema de seleção que realça o objeto escolhido, recomenda-se eliminar o componente quando o destaque for removido. Manter os componentes em torno de características neutras pode rapidamente somar.

A renderização transparente coloca mais carga de trabalho nas GPUs do servidor do que na renderização padrão. Se grandes partes do gráfico de cena forem mudadas para *o see-through,* com muitas camadas de geometria visíveis, pode tornar-se um estrangulamento de desempenho. O mesmo é válido para objetos com contornos de [seleção](../../overview/features/outlines.md#performance).

## <a name="next-steps"></a>Passos seguintes

* [Contornos](../../overview/features/outlines.md)
* [Modos de composição](../../concepts/rendering-modes.md)
* [Consultas espaciais](../../overview/features/spatial-queries.md)
