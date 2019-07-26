---
title: O que é o Azure Reservations?
description: Saiba mais sobre as reservas e os preços do Azure para salvar em suas máquinas virtuais, bancos de dados SQL, Azure Cosmos DB e outros custos de recursos.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: banders
ms.openlocfilehash: c63447ff0a3329e0cc0dc0605984ae4f26e9c25f
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359227"
---
# <a name="what-are-azure-reservations"></a>O que é o Azure Reservations?

As reservas do Azure ajudam você a economizar dinheiro ao pagar por um ou três anos de máquinas virtuais, capacidade de computação do banco de dados SQL, taxa de transferência de Azure Cosmos DB ou outros recursos do Azure. O pagamento antecipado permite que você obtenha um desconto sobre os recursos que usa. As reservas podem reduzir significativamente sua máquina virtual, computação do banco de dados SQL, Azure Cosmos DB ou outros custos de recursos de até 72% em preços pagos conforme o uso. As reservas fornecem um desconto de cobrança e não afetam o estado de tempo de execução de seus recursos.

Você pode comprar uma reserva no [portal do Azure](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Por que comprar uma reserva?

Se você tiver máquinas virtuais, Azure Cosmos DB ou bancos de dados SQL que são executados por longos períodos de tempo, a compra de uma reserva oferecerá a opção mais econômica. Por exemplo, quando você executa continuamente quatro instâncias de um serviço sem uma reserva, você é cobrado de acordo com as tarifas pré-pagas. Ao comprar uma reserva para esses recursos, você obtém imediatamente o desconto de reserva. Os recursos não são mais cobrados com as tarifas pago pelo uso.

## <a name="charges-covered-by-reservation"></a>Encargos cobertos pela reserva

Planos de serviço:

- **Instância de máquina virtual reservada** -uma reserva cobre apenas os custos de computação da máquina virtual. Ele não cobre software adicional, redes ou encargos de armazenamento.
- **Azure Cosmos DB capacidade reservada** -uma reserva abrange a taxa de transferência provisionada para seus recursos. Ele não cobre o armazenamento e os encargos de rede.
- **VCore reservado do banco de dados SQL** – somente os custos de computação estão incluídos com uma reserva. A licença é cobrada separadamente.

Para máquinas virtuais do Windows e banco de dados SQL, você pode cobrir os custos de licenciamento com [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Quem está qualificado para comprar uma reserva?

Para comprar um plano, você deve ter uma função de proprietário de assinatura em uma assinatura Enterprise (MS-AZR-0017P ou MS-AZR-0148P) ou de pagamento conforme o uso (MS-AZR-003P ou MS-AZR-0023P). Os provedores de soluções de nuvem podem usar o portal do Azure ou o [Partner Center](/partner-center/azure-reservations) para comprar reservas do Azure.

Os clientes Enterprise Agreement (EA) podem limitar as compras a administradores de EA. Eles fazem isso desabilitando a opção **adicionar instâncias reservadas** no portal de ea. Os administradores de EA devem ser um proprietário de assinatura para pelo menos uma assinatura de EA para comprar uma reserva. A opção é útil para empresas que desejam que uma equipe centralizada compre reservas para diferentes centros de custo. Após a compra, as equipes centralizadas podem adicionar proprietários de centro de custo às reservas. Os proprietários podem então fazer o escopo da reserva para suas assinaturas. A equipe central não precisa ter acesso de proprietário de assinatura onde a reserva é comprada.

Um desconto de reserva se aplica somente aos recursos associados às assinaturas adquiridas por meio do Enterprise, provedor de soluções de nuvem (CSP) e planos individuais com tarifas pagas conforme o uso.

## <a name="scope-reservations"></a>Reservas de escopo

Você pode fazer o escopo de uma reserva para uma assinatura ou grupos de recursos. Definir o escopo de uma reserva seleciona o local em que a economia de reserva se aplica. Quando você tem o escopo da reserva para um grupo de recursos, os descontos de reserva se aplicam somente ao grupo de recursos — e não à assinatura inteira.

### <a name="reservation-scoping-options"></a>Opções de escopo de reserva

Com o escopo do grupo de recursos, você tem três opções para definir o escopo de uma reserva, dependendo de suas necessidades:

- **Escopo do grupo de recursos único** — aplica o desconto de reserva aos recursos correspondentes somente no grupo de recursos selecionado.
- **Escopo de assinatura única** — aplica o desconto de reserva aos recursos de correspondência na assinatura selecionada.
- **Escopo compartilhado** — aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. Para clientes Enterprise Agreement, o contexto de cobrança é o registro. Para assinaturas individuais com tarifas pagas conforme o uso, o escopo de cobrança é todas as assinaturas qualificadas criadas pelo administrador da conta.

Ao aplicar descontos de reserva no seu uso, o Azure processa a reserva na seguinte ordem:

1. Reservas que estão no escopo de um grupo de recursos
2. Reservas de escopo único
3. Reservas de escopo compartilhado

Um único grupo de recursos pode obter descontos de reserva de várias reservas, dependendo de como você tem como escopo suas reservas.

### <a name="scope-a-reservation-to-a-resource-group"></a>Escopo de uma reserva para um grupo de recursos

Você pode definir o escopo da reserva para um grupo de recursos quando você adquire a reserva ou define o escopo após a compra. Você deve ser um proprietário de assinatura para delimitar a reserva a um grupo de recursos.

Para definir o escopo, vá para a página [reserva de compra](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) na portal do Azure. Selecione o tipo de reserva que você deseja comprar. No formulário **selecionar o produto que você deseja comprar** , altere o valor do escopo para grupo de recursos único. Em seguida, selecione um grupo de recursos.

![Exemplo mostrando a seleção de compra de reserva de VM](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

As recomendações de compra para o grupo de recursos na reserva da máquina virtual são mostradas. As recomendações são calculadas analisando seu uso nos últimos 30 dias. Uma recomendação de compra é feita se o custo da execução de recursos com instâncias reservadas for mais barato do que o custo da execução de recursos com tarifas pagas conforme o uso. Para obter mais informações sobre as recomendações de compra de reserva, consulte [obter recomendações de compra de instância reservada com base no padrão de uso](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations).

Você sempre pode atualizar o escopo depois de comprar uma reserva. Para fazer isso, vá para a reserva, clique em **configuração**e refaça o reescopo da reserva. O reescopo de uma reserva não é uma transação comercial. Seu termo de reserva não é alterado. Para obter mais informações sobre como atualizar o escopo, consulte [atualizar o escopo depois de comprar uma reserva](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

![Exemplo mostrando uma alteração de escopo de reserva](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>Monitorar e otimizar o uso de reserva

Você pode monitorar seu uso de reserva de várias maneiras – por meio de portal do Azure, por meio de APIs ou por meio de dados de uso. Para ver todas as reservas às quais você tem acesso, acesse **reservas** no portal do Azure. A grade reservas mostra a porcentagem de utilização gravada pela última vez para a reserva. Clique na reserva para ver a utilização de longo prazo da reserva.

Você também pode obter a utilização de reserva usando [APIs](billing-reservation-apis.md#see-reservation-usage) e seus [dados de uso](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks) se você for um cliente do Enterprise Agreement.

Se você observar que a utilização da reserva no escopo do grupo de recursos está baixa, você poderá atualizar o escopo de reserva para uma única assinatura ou compartilhá-lo no contexto de cobrança. Você também pode dividir a reserva e aplicar as reservas resultantes a grupos de recursos diferentes.

### <a name="other-considerations"></a>Outras considerações

Se você não tiver recursos correspondentes em um grupo de recursos, a reserva será subutilizada. A reserva não se aplica automaticamente a um grupo de recursos ou assinatura diferente em que há pouca utilização.

Um escopo de reserva não será atualizado automaticamente se você mover o grupo de recursos de uma assinatura para outra. O escopo não será atualizado se você excluir o grupo de recursos. Você precisará redefinir [o escopo da reserva](billing-manage-reserved-vm-instance.md#change-the-reservation-scope). Caso contrário, a reserva será subutilizada.

## <a name="discounted-subscription-and-offer-types"></a>Assinatura com desconto e tipos de oferta

Os descontos de reserva se aplicam às seguintes assinaturas qualificadas e tipos de oferta.

- Contrato Enterprise (números de oferta: MS-AZR-0017P ou MS-AZR-0148P)
- Planos individuais com tarifas pagas conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P)
- Assinaturas do CSP

Os recursos que são executados em uma assinatura com outros tipos de oferta não recebem o desconto de reserva.

## <a name="how-is-a-reservation-billed"></a>Como uma reserva é cobrada?

A reserva é cobrada para o método de pagamento vinculado à assinatura. Se você tiver uma assinatura corporativa, o custo de reserva será deduzido do saldo de compromisso monetário. Quando seu saldo de compromisso monetário não cobre o custo da reserva, você será cobrado pelo excedente. Se você tiver uma assinatura de um plano individual com tarifas pagas conforme o uso, o cartão de crédito que você tem em sua conta será cobrado imediatamente. Quando for cobrado por fatura, você verá os encargos em sua próxima fatura.

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

O desconto de reserva se aplica ao uso de recursos que correspondem aos atributos selecionados quando você adquire a reserva. Os atributos incluem o escopo em que as VMs correspondentes, os bancos de dados SQL, Azure Cosmos DB ou outros recursos são executados. Por exemplo, se você quiser um desconto de reserva para quatro máquinas virtuais D2 padrão na região oeste dos EUA, selecione a assinatura em que as VMs estão em execução.

Um desconto de reserva é "*use-it-or-perca-it*". Se você não tiver recursos correspondentes para nenhuma hora, você perderá uma quantidade de reserva para essa hora. Não é possível postergar horas reservadas não utilizadas.

Quando você encerra um recurso, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso correspondente for encontrado no escopo especificado, as horas reservadas serão *perdidas*.

Por exemplo, você pode criar posteriormente um recurso e ter uma reserva correspondente subutilizada. O desconto de reserva se aplica automaticamente ao novo recurso de correspondência.

Se as máquinas virtuais estiverem em execução em assinaturas diferentes em seu registro/conta, selecione o escopo como compartilhado. O escopo compartilhado permite que o desconto de reserva seja aplicado entre assinaturas. Você pode alterar o escopo depois de comprar uma reserva. Para obter mais informações, consulte [gerenciar reservas do Azure](billing-manage-reserved-vm-instance.md).

Um desconto de reserva só se aplica a recursos associados a empresas, CSP ou assinaturas com tarifas pagas conforme o uso. Os recursos que são executados em uma assinatura com outros tipos de oferta não recebem o desconto de reserva.

## <a name="when-the-reservation-term-expires"></a>Quando o termo de reserva expirar

No final do prazo de reserva, o desconto de cobrança expira. A máquina virtual, o banco de dados SQL, o Azure Cosmos DB ou outro recurso é cobrado pelo preço pago conforme o uso. As reservas do Azure não são renovadas automaticamente. Para continuar obtendo o desconto de cobrança, você deve comprar uma nova reserva para serviços e software qualificados.

## <a name="discount-applies-to-different-sizes"></a>O desconto se aplica a tamanhos diferentes

Quando você adquire uma reserva, o desconto pode ser aplicado a outras instâncias com atributos que estão dentro do mesmo tamanho de grupo. Esse recurso é conhecido como flexibilidade de tamanho de instância. A flexibilidade da cobertura de desconto depende do tipo de reserva e dos atributos que você escolhe ao comprar a reserva.

Planos de serviço:

- Instâncias de VM reservadas: Quando você adquire a reserva e seleciona **otimizado para flexibilidade de tamanho de instância**, a cobertura de desconto depende do tamanho da VM selecionado. A reserva pode ser aplicada a tamanhos de VMs (máquinas virtuais) no mesmo grupo de séries de tamanho. Para obter mais informações, consulte [flexibilidade de tamanho de máquina virtual com instâncias de VM reservadas](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Capacidade reservada do banco de dados SQL: A cobertura de desconto depende do nível de desempenho que você escolher. Para obter mais informações, consulte [entender como um desconto de reserva do Azure é aplicado](billing-understand-reservation-charges.md).
- Azure Cosmos DB capacidade reservada: A cobertura de desconto depende da taxa de transferência provisionada. Para obter mais informações, consulte [entender como um desconto de reserva de Azure Cosmos DB é aplicado](billing-understand-cosmosdb-reservation-charges.md).

## <a name="reservation-notifications"></a>Notificações de reserva

Dependendo de como você paga pela sua assinatura do Azure, enviamos notificações por email para os seguintes usuários em sua organização. As notificações são enviadas para vários eventos, incluindo:

- Comprar
- Próxima expiração de reserva
- Expiry
- Automática
- Cancelamento
- Alteração de escopo

Para clientes com assinaturas EA:
- Uma notificação de compra é enviada ao comprador e aos contatos de notificação do EA.
- Outras notificações de ciclo de vida de reserva são enviadas somente para os contatos de notificação EA.
- Os usuários adicionados a uma reserva usando a permissão RBAC (IAM) não recebem nenhuma notificação por email.

Para clientes com assinaturas individuais:
- O comprador recebe uma notificação de compra.
- No momento da compra, o proprietário da conta de cobrança da assinatura recebe uma notificação de compra.
- O proprietário da conta recebe todas as outras notificações.


## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre as reservas do Azure com os seguintes artigos:
    - [Gerir o Azure Reservations](billing-manage-reserved-vm-instance.md)
    - [Entenda o uso de reserva para sua assinatura com tarifas pagas conforme o uso](billing-understand-reserved-instance-usage.md)
    - [Entender o uso de reserva para seu registro corporativo](billing-understand-reserved-instance-usage-ea.md)
    - [Custos de software do Windows não incluídos com reservas](billing-reserved-instance-windows-software-costs.md)
    - [Reservas do Azure no programa CSP (provedor de soluções na nuvem) do Partner Center](/partner-center/azure-reservations)

- Saiba mais sobre reservas para planos de serviço:
    - [Máquinas virtuais com instâncias de VM reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Azure Cosmos DB recursos com Azure Cosmos DB capacidade reservada](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Recursos de computação do banco de dados SQL com capacidade reservada do banco de dados SQL](../sql-database/sql-database-reserved-capacity.md) Saiba mais sobre reservas para planos de software:
    - [Planos de software Red Hat de reservas do Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Planos de software SUSE de reservas do Azure](../virtual-machines/linux/prepay-suse-software-charges.md)
