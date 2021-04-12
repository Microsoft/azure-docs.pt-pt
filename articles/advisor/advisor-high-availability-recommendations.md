---
title: Melhorar a fiabilidade da sua candidatura com o Advisor
description: Utilize o Azure Advisor para garantir e melhorar a fiabilidade nas suas implementações Azure críticas ao negócio.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: c96b47f1ee145129f4c14c6646f93abeb8a5aac9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579972"
---
# <a name="improve-the-reliability-of-your-application-by-using-azure-advisor"></a>Melhore a fiabilidade da sua aplicação utilizando o Azure Advisor

O Azure Advisor ajuda-o a garantir e a melhorar a continuidade das suas aplicações críticas ao negócio. Pode obter recomendações de fiabilidade do Advisor no separador **de Fiabilidade** do painel Advisor.

## <a name="check-the-version-of-your-check-point-network-virtual-appliance-image"></a>Verifique a versão da imagem virtual do seu aparelho virtual da rede Check Point

O Advisor pode identificar se a sua máquina virtual está a executar uma versão da imagem Check Point que é conhecida por perder conectividade de rede durante as operações de manutenção da plataforma. A recomendação do Advisor irá ajudá-lo a atualizar para uma versão mais recente da imagem que aborda este problema. Esta verificação garantirá a continuidade do negócio através de uma melhor conectividade de rede.

## <a name="ensure-application-gateway-fault-tolerance"></a>Garantir tolerância à falha do gateway de aplicação

Esta recomendação garante a continuidade do negócio de aplicações críticas de missão que são alimentadas por gateways de aplicações. O conselheiro identifica instâncias de gateway de aplicações que não estão configuradas para tolerância a falhas. Em seguida, sugere medidas de reparação que pode tomar. O advisor identifica os gateways de aplicação de uma instância única média ou grande e recomenda a adição de pelo menos mais uma instância. Identifica igualmente os gateways de aplicações de instância única e de múltiplas instâncias e recomenda a sua migração para SKUs médios ou grandes. O Advisor recomenda estas ações para garantir que as instâncias de gateway de aplicação estão configuradas para satisfazer os atuais requisitos de SLA para estes recursos.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Proteja os dados da sua máquina virtual contra a eliminação acidental

A configuração de cópias de segurança de máquinas virtuais garante a disponibilidade dos dados críticos do seu negócio e oferece proteção contra a eliminação acidental ou corrupção. O Advisor identifica máquinas virtuais onde a cópia de segurança não está ativada e recomenda ativar a cópia de segurança. 

## <a name="ensure-you-have-access-to-azure-experts-when-you-need-it"></a>Certifique-se de que tem acesso a especialistas da Azure quando precisar

Quando se está a executar uma carga de trabalho crítica de negócios, é importante ter acesso a suporte técnico quando se precisa. O Advisor identifica potenciais subscrições críticas ao negócio que não têm suporte técnico incluído nos seus planos de suporte. Recomenda o upgrade para uma opção que inclua suporte técnico.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Crie alertas de Saúde do Serviço Azure para ser notificado quando os problemas do Azure o afetarem

Recomendamos a criação de alertas de Saúde do Serviço Azure para que seja notificado quando os problemas de serviço do Azure o afetarem. [O Azure Service Health](https://azure.microsoft.com/features/service-health/) é um serviço gratuito que fornece orientação e suporte personalizados quando é afetado por um problema de serviço Azure. O Advisor identifica subscrições que não têm alertas configurados e recomenda a sua configuração.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Configurar pontos finais do Gestor de Tráfego para a resiliência

Os perfis do Azure Traffic Manager com mais de um ponto final experimentam uma maior disponibilidade se algum ponto final falhar. A colocação de pontos finais em diferentes regiões melhora ainda mais a fiabilidade do serviço. O advisor identifica os perfis da Traffic Manger onde há apenas um ponto final e recomenda adicionar pelo menos mais um ponto final noutra região.

Se todos os pontos finais de um perfil de Gestor de Tráfego configurado para encaminhamento de proximidade estiverem na mesma região, os utilizadores de outras regiões podem sofrer atrasos de ligação. Adicionar ou mover um ponto final para outra região melhorará o desempenho global e proporcionará uma melhor disponibilidade se todos os pontos finais de uma região falharem. O conselheiro identifica os perfis do Gestor de Tráfego configurados para o encaminhamento de proximidade onde todos os pontos finais estão na mesma região. Recomenda a adição ou a mudança de um ponto final para outra região de Azure.

Se um perfil de Gestor de Tráfego estiver configurado para o encaminhamento geográfico, o tráfego é encaminhado para pontos finais com base em regiões definidas. Se uma região falhar, não há falhas predefinidas. Se tiver um ponto final onde o Agrupamento Regional está configurado para **All (Mundo),** pode evitar o tráfego em queda e melhorar a disponibilidade do serviço. O Conselheiro identifica os perfis do Gestor de Tráfego configurados para o encaminhamento geográfico onde não há ponto final configurado para ter o Agrupamento Regional como **All (Mundo)**. Recomenda alterar a configuração para fazer um ponto final **All (Mundo)**.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Utilize a eliminação suave na sua conta de armazenamento Azure para guardar e recuperar dados após substituição acidental ou eliminação

Ativar [a eliminação suave](../storage/blobs/soft-delete-blob-overview.md) na sua conta de armazenamento de modo a que as bolhas apagadas transitem para um estado de eliminação suave em vez de serem permanentemente eliminadas. Quando os dados são substituídos, é gerado um instantâneo de eliminação de forma recuperável para guardar o estado dos dados substituídos. A utilização de eliminações suaves permite-lhe recuperar de supressões acidentais ou substituições. O Advisor identifica as contas de armazenamento Azure que não têm exclusão suave ativada e sugere que a ative.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Configure a sua porta de entrada VPN para ativar para a resiliência da conexão

Na configuração ativa, ambas as instâncias de um gateway VPN estabelecem túneis VPN S2S para o seu dispositivo VPN no local. Quando um evento de manutenção planeado ou evento não planeado acontece a uma instância de gateway, o tráfego é automaticamente mudado para o outro túnel IPsec ativo. O Azure Advisor identifica gateways VPN que não estão configurados como ativos e sugere que os configure para uma elevada disponibilidade.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Use gateways VPN de produção para executar as suas cargas de trabalho de produção

O Azure Advisor verifica quaisquer gateways VPN que utilizem um SKU Básico e recomenda que utilize uma SKU de produção. O SKU Básico foi concebido para o desenvolvimento e teste. Oferta de SKUs de produção:
- Mais túneis. 
- Suporte BGP. 
- Opções de configuração ativa. 
- Política ipsec/IKE personalizada. 
- Maior estabilidade e disponibilidade.

## <a name="ensure-virtual-machine-fault-tolerance-temporarily-disabled"></a>Garantir tolerância à falha da máquina virtual (temporariamente desativada)

Para fornecer redundância para a sua aplicação, recomendamos que agrupar duas ou mais máquinas virtuais num conjunto de disponibilidade. O advisor identifica máquinas virtuais que não fazem parte de um conjunto de disponibilidade e recomenda movê-las para uma. Esta configuração garante que durante a manutenção planeada ou não planeada, pelo menos uma máquina virtual está disponível e encontra a máquina virtual Azure SLA. Pode optar por criar um conjunto de disponibilidade para a máquina virtual ou adicionar a máquina virtual a um conjunto de disponibilidade existente.

> [!NOTE]
> Se optar por criar um conjunto de disponibilidade, tem de adicionar pelo menos mais uma máquina virtual. Recomendamos que agrupar duas ou mais máquinas virtuais num conjunto de disponibilidades para garantir que pelo menos uma máquina esteja disponível durante uma paragem.

## <a name="ensure-availability-set-fault-tolerance-temporarily-disabled"></a>Garantir a tolerância à falha definida pela disponibilidade (temporariamente desativada)

Para fornecer redundância para a sua aplicação, recomendamos que agrupar duas ou mais máquinas virtuais num conjunto de disponibilidade. O Advisor identifica conjuntos de disponibilidade que contêm uma única máquina virtual e recomenda a adição de uma ou mais máquinas virtuais à sua parte. Esta configuração garante que durante a manutenção planeada ou não planeada, pelo menos uma máquina virtual está disponível e encontra a máquina virtual Azure SLA. Pode optar por criar uma máquina virtual ou adicionar uma máquina virtual existente ao conjunto de disponibilidade.  

## <a name="use-managed-disks-to-improve-data-reliability-temporarily-disabled"></a>Utilize discos geridos para melhorar a fiabilidade dos dados (temporariamente desativado)

As máquinas virtuais que estão num conjunto de disponibilidade com discos que partilham contas de armazenamento ou unidades de escala de armazenamento não são resistentes a falhas em unidades de escala única de armazenamento durante as interrupções. O Advisor identifica estes conjuntos de disponibilidade e recomenda a migração para discos geridos aZure. Esta migração assegurará que os discos das máquinas virtuais no conjunto de disponibilidades estejam suficientemente isolados para evitar um único ponto de falha. 

## <a name="repair-invalid-log-alert-rules"></a>Reparar regras de alerta de registo inválido

O Azure Advisor deteta regras de alerta de registo que têm consultas inválidas especificadas na sua secção de condições. As regras de alerta de registo do Azure Monitor executam consultas em alertas de frequência e incêndio especificados com base nos resultados. As consultas podem tornar-se inválidas ao longo do tempo devido a alterações nos recursos, tabelas ou comandos referenciados. O Advisor recomenda correções para consultas de alerta para evitar que as regras sejam automaticamente desativadas e para garantir a cobertura de monitorização. Para mais informações, consulte [as regras de alerta de resolução de problemas](../azure-monitor/alerts/alerts-troubleshoot-log.md#query-used-in-a-log-alert-isnt-valid)

## <a name="configure-consistent-indexing-mode-on-your-azure-cosmos-db-collection"></a>Configurar o modo de indexação consistente na sua coleção DB Azure Cosmos

Configurar recipientes DB Azure Cosmos com modo de indexação preguiçoso pode afetar a frescura dos resultados da consulta. O Advisor deteta os recipientes configurados desta forma e recomenda a mudança para o modo Consistente. [Saiba mais sobre a indexação das políticas em Azure Cosmos DB.](../cosmos-db/how-to-manage-indexing-policy.md)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Configurar os contentores do Azure Cosmos DB com uma chave de partição

O Azure Advisor identifica as coleções não divididas da Azure Cosmos que se aproximam da sua quota de armazenamento abastada. Recomenda que migrar estas coleções para novas coleções com uma definição de chave de partição para que possam ser automaticamente dimensionadas pelo serviço. [Saiba mais sobre a escolha de uma chave de partição.](../cosmos-db/partitioning-overview.md)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Atualize o seu Azure Cosmos DB .NET SDK para a versão mais recente do NuGet

O Azure Advisor identifica as contas DB da Azure Cosmos que estão a utilizar versões antigas do .NET SDK. Recomenda que faça o upgrade para a versão mais recente do NuGet para as mais recentes correções, melhorias de desempenho e capacidades de funcionalidade. [Saiba mais sobre a Azure Cosmos DB .NET SDK.](../cosmos-db/sql-api-sdk-dotnet-standard.md)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Atualizar o SDK Java do Azure Cosmos DB para a versão mais recente a partir do Maven

O Azure Advisor identifica as contas DB da Azure Cosmos que estão a usar versões antigas do SDK Java. Recomenda que atualize para a versão mais recente da Maven para as mais recentes correções, melhorias de desempenho e capacidades de funcionalidade. [Saiba mais sobre a Azure Cosmos DB Java SDK.](../cosmos-db/sql-api-sdk-java-v4.md)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Atualize o seu conector Azure Cosmos DB Spark para a versão mais recente da Maven

O Azure Advisor identifica as contas DB da Azure Cosmos que estão a utilizar versões antigas do conector Azure Cosmos DB Spark. Recomenda que atualize para a versão mais recente da Maven para as mais recentes correções, melhorias de desempenho e capacidades de funcionalidade. [Saiba mais sobre o conector Azure Cosmos DB Spark.](../cosmos-db/spark-connector.md)

## <a name="consider-moving-to-kafka-21-on-hdinsight-40"></a>Considere mudar-se para Kafka 2.1 em HDInsight 4.0

A partir de 1 de julho de 2020, não poderá criar novos clusters Kafka usando Kafka 1.1 em Azure HDInsight 4.0. Os clusters existentes funcionarão tal como estão sem o suporte da Microsoft. Considere mudar-se para Kafka 2.1 no HDInsight 4.0 até 30 de junho de 2020, para evitar possíveis interrupções do sistema/suporte.

## <a name="consider-upgrading-older-spark-versions-in-hdinsight-spark-clusters"></a>Considere atualizar versões spark mais antigas em clusters HDInsight Spark

A partir de 1 de julho de 2020, não será capaz de criar novos clusters Spark usando Spark 2.1 ou 2.2 no HDInsight 3.6. Não será capaz de criar novos clusters Spark usando Spark 2.3 em HDInsight 4.0. Os clusters existentes funcionarão tal como estão sem o suporte da Microsoft. 

## <a name="enable-virtual-machine-replication"></a>Ativar a replicação da máquina virtual
Máquinas virtuais que não têm replicação ativada para outra região não são resistentes a interrupções regionais. A replicação de máquinas virtuais reduz qualquer impacto adverso no negócio durante as interrupções da região de Azure. O Advisor deteta VMs em que a replicação não está ativada e recomenda que a habilita. Quando ativa a replicação, se houver uma paragem, pode rapidamente trazer as suas máquinas virtuais para uma região remota de Azure. [Saiba mais sobre a replicação de máquinas virtuais.](../site-recovery/azure-to-azure-quickstart.md)

## <a name="upgrade-to-the-latest-version-of-the-azure-connected-machine-agent"></a>Atualizar para a versão mais recente do agente do Azure Connected Machine
O [agente Azure Connected Machine](../azure-arc/servers/manage-agent.md) é atualizado regularmente com correções de erros, melhorias de estabilidade e novas funcionalidades. Identificámos recursos que não estão a trabalhar na versão mais recente do agente de máquinas e esta recomendação do Advisor sugere que atualize o seu agente para a versão mais recente para a melhor experiência do Azure Arc.

## <a name="do-not-override-hostname-to-ensure-website-integrity"></a>Não substituir o nome do anfitrião para garantir a integridade do site
O Advisor recomenda que tente evitar a sobredição do nome de anfitrião ao configurar o Gateway de Aplicação. Ter um domínio diferente no front-end do Gateway de Aplicação do que o utilizado para aceder ao back-end pode potencialmente levar a cookies ou URLs de redirecionamento quebrados. No entanto, poderá não acontecer em todas as situações e determinadas categorias de back-ends (como APIS REST) em geral são menos sensíveis a tal. Confirme se o back-end é capaz de lidar com esta situação ou atualize a configuração do Gateway de Aplicação para que o nome do anfitrião não precise de ser substituído no back-end. Quando utilizado com o Serviço de Aplicações, anexe um nome de domínio personalizado à Web App e evite a utilização do nome de *anfitrião .azurewebsites.net para o backend.* [Saiba mais sobre o domínio personalizado.](../application-gateway/troubleshoot-app-service-redirection-app-service-url.md)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Como aceder a recomendações de elevada disponibilidade no Advisor

1. Inscreva-se no [portal Azure,](https://portal.azure.com)e depois abra [o Advisor](https://aka.ms/azureadvisordashboard).

2.  No painel Advisor, selecione o separador **Alta Disponibilidade.**

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre recomendações do Advisor, consulte:
* [Introdução ao Conselheiro](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Pontuação do conselheiro](azure-advisor-score.md)
* [Recomendações de custos do Assistente](advisor-cost-recommendations.md)
* [Recomendações de desempenho do conselheiro](advisor-performance-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do consultor](advisor-operational-excellence-recommendations.md)