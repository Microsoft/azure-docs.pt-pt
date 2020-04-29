---
title: Configuração DNS de ponto final privado Azure
description: Aprenda a configuração dNS private endpoint do Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 477a5ffa971120d1a98c09ac4ae8ebda1c82b770
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82209031"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Configuração DNS de ponto final privado Azure


Ao ligar-se a um recurso de ligação privado utilizando um nome de domínio totalmente qualificado (FQDN) como parte da cadeia de ligação, é importante configurar corretamente as definições de DNS para resolver o endereço IP privado atribuído. Os serviços Azure existentes podem já ter uma configuração DNS para utilizar ao ligar em um ponto final público. Isto precisa de ser ultrapassado para se ligar usando o seu ponto final privado. 
 
A interface de rede associada ao ponto final privado contém o conjunto completo de informações necessárias para configurar o seu DNS, incluindo endereços IP FQDN e privados atribuídos para um determinado recurso de ligação privada. 
 
Pode utilizar as seguintes opções para configurar as definições de DNS para pontos finais privados: 
- **Utilize o ficheiro Anfitrião (apenas recomendado para testes)**. Pode utilizar o ficheiro anfitrião numa máquina virtual para anular o DNS.  
- **Utilize uma zona Privada de DNS**. Você pode usar [zonas privadas de DNS](../dns/private-dns-privatednszone.md) para anular a resolução DNS para um determinado ponto final privado. Uma zona privada de DNS pode ser ligada à sua rede virtual para resolver domínios específicos.
- **Utilize o seu servidor DNS personalizado**. Pode utilizar o seu próprio servidor DNS para anular a resolução dNS para um determinado recurso de ligação privada. Se o seu [servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) estiver hospedado numa rede virtual, pode criar uma regra de encaminhamento DNS para utilizar uma zona Privada de DNS para simplificar a configuração de todos os recursos de ligação privada.
 
> [!IMPORTANT]
> Não é aconselhável anular uma zona que está ativamente em uso para resolver pontos finais públicos. As ligações aos recursos não serão capazes de resolver corretamente sem que o DNS reencaminha sacar ao DNS público. Para evitar problemas, crie um nome de domínio diferente ou siga o nome sugerido para cada serviço abaixo. 

## <a name="azure-services-dns-zone-configuration"></a>Configuração da zona DNS dos serviços Azure
Os serviços azure criarão um registo de DNS de nome canónico (CNAME) no DNS público para redirecionar a resolução para os nomes de domínio privado sugeridos. Poderá anular a resolução com o endereço IP privado dos seus pontos finais privados. 
 
As suas aplicações não precisam de alterar o URL de ligação. Ao tentar resolver a utilização de um DNS público, o servidor DNS irá agora resolver os seus pontos finais privados. O processo não afeta as suas aplicações existentes. 

Para os serviços Azure, utilize os nomes de zona recomendados descritos no quadro seguinte:

|Tipo de recurso de ligação privada   |Subrecurso  |Nome da zona  |
|---------|---------|---------|
|SQL DB (Microsoft.Sql/servidores)    |  Sql Server (sqlServer)        |   privatelink.database.windows.net       |
|Azure Synapse Analytics (Microsoft.Sql/servidores)    |  Sql Server (sqlServer)        | privatelink.database.windows.net |
|Conta de Armazenamento (Microsoft.Storage/storageAccounts)    |  Blob (bolha, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Conta de Armazenamento (Microsoft.Storage/storageAccounts)    |    Tabela (mesa, table_secondary)      |   privatelink.table.core.windows.net       |
|Conta de Armazenamento (Microsoft.Storage/storageAccounts)    |    Fila (fila, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Conta de Armazenamento (Microsoft.Storage/storageAccounts)   |    Arquivo (arquivo, file_secondary)      |    privatelink.file.core.windows.net      |
|Conta de Armazenamento (Microsoft.Storage/storageAccounts)     |  Web (web, web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake File System Gen2 (Microsoft.Storage/storageAccounts)  |  Data Lake File System Gen2 (dfs, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|SQL    |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|MongoDB    |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Gremlin    |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Tabela|privatelink.table.cosmos.azure.com|
|Base de Dados Azure para PostgreSQL - Servidor único (Microsoft.DBforPostgreSQL/servidores)|postgresqlServer|privatelink.postgres.database.azure.com|
|Base de Dados Azure para MySQL (Microsoft.DBforMySQL/servidores)|mysqlServer|privatelink.mysql.database.azure.com|
|Base de Dados Azure para MariaDB (Microsoft.DBforMariaDB/servidores)|mariadbServer|privatelink.mariadb.database.azure.com|
|Cofre de Chaves Azure (Microsoft.KeyVault/cofres)|cofre|privatelink.vaultcore.azure.net|
|Serviço Azure Kubernetes - Kubernetes API (Microsoft.ContainerService/managedClusters)    | managedCluster | {guid}.privatelink. {região}.azmk8s.io|
|Pesquisa Azure (Microsoft.Search/searchServices)|pesquisaServiço|privatelink.search.windows.net|   
|Registo de contentores azure (Microsoft.ContainerRegistry/registos) | registry | privatelink.azurecr.io |
|Configuração de aplicativos Azure (Microsoft.Appconfiguration/configuraçãoStores)| configuraçãoLoja | privatelink.azconfig.io|
|Backup Azure (Microsoft.RecoveryServices/cofres)| cofre |privatelink. {region}.backup.windowsazure.com|
|Azure Event Hub (Microsoft.EventHub/espaços de nome)| espaço de nomes |privatelink.servicebus.windows.net|
|Ônibus de serviço Azure (Microsoft.ServiceBus/espaços de nome) | espaço de nomes |privatelink.servicebus.windows.net|
|Relé Azure (Microsoft.Relay/namespaces) | espaço de nomes |privatelink.servicebus.windows.net|
|Grelha de Eventos Azure (Microsoft.EventGrid/tópicos)     | tópico | tópico. {region}.privatelink.eventgrid.azure.net|
|Grelha de Eventos Azure (Microsoft.EventGrid/domínios) | domínio | domínio. {region}.privatelink.eventgrid.azure.net |
|WebApps azure (Microsoft.Web/sites)    | site | privatelink.azurewebsites.net |
|Aprendizagem automática azure (Microsoft.MachineLearningServices/espaços de trabalho)    | área de trabalho | privatelink.api.azureml.ms |
 


## <a name="dns-configuration-scenarios"></a>Cenários de configuração dNS

O FQDN dos serviços resolve um endereço ip público, você tem que alterar a sua configuração DNS para resolver o endereço IP privado do ponto final privado.

O DNS é um componente crítico para que a aplicação funcione corretamente, resolvendo de forma correta o endereço IP do ponto final privado.

Com base nas suas preferências, estão disponíveis os seguintes cenários para a resolução dNS integrada:

- [Cargas de trabalho da Rede Virtual sem servidor DNS personalizado](#virtual-network-workloads-without-custom-dns-server)


## <a name="virtual-network-workloads-without-custom-dns-server"></a>Cargas de trabalho da Rede Virtual sem servidor DNS personalizado

Esta configuração é apropriada para cargas de trabalho de rede virtuais sem servidor DNS personalizado. Neste cenário, as consultas de clientes para o endereço IP do ponto final privado para o Azure forneceram DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). O Azure DNS será responsável pela resolução dNS das zonas privadas de DNS.


 > [!NOTE]
> Este cenário está a utilizar a base de dados Azure SQL recomendada para a zona Privada dNS. Para outros serviços pode ajustar o modelo utilizando a seguinte configuração de [zona DNS dos serviços Azure](#azure-services-dns-zone-configuration)de referência .

Para configurar corretamente, precisaria dos seguintes recursos:

- Rede virtual de cliente

- Zona Privada dNS [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) com [registo tipo A](../dns/dns-zones-records.md#record-types)

- Informações de ponto final privado (nome de registo FQDN e endereço IP privado)

O diagrama seguinte ilustra a sequência de resolução dNS a partir de cargas de trabalho de rede virtuais usando a zona privada dns

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="única rede virtual e azure fornecido dns":::

Este modelo pode ser estendido a várias redes virtuais com pares que estão associadas ao mesmo ponto final privado. Isto pode ser feito [adicionando novas ligações de rede virtual](../dns/private-dns-virtual-network-links.md) à zona privada de DNS para todas as redes virtuais.

 > [!IMPORTANT]
>  Uma única zona privada de DNS é necessária para esta configuração, criando múltiplas zonas com o mesmo nome para diferentes redes virtuais precisaria de operações manuais para fundir os registos dNS

Neste cenário há um [hub & topologia de](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) networking falado com as redes faladas que partilham um ponto final privado comum e toda a rede virtual falada está ligada à mesma zona privada de DNS. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="hub e falou com azure fornecido dns":::


## <a name="next-steps"></a>Passos seguintes
- [Saiba mais sobre pontos finais privados](private-endpoint-overview.md)
