---
title: Melhorar a disponibilidade da sua aplicação com o Assistente do Azure | Documentos da Microsoft
description: Utilize o Assistente do Azure para melhorar a elevada disponibilidade das suas implementações do Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: bdba3f135f852312af1692f77643095d865f1d06
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254672"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Melhorar a disponibilidade da sua aplicação com o Assistente do Azure

O Assistente do Azure ajuda-Certifique-se e melhorar a continuidade das suas aplicações críticas para a empresa. É possível obter recomendações de alta disponibilidade por Advisor a partir da **elevada disponibilidade** separador do dashboard do Advisor.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Certifique-se a tolerância a falhas de máquina virtual

Para fornecer redundância à aplicação, é recomendável agrupar duas ou mais máquinas virtuais num conjunto de disponibilidade. Advisor identifica as máquinas virtuais que não fazem parte de um conjunto de disponibilidade e recomenda movendo-as para um conjunto de disponibilidade. Esta configuração garante que, durante a um evento de manutenção planeada ou, pelo menos uma máquina virtual está disponível e cumpre o SLA de máquinas virtuais do Azure. Pode escolher para criar o conjunto de disponibilidade para a máquina virtual ou para adicionar a máquina virtual a um conjunto de disponibilidade existente.

> [!NOTE]
> Se optar por criar um conjunto de disponibilidade, tem de adicionar mais, pelo menos, uma máquina virtual na mesma. Recomendamos que agrupa dois ou mais máquinas virtuais num disponibilidade definida para se certificar de que a máquina, pelo menos, um está disponível durante um período de indisponibilidade.

## <a name="ensure-availability-set-fault-tolerance"></a>Certifique-se de que a tolerância a falhas de conjunto de disponibilidade

Para fornecer redundância à aplicação, é recomendável agrupar duas ou mais máquinas virtuais num conjunto de disponibilidade. O assistente identifica os conjuntos de disponibilidade que contenham uma única máquina virtual e recomenda adicionar uma ou mais máquinas virtuais ao mesmo. Esta configuração garante que, durante a um evento de manutenção planeada ou, pelo menos uma máquina virtual está disponível e cumpre o SLA de máquinas virtuais do Azure. Pode escolher para criar uma máquina virtual ou para adicionar uma máquina virtual existente para o conjunto de disponibilidade.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Utilizar Managed Disks para melhorar a fiabilidade dos dados

Máquinas virtuais que estão num conjunto de disponibilidade com discos que partilham contas de armazenamento ou unidades de escala de armazenamento não são resilientes a falhas de unidade de escala de armazenamento única durante as falhas. O assistente irá identificar esses conjuntos de disponibilidade e recomendamos migrar para Managed Disks do Azure. Isto irá garantir que os discos das máquinas virtuais diferentes no conjunto de disponibilidade estão suficientemente isolados para evitar um ponto único de falha. 

## <a name="ensure-application-gateway-fault-tolerance"></a>Certifique-se a tolerância a falhas de gateway de aplicação

Esta recomendação garante a continuidade de negócio de aplicativos de missão crítica que têm a tecnologia gateways de aplicação. Advisor identifica instâncias de gateway de aplicação que não estão configuradas para tolerância a falhas e sugere ações de remediação que pode efetuar. Advisor identifica os gateways de aplicação de instância única de médias ou grandes, e ele recomenda adicionar pelo menos uma instância mais. Ele também identifica instance único ou vários gateways de aplicação pequenos e recomenda a migração para médias ou grandes SKUs. Advisor recomenda estas ações para garantir que as instâncias de gateway de aplicação estão configuradas para satisfazer os requisitos de SLA atuais para estes recursos.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Proteger os dados de máquina virtual de eliminação acidental

Configurar a cópia de segurança da máquina virtual garante a disponibilidade dos seus dados críticos e oferece proteção contra eliminação ou corrupção acidental. Advisor identifica as máquinas virtuais em que a cópia de segurança não está ativada, e ele recomenda a ativação de cópia de segurança. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Certifique-se de que tem acesso a especialistas de cloud do Azure, sempre que precisar

Ao executar uma carga de trabalho críticas para a empresa, é importante ter acesso ao suporte técnico quando necessário. Advisor identifica potenciais subscrições empresariais críticas que não têm suporte técnico incluído no seu plano de suporte e recomenda a atualização para uma opção que inclui suporte técnico.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Criar alertas do Azure Service Health para ser notificado quando é afetado por problemas do Azure

Recomendamos a configuração de alertas do Azure Service Health para ser notificado quando é afetado por problemas de serviço do Azure. [O Azure Service Health](https://azure.microsoft.com/features/service-health/) é um serviço gratuito que fornece orientação e suporte personalizados quando são afetados por um problema de serviço do Azure. O assistente identifica as subscrições que não tem alertas configurados e recomenda a criação de um.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Configurar pontos finais do Gestor de tráfego para resiliência

Perfis do Gestor de tráfego com mais de um ponto final experiência maior disponibilidade, se falhar de qualquer ponto de extremidade. Colocar os pontos finais em diferentes regiões ainda mais melhora a fiabilidade do serviço. Advisor identifica os perfis de tráfego Manager onde há apenas um ponto final e recomenda adicionar pelo menos um ponto de extremidade mais noutra região.

Se todos os pontos finais num perfil do Gestor de tráfego que está configurado para o encaminhamento de proximidade estiverem na mesma região, os utilizadores a partir de outras regiões podem ocorrem atrasos de ligação. Adicionar ou mover um ponto de extremidade para outra região irá melhorar o desempenho geral e proporcionar melhor disponibilidade se falharem de todos os pontos finais numa única região. Advisor identifica perfis do Gestor de tráfego configurados para o encaminhamento de proximidade onde todos os pontos finais estão na mesma região. Ele recomenda-se ao adicionar ou mover um ponto de extremidade para outra região do Azure.

Se um perfil do Gestor de tráfego estiver configurado para encaminhamento geográfico, em seguida, o tráfego é encaminhado para pontos finais com base em regiões definidas. Se uma região falhar, não há nenhum predefinida de ativação pós-falha. Ter um ponto de extremidade em que o agrupamento Regional está configurado para "Todos (World)", evitar o tráfego a ser removido e melhorar a disponibilidade do serviço. Advisor identifica perfis do Gestor de tráfego configurados para encaminhamento geográfico onde não existe nenhum ponto final configurado para ter o agrupamento Regional como "Todos (World)". Ele recomenda a alteração da configuração para tornar um ponto final "todos (mundo).

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Utilizar a eliminação de forma recuperável na sua conta de armazenamento do Azure para guardar e recuperar dados após uma substituição acidental ou eliminação

Ativar [eliminação de forma recuperável](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) na sua conta de armazenamento, de modo a que eliminou a transição de blobs para um Estado de eliminado de forma recuperável em vez de ser permanentemente eliminado. Quando dados são substituídos, é gerado um instantâneo eliminado de forma recuperável para guardar o estado dos dados substituídos. Utilizar a eliminação de forma recuperável permite-lhe recuperar se existem eliminações acidentais ou substitui. O assistente identifica as contas de armazenamento do Azure que não tem a eliminação de forma recuperável ativada e sugere que o ative.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Configurar o gateway de VPN ativos-ativos para resiliência de ligação

Configuração de ativo-ativo, ambas as instâncias de um gateway VPN irão estabelecer túneis S2S VPN para o dispositivo VPN no local. Quando um evento de manutenção planeada ou não planeado eventos acontece à instância de um gateway, o tráfego irá ser mudado ao longo para o outro túnel IPsec ativo automaticamente. O Assistente do Azure irá identificar os gateways de VPN que não estejam configurados como ativo-ativo e sugerir configurá-las para elevada disponibilidade.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Utilizar gateways de VPN de produção para executar as cargas de trabalho de produção

O Assistente do Azure irá verificar a existência de quaisquer gateways VPN que são um SKU básico e recomendamos que utilize um SKU de produção. O SKU básico foi concebido para fins de testes e de desenvolvimento. SKUs de produção oferecem um número mais elevado de túneis, suporte BGP, opções de configuração de ativo-ativo, personalizados a política de Ipsec/IKE e maior estabilidade e disponibilidade.

## <a name="repair-invalid-log-alert-rules"></a>Reparar regras de alerta de registo inválido

O Assistente do Azure irá detetar as regras de alertas que tenham consultas inválidas especificadas na respetiva secção de condição. Regras de alerta de registo são criadas no Azure Monitor e são utilizadas para executar consultas de análise em intervalos especificados. Os resultados da consulta determinam se um alerta tem de ser acionado. Consultas de análise podem se tornar horas extraordinárias inválida devido a alterações em recursos referenciados, tabelas ou comandos. O assistente irá recomendar que corrija a consulta na regra de alerta para impedi-lo de obtenção de auto-desativado e certifique-se de cobertura de monitorização dos seus recursos no Azure. [Saiba mais sobre regras de alerta de resolução de problemas](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Configurar o modo de indexação consistente em sua coleção do Cosmos DB

Contentores do Azure Cosmos DB configurados com o modo de indexação lento podem afetar a atualização dos resultados da consulta. O assistente irá detetar contentores configurado dessa forma e recomendamos mudar para o modo consistente. [Saiba mais sobre políticas no Cosmos DB de indexação](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Configurar os seus contentores do Azure Cosmos DB com uma chave de partição

O Assistente do Azure identificará o Azure Cosmos DB não-particionada coleções que estão prestes a atingir seus quota de armazenamento aprovisionado. Recomenda migrar essas coleções para novas coleções com uma definição de chave de partição para que eles automaticamente podem ser aumentados horizontalmente pelo serviço. [Saiba mais sobre como escolher uma chave de partição](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Atualizar o SDK de .NET do Azure Cosmos DB para a versão mais recente do Nuget

O Assistente do Azure identificará as contas do Azure Cosmos DB que estejam usando versões antigas do SDK do .NET e recomendável fazer a atualização para a versão mais recente do Nuget para os mais recentes correções, melhorias de desempenho e novas capacidades de funcionalidade. [Saiba mais sobre o SDK de .NET do Cosmos DB](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Atualizar o SDK de Java do Azure Cosmos DB para a versão mais recente a partir do Maven

O Assistente do Azure identificará as contas do Azure Cosmos DB que estiver a utilizar as versões antigas do SDK do Java e recomendável fazer a atualização para a versão mais recente do Maven para as mais recentes correções, melhorias de desempenho e novas capacidades de funcionalidade. [Saiba mais sobre o SDK de Java do Cosmos DB](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Atualizar o seu conector do Spark do Azure Cosmos DB para a versão mais recente a partir do Maven

O Assistente do Azure identificará as contas do Azure Cosmos DB que estejam usando versões antigas do conector do Spark do Cosmos DB e recomendável fazer a atualização para a versão mais recente do Maven para as mais recentes correções, melhorias de desempenho e novas capacidades de funcionalidade. [Saiba mais sobre o conector do Spark do Cosmos DB](https://aka.ms/cosmosdb/spark-connector)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Como acessar as recomendações de elevada disponibilidade no Advisor

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, abra [Advisor](https://aka.ms/azureadvisordashboard).

2.  No dashboard do assistente, clique nas **elevada disponibilidade** separador.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre as recomendações do assistente, consulte:
* [Introdução ao Assistente do Azure](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de custos do Assistente](advisor-cost-recommendations.md)
* [Recomendações de desempenho do Assistente](advisor-performance-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)

