---
title: O que é um Ponto Final Privado Azure?
description: Saiba mais sobre o Azure Private Endpoint
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 51afa118be75c7e9ea2cb6e394d27cc39a58de0b
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849659"
---
# <a name="what-is-azure-private-endpoint"></a>O que é Azure Private Endpoint?

O Ponto Final Privado do Azure é uma interface de rede que o liga a um serviço de forma privada e segura com a tecnologia Azure Private Link. O Private Endpoint utiliza um endereço IP privado do seu VNet, efetivamente trazendo o serviço para o seu VNet. O serviço poderia ser um serviço Azure como Azure Storage, Azure Cosmos DB, SQL, etc. ou o seu próprio [Serviço de Ligação Privada.](private-link-service-overview.md)
  
## <a name="private-endpoint-properties"></a>Propriedades private endpoint 
 Um Ponto Final Privado especifica as seguintes propriedades: 


|Propriedade  |Descrição |
|---------|---------|
|Name    |    Um nome único dentro do grupo de recursos.      |
|Subrede    |  A subnet para implantar e alocar endereços IP privados a partir de uma rede virtual. Para obter requisitos de sub-rede, consulte a secção Limitações neste artigo.         |
|Recurso de ligação privada    |   O recurso de ligação privada para ligar usando identificação ou pseudónimo de recursos, a partir da lista de tipos disponíveis. Será gerado um identificador de rede único para todo o tráfego enviado para este recurso.       |
|Subrecurso-alvo   |      O subrecurso para ligar. Cada tipo de recurso de ligação privada tem diferentes opções para selecionar com base na preferência.    |
|Método de aprovação de ligação    |  Automático ou manual. Com base em permissões de controlo de acesso baseadas em funções (RBAC), o seu ponto final privado pode ser aprovado automaticamente. Se tentar ligar-se a um recurso de ligação privada sem RBAC, utilize o método manual para permitir ao proprietário do recurso aprovar a ligação.        |
|Mensagem de Pedido     |  Pode especificar uma mensagem para que as ligações solicitadas sejam aprovadas manualmente. Esta mensagem pode ser usada para identificar um pedido específico.        |
|Estado da ligação   |   Uma propriedade só para leitura que especifica se o ponto final privado está ativo. Apenas pontos finais privados num estado aprovado podem ser usados para enviar tráfego. Estados adicionais disponíveis: <br>-**Aprovado**: A ligação foi aprovada automaticamente ou manualmente e está pronta a ser utilizada.</br><br>-**Pendente**: A ligação foi criada manualmente e está pendente de aprovação pelo proprietário de recursos de ligação privada.</br><br>-**Rejeitado**: A ligação foi rejeitada pelo proprietário de recursos de ligação privada.</br><br>-**Desligado**: A ligação foi removida pelo proprietário do recurso de ligação privada. O ponto final privado torna-se informativo e deve ser eliminado para limpeza. </br>|

Aqui estão alguns detalhes chave sobre pontos finais privados: 
- O ponto final privado permite a conectividade entre os consumidores do mesmo VNet, VNets regionalmente peered, VNets globalmente e em instalações usando [VPN](https://azure.microsoft.com/services/vpn-gateway/) ou [Rota Expresso](https://azure.microsoft.com/services/expressroute/) e serviços alimentados por Private Link.
 
- As ligações de rede só podem ser iniciadas por clientes que se ligam ao ponto final privado, os prestadores de serviços não têm qualquer configuração de encaminhamento para iniciar ligações aos consumidores de serviços. As ligações só podem ser estabelecidos numa única direção.

- Ao criar um ponto final privado, é também criada uma interface de rede apenas para leitura para o ciclo de vida do recurso. A interface é atribuída dinamicamente endereços IP privados da subnet que mapeia para o recurso de ligação privada. o valor do endereço IP privado permanece inalterado para todo o ciclo de vida do ponto final privado.
 
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
|**Armazenamento Azure**  | Microsoft.Storage/storageAccounts    |  Blob (bolha, blob_secondary)<BR> Tabela (mesa, table_secondary)<BR> Fila (fila, queue_secondary)<BR> Arquivo (arquivo, file_secondary)<BR> Web (web, web_secondary)        |
|**Azure Data Lake Storage Gen2** (Armazenamento do Azure Data Lake Gen2)  | Microsoft.Storage/storageAccounts    |  Blob (bolha, blob_secondary)<BR> Data Lake File System Gen2 (dfs, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts    | Sql, MongoDB, Cassandra, Gremlin, Mesa|
|**Base de Dados Azure para PostgreSQL -Servidor único** | Microsoft.DBforPostgreSQL/servidores    | postgresqlServer |
|**Base de Dados do Azure para MySQL** | Microsoft.DBforMySQL/servidores    | mysqlServer |
|**Azure Database for MariaDB** | Microsoft.DBforMariaDB/servidores    | mariadbServer |
|**Azure IoT Hub** | Microsoft.Devices/IotHubs    | iotHub |
|**Azure Key Vault** | Microsoft.KeyVault/cofres    | cofre |
|**Serviço Azure Kubernetes - Kubernetes API** | Microsoft.ContainerService/managedClusters    | managedCluster |
|**Azure Search** | Microsoft.Search/searchService| pesquisaServiço|  
|**Registo de Contentores do Azure** | Microsoft.ContainerRegistry/registos    | registry |
|**Configuração da Aplicação Azure** | Microsoft.Appconfiguration/configuraçãoStores    | configuraçãoLoja |
|**Azure Backup** | Microsoft.RecoveryServices/cofres    | cofre |
|**Hub de Eventos do Azure** | Microsoft.EventHub/espaços de nome    | espaço de nomes |
|**Service Bus do Azure** | Microsoft.ServiceBus/espaços de nome | espaço de nomes |
|**Reencaminhamento do Azure** | Microsoft.Relay/namespaces | espaço de nomes |
|**Azure Event Grid** | Microsoft.EventGrid/tópicos    | tópico |
|**Azure Event Grid** | Microsoft.EventGrid/domínios    | domínio |
|**Azure WebApps** | Microsoft.Web/sites    | site |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/espaços de trabalho    | área de trabalho |
  
 
## <a name="network-security-of-private-endpoints"></a>Segurança da rede de pontos finais privados 
Ao utilizar pontos finais privados para serviços Azure, o tráfego é garantido a um recurso de ligação privada específico. A plataforma executa um controlo de acesso para validar as ligações de rede atingindo apenas o recurso de ligação privada especificado. Para aceder a recursos adicionais dentro do mesmo serviço Azure, são necessários pontos finais privados adicionais. 
 
Pode bloquear completamente as suas cargas de trabalho desde o acesso a pontos finais públicos para se ligar a um serviço Azure suportado. Este controlo fornece uma camada adicional de segurança de rede aos seus recursos, fornecendo uma proteção de exfiltração incorporada que impede o acesso a outros recursos alojados no mesmo serviço Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Acesso a um recurso de ligação privada utilizando fluxo de trabalho de aprovação 
Pode ligar-se a um recurso de ligação privada utilizando os seguintes métodos de aprovação de ligação:
- **Aprovado automaticamente** quando possui ou tem permissão no recurso de ligação privada específico. A permissão necessária baseia-se no tipo de recurso de ligação privada no seguinte formato: Microsoft. \< Fornecedor>/<resource_type>/privateEndpointConnectionApproval/action
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

Para obter informações detalhadas sobre as melhores práticas e recomendações para configurar DNS para pontos finais privados, por favor, reveja o artigo de [configuração do DNS private Endpoint](private-endpoint-dns.md).



 
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
