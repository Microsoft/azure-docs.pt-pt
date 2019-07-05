---
title: O que é o Azure Reservations?
description: Saiba mais sobre as reservas do Azure e preços para guardar nas suas máquinas virtuais, bases de dados SQL, Azure Cosmos DB e outros custos do recurso.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: banders
ms.openlocfilehash: cd0a70aa0fb5096c5b0157ae078c961da03109bc
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565326"
---
# <a name="what-are-azure-reservations"></a>O que é o Azure Reservations?

Reservas do Azure ajudam a economizar dinheiro ao pagar previamente para um ano ou três anos de máquinas virtuais, base de dados SQL de computação capacidade, débito do Azure Cosmos DB ou outros recursos do Azure. Pré-pagamento permite-lhe obter um desconto sobre os recursos que utiliza. As reservas podem reduzir significativamente a sua máquina virtual, a computação de base de dados SQL, Azure Cosmos DB, ou outros recursos de custos até 72% em preços pay as you go. Reservas de fornecem um desconto de faturação e não afetam o estado de tempo de execução dos seus recursos.

Pode comprar uma reserva [portal do Azure](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Por que devo adquirir uma reserva?

Se tiver máquinas virtuais, Azure Cosmos DB ou bases de dados do SQL que são executadas durante longos períodos de tempo, ter de comprar uma reserva dá-lhe a opção mais económica. Por exemplo, quando executar quatro instâncias de um serviço sem uma reserva continuamente, lhe é cobrada às tarifas pay as you go. Se comprar uma reserva para esses recursos, obtém imediatamente o desconto de reserva. Os recursos já não são cobrados às tarifas pay as you go.

## <a name="charges-covered-by-reservation"></a>Custos abrangidos por reserva

Planos de serviço:

- **Instância da Máquina Virtual de reservado** -uma reserva abrange apenas os custos de computação de máquina virtual. Não cobre custos adicionais de software, sistema de rede ou armazenamento.
- **O Azure Cosmos DB a capacidade de reserva** -uma reserva abrange o débito aprovisionado para os seus recursos. Não abrange a armazenamento e os encargos de rede.
- **Base de dados SQL reservadas vCore** - só os custos de computação estão incluídos com uma reserva. A licença é faturada em separado.

Para as máquinas virtuais do Windows e a base de dados SQL, que podem ser cobertos os custos de licenciamento com [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Quem é elegível para comprar uma reserva?

Para comprar um plano, tem de ter uma função de proprietário da subscrição de uma empresa (MS-AZR - 0017P ou MS-AZR - 0148P) ou uma subscrição pay as you go (MS-AZR - 003P ou MS-AZR - 0023P). Fornecedores de soluções cloud, podem utilizar o portal do Azure ou [Centro de parceiros](/partner-center/azure-reservations) comprar reservas do Azure.

Clientes com contrato EA podem limitar as compras aos administradores EA, desativando a **adicionar as instâncias reservadas** opção no Portal do EA. Os administradores EA tem de ser um proprietário da subscrição pelo menos uma subscrição de EA para comprar uma reserva. A opção é útil para as empresas que querem uma equipe centralizada para comprar reservas para centros de custos diferentes. Após a compra, as equipes centralizadas podem adicionar os proprietários do Centro de custos para as reservas. Os proprietários podem, em seguida, definir o âmbito da reserva para suas assinaturas. A equipe central não precisa ter acesso de proprietário da subscrição em que a reserva é comprada.

Um desconto de reserva aplica-se apenas aos recursos associados com as assinaturas adquiridas por meio de planos individuais com as tarifas pay as you go, Enterprise e CSP.

## <a name="scope-reservations"></a>Reservas de âmbito

Pode definir o âmbito uma reserva para grupos de subscrição ou ao recurso. Definir o âmbito de uma reserva seleciona em que se aplicam a economia de reserva. Quando definir o âmbito de reserva para um grupo de recursos, os descontos de reserva aplicam-se apenas ao grupo de recursos — não a subscrição completa.

### <a name="reservation-scoping-options"></a>Opções de escopo de reserva

Recurso de grupo de âmbito tem três opções para definir âmbito de uma reserva, consoante as suas necessidades:

- **Único âmbito do grupo de recursos** – aplica o desconto de reserva para os recursos correspondentes na apenas o grupo de recursos selecionado.
- **Único âmbito da subscrição** – aplica o desconto de reserva para os recursos correspondentes na subscrição selecionada.
- **Partilhado âmbito** — se aplica o desconto de reserva a correspondência de recursos em subscrições elegíveis que estão no contexto de faturação. Para clientes com Enterprise Agreement, o contexto de faturação é a inscrição. Para obter subscrições individuais com as tarifas pay as you go, o âmbito de faturação é todas as subscrições elegíveis criadas pelo administrador de conta.

Ao aplicar o desconto de reserva na sua utilização, o Azure processa a reserva pela seguinte ordem:

1. Reservas que passam para um grupo de recursos
2. Reservas de escopo único
3. Reservas de âmbito partilhado

Um grupo de recursos pode obter descontos de reserva de reservas vários, dependendo de como definir o âmbito as reservas.

### <a name="scope-a-reservation-to-a-resource-group"></a>Definir o âmbito uma reserva para um grupo de recursos

Pode definir o âmbito da reserva para um grupo de recursos quando comprar a reserva ou definir o âmbito após a compra. Tem de ser um proprietário da subscrição para definir o âmbito da reserva para um grupo de recursos.

Para definir o âmbito, vá para o [comprar a reserva](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) página no portal do Azure. Em seguida, selecione o tipo de reserva que pretende comprar. Sobre o **selecione o produto que deseja comprar** formulário de seleção, alteração a **âmbito** valor para **grupo de recursos único** e selecione um grupo de recursos.

![Exemplo que mostra a seleção de compra de reserva de VM](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

Recomendações de compra para o grupo de recursos a reserva de máquina virtual são apresentadas. As recomendações são calculadas ao analisar a utilização nos últimos 30 dias. Se o custo da execução de recursos com as instâncias reservadas é mais barato do que o custo da execução de recursos com taxas pay as you go, é feita uma recomendação de compra. Para obter mais informações sobre as recomendações de compra de reserva, consulte a [recomendações de compra de instância reservada obter com base num padrão de utilização](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations) postagem de blog.

Pode atualizar o âmbito depois de comprar uma reserva. Para fazer isso, vá para a reserva, clique em **configuração** e rescope a reserva. Rescoping uma reserva não é uma transação comercial. O termo de reserva não é alterado. Para obter mais informações sobre a atualização do escopo, consulte [atualizar o âmbito depois de comprar uma reserva](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

![Exemplo que mostra uma alteração do âmbito de reserva](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>Monitorizar e otimizar a utilização de reserva

Pode monitorizar a utilização de reserva de várias formas, através do portal do Azure, através de APIs ou através de dados de utilização. Para ver todas as reservas que tem acesso a, aceda à **reservas** no portal do Azure. A grade de reservas mostra a percentagem de utilização gravado última para a reserva. Clique a reserva para ver a utilização de longo prazo da reserva.

Também pode obter através da utilização de reserva [APIs](billing-reservation-apis.md#see-reservation-usage) e para o seu [dados de utilização](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks) se for um cliente de contrato enterprise.

Se notar que a utilização do seu grupo de recursos no âmbito reserva é baixa, em seguida, pode atualizar o âmbito da reserva à subscrição individual ou compartilhá-lo entre o contexto de faturação. Também pode dividir a reserva e aplicar as reservas resultantes para grupos de recursos diferentes.

### <a name="other-considerations"></a>Outras considerações

Se não tiver a correspondência de recursos num grupo de recursos, em seguida, a reserva irá subutilizada. A reserva automaticamente não se aplica a um grupo de recursos diferente ou uma subscrição onde há pouca utilização.

Um âmbito de reserva não atualiza automaticamente se mover o grupo de recursos de uma subscrição para outro. Terá de rescope a reserva. Caso contrário, a reserva irá APARENTAREM ser pouco utilizada.

## <a name="discounted-subscription-and-offer-types"></a>Tipos de assinatura e a oferta com desconto

Descontos de reserva aplicam as seguintes subscrições elegíveis e oferecem tipos.

- Contrato Enterprise (números da oferta: MS-AZR-0017P ou MS-AZR - 0148 P)
- Planos individuais com as tarifas pay as you go (números da oferta: MS-AZR-0003P ou MS-AZR - 0023 P)
- Subscrições de CSP

Recursos que são executados numa subscrição com outros tipos de oferta não recebem o desconto de reserva.

## <a name="how-is-a-reservation-billed"></a>Como é faturada uma reserva?

A reserva é cobrada ao método de pagamento associado à subscrição. Se tiver uma subscrição Enterprise, o custo da reserva é deduzido da seu saldo de alocação monetária. Se o seu saldo de alocação monetária não abrange o custo da reserva, é-lhe cobrada a utilização excedida. Se tiver uma subscrição a partir de um plano individual com taxas pay as you go, o cartão de crédito que tem na sua conta é cobrado imediatamente. Se a faturação por fatura, verá os custos na sua fatura seguinte.

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

O desconto de reserva se aplica a utilização de recursos que correspondam os atributos selecionado ao comprar a reserva. Os atributos incluem o âmbito em que executar as VMs correspondentes, bases de dados SQL, Azure Cosmos DB ou outros recursos. Por exemplo, se pretender que um desconto de reserva para quatro máquinas de virtuais de Standard D2 na região E.U.A. oeste, em seguida, selecione a subscrição em que as VMs estão em execução.

Um desconto de reserva é "*utilização-it-ou-perder-it*". Se não tiver recursos de correspondência para qualquer hora, em seguida, perderá uma quantidade de reserva para essa hora. Não pode transportar reencaminhar horas reservadas não utilizadas.

Quando encerra um recurso, o desconto de reserva aplica-se automaticamente para outro recurso correspondente no âmbito especificado. Se não existem recursos correspondentes encontram-se no âmbito especificado, são as horas reservadas *perdido*.

Por exemplo, mais tarde poderá criar um recurso e ter uma reserva de correspondente é subutilizada. Neste exemplo, o desconto de reserva aplica automaticamente para o novo recurso correspondente.

Se as máquinas virtuais são executadas em subscrições diferentes na sua conta/inscrição, em seguida, selecione o âmbito como partilhado. Âmbito partilhado permite que o desconto de reserva a ser aplicado entre subscrições. Pode alterar o âmbito depois de comprar uma reserva. Para obter mais informações, consulte [Gerir reservas de Azure](billing-manage-reserved-vm-instance.md).

Um desconto de reserva é aplicado apenas aos recursos associados à empresa, CSP, ou subscrições com pay as you go taxas. Recursos que são executados numa subscrição com outros tipos de oferta não recebem o desconto de reserva.

## <a name="when-the-reservation-term-expires"></a>Quando expira o termo de reserva

No final do período de reserva, expira o desconto de faturação e a máquina virtual, base de dados SQL, Azure Cosmos DB ou outro recurso é cobrada de acordo com o pay as you acedo preço. Reservas do Azure não a renovação automática. Para continuar a obter o desconto de faturação, tem de comprar uma reserva nova para o software e serviços elegíveis.

## <a name="discount-applies-to-different-sizes"></a>Desconto aplica-se a tamanhos diferentes

Ao comprar uma reserva, pode aplicar o desconto para as outras instâncias com atributos que estão dentro do mesmo grupo de tamanho. Esta funcionalidade é conhecida como a flexibilidade de tamanho de instância. A flexibilidade da cobertura de desconto depende do tipo de reserva e os atributos que escolher quando comprar a reserva.

Planos de serviço:

- Instâncias reservadas da VM: Quando comprar a reserva e selecionar **otimizado para**: **flexibilidade de tamanho de instância**, a cobertura de desconto depende de selecionar o tamanho da VM. A reserva pode aplicar os tamanhos de máquinas virtuais (VMs) no mesmo grupo de séries de tamanho. Para obter mais informações, consulte [flexibilidade de tamanho de Máquina Virtual com as instâncias de VM reservadas](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Capacidade de reserva de base de dados SQL: A cobertura de desconto depende do escalão de desempenho que escolher. Para obter mais informações, consulte [compreender a forma como um desconto de reserva do Azure é aplicado](billing-understand-reservation-charges.md).
- O Azure Cosmos DB a capacidade de reserva: A cobertura de desconto depende do débito aprovisionado. Para obter mais informações, consulte [compreender como é aplicado um desconto de reserva do Azure Cosmos DB](billing-understand-cosmosdb-reservation-charges.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre as reservas do Azure com os seguintes artigos:
    - [Gerir o Azure Reservations](billing-manage-reserved-vm-instance.md)
    - [Compreender a utilização de reserva para a sua subscrição com taxas pay as you go](billing-understand-reserved-instance-usage.md)
    - [Compreender a utilização de reserva para inscrição da sua empresa](billing-understand-reserved-instance-usage-ea.md)
    - [Custos de software do Windows não incluídos com reservas](billing-reserved-instance-windows-software-costs.md)
    - [Reservas do Azure no programa de fornecedor de soluções (CSP) do parceiro Center na nuvem](/partner-center/azure-reservations)

- Saiba mais sobre as reservas para planos de serviço:
    - [Máquinas virtuais com instâncias de VM reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Capacidade de reserva de recursos do Azure do Cosmos DB com o Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Capacidade de reserva de recursos de computação de base de dados SQL com a base de dados do Azure SQL](../sql-database/sql-database-reserved-capacity.md) Saiba mais sobre as reservas para planos de software:
    - [Planos de software do Red Hat do Azure reservas](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Planos de software SUSE das reservas do Azure](../virtual-machines/linux/prepay-suse-software-charges.md)
