---
title: Noções básicas sobre métricas para o Azure Spring Cloud
description: Saiba como examinar as métricas na nuvem do Azure Spring
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d9c6f14b7ecfc2929dc48c11e0df1fe80303c8b1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039127"
---
# <a name="metrics-for-azure-spring-cloud"></a>Métricas para o Azure Spring Cloud

Azure Monitor métricas Explorer é um componente do portal do Microsoft Azure que permite plotar gráficos, correlacionar visualmente tendências e investigar picos e quedas em métricas. Use o Metrics Explorer para investigar a integridade e a utilização de seus recursos. No Azure Spring Cloud, oferecemos duas opções para a exibição de métricas: gráficos na página **visão geral do aplicativo** e a página métricas de nível de serviço.

## <a name="application-overview-page"></a>Página Visão geral do aplicativo

A página **visão geral do aplicativo** de cada aplicativo apresenta um gráfico de métricas que permite que você execute uma verificação rápida de status do seu aplicativo.  Acesse a página do serviço de nuvem do Azure Spring e selecione **painel do aplicativo**e, em seguida, selecione um aplicativo na lista.  

Fornecemos 5 gráficos com métricas atualizadas a cada minuto para o seguinte:

* **Erros do servidor http**: Contagem de erros para solicitações HTTP para seu aplicativo.
* **Dados em**: Bytes recebidos pelo seu aplicativo.
* **Saída de dados**: Bytes enviados ao seu aplicativo.
* **Solicitações**: Solicitações recebidas pelo seu aplicativo.
* **Tempo médio de resposta**: Tempo médio de resposta do seu aplicativo.

Você pode selecionar um intervalo de tempo para o gráfico entre 1 hora e até 7 dias.

## <a name="service-level-metric-queries"></a>Consultas de métrica de nível de serviço

O Azure Spring Cloud permite que você monitore uma variedade de métricas de aplicativo. Examine o [guia para introdução](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) às métricas de Azure monitor para saber mais sobre esse serviço.

Para examinar os dados de métrica, você selecionará sua métrica, sua **agregação**e seu intervalo de tempo.  Esses conceitos são explicados abaixo.

### <a name="aggregation"></a>Agregação 

O Azure sonda e atualiza as métricas a cada minuto. O Azure fornece três maneiras de agregar dados por um período de tempo escolhido:

* **Total**: Some todas as métricas como saída de destino.
* **Média**: Use o valor médio no período como saída de destino.
* **Máx./mín**.: Use o valor max/min no período como saída de destino.

### <a name="time-range"></a>Intervalo de tempo

Selecione um intervalo de tempo padrão ou defina o seu próprio.

### <a name="modifying-the-granularity-of-your-metric-query"></a>Modificando a granularidade da sua consulta de métricas

Por padrão, o Azure agrega métricas para todos os aplicativos de um serviço de nuvem do Azure Spring. Para examinar as métricas no nível do aplicativo ou da instância, use a função de filtro.  
Selecione **Adicionar filtro**, defina a propriedade como **aplicativo** e selecione o aplicativo de destino que você deseja monitorar. Opcionalmente, use a opção **aplicar divisão** para desenhar linhas separadas para cada aplicativo no gráfico.

>[!TIP]
> Você pode criar seus próprios gráficos na página de métricas e fixá-los em seu **painel**. Comece nomeando seu gráfico.  Em seguida, selecione **fixar no painel no canto superior direito**. Agora você pode verificar seu aplicativo no **painel**do Portal.