---
title: Resolver problemas de vistas de custo do Azure enterprise
description: Saiba como resolver quaisquer problemas que poderá ter com exibições de custo organizacional no portal do Azure.
author: bandersmsft
manager: amberb
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 83f7f424b265582a3830c02973cbbb9962ddfbfb
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491264"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Resolver problemas de vistas de custos empresariais

Dentro de inscrições de empresas, há várias configurações que podem fazer com que os utilizadores dentro da inscrição para não ver os custos.  Estas definições são geridas pelo administrador de inscrição. Em alternativa, se a inscrição não é comprada diretamente através da Microsoft, as definições são geridas pelo parceiro.  Este artigo ajuda-o a compreender quais são as definições e seu impacto a inscrição. Estas definições são independentes das funções (RBAC) do controle de acesso baseado em funções do Azure.

## <a name="enable-access-to-costs"></a>Ativar o acesso aos custos

Está a vê uma mensagem não autorizado, ou *"as vistas de custo estão desativadas na sua inscrição".* Quando está à procura de informações de custo?
![Captura de ecrã que mostra "não autorizada" no campo de custo atual para a subscrição.](media/billing-enterprise-mgmt-groups/unauthorized.png)

Pode ser para um dos seguintes motivos:

1. Comprou o Azure através de um parceiro da empresa, e o parceiro não versão de preços ainda. Contacte o seu parceiro para atualizar os preços definição dentro de [Enterprise portal](https://ea.azure.com).
2. Se for um cliente com contrato EA direto, existem duas possibilidades:
    * For um proprietário da conta e o administrador de inscrição desativada a **cobranças de exibição AO** definição.  
    * For um administrador do departamento e o administrador de inscrição desativada a **cobranças de vista DA** definição.
    * Contacte o administrador de inscrição para obter acesso. O administrador de inscrição pode atualizar as definições no [Enterprise portal](https://ea.azure.com/manage/enrollment).

      ![Captura de ecrã que mostra as definições de Portal da empresa para cobranças de vista.](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>Recurso não está disponível

Se obtiver uma mensagem de erro a indicar **este recurso não está disponível** ao tentar aceder a uma subscrição ou grupo de gestão, não tem a função correta para ver este item.  

![Captura de ecrã que mostra a mensagem "o recurso não está disponível".](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Peça ao seu administrador de grupo de gestão ou de subscrição do Azure para o acesso. Para obter mais informações, consulte [gerir o acesso com RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos Seguintes
- Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
