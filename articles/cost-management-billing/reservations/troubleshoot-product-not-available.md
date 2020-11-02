---
title: Resolução de problemas do tipo de reserva Azure não disponível
description: Este artigo ajuda-o a compreender e resolver problemas de instâncias reservadas que aparecem como não disponíveis no portal do Azure.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/27/2020
ms.openlocfilehash: 8575d9d86d8e720122a295cf92fa571ef33d5b4c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92798207"
---
# <a name="troubleshoot-reservation-type-not-available"></a>Resolver problemas de tipo de reserva não disponível

Este artigo ajuda-o a compreender e resolver problemas de instâncias reservadas que aparecem como não disponíveis no portal do Azure.

## <a name="symptoms"></a>Sintomas

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) e navegue para **Reservas** .
2. Selecione **+ Adicionar** e, em seguida, selecione um produto.
3. Selecione o separador **Todos os Produtos** .
4. Na lista de produtos, selecione um. Poderá ver uma das mensagens seguintes:
    - `Product unavailable for the selected subscription or region. Contact support.`  
        :::image type="content" source="./media/troubleshoot-product-not-available/product-unavailable-message.png" alt-text="Exemplo que mostra o Produto indisponível para a mensagem de subscrição ou de região selecionada" lightbox="./media/troubleshoot-product-not-available/product-unavailable-message.png" :::
    - `The selected subscription does not have enough core quota remaining to purchase this product. Request quota increase`  
        :::image type="content" source="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" alt-text="Exemplo que mostra o Produto indisponível para a mensagem de subscrição ou de região selecionada" lightbox="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" :::

## <a name="cause"></a>Causa

Algumas reservas não estão disponíveis para compra porque:

- Nem todos os produtos de instância reservada estão disponíveis para compra em todas as regiões
- A sua subscrição tem uma restrição de quota

### <a name="cause-1"></a>Motivo 1

Nem todos os produtos de instância reservada estão disponíveis para compra. O Azure decide permitir ou não alguns produtos com base nos custos associados ao fornecimento do desconto monetário aos clientes. Noutros casos, o Azure não disponibiliza alguns produtos devido a condições de capacidade em datacenters específicos. Além disso, alguns grupos de desenvolvimento de produtos do Azure podem agora permitir determinados produtos porque pretendem descontinuar um produto mais antigo.

Noutros casos, o Azure coloca as restrições de capacidade em vários produtos com base na procura desses recursos em algumas regiões. Por exemplo, esta restrição pode ser feita quando a procura de um determinado tamanho de VM é executada num datacenter. Neste exemplo, o Azure não pode garantir a capacidade para clientes que tenham comprado uma reserva para esse tamanho nessa região. Por fim, há alguns produtos que são exclusivos por vários motivos. Estes produtos são disponibilizados apenas para um conjunto pequeno e selecionado de clientes.

Como algumas reservas não estão disponíveis para compra, por que são mostradas no portal do Azure? A resposta é porque os utilizadores criam pedidos de suporte, para informar que não conseguem localizar os produtos que pretendem. A equipa de desenvolvimento da Reserva do Azure determinou que mostrar todos os produtos com a `Product unavailable` mensagem resulta em menos pedidos de suporte do que não mostrá-los.

### <a name="cause-2"></a>Motivo 2

A sua subscrição tem uma restrição de quota. As subscrições têm limites sobre quantos núcleos de CPU podem consumir. O limite aplica-se a alguns produtos de instância reservada, principalmente às máquinas virtuais. O Azure quer garantir que as pessoas que comprem uma instância reservada possam utilizá-la. O Azure faz uma verificação rápida simples no portal do Azure para garantir que tenha núcleos livres suficientes na sua subscrição para implementar a VM e obter um benefício da compra de reserva.

A verificação para permitir que adicione um produto específico ao seu carrinho e compre uma reserva é simples. O Azure avalia o número total de núcleos de CPU disponíveis para a sua subscrição e verifica se o número é maior do que o número de núcleos para o item selecionado.

O Azure não verifica a quota de instância reservada de âmbito **Partilhado** . O benefício da instância reservada para o âmbito partilhado aplica-se a todas as subscrições na inscrição. O Azure não pode determinar se tem núcleos livres suficientes em todas as suas subscrições para implementar o recurso. Independentemente da quota, o Azure permite selecionar sempre um tamanho de VM quando o âmbito selecionado é partilhado.

Além disso, o Azure não faz uma verificação de quota para compras **recomendadas** . As recomendações baseiam-se na utilização ativa. O Azure pressupõe que tenha núcleos suficientes para executar um tamanho de VM específico porque já gerou a utilização necessária para criar a recomendação.

## <a name="solution"></a>Solução

Dependendo da mensagem de erro que tiver recebido, utilize uma das seguintes soluções para o seu problema.

### <a name="solution-1"></a>Solução 1

Se vir alguma mensagem de _Produto indisponível_ , selecione a hiperligação **Contactar suporte** na mensagem de erro para pedir a adição de uma exceção para a sua subscrição. As exceções nem sempre são concedidas.

### <a name="solution-2"></a>Solução 2

Se vir uma mensagem de _Quota de núcleo insuficiente_ , pode alterar o âmbito para **Partilhado** . Depois de comprar a reserva, pode alterar o âmbito de reserva de **Partilhado** para **Único** .

Ou então, selecione a hiperligação **Pedir aumento de quota** na mensagem de erro para pedir quota de núcleo de CPU adicional para a sua subscrição.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações acerca das opções de âmbito de reserva, consulte [Âmbito de reservas](prepare-buy-reservation.md#scope-reservations).