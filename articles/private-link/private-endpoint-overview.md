---
title: O que é um ponto de extremidade privado do Azure?
description: Saiba mais sobre o ponto de extremidade privado do Azure
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 031055dce66361cc128ed42a4d0c942ccb5a3b82
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71017974"
---
# <a name="what-is-azure-private-endpoint"></a>O que é o ponto de extremidade privado do Azure?

O ponto de extremidade privado do Azure é uma interface de rede que conecta você de forma privada e segura a um serviço da plataforma Azure link privado. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente o serviço em sua VNet. O serviço pode ser um serviço do Azure, como o armazenamento do Azure, SQL, etc. ou seu próprio [serviço de link privado](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Propriedades do ponto de extremidade privado 
 Um ponto de extremidade privado especifica as seguintes propriedades: 


|Propriedade  |Description |
|---------|---------|
|Name    |    Um nome exclusivo dentro do grupo de recursos.      |
|Subnet    |  A sub-rede para implantar e alocar endereços IP privados de uma rede virtual. Para obter os requisitos de sub-rede, consulte a seção limitações neste artigo.         |
|Recurso de link privado    |   O recurso de link privado para se conectar usando a ID de recurso ou alias da lista de tipos disponíveis. Um identificador de rede exclusivo será gerado para todo o tráfego enviado para esse recurso.       |
|Subrecurso de destino   |      O subrecurso a ser conectado. Cada tipo de recurso de link privado tem opções diferentes para selecionar com base na preferência.    |
|Método de aprovação de conexão    |  Automático ou manual. Com base nas permissões de RBAC (controle de acesso baseado em função), seu ponto de extremidade privado pode ser aprovado automaticamente. Se você tentar se conectar a um recurso de link privado sem RBAC, use o método manual para permitir que o proprietário do recurso aprove a conexão.        |
|Mensagem de solicitação     |  Você pode especificar uma mensagem para que as conexões solicitadas sejam aprovadas manualmente. Essa mensagem pode ser usada para identificar uma solicitação específica.        |
|Estado da ligação   |   Uma propriedade somente leitura que especifica se o ponto de extremidade privado está ativo. Somente pontos de extremidade privados em um Estado aprovado podem ser usados para enviar tráfego. Outros Estados disponíveis: <br>-**Aprovado**: A conexão foi aprovada automaticamente ou manualmente e está pronta para ser usada.</br><br>-**Pendente**: A conexão foi criada manualmente e está aguardando a aprovação do proprietário do recurso de link privado.</br><br>-**Rejeitado**: A conexão foi rejeitada pelo proprietário do recurso de link privado.</br><br>-**Desconectado**: A conexão foi removida pelo proprietário do recurso de link privado. O ponto de extremidade privado se torna informativo e deve ser excluído para limpeza. </br>|

Aqui estão alguns detalhes importantes sobre pontos de extremidade privados: 
- O ponto de extremidade privado permite a conectividade entre os consumidores da mesma VNet, VNets emparelhadas de modo global e no local usando [VPN](https://azure.microsoft.com/services/vpn-gateway/) ou [rota expressa](https://azure.microsoft.com/services/expressroute/) e serviços fornecidos por um link privado.
 
- Ao criar um ponto de extremidade privado, uma interface de rede também é criada para o ciclo de vida do recurso. A interface recebe um endereço IP privado da sub-rede que mapeia para o serviço de vínculo privado.
 
- O ponto de extremidade privado deve ser implantado na mesma região que a rede virtual. 
 
- O recurso de link privado pode ser implantado em uma região diferente da rede virtual e do ponto de extremidade privado.
 
- Vários pontos de extremidade privados podem ser criados usando o mesmo recurso de link privado. Para uma única rede usando uma configuração de servidor DNS comum, a prática recomendada é usar um único ponto de extremidade privado para um determinado recurso de link privado para evitar entradas duplicadas ou conflitos na resolução de DNS. 
 
- Vários pontos de extremidade privados podem ser criados nas mesmas ou em sub-redes diferentes na mesma rede virtual. Há limites para o número de pontos de extremidade privados que você pode criar em uma assinatura. Para obter detalhes, consulte [limites do Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits.md#networking-limits).


 
## <a name="private-link-resource"></a>Recurso de link privado 
Um recurso de link privado é o destino de destino de um determinado ponto de extremidade particular. A seguir está uma lista de tipos de recursos de link privado disponíveis: 
 
|Nome do recurso do link privado  |Tipo de recurso   |Sub-recursos  |
|---------|---------|---------|
|**Serviço de vínculo privado** (Seu próprio serviço)   |  Microsoft. Network/privateLinkServices       | vazio |
|**Base de Dados SQL do Azure** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        |
|**Azure SQL Data Warehouse** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        |
|**Armazenamento do Azure**  | (Microsoft. Storage/storageAccounts)    |  BLOB (BLOB, blob_secondary)<BR> Tabela (tabela, table_secondary)<BR> Fila (fila, queue_secondary)<BR> Arquivo (arquivo, file_secondary)<BR> Web (Web, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | (Microsoft. Storage/storageAccounts)    |  BLOB (BLOB, blob_secondary)       |
 
 
## <a name="network-security-of-private-endpoints"></a>Segurança de rede de pontos de extremidade privados 
Ao usar pontos de extremidade privados para serviços do Azure, o tráfego é protegido para um recurso de link particular específico. A plataforma executa um controle de acesso para validar conexões de rede que atingem apenas o recurso de link particular especificado. Para acessar recursos adicionais dentro do mesmo serviço do Azure, são necessários pontos de extremidade privados adicionais. 
 
Você pode bloquear completamente suas cargas de trabalho de acessar pontos de extremidade públicos para se conectar a um serviço do Azure com suporte. Esse controle fornece uma camada de segurança de rede adicional para seus recursos, fornecendo uma proteção interna do vazamento que impede o acesso a outros recursos hospedados no mesmo serviço do Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Acesso a um recurso de link privado usando o fluxo de trabalho de aprovação 
Você pode se conectar a um recurso de link privado usando os seguintes métodos de aprovação de conexão:
- Aprovado **automaticamente** quando você possui ou tem permissão no recurso de link particular específico. A permissão necessária é baseada no tipo de recurso de link privado no seguinte formato: O. \<> Do provedor/< resource_type >/privateEndpointConnectionApproval/Action
- Solicitação **manual** quando você não tem a permissão necessária e deseja solicitar acesso. Um fluxo de trabalho de aprovação será iniciado. O ponto de extremidade privado e a conexão de ponto de extremidade particular subsequente serão criados em um estado "pendente". O proprietário do recurso de link privado é responsável por aprovar a conexão. Depois de aprovado, o ponto de extremidade privado é habilitado para enviar o tráfego normalmente, conforme mostrado no diagrama de fluxo de trabalho de aprovação a seguir.  

![aprovação do fluxo de trabalho](media/private-endpoint-overview/private-link-paas-workflow.png)
 
O proprietário do recurso de link privado pode executar as seguintes ações em uma conexão de ponto de extremidade particular: 
- Examine todos os detalhes de conexões do ponto de extremidade privado. 
- Aprove uma conexão de ponto de extremidade particular. O ponto de extremidade privado correspondente será habilitado para enviar o tráfego para o recurso de link privado. 
- Rejeite uma conexão de ponto de extremidade privada. O ponto de extremidade privado correspondente será atualizado para refletir o status.
- Exclua uma conexão de ponto de extremidade particular em qualquer Estado. O ponto de extremidade privado correspondente será atualizado com um estado desconectado para refletir a ação, o proprietário do ponto de extremidade privado só poderá excluir o recurso neste ponto. 
 
> [!NOTE]
> Somente um ponto de extremidade privado em um Estado aprovado pode enviar tráfego para um determinado recurso de link privado. 

### <a name="connecting-using-alias"></a>Conectando usando alias
Alias é um moniker exclusivo gerado quando o proprietário do serviço cria o serviço de vínculo privado por trás de um balanceador de carga padrão. O proprietário do serviço pode compartilhar esse alias com seus consumidores offline. Os consumidores podem solicitar uma conexão com o serviço de vínculo privado usando o URI do recurso ou o alias. Se você quiser se conectar usando o alias, deverá criar um ponto de extremidade privado usando o método de aprovação de conexão manual. Para usar o método de aprovação de conexão manual, defina o parâmetro de solicitação manual como true durante o fluxo de criação do ponto de extremidade privado. Examine [New-AzPrivateEndpoint](https://docs.microsoft.com/en-us/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) e [AZ Network Private-Endpoint Create](https://docs.microsoft.com/en-us/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) para obter detalhes. 

## <a name="dns-configuration"></a>Configuração do DNS 
Ao se conectar a um recurso de link privado usando um FQDN (nome de domínio totalmente qualificado) como parte da cadeia de conexão, é importante definir corretamente as configurações de DNS para resolver o endereço IP privado alocado. Os serviços do Azure existentes já podem ter uma configuração de DNS para usar ao se conectar por meio de um ponto de extremidade público. Isso precisa ser substituído para se conectar usando seu ponto de extremidade privado. 
 
O adaptador de rede associado ao ponto de extremidade privado contém o conjunto completo de informações necessárias para configurar o DNS, incluindo endereços IP privados e FQDN alocados para um determinado recurso de link privado. 
 
Você pode usar as seguintes opções para definir as configurações de DNS para pontos de extremidade privados: 
- **Use o arquivo de host (recomendado apenas para teste)** . Você pode usar o arquivo de host em uma máquina virtual para substituir o DNS.  
- **Use uma zona DNS privada**. Você pode usar zonas DNS privadas para substituir a resolução DNS para um determinado ponto de extremidade particular. Uma zona DNS privada pode ser vinculada à sua rede virtual para resolver domínios específicos.
- **Use seu servidor DNS personalizado**. Você pode usar seu próprio servidor DNS para substituir a resolução DNS para um determinado recurso de link privado. Se o servidor DNS estiver hospedado em uma rede virtual, você poderá criar uma regra de encaminhamento de DNS para usar uma zona DNS privada para simplificar a configuração de todos os recursos de link privado.
 
> [!IMPORTANT]
> Não é recomendável substituir uma zona que esteja ativamente em uso para resolver pontos de extremidade públicos. As conexões com recursos não poderão ser resolvidas corretamente sem o encaminhamento de DNS para o DNS público. Para evitar problemas, crie um nome de domínio diferente ou siga o nome sugerido para cada serviço abaixo. 
 
Para os serviços do Azure, use os nomes de zona recomendados, conforme descrito na tabela a seguir:

|Tipo de recurso de link privado   |Subrecursos  |Nome da zona  |
|---------|---------|---------|
|Banco de BD SQL/DW (Microsoft. SQL/Servers)    |  SQL Server (sqlServer)        |   privatelink.database.windows.net       |
|Conta de armazenamento (Microsoft. Storage/storageAccounts)    |  BLOB (BLOB, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Conta de armazenamento (Microsoft. Storage/storageAccounts)    |    Tabela (tabela, table_secondary)      |   privatelink.table.core.windows.net       |
|Conta de armazenamento (Microsoft. Storage/storageAccounts)    |    Fila (fila, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Conta de armazenamento (Microsoft. Storage/storageAccounts)   |    Arquivo (arquivo, file_secondary)      |    privatelink.file.core.windows.net      |
|Conta de armazenamento (Microsoft. Storage/storageAccounts)     |  Web (Web, web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake sistema de arquivos Gen2 (Microsoft. Storage/storageAccounts)  |  Data Lake sistema de arquivos Gen2 (DFS, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
||||
 

O Azure criará um registro DNS de nome canônico (CNAME) no DNS público para redirecionar a resolução para os nomes de domínio sugeridos. Você poderá substituir a resolução pelo endereço IP privado dos seus pontos de extremidade privados. 
 
Seus aplicativos não precisam alterar a URL de conexão. Ao tentar resolver usando um DNS público, o servidor DNS agora será resolvido para seus pontos de extremidade privados. O processo não afeta seus aplicativos. 
 
## <a name="limitations"></a>Limitações
 
A tabela a seguir inclui uma lista de limitações conhecidas ao usar pontos de extremidade privados: 


|Limitação |Descrição |Mitigação  |
|---------|---------|---------|
|As regras do NSG (grupo de segurança de rede) não se aplicam ao ponto de extremidade privado    |Não há suporte para NSG em pontos de extremidade privados. Embora as sub-redes que contenham o ponto de extremidade privado possam ter NSG associado a ela, as regras não serão efetivas no tráfego processado pelo ponto de extremidade privado. Você deve ter [imposição de políticas de rede desabilitada](disable-private-endpoint-network-policy.md) para implantar pontos de extremidade privados em uma sub-rede. O NSG ainda é imposto em outras cargas de trabalho hospedadas na mesma sub-rede.   | Controle o tráfego usando regras de NSG para o tráfego de saída em clientes de origem.        |
|Os pontos de extremidade privados não podem ser criados em sub-redes habilitadas para ponto de extremidades de serviço ou cargas de trabalho especializadas    |Pontos de extremidade privados não podem ser implantados em sub-redes habilitadas para pontos de extremidade de serviço ou sub-redes delegadas a cargas de trabalho especializadas|  Crie uma sub-rede separada para implantar os pontos de extremidade privados.        |
|o ponto de extremidade privado só pode ser mapeado para o serviço de vínculo privado (pertencente ao cliente) na mesma região    |   Não há suporte para a conexão a um serviço de vínculo privado (seu próprio) de uma região diferente       |  Durante a visualização, você deve implantar seu serviço de vínculo privado na mesma região.        |
|Cargas de trabalho especializadas não podem acessar pontos de extremidade privados    |   Os seguintes serviços implantados em sua rede virtual não podem acessar nenhum recurso de link privado usando pontos de extremidade privados:<br>Plano de Serviço de Aplicações</br>Instância de Contentor do Azure</br>Ficheiros NetApp do Azure</br>HSM Dedicado do Azure<br>       |   Sem mitigação durante a visualização.       |
|  O portal não oferece suporte à criação de pontos de extremidade privados usando alias  |   O portal permite apenas a criação de pontos de extremidade privados usando o URI de recurso      | Usar URI de recurso para solicitar conexões de ponto de extremidade particulares        |

## <a name="next-steps"></a>Passos seguintes
- [Criar um ponto de extremidade privado para o servidor de banco de dados SQL usando o portal](create-private-endpoint-portal.md)
- [Criar um ponto de extremidade privado para o servidor de banco de dados SQL usando o PowerShell](create-private-endpoint-powershell.md)
- [Criar um ponto de extremidade privado para o servidor de banco de dados SQL usando a CLI](create-private-endpoint-cli.md)
- [Criar um ponto de extremidade privado para a conta de armazenamento usando o portal](create-private-endpoint-storage-portal.md)
- [Crie seu próprio serviço de vínculo privado usando Azure PowerShell](create-private-link-service-powershell.md)
