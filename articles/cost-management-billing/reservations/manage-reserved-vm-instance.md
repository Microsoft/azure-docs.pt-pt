---
title: Gerir Reservas do Azure
description: Aprenda a gerir as Reservas do Azure. Veja os passos para alterar o âmbito de reserva, dividir uma reserva e otimizar a utilização das reservas.
ms.service: cost-management-billing
ms.subservice: reservations
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 300ac0996d9dcb9ecb07abd3392447e393c1781b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151620"
---
# <a name="manage-reservations-for-azure-resources"></a>Gerir Reservas para recursos do Azure

Depois de comprar uma reserva do Azure, poderá precisar de aplicar a reserva a outra subscrição, mudar quem a pode gerir ou mudar o âmbito da mesma. Também pode dividir uma reserva em duas para aplicar algumas das instâncias que comprou para outra subscrição.

Caso tenha comprado o Azure Reserved VM Instances, poderá alterar a definição de otimização da reserva. O desconto da reserva pode aplicar-se às VMs na mesma série ou pode reservar capacidade de datacenter para um tamanho de VM específico. Além disso, deve tentar otimizar as reservas para que sejam completamente utilizadas.

*A permissão necessária para gerir uma reserva é distinta da permissão de subscrição.*

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Encomenda da Reserva e Reserva

Quando compra uma reserva, são criados dois objetos: **Encomenda de Reserva** e **Reserva**.

No momento da compra, a Encomenda de Reserva contém uma Reserva. Ações como dividir, unir, reembolsar parcialmente ou trocar criam novas reservas na **Encomenda de Reserva**.

Para ver uma Encomenda de Reserva, aceda a **Reservas** > selecione a reserva e, em seguida, clique no **ID da Encomenda de reserva**.

![Exemplo dos detalhes da encomenda de reserva que mostra o ID da Encomenda de reserva ](./media/manage-reserved-vm-instance/reservation-order-details.png)

Uma reserva herda as permissões da encomenda de reserva.

## <a name="change-the-reservation-scope"></a>Alterar o âmbito de reserva

 O desconto de reserva aplica-se a máquinas virtuais, bases de dados SQL, Azure Cosmos DB ou outros recursos que correspondam à sua reserva e que são executados no âmbito da reserva. O contexto de faturação depende da subscrição utilizada para comprar a reserva.

Atualizar o âmbito de uma reserva:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione a reserva.
4. Selecione **Definições** > **Configuração**.
5. Altere o âmbito.

Se alterar o âmbito de partilhado para individual, só poderá selecionar subscrições em que é o proprietário. Apenas as subscrições que fazem parte do mesmo contexto de faturação que a reserva podem ser selecionadas.

O âmbito só se aplica às subscrições individuais com tarifas pay as you go (ofertas MS-AZR-0003P ou MS-AZR-0023P), oferta Enterprise MS-AZR-0017P ou MS-AZR-0148P ou tipos de subscrição CSP.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Adicionar ou alterar os utilizadores que podem gerir uma reserva

Pode delegar a gestão de reservas. Para tal, adicione pessoas às funções no pedido de reservas ou na reserva. Por predefinição, a pessoa que faz o pedido de reserva e o administrador de conta têm a função Proprietário no pedido de reservas e na reserva.

Pode gerir o acesso às encomendas de reservas e às reservas *independentemente das subscrições* que obtêm o desconto de reserva. Quando concede permissões a alguém para gerir uma encomenda de reserva ou a reserva, não é concedida permissão para gerir a subscrição. Da mesma forma, caso conceda permissões a alguém para gerir uma subscrição no âmbito de reserva, não serão concedidos direitos para gerir a encomenda de reserva ou a reserva.

Para realizar uma troca ou reembolso, o utilizador tem de ter acesso à encomenda de reserva. Ao conceder permissões a alguém, o melhor será conceder permissões para a encomenda de reserva e não para a reserva.

Para delegar a gestão do acesso de uma reserva:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione**Todos os Serviços** > **Reserva** para listar as reservas às quais tem acesso.
3. Selecione a reserva cujo o acesso quer delegar a outros utilizadores.
4. Selecione **Controlo de acesso (IAM)** .
5. Selecione **Adicionar atribuição de função** > **Função** > **Proprietário**. Se preferir dar acesso limitado, selecione uma função diferente.
6. Introduza o endereço de e-mail do utilizador que quer adicionar como proprietário.
7. Selecione o utilizador e, em seguida, selecione **Guardar**.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dividir uma reserva individual em duas

 Depois de comprar mais do que uma instância de recursos numa reserva, poderá querer atribuir instâncias nessa reserva a subscrições diferentes. Por predefinição, todas as instâncias têm um âmbito - subscrição individual, grupo de recursos ou partilhada. Por exemplo, comprou uma reserva para 10 instâncias VM e especificou o âmbito como subscrição A. Agora, pretende alterar o âmbito para 7 instâncias VM para a subscrição A e as três restantes para a subscrição B. A divisão de uma reserva permite-lhe fazer isto. Depois de dividir uma reserva, o ReservationID original é cancelado e são criadas duas reservas novas. A divisão não afeta a encomenda de reserva - não há nenhuma transação comercial nova com divisão e as novas reservas têm a mesma data de fim da que foi dividida.

 Pode dividir uma reserva em duas através do PowerShell, da CLI ou da API.

### <a name="split-a-reservation-by-using-powershell"></a>Dividir uma reserva com o PowerShell

1. Obtenha o ID da encomenda de reserva ao executar o seguinte comando:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Obtenha os detalhes de uma reserva:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Divida a reserva em duas e distribua as instâncias:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Pode atualizar o âmbito ao executar o seguinte comando:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](exchange-and-refund-azure-reservations.md).

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Alterar a definição de otimização do Reserved VM Instances

 Quando compra um Reserved VM Instance, escolhe a flexibilidade de tamanho de instância ou a prioridade de capacidade. A flexibilidade de tamanho da instância aplica o desconto de reserva a outras VMs no mesmo [grupo de tamanho de VM](../../virtual-machines/reserved-vm-instance-size-flexibility.md). A prioridade de capacidade prioriza a capacidade de datacenter das implementações. Esta opção oferece-lhe uma garantia adicional no que diz respeito à capacidade de lançar as instâncias de VM quando precisar delas.

Por predefinição, quando o âmbito da reserva é partilhado, a flexibilidade de tamanho de instância fica ativa. A capacidade de datacenter não é priorizada para as implementações de VM.

Para as reservas com âmbito individual, pode otimizar a reserva para a prioridade de capacidade em vez de flexibilidade de tamanho de instância de VM.

Para atualizar a definição de otimização da reserva:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** > **Reservas**.
3. Selecione a reserva.
4. Selecione **Definições** > **Configuração**.  
  ![Exemplo que mostra o item de configuração](./media/manage-reserved-vm-instance/add-product03.png)
5. Altere a definição **Otimizar para**.  
  ![Exemplo que mostra a definição Otimizar para](./media/manage-reserved-vm-instance/instance-size-flexibility-option.png)

## <a name="optimize-reservation-use"></a>Otimizar a utilização da reserva

As poupanças das reservas do Azure só resultam de uma utilização de recursos sustentada. Ao fazer uma compra de reservas, paga por aquilo que é, essencialmente, uma utilização de recursos 100% possível num período de um ou três anos. Tente maximizar a reserva para obter o máximo de utilização e poupanças possível. As secções a seguir explicam como monitorizar uma reserva e otimizar a utilização.

### <a name="view-reservation-use-in-the-azure-portal"></a>Ver a utilização da reserva no portal do Azure

Uma forma de ver a utilização da reserva é através do portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > [**Reservas**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) e observe a **Utilização (%)** relativa a uma reserva.  
  ![Imagem que mostra a lista de reservas](./media/manage-reserved-vm-instance/reservation-list.png)
3. Selecione uma reserva.
4. Analise a tendência de utilização da reserva ao longo do tempo.  
  ![Imagem que mostra a utilização da reserva ](./media/manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>Ver a utilização da reserva com a API

Se for um cliente com Contrato Enterprise (EA), poderá visualizar de forma programática a forma como as reservas na sua organização estão a ser utilizadas. Obtém a reserva não utilizada através dos dados de utilização. Ao analisar os custos da reserva, tenha em consideração que os dados estão divididos entre custo real e custos amortizados. O custo real fornece os dados para reconciliar a fatura mensal. Também inclui o custo das compras das reservas e os detalhes da aplicação das reservas. O custo amortizado é semelhante ao custo real, exceto pelo facto de o preço efetivo da utilização da reserva ser calculado proporcionalmente. As horas de reserva não utilizadas são apresentadas nos dados de custos amortizados. Para obter mais informações sobre os dados de utilização dos clientes EA, veja [Obter a utilização e os custos das reservas do Contrato Enterprise](understand-reserved-instance-usage-ea.md).

Para outros tipos de subscrições, utilize a API [Resumos de Reservas – Lista por Encomenda de Reserva e Reserva](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

### <a name="optimize-your-reservation"></a>Otimizar a reserva

Se observar que as reservas da sua organização estão a ser pouco utilizadas:

- Confirme que as máquinas virtuais que a sua organização cria correspondem ao tamanho da VM que está na reserva.
- Confirme que a flexibilidade de tamanho da instância está ativada. Para obter mais informações, veja [Gerir reservas – Alterar a definição de otimização para Instâncias de VMs Reservadas](#change-optimize-setting-for-reserved-vm-instances).
- Altere o âmbito de reserva para _partilhado_ para que seja aplicado mais amplamente. Para obter mais informações, veja [Alterar o âmbito de uma reserva](#change-the-reservation-scope).
- Considere trocar a quantidade não utilizada. Para obter mais informações, veja [Cancelamentos e trocas](#cancel-exchange-or-refund-reservations).


## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

- [O que são as reservas do Azure?](save-compute-costs-reservations.md)

Para comprar um plano de serviço:
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../../azure-sql/database/reserved-capacity-overview.md)
- [Efetuar pré-pagamento de recursos do Azure Cosmos DB com capacidade reservada do Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)

Para comprar um plano de software:
- [Efetuar pré-pagamento dos planos de software Red Hat nas Reservas do Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Efetuar o pré-pagamento de planos de software SUSE das Reservas do Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)

Compreender o desconto e a utilização:
- [Compreender como o desconto das reservas das VMs é aplicado](../manage/understand-vm-reservation-charges.md)
- [Compreender como o desconto do plano de software Red Hat Enterprise Linux é aplicado](understand-rhel-reservation-charges.md)
- [Compreender como o desconto do plano de software SUSE Linux Enterprise é aplicado](understand-suse-reservation-charges.md)
- [Compreender como outros descontos das reservas são aplicados](understand-reservation-charges.md)
- [Compreender a utilização de reservas na sua subscrição Pay As You Go](understand-reserved-instance-usage.md)
- [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)
- [Custos de software Windows não incluídos nas Reservas](reserved-instance-windows-software-costs.md)