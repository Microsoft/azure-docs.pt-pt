---
title: Configuração de DNS do Ponto Final Privado do Azure
description: Aprenda a configuração de DNS de Endpoint Privado Azure
services: private-link
author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: allensu
ms.openlocfilehash: 3cb5dd88572fda7a939aa529a5372d72575ffdbb
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203288"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Configuração de DNS do Ponto Final Privado do Azure

É importante configurar corretamente as definições de DNS para resolver o endereço IP do ponto final privado para o nome de domínio totalmente qualificado (FQDN) da cadeia de ligação.

Os serviços existentes da Microsoft Azure podem já ter uma configuração DNS para um ponto final público. Esta configuração deve ser ultrapassada para se ligar utilizando o seu ponto final privado. 
 
A interface de rede associada ao ponto final privado contém as informações para configurar o seu DNS. As informações de interface de rede incluem FQDN e endereços IP privados para o seu recurso de ligação privada. 
 
Pode utilizar as seguintes opções para configurar as definições de DNS para pontos finais privados: 
- **Utilize o ficheiro hospedeiro (apenas recomendado para testes)**. Pode utilizar o ficheiro anfitrião numa máquina virtual para anular o DNS.  
- **Utilize uma zona privada de DNS**. Pode utilizar [zonas de DNS privadas](../dns/private-dns-privatednszone.md) para anular a resolução dns para um ponto final privado. Uma zona privada de DNS pode ser ligada à sua rede virtual para resolver domínios específicos.
- **Utilize o seu remetente DNS (opcional)**. Pode utilizar o seu reencaminhador DNS para anular a resolução DNS para um recurso de ligação privada. Crie uma regra de encaminhamento DE DNS para utilizar uma zona de DNS privada no seu [servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) hospedado numa rede virtual.

> [!IMPORTANT]
> Não é recomendado para sobrepor uma zona que está ativamente em uso para resolver pontos finais públicos. As ligações aos recursos não serão capazes de resolver corretamente sem que o DNS reencaminhasse para o DNS público. Para evitar problemas, crie um nome de domínio diferente ou siga o nome sugerido para cada serviço abaixo. 

## <a name="azure-services-dns-zone-configuration"></a>Configuração da zona de DNS dos serviços Azure
Azure cria um registo DNS de nome canónico (CNAME) no DNS público. O registo CNAME redireciona a resolução para o nome de domínio privado. Pode anular a resolução com o endereço IP privado dos seus pontos finais privados. 
 
As suas aplicações não precisam de alterar o URL de ligação. Ao resolver um serviço PÚBLICO DNS, o servidor DNS irá resolver-se para os seus pontos finais privados. O processo não afeta as aplicações existentes. 

> [!IMPORTANT]
> As redes privadas que já utilizam a zona privada de DNS para um determinado tipo, só podem ligar-se a recursos públicos se não tiverem quaisquer ligações privadas de ponto final, caso contrário é necessária uma configuração de DNS correspondente na zona privada do DNS para completar a sequência de resolução de DNS. 

Para os serviços Azure, utilize os nomes de zona recomendados como descritos no quadro seguinte:

| Tipo de recurso de ligação privada / Subresource |Nome da zona privada do DNS | Transeuntes da zona pública de DNS |
|---|---|---|
| Azure Automation / (Microsoft.Automation/automationAccounts) / Webhook, DSCAndHybridWorker | privatelink.azure-automation.net | azure-automation.net |
| Base de Dados Azure SQL (Microsoft.Sql/servidores) / SqlServer | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (Microsoft.Sql/servers) / sqlServer  | privatelink.database.windows.net | database.windows.net |
| Conta de armazenamento (Microsoft.Storage/storageAccounts) / Blob (blob, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Conta de armazenamento (Microsoft.Storage/storageAccounts) / Tabela (tabela, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Conta de armazenamento (Microsoft.Storage/storageAccounts) / Fila (fila, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Conta de armazenamento (Microsoft.Storage/storageAccounts) / Arquivo (ficheiro, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Conta de armazenamento (Microsoft.Storage/storageAccounts) / Web (web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Azure Data Lake File System Gen2 (Microsoft.Storage/storageAccounts) / Data Lake File System Gen2 (dfs, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Tabela | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Base de Dados Azure para PostgreSQL - Servidor Único (Microsoft.DBforPostgreSQL/servidores) / postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Base de Dados Azure para MySQL (Microsoft.DBforMySQL/servidores) / mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Base de Dados Azure para MariaDB (Microsoft.DBforMariaDB/servidores) / mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Cofre de chaves Azure (Microsoft.KeyVault/vaults) / cofre | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Serviço Azure Kubernetes - Kubernetes API (Microsoft.ContainerService/managedClusters) / gestão | privatelink. {região}.azmk8s.io | {região}.azmk8s.io |
| Pesquisa de Azure (Microsoft.Search/searchServices) / searchService | privatelink.search.windows.net | search.windows.net |
| Registo de Contentores Azure (Microsoft.ContainerRegistry/registries) / registo | privatelink.azurecr.io | azurecr.io |
| Configuração de aplicativos Azure (Microsoft.AppConfiguration/configurationStores) / configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure Backup (Microsoft.RecoveryServices/vaults) / cofre | privatelink. {region}.backup.windowsazure.com | {region}.backup.windowsazure.com |
| Recuperação do site Azure (Microsoft.RecoveryServices/cofres) / cofre | {region}.privatelink.siterecovery.windowsazure.com | {região}.hypervrecoverymanager.windowsazure.com |
| Azure Event Hubs (Microsoft.EventHub/namespaces) / namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus (Microsoft.ServiceBus/namespaces) / namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Hub Azure IoT (Microsoft.Devices/IotHubs) / iotHubs | privatelink.azure-devices.net<br/>privatelink.servicebus.windows.net<sup>1</sup> | azure-devices.net<br/>servicebus.windows.net |
| Relé Azure (Microsoft.Relay/namespaces) / espaço de nome | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid (Microsoft.EventGrid/topics) / tópico | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Grelha de eventos Azure (Microsoft.EventGrid/domínios) / domínio | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Web Apps (Microsoft.Web/sites) / sites | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft.MachineLearningServices/workspaces) / amlworkspace | privatelink.api.azureml.ms<br/>privatelink.notebooks.azure.net | api.azureml.ms<br/>notebooks.azure.net<br/>instances.azureml.ms<br/>aznbcontent.net |
| SignalR (Microsoft.SignalRService/SignalR) / signalR | privatelink.service.signalr.net | service.signalr.net |
| Monitor Azure (Microsoft.Insights/privateLinkScopes) / azuremonitor | privatelink.monitor.azure.com<br/> privatelink.oms.opinsights.azure.com <br/> privatelink.ods.opinsights.azure.com <br/> privatelink.agentsvc.azure-automation.net | monitor.azure.com<br/> oms.opinsights.azure.com<br/> ods.opinsights.azure.com<br/> agentsvc.azure-automation.net |
| Serviços Cognitivos (Microsoft.CognitiveServices/contas) / conta | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |
| Azure File Sync (Microsoft.StorageSync/storageSyncServices) / afs |  privatelink.afs.azure.net  |  afs.azure.net  |
| Azure Data Factory (Microsoft.DataFactory/fábricas) / dataFactory |  privatelink.datafactory.azure.net  |  datafactory.azure.net  |
| Azure Data Factory (Microsoft.DataFactory/fábricas) / portal |  privatelink.azure.com  |  azure.com  |
| Cache Azure para Redis (Microsoft.Cache/Redis) / redisCache | privatelink.redis.cache.windows.net | redis.cache.windows.net |

<sup>1</sup> Para utilizar com o ponto de final compatível com o Centro de Eventos IoT Hub. Para saber mais, consulte [o suporte de link privado para o ponto final incorporado do IoT Hub](../iot-hub/virtual-network-support.md#built-in-event-hub-compatible-endpoint)

### <a name="china"></a>China

| Tipo de recurso de ligação privada / Subresource |Nome da zona privada do DNS | Transeuntes da zona pública de DNS |
|---|---|---|
| Base de Dados Azure SQL (Microsoft.Sql/servidores) / SQL Server | privatelink.database.chinacloudapi.cn | database.chinacloudapi.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / SQL | privatelink.documents.azure.cn | documents.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / MongoDB | privatelink.mongo.cosmos.azure.cn | mongo.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Cassandra | privatelink.cassandra.cosmos.azure.cn | cassandra.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Gremlin | privatelink.gremlin.cosmos.azure.cn | gremlin.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Tabela | privatelink.table.cosmos.azure.cn | table.cosmos.azure.cn |
| Base de Dados Azure para PostgreSQL - Servidor Único (Microsoft.DBforPostgreSQL/servidores) / postgresqlServer | privatelink.postgres.database.chinacloudapi.cn | postgres.database.chinacloudapi.cn |
| Base de Dados Azure para MySQL (Microsoft.DBforMySQL/servidores) / mysqlServer | privatelink.mysql.database.chinacloudapi.cn  | mysql.database.chinacloudapi.cn  |
| Base de Dados Azure para MariaDB (Microsoft.DBforMariaDB/servidores) / mariadbServer | privatelink.mariadb.database.chinacloudapi.cn | mariadb.database.chinacloudapi.cn |


## <a name="dns-configuration-scenarios"></a>Cenários de configuração do DNS

O FQDN dos serviços resolve-se automaticamente para um endereço IP público. Para resolver o endereço IP privado do ponto final privado, altere a sua configuração DNS.

O DNS é um componente crítico para que a aplicação funcione corretamente, resolvendo com sucesso o endereço IP do ponto final privado.

Com base nas suas preferências, os seguintes cenários estão disponíveis com resolução DNS integrada:

- [Cargas de trabalho de rede virtuais sem servidor DNS personalizado](#virtual-network-workloads-without-custom-dns-server)
- [Cargas de trabalho no local utilizando um remetente DNS](#on-premises-workloads-using-a-dns-forwarder)
- [Cargas de trabalho de rede virtual e no local utilizando um reencaminhador de DNS](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)

> [!NOTE]
> [O proxy Azure Firewall DNS](../firewall/dns-settings.md#dns-proxy) pode ser usado como reencaminhador de DNS para [cargas de trabalho no local](#on-premises-workloads-using-a-dns-forwarder) e [cargas de trabalho de rede virtual usando um reencaminhador DNS](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder).

## <a name="virtual-network-workloads-without-custom-dns-server"></a>Cargas de trabalho de rede virtuais sem servidor DNS personalizado

Esta configuração é adequada para cargas de trabalho de rede virtuais sem um servidor DNS personalizado. Neste cenário, o cliente questiona o endereço IP do ponto final privado para o serviço DNS fornecido pela Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). O DNS Azure será responsável pela resolução do DNS das zonas privadas de DNS.

> [!NOTE]
> Este cenário utiliza a zona privada de DNS recomendada pela Base de Dados Azure SQL. Para outros serviços, pode ajustar o modelo utilizando a seguinte referência: Configuração da [zona DNS dos serviços Azure](#azure-services-dns-zone-configuration).

Para configurar corretamente, precisa dos seguintes recursos:

- Rede virtual do cliente

- Zona privada de DNS [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)  com [registo tipo A](../dns/dns-zones-records.md#record-types)

- Informações privadas sobre o ponto final (nome de registo FQDN e endereço IP privado)

A imagem a seguir ilustra a sequência de resolução de DNS a partir de cargas de trabalho de rede virtuais utilizando a zona privada de DNS:

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Rede virtual única e DNS fornecidos pelo Azure":::

Pode estender este modelo a redes virtuais espreitadas associadas ao mesmo ponto final privado. [Adicione novas ligações de rede virtual](../dns/private-dns-virtual-network-links.md) à zona privada de DNS para todas as redes virtuais.

> [!IMPORTANT]
> Para esta configuração é necessária uma única zona privada de DNS. Criar várias zonas com o mesmo nome para diferentes redes virtuais precisaria de operações manuais para fundir os registos DNS.

> [!IMPORTANT]
> Se estiver a utilizar um ponto final privado num modelo de hub e spoke a partir de uma subscrição diferente, reutilizámos a mesma zona privada de DNS no centro.

Neste cenário, há um [centro e uma](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) topologia em rede. As redes de fala partilham um ponto final privado. As redes virtuais faladas estão ligadas à mesma zona privada de DNS. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Hub e falou com o DNS fornecido pelo Azure":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Cargas de trabalho no local utilizando um remetente DNS

Para que as cargas de trabalho no local resolvam o FQDN de um ponto final privado, utilize um remetente DNS para resolver a [zona pública de DNS](#azure-services-dns-zone-configuration) do serviço Azure em Azure.

O cenário a seguir é para uma rede no local que tenha um remetente DNS em Azure. Este reencaminhador resolve consultas de DNS através de um encaminhador de nível de servidor para o Azure fornecido DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). 

> [!NOTE]
> Este cenário utiliza a zona privada de DNS recomendada pela Base de Dados Azure SQL. Para outros serviços, pode ajustar o modelo utilizando a seguinte referência: Configuração da [zona DNS dos serviços Azure](#azure-services-dns-zone-configuration).

Para configurar corretamente, precisa dos seguintes recursos:

- Rede no local
- Rede virtual [ligada às instalações](/azure/architecture/reference-architectures/hybrid-networking/)
- Encaminhador de DNS implantado em Azure 
- Zonas privadas de DNS [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) com [registo tipo A](../dns/dns-zones-records.md#record-types)
- Informações privadas sobre o ponto final (nome de registo FQDN e endereço IP privado)

O diagrama seguinte ilustra a sequência de resolução de DNS a partir de uma rede no local. A configuração utiliza um reencaminhador DNS implantado em Azure. A resolução é feita por uma zona privada de DNS [ligada a uma rede virtual:](../dns/private-dns-virtual-network-links.md)

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="No local usando DNS Azure":::

Esta configuração pode ser estendida para uma rede no local que já tenha uma solução DNS no lugar. A solução DNS no local está configurada para encaminhar o tráfego DNS para o Azure DNS através de um [reencaminhador condicional](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). O reencaminhador condicional refere o reencaminhador DNS implantado em Azure.

> [!NOTE]
> Este cenário utiliza a zona privada de DNS recomendada pela Base de Dados Azure SQL. Para outros serviços, pode ajustar o modelo utilizando a seguinte referência: [Configuração da zona DNS dos serviços Azure](#azure-services-dns-zone-configuration)

Para configurar corretamente, precisa dos seguintes recursos:

- Rede de instalações com uma solução de DNS personalizada no lugar 
- Rede virtual [ligada às instalações](/azure/architecture/reference-architectures/hybrid-networking/)
- Encaminhador de DNS implantado em Azure
- Zonas privadas de DNS [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)  com [registo tipo A](../dns/dns-zones-records.md#record-types)
- Informações privadas sobre o ponto final (nome de registo FQDN e endereço IP privado)

O diagrama seguinte ilustra a resolução do DNS a partir de uma rede no local. A resolução do DNS é transmitida condicionalmente ao Azure. A resolução é feita por uma zona privada de DNS [ligada a uma rede virtual.](../dns/private-dns-virtual-network-links.md)

> [!IMPORTANT]
> O reencaminhamento condicional deve ser feito para o [reencaminhador público](#azure-services-dns-zone-configuration)recomendado para a zona de DNS . Por exemplo: `database.windows.net` em vez de **privatelink**.database.windows.net.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="No local reencaminhando para Azure DNS":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>Cargas de trabalho de rede virtual e no local utilizando um reencaminhador de DNS

Para cargas de trabalho que acedam a um ponto final privado a partir de redes virtuais e no local, utilize um remetente DNS para resolver a [zona pública de DNS](#azure-services-dns-zone-configuration) do serviço Azure implantada em Azure.

O cenário a seguir é para uma rede no local com redes virtuais em Azure. Ambas as redes acedem ao ponto final privado localizado numa rede de hubs partilhados.

Este remetente DNS é responsável pela resolução de todas as consultas de DNS através de um reencaminhador de nível de servidor para o serviço DNS fornecido pelo Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md).

> [!IMPORTANT]
> Para esta configuração é necessária uma única zona privada de DNS. Todas as ligações com o cliente feitas a partir de instalações e [redes virtuais com vista](../virtual-network/virtual-network-peering-overview.md) também devem utilizar a mesma zona privada de DNS.

> [!NOTE]
> Este cenário utiliza a zona privada de DNS recomendada pela Base de Dados Azure SQL. Para outros serviços, pode ajustar o modelo utilizando a seguinte referência: Configuração da [zona DNS dos serviços Azure](#azure-services-dns-zone-configuration).

Para configurar corretamente, precisa dos seguintes recursos:

- Rede no local
- Rede virtual [ligada às instalações](/azure/architecture/reference-architectures/hybrid-networking/)
- [Rede virtual esprevada](../virtual-network/virtual-network-peering-overview.md) 
- Encaminhador de DNS implantado em Azure
- Zonas privadas de DNS [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)  com [registo tipo A](../dns/dns-zones-records.md#record-types)
- Informações privadas sobre o ponto final (nome de registo FQDN e endereço IP privado)

O diagrama seguinte mostra a resolução do DNS para ambas as redes, no local e redes virtuais. A resolução está a usar um remetente DNS. A resolução é feita por uma zona privada de DNS [ligada a uma rede virtual:](../dns/private-dns-virtual-network-links.md)

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="Cenário híbrido":::

## <a name="next-steps"></a>Passos seguintes
- [Conheça os pontos finais privados](private-endpoint-overview.md)
