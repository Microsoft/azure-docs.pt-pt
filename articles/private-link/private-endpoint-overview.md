---
title: O que é um Azure Private Endpoint?
description: Saiba mais sobre o Azure Private Endpoint
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: def394369ebcb8b457d78ad6537830487e8049f7
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92277507"
---
# <a name="what-is-azure-private-endpoint"></a>O que é o Ponto Final Privado do Azure?

O Ponto Final Privado do Azure é uma interface de rede que o liga a um serviço de forma privada e segura com a tecnologia Azure Private Link. O Ponto Final Privado utiliza um endereço IP privado na VNet e leva de forma eficaz o serviço até à VNet. O serviço pode ser um serviço Azure como Azure Storage, Azure Cosmos DB, SQL, etc. ou o seu próprio [Serviço de Ligação Privada](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Propriedades private Endpoint 
 Um ponto final privado especifica as seguintes propriedades: 


|Propriedade  |Descrição |
|---------|---------|
|Nome    |    Um nome único dentro do grupo de recursos.      |
|Sub-rede    |  A sub-rede para implantar e alocar endereços IP privados a partir de uma rede virtual. Para obter os requisitos da sub-rede, consulte a secção Limitações neste artigo.         |
|Recurso de ligação privada    |   O recurso de ligação privada para ligar usando iD ou pseudónimo de recurso, a partir da lista de tipos disponíveis. Será gerado um identificador de rede único para todo o tráfego enviado a este recurso.       |
|Subresource-alvo   |      O subresource para ligar. Cada tipo de recurso de ligação privada tem diferentes opções para selecionar com base na preferência.    |
|Método de aprovação de conexão    |  Automático ou manual. Com base nas permissões de controlo de acesso baseado em funções (RBAC), o seu ponto final privado pode ser aprovado automaticamente. Se tentar ligar-se a um recurso de ligação privada sem RBAC, utilize o método manual para permitir ao proprietário do recurso aprovar a ligação.        |
|Mensagem de Pedido     |  Pode especificar uma mensagem para que as ligações solicitadas sejam aprovadas manualmente. Esta mensagem pode ser usada para identificar um pedido específico.        |
|Estado da ligação   |   Uma propriedade só de leitura que especifica se o ponto final privado está ativo. Apenas os pontos finais privados num estado aprovado podem ser utilizados para o envio de tráfego. Estados adicionais disponíveis: <br>-**Aprovado**: A ligação foi aprovada automaticamente ou manualmente e está pronta a ser utilizada.</br><br>-**Pendente**: A ligação foi criada manualmente e está pendente de aprovação pelo proprietário do recurso de ligação privada.</br><br>-**Rejeitado:** A ligação foi rejeitada pelo proprietário de recursos de ligação privada.</br><br>-**Desligado**: A ligação foi removida pelo proprietário do recurso de ligação privada. O ponto final privado torna-se informativo e deve ser eliminado para limpeza. </br>|

Aqui estão alguns detalhes chave sobre os pontos finais privados: 
- O ponto final privado permite a conectividade entre os consumidores a partir do mesmo VNet, VNets de observação regional, VNets de olhos globais e em instalações usando [VPN](https://azure.microsoft.com/services/vpn-gateway/) ou [Rota Expresso](https://azure.microsoft.com/services/expressroute/) e serviços alimentados por Private Link.
 
- As ligações de rede só podem ser iniciadas por clientes que se ligam ao ponto final privado, os prestadores de serviços não têm qualquer configuração de encaminhamento para iniciar ligações aos consumidores de serviços. As ligações só podem ser indicada numa única direção.

- Ao criar um ponto final privado, também é criada uma interface de rede apenas de leitura para o ciclo de vida do recurso. A interface é atribuída dinamicamente endereços IP privados a partir da sub-rede que mapeia para o recurso de ligação privada. O valor do endereço IP privado permanece inalterado durante todo o ciclo de vida do ponto final privado.
 
- O ponto final privado deve ser implantado na mesma região que a rede virtual. 
 
- O recurso de ligação privada pode ser implantado numa região diferente da rede virtual e do ponto final privado.
 
- Vários pontos finais privados podem ser criados usando o mesmo recurso de ligação privada. Para uma única rede utilizando uma configuração comum do servidor DNS, a prática recomendada é utilizar um único ponto final privado para um dado recurso de ligação privada para evitar entradas ou conflitos duplicados na resolução dns. 
 
- Vários pontos finais privados podem ser criados nas mesmas sub-redes ou diferentes dentro da mesma rede virtual. Existem limites para o número de pontos finais privados que pode criar numa subscrição. Para mais detalhes, consulte [os limites de Azure.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)

- A subscrição do recurso de ligação privada também deve ser registada no fornecedor de recursos Micosoft.Network. Para mais informações, consulte [os Fornecedores de Recursos Azure.](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)

 
## <a name="private-link-resource"></a>Recurso de ligação privada 
Um recurso de ligação privada é o alvo de destino de um determinado ponto final privado. Segue-se uma lista dos tipos de recursos de ligação privada disponíveis: 
 
|Nome de recurso de ligação privada  |Tipo de recurso   |Subreufontes  |
|---------|---------|---------|
|**Serviço de Ligação Privada** (O seu próprio serviço)   |  Microsoft.Network/privateLinkServices       | vazio |
|**Automatização do Azure** |  Microsoft.Automation/automation | Webhook, DSCAndHybridWorker |
|**Base de Dados SQL do Azure** | Microsoft.Sql/servidores    |  Sql Server (SqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servidores    |  Sql Server (SqlServer)        | 
|**Armazenamento do Azure**  | Microsoft.Storage/storageAcontas    |  Blob (bolha, blob_secondary)<BR> Tabela (tabela, table_secondary)<BR> Fila (fila, queue_secondary)<BR> Arquivo (arquivo, file_secondary)<BR> Web (web, web_secondary)        |
|**Azure Data Lake Storage Gen2** (Armazenamento do Azure Data Lake Gen2)  | Microsoft.Storage/storageAcontas    |  Blob (bolha, blob_secondary)<BR> Data Lake File System Gen2 (dfs, dfs_secondary)       |
|**BD do Cosmos para o Azure** | Microsoft.AzureCosmosDB/databaseSacons    | Sql, MongoDB, Cassandra, Gremlin, Mesa|
|**Base de Dados Azure para PostgreSQL -Servidor único** | Microsoft.DBforPostgreSQL/servidores    | postgresqlServer |
|**Base de Dados do Azure para MySQL** | Microsoft.DBforMySQL/servidores    | mysqlServer |
|**Azure Database for MariaDB** | Microsoft.DBforMariaDB/servidores    | mariadbServer |
|**Hub IoT do Azure** | Microsoft.Devices/IotHubs    | IotHub |
|**Azure Key Vault** | Microsoft.KeyVault/cofres    | cofre |
|**Serviço Azure Kubernetes - Kubernetes API** | Microsoft.ContainerService/managedClusters    | gestão |
|**Azure Search** | Microsoft.Search/searchService| searchService|  
|**Azure Container Registry** | Microsoft.ContainerRegistry/registries    | registry |
|**Configuração da Aplicação Azure** | Microsoft.Appconfiguration/configurationStores    | configuraçõesStores |
|**Azure Backup** | Microsoft.RecoveryServices/cofres    | cofre |
|**Hub de Eventos do Azure** | Microsoft.EventHub/espaços de nome    | espaço de nomes |
|**Azure Service Bus** | Microsoft.ServiceBus/namespaces | espaço de nomes |
|**Reencaminhamento do Azure** | Microsoft.Relay/namespaces | espaço de nomes |
|**Azure Event Grid** | Microsoft.EventGrid/tópicos    | tópico |
|**Azure Event Grid** | Microsoft.EventGrid/domínios    | domínio |
|**Serviço de Aplicações do Azure** | Microsoft.Web/sites    | sites |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces    | área de trabalho |
|**SignalR** | Microsoft.SignalrService/Signalr    | sinalR |
|**Azure Monitor** | Microsoft.Insights/privateLinkScopes    | azuremonitor |
|**Serviços Cognitivos** | (Microsoft.CognitiveServices/contas    | conta |
|**Azure File Sync** | Microsoft.StorageSync/storageSyncServices    | Rio Afs |
    
  

  
 
## <a name="network-security-of-private-endpoints"></a>Segurança da rede de pontos finais privados 
Ao utilizar pontos finais privados para serviços Azure, o tráfego é assegurado a um recurso de ligação privada específico. A plataforma realiza um controlo de acesso para validar ligações de rede atingindo apenas o recurso de ligação privada especificado. Para aceder a recursos adicionais dentro do mesmo serviço Azure, são necessários pontos finais privados adicionais. 
 
Pode bloquear completamente as suas cargas de trabalho de aceder a pontos finais públicos para se ligar a um serviço Azure suportado. Este controlo fornece uma camada de segurança adicional da rede aos seus recursos, fornecendo uma proteção de exfiltração incorporada que impede o acesso a outros recursos alojados no mesmo serviço Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Acesso a um recurso de ligação privada utilizando fluxo de trabalho de aprovação 
Pode ligar-se a um recurso de ligação privada utilizando os seguintes métodos de aprovação de ligação:
- **Aprovado automaticamente** quando possui ou tem permissão sobre o recurso de ligação privada específico. A permissão necessária baseia-se no tipo de recurso de ligação privada no seguinte formato: Microsoft. \<Provider> /<resource_type>/privateEndpointConnectionApproval/action
- **Pedido manual** quando não tiver a permissão necessária e gostaria de solicitar acesso. Será iniciado um fluxo de trabalho de aprovação. O ponto final privado e a subsequente ligação do ponto final privado serão criados num estado “Pendente”. O proprietário do recurso de ligação privada é responsável por aprovar a ligação. Depois de aprovado, o ponto final privado está habilitado a enviar o tráfego normalmente, como mostrado no diagrama de trabalho de aprovação seguinte.  

![aprovação do fluxo de trabalho](media/private-endpoint-overview/private-link-paas-workflow.png)
 
O proprietário de recursos de ligação privada pode executar as seguintes ações sobre uma ligação de ponto final privado: 
- Reveja todos os detalhes das ligações de ponto final privado. 
- Aprovar uma ligação privada de ponto final. O ponto final privado correspondente será habilitado a enviar tráfego para o recurso de ligação privada. 
- Rejeite uma ligação privada de ponto final. O respetivo ponto final privado será atualizado para refletir o estado.
- Elimine uma ligação de ponto final privado em qualquer estado. O ponto final privado correspondente será atualizado com um estado desligado para refletir a ação, o proprietário do ponto final privado só pode apagar o recurso neste momento. 
 
> [!NOTE]
> Apenas um ponto final privado num estado aprovado pode enviar tráfego para um determinado recurso de ligação privada. 

### <a name="connecting-using-alias"></a>Ligação usando Alias
O Alias é um nome único que é gerado quando o proprietário do serviço cria o serviço de ligação privada por trás de um balanceador de carga padrão. O proprietário do serviço pode partilhar este Alias com os seus consumidores offline. Os consumidores podem solicitar uma ligação ao serviço de ligação privada utilizando o recurso URI ou o Alias. Se pretender ligar-se utilizando o Alias, tem de criar um ponto final privado utilizando o método de aprovação de ligação manual. Para utilizar o método de aprovação de ligação manual, desaponte o parâmetro de pedido manual para ser verdadeiro durante o ponto final privado, crie fluxo. Veja o [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) e [o ponto de terminação privado da rede Az para obter](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) detalhes. 

## <a name="dns-configuration"></a>Configuração do DNS 
Ao ligar-se a um recurso de ligação privada utilizando um nome de domínio totalmente qualificado (FQDN) como parte da cadeia de ligação, é importante configurar corretamente as definições de DNS para resolver o endereço IP privado atribuído. Os serviços Azure existentes podem já ter uma configuração DNS para utilizar ao ligar em cima de um ponto final público. Isto precisa de ser ultrapassado para se ligar utilizando o seu ponto final privado. 
 
A interface de rede associada ao ponto final privado contém o conjunto completo de informações necessárias para configurar o seu DNS, incluindo endereços IP FQDN e privados atribuídos para um determinado recurso de ligação privada. 

Para obter informações detalhadas completas sobre as melhores práticas e recomendações para configurar o DNS para Private Endpoints, por favor reveja [o artigo de configuração do Private Endpoint DNS](private-endpoint-dns.md).



 
## <a name="limitations"></a>Limitações
 
A tabela a seguir inclui uma lista de limitações conhecidas ao utilizar pontos finais privados: 


|Limitação |Descrição |Mitigação  |
|---------|---------|---------|
|As regras do Grupo de Segurança de Rede (NSG) e as rotas definidas pelo utilizador não se aplicam ao Ponto Final Privado    |O NSG não é suportado em pontos finais privados. Embora as sub-redes que contenham o ponto final privado possam ter NSG associada, as regras não serão eficazes no tráfego processado pelo ponto final privado. Deve ter [políticas de rede desativadas](disable-private-endpoint-network-policy.md) para implantar pontos finais privados numa sub-rede. O NSG ainda é aplicado em outras cargas de trabalho alojoadas na mesma sub-rede. As rotas em qualquer sub-rede de clientes usarão um prefixo /32, alterando o comportamento de encaminhamento padrão requer um UDR semelhante  | Controle o tráfego utilizando as regras NSG para tráfego de saída em clientes de origem. Implementar rotas individuais com prefixo /32 para substituir rotas privadas de ponto final. Os registos do NSG Flow e as informações de monitorização das ligações de saída ainda são suportados e podem ser utilizados        |


## <a name="next-steps"></a>Passos seguintes
- [Criar um ponto de final privado para base de dados SQL utilizando o Portal ](create-private-endpoint-portal.md)
- [Criar um ponto final privado para base de dados SQL utilizando PowerShell ](create-private-endpoint-powershell.md)
- [Criar um ponto final privado para base de dados SQL utilizando CLI ](create-private-endpoint-cli.md)
- [Criar um ponto final privado para conta de armazenamento utilizando o Portal ](create-private-endpoint-storage-portal.md)
- [Criar um ponto final privado para conta Azure Cosmos usando portal ](../cosmos-db/how-to-configure-private-endpoints.md)
- [Crie o seu próprio serviço de Ligação Privada utilizando a Azure PowerShell](create-private-link-service-powershell.md)
- [Crie o seu próprio Link Privado para Base de Dados de Azure para PostgreSQL - Servidor único utilizando o Portal](../postgresql/howto-configure-privatelink-portal.md)
- [Crie o seu próprio Link Privado para Base de Dados de Azure para PostgreSQL - Servidor único utilizando CLI](../postgresql/howto-configure-privatelink-cli.md)
- [Crie o seu próprio Link Privado para Base de Dados Azure para o MySQL utilizando o Portal](../mysql/howto-configure-privatelink-portal.md)
- [Crie o seu próprio Link Privado para Base de Dados Azure para o MySQL utilizando o CLI](../mysql/howto-configure-privatelink-cli.md)
- [Crie o seu próprio Link Privado para Base de Dados Azure para MariaDB usando o Portal](../mariadb/howto-configure-privatelink-portal.md)
- [Crie o seu próprio Link Privado para Base de Dados Azure para MariaDB utilizando o CLI](../mariadb/howto-configure-privatelink-cli.md)
