---
title: Integrar o gerenciamento de API do Azure com o Aplicativo Azure insights
titleSuffix: Azure API Management
description: Saiba como registrar e exibir eventos do gerenciamento de API do Azure no Aplicativo Azure insights.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 12aeea8393a00d7d2662c826f847265bdbdc0119
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442719"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Como integrar o gerenciamento de API do Azure com informações do Aplicativo Azure

O gerenciamento de API do Azure permite uma fácil integração com o Aplicativo Azure insights – um serviço extensível para desenvolvedores da Web que criam e gerenciam aplicativos em várias plataformas. Este guia percorre cada etapa dessa integração e descreve estratégias para reduzir o impacto no desempenho em sua instância do serviço de gerenciamento de API.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia, você precisa ter uma instância de gerenciamento de API do Azure. Se você não tiver um, conclua o [tutorial](get-started-create-service-instance.md) primeiro.

## <a name="create-an-azure-application-insights-instance"></a>Criar uma instância do Revisions do Aplicativo Azure

Antes de poder usar o Aplicativo Azure insights, primeiro você precisa criar uma instância do serviço.

1. Abra o **portal do Azure** e navegue até **Application insights**.  
    ![o app insights criar](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Clique em **+ Adicionar**.  
    ![o app insights criar](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Preencha o formulário. Selecione **geral** como o **tipo de aplicativo**.
4. Clique em **Criar**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Criar uma conexão entre o Aplicativo Azure insights e a instância do serviço de gerenciamento de API do Azure

1. Navegue até a **instância do serviço de gerenciamento de API do Azure** no **portal do Azure**.
2. Selecione **Application insights** no menu à esquerda.
3. Clique em **+ Adicionar**.  
    ![o agente de informações do aplicativo](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Selecione a instância de **Application insights** criada anteriormente e forneça uma breve descrição.
5. Clique em **Criar**.
6. Você acabou de criar um Aplicativo Azure o agente de informações com uma chave de instrumentação. Agora ele deve aparecer na lista.  
    ![Agente de informações do aplicativo](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> Por trás da cena, uma entidade de [agente](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger/createorupdate) é criada na sua instância de gerenciamento de API, que contém a chave de instrumentação da instância de Application insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Habilitar o log de Application Insights para sua API

1. Navegue até a **instância do serviço de gerenciamento de API do Azure** no **portal do Azure**.
2. Selecione **APIs** no menu à esquerda.
3. Clique em sua API, neste caso **demonstração de API de conferência**.
4. Vá para a guia **configurações** na barra superior.
5. Role para baixo até a seção **logs de diagnóstico** .  
    ![o agente de informações do aplicativo](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Marque a caixa **habilitar** .
7. Selecione o agente anexado na lista suspensa **destino** .
8. Insira **100** como **amostragem (%)** e marque a caixa de seleção **erros sempre registrar em log** .
9. Clique em **Guardar**.

> [!WARNING]
> Substituir o valor padrão **0** nos **primeiros bytes do campo corpo** pode diminuir significativamente o desempenho de suas APIs.

> [!NOTE]
> Por trás da cena, uma entidade de [diagnóstico](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/diagnostic/createorupdate) chamada ' applicationinsights ' é criada no nível da API.

| Nome da definição                        | Tipo de valor                        | Descrição                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ativar                              | boolean                           | Especifica se o log dessa API está habilitado.                                                                                                                                                                                                                                                                                                |
| Destino                         | Agente de informações do Aplicativo Azure | Especifica Aplicativo Azure agente de log de informações a ser usado                                                                                                                                                                                                                                                                                           |
| Amostragem (%)                        | decimal                           | Valores de 0 a 100 (porcentagem). <br/> Especifica a porcentagem de solicitações que serão registradas Aplicativo Azure insights. a amostragem de 0% significa zero solicitações registradas, enquanto a amostragem de 100% significa todas as solicitações registradas. <br/> Essa configuração é usada para reduzir as implicações de desempenho de solicitações de log para Aplicativo Azure insights (consulte a seção abaixo). |
| Sempre registrar erros                   | boolean                           | Se essa configuração for selecionada, todas as falhas serão registradas no Aplicativo Azure insights, independentemente da configuração de **amostragem** .                                                                                                                                                                                                                  |
| Opções básicas: cabeçalhos              | list                              | Especifica os cabeçalhos que serão registrados no Aplicativo Azure insights para solicitações e respostas.  Padrão: nenhum cabeçalho é registrado.                                                                                                                                                                                                             |
| Opções básicas: primeiros bytes de corpo  | número inteiro                           | Especifica quantos primeiros bytes do corpo são registrados em Aplicativo Azure informações para solicitações e respostas.  Padrão: o corpo não está registrado.                                                                                                                                                                                                    |
| Opções avançadas: detalhes         |                                   | Especifica o nível de verbosidade. Somente rastreamentos personalizados com nível de severidade mais alto serão registrados. Padrão: informações.                                                                                                                                                                                                                               |
| Opções avançadas: solicitação de front-end  |                                   | Especifica se e como *as solicitações de front-end* serão registradas no aplicativo Azure insights. A *solicitação de front-end* é uma solicitação de entrada para o serviço de gerenciamento de API do Azure.                                                                                                                                                                        |
| Opções avançadas: resposta de front-end |                                   | Especifica se e como as *respostas de front-end* serão registradas no aplicativo Azure insights. A *resposta de front-end* é uma resposta de saída do serviço de gerenciamento de API do Azure.                                                                                                                                                                   |
| Opções avançadas: solicitação de back-end   |                                   | Especifica se e como *as solicitações de back-end* serão registradas no aplicativo Azure insights. A *solicitação de back-end* é uma solicitação de saída do serviço de gerenciamento de API do Azure.                                                                                                                                                                        |
| Opções avançadas: resposta de back-end  |                                   | Especifica se e como as *respostas de back-end* serão registradas aplicativo Azure informações. A *resposta de back-end* é uma resposta de entrada para o serviço de gerenciamento de API do Azure.                                                                                                                                                                       |

> [!NOTE]
> Você pode especificar agentes em diferentes níveis-agente de log de API único ou um agente para todas as APIs.
>  
> Se você especificar ambos:
> + Se eles forem agentes diferentes, ambos serão usados (multiplexando logs),
> + Se eles forem os mesmos agentes, mas tiverem configurações diferentes, o único para a API única (nível mais granular) substituirá aquele para todas as APIs.

## <a name="what-data-is-added-to-azure-application-insights"></a>Quais dados são adicionados ao Aplicativo Azure insights

O Aplicativo Azure insights recebe:

+ Item de telemetria de *solicitação* , para cada solicitação de entrada (*solicitação de front-end*, resposta de front- *end*),
+ Item de telemetria de *dependência* , para cada solicitação encaminhada para um serviço de back-end (*solicitação de back-end*, *resposta de back-end*)
+ Item de telemetria de *exceção* , para cada solicitação com falha.

Uma solicitação com falha é uma solicitação, que:

+ falha devido a uma conexão de cliente fechada ou
+ disparou uma seção *On-Error* das políticas de API ou
+ tem um código de status HTTP de resposta correspondente a 4xx ou 5xx.

## <a name="performance-implications-and-log-sampling"></a>Implicações de desempenho e amostragem de log

> [!WARNING]
> O registro em log de todos os eventos pode ter implicações graves de desempenho, dependendo da taxa de solicitações de entrada.

Com base nos testes de carga internos, a habilitação desse recurso causou uma redução de 40% a 50% na taxa de transferência quando a taxa de solicitação excedeu 1.000 solicitações por segundo. O Aplicativo Azure insights foi projetado para usar a análise estatística para avaliar o desempenho do aplicativo. Ele não se destina a ser um sistema de auditoria e não é adequado para registrar em log cada solicitação individual para APIs de alto volume.

Você pode manipular o número de solicitações que estão sendo registradas, ajustando a configuração de **amostragem** (consulte as etapas acima). O valor 100% significa que todas as solicitações são registradas, enquanto 0% não reflete nenhum registro em log. A **amostragem** ajuda a reduzir o volume de telemetria, evitando efetivamente a degradação significativa do desempenho, ao mesmo tempo em que ainda tem os benefícios do registro em log.

Ignorar o registro em log de cabeçalhos e o corpo de solicitações e respostas também terá um impacto positivo na atenuação de problemas de desempenho.

## <a name="video"></a>Vídeo

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Passos seguintes

+ Saiba mais sobre o [aplicativo Azure insights](https://docs.microsoft.com/azure/application-insights/).
+ Considere o [registro em log com os hubs de eventos do Azure](api-management-howto-log-event-hubs.md).
