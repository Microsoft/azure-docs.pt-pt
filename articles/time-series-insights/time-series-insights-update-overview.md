---
title: 'Visão geral: O que é a pré-visualização da Série de Tempo Azure? - Azure Time Series Insights [ Azure Time Series Insights ] Microsoft Docs'
description: Conheça as mudanças, melhorias e funcionalidades na Pré-visualização de Insights da Série de Tempo do Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 04/13/2020
ms.custom: seodec18
ms.openlocfilehash: 73244a635bbf14efcf33f1b978db14e9e2589581
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271068"
---
# <a name="what-is-azure-time-series-insights-preview"></a>O que é a Pré-visualização do Azure Time Series Insights?

A Pré-visualização da Série de Tempo Azure Insights é uma oferta de plataforma de ponta a ponta (PaaS). Pode usá-lo para recolher, processar, armazenar, analisar e consultar dados na escala Internet of Things (IoT) -- dados altamente contextuacionais e otimizados para séries temporais. 

Time Series Insights é projetado para a exploração de dados ad hoc e análise operacional. É uma oferta de serviço extensível e personalizada que satisfaz as grandes necessidades das implementações industriais de IoT.

## <a name="video"></a>Vídeo

Saiba mais sobre a Pré-visualização de Insights da Série De Tempo Azure.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="definition-of-iot-data"></a>Definição de dados ioT

Os dados industriais de IoT em organizações intensivas de ativos muitas vezes carecem de consistência estrutural devido à natureza variada de dispositivos e sensores em ambiente industrial. Os dados destes fluxos são caracterizados por lacunas significativas, por vezes mensagens corrompidas e leituras falsas. Os dados do IoT são muitas vezes significativos no contexto de entradas adicionais de dados provenientes de fontes de primeira ou de terceiros, como CRM ou ERP que acrescentam contexto para fluxos de trabalho de ponta a ponta. As inputs de fontes de dados de terceiros, como dados meteorológicos, podem ajudar a aumentar os fluxos de telemetria numa determinada instalação. 

Tudo isto implica que apenas uma fração dos dados é utilizado para fins operacionais e empresariais, e a análise requer contextualização. Os dados industriais são muitas vezes historicaizados para uma análise aprofundada ao longo de períodos de tempo mais longos para compreender e correlacionar tendências. Transformar os dados ioT recolhidos em insights exequíveis requer: 

* Processamento de dados para limpar, filtrar, interpolar, transformar e preparar dados para análise.
* Uma estrutura para navegar e compreender os dados, isto é, para normalizar e contextualizar os dados.
* Armazenamento rentável para a retenção longa ou infinita de dados processados (ou derivados) e dados brutos.

Estes dados fornecem informações consistentes, abrangentes, atuais e corretas para análise e reporte de negócios.

A imagem que se segue mostra um fluxo típico de dados ioT.

[![Fluxo de dados ioT](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-for-industrial-iot"></a>Insights da Série Relógio Azure para IoT industrial

A paisagem IoT é diversificada com clientes abrangendo uma variedade de segmentos da indústria, incluindo fabricação, automóvel, energia, utilitários, edifícios inteligentes e consultoria. Ao longo desta vasta gama de mercados industriais de IoT, as soluções nativas da nuvem que fornecem análises abrangentes direcionadas a dados ioT em larga escala continuam a evoluir. 

A Azure Time Series Insights aborda esta necessidade do mercado, fornecendo uma solução de análise IoT chave na mão, de ponta a ponta, com uma rica modelação semântica para contextualização de dados da série time, insights baseados em ativos e experiência de utilizador de melhor classe para descoberta, tendência, deteção de anomalias e inteligência operacional. 

Uma rica plataforma de análise operacional combinada com as nossas capacidades interativas de exploração de dados, pode utilizar insights da Time Series para obter mais valor a partir de dados recolhidos a partir de ativos IoT. A pré-visualização oferece suportes: 

* Solução de armazenamento com várias camadas com suporte a análise quente e frio, proporcionando aos clientes a opção de encaminhar dados entre analítica quente e fria para análiseinterativa sobre dados quentes, bem como inteligência operacional ao longo de décadas de dados históricos. 

    *    Uma solução de análise quente altamente interativa para realizar frequentes, e um grande número de consultas ao longo de dados de tempo mais curto 
    *    Um lago de dados de séries de tempo escalável, performante e custo otimizado com base no Armazenamento Azure, permitindo aos clientes tendência de dados de séries temporais em segundos. 

* Suporte de modelo semântico que descreve o domínio e metadados associados aos sinais derivados e brutos de ativos e dispositivos.

* Plataforma de análise flexível para armazenar dados de séries históricas em conta de armazenamento de tempo histórico na conta de Armazenamento Azure, permitindo assim aos clientes a propriedade dos seus dados IoT. Os dados são armazenados em formato Apache Parquet de código aberto que permite conectividade e interop através de uma variedade de cenários de dados, incluindo análisepre, machine learning e outras computações personalizadas feitas usando tecnologias familiares, incluindo Spark, Databricks e Jupyter.

* Análise rica com APIs de consulta melhorada e experiência do utilizador que combina insights de dados baseados em ativos com análise símio de dados ad hoc com suporte para funções de interpolação, esescalar e agregadas, variáveis categóricas, enredos de dispersão e sinais de séries de tempo de mudança de tempo para análise aprofundada.

*    Plataforma de grau empresarial para suportar as necessidades de escala, desempenho, segurança e fiabilidade dos nossos clientes ioT da empresa.

* Suporte de extensibility e integração para análises de ponta a ponta. Time Series Insights fornece uma plataforma de análise extensível para uma variedade de cenários de dados. O conector Time Series Insights Power BI permite que os clientes levem as consultas que fazem na Time Series Insights diretamente para o Power BI para obter uma visão unificada da sua análise bi e série stemporal num único painel de vidro.

O diagrama que se segue mostra o fluxo de dados de alto nível.

  [![Principais capacidades](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

A Azure Time Series Insights fornece um modelo de preços escaláveis para o processamento de dados, armazenamento (dados e metadados) e consulta, permitindo aos clientes afinar em função das suas exigências comerciais. 
 
Com a introdução destas principais capacidades industriais de IoT, a Time Series Insights também fornece os seguintes benefícios-chave.  

| | |
| ---| ---|
| Armazenamento em várias camadas para dados da série de tempo à escala IoT | Com um pipeline de processamento de dados partilhado para ingerir dados, pode ingerir dados em lojas quentes e frias. Utilize uma loja quente para consultas interativas e frigoríficos para armazenar grandes volumes de dados. Para saber mais sobre como aproveitar as consultas baseadas em ativos de alto desempenho, consulte [consultas](./time-series-insights-update-tsq.md). |
| Modelo de Série de Tempo para contextualizar telemetria bruta e obter insights baseados em ativos | Pode utilizar o modelo da série de tempo para criar instâncias, hierarquias, tipos e variáveis para os dados da série de tempo. Para saber mais sobre o Modelo da Série Tempo, consulte o [Modelo série de tempo.](./time-series-insights-update-tsm.md)  |
| Integração suave e contínua com outras soluções de dados | Os dados da loja de frio Time Series Insights são [armazenados](./time-series-insights-update-storage-ingress.md) em ficheiros Apache Parquet de código aberto. Isto permite a integração de dados com outras soluções de dados, 1º ou 3º partido, para cenários que incluem inteligência empresarial, machine learning avançada e análise preditiva. |
| Exploração de dados em tempo real | A experiência do utilizador do explorador de visualização de Insights [da Série De Tempo Azure](./time-series-insights-update-explorer.md) fornece visualização de todos os dados que transmitem através do pipeline de ingestão. Depois de ligar uma fonte de evento, pode ver, explorar e consultar os dados do evento. Desta forma, pode validar se um dispositivo emite dados como esperado. Também pode monitorizar um ativo IoT para a saúde, produtividade e eficácia geral. | 
| Extebilidade e integração | A integração do Conector De Power BI da Série De Tempo Azure insights está disponível diretamente na experiência do utilizador do Time Series Explorer através da opção **Export,** permitindo aos clientes exportaras as consultas de séries de tempo que criam na nossa experiência de utilizador diretamente no ambiente de trabalho do Power BI e visualizar os seus gráficos de séries de tempo ao lado de outras análises bi. Isto abre a porta a uma nova classe de cenários para empresas industriais de IoT que investiram no Power BI, fornecendo um único painel de vidro sobre a análise de várias fontes de dados, incluindo séries temporais IoT. | 
| Aplicações personalizadas construídas na plataforma Time Series Insights | Time Series Insights suporta o [JavaScript SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). O SDK fornece controlos ricos e acesso simplificado a consultas. Use o SDK para construir aplicações IoT personalizadas em cima de Time Series Insights para atender às suas necessidades de negócio. Também pode utilizar as [APIs](./time-series-insights-update-tsq.md) de consulta de Insights da Série De Tempo diretamente para conduzir dados em aplicações IoT personalizadas. |

## <a name="next-steps"></a>Passos seguintes

Inicie-se com a Pré-visualização de Insights da Série De Tempo azure:

> [!div class="nextstepaction"]
> [Guia de início rápido](./time-series-insights-update-quickstart.md)

Saiba mais sobre casos de uso:

> [!div class="nextstepaction"]
> [Casos de pré-visualização da Série de Tempo Azure Insights](./time-series-insights-update-use-cases.md)