---
title: Transferir subscrições do Azure entre subscritores e CSPs
description: Saiba como pode transferir subscrições do Azure entre subscritores e CSPs.
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: banders
ms.openlocfilehash: 4e7e9ea61d74eb38f3c225d66a39906bc18fe6d2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77200525"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>Transferir subscrições do Azure entre subscritores e CSPs

Este artigo apresenta os passos de alto nível utilizados para transferir subscrições do Azure entre parceiros Fornecedores de Soluções Cloud (CSP) e os clientes.

## <a name="transfer-ea-subscriptions"></a>Transferir subscrições EA

Os parceiros CSP de faturação direta certificados como [Azure Expert Managed Services Provider (MSP)](https://partner.microsoft.com/membership/azure-expert-msp) podem pedir a transferência de subscrições do Azure para os clientes que tenham um Contrato Enterprise (EA) Direto. As transferências de subscrições são permitidas apenas para os clientes que aceitaram o Contrato de Cliente Microsoft (MCA) e que tenham comprado um plano do Azure.

Quando o pedido for aprovado, o CSP poderá disponibilizar uma fatura combinada aos clientes. Para saber mais acerca da transferência de subscrições pelos CSPs, veja [ Obter a propriedade de faturação da conta MPA](mpa-request-ownership.md).

## <a name="other-subscription-transfers-to-a-csp-partner"></a>Outras transferências de subscrições para um parceiro CSP

Para transferir quaisquer outras subscrições do Azure para um parceiro CSP, o subscritor tem de mover os recursos das subscrições de origem para as subscrições CSP. Utilize as seguintes orientações para mover recursos entre subscrições.

1. Trabalhe em conjunto com o parceiro CSP para criar subscrições de destino do Azure CSP.
1. Garanta que as subscrições CSP de origem e de destino se encontram no mesmo inquilino do Azure Active Directory (AAD).  
    Não pode mudar o inquilino do AAD de uma subscrição do Azure CSP. Em vez disso, tem de adicionar ou associar a subscrição de origem ao inquilino do AAD CSP. Para obter mais informações, veja [Associar ou adicionar uma subscrição do Azure ao inquilino do Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    > [!IMPORTANT]
    > - Quando associa uma subscrição a um diretório diferente do AAD, os utilizadores com funções atribuídas através do [controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/role-assignments-portal.md) perdem o acesso. Os administradores de subscrição clássicos, incluindo o Administrador de Serviços e os Coadministradores, também perdem o acesso.
    > - As Atribuições de Políticas também são removidas de uma subscrição quando a subscrição é associada a um diretório diferente.
1. A conta de utilizador que utiliza para fazer a transferência tem de ter um acesso de proprietário do [RBAC](add-change-subscription-administrator.md) em ambas as subscrições.
1. Antes de começar, [confirme](/rest/api/resources/resources/validatemoveresources) que todos os recursos do Azure podem ser movidos da subscrição de origem para a subscrição de destino.  
    Alguns recursos do Azure não podem ser movidos entre subscrições. Para ver a lista completa dos recursos do Azure que podem ser movidos, veja [Suporte da operação de transferência de recursos](../../azure-resource-manager/management/move-support-resources.md).
    > [!IMPORTANT]
    >  - O Azure CSP só suporta recursos do Azure Resource Manager. Se os recursos do Azure na subscrição de origem tiverem sido criados através do modelo de implementação clássica do Azure, terá de os migrar para o [Azure Resource Manager](https://docs.microsoft.com/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm) antes da migração. Tem de ser um parceiro para ver a página Web.

1. Confirme que todos os serviços da subscrição de origem utilizam o modelo do Azure Resource Manager. Em seguida, transfira os recursos da subscrição de origem para a subscrição de destino com [Mover Recursos do Azure](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - A transferência de recursos do Azure entre subscrições pode resultar em tempo de inatividade do serviço, com base nos recursos na subscrição.

## <a name="all-subscription-transfers-from-a-csp-partner"></a>Todas as transferências de subscrições de um Parceiro CSP

Para transferir quaisquer outras subscrições de um Parceiro CSP para qualquer outra oferta do Azure, o subscritor precisa de mover os recursos entre subscrições CSP de origem e subscrições de destino.

1. Crie subscrições do Azure de destino.
1. Garanta que as subscrições de origem e de destino estão no mesmo inquilino do Azure Active Directory (AAD). Para obter mais informações sobre a alteração do inquilino do AAD, veja [Associar ou adicionar uma subscrição do Azure ao inquilino do Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

    > [!IMPORTANT]
    >  - Quando associa uma subscrição a um diretório diferente, os utilizadores que têm funções atribuídas através do [RBAC](../../role-based-access-control/role-assignments-portal.md) perdem o acesso. Os administradores de subscrição clássicos, incluindo o Administrador de Serviços e os Coadministradores, também perdem o acesso.
    >  - As Atribuições de Políticas também são removidas de uma subscrição quando a subscrição é associada a um diretório diferente.

1. A conta de utilizador que utiliza para fazer a transferência tem de ter um acesso de proprietário do [RBAC](add-change-subscription-administrator.md) em ambas as subscrições.
1. Antes de começar, [confirme](/rest/api/resources/resources/validatemoveresources) que todos os recursos do Azure podem ser movidos da subscrição de origem para a subscrição de destino.
    > [!IMPORTANT]
    >  - Alguns recursos do Azure não podem ser movidos entre subscrições. Para ver a lista completa dos recursos do Azure que podem ser movidos, veja [Suporte da operação de transferência de recursos](../../azure-resource-manager/management/move-support-resources.md).

1. Transfira recursos de uma subscrição de origem para a subscrição de destino com [Mover recursos do Azure](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - A transferência de recursos do Azure entre subscrições pode resultar em tempo de inatividade do serviço, com base nos recursos na subscrição.

## <a name="next-steps"></a>Passos seguintes
- [Obter a propriedade da faturação das subscrições do Azure da conta MPA](mpa-request-ownership.md).
- Leia para saber como [Gerir contas e subscrições com a Faturação do Azure](index.yml).
