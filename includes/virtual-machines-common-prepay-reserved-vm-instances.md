---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/22/2019
ms.openlocfilehash: f7fbbb421a01b268b784a6d6c875cd959a5d1d42
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407788"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Efetue o pré-pagamento de máquinas virtuais com instâncias de VM reservadas do Azure

Efetue o pré-pagamento de máquinas virtuais e poupe dinheiro com as instâncias de Máquina Virtual reservada do Azure (VM). Para obter mais informações, consulte [oferta do Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Pode comprar uma instância de VM reservada [portal do Azure](https://portal.azure.com). Para comprar uma instância:

- Deve estar numa função de proprietário de, pelo menos, uma empresa ou uma subscrição pay as you go.
- Para subscrições Enterprise, **adicionar as instâncias reservadas** tem de estar ativada no [portal EA](https://ea.azure.com). Em alternativa, se essa definição estiver desativada, tem de ser um administrador de EA da subscrição.
- Para o programa de fornecedor de soluções Cloud (CSP), apenas o admin de agentes ou agentes de vendas podem comprar reservas.

O desconto de reserva é aplicado automaticamente ao número de máquinas virtuais que correspondem à âmbito de reserva e atributos em execução. Pode atualizar o âmbito da reserva através de [portal do Azure](https://portal.azure.com), PowerShell, CLI, ou por meio da API.

## <a name="determine-the-right-vm-size-before-you-buy"></a>Determinar o tamanho VM correto antes de comprar

Antes de comprar uma reserva, deve determinar o tamanho da VM que precisa. As seções a seguir ajudará a determinar o tamanho VM correto.

### <a name="use-reservation-recommendations"></a>Utilize recomendações de reserva

Pode utilizar as recomendações de reserva para ajudar a determinar as reservas que deve comprar.

- Recomendações de compra e a quantidade recomendada são Mostrar ao comprar uma instância reservada de VM no portal do Azure.
- O Assistente do Azure fornece recomendações de compra de assinaturas individuais.  
- Pode utilizar as APIs para obter recomendações de compra para o âmbito partilhado e o âmbito da subscrição única. Para obter mais informações, consulte [reservados a recomendação de compra de instância APIs para os clientes empresariais](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Para clientes com contrato EA, comprar recomendações para partilhados e âmbitos de subscrição individual estão disponíveis com o [pacote de conteúdos do Power BI do Azure consumo Insights](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="analyze-your-usage-information"></a>Analisar as suas informações de utilização
Deve analisar as informações de utilização para ajudar a determinar quais as reservas deve adquirir.

Dados de utilização estão disponíveis no ficheiro de utilização e APIs. Usá-los em conjunto para determinar qual reserva para comprar. Deve verificar para instâncias de VM com elevada utilização numa base diária para determinar a quantidade de reservas para comprar.

Evitar a `Meter` subcategoria e `Product` campos nos dados de utilização. Eles não distinguem entre tamanhos de VM que utilizam o armazenamento premium. Se utilizar estes campos para determinar o tamanho da VM para a compra de reserva, pode comprar o tamanho errado. Não receberá o desconto de reserva que espera. Em vez disso, consulte o `AdditionalInfo` campo no seu ficheiro de utilização ou a API de utilização para determinar o tamanho VM correto.

### <a name="purchase-restriction-considerations"></a>Considerações de restrição de compra

Instâncias de VM reservadas estão disponíveis para a maioria dos tamanhos de VM com algumas exceções. Descontos de reserva não se aplicam para as VMs seguintes:

- **Série VM** -série A, série Av2 ou série G.

- **As VMs em pré-visualização** -qualquer série de VM ou o tamanho que está em pré-visualização.

- **Nuvens** -as reservas não estão disponíveis para compra em regiões da Alemanha ou China.

- **Quota insuficiente** -uma reserva de que tem um âmbito a uma subscrição individual tem de ter a quota de vCPU disponível na subscrição para a nova RI. Por exemplo, se a subscrição de destino tem um limite de quota de 10 vCPUs para a série D, em seguida, é possível comprar uma reserva para 11 Standard_D1 instâncias. A verificação de quota para as reservas inclui VMs já implementadas na subscrição. Por exemplo, se a subscrição tem uma quota de 10 vCPUs para a série D e tem duas instâncias de standard_D1 implementadas, em seguida, pode comprar uma reserva para 10 instâncias standard_D1 nesta subscrição. Pode [aspa criar pedido de aumento](../articles/azure-supportability/resource-manager-core-quotas-request.md) para resolver este problema.

- **Restrições de capacidade** - em situações raras, limites de tamanhos a compra de reservas de novo para o subconjunto da VM, devido à baixa capacidade numa região do Azure.

## <a name="buy-a-reserved-vm-instance"></a>Comprar uma instância VM reservadas

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** > **reservas**.
3. Selecione **adicionar** para comprar uma reserva nova.
4. Preencha os campos obrigatórios. Instâncias de VM em execução que correspondam aos atributos que selecionar se qualificar para obter o desconto de reserva. O número real de suas instâncias de VM que obter o desconto depende do escopo e quantidade selecionado.

    | Campo      | Descrição|
    |------------|--------------|
    |Name        |O nome desta reserva.|
    |Subscrição|A subscrição utilizada para pagar a reserva. O método de pagamento da subscrição é cobrado os custos iniciais para a reserva. O tipo de subscrição tem de ser um contrato enterprise (números da oferta: MS-AZR-0017P ou MS-AZR - 0148 P) ou pay as you go (números da oferta: MS-AZR-0003P ou MS-AZR - 0023 P). Para uma subscrição Enterprise, os custos são deduzidos do saldo de fidelização monetária da inscrição ou cobrados como utilização excedida. Para a subscrição Pay As You Go, os custos são debitados no cartão de crédito ou cobrados de acordo com o método de pagamento indicado na subscrição.|    
    |Âmbito       |Âmbito da reserva pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar: <ul><li>Subscrição individual - o desconto de reserva é aplicada a VMs nesta subscrição. </li><li>Partilhado - o desconto de reserva é aplicado a VMs em execução no caso de subscrições no seu contexto de faturação. Para os clientes empresariais, o escopo compartilhado é a inscrição e inclui todas as subscrições na inscrição. Para clientes pay as you go, o âmbito partilhado é todas as subscrições pay as you go a criada pelo administrador de conta.</li></ul>|
    |Região    |A região do Azure que é abrangida pela reserva.|    
    |Tamanho da VM     |O tamanho das instâncias de VM.|
    |Otimizar para     |Flexibilidade de tamanho de instância VM aplica o desconto de reserva às outras VMs no mesmo [grupo de tamanho VM](https://aka.ms/RIVMGroups). Prioridade de capacidade dá prioridade à capacidade do Centro de dados para as suas implementações. Isso oferece a confiança adicional na sua habilidade de iniciar as instâncias VM quando precisar delas. Prioridade de capacidade apenas está disponível quando o âmbito da reserva é subscrição única. |
    |Termo        |Um ano ou três anos.|
    |Quantidade    |O número de instâncias que está a ser comprado dentro a reserva. A quantidade é o número de instâncias VM que podem obter o desconto de faturação em execução. Por exemplo, se estiver a executar 10 Standard_D2 VMs nos EUA leste, em seguida, tem de especificar quantidade como 10 para maximizar o benefício de todas as máquinas em execução. |
5. Pode ver o custo da reserva quando seleciona **calcular o custo**.

    ![Captura de ecrã antes de submeter a compra de reserva](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Selecione **Comprar**.
7. Selecione **ver esta reserva** para ver o estado da sua compra.

    ![Captura de tela depois de submeter a compra de reserva](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="change-a-reservation-after-purchase"></a>Alterar uma reserva após a compra

Pode efetuar os seguintes tipos de alterações a uma reserva após a compra:

- Atualizar o âmbito da reserva
- Flexibilidade de tamanho de instância (se aplicável)
- Propriedade

Também pode dividir uma reserva em segmentos menores e de intercalação já dividir as reservas. Nenhuma das alterações fazer com que uma nova transação comercial ou alterar a data de fim da reserva.

Não pode efetuar os seguintes tipos de alterações após a compra, diretamente:

- Região de uma reserva existente
- SKU
- Quantidade
- Duração

No entanto, pode *exchange* uma reserva se pretender efetuar alterações.

## <a name="cancellations-and-exchanges"></a>Cancelamentos e trocas

Se precisar de cancelar a sua reserva, pode ser cobrada uma taxa de cessação antecipada de 12%. Os reembolsos baseiam-se no preço mais baixo do preço de compra ou do preço atual da reserva. Os reembolsos estão limitados a 50 000 $ por ano. O reembolso que receber corresponde ao saldo rateado restante menos a taxa de cessação antecipada de 12%. Para pedir um cancelamento, vá para a reserva no portal do Azure e selecione **reembolsar** para criar um pedido de suporte.

Se precisar de alterar a sua reserva de Instâncias de VM Reservadas para outra região, grupo de tamanho de VM ou período, pode trocá-la por outra reserva de valor igual ou superior. A data de início do período da nova reserva não transita da reserva trocada. O termo de 1 ou 3 anos começa a partir ao criar a nova reserva. Para pedir uma troca, vá para a reserva no portal do Azure e selecione **Exchange** para criar um pedido de suporte.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos Seguintes

- Para saber como gerir uma reserva, consulte [Gerir reservas de Azure](../articles/billing/billing-manage-reserved-vm-instance.md).
- Para saber mais sobre as reservas do Azure, veja os artigos seguintes:
    - [Quais são as reservas do Azure?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Gerir reservas no Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Compreender como o desconto de reserva é aplicado](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Compreender a utilização de reserva para a sua subscrição pay as you go](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Compreender a utilização de reserva para inscrição da sua empresa](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Custos de software do Windows não incluídos com reservas](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Reservas do Azure no programa de fornecedor de soluções (CSP) do parceiro Center na nuvem](https://docs.microsoft.com/partner-center/azure-reservations)
