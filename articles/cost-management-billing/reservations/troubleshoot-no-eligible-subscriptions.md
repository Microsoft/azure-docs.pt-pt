---
title: Resolver problemas de subscrições não elegíveis no portal do Azure
description: Este artigo ajuda-o a resolver a mensagem de erro Nenhuma subscrição elegível no portal do Azure quando tenta comprar uma reserva.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 11/16/2020
ms.openlocfilehash: 1b36577c3c0940687f98394f8ea4faae83f371be
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887200"
---
# <a name="troubleshoot-no-eligible-subscriptions"></a>Resolver o erro Nenhuma subscrição elegível

Este artigo ajuda-o a resolver a mensagem de erro *Nenhuma subscrição elegível* no portal do Azure quando tenta comprar uma reserva.

## <a name="symptoms"></a>Sintomas

1. Inicie sessão no [portal do Azure](https://portal.azure.com) e navegue para **Reservas**.
1. Selecione **Adicionar** e selecione um serviço.
1. Verá a mensagem de erro seguinte:
   ```
    No eligible subscriptions
    
    You do not have any eligible subscriptions to purchase reservations. To purchase a reservation, you should have owner or reservation purchaser permission on at least one subscription of the following type: Pay-as-you-go, CSP, Microsoft Enterprise or Microsoft Customer Agreement.
    ```
1. Na área, **Selecionar o produto que pretende comprar**, expanda a lista **Subscrição de faturação** para ver o motivo pelo qual uma determinada subscrição não é elegível para comprar uma instância reservada. A imagem seguinte mostra exemplos dos motivos pelos quais não é possível comprar uma reserva.  
    :::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" alt-text="Exemplo que mostra por que motivo não é possível comprar uma reserva" lightbox="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" :::

## <a name="cause"></a>Causa

Para comprar uma instância reservada do Azure, tem de ter, pelo menos, uma subscrição que cumpra os requisitos seguintes:

- A subscrição tem de ser um tipo de oferta suportada. Os tipos de ofertas suportadas são: pay as you go, Fornecedor de Soluções Cloud (CSP), Microsoft Azure Enterprise ou Contrato de Cliente Microsoft.
- Tem de ser proprietário ou o comprador da reserva na subscrição.

Se não tiver uma subscrição que cumpra estes requisitos, recebe o erro `No eligible subscriptions`.

### <a name="cause-1"></a>Motivo 1

A subscrição tem de ser um tipo de oferta suportada. Os tipos de ofertas suportadas são: pay as you go, CSP, Microsoft Azure Enterprise ou Contrato de Cliente Microsoft. O tipo da sua subscrição não é suportado. Quando seleciona uma subscrição cujo tipo de oferta não suporta reservas, vê o erro seguinte.

```
Subscription not eligible for purchase

This subscription is not eligible for reservation benefit an cannot be used to purchase a reservation.
```

:::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/subscription-not-eligible.png" alt-text="Exemplo que mostra a mensagem de erro Subscrição não elegível para compra" :::

### <a name="cause-2"></a>Motivo 2

Tem de ser proprietário ou o comprador da reserva na subscrição. Se não tiver permissões suficientes, verá o erro seguinte.

```
You do not have owner or reservation purchaser access on the subscription

You can only purchase reservations using subscriptions on which you have owner or reservation purchaser access.
```

## <a name="solution"></a>Solução

- Se a sua oferta atual não suportar reservas, tem de criar uma subscrição do Azure nova.
- Se não tiver acesso a uma reserva já existente, pode obter acesso à mesma junto do proprietário atual.

### <a name="solution-1"></a>Solução 1

Para comprar uma reserva, tem de criar uma subscrição do Azure nova que suporte reservas.

- Se tiver uma avaliação gratuita do Azure, pode [atualizar a sua subscrição](../manage/upgrade-azure-subscription.md).
- Pode criar uma subscrição do Azure nova com as [tarifas pay as you go](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).
- Inscreva-se num [Contrato de Cliente Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) e crie uma subscrição nova.
- Compre uma subscrição nova junto de um [CSP](https://www.microsoft.com/solution-providers/home) e crie uma subscrição nova.

### <a name="solution-2"></a>Solução 2

Para obter acesso de proprietário a uma reserva, tem de obter acesso:

- À encomenda da reserva com a qual a mesma foi comprada
- À reserva propriamente dita

O proprietário da encomenda de reserva atual ou o proprietário da reserva pode delegar-lhe o acesso com os passos seguintes.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços** > **Reserva** para listar as reservas às quais tem acesso.
1. Selecione a reserva cujo o acesso quer delegar a outros utilizadores.
1. Selecione **Controlo de acesso (IAM)** .
1. Selecione **Adicionar atribuição de função** > **Função** > **Proprietário**. Se preferir dar acesso limitado, selecione uma função diferente.
1. Introduza o endereço de e-mail do utilizador que quer adicionar como proprietário.
1. Selecione o utilizador e, em seguida, selecione **Guardar**.

Para obter mais informações, veja [Adicionar ou alterar os utilizadores que podem gerir uma reserva](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="next-steps"></a>Passos seguintes

- Se precisar de pedir a um proprietário de reserva que lhe conceda acesso, reveja [Adicionar ou alterar os utilizadores que podem gerir uma reserva](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
