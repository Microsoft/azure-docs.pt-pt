---
title: Integre a Gestão da API da Azure com Insights de Aplicação Azure
titleSuffix: Azure API Management
description: Saiba como registar e visualizar eventos da Azure API Management in Azure Application Insights.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2021
ms.author: apimpm
ms.openlocfilehash: 97f4eb34b88b3454d65b65d236833e1256c98671
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564265"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Como integrar a Gestão de API do Azure com o Azure Application Insights

A Azure API Management permite uma fácil integração com o Azure Application Insights - um serviço extensível para desenvolvedores web que couem e gerem apps em várias plataformas. Este guia percorre cada passo dessa integração e descreve estratégias para reduzir o impacto do desempenho na sua instância de serviço de Gestão API.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia, você precisa ter uma instância de Gestão API Azure. Se não tiver um, complete o [tutorial](get-started-create-service-instance.md) primeiro.

## <a name="create-an-application-insights-instance"></a>Criar uma instância de Insights de Aplicação

Antes de poder utilizar o Application Insights, primeiro tem de criar uma instância do serviço. Para obter medidas para criar um exemplo utilizando o portal Azure, consulte os [recursos de Insights de Aplicação baseados no Espaço de Trabalho](../azure-monitor/app/create-workspace-resource.md).
## <a name="create-a-connection-between-application-insights-and-api-management"></a>Criar uma ligação entre Insights de Aplicações e Gestão de API

1. Navegue para a sua **instância de serviço de Gestão API Azure** no portal **Azure.**
1. Selecione Informações de **aplicação** do menu à esquerda.
1. Clique em **+ Adicionar**.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="Screenshot que mostra onde adicionar uma nova ligação":::
1. Selecione a instância **de Insights de Aplicação** previamente criada e forneça uma breve descrição.
1. Clique em **Criar**.
1. Acaba de criar um madeireiro Application Insights com uma chave de instrumentação. Deve agora aparecer na lista.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="Screenshot que mostra onde ver o recém-criado Coveiro de Aplicações com chave de instrumentação":::

> [!NOTE]
> Por detrás da cena, é criada uma entidade [Logger](/rest/api/apimanagement/2019-12-01/logger/createorupdate) no seu caso de Gestão de API, contendo a Chave de Instrumentação da instância Application Insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Ativar insights de aplicação para a sua API

1. Navegue para a sua **instância de serviço de Gestão API Azure** no portal **Azure.**
1. Selecione **APIs** no menu à esquerda.
1. Clique na sua API, neste caso **Demo Conference API**. Se configurado, selecione uma versão.
1. Vá ao **separador Definições** a partir da barra superior.
1. Desloque-se até à secção **de Registos de Diagnóstico.**  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-api-1.png" alt-text="Logger App Insights":::
1. Verifique a caixa **Ativar.**
1. Selecione o seu madeireiro anexado no **dropdown destino.**
1. Insira **100** como **Amostragem (%)** e selecione a caixa de verificação **de erros de registo Always.**
1. Selecione **Guardar**.

> [!WARNING]
> A sobreposição do valor predefinido **0** no **Número de bytes de carga útil para registar a** definição pode diminuir significativamente o desempenho das suas APIs.

> [!NOTE]
> Por detrás do local, é criada uma entidade de [Diagnóstico](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) denominada 'applicationinsights' ao nível da API.

| Nome da definição                        | Tipo de valor                        | Descrição                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ativar                              | boolean                           | Especifica se o registo desta API está ativado.                                                                                                                                                                                                                                                                                                |
| Destino                         | Azure Application Insights logger | Especifica o Azure Application Insights logger a ser usado                                                                                                                                                                                                                                                                                           |
| Amostragem (%)                        | decimal                           | Valores de 0 a 100 (por cento). <br/> Especifica a percentagem de pedidos que serão registados no Application Insights. 0% de amostragem significa zero pedidos registados, enquanto 100% de amostragem significa todos os pedidos registados. <br/> Utilize esta definição para reduzir o efeito sobre o desempenho ao registar pedidos para Insights de Aplicação. Consulte [as implicações de desempenho e a amostragem de registo](#performance-implications-and-log-sampling). |
| Sempre registar erros                   | boolean                           | Se esta definição for selecionada, todas as falhas serão registadas no Application Insights, independentemente da definição **de Amostragem.**   
| Registar endereço IP do cliente | |  Se esta definição for selecionada, o endereço IP do cliente para pedidos de API será registado no Application Insights.                                         |
| Verbosidade         |                                   | Especifica o nível de verbosidade. Apenas os vestígios personalizados com maior gravidade serão registados. Padrão: Informação.      | 
| Protocolo de correlação |  |  Selecione o protocolo utilizado para correlacionar a telemetria enviada por vários componentes. Padrão: Legado <br/>Para obter informações, consulte [a correlação de telemetria em Insights de Aplicação](../azure-monitor/app/correlation.md).  |
| Opções básicas: Cabeçalhos para registar              | lista                              | Especifica os cabeçalhos que serão registados no Application Insights para pedidos e respostas.  Predefinido: não há cabeçalhos registados.                                                                                                                                                                                                             |
| Opções Básicas: Número de bytes de carga útil para registar | número inteiro                           | Especifica quantos primeiros bytes do corpo são registados no Application Insights para pedidos e respostas.  Predefinição: 0.                                                                                                                                                                                                    |
| Opções Avançadas: Pedido de Frontend  |                                   | Especifica se e como os *pedidos de frontend* serão registados no Application Insights. *O pedido frontend* é um pedido de entrada no serviço de Gestão API da Azure.                                                                                                                                                                        |
| Opções Avançadas: Resposta frontend |                                   | Especifica se e como as *respostas frontais* serão registadas no Application Insights. *A resposta frontend* é uma resposta saída do serviço de Gestão API da Azure.                                                                                                                                                                   |
| Opções Avançadas: Pedido de Backend   |                                   | Especifica se e como os *pedidos de backend* serão registados no Application Insights. *O pedido de backend* é um pedido de saída do serviço de Gestão API da Azure.                                                                                                                                                                        |
| Opções Avançadas: Resposta ao backend  |                                   | Especifica se e como as *respostas de backend* serão registadas no Application Insights. *Resposta backend* é uma resposta que chega ao serviço de Gestão API da Azure.                                                                                                                                                                       |

> [!NOTE]
> Você pode especificar madeireiros em diferentes níveis - um único madeireiro API ou um madeireiro para todas as APIs.
>  
> Se especificar ambos:
> + se forem madeireiros diferentes, então ambos serão usados (troncos multiplexing),
> + se forem os mesmos madeireiros, mas tiverem configurações diferentes, então a de um único API (nível mais granular) irá sobrepor-se à de todas as APIs.

## <a name="what-data-is-added-to-application-insights"></a>Que dados são adicionados aos Insights de Aplicação

O Application Insights recebe:

+ *Solicitação* de telemetria, para cada pedido de entrada *(pedido de receção* frontal, *resposta frontal),*
+ Artigo de telemetria *de dependência,* para cada pedido reencaminhado para um serviço de *backend (pedido de backend,* *resposta de backend),*
+ Artigo de telemetria de *exceção,* para cada pedido falhado:
    + falhou por causa de uma conexão cliente fechado
    + desencadeou uma secção *de erro* das políticas da API
    + tem um código de estado HTTP de resposta que corresponde a 4xx ou 5xx.
+ *Trace* o item da telemetria, se configurar uma política [de rastreio.](api-management-advanced-policies.md#Trace) A `severity` definição na `trace` política deve ser igual ou superior à `verbosity` definição no registo de Insights de Aplicação.

> [!NOTE]
> Consulte [os limites de Insights de Aplicação](../azure-monitor/service-limits.md#application-insights) para obter informações sobre o tamanho máximo e o número de métricas e eventos por instância de Insights de Aplicação.

## <a name="performance-implications-and-log-sampling"></a>Implicações de desempenho e amostragem de registo

> [!WARNING]
> Registar todos os eventos pode ter sérias implicações de desempenho, dependendo da taxa de pedidos de entrada.

Com base em testes internos de carga, permitir esta função causou uma redução de 40%-50% na produção quando a taxa de pedido excedia 1.000 pedidos por segundo. O Application Insights foi concebido para utilizar a análise estatística para avaliar os desempenhos das aplicações. Não se destina a ser um sistema de auditoria e não é adequado para registar cada pedido individual de APIs de grande volume.

Pode manipular o número de pedidos que estão a ser registados ajustando a definição **de Amostragem** (ver os passos anteriores). Um valor de 100% significa que todos os pedidos são registados, enquanto 0% não reflete nenhuma exploração madeireira. **A amostragem** ajuda a reduzir o volume de telemetria, evitando efetivamente uma degradação significativa do desempenho, enquanto ainda carrega os benefícios da exploração madeireira.

Saltar o registo de cabeçalhos e o corpo de pedidos e respostas também terão um impacto positivo na redução das questões de desempenho.

## <a name="video"></a>Vídeo

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Passos seguintes

+ Saiba mais sobre [a Azure Application Insights](/azure/application-insights/).
+ Considere [fazer login com os Azure Event Hubs](api-management-howto-log-event-hubs.md).
