---
title: Ver métricas com monitor Azure
titleSuffix: Azure Digital Twins
description: Veja como ver as métricas do Azure Digital Twins no Azure Monitor.
author: baanders
ms.author: baanders
ms.date: 8/4/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 63817c8a65678579ce535a3c2e667e4eb0971a63
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434071"
---
# <a name="troubleshooting-azure-digital-twins-metrics"></a>Problemas na resolução de gémeos digitais Azure: Métricas

As métricas descritas neste artigo dão-lhe informações sobre o estado dos recursos da Azure Digital Twins na sua subscrição Azure. As métricas Azure Digital Twins ajudam-no a avaliar a saúde geral do serviço Azure Digital Twins e os recursos que lhe estão ligados. Estas estatísticas viradas para o utilizador ajudam-no a ver o que se passa com as suas Gémeas Digitais Azure e ajudam a realizar análises de causa-raiz em problemas sem necessidade de contactar o suporte do Azure.

As métricas são ativadas por padrão. Pode ver as métricas do Azure Digital Twins a partir do [portal Azure](https://portal.azure.com).

## <a name="how-to-view-azure-digital-twins-metrics"></a>Como ver as métricas da Azure Digital Twins

1. Crie uma instância Azure Digital Twins. Pode encontrar instruções sobre como configurar uma instância Azure Digital Twins em [*Como-a- Configurar um caso e autenticação*](how-to-set-up-instance-portal.md).

2. Encontre a sua instância Azure Digital Twins no [portal Azure](https://portal.azure.com) (pode abrir a página para ela digitando o seu nome na barra de pesquisa do portal). 

    A partir do menu do caso, selecione **Metrics**.
   
    :::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Screenshot mostrando a página métrica para Azure Digital Twins":::

    Esta página apresenta as métricas para a sua instância Azure Digital Twins. Também pode criar vistas personalizadas das suas métricas selecionando as que deseja ver na lista.
    
3. Pode optar por enviar os seus dados de métricas para um ponto final do Event Hubs ou uma conta de Armazenamento Azure selecionando **as definições** de Diagnóstico do menu e, em seguida, adicione a **definição** de diagnóstico .

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Screenshot mostrando a página e botão de definições de diagnóstico para adicionar":::

    Para obter mais informações sobre este processo, consulte [*Resolução de Problemas: Configurar diagnósticos*](troubleshoot-diagnostics.md).

4. Pode optar por configurar alertas para os dados das suas métricas selecionando **Alertas** do menu e, em seguida, **+ Nova regra de alerta**.
    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="Screenshot mostrando a página e botão alertas para adicionar":::

    Para obter mais informações sobre este processo, consulte [*Resolução de Problemas: Configurar alertas*](troubleshoot-alerts.md).

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Métricas Azure Digital Twins e como usá-las

A Azure Digital Twins fornece várias métricas para lhe dar uma visão geral da saúde do seu caso e dos seus recursos associados. Você também pode combinar informações de várias métricas para pintar uma imagem maior do estado do seu caso. 

As tabelas a seguir descrevem as métricas rastreadas por cada instância Azure Digital Twins, e como cada métrica se relaciona com o estado geral do seu caso.

#### <a name="metrics-for-tracking-service-limits"></a>Métricas para rastreio de limites de serviço

Pode configurar estas métricas para acompanhar quando se aproxima de um [limite de serviço publicado](reference-service-limits.md#functional-limits) para algum aspeto da sua solução. 

Para configurar isto, utilize a função [de alerta](troubleshoot-alerts.md) no Azure Monitor. Pode definir limiares para estas métricas para que receba um alerta quando uma métrica atinge uma determinada percentagem do seu limite publicado.

| Metric | Nome de exibição métrica | Unidade | Tipo de agregação| Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| TwinCount | Contagem de Gémeos (Pré-visualização) | de palavras | Total | Número total de gémeos no caso Azure Digital Twins. Utilize esta métrica para determinar se está a aproximar-se do limite de [serviço](reference-service-limits.md#functional-limits) para o número máximo de gémeos permitido por exemplo. |  Nenhum |
| ModelCount | Contagem de modelos (Pré-visualização) | de palavras | Total | Número total de modelos na instância Azure Digital Twins. Utilize esta métrica para determinar se está a aproximar-se do limite de [serviço](reference-service-limits.md#functional-limits) para o número máximo de modelos permitidos por exemplo. | Nenhum |

#### <a name="api-request-metrics"></a>Métricas de pedido da API

Métricas que têm a ver com pedidos de API:

| Metric | Nome de exibição métrica | Unidade | Tipo de agregação| Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | Pedidos da API | de palavras | Total | O número de Pedidos de API feitos para Gémeos Digitais lêem, escrevem, apagam e consultam as operações. |  Autenticação, <br>Operação, <br>Protocolo, <br>Código de Estado, <br>Classe código de estado, <br>Texto de estado |
| ApiRequestsFailureRate | Taxa de falha de pedidos da API | Percentagem | Média | A percentagem de pedidos da API que o serviço recebe, por exemplo, que dão um código de resposta de erro interno (500) para as Gémeas Digitais ler, escrever, excluir e consultar operações. | Autenticação, <br>Operação, <br>Protocolo, <br>Código de Estado, <br>Classe código de estado, <br>Texto de estado
| ApiRequestsLatency | API solicita latência | Milissegundos | Média | O tempo de resposta para pedidos de API. Isto refere-se ao momento em que o pedido é recebido pela Azure Digital Twins até que o serviço envie um resultado de sucesso/falha para as operações de leitura, escrita, exclusão e consulta das Gémeas Digitais. | Autenticação, <br>Operação, <br>Protocolo |

#### <a name="billing-metrics"></a>Métricas de faturação

Métricas que têm a ver com faturação:

| Metric | Nome de exibição métrica | Unidade | Tipo de agregação| Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperações | Operações de Faturação da API | de palavras | Total | Métrica de faturação para a contagem de todos os pedidos da API feitos contra o serviço Azure Digital Twins. | ID do Medidor |
| BillingMessagesProcessado | Mensagens de faturação processadas | de palavras | Total | Métrica de faturação para o número de mensagens enviadas da Azure Digital Twins para pontos finais externos.<br><br>Para ser considerada uma única mensagem para efeitos de faturação, uma carga útil não deve ser superior a 1 KB. As cargas superiores a esta serão contadas como mensagens adicionais em incrementos de 1 KB (assim, uma mensagem entre 1 e 2 KB será contada como 2 mensagens, entre 2 e 3 KB serão 3 mensagens, e assim por diante).<br>Esta restrição também se aplica às respostas, pelo que uma chamada que devolve 1.5KB no organismo de resposta, por exemplo, será faturada como 2 operações. | ID do Medidor |
| BillingQueryUnits | Unidades de consulta de faturação | de palavras | Total | O número de Unidades de Consulta, uma medida internamente calculada de utilização de recursos de serviço, consumido para executar consultas. Há também uma API auxiliar disponível para medir Unidades de Consulta: [Classe QueryChargeHelper](/dotnet/api/azure.digitaltwins.core.querychargehelper) | ID do Medidor |

Para mais detalhes sobre a forma como a Azure Digital Twins é cobrada, consulte [*os preços da Azure Digital Twins.*](https://azure.microsoft.com/pricing/details/digital-twins/)

#### <a name="ingress-metrics"></a>Métricas de entrada

Métricas que têm a ver com a entrada de dados:

| Metric | Nome de exibição métrica | Unidade | Tipo de agregação| Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| IngresssEvents | Eventos ingressos | de palavras | Total | O número de eventos de telemetria a entrar em Azure Digital Twins. | Resultado |
| IngressEventsFailureRate | Taxa de falha de eventos ingress | Percentagem | Média | A percentagem de eventos de telemetria de entrada para os quais o serviço devolve um código de resposta de erro interno (500). | Resultado |
| IngresssEventsLatency | Ingresss Eventos Latência | Milissegundos | Média | O momento em que um evento chega até quando está pronto para ser evacuado pela Azure Digital Twins, altura em que o serviço envia um resultado de sucesso/falha. | Resultado |

#### <a name="routing-metrics"></a>Métricas de encaminhamento

Métricas que têm a ver com o encaminhamento:

| Metric | Nome de exibição métrica | Unidade | Tipo de agregação| Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| Mensagens Remadas | Mensagens encaminhada | de palavras | Total | O número de mensagens encaminhada para um serviço Azure de ponto final, como Event Hub, Service Bus ou Event Grid. | Tipo ponto final, <br>Resultado |
| EncaminhamentoFailureRate | Taxa de falha de encaminhamento | Percentagem | Média | A percentagem de eventos que resultam num erro, uma vez que são encaminhados da Azure Digital Twins para um serviço Azure de ponta, como o Event Hub, Service Bus ou Event Grid. | Tipo ponto final, <br>Resultado |
| Encaminhamento | Latência de encaminhamento | Milissegundos | Média | O tempo decorreu entre um evento que foi encaminhado de Azure Digital Twins para quando é colocado no serviço endpoint Azure, como Event Hub, Service Bus ou Event Grid. | Tipo ponto final, <br>Resultado |

## <a name="dimensions"></a>Dimensões

As dimensões ajudam a identificar mais detalhes sobre as métricas. Algumas das métricas de encaminhamento fornecem informações por ponto final. A tabela abaixo lista valores possíveis para estas dimensões.

| Dimensão | Valores |
| --- | --- |
| Autenticação | Oauth |
| Operação (para pedidos de API) | Microsoft.DigitalTwins/digitaltwins/delete, <br>Microsoft.DigitalTwins/digitaltwins/write, <br>Microsoft.DigitalTwins/digitaltwins/read, <br>Microsoft.DigitalTwins/eventroutes/read, <br>Microsoft.DigitalTwins/eventroutes/write, <br>Microsoft.DigitalTwins/eventroutes/delete, <br>Microsoft.DigitalTwins/modelos/read, <br>Microsoft.DigitalTwins/models/write, <br>Microsoft.DigitalTwins/models/delete, <br>Microsoft.DigitalTwins/consulta/ação |
| Tipo de ponto final | Grelha de Eventos, <br>Centro de Eventos, <br>Service Bus |
| Protocolo | HTTPS |
| Resultado | Sucesso, <br>Falha |
| Código de Estado | 200, 404, 500, e assim por diante. |
| Classe código de estado | 2xx, 4xx, 5xx, e assim por diante. |
| Texto de estado | Erro interno do servidor, não encontrado, e assim por diante. |

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a gestão das métricas gravadas para a Azure Digital Twins, consulte [*Troubleshooting: Configurar diagnósticos*](troubleshoot-diagnostics.md).
