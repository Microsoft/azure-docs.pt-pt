---
title: Escala automática em Azure usando uma métrica personalizada
description: Aprenda a escalar o seu recurso por métrica personalizada em Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 7758c440c75af5819099110dcbdaf5a86a1d2a04
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425124"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Começar com escala de automóvel por métrica personalizada em Azure
Este artigo descreve como escalar o seu recurso por uma métrica personalizada no portal Azure.

A escala automática Do Monitor Azure aplica-se apenas a conjuntos de escala de [máquinas virtuais, serviços](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [de nuvem,](https://azure.microsoft.com/services/cloud-services/) [serviço de aplicações - Aplicações Web,](https://azure.microsoft.com/services/app-service/web/)Cluster do Explorador de [Dados Azure,](https://azure.microsoft.com/services/data-explorer/)   
Serviço de Integração Ambiente e [Serviços de Gestão aPI.](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)

## <a name="lets-get-started"></a>Vamos começar
Este artigo assume que tem uma aplicação web com insights de aplicação configurados. Se ainda não tiver uma, pode configurar Insights de [Aplicação para o seu site ASP.NET][1]

- Portal [Open Azure][2]
- Clique no ícone Do Monitor Azure no painel de navegação esquerdo.
  ![lançamento do Monitor Azure][3]
- Clique na definição de escala automática para ver todos os recursos para os quais a escala de automóvel é aplicável, juntamente com o seu estado de escala automática atual ![Descubra a escala automática no monitor Azure][4]
- Abra a lâmina 'Autoscale' no Monitor Azure e selecione um recurso que pretende escalar
  > Nota: Os passos abaixo utilizam um plano de serviço de aplicações associado a uma aplicação web que tem insights de aplicação configurados.
- Na lâmina de regulação da escala para o recurso, note que a contagem de instâncias atuais é de 1. Clique em 'Ativar a escala automática'.
  Definição de escala ![para nova aplicação web][5]
- Forneça um nome para a definição de escala e clique em "Adicionar uma regra". Note as opções de regra de escala que se abrem como um painel de contexto no lado direito. Por padrão, define a opção de escalar a sua contagem de instâncias em 1 se a percentagem de CPU do recurso exceder 70%. Mude a fonte métrica no topo para "Application Insights", selecione o recurso de insights da aplicação no dropdown 'Resource' e, em seguida, selecione a métrica personalizada com base na qual pretende escalar.
  Escala ![por][6] métrica personalizada
- Semelhante ao passo acima, adicione uma regra de escala que escalará e diminuirá a contagem de escala em 1 se a métrica personalizada estiver abaixo de um limiar.
  Escala ![com base no][7] do CPU
- Estabeleça os limites de instância. Por exemplo, se pretender escalar entre 2 a 5 instâncias dependendo das flutuações métricas personalizadas, defino 'mínimo' para '2', 'máximo' para '5' e 'padrão' para '2'
  > Nota: Caso exista um problema na leitura das métricas de recursos e a capacidade atual está abaixo da capacidade padrão, para garantir a disponibilidade do recurso, a escala automática irá dimensionar para o valor padrão. Se a capacidade atual já for superior à capacidade padrão, a escala automática não entrará em escala.
- Clique em 'Guardar'

Parabéns! Criou agora com sucesso a sua definição de escala para escalar automaticamente a sua aplicação web com base numa métrica personalizada.

> Nota: Os mesmos passos são aplicáveis para começar com uma função de vMSS ou serviço na nuvem.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png

