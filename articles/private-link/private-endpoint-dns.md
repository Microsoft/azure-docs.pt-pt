---
title: Configuração DNS de ponto final privado Azure
description: Aprenda a configuração dNS private endpoint do Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 7c8ff0808ada522dc24ef3c27156f4151832fbcd
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715941"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Configuração DNS de ponto final privado Azure


Ao ligar-se a um recurso de ligação privado utilizando um nome de domínio totalmente qualificado (FQDN) como parte da cadeia de ligação, é importante configurar corretamente as definições de DNS para resolver o endereço IP privado atribuído. Os serviços Azure existentes podem já ter uma configuração DNS para utilizar ao ligar em um ponto final público. Esta configuração precisa de ser ultrapassada para se ligar utilizando o seu ponto final privado. 
 
A interface de rede associada ao ponto final privado contém o conjunto completo de informações necessárias para configurar o seu DNS, incluindo endereços IP FQDN e privados atribuídos para um determinado recurso de ligação privada. 
 
Pode utilizar as seguintes opções para configurar as definições de DNS para pontos finais privados: 
- **Utilize o ficheiro Anfitrião (apenas recomendado para testes)**. Pode utilizar o ficheiro anfitrião numa máquina virtual para anular o DNS.  
- **Utilize uma zona Privada de DNS**. Você pode usar [zonas privadas de DNS](../dns/private-dns-privatednszone.md) para anular a resolução DNS para um determinado ponto final privado. Uma zona privada de DNS pode ser ligada à sua rede virtual para resolver domínios específicos.
- **Utilize o seu avançado DNS (Opcional)**. Pode utilizar o seu reencaminhador DNS para anular a resolução do DNS para um determinado recurso de ligação privada. Se o seu [servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) estiver hospedado numa rede virtual, pode criar uma regra de encaminhamento DNS para utilizar uma zona Privada de DNS para simplificar a configuração de todos os recursos de ligação privada.
 
> [!IMPORTANT]
> Não é aconselhável anular uma zona que está ativamente em uso para resolver pontos finais públicos. As ligações aos recursos não serão capazes de resolver corretamente sem que o DNS reencaminha sacar ao DNS público. Para evitar problemas, crie um nome de domínio diferente ou siga o nome sugerido para cada serviço abaixo. 

## <a name="azure-services-dns-zone-configuration"></a>Configuração da zona DNS dos serviços Azure
Os serviços azure criarão um registo de DNS de nome canónico (CNAME) no DNS público para redirecionar a resolução para o nome de domínio privado sugerido. Pode anular a resolução com o endereço IP privado dos seus pontos finais privados. 
 
As suas aplicações não precisam de alterar o URL de ligação. Ao tentar resolver a utilização de um DNS público, o servidor DNS irá agora resolver os seus pontos finais privados. O processo não afeta as suas aplicações existentes. 

Para os serviços Azure, utilize os nomes de zona recomendados descritos no quadro seguinte:

| Tipo de recurso de ligação privada / Subrecurso |Nome da Zona DNS Privada | Avançados públicos da zona dNS |
|---|---|---|---|
| SQL DB (Microsoft.Sql/servidores) / Sql Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (Microsoft.Sql/servidores) / Sql Server  | privatelink.database.windows.net | database.windows.net |
| Conta de Armazenamento (Microsoft.Storage/storageAccounts) / Blob (blob, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Conta de Armazenamento (Microsoft.Storage/storageAccounts) / Tabela (tabela, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Conta de Armazenamento (Microsoft.Storage/storageAccounts) / Fila (fila, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Conta de Armazenamento (Microsoft.Storage/storageAccounts) / Ficheiro (ficheiro, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Conta de Armazenamento (Microsoft.Storage/storageAccounts) / Web (web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Data Lake File System Gen2 (Microsoft.Storage/storageAccounts) / Data Lake File System Gen2 (dfs, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Tabela | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Base de Dados Azure para PostgreSQL - Servidor único (Microsoft.DBforPostgreSQL/servidores) / postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Base de Dados Azure para MySQL (Microsoft.DBforMySQL/servidores) / mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Base de Dados Azure para MariaDB (Microsoft.DBforMariaDB/servidores) / mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Cofre de Chaves Azure (Microsoft.KeyVault/cofres) / cofre | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Serviço Azure Kubernetes - Kubernetes API (Microsoft.containerService/managedClusters) / managedCluster | privatelink. {região}.azmk8s.io | {região}.azmk8s.io |
| Pesquisa Azure (Microsoft.Search/searchServices) / searchService | privatelink.search.windows.net | search.windows.net |
| Registo de contentores azure (Microsoft.ContainerRegistry/registos) /registo | privatelink.azurecr.io | azurecr.io |
| Configuração de aplicativos Azure (Microsoft.AppConfiguration/configuraçãoStores) / configuraçãoLoja | privatelink.azconfig.io | azconfig.io |
| Backup Azure (Microsoft.RecoveryServices/cofres) / cofre | privatelink. {region}.backup.windowsazure.com | {region}.backup.windowsazure.com |
| Azure Event Hub (Microsoft.EventHub/namespaces) / espaço de nome | privatelink.servicebus.windows.net | servicebus.windows.net |
| Ônibus de serviço Azure (Microsoft.ServiceBus/espaços de nome) / espaço de nome | privatelink.servicebus.windows.net | servicebus.windows.net |
| Relé Azure (Microsoft.Relay/namespaces) / espaço de nome | privatelink.servicebus.windows.net | servicebus.windows.net |
| Grelha de Eventos Azure (Microsoft.EventGrid/tópicos) / tópico | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Grelha de Eventos Azure (Microsoft.EventGrid/domínios) / domínio | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| WebApps azure (Microsoft.Web/sites) / site | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft.MachineLearningServices/workspaces) / espaço de trabalho | privatelink.api.azureml.ms | api.azureml.ms |

 


## <a name="dns-configuration-scenarios"></a>Cenários de configuração dNS

O FQDN dos serviços resolve-se automaticamente para um endereço IP público, por isso, para resolver o endereço IP privado do ponto final privado, deve alterar a sua configuração DeNS em conformidade.

O DNS é um componente crítico para que a aplicação funcione corretamente, resolvendo de forma correta o endereço IP do ponto final privado.

Com base nas suas preferências, estão disponíveis os seguintes cenários para a resolução dNS integrada:

- [Cargas de trabalho de rede virtuais sem servidor DNS personalizado](#virtual-network-workloads-without-custom-dns-server)
- [Cargas de trabalho no local utilizando um avançado do DNS](#on-premises-workloads-using-a-dns-forwarder)

## <a name="virtual-network-workloads-without-custom-dns-server"></a>Cargas de trabalho de rede virtuais sem servidor DNS personalizado

Esta configuração é apropriada para cargas de trabalho de rede virtuais sem servidor DNS personalizado. Neste cenário, as consultas de clientes para o endereço IP do ponto final privado para o Azure forneceram DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). O Azure DNS será responsável pela resolução dNS das zonas privadas de DNS.


> [!NOTE]
> Este cenário está a utilizar a base de dados Azure SQL recomendada para a zona Privada dNS. Para outros serviços pode ajustar o modelo utilizando a seguinte configuração de [zona DNS dos serviços Azure](#azure-services-dns-zone-configuration)de referência .

Para configurar corretamente, precisaria dos seguintes recursos:

- Rede virtual de cliente

- Zona Privada dNS [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) com [registo tipo A](../dns/dns-zones-records.md#record-types)

- Informações de ponto final privado (nome de registo FQDN e endereço IP privado)

O diagrama seguinte ilustra a sequência de resolução dNS a partir de cargas de trabalho de rede virtuais usando a zona privada dns

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Rede virtual única e DNS fornecidos pelo Azure":::

Este modelo pode ser estendido a várias redes virtuais com pares que estão associadas ao mesmo ponto final privado. Isto pode ser feito [adicionando novas ligações de rede virtual](../dns/private-dns-virtual-network-links.md) à zona privada de DNS para todas as redes virtuais.

> [!IMPORTANT]
> Uma única zona privada de DNS é necessária para esta configuração, criando múltiplas zonas com o mesmo nome para diferentes redes virtuais precisaria de operações manuais para fundir os registos dNS

Neste cenário, há um [hub & topologia de](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) networking falado com as redes faladas que partilham um ponto final privado comum e toda a rede virtual falada está ligada à mesma zona privada de DNS. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Hub e falou com dNS fornecido pelo Azure":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Cargas de trabalho no local utilizando um avançado do DNS

Para que as cargas de trabalho no local possam resolver um FQDN de um ponto final privado no endereço IP privado, deve utilizar um reencaminhador DNS para efetuar a resolução da zona pública de [DNS](#azure-services-dns-zone-configuration) de serviço Azure implantada em Azure.


O seguinte cenário é apropriado para uma rede no local que tenha um avançado dNS em Azure, que por sua vez é responsável pela resolução de todas as consultas de DNS através de um forwardr de nível de servidor para o Azure fornecido DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) 

> [!NOTE]
> Este cenário está a utilizar a base de dados Azure SQL recomendada para a zona Privada dNS.Para outros serviços pode ajustar o modelo utilizando a seguinte configuração de [zona DNS dos serviços Azure](#azure-services-dns-zone-configuration)de referência .

Para configurar corretamente, precisaria dos seguintes recursos:

- Rede no local
- Rede virtual [ligada às instalações](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Avançado do DNS implantado em Azure 
- Zonas privadas de DNS [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)   com registo tipo [A](../dns/dns-zones-records.md#record-types)
- Informações de ponto final privado (nome de registo FQDN e endereço IP privado)

O diagrama seguinte ilustra a sequência de resolução dNS de uma rede no local que utiliza um reencaminhador DNS implantado em Azure, onde a resolução é feita por uma zona privada de DNS [ligada a uma rede virtual.](../dns/private-dns-virtual-network-links.md)

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="No local usando DNS Azure":::

Esta configuração pode ser estendida para uma rede no local que já tem uma solução DNS no lugar. 
A solução DNS no local precisa de ser configurada para encaminhar o tráfego de DNS para o DNS Azure através de um [avançado condicional](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) que referencia o avançado do DNS implantado em Azure.

> [!NOTE]
> Este cenário está a utilizar a base de dados Azure SQL recomendada para a zona Privada dNS.Para outros serviços pode ajustar o modelo utilizando a seguinte configuração de [zona DNS dos serviços Azure](#azure-services-dns-zone-configuration)de referência .

Para configurar corretamente, precisaria dos seguintes recursos:

- Rede no local com uma solução DNS personalizada no lugar 
- Rede virtual [ligada às instalações](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Avançado do DNS implantado em Azure
- Zonas privadas de DNS [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)    com registo tipo [A](../dns/dns-zones-records.md#record-types)
- Informações de ponto final privado (nome de registo FQDN e endereço IP privado)

O diagrama seguinte ilustra a sequência de resolução dNS de uma rede no local que encaminha condicionalmente o tráfego de DNS para Azure, onde a resolução é feita por uma zona privada de DNS [ligada a uma rede virtual.](../dns/private-dns-virtual-network-links.md)

> [!IMPORTANT]
> O encaminhamento condicional deve ser feito ao avançado público recomendado da [**zona DNS**](#azure-services-dns-zone-configuration)   Ex: , em vez de  `database.windows.net`    **privatelink**.database.windows.net

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="No local encaminhamento para O DNS Azure":::


## <a name="next-steps"></a>Próximos passos
- [Saiba mais sobre pontos finais privados](private-endpoint-overview.md)
