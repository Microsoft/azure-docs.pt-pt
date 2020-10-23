---
title: Opções de agrupamento e filtragem no Azure Cost Management
description: Este artigo explica como utilizar as opções de agrupamento e filtragem no Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 06/08/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: ac9828ca61009eb3ee39412169b2b454b9ecbd00
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131875"
---
# <a name="group-and-filter-options-in-cost-analysis"></a>Opções de agrupamento e filtragem na Análise de custos

A análise de custos tem muitas opções de agrupamento e filtragem. Este artigo ajuda a compreender quando as deve utilizar.

Para ver um vídeo sobre opções de agrupamento e filtragem, veja o vídeo [Cost Management reporting by dimensions and tags](https://www.youtube.com/watch?v=2Vx7V17zbmk) (Relatórios do Cost Management por dimensões e etiquetas). Para ver outros vídeos, visite o canal do YouTube [Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/2Vx7V17zbmk]

## <a name="group-and-filter-properties"></a>Propriedades de agrupamento e filtragem

A tabela seguinte lista algumas das opções de agrupamento e filtragem mais comuns disponíveis na Análise de custos e quando deverão ser utilizadas.

| Propriedade | Quando utilizar | Notas |
| --- | --- | --- |
| **Zonas de disponibilidade** | Divida os custos do AWS por zona de disponibilidade. | Aplicável apenas a âmbitos e grupos de gestão do AWS. Os dados do Azure não incluem a zona de disponibilidade e serão mostrados como **Não aplicável**. |
| **Período de faturação** | Separe os custos PAYG pelo mês em que foram, ou serão, faturados. | Utilize o **Período de faturação** para obter uma representação fiel dos custos PAYG faturados. Inclua dois dias extra antes e depois do período de faturação se estiver a filtrar por um intervalo de datas personalizado. Se se limitar às datas do período de faturação exatas, não obterá a correspondência à fatura. Serão mostrados os custos de todas as faturas no período de faturação. Utilize o **ID da fatura** para filtrar por uma fatura específica. Aplicável apenas a subscrições PAYG porque o EA e o MCA são faturados por meses do calendário. As contas EA/MCA podem utilizar os meses do calendário no seletor de datas ou na granularidade mensal para conseguir o mesmo objetivo. |
| **Tipo de custo** | Divida os custos de utilização, de compras, de reembolsos e de reservas não utilizadas. | As compras de reservas e os reembolsos estão disponíveis apenas quando utiliza os custos reais e não quando utiliza os custos amortizados. Os custos de reservas não utilizadas estão disponíveis apenas quando consulta os custos amortizados. |
| **Departamento** | Divida os custos por departamento do EA. | Disponível apenas para o EA e os grupos de gestão. As subscrições PAYG não têm departamentos e aparecerão como **Não aplicável** ou **Não atribuída**. |
| **Conta de inscrição** | Divida os custos por proprietário de conta EA. | Disponível apenas para contas de faturação, departamentos e grupos de gestão do EA. As subscrições PAYG não têm contas de inscrição do EA e serão mostradas como **Não aplicável** ou **Não atribuída**. |
| **Frequência** | Divida os custos por custos baseados na utilização, nos custos únicos e nos custos recorrentes. | |
| **ID da fatura** | Divida os custos por fatura. | Os custos não faturados ainda não têm um ID da fatura e os custos do EA não incluem os detalhes da fatura e serão mostrados como **Não aplicável**.  |
| **Medidor** | Divida os custos por medidor de utilização. | As compras e a utilização do Marketplace serão mostradas como **Não aplicável**. Veja o **Tipo de custo** para identificar as compras e o **Tipo de editor** para identificar os custos do Marketplace. |
| **Operação** | Divida os custos do AWS por operação. | Aplicável apenas a âmbitos e grupos de gestão do AWS. Os dados do Azure não incluem a operação e serão mostrados como **Não aplicável**. Como alternativa, utilize o **Medidor**. |
| **Modelo de preços** | Separe os custos por a pedido, reserva ou utilização pontual. | As compras são mostradas como **OnDemand**. Se vir **Não aplicável**, agrupe por **Reserva** para determinar se é uma utilização de reserva ou a pedido e por **Tipo de custo** para identificar as compras.
| **Fornecedor** | Divida os custos por AWS e Azure. | Disponível apenas para grupos de gestão. |
| **Tipo de editor** | Divide os custos do AWS, do Azure e do Marketplace. |  |
| **Reserva** | Divida os custos por reserva. | Qualquer utilização ou compra que não esteja associada à reserva será mostrada como **Não aplicável**. Agrupe por **Tipo de editor** para identificar outras compras do Azure, do AWS ou do Marketplace. |
| **Recurso** | Divida os custos por recurso. | As compras são mostradas como **Não aplicável** porque são aplicadas numa conta de faturação EA/PAYG ou num nível de perfil de faturação MCA e não são associadas a um recurso específico. Agrupe por **Tipo de editor** para identificar outras compras do Azure, do AWS ou do Marketplace. |
| **Grupo de recursos** | Divida os custos por grupo de recursos. | As compras, os recursos de inquilinos não associados a subscrições, os recursos da subscrição não implementados num grupo de recursos e os recursos clássicos não têm um grupo de recursos e serão mostrados como **outros**, **serviços clássicos**, **$system** ou **Não aplicável**. |
| **Tipo de recurso** | Divida os custos por tipo de recurso. | As compras e os serviços clássicos não têm nenhum tipo de recurso do Azure Resource Manager e serão mostrados como **outros**, **serviços clássicos** ou **Não aplicável**. |
| **Localização do recurso** | Divida os custos por localização ou região. | As compras e a utilização do Marketplace podem ser mostradas como **não atribuída**, **desconhecida**, **não mapeada** ou **Não aplicável**. |
| **Nome do serviço** ou **Categoria do medidor** | Divida os custos por serviço do Azure. | As compras e a utilização do Marketplace serão mostradas como **Não aplicável** ou **não atribuída**. |
| **Escalão de serviço** ou **Subcategoria de medidor** | Divida os custos por subclassificação do medidor de utilização do Azure. | As compras e a utilização do Marketplace serão mostradas como **Não aplicável** ou **não atribuída**. |
| **Subscrição** | Divida os custos por subscrição do Azure e por conta associada do AWS. | As compras e os recursos de inquilinos podem ser mostrados como **Não aplicável**. |
| **Tag** | Divida os custos por valores de etiqueta para uma chave de etiqueta específica. | As etiquetas não estão disponíveis para: compras, recursos de inquilinos não associados a subscrições, recursos da subscrição não implementados num grupo de recursos nem recursos clássicos. Alguns serviços não incluem etiquetas nos dados de utilização. Saiba mais sobre o [suporte de etiquetas para cada tipo de recurso](../../azure-resource-manager/management/tag-support.md). |

Para obter mais informações sobre os termos, veja [Compreender os termos utilizados no ficheiro de utilização e de custos do Azure](../understand/understand-usage.md).

## <a name="next-steps"></a>Passos seguintes

- [Começar a analisar os custos](./quick-acm-cost-analysis.md).