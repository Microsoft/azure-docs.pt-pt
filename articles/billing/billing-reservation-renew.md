---
title: Renovar automaticamente reservas do Azure
description: Saiba como pode renovar automaticamente reservas do Azure para continuar a obter descontos de reservas.
services: billing
author: bandersmsft
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 7fd74ded4afea56a4ae1baf18895e0b6661b8f58
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225987"
---
# <a name="automatically-renew-reservations"></a>Renovar reservas automaticamente

Pode renovar reservas para comprar automaticamente uma substituição quando uma reserva existente expirar. A renovação automática é uma forma fácil de continuar a obter descontos de reserva. Também evita que precise de monitorizar de perto a expiração de uma reserva. Com a renovação automática, evita a perda de benefícios de poupanças ao não ter de renovar manualmente. A definição da renovação está desativada por predefinição. Ative ou desative a definição da renovação em qualquer altura, até à expiração da reserva existente.

A renovação de uma reserva cria uma nova reserva quando a reserva existente expira. Não prolonga o período da reserva existente.

Opte por renovar automaticamente a qualquer momento. O preço da renovação está disponível 30 dias antes da expiração da reserva existente. Quando ativa a renovação mais de 30 dias antes da expiração da reserva, recebe um e-mail com detalhes sobre os custos de renovação 30 dias antes da expiração. O preço da reserva pode ser alterado entre a altura em que bloqueia o preço da renovação e a altura da renovação. Neste caso, o custo da renovação é o mais baixo dos dois custos. É possível alterar a quantidade de reserva. Se o fizer, a renovação é atualizada para utilizar o preço no mercado definido no momento da alteração da quantidade.

Não há nenhuma obrigação de renovação e pode recusar a renovação a qualquer momento antes que a reserva existente expire.

## <a name="set-up-renewal"></a>Configurar a renovação

Aceda ao portal do Azure > **Reservas**.

1. Selecione a reserva.
2. Clique em **Renovação**.
3. Selecione **Comprar automaticamente uma nova reserva ao expirar**.  
  ![Exemplo que mostra a renovação da reserva](./media/billing-reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>Se não renovar

Os serviços continuam a ser executados normalmente. São-lhe cobradas tarifas pay as you go pela utilização após a expiração da reserva.

## <a name="required-renewal-permissions"></a>Permissões de renovação necessárias

As seguintes condições são necessárias para renovar uma reserva:

- Tem de ser proprietário da reserva existente.
- Tem de ser proprietário da subscrição se o âmbito da reserva for uma única subscrição ou grupo de recursos.
- Tem de ser proprietário da subscrição se a mesma tiver um âmbito partilhado.

## <a name="default-renewal-settings"></a>Predefinições da renovação

Por predefinição, a renovação herda todas as propriedades da reserva a expirar. Uma compra da renovação de uma reserva tem o mesmo SKU, região, âmbito, subscrição de faturação, termo e quantidade.

No entanto, pode atualizar a quantidade de compra de renovação da reserva para otimizar as suas poupanças.

## <a name="when-the-new-reservation-is-purchased"></a>Quando é comprada a nova reserva

Uma nova reserva é comprada quando a reserva existente expira. Tentamos evitar atrasos entre as duas reservas. A continuidade garante que os seus custos são previsíveis e que continua a obter descontos.

## <a name="changing-parent-reservation-after-setting-renewal"></a>Alterar a reserva principal após definir a renovação

Se efetuar qualquer uma das seguintes alterações na reserva a expirar, a renovação da reserva é cancelada:

- Dividir
- Intercalar
- Transferir a reserva de uma conta para outra
- Transferir a reserva de uma subscrição do WebDirect para uma subscrição do EA (Contrato Enterprise) ou qualquer outro método de compra
- Renovar a inscrição

A nova reserva herda o âmbito e a definição de flexibilidade de tamanho de instância da reserva a expirar durante a renovação.

## <a name="new-reservation-permissions"></a>Novas permissões de reserva

O Azure copia as permissões da reserva a expirar para a nova reserva. Além disso, o administrador da conta de subscrição da compra de reserva tem acesso à nova reserva.

## <a name="potential-renewal-problems"></a>Possíveis problemas de renovação

O Azure poderá não processar a renovação se:

- Não for possível recolher o pagamento
- Ocorrer um erro do sistema durante a renovação
- O SKU a expirar não estiver ativo durante a renovação
- O EA for renovado num EA diferente

Receberá uma notificação por e-mail se qualquer uma das condições anteriores ocorrer e a renovação é desativada.

## <a name="renewal-notification"></a>Notificação de renovação

Os e-mails são enviados para pessoas diferentes, consoante o método de compra:

- Clientes do EA – os e-mails são enviados para os contactos de notificação definidos no EA Portal.
- Clientes de subscrição individual com tarifas pay as you go – os e-mails são enviados aos utilizadores que estão configurados como administradores de contas.
- Clientes do Fornecedor de Soluções Cloud – os e-mails são enviados para o contacto de notificação do parceiro.

## <a name="next-steps"></a>Passos seguintes
- Para saber mais sobre as Reservas do Azure, veja [O que são as Reservas do Azure?](billing-save-compute-costs-reservations.md)
