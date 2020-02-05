---
title: 'Visão geral: o que é Azure Time Series Insights versão prévia? -Azure Time Series Insights | Microsoft Docs'
description: Saiba mais sobre as alterações, melhorias e recursos no Azure Time Series Insights Preview.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 1e4f72300752c93659db4edd4610464dbebf2503
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014424"
---
# <a name="what-is-azure-time-series-insights-preview"></a>O que é a Pré-visualização do Azure Time Series Insights?

A versão prévia do Azure Time Series Insights é uma oferta de PaaS (plataforma como serviço) de ponta a ponta. Você pode usá-lo para coletar, processar, armazenar, analisar e consultar dados em escala de Internet das Coisas (IoT)--dados altamente contextuais e otimizados para a série temporal. 

O Time Series Insights foi projetado para exploração de dados ad hoc e análise operacional. É uma oferta de serviço extensível e personalizada que atende às amplas necessidades de implantações de IoT industriais.

## <a name="video"></a>Vídeo

Saiba mais sobre a versão prévia do Azure Time Series Insights.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="definition-of-iot-data"></a>Definição de dados de IoT

Dados de IoT industrial em organizações com uso intensivo de ativos geralmente não têm consistência estrutural devido à natureza variada de dispositivos e sensores em uma configuração industrial. Os dados desses fluxos são caracterizados por lacunas significativas e, às vezes, mensagens corrompidas e leituras falsas. Os dados de IoT geralmente são significativos no contexto de entradas de dados adicionais provenientes de terceiros ou de outras fontes, como CRM ou ERP, que adicionam contexto para fluxos de trabalho de ponta a ponta. As entradas de fontes de dados de terceiros, como dados meteorológicos, podem ajudar a aumentar os fluxos de telemetria em uma determinada instalação. 

Tudo isso implica que apenas uma fração dos dados é usada para fins operacionais e comerciais, e a análise requer contextualização. Os dados industriais geralmente são históricos para análise detalhada em intervalos de tempo mais longos para entender e correlacionar tendências. A ativação dos dados de IoT coletados em informações acionáveis requer: 

* Processamento de dados para limpar, filtrar, interpolar, transformar e preparar dados para análise.
* Uma estrutura para navegar e entender os dados, ou seja, para normalizar e contextualizer os dados.
* Armazenamento econômico para retenção longa ou infinita de dados processados (ou derivados) e dados brutos.

Esses dados fornecem informações consistentes, abrangentes, atuais e corretas para análise e relatórios de negócios.

A imagem a seguir mostra um fluxo de dados de IoT típico.

[fluxo de dados ioT ![](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-for-industrial-iot"></a>O Azure Time Series Insights para o industrial IoT

O cenário de IoT é diversificado com clientes abrangendo uma variedade de segmentos do setor, incluindo manufatura, automotivo, energia, utilitários, prédios inteligentes e consultoria. Em toda a ampla variedade de mercado IoT industrial, soluções nativas de nuvem que fornecem análises abrangentes direcionadas a dados de IoT de grande escala ainda estão em evolução. 

Azure Time Series Insights atende a essa necessidade de mercado fornecendo uma solução de análise de IoT completa e completa com modelagem semântica avançada para a condefinição de dados de série temporal, insights baseados em ativos e a melhor experiência de usuário de classe para descoberta, tendência, detecção de anomalias e inteligência operacional. 

Uma plataforma de análise operacional avançada combinada com nossos recursos de exploração de dados interativos, você pode usar Time Series Insights para derivar mais valor dos dados coletados de ativos de IoT. A oferta de visualização oferece suporte a: 

* Solução de armazenamento em várias camadas com suporte a análise passiva e fria, fornecendo aos clientes a opção de rotear dados entre o baixo e o frio para análise interativa sobre dados quentes, bem como inteligência operacional em décadas de dados históricos. 

    *   Uma solução de análise quente altamente interativa para executar com frequência e um grande número de consultas em dados com intervalos de tempo menores 
    *   Um data Lake de série temporal escalonável, de alto desempenho e com otimização de custos com base no armazenamento do Azure, permitindo que os clientes tenham uma tendência de dados de série temporal em segundos. 

* Suporte de modelo semântico que descreve o domínio e os metadados associados aos sinais derivados e brutos de ativos e dispositivos.

* Plataforma de análise flexível para armazenar dados históricos de série temporal na conta de armazenamento do Azure de Propriedade do cliente, permitindo assim que os clientes tenham propriedade de seus dados de IoT. Os dados são armazenados no formato Apache parquet de código aberto que permite a conectividade e a interoperabilidade em uma variedade de cenários de dados, incluindo análise preditiva, aprendizado de máquina e outros cálculos personalizados feitos usando tecnologias familiares, incluindo o Spark, Databricks e Jupyter.

* Análise avançada com APIs de consulta aprimoradas e experiência do usuário que combina informações de dados com base em ativos com análises de dados avançadas e ad hoc com suporte para interpolação, funções escalares e de agregação, variáveis categóricas, gráficos de dispersão e tempo de mudança de tempo sinais de série para análise detalhada.

*   Plataforma de nível empresarial para dar suporte às necessidades de escala, desempenho, segurança e confiabilidade de nossos clientes de IoT corporativos.

* Suporte de extensibilidade e integração para análise de ponta a ponta. O Time Series Insights fornece uma plataforma de análise extensível para uma variedade de cenários de dados. Time Series Insights conector de Power BI permite que os clientes executem as consultas que eles fazem em Time Series Insights diretamente no Power BI para obter uma exibição unificada de suas análises de BI e de série temporal em um único painel.

O diagrama a seguir mostra o fluxo de dados de alto nível.

  [![principais capacidades](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

O Azure Time Series Insights fornece um modelo de preços pago conforme o uso para processamento de dados, armazenamento (dados e metadados) e consulta, permitindo que os clientes ajustem seu uso para atender às suas demandas de negócios. 
 
Com a introdução desses principais recursos de IoT industrial, a Time Series Insights também oferece os seguintes benefícios principais.  

| | |
| ---| ---|
| Armazenamento em várias camadas para dados de série temporal de escala IoT | Com um pipeline de processamento de dados compartilhado para ingerir dados, você pode ingerir dados em lojas quentes e inativos. Use o armazenamento quente para consultas interativas e Cold Store para armazenar grandes volumes de dados. Para saber mais sobre como aproveitar as vantagens de alto desempenho de consultas baseadas em ativos, consulte [consultas](./time-series-insights-update-tsq.md). |
| Modelo de série temporal para Contextualize telemetria bruta e derivar informações baseadas em ativos | Você pode usar o modelo de série temporal para criar instâncias, hierarquias, tipos e variáveis para seus dados de série temporal. Para saber mais sobre o modelo de série temporal, confira [modelo de série temporal](./time-series-insights-update-tsm.md).  |
| Integração tranqüila e contínua com outras soluções de dados | Os dados em Time Series Insights armazenamento frio são [armazenados](./time-series-insights-update-storage-ingress.md) em arquivos Apache parquet de software livre. Isso permite a integração de dados com outras soluções de dados, 1ª ou terceiros, para cenários que incluem business intelligence, aprendizado de máquina avançado e análise preditiva. |
| Exploração de dados quase em tempo real | A experiência do usuário do [Azure Time Series insights Preview Explorer](./time-series-insights-update-explorer.md) fornece visualização para todos os fluxos de dados por meio do pipeline de ingestão. Depois de conectar uma origem de evento, você pode exibir, explorar e consultar dados de evento. Dessa forma, você pode validar se um dispositivo emite dados conforme o esperado. Você também pode monitorar um ativo de IoT quanto à integridade, à produtividade e à eficácia geral. | 
| Extensibilidade e integração | A integração do conector de Power BI Azure Time Series Insights está disponível diretamente na experiência do usuário do Gerenciador de séries temporais por meio da opção **Exportar** , permitindo que os clientes exportem as consultas de série temporal que criam em nossa experiência do usuário diretamente na área de trabalho do Power bi e exibam seus gráficos de série temporal junto com outras análises de BI. Isso abre a porta para uma nova classe de cenários para empresas IoT industriais que investiram em Power BI fornecendo um único painel sobre análise de várias fontes de dados, incluindo série temporal de IoT. | 
| Aplicativos personalizados criados na plataforma de Time Series Insights | Time Series Insights dá suporte ao [SDK do JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). O SDK fornece controles avançados e acesso simplificado às consultas. Use o SDK para criar aplicativos de IoT personalizados sobre Time Series Insights para atender às suas necessidades de negócios. Você também pode usar as Time Series Insights [APIs de consulta](./time-series-insights-update-tsq.md) diretamente para impulsionar dados em aplicativos de IOT personalizados. |

## <a name="next-steps"></a>Passos seguintes

Introdução à versão prévia do Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Guia de início rápido](./time-series-insights-update-quickstart.md)

Saiba mais sobre casos de utilização:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Visualizar casos de uso](./time-series-insights-update-use-cases.md)