---
title: Modelo de dados para telemetria de solicitação-Aplicativo Azure insights
description: Modelo de dados Application Insights para telemetria de solicitação
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: b253a95a39f118efe82e36ac7261a4d6c62a99d6
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928841"
---
# <a name="request-telemetry-application-insights-data-model"></a>Telemetria de solicitação: modelo de dados de Application Insights

Um item de telemetria de solicitação (em [Application insights](../../azure-monitor/app/app-insights-overview.md)) representa a sequência lógica de execução disparada por uma solicitação externa para seu aplicativo. Cada execução de solicitação é identificada por `ID` exclusivos e `url` que contém todos os parâmetros de execução. Você pode agrupar solicitações por `name` lógico e definir o `source` dessa solicitação. A execução de código pode resultar em `success` ou `fail` e tem um determinado `duration`. As execuções de êxito e de falha podem ser agrupadas ainda mais por `resultCode`. Hora de início da telemetria de solicitação definida no nível do envelope.

A telemetria de solicitação dá suporte ao modelo de extensibilidade padrão usando `properties` e `measurements`personalizados.

## <a name="name"></a>Nome

O nome da solicitação representa o caminho de código obtido para processar a solicitação. Valor de baixa cardinalidade para permitir melhor agrupamento de solicitações. Para solicitações HTTP, ele representa o método HTTP e o modelo de caminho de URL, como `GET /values/{id}` sem o valor real `id`.

Application Insights Web SDK envia o nome da solicitação "no estado em que se encontra" com relação ao caso de letra. O agrupamento na interface do usuário diferencia maiúsculas de minúsculas, portanto `GET /Home/Index` é contado separadamente do `GET /home/INDEX` mesmo que, muitas vezes, eles resultem na mesma execução do controlador e da ação. O motivo disso é que as URLs em geral diferenciam [maiúsculas de minúsculas](https://www.w3.org/TR/WD-html40-970708/htmlweb.html). Talvez você queira ver se todos os `404` ocorreram para as URLs digitadas em letras maiúsculas. Você pode ler mais sobre a coleção de nomes de solicitação pelo SDK da Web do ASP.NET na [postagem do blog](https://apmtips.com/blog/2015/02/23/request-name-and-url/).

Comprimento máximo: 1024 caracteres

## <a name="id"></a>ID

Identificador de uma instância de chamada de solicitação. Usado para correlação entre a solicitação e outros itens de telemetria. A ID deve ser globalmente exclusiva. Para obter mais informações, consulte a página [correlação](../../azure-monitor/app/correlation.md) .

Comprimento máximo: 128 caracteres

## <a name="url"></a>Url

URL de solicitação com todos os parâmetros de cadeia de caracteres de consulta.

Comprimento máximo: 2048 caracteres

## <a name="source"></a>Origem

Origem da solicitação. Os exemplos são a chave de instrumentação do chamador ou o endereço IP do chamador. Para obter mais informações, consulte a página [correlação](../../azure-monitor/app/correlation.md) .

Comprimento máximo: 1024 caracteres

## <a name="duration"></a>Duração

Duração da solicitação no formato: `DD.HH:MM:SS.MMMMMM`. Deve ser positivo e menor que `1000` dias. Esse campo é necessário, pois a telemetria de solicitação representa a operação com o início e o fim.

## <a name="response-code"></a>Código de resposta

Resultado de uma execução de solicitação. Código de status HTTP para solicitações HTTP. Pode ser `HRESULT` valor ou tipo de exceção para outros tipos de solicitação.

Comprimento máximo: 1024 caracteres

## <a name="success"></a>Êxito

Indicação de chamada bem-sucedida ou malsucedida. Este campo é obrigatório. Quando não definido explicitamente para `false`-uma solicitação é considerada com êxito. Defina esse valor como `false` se a operação foi interrompida por exceção ou retornou o código de resultado do erro.

Para os aplicativos Web, Application Insights definir uma solicitação como bem-sucedida quando o código de resposta for menor que `400` ou igual a `401`. No entanto, há casos em que esse mapeamento padrão não corresponde à semântica do aplicativo. O código de resposta `404` pode indicar "nenhum registro", que pode fazer parte do fluxo regular. Ele também pode indicar um link desfeito. Para os links desfeitos, você pode até mesmo implementar uma lógica mais avançada. Você pode marcar links desfeitos como falhas somente quando esses links estiverem localizados no mesmo site analisando referenciador de URL. Ou marcá-las como falhas quando acessadas por meio do aplicativo móvel da empresa. Da mesma forma `301` e `302` indica falha quando acessado do cliente que não dá suporte a redirecionamento.

O conteúdo parcialmente aceito `206` pode indicar uma falha de uma solicitação geral. Por exemplo, Application Insights ponto de extremidade recebe um lote de itens de telemetria como uma única solicitação. Ele retorna `206` quando alguns itens no lote não foram processados com êxito. A taxa crescente de `206` indica um problema que precisa ser investigado. A lógica semelhante se aplica a `207` vários status em que o sucesso pode ser o pior dos códigos de resposta separados.

Você pode ler mais sobre o código do resultado da solicitação e o código de status na [postagem do blog](https://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Passos seguintes

- [Gravar telemetria de solicitação personalizada](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)
- Consulte [modelo de dados](data-model.md) para tipos de Application insights e modelo de dados.
- Saiba como [configurar ASP.NET Core](../../azure-monitor/app/asp-net.md) aplicativo com Application insights.
- Confira as [plataformas](../../azure-monitor/app/platforms.md) com suporte pelo Application insights.
