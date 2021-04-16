---
title: O que é o Azure Digital Twins?
titleSuffix: Azure Digital Twins
description: Visão geral do que pode ser feito com a Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: d03a4865c8db52f74f4130c458fec3028f5b95a4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481672"
---
# <a name="what-is-azure-digital-twins"></a>O que é o Azure Digital Twins?

**A Azure Digital Twins** é uma plataforma como uma oferta de serviço (PaaS) que permite a criação de gráficos de conhecimento baseados em modelos digitais de ambientes inteiros. Estes ambientes poderiam ser edifícios, fábricas, fazendas, redes de energia, caminhos de ferro, estádios e muito mais - até cidades inteiras. Estes modelos digitais podem ser usados para obter insights que impulsionam melhores produtos, operações otimizadas, custos reduzidos e experiências inovadoras do cliente.

Aproveite a sua experiência de domínio em cima da Azure Digital Twins para construir soluções personalizadas e conectadas que:
* Modele qualquer ambiente e traga gémeos digitais à vida de forma escalável e segura
* Ligar ativos como dispositivos IoT e sistemas de negócio existentes
* Use um sistema de eventos robusto para construir lógica de negócio dinâmica e processamento de dados
* Integre com dados, análises e serviços de IA do AZure para ajudá-lo a rastrear o passado e, em seguida, prever o futuro

## <a name="azure-digital-twins-capabilities"></a>Capacidades de Gémeos Digitais Azure

Aqui está um resumo das funcionalidades fornecidas pela Azure Digital Twins.

### <a name="open-modeling-language"></a>Linguagem de modelação aberta

No Azure Digital Twins, define as entidades digitais que representam as pessoas, lugares e coisas no seu ambiente físico usando tipos [**gémeos personalizados chamados modelos.**](concepts-models.md) 

Pode pensar nestas definições de modelo como um vocabulário especializado para descrever o seu negócio. Para uma solução de gestão de edifícios, por exemplo, poderá definir modelos como "edifício", "piso" e "elevador". Em seguida, pode criar **gémeos digitais** com base nestes modelos para representar o seu ambiente específico.

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

Os modelos são definidos numa linguagem semelhante a JSON chamada Linguagem de Definição de [Gémeos Digitais (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)e descrevem gémeos em termos das suas propriedades estatais, eventos de telemetria, comandos, componentes e relacionamentos.
* Os modelos definem **relações** semânticas entre as suas entidades para que possa ligar os seus gémeos a um gráfico de conhecimento que reflita as suas interações. Podes pensar nos modelos como substantivos numa descrição do teu mundo, e nas relações como verbos.
* Também pode especializar gémeos usando a herança modelo. Um modelo pode herdar de outro.

O DTDL é utilizado para modelos de dados em outros serviços Azure IoT, incluindo [IoT Plug and Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) e [Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md). Isto ajuda-o a manter a sua solução Azure Digital Twins conectada e compatível com outras partes do ecossistema Azure.

### <a name="live-execution-environment"></a>Ambiente de execução ativo

Os modelos digitais em Azure Digital Twins são representações ao vivo e atualizadas do mundo real. Utilizando as relações nos seus modelos DTDL personalizados, irá ligar gémeos a um **gráfico ao vivo** que representa o seu ambiente.

Pode ver uma visualização do seu gráfico Azure Digital Twins através da ajuda de uma aplicação de amostra, [**explorador de Gémeos Digitais Azure.**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)

Aqui está uma visão de como a visualização da amostra parece:

:::image type="content" source="media/includes/azure-digital-twins-explorer.png" alt-text="Screenshot da aplicação de amostra de explorador de Azure Digital Twins, mostrando um gráfico de nóles representando gémeos digitais" lightbox="media/includes/azure-digital-twins-explorer.png":::

A Azure Digital Twins fornece um sistema de **eventos** rico para manter esse gráfico atual com processamento de dados e lógica de negócio. Pode ligar recursos de computação externos, como [as Funções Azure,](../azure-functions/functions-overview.md)para impulsionar este processamento de dados de forma flexível e personalizada.

Também pode extrair insights do ambiente de execução ao vivo, utilizando a poderosa **consulta API** da Azure Digital Twins. A API permite-lhe consultar com condições de pesquisa ricas, incluindo valores de propriedade, relacionamentos, propriedades de relacionamento, informação de modelo, e muito mais. Você também pode combinar consultas, reunindo uma ampla gama de insights sobre o seu ambiente e respondendo a questões personalizadas que são importantes para você.

### <a name="input-from-iot-and-business-systems"></a>Entrada de IoT e sistemas de negócios

Para manter o ambiente de execução ao vivo da Azure Digital Twins atualizado com o mundo real, pode utilizar o [IoT Hub](../iot-hub/about-iot-hub.md) para ligar a sua solução a dispositivos IoT e IoT Edge. Estes dispositivos geridos pelo hub são representados como parte do seu gráfico gémeo e fornecem os dados que impulsionam o seu modelo.

Pode criar um novo Hub IoT para este fim com a Azure Digital Twins, ou ligar um Hub IoT existente juntamente com os dispositivos que já gere.

Também pode conduzir a Azure Digital Twins a partir de outras fontes de dados, utilizando APIs rest ou conectores para outros serviços como [As Aplicações Lógicas.](../logic-apps/logic-apps-overview.md)

### <a name="output-to-tsi-storage-and-analytics"></a>Saída para TSI, armazenamento e análise

Os dados do seu modelo Azure Digital Twins podem ser encaminhados para os serviços Azure a jusante para análise ou armazenamento adicional. Isto é fornecido através de **rotas de eventos**, que usam [Event Hub,](../event-hubs/event-hubs-about.md) [Event Grid](../event-grid/overview.md)ou [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) para conduzir os fluxos de dados pretendidos.

Algumas coisas que você pode fazer com as rotas do evento incluem:
* Armazenar dados do Azure Digital Twins no [Azure Data Lake](../storage/blobs/data-lake-storage-introduction.md)
* Analisar dados do Azure Digital Twins com o [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) ou outras ferramentas de análise de dados da Microsoft
* Integrar fluxos de trabalho maiores com o Logic Apps
* Ligar as Gémeas Digitais Azure a Time Series Insights para acompanhar a história da série de tempo de cada gémeo
* Alinhar um Modelo de Série de Tempo ao Time Series Insights com origem no Azure Digital Twins

Esta é outra forma de a Azure Digital Twins se ligar a uma solução maior e suportar as suas necessidades personalizadas para continuar a trabalhar com estas ideias.

## <a name="azure-digital-twins-in-a-solution-context"></a>Azure Digital Twins em contexto de solução

A azure Digital Twins é comumente usado em combinação com outros serviços Azure como parte de uma solução IoT maior. 

Uma solução completa utilizando as Gémeas Digitais Azure pode conter as seguintes partes:
* A instância de serviço da Azure Digital Twins. Isto armazena os seus modelos gémeos e o seu gráfico gémeo com o seu estado, e orquestra o processamento de eventos.
* Uma ou mais aplicações de clientes que impulsionam a instância Azure Digital Twins configurando modelos, criando topologia e extraindo insights do gráfico gémeo.
* Um ou mais recursos de computação externos para processar eventos gerados pela Azure Digital Twins, ou fontes de dados conectadas, como dispositivos. Uma forma comum de fornecer recursos computacional é através das [Funções Azure.](../azure-functions/functions-overview.md)
* Um hub IoT para fornecer capacidades de gestão de dispositivos e fluxo de dados IoT.
* Serviços a jusante para lidar com tarefas como integração de fluxos de trabalho (como [Aplicações Lógicas,](../logic-apps/logic-apps-overview.md)armazenamento frio, integração de séries de tempo ou análise).

O diagrama que se segue mostra onde a Azure Digital Twins se encontra no contexto de uma solução Azure IoT maior.

:::image type="content" source="media/overview/solution-context.png" alt-text="Diagrama mostrando fontes de entrada, serviços de saída e comunicação bidirecional com aplicações de clientes e recursos de computação externa." border="false" lightbox="media/overview/solution-context.png":::

## <a name="service-limits"></a>Limites do serviço

Pode ler aqui sobre os **limites** de serviço da Azure Digital Twins: Limites de [serviço Azure Digital Twins](reference-service-limits.md). Isto pode ser útil ao trabalhar com o serviço para entender as limitações funcionais e de tarifas do serviço, bem como quais os limites que podem ser ajustados se necessário.

## <a name="terminology"></a>Terminologia

Você pode ver uma lista de **termos IoT comuns** e seus usos através dos serviços Azure IoT, incluindo Azure Digital Twins, aqui: [Azure IoT Glossary](../iot-fundamentals/iot-glossary.md?toc=/azure/digital-twins/toc.json&bc=/azure/digital-twins/breadcrumb/toc.json). Esta pode ser uma referência útil enquanto começa com a Azure Digital Twins e constrói uma solução IoT.

## <a name="next-steps"></a>Passos seguintes

* Mergulhe em trabalhar com a Azure Digital Twins no arranque rápido: [*Quickstart: Explore um cenário de amostra*](quickstart-azure-digital-twins-explorer.md).

* Ou, comece a ler sobre conceitos Azure Digital Twins com [*Conceitos: Modelos personalizados.*](concepts-models.md)