---
title: Resolver visualizações de custos empresariais do Azure
description: Saiba como resolver os problemas que possa ter com as visualizações de custos organizacionais dentro do portal do Azure.
author: bandersmsft
ms.reviewer: amberb
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: troubleshooting
ms.date: 08/20/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: bd7f1ff20189b971ba32e5c4b40b380fc22611b2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686858"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Resolver visualizações de custos empresariais

Em inscrições corporativas, há várias definições que podem fazer com que os utilizadores dentro da inscrição não vejam os custos.  Estas definições são geridas pelo administrador de inscrição. Ou, se a inscrição não for comprada diretamente através da Microsoft, as definições serão geridas pelo parceiro.  Este artigo ajuda a compreender quais são as definições e como elas afetam a inscrição. Estas definições são independentes das funções do Azure.

## <a name="enable-access-to-costs"></a>Ativar o acesso aos custos

Vê uma mensagem de Não autorizado ou *“As visualizações de custos estão desativadas na sua inscrição”.* ao procurar informações de custos?
![Captura de ecrã que mostra “Não autorizado” no campo Custo atual para a subscrição.](./media/enterprise-mgmt-grp-troubleshoot-cost-view/unauthorized.png)

Poderá ser por um dos seguintes motivos:

1. Comprou o Azure através de um parceiro corporativo e o parceiro ainda não publicou os preços. Entre em contato com o seu parceiro para atualizar a definição de preços no [Enterprise Portal](https://ea.azure.com).
2. Se for um cliente do EA Direct, haverá duas possibilidades:
    * É Proprietário de Conta e o seu Administrador de Inscrição desativou a definição **Os Proprietários de Contas podem ver os custos**.  
    * É Administrador de Departamento e o seu Administrador de Inscrição desativou a definição **Os Administradores de Departamento podem ver os custos**.
    * Contacte o Administrador de Inscrição para obter acesso. O Administrador de Inscrição pode atualizar as definições no [Enterprise Portal](https://ea.azure.com/manage/enrollment).

      ![Captura de ecrã que mostra as definições do Enterprise Portal para visualizar custos.](./media/enterprise-mgmt-grp-troubleshoot-cost-view/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>O elemento está indisponível

Se receber uma mensagem de erro que diz **Este elemento está indisponível** ao tentar aceder a uma subscrição ou grupo de gestão, não tem a função correta para visualizar este item.  

![Captura de ecrã que mostra a mensagem “O elemento está indisponível”.](./media/enterprise-mgmt-grp-troubleshoot-cost-view/asset-not-found.png)

Peça acesso ao administrador do grupo de gestão ou da subscrição do Azure. Para obter mais informações, veja [Gerir o acesso através do RBAC e do portal do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos seguintes
- Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
