---
title: Gerir Reservas do Azure
description: Aprenda a gerir as Reservas do Azure. Veja os passos para alterar o âmbito de reserva, dividir uma reserva e otimizar a utilização das reservas.
ms.service: cost-management-billing
ms.subservice: reservations
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: how-to
ms.date: 04/21/2021
ms.author: banders
ms.openlocfilehash: e8a5e6d40b745efb7fcd50919cd27664b3406b7d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862854"
---
# <a name="manage-reservations-for-azure-resources"></a>Gerir Reservas para recursos do Azure

Depois de comprar uma reserva do Azure, poderá precisar de aplicar a reserva a outra subscrição, mudar quem a pode gerir ou mudar o âmbito da mesma. Também pode dividir uma reserva em duas para aplicar algumas das instâncias que comprou para outra subscrição.

Se tiver comprado Azure Reserved Virtual Machine Instances, pode alterar a definição de otimização da reserva. O desconto da reserva pode aplicar-se às VMs na mesma série ou pode reservar capacidade de datacenter para um tamanho de VM específico. Deve tentar otimizar as reservas para que sejam completamente utilizadas.

*A permissão necessária para gerir uma reserva é distinta da permissão de subscrição.*

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Encomenda da Reserva e Reserva

Quando compra uma reserva, são criados dois objetos: **Encomenda de Reserva** e **Reserva**.

No momento da compra, a Encomenda de Reserva contém uma Reserva. Ações como dividir, unir, reembolsar parcialmente ou trocar criam novas reservas na **Encomenda de Reserva**.

Para ver uma Encomenda de Reserva, aceda a **Reservas** > selecione a reserva e **ID da Encomenda de reserva**.

![Exemplo dos detalhes da encomenda de reserva que mostra o ID da Encomenda de reserva ](./media/manage-reserved-vm-instance/reservation-order-details.png)

Uma reserva herda as permissões da encomenda de reserva. Para trocar ou reembolsar uma reserva, o utilizador deve ser adicionado à ordem de reserva.

## <a name="change-the-reservation-scope"></a>Alterar o âmbito de reserva

 O desconto de reserva aplica-se a máquinas virtuais, bases de dados SQL, Azure Cosmos DB ou outros recursos que correspondam à sua reserva e que são executados no âmbito da reserva. O contexto de faturação depende da subscrição utilizada para comprar a reserva.

Atualizar o âmbito de uma reserva:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione a reserva.
4. Selecione **Definições** > **Configuração**.
5. Altere o âmbito.

Se alterar o âmbito de partilhado para individual, só poderá selecionar subscrições em que é o proprietário. Só podem ser selecionadas as subscrições que fazem parte do mesmo contexto de faturação da reserva.

O âmbito só se aplica às subscrições individuais com tarifas pay as you go (ofertas MS-AZR-0003P ou MS-AZR-0023P), oferta Enterprise MS-AZR-0017P ou MS-AZR-0148P ou tipos de subscrição CSP.

## <a name="who-can-manage-a-reservation-by-default"></a>Quem pode gerir reservas por predefinição

Por predefinição, os utilizadores seguintes podem ver e gerir as reservas:

- A pessoa que comprou a reserva e o proprietário da conta para a subscrição de faturação obtém acesso Azure RBAC à ordem de reserva.
-  Os contribuintes do Enterprise Agreement e do Microsoft Customer Agreement podem gerir todas as reservas da Cost Management + Billing > Desemescagens de Reservas > selecione o banner azul.

Para permitir que outras pessoas façam a gestão de reservas, tem duas opções:

- Delegar a gestão do acesso a encomendas de reservas individuais:
    1. Inicie sessão no [portal do Azure](https://portal.azure.com).
    1. Selecione **Todos os Serviços** > **Reserva** para listar as reservas às quais tem acesso.
    1. Selecione a reserva cujo o acesso quer delegar a outros utilizadores.
    1. Nos Detalhes da reserva, selecione a encomenda da reserva.
    1. Selecione **Controlo de acesso (IAM)** .
    1. Selecione **Adicionar atribuição de função** > **Função** > **Proprietário**. Se quiser conceder acesso limitado, selecione uma função diferente.
    1. Introduza o endereço de e-mail do utilizador que quer adicionar como proprietário.
    1. Selecione o utilizador e, em seguida, selecione **Guardar**.

- Adicionar um utilizador como administrador de faturação a um Contrato Enterprise ou Contrato de Cliente Microsoft:
    - Num Contrato Enterprise, adicione utilizadores com a função _Administrador do Enterprise_ para ver e gerir todas as encomendas de reservas que se aplicam a esse Contrato Enterprise. Os utilizadores com a função _Administrador do Enterprise (só de leitura)_ só podem ver a reserva. Os administradores de departamentos e os proprietários de conta não podem ver as reservas, _a não ser_ que sejam adicionados explicitamente às mesmas com o Controlo de acesso (IAM). Para obter mais informações, veja [Gerir funções empresariais do Azure](../manage/understand-ea-roles.md).

        _Os administradores Enterprise podem assumir a propriedade de uma encomenda de reserva e podem adicionar outros utilizadores a uma reserva com o Controlo de acesso (IAM)._
    - Num Contrato de Cliente Microsoft, os utilizadores com a função de proprietário de perfil de faturação ou a função contribuidor de perfil de faturação podem gerir todas as compras de reservas feitas com o perfil de faturação. Os leitores de perfil de faturação e os gestores de faturas podem ver todas as reservas que são pagas com o perfil de faturação. No entanto, não podem fazer alterações às reservas.
    Para obter mais informações, veja [Funções e tarefas do perfil de faturação](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="how-billing-administrators-view-or-manage-reservations"></a>Como os administradores de faturação veem ou gerem as reservas

1. Aceda a **Cost Management + Faturação** e, no lado esquerdo da página, selecione **Transações de Reservas**.
2. Se tiver as permissões de faturação necessárias, pode ver e gerir reservas. Se não vir reservas, confirme que tem sessão iniciada com o inquilino do Azure AD em que as reservas foram criadas.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dividir uma reserva individual em duas

 Depois de comprar mais do que uma instância de recursos numa reserva, poderá querer atribuir instâncias nessa reserva a subscrições diferentes. Por predefinição, todas as instâncias têm um âmbito - subscrição individual, grupo de recursos ou partilhada. Por exemplo, comprou uma reserva para dez instâncias de VM e especificou o âmbito como a subscrição A. Agora, pretende alterar o âmbito para sete instâncias de VM para a subscrição A e as três restantes para a subscrição B. A divisão de uma reserva permite-lhe fazer isto. Depois de dividir uma reserva, o ReservationID original é cancelado e são criadas duas reservas novas. A divisão não afeta a encomenda de reserva. Não há nenhuma transação comercial nova com divisão e as novas reservas têm a mesma data de fim da que foi dividida.

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

 Quando compra um Reserved VM Instance, escolhe a flexibilidade de tamanho de instância ou a prioridade de capacidade. A flexibilidade de tamanho da instância aplica o desconto de reserva a outras VMs no mesmo [grupo de tamanho de VM](../../virtual-machines/reserved-vm-instance-size-flexibility.md). A prioridade de capacidade designa a capacidade do datacenter mais importante para as suas implementações. Esta opção oferece-lhe uma garantia adicional no que diz respeito à capacidade de lançar as instâncias de VM quando precisar delas.

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
  ![Imagem que mostra a utilização da reserva](./media/manage-reserved-vm-instance/reservation-utilization-trend.png)

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
 - [Ver utilização da reserva](reservation-utilization.md)
 - [Troca e reembolso](exchange-and-refund-azure-reservations.md)
 - [Renovar reservas](reservation-renew.md)
 - [Transfers entre inquilinos](troubleshoot-reservation-transfers-between-tenants.md)
 - [Encontre um comprador de reservas a partir de registos Azure](find-reservation-purchaser-from-logs.md)
 - [Renovar uma reserva](reservation-renew.md)
