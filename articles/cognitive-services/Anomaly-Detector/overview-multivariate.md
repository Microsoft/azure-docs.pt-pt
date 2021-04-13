---
title: O que é o Detetor de Anomalias Multivariate API?
titleSuffix: Azure Cognitive Services
description: Visão geral do novo Detetor de Anomalias pré-visualização de APIs multivariados.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: deteção de anomalias, aprendizagem automática, algoritmos
ms.openlocfilehash: 63ad63cd57dce5f503e317e8c6330dca0325ba05
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315667"
---
# <a name="multivariate-time-series-anomaly-detection-public-preview"></a>Deteção de anomalias em séries multivariadas (visualização pública)

A primeira versão do Detetor de Anomalias de Serviços Cognitivos Azure permitiu-lhe construir soluções de monitorização de métricas utilizando as APIs da [série de tempo univariado](overview.md)de fácil utilização . Ao permitir a análise das séries de tempo individualmente, o detetor de anomalias univariado proporciona simplicidade e escalabilidade.

As novas APIs **de deteção de anomalias multivariadas** permitem ainda aos desenvolvedores integrar facilmente a IA avançada para detetar anomalias de grupos de métricas, sem necessidade de conhecimentos de aprendizagem automática ou dados rotulados. As dependências e inter-correlações entre até 300 sinais diferentes são agora automaticamente contadas como factores-chave. Esta nova capacidade ajuda-o a proteger proativamente os seus sistemas complexos, tais como aplicações de software, servidores, máquinas de fábrica, naves espaciais ou até mesmo o seu negócio, contra falhas.

Imagine 20 sensores de um motor automático que gera 20 sinais diferentes como vibração, temperatura, pressão do combustível, etc. As leituras desses sinais individualmente podem não lhe dizer muito sobre questões de nível do sistema, mas juntos podem representar a saúde do motor. Quando a interação desses sinais se desvia fora do alcance habitual, a característica de deteção de anomalias multivariadas pode sentir a anomalia como um perito experiente. Os modelos de IA subjacentes são treinados e personalizados usando os seus dados de forma a entender as necessidades únicas do seu negócio. Com as novas APIs no Detetor de Anomalias, os desenvolvedores podem agora integrar facilmente as capacidades de deteção de anomalias de séries de tempo multivariadas em soluções de manutenção preditiva, soluções de monitorização de AIOps para software empresarial complexo ou ferramentas de inteligência empresarial.

## <a name="when-to-use-multivariate-versus-univariate"></a>Quando usar **multivariado** versus **univariado**

Utilize APIs de deteção de anomalias univariadas, se o seu objetivo é detetar anomalias a partir de um padrão normal em cada série de tempo individual puramente com base nos seus próprios dados históricos. Exemplos: pretende detetar anomalias diárias de receitas com base em dados de receitas em si, ou pretende detetar um pico de CPU exclusivamente com base em dados do CPU.
- `POST /anomalydetector/v1.0/timeseries/last/detect`
- `POST /anomalydetector/v1.0/timeseries/batch/detect`
- `POST /anomalydetector/v1.0/timeseries/changepoint/detect`

![Gráfico de linha de séries de tempo com valores flutuantes de uma única variável capturado por uma linha azul com anomalias identificadas por círculos laranja](./media/anomaly_detection2.png)

Utilize APIs de deteção de anomalias multivariadas abaixo, se o seu objetivo é detetar anomalias de nível do sistema a partir de um grupo de dados da série de tempo. Particularmente, quando qualquer série de tempo individual não lhe dirá muito, e você tem que olhar para todos os sinais (um grupo de séries tempormais) holisticamente para determinar um problema de nível do sistema. Exemplo: tem um ativo físico caro como aeronaves, equipamento numa plataforma petrolífera ou um satélite. Cada um destes ativos tem dezenas ou centenas de diferentes tipos de sensores. Teria de olhar para todos os sinais das séries temporinhas desses sensores para decidir se existe problema de nível do sistema.

- `POST /anomalydetector/v1.1-preview/multivariate/models`
- `GET /anomalydetector/v1.1-preview/multivariate/models[?$skip][&$top]`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `POST/anomalydetector/v1.1-preview/multivariate/models/{modelId}/detect`
- `GET /anomalydetector/v1.1-preview/multivariate/results/{resultId}`
- `DELETE /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}/export`

![Gráficos de linha de séries de tempo múltiplas para variáveis de: vibração, temperatura, pressão, velocidade, velocidade de rotação com anomalias destacadas em laranja](./media/multivariate-graph.png)

## <a name="region-support"></a>Suporte de região

A pré-visualização pública do Multivariato de Detetor de Anomalias está atualmente disponível em três regiões: West US2, East US2 e West Europe.

## <a name="algorithms"></a>Algoritmos

- [Deteção de anomalias de séries de tempo multivariadas através da Rede de Atenção de Gráficos](https://arxiv.org/abs/2009.02040)

## <a name="join-the-anomaly-detector-community"></a>Juntar-se à comunidade do Detetor de Anomalias

- Junte-se ao [grupo de detetores de anomalias em equipas da Microsoft](https://aka.ms/AdAdvisorsJoin)

## <a name="next-steps"></a>Passos seguintes

- [Quickstarts](./quickstarts/client-libraries-multivariate.md).
- [Boas Práticas](./concepts/best-practices-multivariate.md): Este artigo é sobre padrões recomendados para usar com as APIs multivariadas.
