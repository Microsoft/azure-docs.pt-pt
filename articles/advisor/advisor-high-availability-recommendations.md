---
title: Melhorar a disponibilidade do seu aplicativo com o Azure Advisor
description: Use o Azure Advisor para melhorar a alta disponibilidade de suas implantações do Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 997681ed62fa9985e3122ece22565dbae0e65b53
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443113"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Melhorar a disponibilidade do seu aplicativo com o Azure Advisor

O assistente do Azure ajuda a garantir e melhorar a continuidade dos seus aplicativos críticos para os negócios. Você pode obter recomendações de alta disponibilidade pelo Advisor na guia **alta disponibilidade** do painel do Advisor.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Garantir tolerância a falhas da máquina virtual

Para fornecer redundância à aplicação, é recomendável agrupar duas ou mais máquinas virtuais num conjunto de disponibilidade. O Advisor identifica as máquinas virtuais que não fazem parte de um conjunto de disponibilidade e recomenda movê-las para um conjunto de disponibilidade. Essa configuração garante que, durante um evento de manutenção planejada ou não planejada, pelo menos uma máquina virtual esteja disponível e atenda ao SLA da máquina virtual do Azure. Você pode optar por criar um conjunto de disponibilidade para a máquina virtual ou adicionar a máquina virtual a um conjunto de disponibilidade existente.

> [!NOTE]
> Se você optar por criar um conjunto de disponibilidade, deverá adicionar pelo menos mais uma máquina virtual a ele. Recomendamos que você agrupe duas ou mais máquinas virtuais em um conjunto de disponibilidade para garantir que pelo menos uma máquina esteja disponível durante uma interrupção.

## <a name="ensure-availability-set-fault-tolerance"></a>Garantir tolerância a falhas de conjunto de disponibilidade

Para fornecer redundância à aplicação, é recomendável agrupar duas ou mais máquinas virtuais num conjunto de disponibilidade. O Advisor identifica os conjuntos de disponibilidade que contêm uma única máquina virtual e recomenda adicionar uma ou mais máquinas virtuais a ele. Essa configuração garante que, durante um evento de manutenção planejada ou não planejada, pelo menos uma máquina virtual esteja disponível e atenda ao SLA da máquina virtual do Azure. Você pode optar por criar uma máquina virtual ou adicionar uma máquina virtual existente ao conjunto de disponibilidade.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Utilizar o Managed Disks para melhorar a fiabilidade dos dados

As máquinas virtuais que estão em um conjunto de disponibilidade com discos que compartilham contas de armazenamento ou unidades de escala de armazenamento não são resilientes a falhas de unidade de escala de armazenamento único durante interrupções. O Advisor identificará esses conjuntos de disponibilidade e recomendará migrar para o Azure Managed Disks. Isso garantirá que os discos das diferentes máquinas virtuais no conjunto de disponibilidade sejam suficientemente isolados para evitar um ponto único de falha. 

## <a name="ensure-application-gateway-fault-tolerance"></a>Garantir tolerância a falhas do gateway de aplicativo

Essa recomendação garante a continuidade dos negócios de aplicativos de missão crítica que são alimentados por gateways de aplicativo. O Advisor identifica as instâncias de gateway de aplicativo que não estão configuradas para tolerância a falhas e sugere ações de correção que podem ser tomadas. O Advisor identifica gateways de aplicativo de instância única médio ou grande e recomenda adicionar pelo menos mais uma instância. Ele também identifica gateways de aplicativo simples ou com várias instâncias e recomenda a migração para SKUs médios ou grandes. O Advisor recomenda essas ações para garantir que as instâncias do gateway de aplicativo sejam configuradas para atender aos requisitos atuais de SLA para esses recursos.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Proteger seus dados de máquina virtual contra exclusão acidental

Configurar o backup da máquina virtual garante a disponibilidade de seus dados críticos para os negócios e oferece proteção contra exclusão acidental ou corrupção. O Advisor identifica as máquinas virtuais em que o backup não está habilitado e recomenda habilitar o backup. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Verifique se você tem acesso aos especialistas de nuvem do Azure quando precisar

Ao executar uma carga de trabalho comercialmente crítica, é importante ter acesso ao suporte técnico quando necessário. O Advisor identifica as possíveis assinaturas críticas para os negócios que não têm suporte técnico incluído em seu plano de suporte e recomenda a atualização para uma opção que inclui suporte técnico.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Crie alertas de integridade do serviço do Azure para ser notificado quando os problemas do Azure afetarem você

É recomendável configurar alertas de integridade do serviço do Azure para ser notificado quando os problemas de serviço do Azure afetarem você. A [integridade do serviço do Azure](https://azure.microsoft.com/features/service-health/) é um serviço gratuito que fornece orientação e suporte personalizados quando você é afetado por um problema de serviço do Azure. O Advisor identifica as assinaturas que não têm alertas configurados e recomenda a criação de um.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Configurar pontos de extremidade do Gerenciador de tráfego para resiliência

Os perfis do Gerenciador de tráfego com mais de um ponto de extremidade terão maior disponibilidade se qualquer ponto de extremidade especificado falhar. Colocar pontos de extremidade em diferentes regiões melhora ainda mais a confiabilidade do serviço. O Advisor identifica os perfis do Gerenciador de tráfego em que há apenas um ponto de extremidade e recomenda adicionar pelo menos mais um ponto de extremidade a outra região.

Se todos os pontos de extremidade em um perfil do Gerenciador de tráfego configurado para roteamento de proximidade estiverem na mesma região, os usuários de outras regiões poderão enfrentar atrasos de conexão. Adicionar ou mover um ponto de extremidade para outra região melhorará o desempenho geral e fornecerá melhor disponibilidade se todos os pontos de extremidade em uma região falharem. O Advisor identifica os perfis do Gerenciador de tráfego configurados para o roteamento de proximidade onde todos os pontos de extremidade estão na mesma região. Ele recomenda adicionar ou mover um ponto de extremidade para outra região do Azure.

Se um perfil do Gerenciador de tráfego estiver configurado para roteamento geográfico, o tráfego será roteado para pontos de extremidade com base em regiões definidas. Se uma região falhar, não haverá um failover predefinido. Ter um ponto de extremidade onde o agrupamento regional está configurado como "todos (mundo)" evitará que o tráfego seja eliminado e melhore a disponibilidade do serviço. O Advisor identifica os perfis do Gerenciador de tráfego configurados para roteamento geográfico, onde não há nenhum ponto de extremidade configurado para ter o agrupamento regional como "todos (mundo)". Recomenda alterar a configuração para tornar um ponto de extremidade "todos (mundo).

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Use a exclusão reversível em sua conta de armazenamento do Azure para salvar e recuperar dados após substituição ou exclusão acidental

Habilite a [exclusão reversível](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) em sua conta de armazenamento para que os BLOBs excluídos façam a transição para um estado de exclusão reversível em vez de serem excluídos permanentemente. Quando os dados são substituídos, é gerado um instantâneo de eliminação de forma recuperável para guardar o estado dos dados substituídos. Usar a exclusão reversível permite que você recupere se há exclusões acidentais ou substituições. O Advisor identifica as contas de armazenamento do Azure que não têm a exclusão reversível habilitada e sugere que você a habilite.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Configurar o gateway de VPN para ativo-ativo para resiliência de conexão

Na configuração ativo-ativo, ambas as instâncias de um gateway de VPN estabelecerão túneis VPN S2S para seu dispositivo VPN local. Quando um evento de manutenção planejada ou um evento não planejado ocorrer em uma instância de gateway, o tráfego será alternado para o outro túnel IPsec ativo automaticamente. O Azure Advisor identificará os gateways de VPN que não estão configurados como ativo-ativo e sugere que você os configure para alta disponibilidade.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Usar gateways de VPN de produção para executar suas cargas de trabalho de produção

O Azure Advisor verificará se há gateways de VPN que são uma SKU básica e recomendamos que você use uma SKU de produção. O SKU básico foi projetado para fins de desenvolvimento e teste. As SKUs de produção oferecem um número maior de túneis, suporte a BGP, opções de configuração ativo-ativo, política de IPSec/IKE personalizada e maior estabilidade e disponibilidade.

## <a name="repair-invalid-log-alert-rules"></a>Reparar regras de alerta de log inválidas

O Azure Advisor detectará regras de alerta com consultas inválidas especificadas na seção de condição. As regras de alerta de registo são criadas no Azure Monitor e são utilizadas para executar consultas de análise em intervalos especificados. Os resultados da consulta determinam se um alerta tem de ser acionado. As consultas de análise podem tornar-se inválidas ao longo do tempo devido a alterações em recursos, tabelas ou comandos referenciados. O Advisor recomendará que você corrija a consulta na regra de alerta para impedir que ela seja desabilitada automaticamente e garanta a cobertura de monitoramento de seus recursos no Azure. [Saiba mais sobre como solucionar problemas de regras de alerta](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Configurar o modo de indexação consistente na sua coleção de Cosmos DB

Azure Cosmos DB contêineres configurados com o modo de indexação lento podem afetar a atualização dos resultados da consulta. O Advisor detectará contêineres configurados dessa maneira e recomendará alternar para o modo consistente. [Saiba mais sobre políticas de indexação no Cosmos DB](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Configurar os contentores do Azure Cosmos DB com uma chave de partição

O Azure Advisor identificará Azure Cosmos DB coleções não particionadas que estão se aproximando da cota de armazenamento provisionada. Ele recomendará migrar essas coleções para novas coleções com uma definição de chave de partição para que elas possam ser automaticamente dimensionadas pelo serviço. [Saiba mais sobre como escolher uma chave de partição](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Atualizar o SDK .NET do Azure Cosmos DB para a versão mais recente do Nuget

O assistente do Azure identificará Azure Cosmos DB contas que estão usando versões antigas do SDK do .NET e recomendará atualizar para a versão mais recente do NuGet para as correções mais recentes, aprimoramentos de desempenho e novos recursos de recurso. [Saiba mais sobre o SDK do .NET Cosmos DB](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Atualizar o SDK Java do Azure Cosmos DB para a versão mais recente a partir do Maven

O assistente do Azure identificará Azure Cosmos DB contas que estão usando versões antigas do SDK do Java e recomendará atualizar para a versão mais recente do Maven para as correções mais recentes, melhorias de desempenho e novos recursos de recurso. [Saiba mais sobre o SDK do Java Cosmos DB](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Atualizar o Conector Spark do Azure Cosmos DB para a versão mais recente a partir do Maven

O assistente do Azure identificará Azure Cosmos DB contas que estão usando versões antigas do conector Cosmos DB Spark e recomendará atualizar para a versão mais recente do Maven para as correções mais recentes, melhorias de desempenho e novos recursos de recurso. [Saiba mais sobre o conector do Spark Cosmos DB](https://aka.ms/cosmosdb/spark-connector)

## <a name="enable-virtual-machine-replication"></a>Ativar a replicação da máquina virtual
As máquinas virtuais que não têm replicação habilitada para outra região não são resilientes a interrupções regionais. A replicação de máquinas virtuais reduz qualquer impacto adverso nos negócios durante o tempo de uma interrupção da região do Azure. O Advisor detectará VMs que não têm a replicação habilitada e recomenda habilitar a replicação para que, no caso de uma interrupção, você possa abrir rapidamente suas máquinas virtuais em uma região remota do Azure. [Saiba mais sobre a replicação da máquina virtual](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Como acessar as recomendações de alta disponibilidade no Advisor

1. Entre no [portal do Azure](https://portal.azure.com)e abra o [Advisor](https://aka.ms/azureadvisordashboard).

2.  No painel do Advisor, clique na guia **alta disponibilidade** .

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as recomendações do Advisor, consulte:
* [Introdução ao Azure Advisor](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do Advisor](advisor-operational-excellence-recommendations.md)
