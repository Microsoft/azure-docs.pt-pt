---
title: 'Descrição geral: Visualização de Azure Time Series Insights | Microsoft Docs'
description: Visão geral de Azure Time Series Insights visualização.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: dpalled
ms.workload: big-data
ms.topic: overview
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: e4a57c6d4a9034d9145058e9137759c2cea19aa6
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978661"
---
# <a name="what-is-azure-time-series-insights-preview"></a>O que é a Pré-visualização do Azure Time Series Insights?

A versão prévia do Azure Time Series Insights é uma oferta de PaaS (plataforma como serviço) de ponta a ponta. Ele é usado para coletar, processar, armazenar, analisar e consultar dados altamente contextuais e com escala de IoT otimizados para a série temporal. Time Series Insights é ideal para exploração de dados ad hoc e análise operacional. Time Series Insights é uma oferta de serviço extensível e personalizada exclusiva que atende às amplas necessidades de implantações de IoT industriais.

> [!TIP]
> Para recursos em disponibilidade geral (GA), leia a [visão geral do Azure Time Series insights GA](time-series-insights-overview.md).

## <a name="video"></a>Vídeo

### <a name="learn-more-about-azure-time-series-insights-preview-br"></a>Saiba mais sobre a versão prévia do Azure Time Series Insights. </br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="define-iot-data"></a>Definir dados de IoT

Os dados de IoT são dados industriais que estão disponíveis em organizações de uso intensivo de ativos. Os dados de IoT geralmente são altamente não estruturados porque são enviados de ativos que registram medidas razoavelmente ruidosas. Essas medidas incluem temperatura, movimento e umidade. Esses fluxos de dados são frequentemente caracterizados por lacunas significativas, mensagens corrompidas e leituras falsas. Os dados desses fluxos devem ser limpos antes que qualquer análise possa ocorrer.

Os dados de IoT geralmente são significativos apenas no contexto de entradas de dados adicionais provenientes de fontes de terceiros, como CRM ou ERP. As entradas também são provenientes de fontes de dados de terceiros, como clima ou local.

Como resultado, apenas uma fração dos dados é usada para fins operacionais e comerciais. Esses dados fornecem informações consistentes, abrangentes, atuais e corretas para análise e relatórios de negócios. A ativação dos dados de IoT coletados em informações acionáveis requer:

* Processamento de dados para limpar, filtrar, interpolar, transformar e preparar dados para análise.
* Uma estrutura para navegar e entender os dados, ou seja, para normalizar e contextualizer os dados.
* Armazenamento econômico para retenção longa ou infinita de décadas de processamento ou derivados, dados e dados brutos.

Um fluxo de dados IoT típico é mostrado na imagem a seguir.

  ![Fluxo de dados de IoT][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>O Azure Time Series Insights para o industrial IoT

O atual cenário de IoT é diversificado. Os clientes abrangem os setores de manufatura, automotivo, energia, utilitários, prédios inteligentes e consultoria. Os cenários incluem a exploração de dados ad hoc em que a forma dos dados é desconhecida. Os cenários também incluem a análise operacional em esquematizados ou dados explicitamente modelados para impulsionar a eficiência operacional. Esses cenários normalmente existem lado a lado e dão suporte a diferentes casos de uso. Os recursos de plataforma que são fundamentais para o sucesso de empresas IoT industriais e sua revolução digital incluem:

- Armazenamento em várias camadas, quente e frio.
- A capacidade de armazenar décadas de dados de série temporal.
- A capacidade de modelar e otimizar explicitamente as consultas para inteligência operacional baseada em ativos.

Time Series Insights é uma oferta abrangente de PaaS de ponta a ponta para exploração de dados de IoT e informações operacionais. O Time Series Insights oferece um serviço de nuvem totalmente gerenciado para analisar dados de série temporal de escala IoT.

Você pode armazenar dados brutos em um repositório de memória sem esquema. Em seguida, você pode realizar consultas ad hoc interativas por meio de um mecanismo de consulta distribuído e uma API. Faça uso da rica experiência do usuário para visualizar bilhões de eventos em segundos. Saiba mais sobre os [recursos de exploração de dados](./time-series-insights-overview.md).

O Time Series Insights também oferece recursos de informações operacionais atualmente em versão prévia. Junto com a exploração de dados interativa e inteligência operacional, você pode usar Time Series Insights para derivar mais valor dos dados coletados de ativos de IoT. A oferta de visualização oferece suporte a:

* Um armazenamento de dados de série temporal escalonável e de desempenho e com otimização de custos. Essa solução de IoT baseada em nuvem pode tendência dos anos de dados de série temporal em segundos.
* Suporte a um modelo semântico que descreve o domínio e os metadados associados aos sinais derivados e não derivados de ativos e dispositivos.
* Uma experiência de usuário aprimorada que combina informações de dados com base em ativos com análise de dados avançada e ad hoc. Essa combinação orienta a inteligência comercial e operacional.
* Integração com ferramentas avançadas de aprendizado de máquina e análise. As ferramentas incluem Azure Databricks, Apache Spark, Azure Machine Learning, notebooks Jupyter e Power BI. Essas ferramentas ajudam você a resolver os desafios de dados de série temporal e a aumentar a eficiência operacional.

Juntos, as informações operacionais e a exploração de dados são oferecidas com um modelo de preços pago conforme o uso simples para processamento de dados, armazenamento e consulta. Esse modelo de cobrança é adequado para suas necessidades comerciais em constante mudança.

Este diagrama de fluxo de dados de alto nível mostra as atualizações.

  ![Principais capacidades][2]

Com a introdução desses principais recursos de IoT industrial, Time Series Insights fornece os seguintes benefícios principais.

| | |
| ---| ---|
| Armazenamento em várias camadas para dados de série temporal de escala IoT | Com um pipeline de processamento de dados comum para ingerir dados, você pode armazenar dados em armazenamento quente para consultas interativas. Você também pode armazenar dados no armazenamento frio para grandes volumes de dados. Aproveite as [consultas](./time-series-insights-update-tsq.md)de alto desempenho baseadas em ativos. |
| Modelo de série temporal para Contextualize telemetria bruta e derivar informações baseadas em ativos | Contextualize dados de telemetria brutos com o [modelo de série temporal](./time-series-insights-update-tsm.md)descritiva. Derive inteligência operacional sofisticada com consultas baseadas em dispositivo altamente otimizadas para desempenho e de custo. |
| Integração tranqüila e contínua com outras soluções de dados | Os dados no Time Series Insights são [armazenados](./time-series-insights-update-storage-ingress.md) em arquivos do Apache parquet de software livre. Essa integração com outras soluções de dados, seja pela primeira ou por terceiros, é fácil para cenários de ponta a ponta. Esses cenários incluem business intelligence, aprendizado avançado de máquina e análise preditiva. |
| Exploração de dados quase em tempo real | A experiência do usuário do [Azure Time Series insights Preview Explorer](./time-series-insights-update-explorer.md) fornece visualização para todos os fluxos de dados por meio do pipeline de ingestão. Logo após a conexão de uma origem de evento, você pode exibir, explorar e consultar dados de evento. Dessa forma, você pode validar se um dispositivo emite dados conforme o esperado. Você também pode monitorar um ativo de IoT quanto à integridade, à produtividade e à eficácia geral. |
| Análise de causa raiz e detecção de anomalias | O [Azure Time Series insights Preview Explorer](./time-series-insights-update-explorer.md) dá suporte a modos de exibição de padrão e perspectiva para conduzir e salvar a análise de causa raiz de várias etapas. Em combinação com Azure Stream Analytics, você pode usar Time Series Insights para detectar alertas e anomalias quase em tempo real. |
| Aplicativos personalizados criados na plataforma Time Series Insights | Time Series Insights dá suporte ao [SDK do JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). O SDK fornece controles avançados e acesso simplificado às consultas. Use o SDK para criar aplicativos de IoT personalizados sobre Time Series Insights para atender às suas necessidades de negócios específicas. Você também pode usar as Time Series Insights [APIs de consulta](./time-series-insights-update-tsq.md) diretamente para impulsionar dados em aplicativos de IOT personalizados. |

## <a name="next-steps"></a>Passos seguintes

Introdução à versão prévia do Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Leia o guia de início rápido](./time-series-insights-update-quickstart.md)

Saiba mais sobre casos de utilização:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Visualizar casos de uso](./time-series-insights-update-use-cases.md)

<!-- Images -->
[1]: media/v2-update-overview/overview-one.png
[2]: media/v2-update-overview/overview-two.png
