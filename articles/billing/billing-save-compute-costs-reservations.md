---
title: O que é o Azure Reservations? | Microsoft Docs
description: Saiba mais sobre as reservas do Azure e preços para guardar nas suas máquinas virtuais, bases de dados SQL, Azure Cosmos DB e outros custos do recurso.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: cwatson
ms.openlocfilehash: a0fb5eccf14aee07005ab345f1af293f341f9215
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47422954"
---
# <a name="what-are-azure-reservations"></a>O que é o Azure Reservations?

Reservas do Azure permite-lhe poupar dinheiro ao pagar previamente para um ano ou três anos de máquina virtual, capacidade de computação de base de dados SQL, débito do Azure Cosmos DB ou outros recursos do Azure. Pré-pagamento permite-lhe obter um desconto sobre os recursos que utiliza. As reservas podem reduzir significativamente a sua máquina virtual, a computação de base de dados SQL, Azure Cosmos DB, ou outros recursos de custos até 72% em preços pay as you go. Reservas de fornecem um desconto de faturação e não afetam o estado de tempo de execução dos seus recursos.

Pode comprar uma reserva [portal do Azure](https://aka.ms/reservations). Para obter mais informações, consulte os seguintes tópicos:

- [Efetue o pré-pagamento de máquinas virtuais com instâncias de VM reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Efetue o pré-pagamento do recursos de computação de base de dados SQL com capacidade de base de dados do SQL Azure reservados](../sql-database/sql-database-reserved-capacity.md)
- [Efetue o pré-pagamento do recursos do Azure Cosmos DB com capacidade de reservada do Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>Por que devo comprar uma reserva?

Se tiver máquinas virtuais, Azure Cosmos DB ou bases de dados do SQL que são executadas durante longos períodos de tempo, comprar uma reserva dá-lhe a opção mais económica. Por exemplo, se executar quatro instâncias de um serviço sem uma reserva de forma contínua, são cobradas às tarifas pay as you go. Se comprar uma reserva para esses recursos, obtém imediatamente o desconto de reserva. Os recursos já não são cobrados às tarifas pay as you go.

## <a name="what-charges-does-a-reservation-cover"></a>O que os custos é uma capa de reserva?

- Instância de Máquina Virtual reservada: Uma reserva abrange apenas os custos de computação de máquina virtual. Não cobre custos adicionais de software, sistema de rede ou armazenamento.
- Base de dados SQL reservadas vCore: apenas os custos de computação estão incluídos com uma reserva. A licença é faturada em separado.
- Capacidade de reserva do Azure Cosmos DB: uma reserva abrange o débito aprovisionado para os seus recursos, não abrange os custos de armazenamento e rede. 

Para as máquinas virtuais do Windows e a base de dados SQL, que podem ser cobertos os custos de licenciamento com [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Quem é elegível para comprar uma reserva?

Os clientes do Azure com esses tipos de subscrições, podem comprar uma reserva:

- Tipo de oferta de subscrição Contrato Enterprise (MS-AZR-0017P).
- Tipo de oferta de subscrição [Pay As You Go](https://azure.microsoft.com/offers/ms-azr-0003p/) (MS-AZR-003P). Tem de ter a função "Proprietário" na subscrição para comprar uma reserva.
- Parceiros da cloud Solution Provider (CSP) podem utilizar o portal do Azure ou [Centro de parceiros](https://docs.microsoft.com/partner-center/azure-reservations) comprar reservas do Azure.

Um desconto de reserva aplica-se apenas aos recursos associados a tipos de subscrição Enterprise, pay as you go ou CSP.

## <a name="how-is-a-reservation-billed"></a>Como é faturada uma reserva?

A reserva é cobrada ao método de pagamento associado à subscrição. Se tiver uma subscrição Enterprise, o custo da reserva é deduzido da seu saldo de alocação monetária. Se o seu saldo de alocação monetária não abrange o custo da reserva, é-lhe cobrada a utilização excedida. Se tiver uma subscrição pay as you go, o cartão de crédito que tem na sua conta é cobrado imediatamente. Se a faturação por fatura, verá os custos na sua fatura seguinte.

## <a name="how-is-the-reservation-discount-applied"></a>Como é que o desconto de reserva é aplicado?

O desconto de reserva se aplica a utilização de recursos que corresponda os atributos que selecionou quando comprar a reserva. Os atributos incluem o âmbito em que executar as VMs correspondentes, bases de dados SQL, Azure Cosmos DB ou outros recursos. Por exemplo, se pretender que um desconto de reserva para quatro máquinas de virtuais de Standard D2 na região E.U.A. oeste, selecione a subscrição em que as VMs estão em execução. Se as máquinas virtuais são executadas em subscrições diferentes na sua conta/inscrição, em seguida, selecione o âmbito como partilhado. Âmbito partilhado permite que o desconto de reserva a ser aplicado entre subscrições. Pode alterar o âmbito depois de comprar uma reserva. Para obter mais informações, consulte [Gerir reservas de Azure](billing-manage-reserved-vm-instance.md).

Um desconto de reserva aplica-se apenas aos recursos associados a tipos de subscrição Enterprise, pay as you go ou CSP. Recursos que são executados numa subscrição com outros tipos de oferta não recebem o desconto de reserva. Para inscrições de empresas, as subscrições de desenvolvimento/teste empresarial não elegíveis para o benefício de reserva.

Para compreender melhor como as reservas afeta sua faturação, consulte os seguintes tópicos:

-  [Compreender o que desconto de instâncias de VM reservadas do Azure](billing-understand-vm-reservation-charges.md)
- [Compreender o desconto de reserva do Azure](billing-understand-vm-reservation-charges.md)
- [Compreender o desconto de reserva do Azure Cosmos DB](billing-understand-cosmosdb-reservation-charges.md)
- [Compreender o desconto de reserva do Azure e a utilização do SUSE](billing-understand-suse-reservation-charges.md)

## <a name="what-happens-when-the-reservation-term-expires"></a>O que acontece quando o termo de reserva expira?

No final do período de reserva, expira o desconto de faturação e a máquina virtual, base de dados SQL, Azure Cosmos DB ou outro recurso é cobrada de acordo com o pay as you acedo preço. Reservas do Azure não a renovação automática. Para continuar a obter o desconto de faturação, tem de comprar uma reserva nova para o software e serviços elegíveis.

## <a name="discount-applies-to-different-sizes-with-instance-size-flexibility"></a>Desconto aplica-se a tamanhos diferentes com a flexibilidade de tamanho de instância

Ao comprar uma reserva, pode aplicar o desconto para as outras instâncias com atributos que estão dentro do mesmo grupo de tamanho. A flexibilidade da cobertura de desconto depende do tipo de reserva e os atributos que escolher quando comprar a reserva.

- Instâncias de VM reservadas: Quando adquire a reserva, se selecionar **otimizado para**: **flexibilidade de tamanho de instância**, a cobertura de desconto depende de selecionar o tamanho da VM. A reserva pode aplicar os tamanhos de máquinas virtuais (VMs) no mesmo grupo de séries de tamanho. Para obter mais informações, consulte [flexibilidade de tamanho de Máquina Virtual com as instâncias de VM reservadas](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Plano de software SUSE Linux Enterprise: A cobertura de desconto depende de vCPUs das VMs em que executa o software SUSE. Para obter mais informações, consulte [compreender a forma como o desconto de plano de software SUSE Linux Enterprise é aplicado](billing-understand-suse-reservation-charges.md).
- Capacidade de reserva de base de dados SQL: A cobertura de desconto depende do escalão de desempenho que escolher. Para obter mais informações, consulte [compreender a forma como um desconto de reserva do Azure é aplicado](billing-understand-reservation-charges.md).
- Capacidade de reserva do Azure Cosmos DB: A cobertura de desconto depende do débito aprovisionado. Para obter mais informações, consulte [compreender como é aplicado um desconto de reserva do Azure Cosmos DB](billing-understand-cosmosdb-reservation-charges.md).

## <a name="next-steps"></a>Passos Seguintes

Iniciar a guardar nas suas máquinas virtuais ao comprar uma [instância de VM reservada](../virtual-machines/windows/prepay-reserved-vm-instances.md), [capacidade de reserva de base de dados SQL](../sql-database/sql-database-reserved-capacity.md), ou [capacidade de reserva do Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md).

Para saber mais sobre as reservas do Azure, veja os artigos seguintes:

- [Gerir o Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Compreender a utilização de reserva para a sua subscrição pay as you go](billing-understand-reserved-instance-usage.md)
- [Compreender a utilização de reserva para inscrição da sua empresa](billing-understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não incluídos com reservas](billing-reserved-instance-windows-software-costs.md)
- [Reservas do Azure no programa de fornecedor de soluções (CSP) do parceiro Center na nuvem](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda tiver mais perguntas, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.
