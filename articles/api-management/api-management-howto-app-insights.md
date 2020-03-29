---
title: Integrar a Gestão da API Azure com insights de aplicação azure
titleSuffix: Azure API Management
description: Saiba como registar e ver eventos da Azure API Management em Insights de Aplicação Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442719"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Como integrar a Gestão de API do Azure com o Azure Application Insights

A Azure API Management permite uma fácil integração com o Azure Application Insights - um serviço extensível para a construção e gestão de aplicações em várias plataformas. Este guia percorre cada passo dessa integração e descreve estratégias para reduzir o impacto de desempenho na sua instância de serviço de Gestão API.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia, é necessário ter uma instância de Gestão API Azure. Se não tiver um, complete o [tutorial](get-started-create-service-instance.md) primeiro.

## <a name="create-an-azure-application-insights-instance"></a>Criar uma instância de Insights de Aplicação Azure

Antes de poder utilizar o Azure Application Insights, primeiro precisa de criar uma instância do serviço.

1. Abra o **portal Azure** e navegue para **Application Insights**.  
    ![App Insights criar](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Clique em **+ Adicionar**.  
    ![App Insights criar](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Preencha o formulário. Selecione **General** como o Tipo de **Aplicação**.
4. Clique em **Criar**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Criar uma ligação entre a Azure Application Insights e a instância de serviço de Gestão API Azure

1. Navegue para a sua instância de **serviço azure API Management** no **portal Azure.**
2. Selecione Informações de **Aplicação** do menu à esquerda.
3. Clique em **+ Adicionar**.  
    ![Logger App Insights](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Selecione a instância de Insights de **Aplicação** previamente criada e forneça uma descrição curta.
5. Clique em **Criar**.
6. Acaba de criar um logger Azure Application Insights com uma chave de instrumentação. Deve agora aparecer na lista.  
    ![Logger App Insights](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> No local, uma entidade [Logger](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger/createorupdate) é criada na sua instância de Gestão API, contendo a Chave de Instrumentação da instância de Insights de Aplicação.

## <a name="enable-application-insights-logging-for-your-api"></a>Ativar insights de aplicação para o seu API

1. Navegue para a sua instância de **serviço azure API Management** no **portal Azure.**
2. Selecione **APIs** no menu à esquerda.
3. Clique na sua API, neste caso **Demo Conference API**.
4. Vá ao separador **Definições** a partir da barra superior.
5. Desloque-se até à secção de registos de **diagnóstico.**  
    ![Logger App Insights](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Verifique a caixa **Ativa.**
7. Selecione **o** seu madeireiro ligado no dropdown destination.
8. Insere **100** como **Amostragem (%)** e marque a caixa de **verificação de erros de registo Always.**
9. Clique em **Guardar**.

> [!WARNING]
> A sobreposição do valor padrão **0** nos **primeiros bytes do** campo do corpo pode diminuir significativamente o desempenho das suas APIs.

> [!NOTE]
> No local, uma entidade de [Diagnóstico](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/diagnostic/createorupdate) chamada 'applicationinsights' é criada ao nível da API.

| Nome da definição                        | Tipo de valor                        | Descrição                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ativar                              | boolean                           | Especifica se o registo desta API está ativado.                                                                                                                                                                                                                                                                                                |
| Destino                         | Logger de Insights de Aplicação Azure | Especifica o logger de insights de aplicação Azure a utilizar                                                                                                                                                                                                                                                                                           |
| Amostragem (%)                        | decimal                           | Valores de 0 a 100 (por cento). <br/> Especifica qual a percentagem de pedidos que serão registados na Azure Application Insights. 0% de amostragem significa zero pedidos registados, enquanto 100% de amostragem significa todos os pedidos registados. <br/> Esta definição é utilizada para reduzir as implicações de desempenho dos pedidos de registo a Insights de Aplicação Azure (ver a secção abaixo). |
| Sempre registar erros                   | boolean                           | Se esta definição for selecionada, todas as falhas serão registadas no Azure Application Insights, independentemente da definição de **Amostragem.**                                                                                                                                                                                                                  |
| Opções Básicas: Cabeçalhos              | list                              | Especifica os cabeçalhos que serão registados na Azure Application Insights para pedidos e respostas.  Predefinição: não há cabeçalhos registados.                                                                                                                                                                                                             |
| Opções Básicas: Primeiros bytes do corpo  | número inteiro                           | Especifica quantos primeiros bytes do corpo são registados na Azure Application Insights para pedidos e respostas.  Padrão: o corpo não está registado.                                                                                                                                                                                                    |
| Opções Avançadas: Verbosity         |                                   | Especifica o nível de verbosidade. Apenas vestígios personalizados com maior nível de gravidade serão registados. Predefinição: Informação.                                                                                                                                                                                                                               |
| Opções Avançadas: Pedido frontend  |                                   | Especifica se e como os *pedidos frontend* serão registados na Azure Application Insights. *O pedido frontend* é um pedido de entrada no serviço de Gestão API Azure.                                                                                                                                                                        |
| Opções Avançadas: Resposta frontend |                                   | Especifica se e como as *respostas frontend* serão registadas na Azure Application Insights. *A resposta frontend* é uma resposta saída do serviço de Gestão API Azure.                                                                                                                                                                   |
| Opções Avançadas: Pedido de backend   |                                   | Especifica se e como os pedidos de backend serão *registados* na Azure Application Insights. *O pedido de backend* é um pedido de saída do serviço de Gestão API Azure.                                                                                                                                                                        |
| Opções Avançadas: Resposta de backend  |                                   | Especifica se e como as *respostas de backend* serão registadas na Azure Application Insights. *A resposta backend* é uma resposta ao serviço de Gestão API Azure.                                                                                                                                                                       |

> [!NOTE]
> Pode especificar os madeireiros em diferentes níveis - único logger API ou um madeireiro para todas as APIs.
>  
> Se especificar ambos:
> + se forem diferentes madeireiros, então ambos serão usados (múltiplos registos),
> + se forem os mesmos madeireiros, mas tiverem configurações diferentes, então a de Uma Única API (nível mais granular) irá sobrepor-se à de todas as APIs.

## <a name="what-data-is-added-to-azure-application-insights"></a>Que dados são adicionados aos Insights de Aplicação Azure

Os Insights de Aplicação Azure recebem:

+ *Solicitar* artigo de telemetria, para cada pedido de entrada *(pedido frontend,* *resposta frontend),*
+ Item de telemetria *de dependência,* para cada pedido reencaminhado para um serviço de*backend (pedido de backend,* *resposta backend),*
+ *A exceção* do artigo de telemetria, para cada pedido falhado.

Um pedido falhado é um pedido que:

+ falhou por causa de uma ligação de cliente fechado, ou
+ desencadeou uma secção *de erro* das políticas da API, ou
+ tem um código de estado HTTP de resposta correspondente a 4xx ou 5xx.

## <a name="performance-implications-and-log-sampling"></a>Implicações de desempenho e amostragem de registo

> [!WARNING]
> A exploração de registos de todos os eventos pode ter implicações graves no desempenho, dependendo da taxa de pedidos de entrada.

Com base em testes internos de carga, a ativação desta funcionalidade provocou uma redução de 40%-50% na taxa de entrada quando a taxa de pedido ultrapassou os 1.000 pedidos por segundo. O Azure Application Insights foi concebido para utilizar a análise estatística para avaliar os desempenhos das aplicações. Não se destina a ser um sistema de auditoria e não é adequado para registar cada pedido individual de APIs de elevado volume.

Pode manipular o número de pedidos registados ajustando a definição **de amostragem** (ver os passos acima). Valor 100% significa que todos os pedidos estão registados, enquanto 0% não reflete nenhuma exploração madeireira. **A amostragem** ajuda a reduzir o volume de telemetria, efetivamente evitando uma degradação significativa do desempenho, ao mesmo tempo que carrega os benefícios da exploração madeireira.

Saltar o registo de cabeçalhos e corpo de pedidos e respostas também terá um impacto positivo na aparação de problemas de desempenho.

## <a name="video"></a>Vídeo

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Passos seguintes

+ Saiba mais sobre [os Insights de Aplicação Azure.](https://docs.microsoft.com/azure/application-insights/)
+ Considere [a exploração madeireira com o Azure Event Hubs.](api-management-howto-log-event-hubs.md)
