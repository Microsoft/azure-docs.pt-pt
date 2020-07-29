---
title: Gerencie melhor o seu SOC com métricas de incidentes em Azure Sentinel Microsoft Docs
description: Utilize informações do ecrã e do livro de métricas de incidentes do Azure Sentinel para o ajudar a gerir o seu Centro de Operações de Segurança (SOC).
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2020
ms.author: yelevin
ms.openlocfilehash: f14b0050aefc598d26dec7a7781a3378ccaa7570
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294506"
---
# <a name="manage-your-soc-better-with-incident-metrics"></a>Gerir melhor o SOC com métricas de incidentes

> [!IMPORTANT]
> As características das métricas do incidente estão atualmente em visualização pública.
> Estas características são fornecidas sem um contrato de nível de serviço, e não são recomendadas para cargas de trabalho de produção.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Como gestor do Centro de Operações de Segurança (SOC), é necessário ter métricas e medidas de eficiência global na ponta dos dedos para avaliar o desempenho da sua equipa. Você vai querer ver operações de incidentes ao longo do tempo por muitos critérios diferentes, como gravidade, táticas MITRE, tempo médio para triagem, tempo médio para resolver, e muito mais. O Azure Sentinel disponibiliza-lhe agora estes dados com a nova tabela e esquema **securityIncident** no Log Analytics e o livro de **operações de segurança** que o acompanha. Poderá visualizar o desempenho da sua equipa ao longo do tempo e utilizar esta perspicácia para melhorar a eficiência. Também pode escrever e usar as suas próprias consultas de KQL contra a tabela de incidentes para criar livros personalizados que se adaptem às suas necessidades específicas de auditoria e KPI's.

## <a name="use-the-security-incidents-table"></a>Use a mesa de incidentes de segurança

A tabela **SecurityIncident** é incorporada em Azure Sentinel. Você vai encontrá-lo com as outras tabelas na coleção **SecurityInsights** em **Logs**. Pode consultar como qualquer outra tabela no Log Analytics.

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incident-table.png" alt-text="Tabela de incidentes de segurança":::

Sempre que criar ou atualizar um incidente, será adicionada uma nova entrada de registo à tabela. Isto permite-lhe rastrear as alterações feitas aos incidentes, e permite métricas SOC ainda mais potentes, mas você precisa estar atento a isso ao construir consultas para esta tabela, pois você pode precisar remover entradas duplicadas para um incidente (dependendo da consulta exata que você está executando). 

Por exemplo, se quiser devolver uma lista de todos os incidentes classificados pelo seu número de incidentes, mas apenas quiser devolver o registo mais recente por incidente, pode fazê-lo utilizando o [operador de resumo](https://docs.microsoft.com/azure/data-explorer/kusto/query/summarizeoperator) KQL com a `arg_max()` [função de agregação:](https://docs.microsoft.com/azure/data-explorer/kusto/query/arg-max-aggfunction)

`SecurityIncident` <br>
`| summarize arg_max(LastModifiedTime, *) by IncidentNumber`

## <a name="security-operations-efficiency-workbook"></a>Livro de eficiência de operações de segurança

Para complementar a tabela **SecurityIncidents,** fornecemos-lhe um modelo de livro de **segurança** fora da caixa que pode usar para monitorizar as suas operações SOC. O livro contém as seguintes métricas: 
- Incidente criado ao longo do tempo 
- Incidentes criados por classificação, gravidade, proprietário e estatuto 
- Tempo médio para triagem 
- Tempo médio para encerrar 
- Incidentes criados pela severidade, proprietário, estatuto, produto e táticas ao longo do tempo 
- Hora de triagem percentiles 
- Hora de fechar percentiles 
- Tempo médio para triagem por proprietário 
- Atividades recentes 
- Classificações finais recentes  

Pode encontrar este novo modelo de livro, escolhendo livros de **trabalho** do menu de navegação Azure Sentinel e selecionando o separador **Modelos.** Escolha a eficiência das **operações** de Segurança na galeria e clique num dos botões de **modelo View Saved Workbook** e **View.**

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incidents-workbooks-gallery.png" alt-text="Galeria de livros de incidentes de segurança":::

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-operations-workbook-1.png" alt-text="Livro de incidentes de segurança completo":::

Pode utilizar o modelo para criar os seus próprios livros personalizados adaptados às suas necessidades específicas.

## <a name="securityincidents-schema"></a>Esquema de SegurançaIncidents

[!INCLUDE [SecurityIncidents schema](../../includes/sentinel-schema-security-incident.md)]

## <a name="next-steps"></a>Passos seguintes

- Para começar com o Azure Sentinel, precisa de uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).
- Aprenda a [embarcar os seus dados ao Azure Sentinel](quickstart-onboard.md)e obtenha [visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
