---
title: Modelo de dados para telemetria de pedidos - Insights de aplicação Azure
description: Modelo de dados de Insights de Aplicação para telemetria de pedidos
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: d8a28063bf6780c3cace4ead81e289779b95eb9a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671907"
---
# <a name="request-telemetry-application-insights-data-model"></a>Telemetria de pedidos: Modelo de dados de Insights de Aplicação

Um item de telemetria de pedido (em Insights de [Aplicação)](../../azure-monitor/app/app-insights-overview.md)representa a sequência lógica de execução desencadeada por um pedido externo à sua aplicação. Cada execução de pedido é identificada por `ID` e `url` únicos contendo todos os parâmetros de execução. Pode agrupar pedidos por `name` lógicos e definir o `source` deste pedido. A execução do código pode resultar em `success` ou `fail` e tem um certo `duration`. Tanto as execuções de sucesso como de fracasso podem ser agrunadas ainda mais por `resultCode`. Hora de início da telemetria de pedido definida no nível do envelope.

A telemetria de pedidos suporta o modelo de extensibility padrão utilizando `properties` e `measurements`personalizados.

## <a name="name"></a>Nome

O nome do pedido representa o caminho de código tomado para processar o pedido. Baixo valor de cardeal para permitir um melhor agrupamento de pedidos. Para pedidos http representa o método HTTP e o modelo de caminho URL como `GET /values/{id}` sem o valor real `id`.

Application Insights web SDK envia nome de pedido "como está" no que diz respeito ao caso de carta. O agrupamento na UI é sensível aos casos, pelo que `GET /Home/Index` é contada separadamente de `GET /home/INDEX`, embora muitas vezes resultem no mesmo controlador e execução de ação. A razão para isso é que os urls em geral são [sensíveis aos casos.](https://www.w3.org/TR/WD-html40-970708/htmlweb.html) Talvez queira ver se todos os `404` aconteceram para as urls dactilografadas em maiúsculas. Pode ler mais sobre a recolha de nomes de pedido por ASP.NET Web SDK na publicação do [blog](https://apmtips.com/blog/2015/02/23/request-name-and-url/).

Comprimento máximo: 1024 caracteres

## <a name="id"></a>ID

Identificador de uma chamada de pedido. Utilizado para a correlação entre pedido e outros itens de telemetria. A identificação deve ser globalmente única. Para mais informações, consulte a [página de correlação.](../../azure-monitor/app/correlation.md)

Comprimento máximo: 128 caracteres

## <a name="url"></a>Url

Solicite URL com todos os parâmetros de corda de consulta.

Comprimento máximo: 2048 caracteres

## <a name="source"></a>Origem

Fonte do pedido. Exemplos são a chave de instrumentação do chamador ou o endereço ip do chamador. Para mais informações, consulte a [página de correlação.](../../azure-monitor/app/correlation.md)

Comprimento máximo: 1024 caracteres

## <a name="duration"></a>Duração

Duração do pedido em formato: `DD.HH:MM:SS.MMMMMM`. Deve ser positivo e menos de `1000` dias. Este campo é necessário como pedido de telemetria representa a operação com o início e o fim.

## <a name="response-code"></a>Código de resposta

Resultado de uma execução de pedido. Código de estado HTTP para pedidos HTTP. Pode ser `HRESULT` valor ou tipo de exceção para outros tipos de pedidos.

Comprimento máximo: 1024 caracteres

## <a name="success"></a>Êxito

Indicação de chamada bem sucedida ou mal sucedida. Este campo é necessário. Quando não é explicitamente definido para `false` - um pedido é considerado como tendo sucesso. Desloque este valor para `false` se o funcionamento foi interrompido por exceção ou código de resultado de erro devolvido.

Para as aplicações web, os Insights de Aplicação definem um pedido como bem sucedido quando o código de resposta é inferior a `400` ou igual a `401`. No entanto, há casos em que este mapeamento padrão não corresponde ao semântico da aplicação. O código de resposta `404` pode indicar "nenhum registo", que pode fazer parte do fluxo regular. Também pode indicar uma ligação partida. Para os links quebrados, pode até implementar uma lógica mais avançada. Só é possível marcar links quebrados como falhas quando esses links estiverem localizados no mesmo site através da análise do url referrer. Ou marque-as como falhas quando acedidas a partir da aplicação móvel da empresa. Da mesma forma, `301` e `302` indica falha quando acedida pelo cliente que não suporta redirecionar.

Conteúdo parcialmente aceite `206` pode indicar uma falha de um pedido global. Por exemplo, o ponto final da Application Insights recebe um lote de artigos de telemetria como um único pedido. Devolve `206` quando alguns itens do lote não foram processados com sucesso. O aumento da taxa de `206` indica um problema que tem de ser investigado. Lógica semelhante aplica-se a `207` Multi-Status onde o sucesso pode ser o pior de códigos de resposta separados.

Pode ler mais sobre código de resultados de pedido e código de estado no post do [blog](https://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medições personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Passos seguintes

- [Escreva telemetria de pedido personalizado](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)
- Consulte o modelo de [dados](data-model.md) para tipos de Insights de Aplicação e modelo de dados.
- Saiba como [configurar ASP.NET](../../azure-monitor/app/asp-net.md) aplicação Core com Insights de Aplicação.
- Confira [as plataformas](../../azure-monitor/app/platforms.md) suportadas por Application Insights.
