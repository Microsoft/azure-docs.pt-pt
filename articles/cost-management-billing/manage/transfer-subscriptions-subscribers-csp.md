---
title: Transferir subscrições do Azure entre subscritores e CSPs
description: Saiba como pode transferir subscrições do Azure entre subscritores e CSPs.
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: 687db46ea2f6c9c4fae6e5355e3236cde3d7a401
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567252"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>Transferir subscrições do Azure entre subscritores e CSPs

Este artigo apresenta os passos de alto nível utilizados para transferir subscrições do Azure de e para parceiros Fornecedores de Soluções Cloud (CSP) e os clientes. As presentes informações destinam-se aos subscritores do Azure para os ajudar a coordenarem-se com os respetivos parceiros. As informações que os parceiros da Microsoft utilizam para o processo de transferência estão documentadas em [Aprender a transferir a subscrição do Azure de um cliente para outro parceiro](/partner-center/switch-azure-subscriptions-to-a-different-partner).

Antes de iniciar um pedido de transferência, deve transferir ou exportar qualquer informação de custo e faturação que pretenda manter. As informações de faturação e utilização não são transferidas com a subscrição. Para obter mais informações acerca da exportação dos dados de gestão de custo, consulte [Criar e gerir dados exportados](../costs/tutorial-export-acm-data.md). Para obter mais informações sobre como transferir a sua fatura e dados de utilização, consulte [Transferir ou visualizar a sua fatura de faturação do Azure e dados de utilização diária](download-azure-invoice-daily-usage-date.md).

Se tiver reservas existentes, deixam de se candidatar 90 dias após a transferência de uma subscrição. Certifique-se de [cancelar quaisquer reservas e reembolsá-las](../reservations/exchange-and-refund-azure-reservations.md) antes de transferir uma subscrição para evitar encargos após o período de carência de 90 dias.

## <a name="transfer-ea-subscriptions-to-a-csp-partner"></a>Transferir subscrições EA para um parceiro CSP

Os parceiros CSP de faturação direta certificados como [Azure Expert Managed Services Provider (MSP)](https://partner.microsoft.com/membership/azure-expert-msp) podem pedir a transferência de subscrições do Azure para os clientes que tenham um Contrato Enterprise (EA) Direto. As transferências de subscrições são permitidas apenas para os clientes que aceitaram o Contrato de Cliente Microsoft (MCA) e que tenham comprado um plano do Azure com o programa CSP.

Quando o pedido for aprovado, o CSP poderá disponibilizar uma fatura combinada aos clientes. Para saber mais acerca da transferência de subscrições pelos CSPs, veja [ Obter a propriedade de faturação da conta MPA](mpa-request-ownership.md).

>[!IMPORTANT]
> Depois de transferir uma subscrição de EA para um parceiro CSP, quaisquer aumentos de quota anteriormente aplicados à subscrição de EA serão repostos para o valor predefinido. Se uma quota adicional for obrigatória após a transferência da subscrição, peça ao fornecedor CSP que envie um pedido de [aumento de quota](../../azure-portal/supportability/regional-quota-requests.md). 

## <a name="other-subscription-transfers-to-a-csp-partner"></a>Outras transferências de subscrições para um parceiro CSP

Para transferir quaisquer outras subscrições do Azure para um parceiro CSP, o subscritor tem de mover os recursos das subscrições de origem para as subscrições CSP. Utilize as seguintes orientações para mover recursos entre subscrições.

1. Estabeleça uma [relação de revendedor](/partner-center/request-a-relationship-with-a-customer) com o cliente. Reveja a Visão Geral de Autorização Regional da [CSP](/partner-center/regional-authorization-overview) para garantir que tanto o cliente como o inquilino parceiro se encontram nas mesmas regiões autorizadas.
1. Trabalhe em conjunto com o parceiro CSP para criar subscrições de destino do Azure CSP.
1. Garanta que as subscrições CSP de origem e de destino se encontram no mesmo inquilino do Azure Active Directory (AAD).  
    Não pode mudar o inquilino do AAD de uma subscrição do Azure CSP. Em vez disso, tem de adicionar ou associar a subscrição de origem ao inquilino do AAD CSP. Para obter mais informações, veja [Associar ou adicionar uma subscrição do Azure ao inquilino do Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    > [!IMPORTANT]
    > - Quando associa uma subscrição a um diretório diferente do Azure AD, os utilizadores com funções atribuídas através do [controlo de acesso baseado em funções do Azure (RBAC do Azure)](../../role-based-access-control/role-assignments-portal.md) perdem o acesso. Os administradores de subscrição clássicos, incluindo o Administrador de Serviços e os Coadministradores, também perdem o acesso.
    > - As Atribuições de Políticas também são removidas de uma subscrição quando a subscrição é associada a um diretório diferente.
1. A conta de utilizador que utiliza para fazer a transferência tem de ter um acesso de proprietário do [RBAC do Azure](add-change-subscription-administrator.md) em ambas as subscrições.
1. Antes de começar, [confirme](/rest/api/resources/resources/validatemoveresources) que todos os recursos do Azure podem ser movidos da subscrição de origem para a subscrição de destino.  
    Alguns recursos do Azure não podem ser movidos entre subscrições. Para ver a lista completa dos recursos do Azure que podem ser movidos, veja [Suporte da operação de transferência de recursos](../../azure-resource-manager/management/move-support-resources.md).
    > [!IMPORTANT]
    >  - O Azure CSP só suporta recursos do Azure Resource Manager. Se os recursos do Azure na subscrição de origem tiverem sido criados através do modelo de implementação clássica do Azure, terá de os migrar para o [Azure Resource Manager](/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm) antes da migração. Tem de ser um parceiro para ver a página Web.

1. Confirme que todos os serviços da subscrição de origem utilizam o modelo do Azure Resource Manager. Em seguida, transfira os recursos da subscrição de origem para a subscrição de destino com [Mover Recursos do Azure](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - A transferência de recursos do Azure entre subscrições pode resultar em tempo de inatividade do serviço, com base nos recursos na subscrição.

## <a name="transfer-csp-subscription-to-other-offer"></a>Transferir subscrição CSP para outra oferta

Para transferir quaisquer outras subscrições de um Parceiro CSP para qualquer outra oferta do Azure, o subscritor precisa de mover os recursos entre subscrições CSP de origem e subscrições de destino. Este é um trabalho feito por um parceiro e um cliente - não é trabalho feito por um representante da Microsoft.

1. O cliente cria subscrições target Azure.
1. Garanta que as subscrições de origem e de destino estão no mesmo inquilino do Azure Active Directory (AAD). Para obter mais informações sobre a alteração do inquilino do AAD, veja [Associar ou adicionar uma subscrição do Azure ao inquilino do Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    Tenha em conta que a opção da alteração do diretório não é suportada na subscrição CSP. Por exemplo, está a transferir-se de um CSP para uma subscrição pay as you go. Tem de alterar o diretório da subscrição pay as you go para corresponder ao diretório.

    > [!IMPORTANT]
    >  - Quando associa uma subscrição a um diretório diferente, os utilizadores que têm funções atribuídas através do [RBAC do Azure](../../role-based-access-control/role-assignments-portal.md) perdem o acesso. Os administradores de subscrição clássicos, incluindo o Administrador de Serviços e os Coadministradores, também perdem o acesso.
    >  - As Atribuições de Políticas também são removidas de uma subscrição quando a subscrição é associada a um diretório diferente.

1. A conta de utilizador do cliente que utiliza para fazer a transferência deve ter acesso ao proprietário do [Azure RBAC](add-change-subscription-administrator.md) em ambas as subscrições.
1. Antes de começar, [confirme](/rest/api/resources/resources/validatemoveresources) que todos os recursos do Azure podem ser movidos da subscrição de origem para a subscrição de destino.
    > [!IMPORTANT]
    >  - Alguns recursos do Azure não podem ser movidos entre subscrições. Para ver a lista completa dos recursos do Azure que podem ser movidos, veja [Suporte da operação de transferência de recursos](../../azure-resource-manager/management/move-support-resources.md).

1. Transfira recursos de uma subscrição de origem para a subscrição de destino com [Mover recursos do Azure](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - A transferência de recursos do Azure entre subscrições pode resultar em tempo de inatividade do serviço, com base nos recursos na subscrição.

## <a name="next-steps"></a>Passos seguintes
- [Obter a propriedade da faturação das subscrições do Azure da conta MPA](mpa-request-ownership.md).
- Leia para saber como [Gerir contas e subscrições com a Faturação do Azure](../index.yml).
