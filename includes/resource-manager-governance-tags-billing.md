---
title: incluir ficheiro
description: incluir ficheiro
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 34388fe975df359c695b9358cafcd3ae711af0cf
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98901145"
---
Depois de aplicar etiquetas a recursos, pode ver os custos dos recursos com essas etiquetas. Demora algum tempo para a análise de custos mostrar a utilização mais recente, pelo que poderá ainda não ver os custos. Quando os custos estão disponíveis, pode ver os custos dos recursos entre os grupos de recursos na sua subscrição. Os utilizadores devem ter [acesso ao nível de subscrição das informações de faturação](../articles/cost-management-billing/manage/manage-billing-access.md) para ver os custos.

Para ver os custos por etiqueta no portal, selecione a sua subscrição e selecione **Análise de Custos**.

![Análise de custos](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

Em seguida, filtre por valor da etiqueta e selecione **Aplicar**.

![Ver custo por etiqueta](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

Também pode utilizar a visão geral da [API de consumo Azure](../articles/cost-management-billing/manage/consumption-api-overview.md) para visualizar programáticamente os custos.
