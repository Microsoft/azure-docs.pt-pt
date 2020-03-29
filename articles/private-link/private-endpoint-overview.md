---
title: O que é um Ponto Final Privado Azure?
description: Saiba mais sobre o Azure Private Endpoint
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: fd389c1e909e6875ead8410b5ca692b82c79e0de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063079"
---
# <a name="what-is-azure-private-endpoint"></a>O que é Azure Private Endpoint?

O Ponto Final Privado do Azure é uma interface de rede que o liga a um serviço de forma privada e segura com a tecnologia Azure Private Link. O Private Endpoint utiliza um endereço IP privado do seu VNet, efetivamente trazendo o serviço para o seu VNet. O serviço poderia ser um serviço Azure como Azure Storage, Azure Cosmos DB, SQL, etc. ou o seu próprio [Serviço de Ligação Privada.](private-link-service-overview.md)
  
## <a name="private-endpoint-properties"></a>Propriedades private endpoint 
 Um Ponto Final Privado especifica as seguintes propriedades: 


|Propriedade  |Descrição |
|---------|---------|
|Nome    |    Um nome único dentro do grupo de recursos.      |
|Subrede    |  A subnet para implantar e alocar endereços IP privados a partir de uma rede virtual. Para obter requisitos de sub-rede, consulte a secção Limitações neste artigo.         |
|Recurso de ligação privada    |   O recurso de ligação privada para ligar usando identificação ou pseudónimo de recursos, a partir da lista de tipos disponíveis. Será gerado um identificador de rede único para todo o tráfego enviado para este recurso.       |
|Subrecurso-alvo   |      O subrecurso para ligar. Cada tipo de recurso de ligação privada tem diferentes opções para selecionar com base na preferência.    |
|Método de aprovação de ligação    |  Automático ou manual. Com base em permissões de controlo de acesso baseadas em funções (RBAC), o seu ponto final privado pode ser aprovado automaticamente. Se tentar ligar-se a um recurso de ligação privada sem RBAC, utilize o método manual para permitir ao proprietário do recurso aprovar a ligação.        |
|Mensagem de Pedido     |  Pode especificar uma mensagem para que as ligações solicitadas sejam aprovadas manualmente. Esta mensagem pode ser usada para identificar um pedido específico.        |
|Estado da ligação   |   Uma propriedade só para leitura que especifica se o ponto final privado está ativo. Apenas pontos finais privados num estado aprovado podem ser usados para enviar tráfego. Estados adicionais disponíveis: <br>-**Aprovado**: A ligação foi aprovada automaticamente ou manualmente e está pronta a ser utilizada.</br><br>-**Pendente**: A ligação foi criada manualmente e está pendente de aprovação pelo proprietário de recursos de ligação privada.</br><br>-**Rejeitado**: A ligação foi rejeitada pelo proprietário de recursos de ligação privada.</br><br>-**Desligado**: A ligação foi removida pelo proprietário do recurso de ligação privada. O ponto final privado torna-se informativo e deve ser eliminado para limpeza. </br>|

Aqui estão alguns detalhes chave sobre pontos finais privados: 
- O ponto final privado permite a conectividade entre os consumidores do mesmo VNet, VNets regionalmente peered, VNets globalmente e em instalações usando [VPN](https://azure.microsoft.com/services/vpn-gateway/) ou [Rota Expresso](https://azure.microsoft.com/services/expressroute/) e serviços alimentados por Private Link.
 
- Ao criar um ponto final privado, é também criada uma interface de rede para o ciclo de vida do recurso. A interface é atribuída a um endereço IP privado da subnet que mapeia para o Serviço de Ligação Privada.
 
- O ponto final privado deve ser implantado na mesma região que a rede virtual. 
 
- O recurso de ligação privada pode ser implantado numa região diferente da rede virtual e do ponto final privado.
 
- Vários pontos finais privados podem ser criados usando o mesmo recurso de ligação privada. Para uma única rede utilizando uma configuração comum do servidor DNS, a prática recomendada é utilizar um único ponto final privado para um determinado recurso de ligação privada para evitar entradas duplicadas ou conflitos na resolução dNS. 
 
- Vários pontos finais privados podem ser criados nas mesmas subredes ou diferentes dentro da mesma rede virtual. Existem limites para o número de pontos finais privados que pode criar numa subscrição. Para mais detalhes, consulte [os limites de Azure.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


 
## <a name="private-link-resource"></a>Recurso de ligação privada 
Um recurso de ligação privada é o alvo de destino de um determinado ponto final privado. Segue-se uma lista dos tipos de recursos de ligação privada disponíveis: 
 
|Nome de recurso de ligação privada  |Tipo de recurso   |Subrecursos  |
|---------|---------|---------|
|**Serviço de Link Privado** (Seu próprio serviço)   |  Microsoft.Network/privateLinkServices       | vazio |
|**Base de Dados SQL do Azure** | Microsoft.Sql/servidores    |  Sql Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servidores    |  Sql Server (sqlServer)        | 
|**Storage do Azure**  | Microsoft.Storage/storageAccounts    |  Blob (bolha, blob_secondary)<BR> Tabela (mesa, table_secondary)<BR> Fila (fila, queue_secondary)<BR> Arquivo (arquivo, file_secondary)<BR> Web (web, web_secondary)        |
|**Azure Data Lake Storage Gen2** (Armazenamento do Azure Data Lake Gen2)  | Microsoft.Storage/storageAccounts    |  Blob (bolha, blob_secondary)<BR> Data Lake File System Gen2 (dfs, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts | Sql, MongoDB, Cassandra, Gremlin, Mesa|
|**Base de Dados Azure para PostgreSQL -Servidor único** | Microsoft.DBforPostgreSQL/servidores   | postgresqlServer |
|**Base de Dados do Azure para MySQL** | Microsoft.DBforMySQL/servidores    | mysqlServer |
|**Azure Database for MariaDB** | Microsoft.DBforMariaDB/servidores    | mariadbServer |
|**Azure Key Vault** | Microsoft.KeyVault/cofres    | cofre |
|**Serviço Azure Kubernetes - Kubernetes API** | Microsoft.ContainerService/managedClusters | managedCluster |
|**Azure Search** | Microsoft.Search/searchService| pesquisaServiço|  
|**Registo de Contentores Azure** | Microsoft.ContainerRegistry/registos  | registry |
|**Configuração da Aplicação Azure** | Microsoft.Appconfiguration/configuraçãoStores   | configuraçãoLoja |
|**Azure Backup** | Microsoft.RecoveryServices/cofres   | cofre |
|**Hub de Eventos do Azure** | Microsoft.EventHub/espaços de nome    | espaço de nomes |
|**Azure Service Bus** | Microsoft.ServiceBus/espaços de nome | espaço de nomes |
|**Reencaminhamento do Azure** | Microsoft.Relay/namespaces | espaço de nomes |
|**Azure Event Grid** | Microsoft.EventGrid/tópicos  | tópico |
|**Azure Event Grid** | Microsoft.EventGrid/domínios | domínio |
|**Azure WebApps** | Microsoft.Web/sites    | sites |
 
## <a name="network-security-of-private-endpoints"></a>Segurança da rede de pontos finais privados 
Ao utilizar pontos finais privados para serviços Azure, o tráfego é garantido a um recurso de ligação privada específico. A plataforma executa um controlo de acesso para validar as ligações de rede atingindo apenas o recurso de ligação privada especificado. Para aceder a recursos adicionais dentro do mesmo serviço Azure, são necessários pontos finais privados adicionais. 
 
Pode bloquear completamente as suas cargas de trabalho desde o acesso a pontos finais públicos para se ligar a um serviço Azure suportado. Este controlo fornece uma camada adicional de segurança de rede aos seus recursos, fornecendo uma proteção de exfiltração incorporada que impede o acesso a outros recursos alojados no mesmo serviço Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Acesso a um recurso de ligação privada utilizando fluxo de trabalho de aprovação 
Pode ligar-se a um recurso de ligação privada utilizando os seguintes métodos de aprovação de ligação:
- **Aprovado automaticamente** quando possui ou tem permissão no recurso de ligação privada específico. A permissão necessária baseia-se no tipo de recurso de ligação privada no seguinte formato: Microsoft. \<Fornecedor>/<resource_type>/privateEndpointConnectionApproval/action
- **Pedido manual** quando não tiver a permissão necessária e gostaria de solicitar acesso. Será iniciado um fluxo de trabalho de aprovação. O ponto final privado e a subsequente ligação do ponto final privado serão criados num estado “Pendente”. O proprietário do recurso de ligação privada é responsável por aprovar a ligação. Depois de aprovado, o ponto final privado está habilitado a enviar tráfego normalmente, como mostra o seguinte diagrama de fluxo de trabalho de aprovação.  

![aprovação de fluxo de trabalho](media/private-endpoint-overview/private-link-paas-workflow.png)
 
O proprietário de recursos de ligação privada pode realizar as seguintes ações sobre uma ligação de ponto final privado: 
- Reveja todos os detalhes das ligações de fim de ponta privadas. 
- Aprove uma ligação de ponto final privado. O ponto final privado correspondente será permitido enviar tráfego para o recurso de ligação privada. 
- Rejeite uma ligação de ponto final privado. O ponto final privado correspondente será atualizado para refletir o estado.
- Elimine uma ligação de ponto final privado em qualquer estado. O ponto final privado correspondente será atualizado com um estado desligado para refletir a ação, o proprietário do ponto final privado só pode apagar o recurso neste momento. 
 
> [!NOTE]
> Apenas um ponto final privado num estado aprovado pode enviar tráfego para um determinado recurso de ligação privada. 

### <a name="connecting-using-alias"></a>Ligação usando Alias
Alias é um apelido único que é gerado quando o proprietário do serviço cria o serviço de ligação privada por trás de um equilibrista de carga padrão. O proprietário do serviço pode partilhar este Alias com os seus consumidores offline. Os consumidores podem solicitar uma ligação ao serviço de ligação privada utilizando o recurso URI ou o Alias. Se pretender ligar-se utilizando alias, deve criar um ponto final privado utilizando o método de homologação manual de ligação. Para utilizar o método de homologação manual de ligação, defina o parâmetro manual de pedido para o verdadeiro durante o fim do ponto de partida privado criar fluxo. Veja a criação de pontos de venda privado sinuosos da [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) e da [rede Az.](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) 

## <a name="dns-configuration"></a>Configuração do DNS 
Ao ligar-se a um recurso de ligação privado utilizando um nome de domínio totalmente qualificado (FQDN) como parte da cadeia de ligação, é importante configurar corretamente as definições de DNS para resolver o endereço IP privado atribuído. Os serviços Azure existentes podem já ter uma configuração DNS para utilizar ao ligar em um ponto final público. Isto precisa de ser ultrapassado para se ligar usando o seu ponto final privado. 
 
A interface de rede associada ao ponto final privado contém o conjunto completo de informações necessárias para configurar o seu DNS, incluindo endereços IP FQDN e privados atribuídos para um determinado recurso de ligação privada. 
 
Pode utilizar as seguintes opções para configurar as definições de DNS para pontos finais privados: 
- **Utilize o ficheiro Anfitrião (apenas recomendado para testes)**. Pode utilizar o ficheiro anfitrião numa máquina virtual para anular o DNS.  
- **Utilize uma zona Privada de DNS**. Você pode usar zonas privadas de DNS para anular a resolução DNS para um determinado ponto final privado. Uma zona privada de DNS pode ser ligada à sua rede virtual para resolver domínios específicos.
- **Utilize o seu servidor DNS personalizado**. Pode utilizar o seu próprio servidor DNS para anular a resolução dNS para um determinado recurso de ligação privada. Se o seu [servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) estiver hospedado numa rede virtual, pode criar uma regra de encaminhamento DNS para utilizar uma zona Privada de DNS para simplificar a configuração de todos os recursos de ligação privada.
 
> [!IMPORTANT]
> Não é aconselhável anular uma zona que está ativamente em uso para resolver pontos finais públicos. As ligações aos recursos não serão capazes de resolver corretamente sem que o DNS reencaminha sacar ao DNS público. Para evitar problemas, crie um nome de domínio diferente ou siga o nome sugerido para cada serviço abaixo. 
 
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
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|SQL |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|MongoDB |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Gremlin |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Tabela|privatelink.table.cosmos.azure.com|
|Base de Dados Azure para PostgreSQL - Servidor único (Microsoft.DBforPostgreSQL/servidores)|postgresqlServer|privatelink.postgres.database.azure.com|
|Base de Dados Azure para MySQL (Microsoft.DBforMySQL/servidores)|mysqlServer|privatelink.mysql.database.azure.com|
|Base de Dados Azure para MariaDB (Microsoft.DBforMariaDB/servidores)|mariadbServer|privatelink.mariadb.database.azure.com|
|Cofre de Chaves Azure (Microsoft.KeyVault/cofres)|cofre|privatelink.vaultcore.azure.net|
|Serviço Azure Kubernetes - Kubernetes API (Microsoft.ContainerService/managedClusters) | managedCluster | {guid}.privatelink. <region>.azmk8s.io|
|Pesquisa Azure (Microsoft.Search/searchServices)|pesquisaServiço|privatelink.search.windows.net|   
|Registo de contentores azure (Microsoft.ContainerRegistry/registos) | registry | privatelink.azurecr.io |
|Configuração de aplicativos Azure (Microsoft.Appconfiguration/configuraçãoStores)| configuraçãoLoja | privatelink.azconfig.io|
|Backup Azure (Microsoft.RecoveryServices/cofres)| cofre |privatelink. {region}.backup.windowsazure.com|
|Azure Event Hub (Microsoft.EventHub/espaços de nome)| espaço de nomes |privatelink.servicebus.windows.net|
|Ônibus de serviço Azure (Microsoft.ServiceBus/espaços de nome) | espaço de nomes |privatelink.servicebus.windows.net|
|Relé Azure (Microsoft.Relay/namespaces) | espaço de nomes |privatelink.servicebus.windows.net|
|Grelha de Eventos Azure (Microsoft.EventGrid/tópicos)   | tópico | tópico. {region}.privatelink.eventgrid.azure.net|
|Grelha de Eventos Azure (Microsoft.EventGrid/domínios) | domínio | domínio. {region}.privatelink.eventgrid.azure.net |
|WebApps azure (Microsoft.Web/sites) | site | privatelink.azurewebsites.net |
 
O Azure criará um registo de DNS de nome canónico (CNAME) no DNS público para redirecionar a resolução para os nomes de domínio sugeridos. Poderá anular a resolução com o endereço IP privado dos seus pontos finais privados. 
 
As suas aplicações não precisam de alterar o URL de ligação. Ao tentar resolver a utilização de um DNS público, o servidor DNS irá agora resolver os seus pontos finais privados. O processo não afeta as suas aplicações. 
 
## <a name="limitations"></a>Limitações
 
O quadro seguinte inclui uma lista de limitações conhecidas ao utilizar pontos finais privados: 


|Limitação |Descrição |Mitigação  |
|---------|---------|---------|
|As regras do Grupo de Segurança da Rede (NSG) e as rotas definidas pelo utilizador não se aplicam ao Ponto Final Privado    |A NSG não é apoiada em pontos finais privados. Embora as subredes que contenham o ponto final privado possam ter o NSG associado a ele, as regras não serão eficazes no tráfego processado pelo ponto final privado. Deve ter políticas de [rede desativadas](disable-private-endpoint-network-policy.md) para implantar pontos finais privados numa subnet. O NSG ainda é aplicado em outras cargas de trabalho alojadas na mesma subnet. As rotas em qualquer subnet do cliente estarão a usar um prefixo /32, alterando o comportamento de encaminhamento predefinido requer um UDR semelhante  | Controlar o tráfego utilizando regras de NSG para tráfego de saída em clientes de origem. Implementar rotas individuais com prefixo /32 para sobrepor rotas de pontos finais privados. Os registos de fluxo NSG e informações de monitorização para ligações de saída ainda são suportados e podem ser usados        |


## <a name="next-steps"></a>Passos seguintes
- [Criar um ponto final privado para o Servidor de Base de Dados SQL utilizando o Portal](create-private-endpoint-portal.md)
- [Criar um ponto final privado para o Servidor de Base de Dados SQL usando powerShell](create-private-endpoint-powershell.md)
- [Criar um ponto final privado para o Servidor de Base de Dados SQL utilizando o CLI](create-private-endpoint-cli.md)
- [Criar um ponto final privado para a conta de armazenamento usando o Portal](create-private-endpoint-storage-portal.md)
- [Criar um endpoint privado para a conta Azure Cosmos usando o Portal](../cosmos-db/how-to-configure-private-endpoints.md)
- [Crie o seu próprio serviço private link usando o Azure PowerShell](create-private-link-service-powershell.md)
- [Crie o seu próprio Link Privado para base de dados Azure para PostgreSQL - Servidor único usando o Portal](../postgresql/howto-configure-privatelink-portal.md)
- [Crie o seu próprio Link Privado para base de dados Azure para PostgreSQL - Servidor único usando CLI](../postgresql/howto-configure-privatelink-cli.md)
- [Crie o seu próprio Link Privado para base de dados Azure para MySQL usando o Portal](../mysql/howto-configure-privatelink-portal.md)
- [Crie o seu próprio Link Privado para base de dados Azure para MySQL usando cli](../mysql/howto-configure-privatelink-cli.md)
- [Crie o seu próprio Link Privado para base de dados Azure para MariaDB usando o Portal](../mariadb/howto-configure-privatelink-portal.md)
- [Crie o seu próprio Link Privado para base de dados Azure para MariaDB usando o CLI](../mariadb/howto-configure-privatelink-cli.md)
