---
title: Integre a Gestão da API da Azure com Insights de Aplicação Azure
titleSuffix: Azure API Management
description: Saiba como registar e visualizar eventos da Azure API Management in Azure Application Insights.
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
ms.openlocfilehash: 8c9df3393a0554d2e65b3918c6760885f89e11ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86254749"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Como integrar a Gestão de API do Azure com o Azure Application Insights

A Azure API Management permite uma fácil integração com o Azure Application Insights - um serviço extensível para desenvolvedores web que couem e gerem apps em várias plataformas. Este guia percorre cada passo dessa integração e descreve estratégias para reduzir o impacto do desempenho na sua instância de serviço de Gestão API.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia, você precisa ter uma instância de Gestão API Azure. Se não tiver um, complete o [tutorial](get-started-create-service-instance.md) primeiro.

## <a name="create-an-azure-application-insights-instance"></a>Criar uma instância Azure Application Insights

Antes de poder utilizar o Azure Application Insights, primeiro tem de criar uma instância do serviço.

1. Abra o **portal Azure** e navegue para **o Application Insights**.  
    ![App Insights criar](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Clique em **+ Adicionar**.  
    ![App Insights criar](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Preencha o formulário. Selecione **Geral** como **o Tipo de Aplicação**.
4. Clique em **Criar**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Criar uma ligação entre a Azure Application Insights e a azure API Management service instance

1. Navegue para a sua **instância de serviço de Gestão API Azure** no portal **Azure.**
2. Selecione Informações de **aplicação** do menu à esquerda.
3. Clique em **+ Adicionar**.  
    ![Logger App Insights](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Selecione a instância **de Insights de Aplicação** previamente criada e forneça uma breve descrição.
5. Clique em **Criar**.
6. Acaba de criar um madeireiro Azure Application Insights com uma chave de instrumentação. Deve agora aparecer na lista.  
    ![Logger App Insights](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> Por detrás da cena, é criada uma entidade [Logger](/rest/api/apimanagement/2019-12-01/logger/createorupdate) no seu caso de Gestão de API, contendo a Chave de Instrumentação da instância Application Insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Ativar insights de aplicação para a sua API

1. Navegue para a sua **instância de serviço de Gestão API Azure** no portal **Azure.**
2. Selecione **APIs** no menu à esquerda.
3. Clique na sua API, neste caso **Demo Conference API**.
4. Vá ao **separador Definições** a partir da barra superior.
5. Desloque-se até à secção **de Registos de Diagnóstico.**  
    ![Logger App Insights](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Verifique a caixa **Ativar.**
7. Selecione o seu madeireiro anexado no **dropdown destino.**
8. Inserir **100** como **Amostragem (%)** e marcar a caixa de verificação **de erros de registo Always.**
9. Clique em **Guardar**.

> [!WARNING]
> Sobrepriming o valor **padrão 0** nos **primeiros bytes do** campo do corpo pode diminuir significativamente o desempenho das suas APIs.

> [!NOTE]
> Por detrás do local, é criada uma entidade de [Diagnóstico](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) denominada 'applicationinsights' ao nível da API.

| Nome da definição                        | Tipo de valor                        | Descrição                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ativar                              | boolean                           | Especifica se o registo desta API está ativado.                                                                                                                                                                                                                                                                                                |
| Destino                         | Azure Application Insights logger | Especifica o Azure Application Insights logger a ser usado                                                                                                                                                                                                                                                                                           |
| Amostragem (%)                        | decimal                           | Valores de 0 a 100 (por cento). <br/> Especifica qual a percentagem de pedidos que serão registados no Azure Application Insights. 0% de amostragem significa zero pedidos registados, enquanto 100% de amostragem significa todos os pedidos registados. <br/> Esta definição é utilizada para reduzir as implicações de desempenho dos pedidos de registo de registos para Azure Application Insights (ver secção abaixo). |
| Sempre registar erros                   | boolean                           | Se esta definição for selecionada, todas as falhas serão registadas no Azure Application Insights, independentemente da definição de **Amostragem.**                                                                                                                                                                                                                  |
| Opções básicas: Cabeçalhos              | lista                              | Especifica os cabeçalhos que serão registados no Azure Application Insights para pedidos e respostas.  Predefinido: não há cabeçalhos registados.                                                                                                                                                                                                             |
| Opções Básicas: Primeiros bytes do corpo  | número inteiro                           | Especifica quantos primeiros bytes do corpo são registados no Azure Application Insights para pedidos e respostas.  Padrão: o corpo não está registado.                                                                                                                                                                                                    |
| Opções Avançadas: Verbosity         |                                   | Especifica o nível de verbosidade. Apenas os vestígios personalizados com maior gravidade serão registados. Padrão: Informação.                                                                                                                                                                                                                               |
| Opções Avançadas: Pedido de Frontend  |                                   | Especifica se e como os *pedidos de frontend* serão registados no Azure Application Insights. *O pedido frontend* é um pedido de entrada no serviço de Gestão API da Azure.                                                                                                                                                                        |
| Opções Avançadas: Resposta frontend |                                   | Especifica se e como as *respostas frontais* serão registadas no Azure Application Insights. *A resposta frontend* é uma resposta saída do serviço de Gestão API da Azure.                                                                                                                                                                   |
| Opções Avançadas: Pedido de Backend   |                                   | Especifica se e como os *pedidos de backend* serão registados no Azure Application Insights. *O pedido de backend* é um pedido de saída do serviço de Gestão API da Azure.                                                                                                                                                                        |
| Opções Avançadas: Resposta ao backend  |                                   | Especifica se e como as *respostas de backend* serão registadas no Azure Application Insights. *Resposta backend* é uma resposta que chega ao serviço de Gestão API da Azure.                                                                                                                                                                       |

> [!NOTE]
> Você pode especificar madeireiros em diferentes níveis - um único madeireiro API ou um madeireiro para todas as APIs.
>  
> Se especificar ambos:
> + se forem madeireiros diferentes, então ambos serão usados (troncos multiplexing),
> + se forem os mesmos madeireiros, mas tiverem configurações diferentes, então a de um único API (nível mais granular) irá sobrepor-se à de todas as APIs.

## <a name="what-data-is-added-to-azure-application-insights"></a>Que dados são adicionados ao Azure Application Insights

A Azure Application Insights recebe:

+ *Solicitação* de telemetria, para cada pedido de entrada *(pedido de receção*frontal, *resposta frontal),*
+ Artigo de telemetria *de dependência,* para cada pedido reencaminhado para um serviço de*backend (pedido de backend,* *resposta de backend),*
+ *Artigo* de telemetria exceção, para cada pedido falhado.

Um pedido falhado é um pedido que:

+ falhou por causa de uma ligação de cliente fechado, ou
+ desencadeou uma secção *de erro* das políticas da API, ou
+ tem um código de estado HTTP de resposta que corresponde a 4xx ou 5xx.

## <a name="performance-implications-and-log-sampling"></a>Implicações de desempenho e amostragem de registo

> [!WARNING]
> Registar todos os eventos pode ter sérias implicações de desempenho, dependendo da taxa de pedidos de entrada.

Com base em testes internos de carga, permitir esta função causou uma redução de 40%-50% na produção quando a taxa de pedido excedia 1.000 pedidos por segundo. A Azure Application Insights foi concebida para utilizar a análise estatística para avaliar os desempenhos das aplicações. Não se destina a ser um sistema de auditoria e não é adequado para registar cada pedido individual de APIs de grande volume.

Pode manipular o número de pedidos que estão a ser registados ajustando a definição **de Amostragem** (ver os passos acima). Valor 100% significa que todos os pedidos são registados, enquanto 0% não reflete nenhuma exploração madeireira. **A amostragem** ajuda a reduzir o volume de telemetria, evitando efetivamente uma degradação significativa do desempenho, enquanto ainda carrega os benefícios da exploração madeireira.

Saltar o registo de cabeçalhos e o corpo de pedidos e respostas também terão um impacto positivo na redução das questões de desempenho.

## <a name="video"></a>Vídeo

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Passos seguintes

+ Saiba mais sobre [a Azure Application Insights](/azure/application-insights/).
+ Considere [fazer login com os Azure Event Hubs](api-management-howto-log-event-hubs.md).
