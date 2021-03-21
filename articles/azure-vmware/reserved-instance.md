---
title: Economize custos com a Azure VMware Solution reservado instância
description: Saiba como comprar uma instância reservada para a Azure VMware Solution.
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 227ea9274f70cc58468e7e0b3ddcf8239f5b7f5b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99550980"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Economize custos com a Azure VMware Solution

Quando se compromete com uma instância reservada da [Azure VMware Solution,](introduction.md)economiza dinheiro.  O desconto de reserva é aplicado automaticamente aos anfitriões Azure VMware Solution que correspondem ao âmbito e atributos da reserva. Uma compra de instância reservada cobre apenas a parte computacional do seu uso e inclui custos de licenciamento de software. 

## <a name="purchase-restriction-considerations"></a>Considerações de restrição de compra

Casos reservados estão disponíveis com algumas exceções.

-   **Nuvens** - As reservas só estão disponíveis nas regiões listadas nos [Produtos disponíveis por página da região.](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware)

-   **Contingente insuficiente** - Uma reserva a uma subscrição única/partilhada deve ter quota de anfitriões disponível na subscrição para o novo caso reservado. Pode [criar um pedido de aumento de quota](enable-azure-vmware-solution.md) para resolver esta questão.

-   **Oferecer elegibilidade**- Vai precisar de um [Acordo de Empresa Azure (EA)](../cost-management-billing/manage/ea-portal-agreements.md) com a Microsoft.

-   **Restrições de capacidade** - Em circunstâncias raras, o Azure limita a compra de novas reservas para a Azure VMware Solution host SKUs devido à baixa capacidade numa região.

## <a name="buy-a-reservation"></a>Comprar uma reserva

Pode comprar uma instância reservada de um anfitrião Azure VMware Solution no [portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Pode pagar a reserva [adiantadamente ou com pagamentos mensais.](../cost-management-billing/reservations/prepare-buy-reservation.md)

Estes requisitos aplicam-se à compra de um caso de anfitrião dedicado reservado:

-   Você deve estar numa função *proprietário* para pelo menos uma subscrição EA ou uma subscrição com uma taxa de pagamento como você-go.

-   Para as subscrições EA, deve ativar a opção **Add Reserved Instances** no [portal EA](https://ea.azure.com/). Se estiver desativado, deve ser um Administrador EA para a subscrição para o habilitar.

-   Para a subscrição no âmbito de um Plano Azure do Cloud Solution Provider (CSP), o parceiro deve adquirir as instâncias reservadas no portal Azure para o cliente. 

### <a name="buy-reserved-instances-for-an-ea-subscription"></a>Comprar instâncias reservadas para uma subscrição da EA

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione **Todos os serviços** > **Reservas**.

3. Selecione **Comprar De vez** em quando selecione **Azure VMware Solution**.

4. Insira os campos necessários. Os atributos selecionados que correspondem a correr Azure VMware Solution os anfitriões qualificam-se para o desconto de reserva.  Os atributos incluem o SKU, regiões (quando aplicável) e âmbito. O âmbito de reserva permite selecionar onde as poupanças na reserva serão aplicadas.

   Se tiver um acordo EA, pode utilizar a **opção Adicionar mais** instâncias rapidamente. A opção não está disponível para outros tipos de subscrição.

   | Campo        |  Descrição |
   | ------------ | ------------ |
   | Subscrição | A assinatura usada para pagar a reserva. Os custos de reserva são cobrados de acordo com o método de pagamento indicado na subscrição. O tipo de subscrição deve ser um acordo de empresa (números de oferta: MS-AZR-0017P ou MS-AZR-0148P), Microsoft Customer Agreement, ou uma subscrição individual com taxas pay-as-you-go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Os custos são deduzidos do saldo do Pré-pagamento do Azure (que antes se chamava alocação monetária), se disponível, ou cobrados como utilização excedida. Para uma subscrição com taxas de pagamento, os encargos são cobrados no cartão de crédito da subscrição ou num método de pagamento de fatura. |
   | Âmbito        | O âmbito da reserva pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar:<br><ul><li><b>Âmbito de grupo de recursos únicos</b> - Aplica o desconto de reserva aos recursos correspondentes apenas no grupo de recursos selecionado.</li><li><b>Âmbito de subscrição único</b> - Aplica o desconto de reserva aos recursos correspondentes na subscrição selecionada.</li><li><b>Âmbito partilhado</b> - Aplica o desconto de reserva aos recursos correspondentes em subscrições elegíveis que estão no contexto de faturação. Para os clientes da EA, o contexto de faturação é a inscrição. Para subscrições individuais com taxas pay as you go, o âmbito da faturação reside em todas as subscrições elegíveis criadas pelo administrador de conta.</li></ul>       |
   | Region       | A região de Azure que está coberta pela reserva.   |
   | Tamanho do anfitrião    | AV36    |
   | Termo         | Um ano ou três anos.  |
   | Quantidade     | O número de casos para comprar dentro da reserva. A quantidade é o número de anfitriões da Azure VMware Solution que podem obter o desconto de faturação.    |

### <a name="buy-reserved-instances-for-a-csp-subscription"></a>Comprar instâncias reservadas para uma subscrição de CSP

Os CSPs que pretendam adquirir instâncias reservadas para os seus clientes devem utilizar o procedimento **Admin Em nome da** [documentação](/partner-center/azure-plan-manage)do Centro parceiro . Para mais informações, consulte o Administrador em nome do vídeo [(AOBO).](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO)

1. Inscreva-se no [Partner Center](https://partner.microsoft.com).

2. Selecione **CSP** para aceder à área **clientes.**

3. Expandir os detalhes do cliente e selecionar **o Microsoft Azure Management Portal**. 

   :::image type="content" source="media/reserved-instances/csp-partner-center-aobo.png" alt-text="Área de clientes do Microsoft Partner Center" lightbox="media/reserved-instances/csp-partner-center-aobo.png":::

4. No portal Azure, selecione **Todas as** Reservas de  >  **Serviços**.

5. Selecione **Comprar De vez** em quando selecione **Azure VMware Solution**.

   :::image type="content" source="media/reserved-instances/csp-buy-ri-azure-portal.png" alt-text="Reservas do portal Microsoft Azure" lightbox="media/reserved-instances/csp-buy-ri-azure-portal.png":::

6. Insira os campos necessários. Os atributos selecionados que correspondem a correr Azure VMware Solution os anfitriões qualificam-se para o desconto de reserva.  Os atributos incluem o SKU, regiões (quando aplicável) e âmbito. O âmbito de reserva permite selecionar onde as poupanças na reserva serão aplicadas.

   | Campo        |  Descrição |
   | ------------ | ------------ |
   | Subscrição | A assinatura usada para pagar a reserva. Os custos de reserva são cobrados de acordo com o método de pagamento indicado na subscrição. O tipo de assinatura deve ser elegível, que neste caso é uma subscrição CSP|
   | Âmbito        | O âmbito da reserva pode abranger uma subscrição ou várias subscrições (âmbito partilhado). Se selecionar:<br><ul><li><b>Âmbito de grupo de recursos únicos</b> - Aplica o desconto de reserva aos recursos correspondentes apenas no grupo de recursos selecionado.</li><li><b>Âmbito de subscrição único</b> - Aplica o desconto de reserva aos recursos correspondentes na subscrição selecionada.</li><li><b>Âmbito partilhado</b> - Aplica o desconto de reserva aos recursos correspondentes em subscrições elegíveis que estão no contexto de faturação. Para os clientes da EA, o contexto de faturação é a inscrição. Para subscrições individuais com taxas pay as you go, o âmbito da faturação reside em todas as subscrições elegíveis criadas pelo administrador de conta.</li></ul>       |
   | Region       | A região de Azure que está coberta pela reserva.   |
   | Tamanho do anfitrião    | AV36    |
   | Termo         | Um ano ou três anos.  |
   | Quantidade     | O número de casos para comprar dentro da reserva. A quantidade é o número de anfitriões da Azure VMware Solution que podem obter o desconto de faturação.     |

Para saber mais sobre como ver as reservas adquiridas para o seu cliente, consulte [as reservas do Azure como um artigo do Cloud Solution Provider (CSP).](../cost-management-billing/reservations/how-to-view-csp-reservations.md)

## <a name="usage-data-and-reservation-usage"></a>Dados de utilização e utilização da reserva

O seu uso que obtém um desconto de reserva tem um preço efetivo de zero. Pode ver qual a instância Azure VMware Solution recebeu o desconto de reserva para cada reserva.

Para mais informações sobre como os descontos de reserva aparecem nos dados de utilização:

- Para clientes da EA, consulte [o uso da reserva Understand Azure para a sua inscrição na Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- Para subscrições individuais, consulte [o uso da reserva Understand Azure para a sua subscrição Pay-As-You-Go](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="change-a-reservation-after-purchase"></a>Alterar uma reserva após a compra

Pode efetuar estas alterações a uma reserva após a compra:

-   Atualizar âmbito da reserva

-   Flexibilidade do tamanho do exemplo (se aplicável)

-   Propriedade

Você também pode dividir uma reserva em pedaços menores ou fundir reservas. Nenhuma das alterações causa uma nova transação comercial ou altera a data de fim da reserva.

Para mais informações sobre reservas geridas pela CSP, consulte [as reservas do Microsoft Azure para clientes que utilizem o Partner Center, o portal Azure ou APIs](/partner-center/azure-reservations).



>[!NOTE]
>Uma vez adquirida a sua reserva, não poderá efetuar este tipo de alterações diretamente:
>
> - Uma região de reserva existente
> - SKU
> - Quantidade
> - Duração
>
>No entanto, pode *trocar* uma reserva se quiser fazer alterações.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

Os CSPs podem cancelar, trocar ou reembolsar reservas, com determinadas limitações, adquiridas para o seu cliente. Para mais informações, consulte [Gerir, cancelar, trocar ou reembolsar reservas do Microsoft Azure para os clientes.](/partner-center/azure-reservations-manage)

## <a name="next-steps"></a>Passos seguintes

Agora que cobriu a compra de um caso reservado da Azure VMware Solution, talvez queira saber:

- [Criar uma avaliação da Solução VMware Azure](../migrate/how-to-create-azure-vmware-solution-assessment.md).
- [Gestão da DHCP para a Azure VMware Solution](manage-dhcp.md).
- [Gestão do ciclo de vida da Azure VMware Solution VMs](lifecycle-management-of-azure-vmware-solution-vms.md).
 