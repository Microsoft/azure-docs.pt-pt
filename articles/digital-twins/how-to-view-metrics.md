---
title: Ver métricas com monitor Azure
titleSuffix: Azure Digital Twins
description: Veja como ver as métricas do Azure Digital Twins no Azure Monitor.
author: baanders
ms.author: baanders
ms.date: 7/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c6db571d64b0fd276519f15a3984848e80c4e18a
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387681"
---
# <a name="view-and-understand-azure-digital-twins-metrics"></a>Ver e compreender as métricas da Azure Digital Twins

Estas métricas dão-lhe informações sobre o estado dos recursos da Azure Digital Twins na sua subscrição Azure. As métricas Azure Digital Twins ajudam-no a avaliar a saúde geral do serviço Azure Digital Twins e os recursos que lhe estão ligados. Estas estatísticas viradas para o utilizador ajudam-no a ver o que se passa com as suas Gémeas Digitais Azure e ajudam a realizar análises de causa-raiz em problemas sem necessidade de contactar o suporte do Azure.

As métricas são ativadas por padrão. Pode ver as métricas do Azure Digital Twins a partir do [portal Azure](https://portal.azure.com).

## <a name="how-to-view-azure-digital-twins-metrics"></a>Como ver as métricas da Azure Digital Twins

1. Crie uma instância Azure Digital Twins. Pode encontrar instruções sobre como configurar uma instância Azure Digital Twins em [*Como-a- Configurar um caso e autenticação*](how-to-set-up-instance-scripted.md).

2. Encontre a sua instância Azure Digital Twins no [portal Azure](https:/portal.azure.com) (pode abrir a página para ela digitando o seu nome na barra de pesquisa do portal). 

    A partir do menu do caso, selecione **Metrics**.
   
    :::image type="content" source="media/how-to-view-metrics/azure-digital-twins-metrics.png" alt-text="Screenshot mostrando a página métrica para Azure Digital Twins":::

    Esta página apresenta as métricas para a sua instância Azure Digital Twins. Também pode criar vistas personalizadas das suas métricas selecionando as que deseja ver na lista.
    
3. Pode optar por enviar os seus dados de métricas para um ponto final do Event Hubs ou uma conta de Armazenamento Azure clicando **nas definições** de Diagnóstico do menu e, em seguida, **Adicione a definição**de diagnóstico .

    :::image type="content" source="media/how-to-view-metrics/diagnostic-settings.png" alt-text="Screenshot mostrando a página e botão de definições de diagnóstico para adicionar":::

    Para obter mais informações sobre este processo, consulte [*Resolução de Problemas: Configurar diagnósticos*](troubleshoot-diagnostics.md).

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Métricas Azure Digital Twins e como usá-las

A Azure Digital Twins fornece várias métricas para lhe dar uma visão geral da saúde do seu caso e dos seus recursos associados. Você também pode combinar informações de várias métricas para pintar uma imagem maior do estado do seu caso. 

As tabelas a seguir descrevem as métricas rastreadas por cada instância Azure Digital Twins, e como cada métrica se relaciona com o estado geral do seu caso.

#### <a name="api-request-metrics"></a>Métricas de pedido da API

Métricas que têm a ver com pedidos de API:

| Métrica | Nome de exibição métrica | Unidade | Tipo de agregação| Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | Pedidos de API (Pré-visualização) | de palavras | Total | O número de Pedidos de API feitos para Gémeos Digitais lêem, escrevem, apagam e consultam as operações. |  Autenticação </br>Operação </br>Protocolo </br>Código de Estado </br>Classe código de estado </br>Texto de estado |
| ApiRequestsLatency | Pedidos de Latência da API (Pré-visualização) | Milissegundos | Média | O tempo de resposta para pedidos de API. Isto refere-se ao momento em que o pedido é recebido pela Azure Digital Twins até que o serviço envie um resultado de sucesso/falha para as operações de leitura, escrita, exclusão e consulta das Gémeas Digitais. | Autenticação </br>Operação </br>Protocolo |
| ApiRequestsFailureRate | Taxa de falha de pedidos de API (pré-visualização) | Percentagem | Média | A percentagem de pedidos da API que o serviço recebe, por exemplo, que dão um código de resposta de erro interno (500) para as Gémeas Digitais ler, escrever, excluir e consultar operações. | Autenticação </br>Operação </br>Protocolo </br>Código de Estado </br>Classe código de estado </br>Texto de estado

#### <a name="routing-metrics"></a>Métricas de encaminhamento

Métricas que têm a ver com o encaminhamento:

| Métrica | Nome de exibição métrica | Unidade | Tipo de agregação| Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| Encaminhamento | Encaminhamento (Pré-visualização) | de palavras | Total | O número de mensagens encaminhada para um serviço Azure de ponto final, como Event Hub, Service Bus ou Event Grid. | Operação </br>Resultado |
| Encaminhamento | Latência de encaminhamento (Pré-visualização) | Milissegundos | Média | O tempo decorreu entre um evento que foi encaminhado de Azure Digital Twins para quando é colocado no serviço endpoint Azure, como Event Hub, Service Bus ou Event Grid. | Operação </br>Resultado |
| EncaminhamentoFailureRate | Taxa de falha de encaminhamento (pré-visualização) | Percentagem | Média | A percentagem de eventos que resultam num erro, uma vez que são encaminhados da Azure Digital Twins para um serviço Azure de ponta, como o Event Hub, Service Bus ou Event Grid. | Operação </br>Resultado |

#### <a name="billing-metrics"></a>Métricas de faturação

Métricas que têm a ver com faturação:

>[!NOTE]
> Durante **a pré-visualização, a faturação é um custo zero**. Embora estas métricas ainda apareçam na lista selecionável, não se aplicam durante a pré-visualização e permanecerão em zero até que o serviço se mova para além da pré-visualização.

| Métrica | Nome de exibição métrica | Unidade | Tipo de agregação| Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperações | Operações de faturação da API (Pré-visualização) | de palavras | Total | Métrica de faturação para a contagem de todos os pedidos da API feitos contra o serviço Azure Digital Twins. | `MeterId` |
| BillingQueryUnits | Unidades de consulta de faturação (pré-visualização) | de palavras | Total | O número de Unidades de Consulta, uma medida internamente calculada de utilização de recursos de serviço, consumido para executar consultas. Há também uma API auxiliar disponível para medir Unidades de Consulta: [Classe QueryChargeHelper](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.querychargehelper?view=azure-dotnet-preview) | `MeterId` |
| BillingMessagesProcessado | Mensagens de faturação processadas (pré-visualização) | de palavras | Total | Métrica de faturação para o número de mensagens enviadas da Azure Digital Twins para pontos finais externos. | `MeterId` |

## <a name="dimensions"></a>Dimensões

As dimensões ajudam a identificar mais detalhes sobre as métricas. Algumas das métricas de encaminhamento fornecem informações por ponto final. A tabela abaixo lista valores possíveis para estas dimensões.

| Dimensão | Valores |
| --- | --- |
| Autenticação | Oauth |
| Operação (para pedidos de API) | Microsoft.DigitalTwins/digitaltwins/delete</br>Microsoft.DigitalTwins/digitaltwins/write</br>Microsoft.DigitalTwins/digitaltwins/read </br>Microsoft.DigitalTwins/eventroutes/read </br>Microsoft.DigitalTwins/eventroutes/write </br>Microsoft.DigitalTwins/eventroutes/delete </br>Microsoft.DigitalTwins/modelos/ler </br>Microsoft.DigitalTwins/models/write </br>Microsoft.DigitalTwins/models/delete </br>Microsoft.DigitalTwins/consulta/ação |
Operação (para encaminhamento) | Event Grid </br>Hub de Eventos </br>Service Bus |
| Protocolo | HTTPS |
| Resultado | Success </br>Falha |
| Código de Estado | 200, 404, 500, e assim por diante. |
| Classe código de estado | 2xx, 4xx, 5xx, e assim por diante. |
| Texto de estado | Erro interno do servidor, não encontrado, e assim por diante. |

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre a gestão das métricas gravadas para a Azure Digital Twins, consulte [*Troubleshooting: Configurar diagnósticos*](troubleshoot-diagnostics.md).

Ou, agora que viu uma visão geral das métricas da Azure Digital Twins, siga estas ligações para saber mais sobre a gestão das Gémeas Digitais Azure:

* [*Como fazer: Use as APIs e SDKs de gémeos digitais Azure*](how-to-use-apis-sdks.md)
* [*Como fazer: Gerir modelos personalizados*](how-to-manage-model.md)
* [*Como fazer: Gerir gémeos digitais*](how-to-manage-twin.md)
* [*Como fazer: Gerir o gráfico gémeo com relacionamentos*](how-to-manage-graph.md)
* [*Como fazer: Gerir pontos finais e rotas*](how-to-manage-routes.md)