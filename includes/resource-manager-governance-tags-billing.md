---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 226079b1970508004c55c54cd3de6d2e9475aa08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75970140"
---
Depois de aplicar etiquetas a recursos, pode ver os custos dos recursos com essas etiquetas. Demora algum tempo para a análise de custos mostrar a utilização mais recente, pelo que poderá ainda não ver os custos. Quando os custos estão disponíveis, pode ver os custos dos recursos entre os grupos de recursos na sua subscrição. Os utilizadores devem ter [acesso ao nível de subscrição das informações de faturação](../articles/cost-management-billing/manage/manage-billing-access.md) para ver os custos.

Para ver os custos por etiqueta no portal, selecione a sua subscrição e selecione **Análise de Custos**.

![Análise de custos](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

Em seguida, filtre por valor da etiqueta e selecione **Aplicar**.

![Ver custo por etiqueta](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

Também pode utilizar as [APIs de Faturação do Azure](../articles/cost-management-billing/manage/usage-rate-card-overview.md) para ver os custos programaticamente.
