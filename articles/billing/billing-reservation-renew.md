---
title: Renovar automaticamente reservas do Azure
description: Saiba como você pode renovar automaticamente reservas do Azure para continuar a obter descontos de reserva.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: banders
ms.openlocfilehash: c19c6af68bcde753ec9bed990e08aa81eabdd37d
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679461"
---
# <a name="automatically-renew-reservations"></a>Renovar reservas automaticamente

Você pode renovar reservas para comprar automaticamente uma substituição quando uma reserva existente expirar. A renovação automática fornece uma maneira fácil de continuar recebendo descontos de reserva. Ele também evita que você precise monitorar de forma mais próxima a expiração de uma reserva. Com a renovação automática, você evita a perda de benefícios que não precisa ser renovada manualmente. A configuração de renovação é desativada por padrão. Habilite ou desabilite a configuração de renovação a qualquer momento, até a expiração da reserva existente.

Renovar uma reserva cria uma nova reserva quando a reserva existente expira. Ele não estende o termo da reserva existente.

Aceite para renovar automaticamente a qualquer momento. O preço de renovação está disponível 30 dias antes da expiração da reserva existente. Quando você habilita a renovação mais de 30 dias antes da expiração da reserva, você recebe um email detalhando os custos de renovação 30 dias antes da expiração. O preço de reserva pode ser alterado entre a hora em que você bloqueia o preço de renovação e o tempo de renovação. Nesse caso, seu custo de renovação é o menor dos dois custos. Você pode fazer alterações na quantidade de reserva. Se você fizer isso, a renovação será atualizada para usar o preço no mercado definido no momento da alteração da quantidade.

Não há nenhuma obrigação de renovar e você pode recusar a renovação a qualquer momento antes que a reserva existente expire.

## <a name="set-up-renewal"></a>Configurar renovação

Vá para portal do Azure > **reservas**.

1. Selecione a reserva.
2. Clique em **renovação**.
3. Selecione **comprar automaticamente uma nova reserva após a expiração**.  
  ![Exemplo mostrando renovação de reserva](./media/billing-reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>Se você não renovar

Seus serviços continuam a ser executados normalmente. Você é cobrado pelas tarifas pré-pagas pelo uso após a expiração da reserva.

## <a name="required-renewal-permissions"></a>Permissões de renovação necessárias

As seguintes condições são necessárias para renovar uma reserva:

- Você deve ser um proprietário da reserva existente.
- Você deve ser um proprietário da assinatura se a reserva estiver no escopo de uma única assinatura ou grupo de recursos.
- Você deve ser um proprietário da assinatura se tiver um escopo compartilhado.

## <a name="default-renewal-settings"></a>Configurações de renovação padrão

Por padrão, a renovação herda todas as propriedades da reserva de expiração. Uma compra de renovação de reserva tem a mesma SKU, região, escopo, assinatura de cobrança, termo e quantidade.

No entanto, você pode atualizar a quantidade de compra de reserva de renovação para otimizar sua economia.

## <a name="when-the-new-reservation-is-purchased"></a>Quando a nova reserva for comprada

Uma nova reserva é comprada quando a reserva existente expira. Tentamos evitar qualquer atraso entre as duas reservas. A continuidade garante que seus custos sejam previsíveis e você continue a obter descontos.

## <a name="changing-parent-reservation-after-setting-renewal"></a>Alterando a reserva pai após a renovação da configuração

Se você fizer qualquer uma das seguintes alterações na reserva de expiração, a renovação da reserva será cancelada:

- Dividir
- Unir
- Transferindo a reserva de uma conta para outra
- Transferindo a reserva de uma assinatura do webdirect para uma assinatura do EA (Enterprise Agreement) ou qualquer outro método de compra
- Renovar o registro

A nova reserva herda a configuração de flexibilidade de tamanho de instância e escopo da reserva de expiração durante a renovação.

## <a name="new-reservation-permissions"></a>Novas permissões de reserva

O Azure copia as permissões da reserva de expiração para a nova reserva. Além disso, o administrador da conta de assinatura da compra de reserva tem acesso à nova reserva.

## <a name="potential-renewal-problems"></a>Possíveis problemas de renovação

O Azure poderá não processar a renovação se:

- O pagamento não pode ser coletado
- Ocorre um erro do sistema durante A renovação
- O SKU de expiração não está ativo durante a renovação
- O EA é renovado em um EA diferente

Você receberá uma notificação por email se qualquer uma das condições anteriores ocorrer e a renovação for desativada.

## <a name="renewal-notification"></a>Notificação de renovação

Os emails são enviados para pessoas diferentes, dependendo do seu método de compra:

- Clientes do EA-os emails são enviados para os contatos de notificação definidos no portal do EA.
- Clientes de assinatura individuais com tarifas pagas conforme o uso-os emails são enviados aos usuários que estão configurados como administradores de contas.
- Clientes do provedor de soluções na nuvem-emails são enviados para o contato de notificação do parceiro.

## <a name="next-steps"></a>Passos Seguintes
- Para saber mais sobre as reservas do Azure, confira [o que são as reservas do Azure?](billing-save-compute-costs-reservations.md)
