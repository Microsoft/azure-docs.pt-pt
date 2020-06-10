---
title: Melhorar a disponibilidade da sua candidatura com o Azure Advisor
description: Utilize o Azure Advisor para melhorar a elevada disponibilidade das suas implementações Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: bed092a51b5a4aba1dfa64c17f5ed3d6f72212da
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84658480"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Melhorar a disponibilidade da sua candidatura com o Azure Advisor

O Azure Advisor ajuda-o a garantir e a melhorar a continuidade das suas aplicações críticas ao negócio. Pode obter recomendações de alta disponibilidade pelo Advisor a partir do separador De **Alta Disponibilidade** do painel Advisor.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Garantir tolerância à falha da máquina virtual

Para fornecer redundância à aplicação, é recomendável agrupar duas ou mais máquinas virtuais num conjunto de disponibilidade. O Advisor identifica máquinas virtuais que não fazem parte de um conjunto de disponibilidade e recomenda movê-las para um conjunto de disponibilidade. Esta configuração garante que durante um evento de manutenção planeado ou não planeado, pelo menos uma máquina virtual está disponível e encontra a máquina virtual Azure SLA. Pode optar por criar um conjunto de disponibilidade para a máquina virtual ou adicionar a máquina virtual a um conjunto de disponibilidade existente.

> [!NOTE]
> Se optar por criar um conjunto de disponibilidade, deve adicionar pelo menos mais uma máquina virtual. Recomendamos que agrupar duas ou mais máquinas virtuais num conjunto de disponibilidades para garantir que pelo menos uma máquina esteja disponível durante uma paragem.

## <a name="ensure-availability-set-fault-tolerance"></a>Garantir tolerância de falha definida de disponibilidade

Para fornecer redundância à aplicação, é recomendável agrupar duas ou mais máquinas virtuais num conjunto de disponibilidade. O Advisor identifica conjuntos de disponibilidade que contêm uma única máquina virtual e recomenda a adição de uma ou mais máquinas virtuais à sua parte.Esta configuração garante que durante um evento de manutenção planeado ou não planeado, pelo menos uma máquina virtual está disponível e encontra a máquina virtual Azure SLA.Pode optar por criar uma máquina virtual ou adicionar uma máquina virtual existente ao conjunto de disponibilidade.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Utilizar o Managed Disks para melhorar a fiabilidade dos dados

As máquinas virtuais que estão num conjunto de disponibilidade com discos que partilham contas de armazenamento ou unidades de escala de armazenamento não são resistentes a falhas de unidade de escala de armazenamento única durante as interrupções. O Advisor identificará estes conjuntos de disponibilidade e recomendará a migração para discos geridos Azure. Isto garantirá que os discos das diferentes máquinas virtuais do conjunto de disponibilidades estejam suficientemente isolados para evitar um único ponto de falha. 

## <a name="known-issue-with-check-point-network-virtual-appliance-image-version"></a>Problema conhecido com a versão da imagem da Aplicação Virtual de Rede da Check Point

O Advisor pode identificar se a sua Máquina Virtual pode estar a executar uma versão da imagem do Check Point que é conhecida por perder a conectividade da rede em caso de funcionamento da plataforma. A recomendação do conselheiro irá ajudá-lo a atualizar para uma versão mais recente da imagem que aborda este problema. Isto garantirá a continuidade do negócio através de uma melhor conectividade de rede.

## <a name="ensure-application-gateway-fault-tolerance"></a>Garantir tolerância à falha do gateway de aplicação

Esta recomendação garante a continuidade do negócio de aplicações críticas de missão que são alimentadas por gateways de aplicações. O advisor identifica casos de gateway de aplicação que não estão configurados para tolerância a falhas, e sugere ações de reparação que você pode tomar. O advisor identifica os gateways de aplicação de uma instância única média ou grande, e recomenda adicionar pelo menos mais uma instância. Identifica igualmente pequenos portais de aplicação de pequenas ou múltiplas instâncias e recomenda a migração para SKUs médios ou grandes. O Advisor recomenda estas ações para garantir que as instâncias de gateway da sua aplicação sejam configuradas para satisfazer os atuais requisitos de SLA para estes recursos.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Proteja os dados da sua máquina virtual contra a eliminação acidental

A configuração de cópias de segurança de máquinas virtuais garante a disponibilidade dos dados críticos do seu negócio e oferece proteção contra a eliminação acidental ou corrupção. O Advisor identifica máquinas virtuais onde a cópia de segurança não está ativada e recomenda ativar a cópia de segurança. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Certifique-se de que tem acesso a especialistas em nuvem Azure quando precisar

Ao executar uma carga de trabalho crítica de negócios, é importante ter acesso ao suporte técnico quando necessário. O Advisor identifica potenciais subscrições críticas ao negócio que não têm suporte técnico incluído no seu plano de suporte e recomenda o upgrade para uma opção que inclua suporte técnico.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Crie alertas de Saúde do Serviço Azure para ser notificado quando os problemas do Azure o afetarem

Recomendamos a criação de alertas de Saúde do Serviço Azure para serem notificados quando os problemas de serviço da Azure o afetarem. [O Azure Service Health](https://azure.microsoft.com/features/service-health/) é um serviço gratuito que fornece orientação e suporte personalizados quando é afetado por um problema de serviço Azure. O Advisor identifica subscrições que não têm alertas configurados e recomenda a criação de uma.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Configurar pontos finais do Gestor de Tráfego para a resiliência

Os perfis do Gestor de Tráfego com mais de um ponto final experimentam uma maior disponibilidade se algum ponto final falhar. A colocação de pontos finais em diferentes regiões melhora ainda mais a fiabilidade do serviço. O advisor identifica os perfis da Traffic Manger onde existe apenas um ponto final e recomenda a adição de pelo menos mais um ponto final noutra região.

Se todos os pontos finais de um perfil de Gestor de Tráfego configurado para encaminhamento de proximidade estiverem na mesma região, os utilizadores de outras regiões podem sofrer atrasos de ligação. Adicionar ou mover um ponto final para outra região melhorará o desempenho global e proporcionará uma melhor disponibilidade se todos os pontos finais de uma região falharem. O conselheiro identifica os perfis do Gestor de Tráfego configurados para o encaminhamento de proximidade onde todos os pontos finais estão na mesma região. Recomenda a adição ou a mudança de um ponto final para outra região de Azure.

Se um perfil de Gestor de Tráfego estiver configurado para o encaminhamento geográfico, então o tráfego é encaminhado para pontos finais com base em regiões definidas. Se uma região falhar, não há falha pré-definida. Ter um ponto final onde o Agrupamento Regional está configurado para "All (World)" evitará que o tráfego seja retirado e melhore a disponibilidade do serviço. O Conselheiro identifica os perfis do Gestor de Tráfego configurados para o encaminhamento geográfico onde não existe um ponto final configurado para ter o Agrupamento Regional como "All (World)". Recomenda alterar a configuração para fazer um ponto final "All (World).

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Utilize a eliminação suave na sua Conta de Armazenamento Azure para guardar e recuperar dados após substituição acidental ou eliminação

Ativar [a eliminação suave](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) na sua conta de armazenamento de modo a que as bolhas apagadas transitem para um estado de eliminação suave em vez de serem permanentemente eliminadas. Quando os dados são substituídos, é gerado um instantâneo de eliminação de forma recuperável para guardar o estado dos dados substituídos. A utilização de eliminações suaves permite-lhe recuperar se houver eliminações acidentais ou substituições. O Advisor identifica as contas de Armazenamento Azure que não têm exclusão suave ativada e sugere que o ative.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Configure a sua porta de entrada VPN para ativar para a resiliência da conexão

Na configuração ativa, ambas as instâncias de um gateway VPN estabelecerão túneis VPN S2S para o seu dispositivo VPN no local. Quando um evento de manutenção planeado ou evento não planeado acontece a uma instância de gateway, o tráfego será automaticamente transferido para o outro túnel IPsec ativo. O Azure Advisor identificará gateways VPN que não estão configurados como ativos e sugerirá que os configure para uma elevada disponibilidade.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Use gateways VPN de produção para executar as suas cargas de trabalho de produção

O Azure Advisor verificará se existem portas VPN que sejam um SKU Básico e recomendará que utilize uma SKU de produção. O SKU Básico foi concebido para fins de desenvolvimento e teste. Os SKUs de produção oferecem um maior número de túneis, suporte BGP, opções de configuração ativa, política ipsec/IKE personalizada, e maior estabilidade e disponibilidade.

## <a name="repair-invalid-log-alert-rules"></a>Reparar regras de alerta de registo inválido

O Azure Advisor detetará regras de alerta que tenham consultas inválidas especificadas na sua secção de condições. As regras de alerta de registo são criadas no Azure Monitor e são utilizadas para executar consultas de análise em intervalos especificados. Os resultados da consulta determinam se um alerta tem de ser acionado. As consultas de análise podem tornar-se inválidas ao longo do tempo devido a alterações em recursos, tabelas ou comandos referenciados. O Advisor recomendará que corrija a consulta na regra de alerta para evitar que fique desativada automaticamente e garanta a cobertura de monitorização dos seus recursos em Azure. [Saiba mais sobre regras de alerta de resolução de problemas](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Configurar o modo de indexação consistente na sua coleção Cosmos DB

Os recipientes DB Azure Cosmos configurados com o modo de indexação Preguiçoso podem ter impacto na frescura dos resultados da consulta. O advisor detetará os recipientes configurados desta forma e recomendará a mudança para o modo Consistente. [Saiba mais sobre a indexação das políticas em Cosmos DB](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Configurar os contentores do Azure Cosmos DB com uma chave de partição

O Azure Advisor identificará as coleções não divididas da Azure Cosmos que se aproximam da sua quota de armazenamento abastada. Recomendará a migração destas coleções para novas coleções com uma definição de chave de partição para que possam ser automaticamente dimensionada pelo serviço. [Saiba mais sobre a escolha de uma chave de partição](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Atualizar o SDK .NET do Azure Cosmos DB para a versão mais recente do Nuget

O Azure Advisor irá identificar as contas DB da Azure Cosmos que estão a utilizar versões antigas do .NET SDK e recomendará o upgrade para a versão mais recente da Nuget para as mais recentes correções, melhorias de desempenho e novas capacidades de funcionalidade. [Saiba mais sobre cosmos DB .NET SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Atualizar o SDK Java do Azure Cosmos DB para a versão mais recente a partir do Maven

O Azure Advisor irá identificar as contas DB da Azure Cosmos que estão a utilizar versões antigas do SDK Java e recomendará o upgrade para a versão mais recente da Maven para as mais recentes correções, melhorias de desempenho e novas capacidades de recurso. [Saiba mais sobre cosmos DB Java SDK](https://aka.ms/cosmosdb/sql-api-sdk-async-java)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Atualizar o Conector Spark do Azure Cosmos DB para a versão mais recente a partir do Maven

O Azure Advisor irá identificar as contas DB da Azure Cosmos que estão a utilizar versões antigas do conector Cosmos DB Spark e recomendar o upgrade para a versão mais recente da Maven para as mais recentes correções, melhorias de desempenho e novas capacidades de recurso. [Saiba mais sobre o conector Cosmos DB Spark](https://aka.ms/cosmosdb/spark-connector)

## <a name="upgrade-recommendation-for-deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Recomendação de upgrade para depreciação de Kafka 1.1 em HDInsight 4.0 Kafka cluster

A partir de 1 de julho de 2020, os clientes deixarão de poder criar novos clusters do Kafka com o Kafka 1.1 no HDInsight 4.0. Os clusters existentes funcionarão tal como estão sem o suporte da Microsoft. Considere migrar para o Kafka 2.1 no HDInsight 4.0 até 30 de junho de 2020 para evitar a potencial interrupção do sistema/suporte.

## <a name="upgrade-recommendation-for-deprecation-of-older-spark-versions-in-hdinsight-spark-cluster"></a>Recomendação de upgrade para depreciação de versões de faíscas mais antigas no cluster HDInsight Spark

A partir de 1 de julho de 2020, os clientes deixarão de poder criar novos clusters do Spark com o Spark 2.1 e 2.2 no HDInsight 3.6 e o Spark 2.3 no HDInsight 4.0. Os clusters existentes funcionarão tal como estão sem o suporte da Microsoft. ",

## <a name="enable-virtual-machine-replication"></a>Ativar a replicação da máquina virtual
As máquinas virtuais que não têm replicação ativada para outra região não são resistentes a interrupções regionais. A replicação de máquinas virtuais reduz qualquer impacto adverso no negócio durante o período de paragem da região de Azure. O Advisor detetará VMs que não têm replicação ativada e recomendará a replicação ativada para que, em caso de paragem, possa rapidamente trazer as suas máquinas virtuais numa região remota de Azure. [Saiba mais sobre a replicação da máquina virtual](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Como aceder a recomendações de Alta Disponibilidade no Advisor

1. Inscreva-se no [portal Azure,](https://portal.azure.com)e depois abra [o Advisor](https://aka.ms/azureadvisordashboard).

2.  No painel Advisor, clique no separador **Alta Disponibilidade.**

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre recomendações do Advisor, consulte:
* [Introdução ao Conselheiro](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de Custos Do Consultor](advisor-cost-recommendations.md)
* [Recomendações de Desempenho do Conselheiro](advisor-performance-recommendations.md)
* [Recomendações de Segurança do Conselheiro](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do consultor](advisor-operational-excellence-recommendations.md)
