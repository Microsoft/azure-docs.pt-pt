---
title: Escalamento orientado para eventos em Funções Azure
description: Explica os comportamentos de escala do plano de consumo e aplicações de função de plano Premium.
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: azure-functions
ms.openlocfilehash: 8aca1ab6629f95ef9162e1247434bd3189d5a7d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97937751"
---
# <a name="event-driven-scaling-in-azure-functions"></a>Escalamento orientado para eventos em Funções Azure

Nos planos Consumption and Premium, o Azure Functions escala o CPU e os recursos de memória adicionando instâncias adicionais do anfitrião funções. O número de ocorrências é determinado no número de eventos que desencadeiam uma função. 

Cada instância do hospedeiro de Funções no plano de Consumo está limitada a 1,5 GB de memória e a um CPU.  Um caso do anfitrião é toda a aplicação de função, o que significa que todas as funções dentro de uma aplicação de função partilham recurso dentro de um caso e escala ao mesmo tempo. As aplicações de função que partilham a mesma escala do plano de consumo de forma independente.  No plano Premium, o tamanho do plano determina a memória disponível e CPU para todas as aplicações nesse plano nesse caso.  

Os ficheiros de código de função são armazenados nas ações do Azure Files na conta principal de armazenamento da função. Quando elimina a conta de armazenamento principal da aplicação de função, os ficheiros de código de função são eliminados e não podem ser recuperados.

## <a name="runtime-scaling"></a>Escalagem de tempo de execução

A Azure Functions utiliza um componente chamado controlador de *escala* para monitorizar a taxa de eventos e determinar se deve escalar ou escalar. O controlador de escala utiliza heurística para cada tipo de gatilho. Por exemplo, quando se está a usar um gatilho de armazenamento da Fila Azure, ele escala com base no comprimento da fila e na idade da mensagem de fila mais antiga.

A unidade de escala para Funções Azure é a aplicação de função. Quando a aplicação de função é dimensionada, são atribuídos recursos adicionais para executar várias instâncias do anfitrião Azure Functions. Inversamente, à medida que a procura de cálculo é reduzida, o controlador de escala remove os casos de hospedeiro da função. O número de casos é eventualmente "dimensionado" para zero quando não há funções a funcionar dentro de uma aplicação de função.

![Eventos de monitorização do controlador de escala e criação de instâncias](./media/functions-scale/central-listener.png)

## <a name="cold-start"></a>Início a Frio

Depois de a sua aplicação de função ter estado inativa durante alguns minutos, a plataforma pode escalar o número de casos em que a sua aplicação se reduz a zero. O próximo pedido tem a latência adicional de escala de zero para um. Esta latência é referida como um _começo a frio._ O número de dependências exigidas pela sua aplicação de função pode ter impacto na hora de início a frio. O arranque a frio é mais um problema para operações sincronizadas, como os gatilhos HTTP que devem devolver uma resposta. Se os arranques frios estiverem a afetar as suas funções, considere executar num plano Premium ou num plano dedicado com o **Always on** setting ativado.   

## <a name="understanding-scaling-behaviors"></a>Compreender comportamentos de escala

A escala pode variar em vários fatores, e escalar de forma diferente com base no gatilho e na linguagem selecionada. Há algumas complexidades de comportamentos de escala para estar ciente de:

* **Ocorrências máximas:** Uma única aplicação de função apenas escala para um máximo de 200 instâncias. Uma única instância pode processar mais do que uma mensagem ou pedido de cada vez, por isso não há um limite definido para o número de execuções simultâneas.  Pode [especificar uma](#limit-scale-out) escala máxima mais baixa para o acelerador, conforme necessário.
* **Taxa de caso novo:** Para os gatilhos HTTP, são atribuídas novas instâncias, no máximo, uma vez por segundo. Para os gatilhos não-HTTP, são atribuídas novas instâncias, no máximo, uma vez a cada 30 segundos. A escala é mais rápida quando funciona num [plano Premium.](functions-premium-plan.md)
* **Eficiência da escala:** Para os gatilhos de Service Bus, utilize _direitos de gestão_ de recursos para a escala mais eficiente. Com os direitos _de escuta,_ o escalonamento não é tão preciso porque o comprimento da fila não pode ser usado para informar decisões de escala. Para saber mais sobre a definição de direitos nas políticas de acesso a autocarros de serviço, consulte [a Política de Autorização de Acesso Partilhado.](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies) Para os gatilhos do Event Hub, consulte a [orientação de escala](functions-bindings-event-hubs-trigger.md#scaling) no artigo de referência. 

## <a name="limit-scale-out"></a>Limite escala para fora

Pode pretender restringir o número máximo de casos que uma aplicação usada para escalar.  Isto é mais comum em casos em que um componente a jusante como uma base de dados tem uma produção limitada.  Por padrão, as funções do plano de consumo escalam até 200 instâncias, e as funções do plano Premium irão aumentar para até 100 instâncias.  Pode especificar um máximo inferior para uma aplicação específica modificando o `functionAppScaleLimit` valor.  O `functionAppScaleLimit` pode ser definido para ou para sem `0` `null` restrições, ou um valor válido entre `1` e o máximo da aplicação.

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <RESOURCE_GROUP> -n <FUNCTION_APP-NAME>/config/web --set properties.functionAppScaleLimit=<SCALE_LIMIT>
```

## <a name="best-practices-and-patterns-for-scalable-apps"></a>Melhores práticas e padrões para apps escaláveis

Existem muitos aspetos de uma aplicação de função que impacta a forma como escala, incluindo a configuração do anfitrião, a pegada de tempo de execução e a eficiência do recurso.  Para mais informações, consulte a [secção de escalabilidade do artigo de considerações de desempenho](functions-best-practices.md#scalability-best-practices). Também deve estar ciente de como as ligações se comportam à medida que as escalas de aplicações da sua função. Para obter mais informações, consulte [Como gerir as ligações em Funções Azure](manage-connections.md).

Para obter mais informações sobre a escala em Python e Node.js, consulte [o guia de desenvolvimento python do Azure Functions Python - Escala e concurrency](functions-reference-python.md#scaling-and-performance) e [Azure Functions Node.js developer guide - Escala e conúnência](functions-reference-node.md#scaling-and-concurrency).

## <a name="billing-model"></a>Modelo de faturação

A faturação dos diferentes planos é descrita em detalhe na página de preços do [Azure Functions](https://azure.microsoft.com/pricing/details/functions/). A utilização é agregada ao nível da aplicação de função e conta apenas o tempo em que o código de função é executado. Seguem-se as unidades de faturação:

* **Consumo de recursos em gigabytes-segundos (GB-s)**. Computado como uma combinação de tamanho de memória e tempo de execução para todas as funções dentro de uma aplicação de função. 
* **Execuções.** Contado cada vez que uma função é executada em resposta a um gatilho do evento.

Consultas úteis e informações sobre como entender a sua conta de consumo podem ser encontradas [na faturação faQ.](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="next-steps"></a>Passos seguintes

+ [Opções de hospedagem de funções Azure Functions](functions-scale.md)

