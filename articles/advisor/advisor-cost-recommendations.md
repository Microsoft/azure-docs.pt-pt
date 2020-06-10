---
title: Reduzir os custos de serviço usando o Azure Advisor
description: Utilize o Azure Advisor para otimizar o custo das suas implementações Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 99dfec669d8981a557b2e8a8d8979292af74616f
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84658572"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Reduzir os custos de serviço usando o Azure Advisor

O Advisor ajuda-o a otimizar e a reduzir os seus gastos globais do Azure, identificando recursos inativos e subutilizados.Pode obter recomendações de custos a partir do separador **Custos** no painel De aconselhamento.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Otimizar a despesa da máquina virtual ao redimensionar ou encerrar instâncias subutilizadas 

Embora certos cenários de aplicação possam resultar em baixa utilização por design, você pode muitas vezes economizar dinheiro gerindo o tamanho e o número das suas máquinas virtuais. Os modelos de avaliação avançada do consultor consideram as máquinas virtuais para desligar quando o P95º do valor máximo de utilização do CPU é inferior a 3% e a utilização da rede é inferior a 2% durante um período de 7 dias. As máquinas virtuais são consideradas para o tamanho certo quando é possível encaixar a carga de corrente num SKU menor (dentro da mesma família SKU) ou num número menor de exemplos, de tal forma que a carga atual não ultrapassa os 80% de utilização quando não o utilizador enfrenta cargas de trabalho e não acima de 40% quando a carga de trabalho virada para o utilizador. Aqui, o tipo de carga de trabalho é determinado através da análise das características de utilização do CPU da carga de trabalho.

As ações recomendadas são encerradas ou redimensionais, específicas para o recurso recomendado. O advisor mostra-lhe as economias de custos estimadas para as ações recomendadas - redimensionar ou desligar. Além disso, para redimensionar a ação recomendada, o Advisor fornece informações atuais e direcionais do SKU. 

Se quiser ser mais agressivo na identificação de máquinas virtuais subutilizadas, pode ajustar a regra de utilização do CPU por subscrição.

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>Otimizar os gastos para os servidores MariaDB, MySQL e PostgreSQL por dimensionamento certo 
O Advisor analisa a sua utilização e recomenda se os seus recursos de servidor de base de dados MariaDB/MySQL/PostgreSQL foram subutilizados por um longo período de tempo ao longo dos últimos 7 dias. A baixa utilização dos recursos resulta em despesas indesejadas que podem ser corrigidas sem um impacto significativo sobre o desempenho. Para reduzir os custos e gerir de forma eficiente os recursos, recomendamos que reduza para metade o tamanho da computação (vCores).

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Reduzir custos eliminando circuitos ExpressRoute não provisão

O Advisor identifica os circuitos ExpressRoute que estão no estatuto de fornecedor de *Não Provisionados* há mais de um mês, e recomenda a eliminação do circuito se não estiver a planear a provisionar o circuito com o seu fornecedor de conectividade.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Reduzir custos ao eliminar ou reconfigurar gateways de rede virtual

O conselheiro identifica os gateways de rede virtuais que estão inativos há mais de 90 dias. Uma vez que estes gateways são cobrados de hora a hora, deve considerar reconfigurá-los ou apagá-los se não pretender usá-los mais. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Comprar instâncias de máquina virtual reservadas para poupar dinheiro sobre os custos pay as you go

O Advisor irá rever o uso da sua máquina virtual nos últimos 30 dias e determinará se pode economizar dinheiro comprando uma reserva Azure. O advisor irá mostrar-lhe as regiões e tamanhos onde potencialmente tem mais poupanças e irá mostrar-lhe as economias estimadas da compra de reservas. Com reservas Azure, pode pré-comprar os custos base das suas máquinas virtuais. Os descontos aplicar-se-ão automaticamente aos VM novos ou existentes que tenham o mesmo tamanho e região que as suas reservas. [Saiba mais sobre Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

O conselheiro também o notificará das instâncias reservadas que tem que expirarão nos próximos 30 dias. Recomenda-se que compre novas instâncias reservadas para evitar pagar preços de pagamento.

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>Compre instâncias reservadas para vários tipos de recursos para economizar sobre os seus custos de pagamento à medida

Analisamos o padrão de utilização para abaixo da lista de recursos, ao longo dos últimos 30 dias e recomendamos a compra de capacidade reservada que maximize as suas poupanças. 
### <a name="cosmos-db-reserved-capacity"></a>Capacidade reservada do Cosmos DB
Analisámos o seu padrão de utilização do Cosmos DB nos últimos 30 dias e recomendamos a compra de capacidade reservada para otimizar os custos. Com a capacidade reservada, pode pré-comprar utilização por hora do Cosmos DB e poupar nos custos “pay as you go”. A capacidade reservada é um benefício de faturação e será aplicada automaticamente às implementações novas ou existentes. As estimativas de poupança são calculadas para subscrições individuais com preços de reserva de três anos e ao extrapolar o padrão de utilização observado ao longo dos últimos 30 dias. Estão disponíveis recomendações do âmbito partilhado na experiência de compra de reservas, as quais podem aumentar ainda mais a poupança.

### <a name="sql-paas-reserved-capacity"></a>Capacidade reservada SQL PaaS
Analisámos os conjuntos elásticos PaaS do SQL e o padrão de utilização de instâncias geridas ao longo dos últimos 30 dias e recomendamos a compra de capacidade reservada para maximizar as suas poupanças. Com capacidade reservada, pode pré-comprar utilização por hora da BD SQL e poupar nos custos de computação do SQL. A licença do SQL é cobrada separadamente e não é descontada pela reserva. A capacidade reservada é um benefício de faturação e será aplicada automaticamente às implementações novas ou existentes. As estimativas de poupança são calculadas para subscrições individuais com preços de reserva de três anos e ao extrapolar o padrão de utilização observado ao longo dos últimos 30 dias. Estão disponíveis recomendações do âmbito partilhado na experiência de compra de reservas, as quais podem aumentar ainda mais a poupança.

### <a name="app-service-stamp-fee-reserved-capacity"></a>Taxa de carimbo de serviço de aplicações reservada
Analisamos o seu padrão de utilização de taxas de selo de ambiente isolado do seu Serviço de Aplicações ao longo dos últimos 30 dias e recomendamos uma compra de capacidade reservada que maximize as suas poupanças. Com a capacidade reservada, pode pré-comprar utilização por hora do imposto de selo de ambiente isolado e poupar nos custos “Pay as you go”. Observe que a capacidade reservada se aplica apenas ao imposto de selo e não às instâncias do Serviço de Aplicações. A capacidade reservada é um benefício de faturação e será aplicada automaticamente às implementações novas ou existentes. As estimativas de poupança são calculadas para subscrições individuais com preços de reserva de três anos e com base no padrão de utilização dos últimos 30 dias.

### <a name="blob-storage-reserved-capacity"></a>Capacidade reservada de armazenamento de blob
Analisámos a sua utilização do armazenamento do Data Lake Storage e do Blob do Azure nos últimos 30 dias e calculámos a compra de capacidade reservada para maximizar a poupança. Com a capacidade reservada, pode pré-comprar utilização por hora e poupar nos custos a pedido. A capacidade reservada do Armazenamento de blobs aplica-se apenas aos dados armazenados no Blob do Azure (GPv2) e no Azure Data Lake Storage (Gen2). A capacidade reservada é um benefício de faturação e será aplicada automaticamente às implementações novas ou existentes. As estimativas de poupança são calculadas para subscrições individuais com preços de reserva de três anos e com o padrão de utilização observado nos últimos 30 dias. Estão disponíveis recomendações do âmbito partilhado na experiência de compra de reservas, as quais podem aumentar ainda mais a poupança.

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>Capacidade reservada mariaDB, MySQL e PostgreSQL
Analisamos a sua Base de Dados Azure para o padrão de utilização mariaDB, MySQL e PostgreSQL ao longo dos últimos 30 dias e recomendamos uma compra de capacidade reservada que maximize as suas poupanças. Com capacidade reservada, pode pré-comprar o uso horário de MariaDB, MySQL e PostgreSQL e economizar acima dos seus custos. A capacidade reservada é um benefício de faturação e será aplicada automaticamente às implementações novas ou existentes. As estimativas de poupança são calculadas para subscrições individuais com preços de reserva de três anos e com o padrão de utilização dos últimos 30 dias. Estão disponíveis recomendações do âmbito partilhado na experiência de compra de reservas, as quais podem aumentar ainda mais a poupança.

### <a name="synapse-analytics-formerly-sql-dw-reserved-capacity"></a>Sinapse analytics (anteriormente SQL DW) capacidade reservada
Analisamos o seu padrão de utilização Azure Synapse Analytics ao longo dos últimos 30 dias e recomendamos uma compra reservada de capacidade que maximize as suas poupanças. Com a capacidade reservada, pode pré-comprar utilização por hora do Synapse Analytics e poupar nos custos a pedido. A capacidade reservada é um benefício de faturação e será aplicada automaticamente às implementações novas ou existentes. As estimativas de poupança são calculadas para subscrições individuais com preços de reserva de três anos e com o padrão de utilização observado nos últimos 30 dias. Estão disponíveis recomendações do âmbito partilhado na experiência de compra de reservas, as quais podem aumentar ainda mais a poupança.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Eliminar endereços IP públicos não ligados para economizar dinheiro

O advisor identifica endereços IP públicos que não estão atualmente associados a recursos Azure, tais como Balanceadores de Carga ou VMs. Estes endereços IP públicos vêm com uma taxa nominal. Se não planeia usá-las, apagá-las pode resultar em poupanças de custos.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Eliminar pipelines do Azure Data Factory com falhas

O Azure Advisor detetará os oleodutos Azure Data Factory que falham repetidamente e recomenda que resolva os problemas ou elimine os gasodutos em falha se já não forem necessários. Serão cobrados por estes oleodutos, mesmo que não estejam a servir-vos enquanto estão a falhar. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Utilize instantâneos padrão para discos geridos
Para poupar 60% no custo, recomendamos que guarde os instantâneos no Armazenamento Standard, independentemente do tipo de armazenamento do disco principal. Esta opção é a opção padrão para instantâneos de Discos Geridos. O Azure Advisor identificará instantâneos que são armazenados de Armazenamento Premium e recomendará a migração do seu instantâneo de Premium para Armazenamento Padrão. [Saiba mais sobre preços geridos em disco](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>Utilizar a Gestão do Ciclo de Vida
O Azure Advisor utilizará informações sobre a contagem de objetos de armazenamento de bolhas Azure, tamanho total e transações para detetar se uma ou mais das suas contas de armazenamento seriam mais adequadas para permitir a gestão do ciclo de vida para os dados de nível. Irá instruí-lo a criar regras de Gestão de Ciclo de Vida para hierarquizar automaticamente os seus dados para Cool ou Archive para otimizar os seus custos de armazenamento, mantendo os seus dados no armazenamento de blob Azure para compatibilidade de aplicações.

## <a name="create-an-ephemeral-os-disk-recommendation"></a>Crie uma recomendação de Disco de SO Efémero
Com [o Disco EFÉMER OS,](https://docs.microsoft.com/azure/virtual-machines/windows/ephemeral-os-disks)os clientes obtêm estes benefícios: Economize no custo de armazenamento do disco OS. Obtêm latência de leitura/escrita mais baixa para o disco de SO. Operação de Recriação da Imagem da VM mais rápida através da reposição do SO (e Disco temporário) para o seu estado original. É mais preferível usar o DISCO EFÉMER PARA Os VMs ou VMs de curta duração com cargas de trabalho apátridas. O Advisor tem recomendação para recursos que podem beneficiar de benefícios com o Disco EFÉMER. 


## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Como aceder às recomendações de custos no Azure Advisor

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Procure e selecione [**Advisor**](https://aka.ms/azureadvisordashboard) a partir de qualquer página.

1. No painel **'Advisor',** selecione o **separador Custos.**

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre as recomendações do Advisor, consulte:
* [Introdução ao Conselheiro](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de desempenho do conselheiro](advisor-performance-recommendations.md)
* [Recomendações de alta disponibilidade do conselheiro](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do consultor](advisor-operational-excellence-recommendations.md)

