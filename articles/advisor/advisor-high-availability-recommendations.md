---
title: Melhore a disponibilidade da sua aplicação com o Azure Advisor
description: Utilize o Azure Advisor para melhorar a elevada disponibilidade das suas implementações Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 5e38a1fb5e07e3811c53e24a5e324575d6774a75
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788030"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Melhore a disponibilidade da sua aplicação com o Azure Advisor

O Azure Advisor ajuda-o a garantir e melhorar a continuidade das suas aplicações críticas ao negócio. Pode obter recomendações de alta disponibilidade pelo Advisor a partir do separador **de alta disponibilidade** do dashboard Advisor.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Garantir a tolerância à falha da máquina virtual

Para fornecer redundância à aplicação, é recomendável agrupar duas ou mais máquinas virtuais num conjunto de disponibilidade. O Advisor identifica máquinas virtuais que não fazem parte de um conjunto de disponibilidade e recomenda movê-las para um conjunto de disponibilidade. Esta configuração garante que durante um evento de manutenção planeado ou não planeado, pelo menos uma máquina virtual está disponível e encontra a máquina virtual Azure SLA. Pode optar por criar um conjunto de disponibilidade para a máquina virtual ou adicionar a máquina virtual a um conjunto de disponibilidade existente.

> [!NOTE]
> Se optar por criar um conjunto de disponibilidade, deve adicionar pelo menos mais uma máquina virtual. Recomendamos que agrupe duas ou mais máquinas virtuais num conjunto de disponibilidade para garantir que pelo menos uma máquina esteja disponível durante uma paragem.

## <a name="ensure-availability-set-fault-tolerance"></a>Garantir a disponibilidade definida tolerância à falha

Para fornecer redundância à aplicação, é recomendável agrupar duas ou mais máquinas virtuais num conjunto de disponibilidade. O Advisor identifica conjuntos de disponibilidade que contêm uma única máquina virtual e recomenda a adição de uma ou mais máquinas virtuais.Esta configuração garante que durante um evento de manutenção planeado ou não planeado, pelo menos uma máquina virtual está disponível e encontra a máquina virtual Azure SLA.Pode optar por criar uma máquina virtual ou adicionar uma máquina virtual existente ao conjunto de disponibilidade.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Utilizar o Managed Disks para melhorar a fiabilidade dos dados

As máquinas virtuais que se encontram num conjunto de disponibilidade com discos que partilham contas de armazenamento ou unidades de escala de armazenamento não são resistentes a falhas de unidade de escala de armazenamento única durante as interrupções. O Advisor identificará estes conjuntos de disponibilidade e recomendará a migração para discos geridos pelo Azure. Isto garantirá que os discos das diferentes máquinas virtuais no conjunto de disponibilidade estão suficientemente isolados para evitar um único ponto de falha. 

## <a name="known-issue-with-check-point-network-virtual-appliance-image-version"></a>Problema conhecido com versão de imagem de check point rede virtual eletrodoméstico

O Advisor pode identificar se a sua Máquina Virtual pode estar a executar uma versão da imagem do Check Point que se sabe perder a conectividade da rede em caso de operação de manutenção da plataforma. A recomendação do advisor irá ajudá-lo a atualizar para uma versão mais recente da imagem que aborda este problema. Isto garantirá a continuidade do negócio através de uma melhor conectividade da rede.

## <a name="ensure-application-gateway-fault-tolerance"></a>Garantir a tolerância à falha do gateway da aplicação

Esta recomendação garante a continuidade do negócio das aplicações críticas da missão que são alimentadas por gateways de aplicação. O Advisor identifica casos de gateway de aplicação que não estão configurados para tolerância a falhas, e sugere ações de reparação que você pode tomar. O Advisor identifica portais de aplicação de média ou grande instância única, e recomenda adicionar pelo menos mais uma instância. Também identifica pequenos portais de aplicação de pequenas ou múltiplas instâncias e recomenda a migração para SKUs médiaou grande. O Advisor recomenda estas ações para garantir que as instâncias de gateway da sua aplicação estão configuradas para satisfazer os requisitos atuais do SLA para estes recursos.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Proteja os dados da sua máquina virtual contra a eliminação acidental

A criação de cópias de segurança de máquinas virtuais garante a disponibilidade dos seus dados críticos de negócio e oferece proteção contra a eliminação acidental ou corrupção. O Advisor identifica máquinas virtuais onde a cópia de segurança não está ativada e recomenda a cópia de segurança. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Certifique-se de que tem acesso a especialistas em nuvem Azure quando precisar

Ao executar uma carga de trabalho crítica do negócio, é importante ter acesso ao suporte técnico quando necessário. O Advisor identifica potenciais subscrições críticas ao negócio que não têm apoio técnico incluído no seu plano de suporte e recomenda a atualização para uma opção que inclui suporte técnico.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Create Azure Service Health alerta para ser notificado quando problemas do Azure o afetam

Recomendamos que a criação de alertas de saúde de serviço Azure seja notificada quando os problemas de serviço do Azure o afetarem. [A Azure Service Health](https://azure.microsoft.com/features/service-health/) é um serviço gratuito que fornece orientação e suporte personalizados quando é afetado por um problema de serviço azure. O Advisor identifica subscrições que não têm alertas configurados e recomenda a criação de uma.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Configure Pontos finais do Gestor de Tráfego para resiliência

Perfis do Gestor de Tráfego com mais de um ponto final experimentam maior disponibilidade se algum ponto final falhar. A colocação de pontos finais em diferentes regiões melhora ainda mais a fiabilidade do serviço. O advisor identifica os perfis de Traffic Manger onde há apenas um ponto final e recomenda a adição de pelo menos mais um ponto final noutra região.

Se todos os pontos finais de um perfil do Gestor de Tráfego configurado para o encaminhamento de proximidade estiverem na mesma região, os utilizadores de outras regiões podem sofrer atrasos de ligação. Adicionar ou mover um ponto final para outra região melhorará o desempenho global e proporcionará uma melhor disponibilidade se todos os pontos finais de uma região falharem. O Advisor identifica os perfis do Traffic Manager configurados para o encaminhamento de proximidade onde todos os pontos finais estão na mesma região. Recomenda adicionar ou mover um ponto final para outra região de Azure.

Se um perfil do Gestor de Tráfego estiver configurado para encaminhamento geográfico, então o tráfego é encaminhado para pontos finais com base em regiões definidas. Se uma região falhar, não há falhas pré-definidas. Ter um ponto final onde o Agrupamento Regional está configurado para "All (Mundo)" evitará que o tráfego seja retirado e melhore a disponibilidade de serviços. O Advisor identifica os perfis do Traffic Manager configurados para encaminhamento geográfico onde não há um ponto final configurado para ter o Agrupamento Regional como "All (Mundo)". Recomenda alterar a configuração para fazer um ponto final "All (Mundo).

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Utilize a eliminação suave na sua Conta de Armazenamento Azure para guardar e recuperar dados após sobrepor ou supressão acidental

Ative a [eliminação suave](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) na sua conta de armazenamento de modo a que as bolhas eliminadas transitem para um estado suave apagado em vez de serem permanentemente eliminadas. Quando os dados são substituídos, é gerado um instantâneo de eliminação de forma recuperável para guardar o estado dos dados substituídos. A utilização de uma eliminação suave permite-lhe recuperar se houver supressões acidentais ou sobreposições. O Advisor identifica contas de Armazenamento Azure que não têm eliminação suave ativada e sugere que o ative.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Configure a sua porta de entrada VPN para ativo para a resiliência da ligação

Na configuração ativa, ambas as instâncias de um gateway VPN estabelecerão túneis S2S VPN para o seu dispositivo VPN no local. Quando um evento de manutenção planeado ou evento não planeado acontece a uma instância de gateway, o tráfego será automaticamente ligado para o outro túnel ativo do IPsec. O Azure Advisor identificará gateways VPN que não estão configurados como ativos e sugere que os configure para alta disponibilidade.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Use gateways VPN de produção para executar as suas cargas de trabalho de produção

O Azure Advisor irá verificar se existem gateways VPN que sejam um SKU Básico e recomendem que utilize um SKU de produção. O SKU Básico foi concebido para fins de desenvolvimento e teste. As SKUs de produção oferecem um maior número de túneis, suporte BGP, opções de configuração ativa, política ipsec/IKE personalizada, e maior estabilidade e disponibilidade.

## <a name="repair-invalid-log-alert-rules"></a>Reparação de regras de alerta de registo inválidas

O Azure Advisor irá detetar regras de alerta que tenham consultas inválidas especificadas na sua secção de condições. As regras de alerta de registo são criadas no Azure Monitor e são utilizadas para executar consultas de análise em intervalos especificados. Os resultados da consulta determinam se um alerta tem de ser acionado. As consultas de análise podem tornar-se inválidas ao longo do tempo devido a alterações em recursos, tabelas ou comandos referenciados. O advisor recomendará que corrija a consulta na regra de alerta para evitar que seja automaticamente desativada e garanta a cobertura de monitorização dos seus recursos em Azure. [Saiba mais sobre regras de alerta de resolução de problemas](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Configure o modo de indexação consistente na sua coleção Cosmos DB

Os recipientes Azure Cosmos DB configurados com o modo de indexação preguiçoso podem ter impacto na frescura dos resultados da consulta. O Advisor detetará recipientes configurados desta forma e recomendará a mudança para o modo Consistente. [Saiba mais sobre políticas de indexação em Cosmos DB](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Configurar os contentores do Azure Cosmos DB com uma chave de partição

O Azure Advisor identificará coleções não partilhadas da Azure Cosmos DB que se aproximam da sua quota de armazenamento aprovisionada. Recomendará a migração destas coleções para novas coleções com uma definição chave de partição para que possam ser automaticamente dimensionadas pelo serviço. [Saiba mais sobre a escolha de uma chave de partição](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Atualizar o SDK .NET do Azure Cosmos DB para a versão mais recente do Nuget

O Azure Advisor identificará as contas do Azure Cosmos DB que estão a utilizar versões antigas do .NET SDK e recomendará o upgrade para a versão mais recente da Nuget para as mais recentes correções, melhorias de desempenho e novas capacidades de funcionalidade. [Saiba mais sobre cosmos DB .NET SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Atualizar o SDK Java do Azure Cosmos DB para a versão mais recente a partir do Maven

O Azure Advisor identificará as contas do Azure Cosmos DB que estão a utilizar versões antigas do Java SDK e recomendará o upgrade para a versão mais recente da Maven para as mais recentes correções, melhorias de desempenho e novas capacidades de funcionalidade. [Saiba mais sobre cosmos DB Java SDK](https://aka.ms/cosmosdb/sql-api-sdk-async-java)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Atualizar o Conector Spark do Azure Cosmos DB para a versão mais recente a partir do Maven

O Azure Advisor identificará as contas do Azure Cosmos DB que estão a utilizar versões antigas do conector Cosmos DB Spark e recomendará o upgrade para a versão mais recente da Maven para as mais recentes correções, melhorias de desempenho e novas capacidades de funcionalidade. [Saiba mais sobre o conector Cosmos DB Spark](https://aka.ms/cosmosdb/spark-connector)

## <a name="enable-virtual-machine-replication"></a>Ativar a replicação da máquina virtual
As máquinas virtuais que não têm replicação habilitada a outra região não são resistentes às interrupções regionais. A replicação de máquinas virtuais reduz qualquer impacto adverso no negócio durante o período de paragem da região de Azure. O Advisor detetará VMs que não tenham replicação ativada e recomende a replicação para que, em caso de paragem, possa rapidamente trazer as suas máquinas virtuais numa região remota de Azure. [Saiba mais sobre a replicação de máquinas virtuais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Como aceder a recomendações de alta disponibilidade em Advisor

1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, abra [o Advisor.](https://aka.ms/azureadvisordashboard)

2.  No painel advisor, clique no separador **De Alta Disponibilidade.**

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre recomendações do Advisor, consulte:
* [Introdução ao Consultor Azure](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de Custos Consultivos](advisor-cost-recommendations.md)
* [Recomendações de Desempenho Do Conselheiro](advisor-performance-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
* [Recomendações de Excelência Operacional Consultiva](advisor-operational-excellence-recommendations.md)
