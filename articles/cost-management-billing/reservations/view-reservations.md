---
title: Permissões para visualizar e gerir reservas do Azure
description: Saiba como ver e gerir reservas do Azure no portal Azure.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: fe2f36b08f98ceb2a5f6085510b589a712ff194d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780466"
---
# <a name="permissions-to-view-and-manage-azure-reservations"></a>Permissões para visualizar e gerir reservas do Azure

Este artigo explica como as permissões de reserva funcionam e como os utilizadores podem ver e gerir reservas do Azure no portal Azure.

## <a name="who-can-manage-a-reservation-by-default"></a>Quem pode gerir reservas por predefinição

Por predefinição, os utilizadores seguintes podem ver e gerir as reservas:

- A pessoa que comprar uma reserva e o administrador de conta da subscrição de faturação utilizada para a compra são adicionados à encomenda da reserva.
- Administradores de faturação do Contrato Enterprise e do Contrato de Cliente Microsoft.
- Utilizadores com acesso elevado para gerir todas as subscrições e grupos de gestão do Azure

O ciclo de vida da reserva é independente de uma subscrição do Azure, pelo que a reserva não é um recurso sob a assinatura Azure. Em vez disso, é um recurso ao nível do inquilino com a sua própria permissão Azure RBAC separada das subscrições. As reservas não herdam permissões de subscrições após a compra.

## <a name="how-billing-administrators-can-view-or-manage-reservations"></a>Como os administradores de faturação podem ver ou gerir reservas

Se você é um administrador de faturação, use os passos seguintes para ver e gerir todas as reservas e transações de reservas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para **Gestão de Custos + Faturação.**
    - Se você é um administrador da EA, no menu esquerdo, selecione **os âmbitos de faturação** e, em seguida, na lista de âmbitos de faturação, selecione um.
    - Se for dono de um perfil de faturação do Microsoft Customer Agreement, no menu esquerdo, selecione **perfis de Faturação**. Na lista de perfis de faturação, selecione um.
1. No menu esquerdo, selecione **Produtos +**  >  **serviços Reservas.**
1. É apresentada a lista completa de reservas para a sua inscrição ou perfil de faturação da EA.
1. Os administradores de faturação podem apropriar-se de uma reserva selecionando-a e, em seguida, selecionando o **acesso** grant na janela que aparece.

### <a name="how-to-add-billing-administrators"></a>Como adicionar administradores de faturação

Adicionar um utilizador como administrador de faturação a um Contrato Enterprise ou Contrato de Cliente Microsoft:

- Num Contrato Enterprise, adicione utilizadores com a função _Administrador do Enterprise_ para ver e gerir todas as encomendas de reservas que se aplicam a esse Contrato Enterprise. Os administradores empresariais podem ver e gerir reservas em **Gestão de Custos + Faturação.**
    - Os utilizadores com a função _de Administrador Empresarial (leia apenas)_ só podem ver a reserva da Gestão de **Custos + Faturação.** 
    - Os administradores de departamentos e os proprietários de conta não podem ver as reservas, _a não ser_ que sejam adicionados explicitamente às mesmas com o Controlo de acesso (IAM). Para obter mais informações, veja [Gerir funções empresariais do Azure](../manage/understand-ea-roles.md).
- Num Contrato de Cliente Microsoft, os utilizadores com a função de proprietário de perfil de faturação ou a função contribuidor de perfil de faturação podem gerir todas as compras de reservas feitas com o perfil de faturação. Os leitores de perfil de faturação e os gestores de faturas podem ver todas as reservas que são pagas com o perfil de faturação. No entanto, não podem fazer alterações às reservas.
    Para obter mais informações, veja [Funções e tarefas do perfil de faturação](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="view-reservations-with-azure-rbac-access"></a>Ver reservas com acesso a Azure RBAC

Se adquiriu a reserva ou se adicionou a uma reserva, use os seguintes passos para ver e gerir reservas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços** > **Reserva** para listar as reservas às quais tem acesso.

## <a name="users-with-elevated-access-can-manage-all-azure-subscriptions-and-management-groups"></a>Os utilizadores com acesso elevado podem gerir todas as subscrições e grupos de gestão do Azure

Pode elevar o acesso de um utilizador [para gerir todas as subscrições e grupos de gestão da Azure.](../../role-based-access-control/elevate-access-global-admin.md?toc=/azure/cost-management-billing/reservations/toc.json)

Depois de ter acesso elevado:

1. Navegue para **Todos os Serviços**  >  **Reserva** para ver todas as reservas que estão no inquilino.
1. Para efetuar modificações na reserva, adicione-se como proprietário da ordem de reserva utilizando o controlo de acesso (IAM).

## <a name="give-users-azure-rbac-access-to-individual-reservations"></a>Dar aos utilizadores Azure RBAC acesso a reservas individuais

Os utilizadores que tenham acesso ao proprietário nas reservas e administradores de faturação podem delegar a gestão de acesso para uma ordem de reserva individual.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços** > **Reserva** para listar as reservas às quais tem acesso.
1. Selecione a reserva cujo o acesso quer delegar a outros utilizadores.
1. Nos Detalhes da reserva, selecione a encomenda da reserva.
1. Selecione **Controlo de acesso (IAM)** .
1. Selecione **Adicionar atribuição de função** > **Função** > **Proprietário**. Se quiser conceder acesso limitado, selecione uma função diferente.
1. Introduza o endereço de e-mail do utilizador que quer adicionar como proprietário.
1. Selecione o utilizador e, em seguida, selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

- [Gerir Reservas do Azure](manage-reserved-vm-instance.md).