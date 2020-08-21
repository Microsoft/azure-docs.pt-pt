---
title: O que é extensão de gráfico de mídia - Azure
description: O Live Video Analytics on IoT Edge permite-lhe alargar as capacidades de processamento de gráficos de mídia através de um nó de extensão de gráfico.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 09fc2b71a6188caf1bdd9b7a2ef4da287f022845
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691946"
---
# <a name="media-graph-extension"></a>Extensão de gráfico de mídia

O Live Video Analytics on IoT Edge permite-lhe alargar as capacidades de processamento de gráficos de mídia através de um nó de extensão de gráfico. O plugin de extensão analítica pode utilizar técnicas tradicionais de processamento de imagem ou modelos de IA de visão computacional. As extensões de gráfico são ativadas através da inclusão de um nó de processador de extensão num gráfico de mídia. O nó do processador de extensão transmite quadros de vídeo para o ponto final configurado e funciona como interface para a sua extensão. A ligação pode ser feita a um ponto final local ou remoto e pode ser protegida por autenticação e encriptação TLS, se necessário. Além disso, o nó do processador de extensão de gráfico permite a dimensionamento e codificação opcionais dos quadros de vídeo antes de serem submetidos à sua extensão personalizada.

Live Video Analytics suporta dois tipos de processadores de extensão de gráficos de mídia:

* [Processador de extensão HTTP](media-graph-concept.md#http-extension-processor)
* [processador de extensão gRPC](media-graph-concept.md#grpc-extension-processor)

## <a name="http-extension-processor"></a>Processador de extensão HTTP

O processador de extensão HTTP permite cenários de extensibilidade utilizando o protocolo HTTP, onde o desempenho e/ou a utilização ótima dos recursos não são a principal preocupação. Pode expor a sua própria IA a um gráfico de mídia através de um ponto final HTTP REST. 

Utilize o nó do processador de extensão HTTP quando:

* Pretende uma melhor interoperabilidade com os sistemas de inferencing HTTP existentes.
* A transferência de dados de baixo desempenho é aceitável.
* Pretende utilizar uma simples interface de resposta a pedidos para o Live Video Analytics.

## <a name="grpc-extension-processor"></a>processador de extensão gRPC

O processador de extensão gRPC permite cenários de extensibilidade utilizando o protocolo estruturado baseado em gRPC e altamente performante. É ideal para cenários onde o desempenho e/ou a utilização ideal de recursos é uma prioridade. O processador de extensão gRPC permite-lhe obter o pleno benefício das definições de dados estruturadas. o gRPC oferece um elevado desempenho de transferência de conteúdo utilizando:

* [memória partilhada na caixa](https://en.wikipedia.org/wiki/Shared_memory) ou 
* incorporando diretamente o conteúdo no corpo de mensagens gRPC. 

O processador de extensão gRPC pode ser utilizado para o envio de propriedades de mídia, juntamente com a troca de mensagens de inferência.
Então, use um nó de processador de extensão gRPC quando:

* Pretender utilizar um contrato estruturado (por exemplo, mensagens estruturadas para pedidos e respostas)
* Utilize tampão de protocolo[(protobuf)](https://developers.google.com/protocol-buffers)como o seu formato de intercâmbio de mensagens subjacente para comunicação.
* Pretender comunicar com um servidor gRPC numa sessão de stream única em vez do modelo tradicional de resposta a pedidos que necessite de um manipulador de pedidos personalizado para analisar os pedidos de entrada e ligar para as funções de implementação certas. 
* O quero baixa latência e alta comunicação de produção entre o Live Video Analytics e o seu módulo.

## <a name="use-your-inferencing-model-with-live-video-analytics"></a>Use o seu modelo de inferencing com Live Video Analytics

As extensões de gráficos de mídia permitem executar modelos de inferência à sua escolha em qualquer tempo de inferência disponível, tais como ONNX, TensorFlow, PyTorch ou outros no seu próprio recipiente de estivadores. A extensão personalizada de inferenculação deve ser implementada ao lado do módulo de borda live video analytics para melhor desempenho e será depois invocada através do processador de extensão HTTP ou do processador de extensão gRPC incluído na sua topologia de gráfico. Além disso, a frequência das chamadas para a sua extensão personalizada pode ser acelerada adicionando opcionalmente um [processador de detetor de movimento](media-graph-concept.md#motion-detection-processor) e um processador de filtro de taxa de [fotogramas](media-graph-concept.md#frame-rate-filter-processor) a montante para o processador de extensão de mídia.

O diagrama abaixo retrata o fluxo de dados de alto nível:
 
![Fluxo de dados](./media/media-graph-extension/data-flow.png)

## <a name="samples"></a>Amostras

Confira algumas das nossas amostras de [caderno Jupyter](https://jupyter.org/) para Live Video Analytics. Estes cadernos fornecer-lhe-ão instruções passo a passo sobre:

* Como criar uma imagem de contentor Docker de um serviço de extensão
* Como implantar o serviço de extensão como um recipiente juntamente com o recipiente Live Video Analytics
* Como utilizar um gráfico de mídia Live Video Analytics com um cliente de extensão e apontá-lo para o ponto final de extensão gRPC
