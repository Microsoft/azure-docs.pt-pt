---
title: Modelo de dados para telemetria de pedido - Azure Application Insights
description: Modelo de dados de Insights de Aplicação para telemetria de pedido
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 7a352f4ce3528d395599a91b53031c74b0873152
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87320566"
---
# <a name="request-telemetry-application-insights-data-model"></a>Telemetria de pedido: Modelo de dados de Insights de Aplicação

Um item de telemetria de pedido (in [Application Insights)](./app-insights-overview.md)representa a sequência lógica de execução desencadeada por um pedido externo à sua aplicação. Cada execução de pedido é identificada por parâmetros únicos `ID` e contendo todos os `url` parâmetros de execução. Pode agrupar os pedidos por lógica `name` e definir o presente `source` pedido. A execução de código pode resultar em `success` ou e tem um certo `fail` `duration` . Tanto as execuções de sucesso como as execuções falhadas podem ser agrupadas por `resultCode` . Hora de início para a telemetria de pedido definida no nível do envelope.

A telemetria solicite suporte o modelo de extensibilidade padrão utilizando o costume `properties` e `measurements` .

## <a name="name"></a>Name

O nome do pedido representa o caminho de código tomado para processar o pedido. Baixo valor cardinalício para permitir um melhor agrupamento de pedidos. Para pedidos HTTP representa o método HTTP e o modelo de caminho URL como `GET /values/{id}` sem o `id` valor real.

Application Insights web SDK envia o nome do pedido "como está" no que diz respeito ao caso da carta. O agrupamento de UI é sensível a casos, pelo `GET /Home/Index` que é contado separadamente, `GET /home/INDEX` embora muitas vezes resultem no mesmo controlador e execução de ação. A razão para isso é que os urls em geral são [sensíveis a casos.](https://www.w3.org/TR/WD-html40-970708/htmlweb.html) Talvez queira ver se tudo `404` aconteceu para os urls dactilografados em maiúsculas. Pode ler mais a pedido de recolha de nomes por ASP.NET Web SDK na publicação de [blog](https://apmtips.com/posts/2015-02-23-request-name-and-url/).

Comprimento máximo: 1024 caracteres

## <a name="id"></a>ID

Identificador de uma instância de chamada de pedido. Utilizado para a correlação entre pedido e outros itens de telemetria. A identificação deve ser globalmente única. Para mais informações, consulte a página [de correlação.](./correlation.md)

Comprimento máximo: 128 caracteres

## <a name="url"></a>Url

Solicite URL com todos os parâmetros de cadeia de consulta.

Comprimento máximo: 2048 caracteres

## <a name="source"></a>Origem

Fonte do pedido. Exemplos são a chave de instrumentação do chamador ou o endereço IP do chamador. Para mais informações, consulte a página [de correlação.](./correlation.md)

Comprimento máximo: 1024 caracteres

## <a name="duration"></a>Duração

Duração do pedido no formato: `DD.HH:MM:SS.MMMMMM` . Deve ser positivo e menos de `1000` dias. Este campo é necessário como telemetria de pedido representa a operação com o início e o fim.

## <a name="response-code"></a>Código de resposta

Resultado de um pedido de execução. Código de estado HTTP para pedidos HTTP. Pode ser `HRESULT` valor ou tipo de exceção para outros tipos de pedido.

Comprimento máximo: 1024 caracteres

## <a name="success"></a>Com êxito

Indicação de chamada bem sucedida ou mal sucedida. Este campo é obrigatório. Quando não for explicitamente definido `false` - um pedido é considerado como um sucesso. Desagre este valor para `false` se a operação foi interrompida por exceção ou código de resultado de erro devolvido.

Para as aplicações web, os Insights de Aplicação definem um pedido como bem sucedido quando o código de resposta é inferior `400` ou igual a `401` . No entanto, existem casos em que este mapeamento predefinido não corresponde à semântica da aplicação. O código de resposta `404` pode indicar "sem registos", o que pode fazer parte do fluxo regular. Também pode indicar uma ligação quebrada. Para os links quebrados, pode até implementar uma lógica mais avançada. Só é possível marcar ligações partidas quando essas ligações estiverem localizadas no mesmo local, analisando o url referrer. Ou marque-as como falhas quando acedidas a partir da aplicação móvel da empresa. Da mesma forma `301` e indica falha quando `302` acedido a partir do cliente que não suporta redirecionamento.

O conteúdo parcialmente aceite `206` pode indicar uma falha de um pedido global. Por exemplo, o ponto final do Application Insights recebe um lote de itens de telemetria como um único pedido. Retorna `206` quando alguns itens no lote não foram processados com sucesso. O aumento da taxa `206` indica um problema que precisa de ser investigado. Lógica semelhante `207` aplica-se ao Multi-Status onde o sucesso pode ser o pior dos códigos de resposta separados.

Pode ler mais sobre o código de resultados do pedido e o código de estado no post do [blog](https://apmtips.com/posts/2016-12-03-request-success-and-response-code/).

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medições personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Passos seguintes

- [Escreva telemetria de pedido personalizado](./api-custom-events-metrics.md#trackrequest)
- Consulte [o modelo de dados](data-model.md) para os tipos de Insights de Aplicação e modelo de dados.
- Saiba como [configurar ASP.NET](./asp-net.md) aplicação Core com Insights de Aplicação.
- Confira as [plataformas](./platforms.md) suportadas pela Application Insights.

