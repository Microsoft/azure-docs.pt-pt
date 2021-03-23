---
title: Visualizar os dados da UA do OPC em Azure Time Series Insights
description: Neste tutorial, aprende-se a visualizar dados com insights da Série Time.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 5bd218c0d94922b6137a964e3993f516216ca4b7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787814"
---
# <a name="tutorial-visualize-data-with-time-series-insights-tsi"></a>Tutorial: Visualizar dados com Insights de Séries Tempo (TSI)

O módulo OPC Publisher conecta-se aos servidores OPC UA e publica dados destes servidores ao IoT Hub. O processador de Telemetria na plataforma Industrial IoT processa estes eventos e encaminha amostras contextualizadas para a TSI e outros consumidores.  

Este guia de como visualizar e analisar a Telemetria OPC UA utilizando este ambiente de Insights de Séries temporítrias.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Todos os tutoriais incluem uma lista resumindo os passos para a conclusão
> * Cada um destes pontos de bala alinha-se com uma chave H2
> * Use estas caixas de verificação verdes num tutorial

## <a name="prerequisite"></a>Pré-requisito

* Implemente a Plataforma IIoT para obter um Time Series Insights Environment criado automaticamente
* Os dados estão a ser publicados no IoT Hub

## <a name="time-series-insights-explorer"></a>Explorador do Time Series Insights

O explorador time series Insights é uma aplicação web que pode usar para visualizar a sua telemetria. Para recuperar o url da aplicação abra o `.env` ficheiro guardado como resultado da implantação.  Abra um navegador para o Url na `PCS_TSI_URL` variável.  

Antes de utilizar o explorador Time Series Insights, deve conceder acesso aos dados da TSI aos utilizadores com direito a visualizar os dados. Note que numa nova implementação não são definidas políticas de acesso a dados por padrão, pelo que ninguém pode ver os dados. As políticas de acesso a dados devem ser definidas no portal Azure, no Time Series Insights Environment implantado na plataforma do IIoT, do seguinte modo:

   ![Time Series Insights Explorer 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-1.png)

Selecione as Políticas de Acesso a Dados:

   ![Time Series Insights Explorer 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-2.png)

Atribuir os utilizadores necessários:

   ![Time Series Insights Explorer 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-3.png)


No TSI Explorer, por favor, note as instâncias de séries de tempo não assinaladas. Uma instância TSI corresponde à série de tempo/valor de um ponto de dados específico originado de um nó publicado num servidor OPC. A Instância TSI, respectivamente o ponto de dados da UA da OPC, é identificada exclusivamente pelo EndpointId, SubscriptionId e NodeId. Os modelos de instâncias TSI são automaticamente detetados e exibidos no explorador com base nos dados de telemetria ingeridos do centro de eventos do processador de telemetria da plataforma IIoT.

   ![Time Series Insights Explorer 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-0.png)

Os dados da telemetria podem ser visualizados na tabela clicando à direita na instância TSI e selecionando o Valor. O prazo a utilizar na tabela pode ser ajustado a partir do canto superior direito. O valor de várias instâncias pode ser visualizado na mesma seleção de bases de tempo.

Para mais informações, consulte [Quickstart: Explore a pré-visualização da Série de TempoS Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)

## <a name="define-and-apply-a-new-model"></a>Definir e aplicar um novo Modelo

Uma vez que as instâncias de telemetria estão agora apenas em formato bruto, precisam de ser contextualizadas com o adequado 

Para informações detalhadas sobre os modelos TSI ver [modelo série de tempo em visualização de insights da série de tempo Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)

1. Passo 1 - No separador modelo do Explorer, defina uma nova hierarquia para os dados de telemetria ingeridos. Uma hierarquia é a estrutura lógico da árvore destinada a permitir ao utilizador inserir a meta-informação necessária para uma navegação mais intuitiva através das instâncias de ESI. um utilizador pode criar/eliminar/modificar modelos de hierarquia que podem ser posteriormente instantâneos para as várias instâncias de TSI.

   ![Passo 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-1.png)

2. Passo 2 - defina um novo tipo para os valores. No nosso exemplo, só lidamos com dados numéricos

   ![Passo 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-2.png)

3. Passo 3 - selecione a nova instância de TSI que requer ser categorizada na hierarquia previamente definida

   ![Passo 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-3.png)

4. Passo 4 - preencha as propriedades das instâncias - nome, descrição, valor de dados, bem como os campos de hierarquia, a fim de combinar com a estrutura lógica 

   ![Passo 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-4.png)

5. Passo 5 - repita o passo 5 para todas as instâncias de TSI não categorizadas

   ![Passo 5](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-5.png)

6. Passo 6 - de volta à página principal do TSI Explorer, caminhe pela hierarquia de instâncias categorizadas e selecione os valores para que os pontos de dados sejam analisados

   ![Passo 6](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-6.png)

## <a name="connect-time-series-insights-to-power-bi"></a>Conecte insights da série de tempo ao Power BI

Também pode ligar o ambiente de Insights de Séries Tempos ao Power BI.  Para obter mais informações, consulte [Como ligar a TSI ao Power BI](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi) e [visualizar dados da TSI no Power BI](https://docs.microsoft.com/azure/time-series-insights/concepts-power-bi).


## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu a visualizar dados na TSI, pode consultar o repositório de IoT GitHub industrial:

> [!div class="nextstepaction"]
> [Repositório da plataforma IIoT GitHub](https://github.com/Azure/iot-edge-opc-publisher)