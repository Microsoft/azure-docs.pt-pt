---
title: Modelam de dados de telemetria do Azure Application Insights - pedido de telemetria | Documentos da Microsoft
description: Modelo de dados do Application Insights para a telemetria de pedido
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: fef016d87cc60bc916fdcb08f92171e115221fe5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884125"
---
# <a name="request-telemetry-application-insights-data-model"></a>Telemetria de pedido: Modelo de dados do Application Insights

Um item de telemetria de pedido (no [Application Insights](../../azure-monitor/app/app-insights-overview.md)) representa a sequência lógica de execução acionada por um pedido externo à sua aplicação. Cada execução de solicitação é identificada por exclusivo `ID` e `url` que contém todos os parâmetros de execução. Pode agrupar solicitações ao lógico `name` e defina o `source` deste pedido. Execução de código pode resultar em `success` ou `fail` e tem um certo `duration`. Execuções com êxito e falha podem ser agrupadas também pelos `resultCode`. Hora de início para a telemetria de pedido definida no nível de envelope.

Pedido de telemetria suporta o modelo de extensibilidade padrão usando custom `properties` e `measurements`.

## <a name="name"></a>Name

Nome do pedido representa o caminho de código para processar o pedido. Valor de cardinalidade baixa para permitir a melhor de agrupamento de pedidos. Para solicitações de HTTP, ele representa o método HTTP e o modelo de caminho de URL, como `GET /values/{id}` sem o real `id` valor.

Web do Application Insights SDK envia o nome do pedido "como estão" com respeito entre maiúsculas/minúsculas. Agrupamento pela interface do Usuário diferencia maiúsculas de minúsculas, de modo `GET /Home/Index` são contadas em separado da `GET /home/INDEX` , apesar de muitas vezes eles resultam na execução de controlador e ação mesmo. O motivo disso é que em geral são urls [diferencia maiúsculas de minúsculas](https://www.w3.org/TR/WD-html40-970708/htmlweb.html). Pode querer ver se todos os `404` aconteceu para os urls de tipos em maiúsculas. Pode ler mais coleção do nome do pedido ativado pelo SDK de Web de ASP.NET no [mensagem de blogue](https://apmtips.com/blog/2015/02/23/request-name-and-url/).

Comprimento máximo: 1024 carateres

## <a name="id"></a>ID

Identificador de uma instância de chamada de pedido. Utilizado para a correlação entre a pedido e outros itens de telemetria. ID deve ser globalmente exclusivo. Para obter mais informações, consulte [correlação](../../azure-monitor/app/correlation.md) página.

Comprimento máximo: 128 carateres

## <a name="url"></a>Url

URL do pedido com todos os parâmetros de cadeia de caracteres de consulta.

Comprimento máximo: 2048 carateres

## <a name="source"></a>Origem

Origem do pedido. Os exemplos são a chave de instrumentação do chamador ou o endereço ip do chamador. Para obter mais informações, consulte [correlação](../../azure-monitor/app/correlation.md) página.

Comprimento máximo: 1024 carateres

## <a name="duration"></a>Duração

Duração no formato do pedido: `DD.HH:MM:SS.MMMMMM`. Tem de ser positivo e menor que `1000` dias. Este campo é obrigatório, como a telemetria de pedido representa a operação com a inicial e final.

## <a name="response-code"></a>Código de resposta

Resultado da execução de um pedido. Código de estado HTTP para pedidos HTTP. Pode ser `HRESULT` tipo de valor ou a exceção para outros tipos de pedido.

Comprimento máximo: 1024 carateres

## <a name="success"></a>Êxito

Indicação de chamada com êxito ou sem êxito. Este campo é obrigatório. Quando não definido explicitamente como `false` -um pedido é considerado como estando concluída com êxito. Definir este valor como `false` se a operação foi interrompida por exceção ou devolveu o código de resultado de erro.

Para as aplicações web, o Application Insights definir um pedido com êxito como quando o código de resposta for inferior a `400` ou igual a `401`. No entanto há casos em que este mapeamento padrão não coincide com a semântica do aplicativo. Código de resposta `404` pode indicar que "não existem registos", que podem fazer parte do fluxo regular. Também pode indicar um link desfeito. Para ligações quebradas, pode até mesmo implementar lógica mais avançada. Pode marcar ligações quebradas como falhas apenas quando essas ligações estão localizadas no mesmo site através da análise de Referenciador de url. Ou marcá-los como falhas quando acede a partir da aplicação móvel da empresa. Da mesma forma `301` e `302` indica falha quando acede a partir do cliente que não suporta o redirecionamento.

Parcialmente aceites conteúdo `206` pode indicar uma falha de um pedido geral. Por exemplo, o ponto final do Application Insights recebe um lote de itens de telemetria como um único pedido. Ele retorna `206` quando alguns itens no lote não foram processados com êxito. Taxa de cada vez maior de `206` indica um problema que tem de ser investigado. Uma lógica similar aplica-se a `207` multi estado em que o sucesso pode ser o pior dos códigos de resposta à parte.

Pode ler mais resultado do pedido no código e o código de estado no [mensagem de blogue](https://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medições personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Passos Seguintes

- [Escrever telemetria personalizada do pedido](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)
- Ver [modelo de dados](data-model.md) para o modelo de tipos e dados do Application Insights.
- Saiba como [configurar o ASP.NET Core](../../azure-monitor/app/asp-net.md) aplicação com o Application Insights.
- Confira [plataformas](../../azure-monitor/app/platforms.md) suportada pelo Application Insights.
