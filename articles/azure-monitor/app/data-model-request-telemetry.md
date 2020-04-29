---
title: Modelo de dados para telemetria de pedidos - Insights de aplicação Azure
description: Modelo de dados de Insights de Aplicação para telemetria de pedidos
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: d8a28063bf6780c3cace4ead81e289779b95eb9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671907"
---
# <a name="request-telemetry-application-insights-data-model"></a>Telemetria de pedidos: Modelo de dados de Insights de Aplicação

Um item de telemetria de pedido (em Insights de [Aplicação)](../../azure-monitor/app/app-insights-overview.md)representa a sequência lógica de execução desencadeada por um pedido externo à sua aplicação. Cada execução de pedido `ID` `url` é identificada por únicos e contendo todos os parâmetros de execução. Pode agrupar pedidos `name` por `source` lógica e definir o pedido deste pedido. A execução `success` do `fail` código pode `duration`resultar em ou e tem um certo . Tanto as execuções de sucesso como `resultCode`de fracasso podem ser agrunadas por . Hora de início da telemetria de pedido definida no nível do envelope.

A telemetria de pedidos suporta o `properties` modelo `measurements`de extensibility padrão usando o costume e .

## <a name="name"></a>Nome

O nome do pedido representa o caminho de código tomado para processar o pedido. Baixo valor de cardeal para permitir um melhor agrupamento de pedidos. Para pedidos http representa o método HTTP e `GET /values/{id}` o `id` modelo de caminho URL como sem o valor real.

Application Insights web SDK envia nome de pedido "como está" no que diz respeito ao caso de carta. O agrupamento na UI é `GET /Home/Index` sensível aos casos, pelo que é contado separadamente, `GET /home/INDEX` embora muitas vezes resultem no mesmo controlador e execução de ação. A razão para isso é que os urls em geral são [sensíveis aos casos.](https://www.w3.org/TR/WD-html40-970708/htmlweb.html) Talvez queira ver se `404` tudo aconteceu para as urls dactilografadas em maiúsculas. Pode ler mais sobre a recolha de nomes de pedido por ASP.NET Web SDK na publicação do [blog](https://apmtips.com/blog/2015/02/23/request-name-and-url/).

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

Duração do `DD.HH:MM:SS.MMMMMM`pedido em formato: . Deve ser positivo `1000` e menos de dias. Este campo é necessário como pedido de telemetria representa a operação com o início e o fim.

## <a name="response-code"></a>Código de resposta

Resultado de uma execução de pedido. Código de estado HTTP para pedidos HTTP. Pode ser `HRESULT` valor ou tipo de exceção para outros tipos de pedidos.

Comprimento máximo: 1024 caracteres

## <a name="success"></a>Êxito

Indicação de chamada bem sucedida ou mal sucedida. Este campo é obrigatório. Quando não é `false` explicitamente definido - um pedido é considerado como tendo sucesso. Detete `false` este valor para se o funcionamento foi interrompido por exceção ou código de resultado de erro devolvido.

Para as aplicações web, os Insights de Aplicação definem um pedido como bem sucedido quando o código de resposta é inferior `400` ou igual a `401`. No entanto, há casos em que este mapeamento padrão não corresponde ao semântico da aplicação. O `404` código de resposta pode indicar "sem registos", que podem fazer parte do fluxo regular. Também pode indicar uma ligação partida. Para os links quebrados, pode até implementar uma lógica mais avançada. Só é possível marcar links quebrados como falhas quando esses links estiverem localizados no mesmo site através da análise do url referrer. Ou marque-as como falhas quando acedidas a partir da aplicação móvel da empresa. Da `301` mesma `302` forma e indica falha quando acedida pelo cliente que não suporta redirecionar.

O conteúdo `206` parcialmente aceite pode indicar uma falha de um pedido global. Por exemplo, o ponto final da Application Insights recebe um lote de artigos de telemetria como um único pedido. Retorna `206` quando alguns itens do lote não foram processados com sucesso. O aumento `206` da taxa de indica um problema que precisa de ser investigado. Lógica semelhante aplica-se ao `207` Multi-Status onde o sucesso pode ser o pior de códigos de resposta separados.

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
