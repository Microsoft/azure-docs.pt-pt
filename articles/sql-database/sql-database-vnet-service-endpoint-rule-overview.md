---
title: Pontos de extremidade de VNet e regras para bancos de dados individuais e em pool no SQL do Azure | Microsoft Docs
description: Marque uma sub-rede como um ponto de extremidade de serviço de rede virtual. Em seguida, o ponto de extremidade como uma regra de rede virtual para a ACL do seu banco de dados SQL do Azure. Em seguida, o banco de dados SQL aceita a comunicação de todas as máquinas virtuais e de outros nós na sub-rede.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 03/12/2019
ms.openlocfilehash: 9b28a8efcc09954d9046ad1dda3ba5f10f45bdfa
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840470"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>Usar pontos de extremidade de serviço de rede virtual e regras para servidores de banco de dados

*As regras de rede virtual* são um recurso de segurança de firewall que controla se o servidor de banco de dados para seus bancos de dados individuais e pool elástico no [banco de dados SQL](sql-database-technical-overview.md) do Azure ou em seus bancos no [SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) aceita comunicações que são enviadas de sub-redes específicas em redes virtuais. Este artigo explica por que o recurso de regra de rede virtual às vezes é a melhor opção para permitir a comunicação com segurança com o banco de dados SQL do Azure e SQL Data Warehouse.

> [!IMPORTANT]
> Este artigo aplica-se ao SQL Server do Azure e ao banco de dados SQL e SQL Data Warehouse bancos que são criados no SQL Server do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse. Este artigo não se aplica a uma implantação de **instância gerenciada** no banco de dados SQL do Azure porque não tem um ponto de extremidade de serviço associado a ela.

Para criar uma regra de rede virtual, primeiro deve haver um [ponto de extremidade de serviço de rede virtual][vm-virtual-network-service-endpoints-overview-649d] para a regra referenciar.

## <a name="how-to-create-a-virtual-network-rule"></a>Como criar uma regra de rede virtual

Se você criar apenas uma regra de rede virtual, poderá pular para as etapas e explicação [mais adiante neste artigo](#anchor-how-to-by-using-firewall-portal-59j).

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologia e descrição

**Rede virtual:** Você pode ter redes virtuais associadas à sua assinatura do Azure.

**Redes** Uma rede virtual contém **sub-redes**. Todas as VMs (máquinas virtuais) do Azure que você tem são atribuídas a sub-redes. Uma sub-rede pode conter várias VMs ou outros nós de computação. Os nós de computação que estão fora da sua rede virtual não podem acessar sua rede virtual, a menos que você configure sua segurança para permitir o acesso.

**Ponto de extremidade de serviço de rede virtual:** Um [ponto de extremidade de serviço de rede virtual][vm-virtual-network-service-endpoints-overview-649d] é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais de tipo de serviço do Azure. Neste artigo, estamos interessados no nome do tipo do **Microsoft. SQL**, que se refere ao serviço do Azure chamado banco de dados SQL.

**Regra de rede virtual:** Uma regra de rede virtual para o servidor do banco de dados SQL é uma sub-rede listada na lista de controle de acesso (ACL) do seu servidor do banco de dados SQL. Para estar na ACL do seu banco de dados SQL, a sub-rede deve conter o nome do tipo **Microsoft. SQL** .

Uma regra de rede virtual instrui o servidor do banco de dados SQL a aceitar comunicações de cada nó que está na sub-rede.

<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Benefícios de uma regra de rede virtual

Até que você execute uma ação, as VMs em suas sub-redes não podem se comunicar com o banco de dados SQL. Uma ação que estabelece a comunicação é a criação de uma regra de rede virtual. A lógica para escolher a abordagem de regra de VNet requer uma discussão de comparação e contraste envolvendo as opções de segurança concorrentes oferecidas pelo firewall.

### <a name="a-allow-access-to-azure-services"></a>R. Permitir acesso aos serviços do Azure

O painel firewall tem um botão **ligar/desligar** que é rotulado como **permitir o acesso aos serviços do Azure**. A configuração **on** permite a comunicação de todos os endereços IP do Azure e de todas as sub-redes do Azure. Esses IPs ou sub-redes do Azure podem não ser de propriedade de você. Essa configuração **ativa** provavelmente é mais aberta do que você deseja que o banco de dados SQL seja. O recurso de regra de rede virtual oferece um controle granular muito mais detalhado.

### <a name="b-ip-rules"></a>B. Regras de IP

O Firewall do banco de dados SQL permite especificar intervalos de endereços IP dos quais as comunicações são aceitas no banco de dados SQL. Essa abordagem é adequada para endereços IP estáveis que estão fora da rede privada do Azure. Mas muitos nós dentro da rede privada do Azure são configurados com endereços IP *dinâmicos* . Os endereços IP dinâmicos podem ser alterados, como quando a VM é reiniciada. Seria ilusório especificar um endereço IP dinâmico em uma regra de firewall em um ambiente de produção.

Você pode recuperar a opção de IP obtendo um endereço IP *estático* para sua VM. Para obter detalhes, consulte [configurar endereços IP privados para uma máquina virtual usando o portal do Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

No entanto, a abordagem de IP estático pode se tornar difícil de gerenciar e é dispendiosa quando feita em escala. As regras de rede virtual são mais fáceis de estabelecer e gerenciar.

> [!NOTE]
> Você ainda não pode ter o banco de dados SQL em uma sub-rede. Se o servidor do banco de dados SQL do Azure era um nó em uma sub-rede em sua rede virtual, todos os nós na rede virtual podem se comunicar com o banco de dados SQL. Nesse caso, suas VMs podem se comunicar com o banco de dados SQL sem a necessidade de nenhuma regra de rede virtual ou regras de IP.

No entanto, a partir de setembro de 2017, o serviço de banco de dados SQL do Azure ainda não está entre os serviços que podem ser atribuídos a uma sub-rede.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Detalhes sobre regras de rede virtual

Esta seção descreve vários detalhes sobre as regras de rede virtual.

### <a name="only-one-geographic-region"></a>Apenas uma região geográfica

Cada ponto de extremidade de serviço de rede virtual aplica-se a apenas uma região do Azure. O ponto de extremidade não permite que outras regiões aceitem comunicação da sub-rede.

Qualquer regra de rede virtual é limitada à região à qual seu ponto de extremidade subjacente se aplica.

### <a name="server-level-not-database-level"></a>Nível de servidor, não nível de banco de dados

Cada regra de rede virtual se aplica a todo o servidor de banco de dados SQL do Azure, não apenas a um banco de dados específico no servidor. Em outras palavras, a regra de rede virtual se aplica no nível do servidor, não no nível do banco de dados.

- Por outro lado, as regras de IP podem ser aplicadas em qualquer nível.

### <a name="security-administration-roles"></a>Funções de administração de segurança

Há uma separação de funções de segurança na administração de pontos de extremidade de serviço de rede virtual. A ação é necessária de cada uma das seguintes funções:

- **Administrador de rede:** &nbsp;Ative o ponto de extremidade.
- **Administrador do banco de dados:** &nbsp;Atualize a lista de controle de acesso (ACL) para adicionar a sub-rede fornecida ao servidor do banco de dados SQL.

*Alternativa de RBAC:*

As funções de administrador de rede e administrador de banco de dados têm mais recursos do que o necessário para gerenciar regras de rede virtual. Apenas um subconjunto de seus recursos é necessário.

Você tem a opção de usar o [controle de acesso baseado em função (RBAC)][rbac-what-is-813s] no Azure para criar uma única função personalizada que tem apenas o subconjunto necessário de recursos. A função personalizada pode ser usada em vez de envolver o administrador de rede ou o administrador de banco de dados. A área da superfície de sua exposição de segurança será menor se você adicionar um usuário a uma função personalizada, em vez de adicionar o usuário às outras duas principais funções de administrador.

> [!NOTE]
> Em alguns casos, o banco de dados SQL do Azure e a sub-rede VNet estão em assinaturas diferentes. Nesses casos, você deve garantir as seguintes configurações:
> - Ambas as assinaturas devem estar no mesmo locatário Azure Active Directory.
> - O usuário tem as permissões necessárias para iniciar operações, como habilitar pontos de extremidade de serviço e adicionar uma sub-rede de rede virtual ao servidor especificado.
> - Ambas as assinaturas devem ter o provedor Microsoft. SQL registrado.

## <a name="limitations"></a>Limitações

Para o banco de dados SQL do Azure, o recurso de regras de rede virtual tem as seguintes limitações:

- Um aplicativo Web pode ser mapeado para um IP privado em uma VNet/sub-rede. Mesmo que os pontos de extremidade de serviço sejam ativados da VNet/sub-rede determinada, as conexões do aplicativo Web para o servidor terão uma origem de IP público do Azure, não uma origem de VNet/sub-rede. Para habilitar a conectividade de um aplicativo Web para um servidor que tem regras de firewall de VNet, você deve **permitir que os serviços do Azure acessem o servidor** no servidor.

- No firewall do banco de dados SQL, cada regra de rede virtual faz referência a uma sub-rede. Todas essas sub-redes referenciadas devem ser hospedadas na mesma região geográfica que hospeda o banco de dados SQL.

- Cada servidor de banco de dados SQL do Azure pode ter até 128 entradas de ACL para qualquer rede virtual específica.

- As regras de rede virtual se aplicam somente a redes virtuais Azure Resource Manager; e não para redes de [modelo de implantação clássica][arm-deployment-model-568f] .

- Ativar pontos de extremidade de serviço de rede virtual para o banco de dados SQL do Azure também habilita os pontos de extremidade para os serviços MySQL e PostgreSQL do Azure. No entanto, com os pontos de extremidade ATIVAdos, as tentativas de conexão dos pontos de extremidade com as instâncias do MySQL ou PostgreSQL podem falhar.
  - O motivo subjacente é que o MySQL e PostgreSQL provavelmente não têm uma regra de rede virtual configurada. Você deve configurar uma regra de rede virtual para o banco de dados do Azure para MySQL e PostgreSQL e a conexão terá sucesso.

- No firewall, os intervalos de endereços IP se aplicam aos seguintes itens de rede, mas as regras de rede virtual não:
  - [VPN (rede virtual privada) site a site (S2S)][vpn-gateway-indexmd-608y]
  - Local via [ExpressRoute][expressroute-indexmd-744v]

### <a name="considerations-when-using-service-endpoints"></a>Considerações ao usar pontos de extremidade de serviço

Ao usar pontos de extremidade de serviço para o banco de dados SQL do Azure, examine as seguintes considerações:

- **São necessários IPS públicos de saída para o banco de dados SQL do Azure**: Os NSGs (grupos de segurança de rede) devem ser abertos para IPs do banco de dados SQL do Azure para permitir a conectividade. Você pode fazer isso usando marcas de [serviço](../virtual-network/security-overview.md#service-tags) NSG para o banco de dados SQL do Azure.

### <a name="expressroute"></a>ExpressRoute

Se você estiver usando o [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de seu local, para emparelhamento público ou emparelhamento da Microsoft, será necessário identificar os endereços IP de NAT que são usados. Para peering público, cada circuito ExpressRoute, por predefinição, utiliza dois endereços IP NAT que são aplicados ao tráfego de serviço do Azure quando o tráfego entra no backbone de rede do Microsoft Azure. Para peering da Microsoft, o(s) endereço(s) IP NAT que são utilizados são fornecidos pelo cliente ou são fornecidos pelo fornecedor de serviços. Para permitir o acesso aos recursos de serviço, tem de permitir estes endereços IP públicos na definição da firewall do IP dos recursos. Para localizar os endereços IP do circuito ExpressRoute de peering público, [abra um pedido de suporte no ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) através do portal do Azure. Saiba mais sobre [NAT para peering público e da Microsoft do ExpressRoute.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
Para permitir a comunicação do seu circuito com o banco de dados SQL do Azure, você deve criar regras de rede IP para os endereços IP públicos de seu NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-azure-services-to-access-server"></a>Impacto da remoção de ' permitir que os serviços do Azure acessem o servidor '

Muitos usuários desejam remover os **Serviços do Azure para acessar o servidor** de seus servidores SQL do Azure e substituí-lo por uma regra de firewall de VNet.
No entanto, remover isso afeta os seguintes recursos:

### <a name="import-export-service"></a>Importar serviço de exportação

O serviço de exportação de importação do banco de dados SQL do Azure é executado em VMs no Azure. Essas VMs não estão em sua VNet e, portanto, obtêm um IP do Azure ao se conectarem ao banco de dados. Ao remover **permitir que os serviços do Azure acessem o servidor** , essas VMs não poderão acessar seus bancos de dados.
Você pode contornar o problema. Execute a importação ou exportação de BACPAC diretamente em seu código usando a API DACFx. Verifique se isso está implantado em uma VM que está na sub-rede VNet para a qual você definiu a regra de firewall.

### <a name="sql-database-query-editor"></a>Editor de consultas do banco de dados SQL

O editor de consultas do banco de dados SQL do Azure é implantado em VMs no Azure. Essas VMs não estão em sua VNet. Portanto, as VMs obtêm um IP do Azure ao se conectarem ao seu banco de dados. Ao remover **permitir que os serviços do Azure acessem o servidor**, essas VMs não poderão acessar seus bancos de dados.

### <a name="table-auditing"></a>Auditoria de tabela

No momento, há duas maneiras de habilitar a auditoria no banco de dados SQL. A auditoria de tabela falha depois que você habilita pontos de extremidade de serviço no SQL Server do Azure. A mitigação aqui é mudar para a auditoria de BLOB.

### <a name="impact-on-data-sync"></a>Impacto na sincronização de dados

O banco de dados SQL do Azure tem o recurso de sincronização de data que se conecta aos seus bancos de dado usando IPs do Azure. Ao usar pontos de extremidade de serviço, é provável que você desative **a opção permitir que os serviços do Azure acessem** o acesso do servidor ao servidor do banco de dados SQL. Isso interromperá o recurso de sincronização de dados.

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Impacto do uso de pontos de extremidade de serviço de VNet com o armazenamento do Azure

O armazenamento do Azure implementou o mesmo recurso que permite limitar a conectividade à sua conta de armazenamento do Azure. Se você optar por usar esse recurso com uma conta de armazenamento do Azure que está sendo usada pelo SQL Server do Azure, poderá encontrar problemas. Veja a seguir uma lista e uma discussão sobre os recursos do banco de dados SQL do Azure e do Azure SQL Data Warehouse que são afetados por isso.

### <a name="azure-sql-data-warehouse-polybase"></a>Polybase do Azure SQL Data Warehouse

O polybase é comumente usado para carregar dados no Azure SQL Data Warehouse de contas de armazenamento do Azure. Se a conta de armazenamento do Azure para a qual você está carregando dados limita o acesso somente a um conjunto de sub-redes de VNet, a conectividade do polybase com a conta será interrompida. Para habilitar os cenários de importação e exportação do polybase com o Azure SQL Data Warehouse se conectando ao armazenamento do Azure que é protegido para VNet, siga as etapas indicadas abaixo:

#### <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

1.  Instale Azure PowerShell usando este [guia](https://docs.microsoft.com/powershell/azure/install-az-ps).
2.  Se você tiver uma conta de armazenamento de blob v1 ou de uso geral, deverá primeiro atualizar para a versão v2 de uso geral usando este [guia](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
3.  Você deve ter **permitir que os serviços confiáveis da Microsoft acessem essa conta de armazenamento** ativada em firewalls da conta de armazenamento do Azure e no menu **de configurações de redes virtuais** . Consulte este [guia](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) para obter mais informações.
 
#### <a name="steps"></a>Passos
1. No PowerShell, **Registre seu servidor de banco de dados SQL** com Azure Active Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```
    
   1. Crie uma **conta de armazenamento de uso geral v2** usando este [guia](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

   > [!NOTE]
   > - Se você tiver uma conta de armazenamento de blob v1 ou de uso geral, deverá **primeiro atualizar para v2** usando este [guia](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
   > - Para problemas conhecidos com o Azure Data Lake Storage Gen2, consulte este [guia](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues).
    
1. Em sua conta de armazenamento, navegue até **controle de acesso (iam)** e clique em **Adicionar atribuição de função**. Atribuir função de RBAC de **colaborador de dados de blob de armazenamento** ao servidor do banco de dados SQL.

   > [!NOTE] 
   > Somente membros com privilégio de proprietário podem executar esta etapa. Para várias funções internas para recursos do Azure, consulte este [guia](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  
1. **Conectividade do polybase com a conta de armazenamento do Azure:**

   1. Crie uma **[chave mestra](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** de banco de dados se você não tiver criado uma anteriormente:
       ```SQL
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```
    
   1. Criar credencial no escopo do banco de dados com **identidade = ' identidade de serviço gerenciada '** :

       ```SQL
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```
       > [!NOTE] 
       > - Não é necessário especificar o segredo com a chave de acesso de armazenamento do Azure porque esse mecanismo usa a [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) nos bastidores.
       > - O nome da identidade deve ser **' identidade de serviço gerenciada '** para que a conectividade polybase funcione com a conta de armazenamento do Azure protegida para VNet.    
    
   1. Crie uma fonte de dados externa com o esquema abfss://para se conectar à sua conta de armazenamento de uso geral v2 usando o polybase:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```
       > [!NOTE] 
       > - Se você já tiver tabelas externas associadas à conta de armazenamento de blob v1 ou de uso geral, deverá primeiro descartar essas tabelas externas e, em seguida, remover a fonte de dados externa correspondente. Em seguida, crie uma fonte de dados externa com o esquema abfss://se conectando à conta de armazenamento de uso geral v2 como acima e recrie todas as tabelas externas usando essa nova fonte de dados externa. Você pode usar o [Assistente para gerar e publicar scripts](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard) para gerar a facilidade de criar scripts para todas as tabelas externas.
       > - Para obter mais informações sobre o esquema abfss://, consulte este [guia](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri).
       > - Para obter mais informações sobre como criar uma fonte de dados externa, consulte este [guia](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql).
        
   1. Consultar normalmente usando [tabelas externas](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Auditoria de blob do banco de dados SQL do Azure

A auditoria de blob envia os logs de auditoria para sua própria conta de armazenamento. Se essa conta de armazenamento usar o recurso de pontos de extremidade de serviço de VNet, a conectividade do banco de dados SQL do Azure à conta de armazenamento será interrompida.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Adicionando uma regra de firewall de VNet ao seu servidor sem ativar os pontos de extremidade de serviço de VNet

Há muito tempo, antes que esse recurso fosse aprimorado, era necessário ativar os pontos de extremidade do serviço VNet antes de implementar uma regra de VNet ao vivo no firewall. Os pontos de extremidade relacionados a uma determinada sub-rede de VNet a um banco de dados SQL do Azure. Mas, agora, a partir de janeiro de 2018, você pode burlar esse requisito definindo o sinalizador **IgnoreMissingVNetServiceEndpoint** .

Simplesmente definir uma regra de firewall não ajuda a proteger o servidor. Você também deve ativar os pontos de extremidade de serviço da VNet para que a segurança entre em vigor. Quando você ativa os pontos de extremidade de serviço, sua sub-rede VNet experimenta o tempo de inatividade até que conclua a transição de desativado para ativado. Isso é especialmente verdadeiro no contexto de grandes VNets. Você pode usar o sinalizador **IgnoreMissingVNetServiceEndpoint** para reduzir ou eliminar o tempo de inatividade durante a transição.

Você pode definir o sinalizador **IgnoreMissingVNetServiceEndpoint** usando o PowerShell. Para obter detalhes, consulte [PowerShell para criar um ponto de extremidade de serviço de rede virtual e regra para o banco de dados SQL do Azure][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Erros 40914 e 40615

O erro de conexão 40914 está relacionado às *regras de rede virtual*, conforme especificado no painel Firewall no portal do Azure. O erro 40615 é semelhante, exceto pelo fato de que ele está relacionado às *regras de endereço IP* no firewall.

### <a name="error-40914"></a>Erro 40914

*Texto da mensagem:* Não é possível abrir o servidor ' *[Server-Name]* ' solicitado pelo logon. O cliente não tem permissão para acessar o servidor.

*Descrição do erro:* O cliente está em uma sub-rede que tem pontos de extremidade de servidor de rede virtual. Mas o servidor do banco de dados SQL do Azure não tem nenhuma regra de rede virtual que conceda à sub-rede o direito de se comunicar com o banco de dados SQL.

*Resolução de erro:* No painel Firewall do portal do Azure, use o controle de regras de rede virtual para [Adicionar uma regra de rede virtual](#anchor-how-to-by-using-firewall-portal-59j) para a sub-rede.

### <a name="error-40615"></a>Erro 40615

*Texto da mensagem:* Não é possível abrir{0}o servidor ' ' solicitado pelo logon. O cliente com o endereço{1}IP ' ' não tem permissão para acessar o servidor.

*Descrição do erro:* O cliente está tentando se conectar de um endereço IP que não está autorizado a se conectar ao servidor do banco de dados SQL do Azure. O servidor da firewall não dispõe de qualquer regra de endereço IP que permita a um cliente comunicar entre esse mesmo endereço IP e a Base de Dados SQL.

*Resolução de erro:* Insira o endereço IP do cliente como uma regra de IP. Faça isso usando o painel Firewall na portal do Azure.

Uma lista de várias mensagens de erro do banco de dados SQL está documentada [aqui][sql-database-develop-error-messages-419g].

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>O portal pode criar uma regra de rede virtual

Esta seção ilustra como você pode usar o [portal do Azure][http-azure-portal-link-ref-477t] para criar uma *regra de rede virtual* no banco de dados SQL do Azure. A regra diz ao banco de dados SQL para aceitar a comunicação de uma determinada sub-rede que foi marcada como um *ponto de extremidade de serviço de rede virtual*.

> [!NOTE]
> Se você pretende adicionar um ponto de extremidade de serviço às regras de firewall de VNet do seu servidor de banco de dados SQL do Azure, primeiro verifique se os pontos de extremidade de serviço estão ativados para a sub-rede.
>
> Se os pontos de extremidade de serviço não estiverem ativados para a sub-rede, o portal solicitará que você os habilite. Clique no botão **habilitar** na mesma folha em que você adiciona a regra.

## <a name="powershell-alternative"></a>Alternativa do PowerShell

Um script do PowerShell também pode criar regras de rede virtual. O cmdlet crucial **New-AzSqlServerVirtualNetworkRule**. Se estiver interessado, consulte [PowerShell para criar um ponto de extremidade de serviço de rede virtual e regra para o banco de dados SQL do Azure][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>Alternativa da API REST

Internamente, os cmdlets do PowerShell para ações de VNet do SQL chamam APIs REST. Você pode chamar as APIs REST diretamente.

- [Regras de rede virtual: Das][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Pré-requisitos

Você já deve ter uma sub-rede que esteja marcada com o *nome do tipo* de ponto de extremidade de serviço de rede virtual específico relevante para o banco de dados SQL do Azure.

- O nome do tipo de ponto de extremidade relevante é **Microsoft. SQL**.
- Se sua sub-rede não puder ser marcada com o nome do tipo, consulte [verificar se sua sub-rede é um ponto de extremidade][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Etapas de portal do Azure

1. Inicie sessão no [portal do Azure][http-azure-portal-link-ref-477t].

2. Em seguida, navegue pelo portal para **servidores** &gt; SQL **Firewall/redes virtuais**.

3. Defina o controle **permitir acesso aos serviços do Azure** como desativado.

    > [!IMPORTANT]
    > Se você deixar o controle definido como ativado, seu servidor de banco de dados SQL do Azure aceitará a comunicação de qualquer sub-rede. Deixar o controle definido como ON pode ser o acesso excessivo de um ponto de vista de segurança. O recurso de ponto de extremidade de serviço Rede Virtual do Microsoft Azure, em coordenação com o recurso de regra de rede virtual do banco de dados SQL, em conjunto pode reduzir sua área de superfície de segurança.

4. Clique em **+ Adicionar controle existente** , na seção **redes virtuais** .

    ![Clique em Adicionar existente (ponto de extremidade de sub-rede, como uma regra SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. No novo painel **criar/atualizar** , preencha os controles com os nomes dos recursos do Azure.

    > [!TIP]
    > Você deve incluir o **prefixo de endereço** correto para sua sub-rede. Você pode encontrar o valor no Portal.
    > Navegar **por todos os recursos** &gt; **todos os tipos** &gt; **redes virtuais**. O filtro exibe suas redes virtuais. Clique em sua rede virtual e, em seguida, clique em **sub-redes**. A coluna **intervalo de endereços** tem o prefixo de endereço de que você precisa.

    ![Preencha os campos para a nova regra.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Clique no botão **OK** próximo à parte inferior do painel.

7. Consulte a regra de rede virtual resultante no painel Firewall.

    ![Consulte a nova regra, no painel Firewall.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Os status ou os Estados a seguir se aplicam às regras:
> - **Esteja** Indica que a operação iniciada foi bem-sucedida.
> - **Falha ao** Indica que a operação iniciada falhou.
> - **Excluí** Aplica-se somente à operação de exclusão e indica que a regra foi excluída e não se aplica mais.
> - **InProgress** Indica que a operação está em andamento. A regra antiga se aplica enquanto a operação está nesse estado.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Artigos relacionados

- [Pontos de extremidade de serviço de rede virtual do Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Regras de firewall no nível de servidor e banco de dados do banco de dados SQL do Azure][sql-db-firewall-rules-config-715d]

O recurso de regra de rede virtual para o banco de dados SQL do Azure tornou-se disponível no final de setembro de 2017.

## <a name="next-steps"></a>Passos Seguintes

- [Use o PowerShell para criar um ponto de extremidade de serviço de rede virtual e uma regra de rede virtual para o banco de dados SQL do Azure.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Regras de rede virtual: Operações][rest-api-virtual-network-rules-operations-862r] com APIs REST

<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.yml

[rbac-what-is-813s]:../role-based-access-control/overview.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
