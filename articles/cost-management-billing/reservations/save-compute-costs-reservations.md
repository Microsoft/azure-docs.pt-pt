---
title: O que é o Azure Reservations?
description: Saiba mais sobre o Azure Reservations e os respetivos preços para poupar nos custos das suas máquinas virtuais, de bases de dados SQL, do Azure Cosmos DB e de outros recursos.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: banders
ms.openlocfilehash: c6a8547235c302f52aacd0e6ae4a8fbf08b538b8
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443640"
---
# <a name="what-are-azure-reservations"></a>O que é o Azure Reservations?

O Azure Reservations ajuda-o a poupar ao subscrever planos de um ou três anos de máquinas virtuais, do Armazenamento de blobs do Azure ou do Azure Data Lake Storage Gen2, capacidade de computação da Base de Dados SQL, Armazenamento de Discos do Azure, débito do Azure Cosmos DB ou outros recursos do Azure. A subscrição permite-lhe obter um desconto nos recursos que utiliza. As reservas podem reduzir significativamente os custos dos recursos até 72% nos preços pay as you go. O Reservations oferece um desconto de faturação e não afeta o estado de atividade dos seus recursos.

Pode pagar uma reserva em adiantado ou mensalmente. O custo total de reservas pagas em adiantado ou mensalmente é o mesmo e não pagará qualquer taxa adicional ao optar por pagamentos mensais. O pagamento mensal está disponível para as reservas do Azure, não para produtos de terceiros.

Pode comprar uma reserva no [portal do Azure](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Porquê comprar uma reserva?

Se possuir o Azure Cosmos DB, máquinas virtuais, dados do Armazenamento de blobs ou bases de dados SQL que utilizem capacidades ou débitos significativos ou que sejam executadas durante longos períodos de tempo, a compra de uma reserva será a opção mais económica para si. Por exemplo, ao executar quatro instâncias de um serviço continuamente sem uma reserva, ser-lhe-ão cobradas taxas pay as you go. Ao comprar uma reserva para esses recursos, terá direito imediato ao desconto de reserva. Deixarão de ser aplicadas taxas pay as you go aos recursos.

## <a name="charges-covered-by-reservation"></a>Custos abrangidos pelas reservas

Planos de serviço:

- **Instância de Máquina Virtual Reservada** – a reserva abrange apenas os custos de computação de máquinas virtuais. Não abrange custos de armazenamento, rede ou software adicionais.
- **Capacidade reservada do Armazenamento do Microsoft Azure** – uma reserva abrange a capacidade de armazenamento das contas de armazenamento standard do Armazenamento de blobs ou do armazenamento do Azure Data Lake Gen2. A reserva não abrange taxas de largura de banda nem de transação.
- **Reservas de Armazenamento de Discos do Azure** – uma reserva abrange apenas SSDs premium de tamanho P30 ou superior. Não abrange outros tipos ou tamanhos de discos inferiores a P30.
- **Capacidade reservada do Azure Cosmos DB** – a reserva abrange o débito aprovisionado para os seus recursos. Não abrange os custos de armazenamento e de rede.
- **VCore reservado de Base de Dados SQL** – apenas os custos de computação são incluídos na reserva. A licença é faturada separadamente.
- **SQL Data Warehouse** – a reserva abrange a utilização de cDWUs. Não abrange os custos de armazenamento ou rede associados à utilização do SQL Data Warehouse.
- **Imposto de selo do Serviço de Aplicações** – a reserva abrange a utilização de selos. Não se aplica às funções de trabalho, pelo que todos os outros recursos associados ao selo serão cobrados separadamente.
- **Azure Databricks** – as reservas abrangem apenas a utilização de DBU. Outros custos, como computação, armazenamento e rede, são aplicados em separado.
- **Base de Dados do Azure para MySQL** – só estão incluídos nas reservas os custos de computação. As reservas não abrangem os custos de software, rede e armazenamento associados ao servidor da Base de Dados MySQL.
- **Base de Dados do Azure para PostgreSQL** – só estão incluídos nas reservas os custos de computação. As reservas não abrangem os custos de software, rede e armazenamento associados aos servidores da Base de Dados para PostgreSQL.
- **Azure Database for MariaDB** – só estão incluídos nas reservas os custos de computação. As reservas não abrangem os custos de software, rede e armazenamento associados ao servidor da MariaDB Database.
- **Azure Data Explorer** – as reservas incluem os custos de marcação do Data Explorer do Azure. As reservas não abrangem os custos de computação, rede e armazenamento associados aos clusters.
- **Managed Disks SSD Premium** – é feita uma reserva para um SKU de disco especificado. 

Planos de software:

- **SUSE Linux** – as reservas abrangem os custos do plano de software. Os descontos aplicam-se apenas aos medidores SUSE e não á utilização da máquina virtual.
- **Planos do Red Hat** – as reservas abrangem os custos do plano de software. O desconto aplica-se apenas aos medidores Red Hat, não à utilização da máquina virtual.
- **Azure VMware Solution by CloudSimple** – as reservas abrangem os Nós de VMWare da CloudSimple. Continuam a ser aplicados custos de software adicionais.
- **Azure Red Hat OpenShift** – as reservas aplicam-se aos custos do OpenShift, não aos custos da infraestrutura do Azure.

Para máquinas virtuais do Windows e Bases de Dados SQL, pode abranger os custos de licenciamento com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Quem é elegível para comprar uma reserva?

Para comprar um plano, tem de ter uma função de proprietário da subscrição numa subscrição Enterprise (MS-AZR-0017P ou MS-AZR-0148P), Pay As You Go (MS-AZR-0003P ou MS-AZR-0023P) ou Contrato de Cliente da Microsoft. Os fornecedores de soluções cloud podem utilizar o portal do Azure ou o  [Centro de Parceiros](/partner-center/azure-reservations)  para comprar o Azure Reservations.

Os clientes do Contrato Enterprise (EA) podem limitar as compras a administradores do EA ao desativar a opção **Adicionar Instâncias Reservadas** no EA Portal. Os administradores do EA têm de ser o proprietário de, pelo menos, uma subscrição do EA para poderem comprar uma reserva. A opção mencionada é útil para as empresas que pretendem uma equipa centralizada encarregue de comprar reservas para diferentes centros de custos. Após a compra, as equipas centralizadas podem adicionar proprietários de centros de custos às reservas. Posteriormente, os proprietários podem definir o âmbito da reserva para as respetivas subscrições. A equipa centralizada não precisa de ter acesso de proprietário da subscrição nos locais onde a compra da reserva já foi concluída.

Os descontos de reserva só se aplicam a recursos associados a subscrições compradas através do Enterprise, do Fornecedor de Soluções Cloud (CSP), do Contrato de Cliente da Microsoft e de planos individuais com taxas pay as you go.

## <a name="scope-reservations"></a>Definir o âmbito de reservas

Pode definir o âmbito de uma reserva para uma subscrição ou para um grupo de recursos. Definir o âmbito de uma reserva permite selecionar onde as poupanças na reserva serão aplicadas. Ao definir o âmbito da reserva para um grupo de recursos, os descontos de reserva aplicam-se apenas ao grupo de recursos, não à totalidade da subscrição.

### <a name="reservation-scoping-options"></a>Opções de definição do âmbito da reserva

Com a definição do âmbito para o grupo de recursos, terá três opções para definir o âmbito de uma reserva, consoante as suas necessidades:

- **Âmbito de grupo de recursos único** – aplica o desconto de reserva aos recursos correspondentes apenas no grupo de recursos selecionado.
- **Âmbito de subscrição individual** – aplica o desconto de reserva aos recursos correspondentes na subscrição selecionada.
- **Âmbito partilhado** – aplica o desconto de reserva aos recursos correspondentes em subscrições elegíveis que estejam no contexto de faturação. Para clientes do Contrato Enterprise, o contexto de faturação é a inscrição. Para clientes do Contrato de Cliente da Microsoft, o âmbito de faturação é o perfil de faturação. Para subscrições individuais com taxas pay as you go, o âmbito da faturação reside em todas as subscrições elegíveis criadas pelo administrador de conta.

Enquanto aplica descontos de reserva sobre a sua utilização, o Azure processa a reserva pela seguinte ordem:

1. Reservas cujo âmbito está definido para um grupo de recursos
2. Reservas de âmbito único
3. Reservas de âmbito partilhado

Um grupo de recursos único pode obter descontos de reserva provenientes de múltiplas reservas, dependendo de como define o âmbito das suas reservas.

### <a name="scope-a-reservation-to-a-resource-group"></a>Definir o âmbito de uma reserva para um grupo de recursos

Pode definir o âmbito de reserva para um grupo de recursos quando comprar a reserva ou definir o âmbito após a compra. Tem de ser um proprietário da subscrição para definir o âmbito da reserva para um grupo de recursos.

Para definir o âmbito, aceda à página [Reservas de compra](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) no portal do Azure. Selecione o tipo de reserva que pretende comprar. No formulário de seleção **Selecione o produto que quer comprar**, altere o valor Âmbito para grupo de recursos Único. Em seguida, selecione um grupo de recursos.

![Exemplo a mostrar a seleção da compra de reserva de VMs](./media/save-compute-costs-reservations/select-product-to-purchase.png)

São apresentadas recomendações de compras para o grupo de recursos na reserva de máquina virtual. As recomendações são calculadas ao analisar a sua utilização durante os últimos 30 dias. Considera-se que uma recomendação de compra é feita se o custo da execução de recursos com instâncias reservadas for mais baixo do que o custo da execução de recursos com taxas pay as you go. Para obter mais informações sobre recomendações de compra de reservas, veja [Get Reserved Instance purchase recommendations based on usage pattern](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations) (Obter recomendações de compra de Instâncias Reservadas com base no padrão de utilização).

Pode sempre atualizar o âmbito após comprar uma reserva. Para tal, aceda à reserva, clique em **Configuração** e redefina o âmbito da reserva. A redefinição do âmbito de uma reserva não constitui uma transação comercial. O seu termo de reserva não será alterado. Para obter mais informações sobre a atualização do âmbito, veja [Atualizar o âmbito depois de comprar uma reserva](manage-reserved-vm-instance.md#change-the-reservation-scope).

![Exemplo a mostrar uma alteração do âmbito de reserva](./media/save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>Monitorizar e otimizar a utilização da reserva

Pode monitorizar a utilização da sua reserva de várias formas: através do portal do Azure, de APIs ou dos dados de utilização. Para ver todas as reservas a que tem acesso, aceda a **Reservas** no portal do Azure. A grelha de reservas mostra o registo da percentagem de utilização mais recente da reserva em causa. Clique na reserva para ver a utilização a longo prazo da reserva.

Também poderá verificar a utilização da reserva através das [APIs](reservation-apis.md#see-reservation-usage) e dos [dados de utilização](understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks) se for um cliente do Contrato Enterprise ou do Contrato de Cliente da Microsoft.

Se reparar que a utilização da sua reserva com âmbito definido para o grupo de recursos é baixa, poderá atualizar o âmbito da reserva para uma subscrição individual ou partilhar a reserva pelo contexto de faturação. Também poderá dividir a reserva e aplicar as reservas resultantes a diferentes grupos de recursos.

### <a name="other-considerations"></a>Outras considerações

Se não tiver recursos correspondentes num grupo de recursos, a reserva será subutilizada. A reserva não é aplicada automaticamente a uma subscrição ou a um grupo de recursos diferente quando existe uma utilização baixa.

Um âmbito de reserva não é atualizado automaticamente se mover o grupo de recursos de uma subscrição para outra. O âmbito não é atualizado se eliminar o grupo de recursos. Terá de [redefinir o âmbito da reserva](manage-reserved-vm-instance.md#change-the-reservation-scope). Caso contrário, a reserva será subutilizada.

## <a name="discounted-subscription-and-offer-types"></a>Subscrição com desconto e tipos de oferta

Os descontos da reserva aplicam-se às seguintes subscrições e tipos de oferta elegíveis.

- Contrato Enterprise (números de oferta: MS-AZR-0017P ou MS-AZR-0148P)
- Subscrições do Contrato de Cliente da Microsoft.
- Planos individuais com preços pay as you go (números de oferta: MS-AZR-0003P ou MS-AZR-0023P)
- Subscrições CSP

Os recursos executados numa subscrição com outros tipos de oferta não recebem o desconto de reserva.

## <a name="how-is-a-reservation-billed"></a>Como é que uma reserva é faturada?

A reserva é cobrada de acordo com o método de pagamento associado à subscrição. O custo da reserva será deduzido do saldo da alocação monetária, se disponível. Quando o saldo da sua alocação monetária não cobrir o custo da reserva, ser-lhe-á faturada a utilização excedida. Se tiver uma subscrição de um plano individual com taxas pay as you go, o cartão de crédito na sua conta será cobrado de imediato por compras adiantadas. Os pagamentos mensais são apresentados na sua fatura e o seu cartão de crédito é cobrado mensalmente. Ao optar pelo custo por fatura, verá os custos na sua próxima fatura.

## <a name="how-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado

O desconto de reserva é aplicado à utilização de recursos que corresponder aos atributos por si selecionados quando comprar a reserva. Os atributos incluem o âmbito no qual as VMs, as bases de dados SQL, o Azure Cosmos DB ou outros recursos são executados. Por exemplo, se quiser um desconto de reserva para quatro máquinas virtuais Standard D2 na região E.U.A. Oeste, selecione a subscrição onde as VMs estão a ser executadas.

Um desconto de reserva é "*use-it-or-lose-it*" (utilizar ou perder). Se não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.

Quando encerra um recurso, o desconto de reserva aplica-se automaticamente a outro recurso correspondente no âmbito especificado. Se não for encontrado nenhum recurso correspondente no âmbito especificado, as horas reservadas serão *perdidas*.

Por exemplo, poderá criar um recurso mais tarde e ter uma reserva correspondente subutilizada. O desconto de reserva é aplicado automaticamente ao novo recurso correspondente.

Se as máquinas virtuais estiverem a ser executadas em subscrições diferentes na sua inscrição/conta, selecione o âmbito como sendo partilhado. O âmbito partilhado permite que o desconto de reserva seja aplicado em várias subscrições. Pode sempre alterar o âmbito após comprar uma reserva. Para obter mais informações, veja [Gerir o Azure Reservations](manage-reserved-vm-instance.md).

Um desconto de reserva só é aplicado a recursos associados a um Contrato Enterprise, um Contrato de Cliente da Microsoft, CSPs ou subscrições com taxas pay as you go. Os recursos executados numa subscrição com outros tipos de oferta não recebem o desconto de reserva.

## <a name="when-the-reservation-term-expires"></a>Quando o termo de reserva expira

No final do período de reserva, o desconto sobre a faturação expira e os recursos passam a ser faturados com base no preço pay as you go. Por predefinição, as reservas não estão definidas para renovação automática. Pode optar por ativar a renovação automática de uma reserva ao selecionar a opção nas definições de renovação. Com a renovação automática, uma reserva de substituição será comprada quando a reserva existente expirar. Por predefinição, a reserva de substituição tem os mesmos atributos da reserva que expira. Pode, opcionalmente, alterar a frequência, o período ou a quantidade da faturação nas definições de renovação. Qualquer utilizador com acesso de proprietário na reserva e na subscrição utilizada para faturação pode configurar a renovação.  

## <a name="discount-applies-to-different-sizes"></a>O desconto aplica-se a diferentes tamanhos

Quando compra uma reserva, o desconto pode ser aplicado a outras instâncias com atributos que se encontram dentro do mesmo grupo de tamanho. Esta funcionalidade é conhecida como flexibilidade de tamanho de instância. A flexibilidade da cobertura do desconto depende do tipo de reserva e dos atributos que escolher quando comprar a reserva.

Planos do serviço:

- Instâncias de VM Reservada: ao comprar a reserva e selecionar a opção **Otimizado para flexibilidade de tamanho de instância**, a cobertura do desconto depende do tamanho da VM que selecionar. A reserva pode ser aplicada aos tamanhos das máquinas virtuais (VMs) no mesmo grupo de série de tamanhos. Para obter mais informações, veja [Flexibilidade de tamanho da máquina virtual com Reserved VM Instances](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Capacidade reservada do Armazenamento do Microsoft Azure: pode comprar capacidade reservada para contas de Armazenamento do Microsoft Azure standard em unidades de 100 TiB ou 1 PiB por mês. A capacidade reservada do Armazenamento do Microsoft Azure está disponível em todas as regiões para qualquer camada de acesso (frequente, esporádico ou arquivo) e para qualquer opção de replicação (LRS, GRS ou ZRS).
- Capacidade de reserva da Base de Dados SQL: a cobertura do desconto depende do escalão de desempenho que escolher. Para obter mais informações, veja [Compreender a forma como é aplicado um desconto de reserva do Azure](understand-reservation-charges.md).
- Capacidade reservada do Azure Cosmos DB: a cobertura do desconto depende do débito aprovisionado. Para obter mais informações, veja [Compreender a forma como é aplicado um desconto de reserva do Azure Cosmos DB](understand-cosmosdb-reservation-charges.md).

## <a name="reservation-notifications"></a>Notificações de reserva

Dependendo da forma como paga a sua subscrição do Azure, enviaremos notificações de reserva por e-mail para os utilizadores na sua organização. As notificações são enviadas a propósito de vários eventos, incluindo:

- Comprar
- Expiração da reserva futura
- Validade
- Renovação
- Cancelamento
- Alteração do âmbito

Para clientes com subscrições EA:
- É enviada uma notificação de compra para o comprador e para os contactos de notificação do EA.
- As restantes notificações sobre o ciclo de vida da reserva são enviadas apenas para os contactos de notificação do EA.
- Os utilizadores adicionados a uma reserva através de uma permissão RBAC (IAM) não recebem notificações por e-mail.

Para os clientes de subscrições individuais:
- O comprador recebe uma notificação de compra.
- No momento da compra, o proprietário da conta de faturação da subscrição recebe uma notificação de compra.
- O proprietário da conta recebe todos os restantes tipos de notificações.


## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o Azure Reservations através dos seguintes artigos:
    - [Gerir o Azure Reservations](manage-reserved-vm-instance.md)
    - [Compreender a utilização de reservas na sua subscrição com taxas pay as you go](understand-reserved-instance-usage.md)
    - [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)
    - [Custos de software Windows não incluídos nas reservas](reserved-instance-windows-software-costs.md)
    - [Azure Reservations no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros](/partner-center/azure-reservations)

- Saiba mais sobre as reservas para planos de serviço:
    - [Máquinas Virtuais com o Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Recursos do Azure Cosmos DB com capacidade de reserva do Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../../sql-database/sql-database-reserved-capacity.md) Saiba mais sobre as reservas para planos de software:
    - [Planos de software Red Hat no Azure Reservations](../../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Planos de software SUSE no Azure Reservations](../../virtual-machines/linux/prepay-suse-software-charges.md)
