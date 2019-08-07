---
title: Gerenciar reservas do Azure
description: Saiba como você pode gerenciar as reservas do Azure.
ms.service: billing
author: bandersmsft
manager: yashesvi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2019
ms.author: banders
ms.openlocfilehash: b161fc7cd4faa75dd87613c297c12f1edd862510
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840031"
---
# <a name="manage-reservations-for-azure-resources"></a>Gerenciar reservas para recursos do Azure

Depois de comprar uma reserva do Azure, talvez seja necessário aplicar a reserva a uma assinatura diferente, alterar quem pode gerenciar a reserva ou alterar o escopo da reserva. Você também pode dividir uma reserva em duas reservas para aplicar algumas das instâncias que você comprou a outra assinatura.

Se você comprou instâncias de máquinas virtuais reservadas do Azure, poderá alterar a configuração otimizar para a reserva. O desconto de reserva pode ser aplicado às VMs na mesma série ou você pode reservar data center capacidade para um tamanho de VM específico. E você deve tentar otimizar as reservas para que elas sejam totalmente usadas.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Ordem de reserva e reserva

Quando você adquire uma reserva, dois objetos são criados: **Ordem de reserva** e **reserva**.

No momento da compra, uma ordem de reserva tem uma reserva sob ela. Ações como dividir, mesclar, reembolsar parcial ou Exchange criam novas reservas sob a **ordem de reserva**.

Para exibir uma ordem de reserva, acesse **reservas** > selecione a reserva e, em seguida, clique na **ID da ordem de reserva**.

![Exemplo de detalhes do pedido de reserva mostrando a ID do pedido de reserva ](./media/billing-manage-reserved-vm-instance/reservation-order-details.png)

Uma reserva herda permissões de sua ordem de reserva.

## <a name="change-the-reservation-scope"></a>Alterar o escopo de reserva

 Seu desconto de reserva se aplica a máquinas virtuais, bancos de dados SQL, Azure Cosmos DB ou outros recursos que correspondam à sua reserva e executados no escopo de reserva. O contexto de cobrança depende da assinatura usada para comprar a reserva.

Para atualizar o escopo de uma reserva:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todas as** > **reservas**de serviços.
3. Selecione a reserva.
4. Selecione **Definições** > **Configuração**.
5. Altere o escopo.

Se você alterar de compartilhado para escopo único, só poderá selecionar assinaturas onde for o proprietário. Apenas as subscrições que fazem parte do mesmo contexto de faturação que a reserva podem ser selecionadas.

O escopo só se aplica a assinaturas individuais com tarifas pagas conforme o uso (oferece MS-AZR-0003P ou MS-AZR-0023P), a empresa oferece MS-AZR-0017P ou MS-AZR-0148P ou tipos de assinatura CSP.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Adicionar ou alterar os utilizadores que podem gerir uma reserva

Você pode delegar o gerenciamento de reserva adicionando pessoas a funções na ordem de reserva ou na reserva. Por padrão, a pessoa que coloca a ordem de reserva e o administrador da conta tem a função de proprietário na ordem de reserva e na reserva.

Você pode gerenciar o acesso a ordens de reservas e reservas independentemente das assinaturas que obtêm o desconto de reserva. Quando você concede a alguém permissões para gerenciar uma ordem de reserva ou a reserva, ele não concede permissão para gerenciar a assinatura. Da mesma forma, se você conceder a alguém permissões para gerenciar uma assinatura no escopo da reserva, ele não fornecerá direitos para gerenciar a ordem de reserva ou a reserva.

Para executar uma troca ou reembolso, o usuário deve ter acesso à ordem de reserva. Ao conceder permissões a alguém, é melhor conceder permissões para a ordem de reserva, não para a reserva.


Para delegar o gerenciamento de acesso para uma reserva:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **toda** > a**reserva** de serviços para listar as reservas às quais você tem acesso.
3. Selecione a reserva que você deseja delegar acesso a outros usuários.
4. Selecione **controlo de acesso (IAM)** .
5. Selecione **Adicionar** > **proprietário**da**função** > de atribuição de função. Se preferir dar acesso limitado, selecione uma função diferente.
6. Digite o endereço de email do usuário que você deseja adicionar como proprietário.
7. Selecione o utilizador e, em seguida, selecione **Guardar**.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dividir uma única reserva em duas reservas

 Depois de comprar mais de uma instância de recurso em uma reserva, talvez você queira atribuir instâncias dentro dessa reserva a assinaturas diferentes. Por padrão, todas as instâncias têm um escopo-uma única assinatura ou compartilhada. Por exemplo, você comprou 10 instâncias de reserva e especificou o escopo como assinatura A. Agora você pode querer alterar o escopo de sete reservas para a assinatura a e as três restantes para a assinatura B. a divisão de uma reserva permite que você distribua instâncias para o gerenciamento de escopo granular. Você pode simplificar a alocação para assinaturas escolhendo escopo compartilhado. Mas para fins de gerenciamento de custos ou orçamento, você pode alocar quantidades para assinaturas específicas.

 Você pode dividir uma reserva em duas reservas por meio do PowerShell, da CLI ou da API.

### <a name="split-a-reservation-by-using-powershell"></a>Dividir uma reserva usando o PowerShell

1. Obtenha a ID do pedido de reserva executando o seguinte comando:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Obter os detalhes de uma reserva:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Divida a reserva em duas e distribua as instâncias:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Você pode atualizar o escopo executando o seguinte comando:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Você pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, consulte [trocas e reembolsos de autoatendimento para reservas do Azure](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Alterar configuração de otimização para instâncias de VM reservadas

 Ao comprar uma instância de VM reservada, você escolhe a flexibilidade de tamanho da instância ou a prioridade da capacidade. A flexibilidade do tamanho da instância aplica o desconto de reserva a outras VMs no mesmo [grupo de tamanhos de VM](https://aka.ms/RIVMGroups). A prioridade de capacidade prioriza data center capacidade para suas implantações. Essa opção oferece confiança adicional em sua capacidade de iniciar as instâncias de VM quando você precisar delas.

Por padrão, quando o escopo da reserva é compartilhado, a flexibilidade do tamanho da instância é on. A capacidade de data center não é priorizada para implantações de VM.

Para as reservas em que o escopo é único, você pode otimizar a reserva de prioridade de capacidade em vez de flexibilidade de tamanho de instância de VM.

Para atualizar a configuração otimizar para a reserva:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todas as** > **reservas**de serviços.
3. Selecione a reserva.
4. Selecione **Definições** > **Configuração**.
5. Altere a configuração **otimizar para** .

## <a name="optimize-reservation-use"></a>Otimizar o uso de reserva

As economias de reserva do Azure resultam apenas do uso sustentado de recursos. Ao fazer uma compra de reserva, você paga um custo antecipado para o que é essencialmente 100% possível de uso de recursos em um prazo de um ou três anos. Tente maximizar sua reserva para obter o máximo de uso e economias possíveis. As seções a seguir explicam como monitorar uma reserva e otimizar seu uso.

### <a name="view-reservation-use-in-the-azure-portal"></a>Exibir uso de reserva no portal do Azure

Uma maneira de exibir o uso de reserva está na portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **todas as** > [**reservas**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) de serviços e observe a **utilização (%)** para uma reserva.  
  ![Imagem mostrando a lista de reservas](./media/billing-manage-reserved-vm-instance/reservation-list.png)
3. Selecione uma reserva.
4. Examine a tendência de uso da reserva ao longo do tempo.  
  ![Imagem mostrando uso de reserva ](./media/billing-manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>Exibir uso de reserva com a API

Se você for um cliente Enterprise Agreement (EA), poderá exibir programaticamente como as reservas em sua organização estão sendo usadas. Você Obtém a reserva não utilizada por meio de dados de uso. Ao examinar os encargos de reserva, tenha em mente que os dados são divididos entre custo real e custos amortizados. O custo real fornece dados para reconciliar sua conta mensal. Ele também tem custo de compra de reserva e detalhes do aplicativo de reserva. O custo amortizado é como o custo real, exceto pelo fato de que o preço efetivo para uso de reserva é rateado. As horas de reserva não utilizadas são mostradas nos dados de custo amortizados. Para obter mais informações sobre os dados de uso para clientes do EA, consulte [obter Enterprise Agreement custos de reserva e uso](billing-understand-reserved-instance-usage-ea.md).

Para outros tipos de assinatura, use os [resumos de reservas de API-List por ordem de reserva e reserva](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

### <a name="optimize-your-reservation"></a>Otimizar sua reserva

Se você descobrir que as reservas da sua organização estão sendo subutilizadas:

- Verifique se as máquinas virtuais que sua organização cria correspondem ao tamanho da VM que é para a reserva.
- Verifique se a flexibilidade do tamanho da instância está ativada. Para obter mais informações, consulte [gerenciar reservas – alterar a configuração otimizar para instâncias de VM reservadas](#change-optimize-setting-for-reserved-vm-instances).
- Altere o escopo da reserva para _compartilhado_ para que ele se aplique mais amplamente. Para obter mais informações, consulte [alterar o escopo de uma reserva](#change-the-reservation-scope).
- Considere a possibilidade de trocar a quantidade não utilizada. Para obter mais informações, consulte cancelamentos [e trocas](#cancel-exchange-or-refund-reservations).


## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as reservas do Azure, consulte os seguintes artigos:

- [O que são reservas para o Azure?](billing-save-compute-costs-reservations.md)

Comprar um plano de serviço:
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../sql-database/sql-database-reserved-capacity.md)
- [Pagar antecipadamente por Azure Cosmos DB recursos com Azure Cosmos DB capacidade reservada](../cosmos-db/cosmos-db-reserved-capacity.md)

Comprar um plano de software:
- [Pagar antecipadamente por planos de software Red Hat de reservas do Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Efetuar o pré-pagamento de planos de software SUSE das Reservas do Azure](../virtual-machines/linux/prepay-suse-software-charges.md)

Entender o desconto e o uso:
- [Entenda como o desconto de reserva da VM é aplicado](billing-understand-vm-reservation-charges.md)
- [Entenda como o desconto do plano de software Red Hat Enterprise Linux é aplicado](../billing/billing-understand-rhel-reservation-charges.md)
- [Entenda como o desconto do plano de software SUSE Linux Enterprise é aplicado](../billing/billing-understand-suse-reservation-charges.md)
- [Entender como outros descontos de reserva são aplicados](billing-understand-reservation-charges.md)
- [Entenda o uso de reserva para sua assinatura pré-paga](billing-understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro corporativo](billing-understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não incluídos com reservas](billing-reserved-instance-windows-software-costs.md)
