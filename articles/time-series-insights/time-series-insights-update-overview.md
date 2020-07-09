---
title: 'Resumo: O que é a antevisão das séries de tempo Azure? - Azure Time Series Insights / Microsoft Docs'
description: Saiba mais sobre alterações, melhorias e funcionalidades na Pré-visualização do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 04/13/2020
ms.custom: seodec18
ms.openlocfilehash: 20ab78aa67fa5d59dac774559b3c43561cf75fbc
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86040712"
---
# <a name="what-is-azure-time-series-insights-preview"></a>O que é a Pré-visualização do Azure Time Series Insights?

A azure Time Series Insights Preview é uma oferta de plataforma-a-serviço (PaaS) de ponta a ponta. Você pode usá-lo para recolher, processar, armazenar, analisar e consultar dados na escala internet of Things (IoT) - dados altamente contextualizados e otimizados para séries temporizadas. 

Time Series Insights é projetado para a exploração de dados ad hoc e análise operacional. É uma oferta de serviços extensível e personalizada que atende às amplas necessidades de implantações industriais de IoT.

## <a name="video"></a>Vídeo

Saiba mais sobre a antevisão da Azure Time Series Insights.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="definition-of-iot-data"></a>Definição de dados IoT

Os dados de IoT industriais em organizações intensivas de ativos muitas vezes carecem de consistência estrutural devido à natureza variada de dispositivos e sensores em um ambiente industrial. Os dados destes fluxos são caracterizados por lacunas significativas, e por vezes mensagens corrompidas e leituras falsas. Os dados ioT são muitas vezes significativos no contexto de entradas de dados adicionais que provêm de fontes de primeira ou terceira, tais como CRM ou ERP que adicionam contexto para fluxos de trabalho de ponta a ponta. As entradas de fontes de dados de terceiros, tais como dados meteorológicos, podem ajudar a aumentar os fluxos de telemetria numa determinada instalação. 

Tudo isto implica que apenas uma fração dos dados é utilizada para fins operacionais e empresariais, e a análise requer contextualização. Os dados industriais são muitas vezes historicizados para uma análise aprofundada ao longo de períodos de tempo mais longos para compreender e correlacionar tendências. A transformação de dados IoT recolhidos em insights acccionados requer: 

* Processamento de dados para limpar, filtrar, interpolar, transformar e preparar dados para análise.
* Uma estrutura para navegar e compreender os dados, isto é, para normalizar e contextualizar os dados.
* Armazenamento rentável para retenção longa ou infinita de dados processados (ou derivados) e dados brutos.

Estes dados fornecem informações consistentes, abrangentes, atuais e corretas para análise e reporte de negócios.

A imagem a seguir mostra um fluxo típico de dados IoT.

[![Fluxo de dados IoT](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights para IoT industrial

A paisagem IoT é diversificada, com clientes que abrangem uma variedade de segmentos da indústria, incluindo fabricação, automóvel, energia, utilitários, edifícios inteligentes e consultoria. Ao longo desta vasta gama de mercados industriais de IoT, as soluções nativas em nuvem que fornecem análises abrangentes direcionadas a dados IoT em larga escala ainda estão a evoluir. 

A Azure Time Series Insights aborda esta necessidade do mercado, fornecendo uma solução de análise IoT chave na mão, de ponta a ponta, com uma modelação semântica rica para a contextualização de dados de séries de tempo, insights baseados em ativos e experiência de utilizador de melhor classe para descoberta, tendência, deteção de anomalias e inteligência operacional. 

Uma rica plataforma de análise operacional combinada com as nossas capacidades interativas de exploração de dados, pode utilizar a Time Series Insights para obter mais valor a partir dos dados recolhidos a partir de ativos IoT. A pré-visualização oferece suportes: 

* Solução de armazenamento em várias camadas com suporte a análises quentes e frias, proporcionando aos clientes a opção de encaminhar dados entre aquecimento e frio para análises interativas sobre dados quentes, bem como inteligência operacional ao longo de décadas de dados históricos. 

    *    Uma solução de análise quente altamente interativa para executar frequentes, e um grande número de consultas ao longo de tempo mais curto 
    *    Um lago de dados de séries de tempo escalável, performável e otimizado de custos com base no Azure Storage, permitindo aos clientes tendências de anos de séries de tempo em segundos. 

* Suporte ao modelo semântico que descreve o domínio e metadados associados aos sinais derivados e brutos de ativos e dispositivos.

* Plataforma de análise flexível para armazenar dados históricos de séries de tempo na conta de armazenamento Azure, detida pelo cliente, permitindo assim que os clientes tenham a propriedade dos seus dados IoT. Os dados são armazenados em formato Apache Parquet de código aberto que permite conectividade e interop em vários cenários de dados, incluindo análise preditiva, machine learning e outros computações personalizadas feitas usando tecnologias familiares, incluindo Spark, Databricks e Jupyter.

* Análise rica com APIs de consulta melhorada e experiência de utilizador que combina insights de dados baseados em ativos com análises de dados ricos e ad hoc com suporte para funções de interpolação, escalar e agregação, variáveis categóricas, parcelas de dispersão e sinais de séries de tempo de mudança de tempo para análise aprofundada.

*    Plataforma de nível empresarial para suportar as necessidades de escala, desempenho, segurança e fiabilidade dos nossos clientes IoT da empresa.

* Apoio à extensibilidade e integração para análises de ponta a ponta. O Time Series Insights fornece uma plataforma de análise extensível para uma variedade de cenários de dados. O conector Time Series Insights Power BI permite que os clientes levem as consultas que fazem no Time Series Insights diretamente no Power BI para obter uma visão unificada do seu BI e análise de séries de tempo num único painel de vidro.

O diagrama seguinte mostra o fluxo de dados de alto nível.

  [![Principais capacidades](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

A Azure Time Series Insights fornece um modelo de preços de pagamento escalável para processamento de dados, armazenamento (dados e metadados), e consulta, permitindo aos clientes sintonizar o seu uso de acordo com as suas exigências de negócio. 
 
Com a introdução destas principais capacidades de IoT industrial, a Time Series Insights também fornece os seguintes benefícios principais.  

| | |
| ---| ---|
| Armazenamento multicamada para dados da série de tempo à escala IoT | Com um pipeline de processamento de dados partilhado para ingerir dados, pode ingerir dados em lojas quentes e frias. Utilize uma loja quente para consultas interativas e para armazenar grandes volumes de dados. Para saber mais sobre como tirar partido de consultas baseadas em ativos de alto desempenho, consulte [consultas.](./concepts-query-overview.md) |
| Modelo de séries de tempo para contextualizar telemetria bruta e obter insights baseados em ativos | Pode utilizar o modelo da série-tempo para criar casos, hierarquias, tipos e variáveis para os seus dados de séries de tempo. Para saber mais sobre o Modelo série de tempo, consulte [o Modelo série de tempo.](./concepts-model-overview.md)  |
| Integração suave e contínua com outras soluções de dados | Data in Time Series Insights o cold store é [armazenado](concepts-storage.md) em ficheiros Apache Parquet de código aberto. Isto permite a integração de dados com outras soluções de dados, 1ª ou 3ª parte, para cenários que incluam inteligência empresarial, aprendizagem de máquinas avançada e análise preditiva. |
| Quase exploração de dados em tempo real | A experiência do utilizador [do Azure Time Series Insights Preview explorer](./time-series-insights-update-explorer.md) proporciona visualização para todo o streaming de dados através do pipeline de ingestão. Depois de ligar uma fonte de evento, pode ver, explorar e consultar dados do evento. Desta forma, pode validar se um dispositivo emite dados como esperado. Também pode monitorizar um ativo IoT para a saúde, produtividade e eficácia geral. | 
| Extensibilidade e integração | A integração do Azure Time Series Insights Power BI Connector está disponível diretamente na experiência do utilizador do Time Series Explorer através da opção **Exportação,** permitindo aos clientes exportar as consultas de séries horárias que criam na nossa experiência de utilizador diretamente no ambiente de trabalho Power BI e ver as suas tabelas de séries de tempo ao lado de outras análises bi. Isto abre a porta a uma nova classe de cenários para empresas industriais de IoT que investiram no Power BI, fornecendo um único painel de vidro sobre a análise de várias fontes de dados, incluindo séries de tempo IoT. | 
| Aplicações personalizadas construídas na plataforma Time Series Insights | O Time Series Insights suporta o [SDK JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). O SDK fornece controlos ricos e acesso simplificado a consultas. Use o SDK para construir aplicações IoT personalizadas em cima de Time Series Insights para atender às necessidades do seu negócio. Também pode utilizar as [APIs de Consulta](./concepts-query-overview.md) de Insights de Séries De Tempo diretamente para conduzir dados em aplicações IoT personalizadas. |

## <a name="next-steps"></a>Próximos passos

Começa com a antevisão da Série De Tempo do Azure:

> [!div class="nextstepaction"]
> [Guia de início rápido](./time-series-insights-update-quickstart.md)

Saiba mais sobre casos de uso:

> [!div class="nextstepaction"]
> [Casos de visualização de insights de séries de tempo Azure](./time-series-insights-update-use-cases.md)