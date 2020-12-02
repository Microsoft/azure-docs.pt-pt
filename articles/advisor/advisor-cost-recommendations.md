---
title: Reduzir os custos de serviço usando o Azure Advisor
description: Utilize o Azure Advisor para otimizar o custo das suas implementações Azure.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 767c82c818535304552c1c98ff50a252acc0d08a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458513"
---
# <a name="reduce-service-costs-by-using-azure-advisor"></a>Reduzir os custos de serviço utilizando o Azure Advisor

O Azure Advisor ajuda-o a otimizar e a reduzir os seus gastos globais do Azure, identificando recursos inativos e subutilizados. Pode obter recomendações de custos a partir do separador **Custos** no painel De aconselhamento.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Otimizar a despesa da máquina virtual ao redimensionar ou encerrar instâncias subutilizadas 

Embora certos cenários de aplicação possam resultar em baixa utilização por design, você pode muitas vezes economizar dinheiro gerindo o tamanho e o número das suas máquinas virtuais. 

As ações recomendadas são encerradas ou redimensionadas, específicas do recurso que está a ser avaliado.

O modelo de avaliação avançada no Advisor considera desligar máquinas virtuais quando todas estas declarações são verdadeiras: 
- P95º do valor máximo de utilização do CPU é inferior a 3%. 
- A utilização da rede é inferior a 2% num período de sete dias.
- A pressão da memória é inferior aos valores limiares

O advisor considera redimensionar máquinas virtuais quando é possível encaixar a carga atual num SKU menor (dentro da mesma família SKU) ou num número menor de casos tais que:
- A carga atual não ultrapassa os 80% de utilização para cargas de trabalho que não estão viradas para o utilizador. 
- A carga não ultrapassa os 40% para as cargas de trabalho viradas para o utilizador. 

Aqui, o Advisor determina o tipo de carga de trabalho analisando as características de utilização do CPU da carga de trabalho.

O consultor mostra as economias de custos estimadas para a ação recomendada: redimensionar ou desligar. Para redimensionar, o Advisor fornece informações atuais e alvo sKU.

Se quiser ser mais agressivo na identificação de máquinas virtuais subutilizadas, pode ajustar a regra de utilização do CPU numa base por subscrição.

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>Otimizar os gastos para os servidores MariaDB, MySQL e PostgreSQL por tamanho certo 
O Advisor analisa o seu uso e avalia se os recursos do servidor de base de dados MariaDB, MySQL ou PostgreSQL foram subutilizados por um longo período de tempo nos últimos sete dias. A baixa utilização de recursos resulta em despesas indesejadas que pode corrigir sem um impacto significativo no desempenho. Para reduzir os seus custos e gerir eficientemente os seus recursos, recomendamos que reduza para metade o tamanho do cálculo (vCores).

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Reduzir custos eliminando circuitos ExpressRoute não provisão

O advisor identifica os circuitos Azure ExpressRoute que estão no estatuto de fornecedor de **Não provisão** há mais de um mês. Recomenda a eliminação do circuito se não estiver a planear a provisionar o circuito com o seu fornecedor de conectividade.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Reduzir custos ao eliminar ou reconfigurar gateways de rede virtual

O conselheiro identifica os gateways de rede virtuais que estão inativos há mais de 90 dias. Como estes gateways são cobrados de hora a hora, deve considerar reconfigurá-los ou apagá-los se não pretender usá-los mais. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Comprar instâncias de máquina virtual reservadas para poupar dinheiro sobre os custos pay as you go

O Advisor revê o uso da sua máquina virtual nos últimos 30 dias para determinar se pode economizar dinheiro comprando uma reserva Azure. O advisor mostra-lhe as regiões e tamanhos onde o potencial de poupança é maior e as poupanças estimadas na compra de reservas. Com reservas Azure, pode pré-comprar os custos base das suas máquinas virtuais. Os descontos aplicam-se automaticamente aos VM novos ou existentes que têm o mesmo tamanho e região que as suas reservas. [Saiba mais sobre Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

O Advisor também o notifica das suas instâncias reservadas que expirarão nos próximos 30 dias. Recomenda que compre novos casos reservados para evitar preços de pagamento.

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>Compre instâncias reservadas para vários tipos de recursos para economizar sobre os seus custos de pagamento à medida

O Advisor analisa os padrões de utilização dos últimos 30 dias para os seguintes recursos e recomenda compras de capacidade reservada que otimizem os custos.

### <a name="azure-cosmos-db-reserved-capacity"></a>Capacidade reservada da Azure Cosmos DB
O Advisor analisa os seus padrões de utilização do Azure Cosmos DB nos últimos 30 dias e recomenda compras de capacidade reservada para otimizar os custos. Ao utilizar a capacidade reservada, pode pré-comprar o uso de hora a hora da Azure Cosmos e economizar sobre os seus custos de pagamento. A capacidade reservada é um benefício de faturação e aplica-se automaticamente a implementações novas e existentes. O Advisor calcula as estimativas de poupança para subscrições individuais utilizando preços de reserva de 3 anos e extrapolando os padrões de utilização observados nos últimos 30 dias. As recomendações de âmbito partilhado estão disponíveis para compras de capacidade reservada e podem aumentar a poupança.

### <a name="sql-database-and-sql-managed-instance-reserved-capacity"></a>SqL Database e SQL Managed Instance reservado capacidade
O Advisor analisa os padrões de utilização da SQL Database e da SQL Managed Instance nos últimos 30 dias. Recomenda então compras de capacidade reservada que otimizem os custos. Ao utilizar a capacidade reservada, pode pré-comprar o uso de DB SQL por hora e economizar acima dos seus custos de computação SQL. A sua licença SQL é cobrada separadamente e não é descontada pela reserva. A capacidade reservada é um benefício de faturação e aplica-se automaticamente a implementações novas e existentes. O Advisor calcula as estimativas de poupança para subscrições individuais utilizando preços de reserva de 3 anos e extrapolando os padrões de utilização observados nos últimos 30 dias. As recomendações de âmbito partilhado estão disponíveis para compras de capacidade reservada e podem aumentar a poupança. Para mais informações, consulte [a Base de Dados Azure SQL & capacidade reservada de instância gerida SQL.](../azure-sql/database/reserved-capacity-overview.md)

### <a name="app-service-stamp-fee-reserved-capacity"></a>Taxa de selo de serviço de aplicação reservada
O Advisor analisa o padrão de utilização da Taxa de Selo para o seu ambiente isolado do Serviço de Aplicações Azure ao longo dos últimos 30 dias e recomenda compras de capacidade reservada que otimizem os custos. Ao utilizar a capacidade reservada, pode pré-comprar o uso por hora para o ambiente isolado Stamp Fee e economizar sobre os seus custos de pagamento à medida. Note que a capacidade reservada se aplica apenas à Taxa de Selo e não às instâncias do Serviço de Aplicações. A capacidade reservada é um benefício de faturação e aplica-se automaticamente a implementações novas e existentes. O Advisor calcula a poupança de estimativas para subscrições individuais utilizando preços de reserva de 3 anos com base em padrões de utilização nos últimos 30 dias.

### <a name="blob-storage-reserved-capacity"></a>Capacidade reservada de armazenamento de blob
O Advisor analisa o seu armazenamento Azure Blob e o armazenamento do Azure Data Lake nos últimos 30 dias. Em seguida, calcula as compras de capacidade reservada que otimizam os custos. Com capacidade reservada, pode pré-comprar o uso por hora e economizar acima dos seus custos a pedido atuais. A capacidade reservada de armazenamento de blob aplica-se apenas aos dados armazenados nas contas Azure Blob de uso geral v2 e Azure Data Lake Storage Gen2. A capacidade reservada é um benefício de faturação e aplica-se automaticamente a implementações novas e existentes. O Advisor calcula as estimativas de poupança para subscrições individuais utilizando preços de reserva de 3 anos e os padrões de utilização observados nos últimos 30 dias. As recomendações de âmbito partilhado estão disponíveis para compras de capacidade reservada e podem aumentar a poupança.

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>MariaDB, MySQL e PósgreSQL reservados
O Advisor analisa os seus padrões de utilização para Azure Database for MariaDB, Azure Database for MySQL e Azure Database for PostgreSQL nos últimos 30 dias. Recomenda então compras de capacidade reservada que otimizem os custos. Ao utilizar a capacidade reservada, pode pré-comprar o uso horário de MariaDB, MySQL e PostgreSQL e economizar acima dos seus custos atuais. A capacidade reservada é um benefício de faturação e aplica-se automaticamente a implementações novas e existentes. O Advisor calcula as estimativas de poupança para subscrições individuais utilizando preços de reserva de 3 anos e os padrões de utilização observados nos últimos 30 dias. As recomendações de âmbito partilhado estão disponíveis para compras de capacidade reservada e podem aumentar a poupança.

### <a name="azure-synapse-analytics-reserved-capacity"></a>Azure Synapse Analytics reservado capacidade
O Advisor analisa os seus padrões de utilização Azure Synapse Analytics ao longo dos últimos 30 dias e recomenda compras de capacidade reservada que otimizem os custos. Ao utilizar a capacidade reservada, pode pré-comprar o Synapse Analytics de hora a hora e economizar acima dos seus custos a pedido. A capacidade reservada é um benefício de faturação e aplica-se automaticamente a implementações novas e existentes. O Advisor calcula as estimativas de poupança para subscrições individuais utilizando preços de reserva de 3 anos e os padrões de utilização observados nos últimos 30 dias. As recomendações de âmbito partilhado estão disponíveis para compras de capacidade reservada e podem aumentar a poupança.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Eliminar endereços IP públicos não ligados para economizar dinheiro

O advisor identifica endereços IP públicos que não estão associados a recursos Azure, como equilibradores de carga e VMs. Uma taxa nominal está associada a estes endereços IP públicos. Se não planeias usá-los, podes poupar dinheiro apagando-os.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Eliminar pipelines do Azure Data Factory com falhas

O Advisor deteta os oleodutos Azure Data Factory que falham repetidamente. Recomenda que resolva os problemas ou elimine os gasodutos se não precisar deles. Estás a cobrar por estes oleodutos, mesmo que não te estejam a servir enquanto eles estão a falhar.

## <a name="use-standard-snapshots-for-managed-disks"></a>Utilize instantâneos padrão para discos geridos
Para poupar 60% do custo, recomendamos guardar as suas fotos em armazenamento padrão, independentemente do tipo de armazenamento do disco principal. Esta opção é a opção padrão para instantâneos de disco geridos. O advisor identifica instantâneos que são armazenados em armazenamento premium e recomenda migrar então de premium para armazenamento padrão. [Saiba mais sobre preços geridos em disco.](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="use-lifecycle-management"></a>Use a gestão do ciclo de vida
Ao utilizar informações sobre a contagem de objetos de armazenamento Azure Blob, tamanho total e transações, o Advisor deteta se deve permitir a gestão do ciclo de vida para dimensionar dados em uma ou mais das suas contas de armazenamento. Solicita-lhe que crie regras de gestão do ciclo de vida para hierarquizar automaticamente os seus dados para arrefecer ou arquivar o armazenamento para otimizar os seus custos de armazenamento, mantendo os seus dados no armazenamento do Azure Blob para compatibilidade com a aplicação.

## <a name="create-an-ephemeral-os-disk-recommendation"></a>Crie uma recomendação de Disco de SO Efémero
[O disco EFÉMER OS](../virtual-machines/ephemeral-os-disks.md) permite:00: 
- Poupe nos custos de armazenamento dos discos de SO. 
- Obtenha uma leitura mais baixa/escreva latência para discos DE. 
- Obtenha operações de reimagem VM mais rápidas repondo o SO (e disco temporário) no seu estado original.

É preferível usar o disco EFÉMER OS Para VMs ou VMs de curta duração com cargas apátridas. O Advisor fornece recomendações para recursos que podem beneficiar do Disco EFÉMER.

## <a name="reduce-azure-data-explorer-table-cache-period-policy-for-cluster-cost-optimization-preview"></a>Reduzir o período de cache da tabela Azure Data Explorer (política) para otimização dos custos do cluster (Pré-visualização)
O Advisor identifica recursos onde a redução da política de cache de mesa libertará os nós do cluster Azure Data Explorer com baixa utilização do CPU, memória e uma configuração de tamanho de cache elevado.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Como aceder às recomendações de custos no Azure Advisor

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Procure e selecione [**Advisor**](https://aka.ms/azureadvisordashboard) a partir de qualquer página.

1. No painel **'Advisor',** selecione o **separador Custos.**

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as recomendações do Advisor, consulte:
* [Introdução ao Conselheiro](advisor-overview.md)
* [Pontuação do conselheiro](azure-advisor-score.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de desempenho do conselheiro](advisor-performance-recommendations.md)
* [Recomendações de alta disponibilidade do conselheiro](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do consultor](advisor-operational-excellence-recommendations.md)