---
title: Autoescala em Azure usando uma métrica personalizada
description: Aprenda a escalar o seu recurso por métrica personalizada em Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 8e744e6a91eb6fbe23a6b45f95c39b1acfdcb61f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617711"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Começar com escala automática por métrica personalizada em Azure
Este artigo descreve como escalar o seu recurso por uma métrica personalizada no portal Azure.

A autoescala do Azure Monitor aplica-se apenas a [conjuntos de escala de máquinas virtuais,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [serviços de nuvem,](https://azure.microsoft.com/services/cloud-services/) [serviço de aplicações - Web Apps](https://azure.microsoft.com/services/app-service/web/), [Azure Data Explorer Cluster](https://azure.microsoft.com/services/data-explorer/) ,   
Serviços de Integração Ambiente e [Gestão de API.](../../api-management/api-management-key-concepts.md)

## <a name="lets-get-started"></a>Vamos começar
Este artigo assume que tem uma aplicação web com insights de aplicações configurados. Se ainda não tiver um, pode [configurar o Application Insights para o seu website ASP.NET][1]

- Portal Open [Azure][2]
- Clique no ícone Azure Monitor no painel de navegação esquerdo.
  ![Launch Azure Monitor][3]
- Clique na definição de Autoscale para ver todos os recursos para os quais a escala automática é aplicável, juntamente com o seu estado de autoescala atual ![ Descubra a escala automática no monitor Azure][4]
- Abra a lâmina 'Autoscale' no Azure Monitor e selecione um recurso que pretende escalar
  > Nota: Os passos abaixo utilizam um plano de serviço de aplicações associado a uma aplicação web que tem insights de aplicações configurados.
- Na lâmina de regulação da balança para o recurso, note que a contagem de instâncias atuais é 1. Clique em 'Ativar a autoescala'.
  ![Definição de escala para nova aplicação web][5]
- Forneça um nome para a definição de escala e clique em "Adicionar uma regra". Note as opções de regra de escala que se abrem como um painel de contexto no lado direito. Por predefinição, define a opção de escalar a contagem de exemplos em 1 se a percentagem de CPU do recurso exceder 70%. Altere a fonte métrica no topo para "Application Insights", selecione o recurso de insights de aplicação no dropdown 'Resource' e, em seguida, selecione a métrica personalizada com base na qual pretende escalar.
  ![Escala por métrica personalizada][6]
- Semelhante ao degrau acima, adicione uma regra de escala que irá escalar e diminuir a contagem de escala em 1 se a métrica personalizada estiver abaixo de um limiar.
  ![Escala à base de cpu][7]
- Estabeleça os limites de instância. Por exemplo, se pretender escalar entre 2 a 5 instâncias dependendo das flutuações métricas personalizadas, de definir 'mínimo' para '2', 'máximo' para '5' e 'predefinido' para '2'
  > Nota: No caso de haver um problema de leitura das métricas de recursos e a capacidade atual está abaixo da capacidade padrão, e depois para garantir a disponibilidade do recurso, a Autoscale irá escalar para o valor padrão. Se a capacidade atual já for superior à capacidade padrão, a Autoscale não se escalará.
- Clique em 'Guardar'

Parabéns! Criou agora com sucesso a sua definição de escala para escalar automaticamente a sua aplicação web com base numa métrica personalizada.

> Nota: Os mesmos passos são aplicáveis para começar com uma função de VMSS ou serviço de nuvem.

<!--Reference-->
[1]: ../app/asp-net.md
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png
