---
title: Acerca de
description: Introdução na renderização remota azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: overview
ms.openlocfilehash: e40109a3c43fa30d70b7a13243723d717b4003c4
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021449"
---
# <a name="about-azure-remote-rendering"></a>Acerca do Azure Remote Rendering

> [!IMPORTANT]
> **A Renderização Remota Azure** está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

*Azure Remote Rendering* (ARR) é um serviço que lhe permite renderizar conteúdo 3D interativo de alta qualidade na nuvem e transmiti-lo em tempo real para dispositivos, como os HoloLens 2.

![Modelo de amostra](../media/arr-engine.png)

Os dispositivos sem amarras têm um poder computacional limitado para a renderização de modelos complexos. Para muitas aplicações, seria inaceitável, no entanto, reduzir a fidelidade visual de qualquer forma. A seguinte imagem compara o modelo com um modelo que foi dizimado usando uma ferramenta comum de criação de conteúdo:

![Modelo de amostra](./media/engine-model-decimated.png)

O modelo reduzido consiste em aproximadamente 200.000 triângulos (incluindo as partes interiores detalhadas), contra mais de 18 milhões de triângulos no modelo original.

*A Renderização Remota* resolve este problema movendo a carga de trabalho de renderização para GPUs topo de gama na nuvem. Um motor gráfico com cloud-hosted torna a imagem, codifica-a como um fluxo de vídeo, e transmite isso para o dispositivo alvo.

## <a name="hybrid-rendering"></a>Renderização híbrida

Na maioria das aplicações, não basta apenas tornar um modelo complexo. Também precisa de UI personalizado para fornecer funcionalidade ao utilizador. A Renderização Remota Azure não o obriga a usar um quadro de UI dedicado, em vez disso suporta *a renderização híbrida.* Isto significa que pode renderizar elementos no dispositivo, utilizando o seu método preferido, como [mrtk](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/GettingStartedWithTheMRTK.html).

No final de uma moldura, a Renderização Remota Azure combina automaticamente o seu conteúdo renderizado localmente com a imagem remota. É mesmo capaz de fazê-lo com oclusão correta.

## <a name="multi-gpu-rendering"></a>Renderização multi-GPU

Alguns modelos são demasiado complexos para renderizar a taxas de fotogramas interativas, mesmo para uma GPU de alta qualidade. Especialmente na visualização industrial, este é um problema comum. Para empurrar ainda mais os limites, a Renderização Remota Azure pode distribuir a carga de trabalho por múltiplas GPUs. Os resultados são fundidos numa única imagem, tornando o processo totalmente transparente para o utilizador.

## <a name="high-level-architecture"></a>Arquitetura geral

Este diagrama ilustra a arquitetura de renderização remota:

![Arquitetura](./media/arr-high-level-architecture.png)

Um ciclo completo para a geração de imagens envolve os seguintes passos:

1. Lado do cliente: Configuração de quadros
    1. O seu código: A entrada do utilizador é processada, o gráfico de cena é atualizado
    1. Código ARR: Atualizações de gráficos de cena e pose de cabeça prevista são enviadas para o servidor
1. Lado do servidor: renderização remota
    1. Motor de renderização distribui renderização em GPUs disponíveis
    1. A saída de múltiplas GPUs é composta em imagem única
    1. Imagem é codificada como fluxo de vídeo, enviada de volta ao cliente
1. Lado do cliente: Finalização
    1. O seu código: O conteúdo local opcional (UI, marcadores, ...) é renderizado
    1. Código ARR: No 'presente', o conteúdo renderizado localmente é automaticamente fundido com fluxo de vídeo

A latência da rede é o principal problema. O tempo de viragem entre o envio de um pedido e a receção do resultado é normalmente demasiado longo para as taxas de fotogramas interativas. Portanto, mais de um quadro pode estar em voo a qualquer momento.

## <a name="next-steps"></a>Próximos passos

* [Requisitos de sistema](system-requirements.md)
* [Quickstart: Render um modelo com Unidade](../quickstarts/render-model.md)
