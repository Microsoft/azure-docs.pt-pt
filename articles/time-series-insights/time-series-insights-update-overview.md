---
title: 'Descrição geral: Pré-visualização do Azure Time Series Insights | Documentos da Microsoft'
description: Descrição geral do Time Series Insights pré-visualização do Azure.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.workload: big-data
ms.topic: overview
ms.date: 12/05/2018
ms.custom: seodec18
ms.openlocfilehash: 7f8731f48b96ccbe0e77970af1ccefa1d7a7155f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438776"
---
# <a name="azure-time-series-insights-preview-overview"></a>Descrição geral de pré-visualização do Time Series Insights do Azure

Pré-visualização de informações de série de tempo do Azure é uma oferta de plataforma-como-serviço de ponto-a-ponto. É utilizado para ingerir, processar, armazenar e consultar contextualizados altamente otimizado de série de tempo, dados de IoT à escala. O Time Series Insights é ideal para exploração de dados ad hoc e análise operacional. O Time Series Insights é um serviço de forma exclusiva extensível e personalizado oferta que satisfazem amplo precisa de implementações de IoT industriais.

## <a name="video"></a>Vídeo

Neste vídeo, fornecemos uma visão geral de pré-visualização de informações de série de tempo do Azure, uma plataforma de análise de IoT com base na cloud.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="define-iot-data"></a>Definir os dados de IoT

Os dados de IoT são todos os dados "industriais" que estão disponíveis em organizações com muitos elemento. Dados de IoT, muitas vezes, são altamente estruturados porque elas enviadas por ativos que registam medidas bastante ruidosos. Estas medidas incluem a temperatura e humidade motion. Estes fluxos de dados com frequência são caracterizados por intervalos significativos, mensagens de danificada e leituras de falsas. Dados a partir destes fluxos de tem ser limpos antes de ocorre qualquer análise. Os dados de IoT, muitas vezes, são significativos apenas no contexto de entradas de dados adicionais que provenientes de fontes originais, como CRM ou ERP. Também entradas provenientes de origens de dados de terceiros, como clima ou localização.

Como resultado, apenas uma fração dos dados, é utilizada para fins operacionais e empresariais. Esses dados fornecem informações consistentes, abrangentes, atuais e corretas para análise e relatórios de negócios. Ativar recolhidos os dados em informações acionáveis precisam de IoT:

* Processamento de dados para limpar, filtrar, fazer a interpolação, transformar e preparar dados para análise.
* Uma estrutura para navegar e compreender os dados, ou seja, para normalizar e a contextualizar os dados.
* Armazenamento económico para o período de retenção de longo ou infinito de valor de processados ou derivadas dos décadas, dados e dados não processados.

Um fluxo de dados de IoT típico é mostrado na imagem seguinte.

  ![Fluxo de dados de IoT][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>O Azure Time Series Insights para o industrial IoT

O atual cenário de IoT é diversificado. Os clientes abrangem o fabrico, automóvel, energia, utilitários, edifícios inteligentes e setores de consultoria. Os cenários incluem a exploração de dados ad hoc em que a forma dos dados é desconhecida. Os cenários incluem também análise operacional sobre dados esquematizados ou modelados explicitamente, a eficiência operacional da unidade. Esses cenários normalmente existem lado a lado e oferecer suporte a diferentes casos de utilização. Capacidades da plataforma que são fundamentais para o sucesso das empresas de IoT industriais e seus revolução digital incluem:

- Armazenamento em várias camadas, semiativos e amovíveis. 
- A capacidade de armazenar décadas de dados de séries de tempo. 
- A capacidade de explicitamente modelar e otimizar as consultas para asset com base em informações operacionais.

O Time Series Insights é uma abrangente e ponto-a-ponto plataforma-como-serviço oferta para exploração de dados de IoT e das informações operacionais. O Time Series Insights oferece um serviço cloud totalmente gerido para análise de dados de séries de tempo de escala de IoT.

Pode armazenar dados não processados num arquivo de esquema, dentro da memória. Pode, em seguida, executar consultas ad hoc interativas através de um motor de consulta distribuída e a API. Tornar a utilização de experiência de usuário avançada para visualizar milhares de milhões de eventos em segundos. Saiba mais sobre o [capacidades de exploração de dados](./time-series-insights-overview.md).

O Time Series Insights também oferece recursos de informações operacionais atualmente em pré-visualização. Em conjunto com a exploração interativa de dados e informações operacionais, pode usar o Time Series Insights para obter mais valor dos dados recolhidos a partir de recursos de IoT. Pré-visualização de oferta suporta:

* A armazenam dimensionável, desempenho e dados de séries temporais com otimização de custos. Esta solução de IoT baseado na nuvem pode de tendência valor de anos de dados de séries de tempo em segundos.
* Suporte de modelo semântico que descreve o domínio e os metadados associados com os sinais derivados e não derivada de ativos e dispositivos.
* Experiência do usuário que combina informações de dados com base no ativo com a análise de dados sofisticado e ad hoc. Esta combinação de unidades de negócios e informações operacionais.
* Integração com ferramentas de análise e aprendizagem automática avançada. As ferramentas incluem o Azure Databricks, Apache Spark, Azure Machine Learning, blocos de notas do Jupyter e Power BI. Essas ferramentas ajudam a enfrentar desafios de dados de séries de tempo e impulsionar a eficiência operacional.

As informações operacionais, em conjunto e exploração de dados são oferecidas com um modelo de preços pay as you go simple para processamento de dados, armazenamento e a consulta. Este modelo de faturação é adequado para suas necessidades empresariais em evolução.

Este diagrama de fluxo de dados de alto nível mostra as atualizações.

  ![Principais capacidades][2]

Com a introdução dessas capacidades essenciais de IoT industriais, o Time Series Insights fornece as seguintes grandes vantagens.

| | |
| ---| ---|
| **Em várias camadas de armazenamento para dados de séries de tempo de escala de IoT** | Com um pipeline de processamento de dados comuns para a ingestão de dados, pode armazenar dados no armazenamento de acesso pouco frequente para consultas interativas. Também pode armazenar dados no armazenamento de esporádico para grandes volumes de dados. Tire partido de alto desempenho com base em asset [consultas](./time-series-insights-update-tsq.md). |
| **Modelo de série de tempo para contextualizar os telemetria não processados e obter informações com base no recurso** | Contextualizar os dados de telemetria não processados com o descritivo [modelo de série de tempo](./time-series-insights-update-tsm.md). Derive as informações operacionais avançada com otimização de desempenho e custo altamente consultas com base no dispositivo. |
| **Integração uniforme e contínua com outras soluções de dados** |  Os dados no Time Series Insights são [armazenados](./time-series-insights-update-storage-ingress.md) nos ficheiros de Apache Parquet aberto. Esta integração com outras soluções de dados, se o primeiro ou de terceiros, é mais fácil para os cenários de ponto-a-ponto. Estes cenários incluem a business intelligence, avançada de machine learning e Análise Preditiva. |
| **Perto de exploração de dados em tempo real** | O [Explorador de pré-visualização do Azure Time Series Insights](./time-series-insights-update-explorer.md) experiência de utilizador fornece a visualização para todos os dados de transmissão em fluxo através do pipeline de ingestão. Pouco tempo depois de ligar uma origem de eventos, pode ver, explorar e consultar dados de eventos. Dessa forma, pode validar se um dispositivo emite dados conforme esperado. Também pode monitorizar um recurso de IoT para o estado de funcionamento, produtividade e eficácia global. |
| **Causa análise e deteção de anomalias** | O [Explorador de pré-visualização do Azure Time Series Insights](./time-series-insights-update-explorer.md) suporta vistas padrão e o ponto de vista para realizar e salvar várias etapas, causa analysis. Em combinação com o Azure Stream Analytics, pode utilizar o Time Series Insights para detetar anomalias e alertas em tempo real. |
| **Aplicativos personalizados baseados na plataforma do Time Series Insights** | Time Series Insights suporta o [JavaScript SDK](./tutorial-explore-js-client-lib.md). O SDK fornece controles avançados e acesso simplificado às consultas. Utilize o SDK para criar aplicações IoT personalizadas com base no Time Series Insights para se adequar às suas necessidades empresariais específicas. Também pode utilizar o Time Series Insights [APIs de consulta](./time-series-insights-update-tsq.md) diretamente aos dados da unidade em aplicações de IoT personalizadas. |

## <a name="next-steps"></a>Passos Seguintes

Comece com a pré-visualização do Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Leia o guia de início rápido](./time-series-insights-update-quickstart.md)

Saiba mais sobre casos de utilização:

> [!div class="nextstepaction"]
> [Casos de utilização de pré-visualização do Time Series Insights do Azure](./time-series-insights-update-use-cases.md)

<!-- Images -->
[1]: media/v2-update-overview/overview_one.png
[2]: media/v2-update-overview/overview_two.png