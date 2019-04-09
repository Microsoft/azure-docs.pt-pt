---
title: Visualizando e analisando dados de registo no Azure Monitor | Documentos da Microsoft
description: Este artigo descreve como utilizar o Log Analytics no portal do Azure para criar e editar as consultas de registo no Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: bwren
ms.openlocfilehash: 0e5b9b43e528b37fd994f9131f145abadb33c53b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259036"
---
# <a name="viewing-and-analyzing-log-data-in-azure-monitor"></a>Visualizando e analisando dados de registo no Azure Monitor
O log Analytics é a experiência principal para trabalhar com dados de registo e criação de consultas no Azure Monitor. Abra o Log Analytics a partir **Logs** no **Azure Monitor** menu. Pode obter uma introdução a este portal e inspecionar seus recursos em [introdução ao Log Analytics no portal do Azure](get-started-portal.md).

O log Analytics fornece as seguintes funcionalidades para trabalhar com consultas de registo.

* Vários separadores – criar guias separados para trabalhar com várias consultas.
* Visualizações ricas – diversas opções de criação de gráficos.
* Melhor Intellisense e a linguagem de preenchimento automático.
* Realce de sintaxe – melhora a legibilidade das consultas. 
* Explorador de consultas – acesso guardado, consulta e funções.
* Esquema ver – rever a estrutura dos seus dados para ajudar a escrever consultas.
* Partilhar – criar ligações para consultas ou consultas de pin para qualquer dashboard partilhado do Azure.
* Análise do smart - identifica os picos de seus gráficos e uma análise rápida da causa.
* Seleção de coluna – Ordenar e agrupar colunas nos resultados da consulta.

> [!NOTE]
> Log Analytics tem a mesma funcionalidade que o portal da análise avançada que é uma ferramenta externa fora do portal do Azure. O portal da análise avançada ainda está disponível, mas ligações e outras referências a ele no portal do Azure estão a ser substituídas com esta nova página.

![Log Analytics](media/portals/log-analytics.png)

## <a name="resource-logs"></a>Registos de recursos
O log Analytics integra-se a vários recursos do Azure como máquinas virtuais. Isso significa que pode abrir o Log Analytics diretamente através do menu de monitorização do recurso sem mudar para o Azure Monitor e perder o contexto de recursos. **Registos** ainda não tiver sido ativada para todos os recursos do Azure, mas vão começar a aparecer no menu do portal para os recursos de diferentes tipos.

Quando abrir o Log Analytics a partir de um recurso específico, ele tem um âmbito automaticamente para criar registos apenas desse recurso.   Se quiser escrever uma consulta que inclui outros registos, em seguida, precisaria para abri-lo no menu do Azure Monitor.

As seguintes opções ainda não estão disponíveis por meio do modo de exibição do Log Analytics:

- Guardar
- Definir alerta
- Explorador de consultas
- Mudar para a área de trabalho/recursos diferentes (atualmente não planejado)


## <a name="firewall-requirements"></a>Requisitos de firewall
O browser requer acesso para os seguintes endereços para aceder ao Log Analytics.  Se o browser estiver acessando o portal do Azure através de uma firewall, tem de ativar o acesso a estes endereços.

| URI | IP | Portas |
|:---|:---|:---|
| portal.loganalytics.io | Dinâmico | 80,443 |
| api.loganalytics.io    | Dinâmico | 80,443 |
| docs.loganalytics.io   | Dinâmico | 80,443 |


## <a name="next-steps"></a>Passos Seguintes

- Percorrer um [tutorial sobre como utilizar o Log Analytics](../../azure-monitor/log-query/get-started-portal.md).
- Percorrer um [tutorial sobre como utilizar a pesquisa de registos](../../azure-monitor/learn/tutorial-viewdata.md).

