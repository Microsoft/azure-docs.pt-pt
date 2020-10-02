---
title: Sobre
description: Introdução à renderização remota do Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: overview
ms.openlocfilehash: 14ebe9dfd11bed2dfaded9f18e23f73d33a708ae
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91650464"
---
# <a name="about-azure-remote-rendering"></a>Acerca do Azure Remote Rendering

> [!IMPORTANT]
> **A Azure Remote Rendering** está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*A Azure Remote Rendering* (ARR) é um serviço que lhe permite renderizar conteúdo 3D interativo de alta qualidade na nuvem e transmiti-lo em tempo real para dispositivos, como os HoloLens 2.

![Diagrama que mostra um exemplo de motor 3D interativo de alta qualidade.](../media/arr-engine.png)

Os dispositivos não amarrados têm uma potência computacional limitada para renderizar modelos complexos. No entanto, para muitas aplicações seria inaceitável reduzir de qualquer forma a fidelidade visual. A imagem a seguir compara o modelo em total detalhe com um modelo que foi dizimado usando uma ferramenta comum de criação de conteúdo:

![Modelo de exemplo](./media/engine-model-decimated.png)

O modelo reduzido consiste em aproximadamente 200.000 triângulos (incluindo as partes internas detalhadas), contra mais de 18 milhões de triângulos no modelo original.

*A renderização remota* resolve este problema movendo a carga de trabalho de renderização para GPUs de alta qualidade na nuvem. Um motor gráfico em nuvem torna a imagem, codifica-a como um fluxo de vídeo, e transmite-a para o dispositivo alvo.

## <a name="hybrid-rendering"></a>Renderização híbrida

Na maioria das aplicações, não basta apenas tornar um modelo complexo. Também precisa de UI personalizado para fornecer funcionalidade ao utilizador. A renderização remota Azure não o obriga a usar uma estrutura de UI dedicada, em vez disso suporta *a renderização híbrida*. Isto significa que pode renderizar elementos no dispositivo, utilizando o seu método preferido, como [o MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/GettingStartedWithTheMRTK.html).

No final de uma moldura, a renderização remota Azure combina automaticamente o seu conteúdo renderizado localmente com a imagem remota. É até capaz de fazê-lo com a oclusão correta.

## <a name="multi-gpu-rendering"></a>Renderização multi-GPU

Alguns modelos são demasiado complexos para renderizar em taxas de fotogramas interativas, mesmo para uma GPU de alta qualidade. Especialmente na visualização industrial, este é um problema comum. Para aumentar ainda mais os limites, a renderização remota Azure pode distribuir a carga de trabalho em várias GPUs. Os resultados são fundidos numa única imagem, tornando o processo inteiramente transparente para o utilizador.

## <a name="high-level-architecture"></a>Arquitetura geral

Este diagrama ilustra a arquitetura de renderização remota:

![Arquitetura](./media/arr-high-level-architecture.png)

Um ciclo completo para a geração de imagem envolve os seguintes passos:

1. Lado do cliente: Configuração do quadro
    1. O seu código: A entrada do utilizador é processada, o gráfico de cena é atualizado
    1. Código ARR: Atualizações de gráficos de cena e pose de cabeça prevista são enviadas para o servidor
1. Lado do servidor: Renderização remota
    1. Motor de renderização distribui renderização em GPUs disponíveis
    1. Saída de vários GPUs é composta em imagem única
    1. A imagem é codificada como transmissão de vídeo, enviada de volta para o cliente
1. Lado do cliente: Finalização
    1. O seu código: Conteúdo local opcional (UI, marcadores, ...) é renderizado
    1. Código ARR: Em 'presente', o conteúdo renderizado localmente é automaticamente fundido com fluxo de vídeo

A latência da rede é o principal problema. O tempo de viragem entre enviar um pedido e receber o resultado é tipicamente demasiado longo para taxas de fotogramas interativas. Portanto, mais de um quadro pode estar em voo a qualquer momento.

## <a name="next-steps"></a>Passos seguintes

* [Requisitos de sistema](system-requirements.md)
* [Quickstart: Renderiza um modelo com Unidade](../quickstarts/render-model.md)
