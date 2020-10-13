---
title: Configuração de DNS do Ponto Final Privado do Azure
description: Aprenda a configuração de DNS de Endpoint Privado Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: 6e3d87d613db63e05ddee47d43aead779eca75c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91628014"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Configuração de DNS do Ponto Final Privado do Azure


Quando está a ligar-se a um recurso de ligação privada utilizando um nome de domínio totalmente qualificado (FQDN) como parte da cadeia de ligação, é importante configurar corretamente as definições de DNS para resolver o endereço IP privado atribuído. Os serviços existentes do Microsoft Azure podem já ter uma configuração DNS para utilizar quando se conectam sobre um ponto final público. Esta configuração precisa de ser ultrapassada para se ligar utilizando o seu ponto final privado. 
 
A interface de rede associada ao ponto final privado contém o conjunto completo de informações necessárias para configurar o seu DNS, incluindo endereços IP FQDN e privados atribuídos para um determinado recurso de ligação privada. 
 
Pode utilizar as seguintes opções para configurar as definições de DNS para pontos finais privados: 
- **Utilize o ficheiro hospedeiro (apenas recomendado para testes)**. Pode utilizar o ficheiro anfitrião numa máquina virtual para anular o DNS.  
- **Utilize uma zona privada de DNS**. Você pode usar [zonas de DNS privadas](../dns/private-dns-privatednszone.md) para anular a resolução DNS para um determinado ponto final privado. Uma zona privada de DNS pode ser ligada à sua rede virtual para resolver domínios específicos.
- **Utilize o seu remetente DNS (opcional)**. Pode utilizar o seu remetente DNS para anular a resolução DNS para um determinado recurso de ligação privada. Se o seu [servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) estiver hospedado numa rede virtual, pode criar uma regra de encaminhamento DENS para utilizar uma zona de DNS privada para simplificar a configuração de todos os recursos de ligação privada.
 
> [!IMPORTANT]
> Não é recomendado para sobrepor uma zona que está ativamente em uso para resolver pontos finais públicos. As ligações aos recursos não serão capazes de resolver corretamente sem que o DNS reencaminhasse para o DNS público. Para evitar problemas, crie um nome de domínio diferente ou siga o nome sugerido para cada serviço abaixo. 

## <a name="azure-services-dns-zone-configuration"></a>Configuração da zona de DNS dos serviços Azure
Os serviços Azure criarão um registo de DNS de nome canónico (CNAME) no serviço público de DNS para redirecionar a resolução para o nome de domínio privado sugerido. Pode anular a resolução com o endereço IP privado dos seus pontos finais privados. 
 
As suas aplicações não precisam de alterar o URL de ligação. Ao tentar resolver através de um serviço PÚBLICO DNS, o servidor DNS irá agora resolver-se para os seus pontos finais privados. O processo não afeta as aplicações existentes. 

> [!IMPORTANT]
> As redes privadas que já utilizam a zona privada de DNS para um determinado tipo, só podem ligar-se a recursos públicos se não tiverem quaisquer ligações privadas de ponto final, caso contrário é necessária uma configuração de DNS correspondente na zona privada do DNS para completar a sequência de resolução de DNS. 

Para os serviços Azure, utilize os nomes de zona recomendados como descritos no quadro seguinte:

| Tipo de recurso de ligação privada / Subresource |Nome da zona privada do DNS | Transeuntes da zona pública de DNS |
|---|---|---|---|
| Azure Automation / (Microsoft.Automation/automationAccounts) / Webhook, DSCAndHybridWorker | privatelink.azure-automation.net | azure-automation.net |
| Base de Dados Azure SQL (Microsoft.Sql/servidores) / SQL Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (Microsoft.Sql/servers) / SQL Server  | privatelink.database.windows.net | database.windows.net |
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
| Azure Event Hubs (Microsoft.EventHub/namespaces) / namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus (Microsoft.ServiceBus/namespaces) / namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Hub Azure IoT (Microsoft.Devices/IotHubs) / iotHubs | privatelink.azure-devices.net | azure-devices.net |
| Relé Azure (Microsoft.Relay/namespaces) / espaço de nome | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid (Microsoft.EventGrid/topics) / tópico | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Grelha de eventos Azure (Microsoft.EventGrid/domínios) / domínio | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Web Apps (Microsoft.Web/sites) / sites | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft.MachineLearningServices/workspaces) / workspace | privatelink.api.azureml.ms | api.azureml.ms |
| IoT Hub (Microsoft.Devices/IotHubs) / IotHub | privatelink.azure-devices.net | azure-devices.net |
| SignalR (Microsoft.SignalRService/SignalR) / signalR | privatelink.service.signalr.net | service.signalr.net |
| Monitor Azure (Microsoft.Insights/privateLinkScopes) / azuremonitor | privatelink.monitor.azure.com<br/> privatelink.oms.opinsights.azure.com <br/> privatelink.ods.opinsights.azure.com <br/> privatelink.agentsvc.azure-automation.net | monitor.azure.com<br/> oms.opinsights.azure.com<br/> ods.opinsights.azure.com<br/> agentsvc.azure-automation.net |
| Serviços Cognitivos (Microsoft.CognitiveServices/contas) / conta | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |
| Azure File Sync (Microsoft.StorageSync/storageSyncServices) / afs |  privatelink.afs.azure.net  |  afs.azure.net  |

 
## <a name="dns-configuration-scenarios"></a>Cenários de configuração do DNS

O FQDN dos serviços resolve-se automaticamente para um endereço IP público. Para resolver o endereço IP privado do ponto final privado, tem de alterar a sua configuração DNS em conformidade.

O DNS é um componente crítico para que a aplicação funcione corretamente, resolvendo com sucesso o endereço IP do ponto final privado.

Com base nas suas preferências, os seguintes cenários estão disponíveis com resolução DNS integrada:

- [Cargas de trabalho de rede virtuais sem servidor DNS personalizado](#virtual-network-workloads-without-custom-dns-server)
- [Cargas de trabalho no local utilizando um remetente DNS](#on-premises-workloads-using-a-dns-forwarder)
- [Cargas de trabalho de rede virtual e no local utilizando um reencaminhador de DNS](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)


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

Este modelo pode ser estendido a várias redes virtuais espreitadas que estão associadas ao mesmo ponto final privado. Isto pode ser feito [adicionando novas ligações de rede virtual](../dns/private-dns-virtual-network-links.md) à zona privada de DNS para todas as redes virtuais.

> [!IMPORTANT]
> Para esta configuração é necessária uma única zona privada de DNS. Criar várias zonas com o mesmo nome para diferentes redes virtuais precisaria de operações manuais para fundir os registos DNS.

> [!IMPORTANT]
> se estiver a utilizar um ponto final privado num modelo de hub-e-spoke a partir de uma subscrição diferente, reutilizámos a mesma zona privada de DNS no centro.

Neste cenário, há um [hub e falou](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) topologia em rede com as redes de fala partilhando um ponto final privado comum, e todas as redes virtuais faladas estão ligadas à mesma zona privada de DNS. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Rede virtual única e DNS fornecidos pelo Azure":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Cargas de trabalho no local utilizando um remetente DNS

Para que as cargas de trabalho no local resolvam um FQDN de um ponto final privado no endereço IP privado, deve utilizar um reencaminhador de DNS para implementar a resolução da zona pública de [DNS](#azure-services-dns-zone-configuration) do serviço Azure em Azure.

O seguinte cenário é apropriado para uma rede no local que tenha um reencaminhador de DNS em Azure, que por sua vez é responsável pela resolução de todas as consultas de DNS através de um reencaminhador de nível de servidor para o DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)fornecido pelo Azure. 

> [!NOTE]
> Este cenário utiliza a zona privada de DNS recomendada pela Base de Dados Azure SQL.Para outros serviços, pode ajustar o modelo utilizando a seguinte referência: Configuração da [zona DNS dos serviços Azure](#azure-services-dns-zone-configuration).

Para configurar corretamente, precisa dos seguintes recursos:

- Rede no local
- Rede virtual [ligada às instalações](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Encaminhador de DNS implantado em Azure 
- Zonas privadas de DNS [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)   com registo do tipo [A](../dns/dns-zones-records.md#record-types)
- Informações privadas sobre o ponto final (nome de registo FQDN e endereço IP privado)

O diagrama seguinte ilustra a sequência de resolução de DNS a partir de uma rede de acesso ao local que utiliza um reencaminhador de DNS implantado em Azure, onde a resolução é feita por uma zona privada de DNS [ligada a uma rede virtual:](../dns/private-dns-virtual-network-links.md)

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Rede virtual única e DNS fornecidos pelo Azure":::

Esta configuração pode ser estendida para uma rede no local que já tenha uma solução DNS no lugar. 
A solução DNS no local precisa de ser configurada para encaminhar o tráfego DNS para o Azure DNS através de um [reencaminhador condicional](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) que faz referência ao reencaminhador DNS implantado no Azure.

> [!NOTE]
> Este cenário utiliza a zona privada de DNS recomendada pela Base de Dados Azure SQL. Para outros serviços, pode ajustar o modelo utilizando a seguinte referência: [Configuração da zona DNS dos serviços Azure](#azure-services-dns-zone-configuration)

Para configurar corretamente, precisa dos seguintes recursos:

- Rede de instalações com uma solução de DNS personalizada no lugar 
- Rede virtual [ligada às instalações](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Encaminhador de DNS implantado em Azure
- Zonas privadas de DNS [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)    com registo do tipo [A](../dns/dns-zones-records.md#record-types)
- Informações privadas sobre o ponto final (nome de registo FQDN e endereço IP privado)

O diagrama seguinte ilustra a sequência de resolução de DNS de uma rede de informação que encaminha condicionalmente o tráfego de DNS para Azure, onde a resolução é feita por uma zona privada de DNS [ligada a uma rede virtual.](../dns/private-dns-virtual-network-links.md)

> [!IMPORTANT]
> O reencaminhamento condicional deve ser feito para o [reencaminhador público](#azure-services-dns-zone-configuration)recomendado para a zona de DNS .Por exemplo:  `database.windows.net`   em vez de **privatelink**.database.windows.net.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Rede virtual única e DNS fornecidos pelo Azure":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>Cargas de trabalho de rede virtual e no local utilizando um reencaminhador de DNS

Para uma abordagem geral que seja adequada para cargas de trabalho que necessitem de acesso a um ponto final privado a partir de redes virtuais e no local, deve utilizar um reencaminhador de DNS partilhado para fazer a resolução da zona pública de [DNS](#azure-services-dns-zone-configuration) do serviço Azure implantada em Azure.

O cenário seguinte é apropriado para uma rede no local que tenha um reencaminhador de DNS em Azure, e redes virtuais que precisam de acesso ao ponto final privado localizado numa rede de hubs partilhados.  

Este remetente DNS é responsável pela resolução de todas as consultas de DNS através de um reencaminhador de nível de servidor para o serviço DNS fornecido pelo Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md).

> [!IMPORTANT]
> Para esta configuração é necessária uma única zona privada de DNS. Todas as ligações com o cliente feitas a partir de instalações e [redes virtuais com vista](../virtual-network/virtual-network-peering-overview.md) também devem utilizar a mesma zona privada de DNS.

> [!NOTE]
> Este cenário utiliza a zona privada de DNS recomendada pela Base de Dados Azure SQL. Para outros serviços, pode ajustar o modelo utilizando a seguinte referência: Configuração da [zona DNS dos serviços Azure](#azure-services-dns-zone-configuration).

Para configurar corretamente, precisa dos seguintes recursos:

- Rede no local
- Rede virtual [ligada às instalações](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- [Rede virtual esprevada](../virtual-network/virtual-network-peering-overview.md) 
- Encaminhador de DNS implantado em Azure
- Zonas privadas de DNS [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)    com registo do tipo [A](../dns/dns-zones-records.md#record-types)
- Informações privadas sobre o ponto final (nome de registo FQDN e endereço IP privado)

O diagrama que se segue ilustra a sequência de resolução de DNS a partir de uma rede virtual que utiliza um reencaminhador DENS implantado em Azure, onde a resolução é feita por uma zona privada de DNS [ligada a uma rede virtual:](../dns/private-dns-virtual-network-links.md)

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="Rede virtual única e DNS fornecidos pelo Azure":::

## <a name="next-steps"></a>Passos seguintes
- [Conheça os pontos finais privados](private-endpoint-overview.md)
