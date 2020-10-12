---
title: Tamanhos de servidor
description: Descreve os tamanhos distintos do servidor que podem ser atribuídos
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.custom: devx-track-csharp
ms.openlocfilehash: b37aabb39e19fa5ec53d2b006a7cbc1793adad72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90988039"
---
# <a name="server-sizes"></a>Tamanhos de servidor

A renderização remota Azure está disponível em duas configurações do servidor: `Standard` e `Premium` .

## <a name="polygon-limits"></a>Limites do polígono

Renderização remota com `Standard` servidor de tamanho tem um tamanho máximo de cena de 20 milhões de polígonos. A renderização remota com `Premium` tamanho não impõe um máximo difícil, mas o desempenho pode ser degradado se o seu conteúdo exceder as capacidades de renderização do serviço.

Quando o renderizador ligado num tamanho do servidor 'Standard' atinge esta limitação, muda a renderização para um fundo de tabuleiro de xadrez:

![A screenshot mostra uma grelha de quadrados preto e branco com um menu Tools.](media/checkerboard.png)

## <a name="specify-the-server-size"></a>Especificar o tamanho do servidor

O tipo de configuração do servidor pretendido deve ser especificado no tempo de inicialização da sessão de renderização. Não pode ser alterado numa sessão de corrida. Os seguintes exemplos de código mostram o local onde o tamanho do servidor deve ser especificado:

```cs
async void CreateRenderingSession(AzureFrontend frontend)
{
    RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
    sessionCreationParams.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend)
{
    RenderingSessionCreationParams sessionCreationParams;
    sessionCreationParams.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    if (auto createSessionAsync = frontend->CreateNewRenderingSessionAsync(sessionCreationParams))
    {
        // ...
    }
}
```

Para o [exemplo, os scripts PowerShell,](../samples/powershell-example-scripts.md)o tamanho do servidor pretendido tem de ser especificado dentro do `arrconfig.json` ficheiro:

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>Como o renderizador avalia o número de polígonos

O número de polígonos considerados para o teste de limitação é o número de polígonos que são efetivamente passados para o renderizador. Esta geometria é tipicamente a soma de todos os modelos instantâneos, mas também há exceções. Não está **incluída**a seguinte geometria:
* Casos de modelo carregados que estão totalmente fora da vista frustum.
* Modelos ou peças-modelo que são comutados para invisíveis, utilizando o [componente hierárquico de sobreposição do estado](../overview/features/override-hierarchical-state.md).

Assim, é possível escrever uma aplicação que vise o `standard` tamanho que carrega vários modelos com uma contagem de polígono perto do limite para cada modelo. Quando a aplicação apenas mostra um único modelo de cada vez, o tabuleiro de xadrez não é acionado.

### <a name="how-to-determine-the-number-of-polygons"></a>Como determinar o número de polígonos

Existem duas formas de determinar o número de polígonos de um modelo ou cena que contribuem para o limite orçamental do tamanho da `standard` configuração:
* No lado da conversão do modelo, recupere o [ficheiro json de saída de conversão](../how-tos/conversion/get-information.md)e verifique a entrada na secção `numFaces` [ *inputStatistics* ](../how-tos/conversion/get-information.md#the-inputstatistics-section)
* Se a sua aplicação estiver a lidar com conteúdo dinâmico, o número de polígonos renderizados pode ser consultado dinamicamente durante o tempo de funcionamento. Utilize uma [consulta de avaliação de desempenho](../overview/features/performance-queries.md#performance-assessment-queries) e verifique se o membro da `polygonsRendered` `FrameStatistics` estrutura. O `polygonsRendered` campo será definido para quando o `bad` renderizador atingir a limitação do polígono. O fundo de tabuleiro de verificação é sempre desvanecido com algum atraso para garantir que as ações do utilizador podem ser tomadas após esta consulta assíncronea. A ação do utilizador pode, por exemplo, ocultar ou eliminar exemplos de modelos.

## <a name="pricing"></a>Preços

Para uma repartição detalhada dos preços de cada tipo de configuração, consulte a página [de preços de renderização remota.](https://azure.microsoft.com/pricing/details/remote-rendering)

## <a name="next-steps"></a>Passos seguintes
* [Scripts do PowerShell de exemplo](../samples/powershell-example-scripts.md)
* [Conversão de modelo](../how-tos/conversion/model-conversion.md)

