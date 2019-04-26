---
title: Como integrar a gestão de API do Azure com o Azure Application Insights | Documentos da Microsoft
description: Saiba como iniciar sessão e ver eventos da gestão de API do Azure no Azure Application Insights.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 69f36773b702d9f0059e0cd27dbb864ccd7f7b2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60527624"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Como integrar a gestão de API do Azure com o Azure Application Insights

Gestão de API do Azure permite a integração fácil com o Azure Application Insights - um serviço extensível para programadores web criar e gerir aplicações em várias plataformas. Este guia explica cada etapa dessa integração e descreve estratégias para reduzir o impacto de desempenho na sua instância do serviço de gestão de API.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia, tem de ter uma instância de gestão de API do Azure. Se não tiver uma, execute o [tutorial](get-started-create-service-instance.md) primeiro.

## <a name="create-an-azure-application-insights-instance"></a>Criar uma instância do Azure Application Insights

Antes de poder utilizar o Azure Application Insights, tem primeiro de criar uma instância do serviço.

1. Abra o **portal do Azure** e navegue até à **Application Insights**.  
    ![Criar o App Insights](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Clique em **+ Adicionar**.  
    ![Criar o App Insights](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Preencha o formulário. Selecione **gerais** como o **tipo de aplicação**.
4. Clique em **Criar**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Criar uma ligação entre o Azure Application Insights e instância de serviço de gestão de API do Azure

1. Navegue até à sua **instância de serviço de gestão de API do Azure** no **portal do Azure**.
2. Selecione **Application Insights** no menu à esquerda.
3. Clique em **+ Adicionar**.  
    ![O agente de log do App Insights](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Selecione criado anteriormente **Application Insights** de instância e fornecer uma breve descrição.
5. Clique em **Criar**.
6. Acabou de criar um agente de log do Application Insights do Azure com uma chave de instrumentação. Deverá agora aparecer na lista.  
    ![O agente de log do App Insights](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> Nos bastidores, uma [Logger](https://docs.microsoft.com/rest/api/apimanagement/logger/createorupdate) entidade é criada na sua instância de gestão de API, que contém a chave de instrumentação da instância do Application Insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Ativar o registo do Application Insights para a sua API

1. Navegue até à sua **instância de serviço de gestão de API do Azure** no **portal do Azure**.
2. Selecione **APIs** no menu à esquerda.
3. Clique na sua API, neste caso **API da conferência de demonstração**.
4. Vá para o **definições** separador de barra superior.
5. Desloque para baixo para o **registos de diagnóstico** secção.  
    ![O agente de log do App Insights](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Verifique os **ativar** caixa.
7. Selecione o seu agente de log anexado no **destino** lista pendente.
8. Entrada **100** como **amostragem (%)** e assinale o **sempre registar erros** caixa de verificação.
9. Clique em **Guardar**.

> [!WARNING]
> Substituindo o valor predefinido **0** no **primeiros bytes do corpo** campo pode diminuir significativamente o desempenho das suas APIs.

> [!NOTE]
> Nos bastidores, uma [diagnóstico](https://docs.microsoft.com/rest/api/apimanagement/diagnostic/createorupdate) "Application insights" com o nome de entidade é criada no nível da API.

| Nome da definição                        | Tipo de valor                        | Descrição                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ativar                              | boolean                           | Especifica se o registo desta API está ativado.                                                                                                                                                                                                                                                                                                |
| Destino                         | Agente de log Application Insights do Azure | Especifica o agente de log do Application Insights do Azure a ser utilizado                                                                                                                                                                                                                                                                                           |
| Amostragem (%)                        | decimal                           | Valores de 0 a 100 (percentagem). <br/> Especifica que percentagem de pedidos será registada para o Azure Application Insights. amostragem de 0% significa zero pedidos com sessão iniciados, enquanto a amostragem de 100% significa que todos os pedidos com sessão iniciados. <br/> Esta definição é utilizada para reduzir as implicações de desempenho de pedidos de registo para o Azure Application Insights (consulte a secção abaixo). |
| Erros de registo de sempre                   | boolean                           | Se esta definição está selecionada, todas as falhas serão registadas para o Azure Application Insights, independentemente do **amostragem** definição.                                                                                                                                                                                                                  |
| Opções básicas: Cabeçalhos              | list                              | Especifica os cabeçalhos que vão ser registados para o Azure Application Insights para pedidos e respostas.  Predefinição: não existem cabeçalhos são registados.                                                                                                                                                                                                             |
| Opções básicas: Primeiros bytes do corpo  | inteiro                           | Especifica quantos primeiros bytes do corpo são registados para o Azure Application Insights para solicitações e respostas.  Predefinição: corpo não é registado.                                                                                                                                                                                              |
| Opções avançadas: Pedido de front-end  |                                   | Especifica se e como *pedidos de front-end* serão registados para o Azure Application Insights. *Pedido de front-end* é um pedido de entrada para o serviço de gestão de API do Azure.                                                                                                                                                                        |
| Opções avançadas: Resposta de front-end |                                   | Especifica se e como *respostas de front-end* serão registados para o Azure Application Insights. *Resposta de front-end* é uma resposta de saída do serviço de gestão de API do Azure.                                                                                                                                                                   |
| Opções avançadas: Pedido de back-end   |                                   | Especifica se e como *pedidos de back-end* serão registados para o Azure Application Insights. *Pedido de back-end* é um pedido de saída do serviço de gestão de API do Azure.                                                                                                                                                                        |
| Opções avançadas: Resposta de back-end  |                                   | Especifica se e como *respostas de back-end* serão registados para o Azure Application Insights. *Resposta de back-end* é uma resposta de entrada para o serviço de gestão de API do Azure.                                                                                                                                                                       |

> [!NOTE]
> Pode especificar loggers nos níveis - agente de log de API única ou um agente de log para todas as APIs.
>  
> Se especificar ambos:
> + Se forem diferentes loggers, ambos serão utilizado (multiplexação de registos),
> + Se forem os mesmo loggers, mas têm definições diferentes, em seguida, uma única API (nível mais granular) irá substituir a um para todas as APIs.

## <a name="what-data-is-added-to-azure-application-insights"></a>Os dados que são adicionados para o Azure Application Insights

Recebe o Azure Application Insights:

+ *Pedido* item de telemetria, para cada solicitação de entrada (*pedido de front-end*, *front-end resposta*),
+ *Dependência* item de telemetria, para cada solicitação reencaminhada para um serviço de back-end (*pedido de back-end*, *resposta de back-end*),
+ *Exceção* item de telemetria, para cada solicitação com falha.

Uma solicitação com falha é um pedido, que:

+ falhou devido a uma ligação de cliente fechados, ou
+ acionada por um *no erro* secção das políticas de API, ou
+ tem um código de resposta HTTP estado correspondente 4xx ou 5xx.

## <a name="performance-implications-and-log-sampling"></a>Implicações de desempenho e amostragem de registo

> [!WARNING]
> Todos os eventos de registo pode ter implicações de graves de desempenho, dependendo da taxa de pedidos de entrada.

Com base nos testes de carga interno, a ativação desta funcionalidade por uma redução de 40% a 50% na taxa de transferência quando a taxa de pedidos excedeu a 1000 pedidos por segundo. O Azure Application Insights foi concebido para utilizar a análise estatística para avaliar os desempenhos do aplicativo. Ele não se destina a ser um sistema de auditoria e não é adequado para cada pedido individual para APIs de grande volume de registo.

Pode manipular o número de pedidos que está a ser conectado ao ajustar a **amostragem** definição (consulte os passos acima). Valor de meios de 100%, todas as solicitações são registadas, enquanto 0% não reflete em todos os nenhum registo. **Amostragem** ajuda a reduzir o volume de telemetria, com eficiência a impedir que degradação significativa do desempenho, ao mesmo tempo ainda com os benefícios do Registro em log.

A ignorar o registo de cabeçalhos e o corpo de solicitações e respostas também terão um impacto positivo resolvendo problemas de problemas de desempenho.

## <a name="video"></a>Vídeo

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Passos Seguintes

+ Saiba mais sobre [do Azure Application Insights](https://docs.microsoft.com/azure/application-insights/).
+ Considere [iniciar sessão com o Event Hubs do Azure](api-management-howto-log-event-hubs.md).
