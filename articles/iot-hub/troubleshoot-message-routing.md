---
title: Resolução de problemas Azure IoT encaminhamento de mensagens
description: Como realizar resolução de problemas para o encaminhamento de mensagens Azure IoT
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: asrastog
ms.openlocfilehash: 871a4c7d99fc44cf9868f19e41560e6e7a2e22f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793325"
---
# <a name="troubleshooting-message-routing"></a>Encaminhamento de mensagens de resolução de problemas

Este artigo fornece orientações de monitorização e resolução de problemas para questões comuns e resolução para [o encaminhamento de mensagens](iot-hub-devguide-messages-d2c.md)IoT Hub . 

## <a name="monitoring-message-routing"></a>Encaminhamento de mensagens de monitorização

[As métricas do IoT Hub](iot-hub-metrics.md) listam todas as métricas que são ativadas por padrão para o seu Hub IoT. Recomendamos que monitorize métricas relacionadas com o encaminhamento de mensagens e pontos finais para lhe dar uma visão geral das mensagens enviadas. Ligue também [os registos de diagnóstico](iot-hub-monitor-resource-health.md) nas definições de diagnóstico do Azure Monitor, para acompanhar as operações das **rotas**. Estes registos de diagnóstico podem ser enviados para registos do Monitor Azure, Centros de Eventos ou Armazenamento Azure para processamento personalizado. Aprenda a [configurar e a utilizar métricas e registos de diagnóstico com um Hub IoT](tutorial-use-metrics-and-diags.md).

Recomendamos também que se permita ativar a [rota de retorno](iot-hub-devguide-messages-d2c.md#fallback-route) se pretender manter mensagens que não correspondam à consulta em nenhuma das rotas. Estes podem ser conservados no [ponto final incorporado](iot-hub-devguide-messages-read-builtin.md) para a quantidade de dias de retenção configurados. 

## <a name="top-issues"></a>Principais questões

Seguem-se as questões mais comuns observadas com o encaminhamento de mensagens. Para começar a resolução de problemas, clique na questão para obter passos detalhados.

* [As mensagens dos meus dispositivos não estão a ser encaminhadas como esperado.](#messages-from-my-devices-are-not-being-routed-as-expected)
* [De repente parei de receber mensagens no ponto final do Event Hubs.](#i-suddenly-stopped-getting-messages-at-the-built-in-endpoint)

### <a name="messages-from-my-devices-are-not-being-routed-as-expected"></a>As mensagens dos meus dispositivos não estão a ser encaminhadas como esperado.

Para resolver este problema, analise o seguinte.

#### <a name="the-routing-metrics-for-this-endpoint"></a>As métricas de encaminhamento para este ponto final
Todas as [métricas do IoT Hub relacionadas](iot-hub-devguide-endpoints.md) com o encaminhamento estão prefixadas com *encaminhamento*. Pode combinar informações de várias métricas para identificar a causa principal para problemas. Por exemplo, utilize tentativas de **entrega de encaminhamento métricas** para identificar o número de mensagens que foram entregues num ponto final ou que foram deixadas cair quando não correspondem a consultas em nenhuma das rotas e a rota de retorno foi desativada. Verifique a métrica **de latência de encaminhamento** para observar se a latência para a entrega da mensagem é constante ou crescente. Uma latência crescente pode indicar um problema com um ponto final específico e recomendamos verificar [a saúde do ponto final](#the-health-of-the-endpoint). Estas métricas de encaminhamento também têm [dimensões](iot-hub-metrics.md#dimensions) que fornecem detalhes sobre a métrica como o tipo de ponto final, nome de ponto final específico e uma razão pela qual a mensagem não foi entregue.

#### <a name="the-diagnostic-logs-for-any-operational-issues"></a>Os registos de diagnóstico para quaisquer problemas operacionais 
Observe os [registos de diagnóstico](iot-hub-monitor-resource-health.md#routes) das **rotas** para obter mais informações sobre as [operações](#operation-names) de encaminhamento e ponto final ou identificar erros e [código de erro](#common-error-codes) relevante para entender melhor o problema. Por exemplo, o nome de operação **RouteEvaluationError** no registo indica que a rota não pôde ser avaliada devido a um problema com o formato de mensagem. Utilize as dicas fornecidas para os nomes de [operação específicos](#operation-names) para mitigar o problema. Quando um evento é registado como um erro, o registo também fornecerá mais informações sobre o porquê da avaliação ter falhado. Por exemplo, se o nome da operação for **EndpointUnhealthy,** um código de [erro](#common-error-codes) de 403004 indica que o ponto final ficou sem espaço.

#### <a name="the-health-of-the-endpoint"></a>A saúde do ponto final
Utilize a API REST [Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) para obter o estado de [saúde](iot-hub-devguide-endpoints.md#custom-endpoints) dos pontos finais. A *API get Endpoint Health* também fornece informações sobre a última vez que uma mensagem foi enviada com sucesso para o ponto final, o [último erro conhecido](#last-known-errors-for-iot-hub-routing-endpoints), última vez de erro conhecido e a última tentativa de envio para este ponto final. Utilize a possível mitigação prevista para o [último erro específico conhecido](#last-known-errors-for-iot-hub-routing-endpoints).

### <a name="i-suddenly-stopped-getting-messages-at-the-built-in-endpoint"></a>De repente parei de receber mensagens no ponto final embutido.

Para resolver este problema, analise o seguinte.

#### <a name="was-a-new-route-created"></a>Foi criada uma nova rota?
Uma vez criada uma rota, os dados param de fluir para o ponto final incorporado, a menos que seja criada uma rota para esse ponto final. Para garantir que as mensagens continuam a fluir para o ponto final incorporado se for adicionada uma nova rota, configure uma rota para o ponto final dos *eventos.* 

#### <a name="was-the-fallback-route-disabled"></a>A rota do Fallback foi desativada?
A rota de retorno envia todas as mensagens que não satisfazem as condições de consulta em qualquer uma das rotas existentes para os [Hubs incorporados em Eventos](iot-hub-devguide-messages-read-builtin.md) (mensagens/eventos), que são compatíveis com os Centros de [Eventos.](https://docs.microsoft.com/azure/event-hubs/) Se o encaminhamento de mensagens estiver ligado, pode ativar a capacidade da rota de recuo. Se não houver rotas para o ponto de entrada embutido e uma rota de retorno estiver ativada, apenas mensagens que não correspondam a quaisquer condições de consulta nas rotas serão enviadas para o ponto de finalidade incorporado. Além disso, se todas as rotas existentes forem eliminadas, a rota de retorno deve ser ativada para receber todos os dados no ponto de extremidade incorporado.

Pode ativar/desativar a rota de recuo na lâmina de encaminhamento de mensagens de >portal Azure. Também pode utilizar o Azure Resource Manager para [FallbackRouteProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) para utilizar um ponto final personalizado para a rota de retorno.

## <a name="last-known-errors-for-iot-hub-routing-endpoints"></a>Últimos erros conhecidos para pontos finais de encaminhamento IoT Hub

<a id="last-known-errors"></a>
[!INCLUDE [iot-hub-include-last-known-errors](../../includes/iot-hub-include-last-known-errors.md)]

## <a name="routes-diagnostic-logs"></a>Rotas registos de diagnóstico

Seguem-se os nomes de operação e os códigos de erro registados nos [registos de diagnóstico](iot-hub-monitor-resource-health.md#routes).

<a id="diagnostics-operation-names"></a>
### <a name="operation-names"></a>Nomes da Operação

[!INCLUDE [iot-hub-diagnostics-operation-names](../../includes/iot-hub-diagnostics-operation-names.md)]

<a id="diagnostics-error-codes"></a>
### <a name="common-error-codes"></a>Códigos de erro comuns

[!INCLUDE [iot-hub-diagnostics-error-codes](../../includes/iot-hub-diagnostics-error-codes.md)]

## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio**.
