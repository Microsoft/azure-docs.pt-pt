---
title: Pontos finais vNet e regras para bases de dados na Base de Dados Azure SQL
description: Marque uma sub-rede como um ponto final de serviço de Rede Virtual. Em seguida, o ponto final como regra de rede virtual para a ACL a sua base de dados. A sua base de dados aceita então a comunicação de todas as máquinas virtuais e outros nós na sub-rede.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: efea5d6548814dc0f165bab9281e5234f3eae925
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791329"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Utilize pontos finais de serviço de rede virtual e regras para servidores na Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*As regras de rede virtual* são uma funcionalidade de segurança de firewall que controla quer o servidor das suas bases de dados e piscinas elásticas na [Base de Dados Azure SQL](sql-database-paas-overview.md) ou para as suas bases de dados em [Azure Synapse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) aceita comunicações enviadas a partir de sub-redes específicas em redes virtuais. Este artigo explica porque é que a funcionalidade de regra de rede virtual é, por vezes, a sua melhor opção para permitir a comunicação segura à sua base de dados na Base de Dados Azure SQL e na Azure Synapse Analytics (anteriormente SQL Data Warehouse).

> [!NOTE]
> Este artigo aplica-se tanto à Base de Dados Azure SQL como à Azure Synapse Analytics. Para simplificar, o termo "base de dados" refere-se a ambas as bases de dados na Base de Dados Azure SQL e na Azure Synapse Analytics. Da mesma forma, quaisquer referências ao 'servidor' referem-se ao [servidor lógico SQL](logical-servers.md) que acolhe a Base de Dados Azure SQL e a Azure Synapse Analytics.

Para criar uma regra de rede virtual, deve primeiro existir um [ponto final de serviço de rede virtual][vm-virtual-network-service-endpoints-overview-649d] para a regra a referenciar.

## <a name="how-to-create-a-virtual-network-rule"></a>Como criar uma regra de rede virtual

Se criar apenas uma regra de rede virtual, pode adiantar-se aos passos e explicações [mais tarde neste artigo.](#anchor-how-to-by-using-firewall-portal-59j)

## <a name="details-about-virtual-network-rules"></a>Detalhes sobre regras de rede virtual

Esta secção descreve vários detalhes sobre as regras de rede virtual.

### <a name="only-one-geographic-region"></a>Apenas uma região geográfica

Cada ponto final de serviço da Rede Virtual aplica-se apenas a uma região de Azure. O ponto final não permite que outras regiões aceitem a comunicação a partir da sub-rede.

Qualquer regra de rede virtual limita-se à região a que se aplica o seu ponto final subjacente.

### <a name="server-level-not-database-level"></a>Nível de servidor, não nível de base de dados

Cada regra de rede virtual aplica-se a todo o servidor, e não apenas a uma base de dados específica no servidor. Por outras palavras, a regra da rede virtual aplica-se ao nível do servidor e não ao nível da base de dados.

- Em contrapartida, as regras de PI podem aplicar-se a ambos os níveis.

### <a name="security-administration-roles"></a>Funções de administração de segurança

Existe uma separação de funções de segurança na administração dos pontos finais de serviço de Rede Virtual. São necessárias medidas de cada uma das seguintes funções:

- **Administração de rede:** &nbsp; Ligue o ponto final.
- **Administração de base de dados:** &nbsp; Atualize a lista de controlo de acesso (ACL) para adicionar a sub-rede dada ao servidor.

*Alternativa RBAC:*

Os papéis de Administração de Rede e Administração de Bases de Dados têm mais capacidades do que as necessárias para gerir as regras de rede virtuais. Apenas um subconjunto das suas capacidades é necessário.

Você tem a opção de usar o [controle de acesso baseado em funções Azure (Azure RBAC)][rbac-what-is-813s] em Azure para criar um único papel personalizado que tem apenas o subconjunto necessário de capacidades. O papel personalizado poderia ser usado em vez de envolver o Administrador de Rede ou o Administrador de Base de Dados. A área de superfície da sua exposição à segurança é menor se adicionar um utilizador a uma função personalizada, em vez de adicionar o utilizador às outras duas principais funções de administrador.

> [!NOTE]
> Em alguns casos, a base de dados na Base de Dados Azure SQL e na sub-rede VNet estão em diferentes subscrições. Nestes casos, deve assegurar as seguintes configurações:
>
> - Ambas as subscrições devem estar no mesmo inquilino do Azure Ative Directory.
> - O utilizador tem as permissões necessárias para iniciar operações, tais como ativar pontos finais de serviço e adicionar uma sub-rede VNet ao servidor dado.
> - Ambas as subscrições devem ter o fornecedor Microsoft.Sql registado.

## <a name="limitations"></a>Limitações

Para a Base de Dados Azure SQL, a funcionalidade de regras de rede virtual tem as seguintes limitações:

- Na firewall da sua base de dados na Base de Dados Azure SQL, cada regra de rede virtual faz referência a uma sub-rede. Todas estas sub-redes referenciadas devem ser aloiadas na mesma região geográfica que acolhe a base de dados.

- Cada servidor pode ter até 128 entradas ACL para qualquer rede virtual.

- As regras de rede virtuais aplicam-se apenas às redes virtuais do Azure Resource Manager; e não para redes [de modelos de implantação clássicas.][arm-deployment-model-568f]

- Ligar os pontos finais do serviço de rede virtual ON para a Base de Dados Azure SQL também permite os pontos finais para os serviços MySQL e PostgreSQL Azure. No entanto, com os pontos finais ON, as tentativas de ligação dos pontos finais às suas instâncias MySQL ou PostgreSQL podem falhar.
  - A razão subjacente é que o MySQL e o PostgreSQL provavelmente não têm uma regra de rede virtual configurada. Tem de configurar uma regra de rede virtual para Azure Database para MySQL e PostgreSQL e a ligação terá sucesso.

- Na firewall, as gamas de endereços IP aplicam-se aos seguintes itens de rede, mas as regras de rede virtuais não:
  - [Rede privada virtual local-a-local (S2S) (VPN)][vpn-gateway-indexmd-608y]
  - No local via [ExpressRoute][expressroute-indexmd-744v]

### <a name="considerations-when-using-service-endpoints"></a>Considerações ao utilizar pontos finais de serviço

Ao utilizar os pontos finais de serviço para a Base de Dados Azure SQL, reveja as seguintes considerações:

- **Saída para Azure SQL Database Os IPs públicos são necessários** : Os Grupos de Segurança da Rede (NSGs) devem ser abertos ao Azure SQL Database IPs para permitir a conectividade. Pode fazê-lo utilizando [tags de serviço](../../virtual-network/network-security-groups-overview.md#service-tags) NSG para base de dados Azure SQL.

### <a name="expressroute"></a>ExpressRoute

Se estiver a utilizar o [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a partir das suas instalações, para espreitar publicamente ou para o microsoft espreitar, terá de identificar os endereços IP NAT utilizados. Para peering público, cada circuito ExpressRoute, por predefinição, utiliza dois endereços IP NAT que são aplicados ao tráfego de serviço do Azure quando o tráfego entra no backbone de rede do Microsoft Azure. Para peering da Microsoft, o(s) endereço(s) IP NAT que são utilizados são fornecidos pelo cliente ou são fornecidos pelo fornecedor de serviços. Para permitir o acesso aos recursos de serviço, tem de permitir estes endereços IP públicos na definição da firewall do IP dos recursos. Para localizar os endereços IP do circuito ExpressRoute de peering público, [abra um pedido de suporte no ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) através do portal do Azure. Saiba mais sobre [NAT para peering público e da Microsoft do ExpressRoute.](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
Para permitir a comunicação do seu circuito até à Base de Dados Azure SQL, tem de criar regras de rede IP para os endereços IP públicos do seu NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Impacto da utilização de pontos finais de serviço VNet com armazenamento Azure

O Armazenamento do Microsoft Azure implementou a mesma funcionalidade que lhe permite limitar a conectividade à conta do Armazenamento do Microsoft Azure. Se optar por utilizar esta funcionalidade com uma conta do Armazenamento do Microsoft Azure que está a ser utilizada pela Base de Dados SQL do Azure, poderão ocorrer problemas. Segue-se uma lista e discussão das funcionalidades Azure SQL Database e Azure Synapse Analytics que são impactadas por isso.

### <a name="azure-synapse-polybase-and-copy-statement"></a>Declaração de Azure Synapse PolyBase e COPY

A PolyBase e a declaração COPY são comumente usadas para carregar dados em Azure Synapse Analytics a partir de Azure Storage contas para ingestão de dados de produção elevada. Se a conta de Armazenamento Azure que está a carregar dados de limites de acesso apenas a um conjunto de sub-redes VNet, a conectividade ao utilizar o PolyBase e a declaração COPY para a conta de armazenamento quebrará. Para permitir cenários de importação e exportação utilizando COPY e PolyBase com Azure Synapse Analytics conectando-se ao Azure Storage que está seguro ao VNet, siga os passos indicados abaixo:

#### <a name="prerequisites"></a>Pré-requisitos

- Siga este [guia](/powershell/azure/install-az-ps) para instalar o Azure PowerShell.
- Se tiver uma conta de armazenamento de blobs ou fins gerais v1, terá primeiro de atualizar para a conta fins gerais v2. Para tal, siga este [guia](../../storage/common/storage-account-upgrade.md).
- Deve ter **permitido que serviços fidedignos da Microsoft acedam a esta conta de armazenamento** ligados no menu de firewalls da conta de armazenamento Azure e redes **virtuais.** Ativar esta configuração permitirá que a PolyBase e a declaração COPY se conectem à conta de armazenamento utilizando uma autenticação forte onde o tráfego de rede permanece na espinha dorsal do Azure. Para obter mais informações, veja este [guia](../../storage/common/storage-network-security.md#exceptions).

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de erros até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre a sua compatibilidade, consulte [a introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Passos

1. No PowerShell, **registe o seu servidor** que hospeda Azure Synapse com o Azure Ative Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

1. Crie uma **conta de armazenamento v2 para fins gerais** utilizando este [guia.](../../storage/common/storage-account-create.md)

   > [!NOTE]
   >
   > - Se tiver uma conta de armazenamento v1 ou blob para fins gerais, tem primeiro de **atualizar para v2** utilizando este [guia](../../storage/common/storage-account-upgrade.md).
   > - Para questões conhecidas com a Azure Data Lake Storage Gen2, consulte este [guia.](../../storage/blobs/data-lake-storage-known-issues.md)

1. Na sua conta de armazenamento, navegue para **Access Control (IAM)** e selecione **Adicionar a atribuição de funções** . Atribua o papel de Azure **do Contribuinte de Dados de Armazenamento** ao servidor que hospeda o seu Azure Synapse Analytics que registou no Azure Ative Directory (AAD) como #1 passo.

   > [!NOTE]
   > Apenas os membros com privilégio proprietário na conta de armazenamento podem realizar este passo. Para várias funções incorporadas do Azure, consulte este [guia.](../../role-based-access-control/built-in-roles.md)
  
1. **Conectividade polybase à conta de Armazenamento Azure:**

   1. Crie uma **[chave principal](/sql/t-sql/statements/create-master-key-transact-sql)** de base de dados se não tiver criado uma anterior:

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Criar credenciais de âmbito de base de dados com **IDENTIDADE = 'Identidade de Serviço Gerido'** :

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Não há necessidade de especificar SECRET com a chave de acesso Azure Storage porque este mecanismo utiliza [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md) sob as capas.
       > - O nome DE IDENTIDADE deve ser **"Identidade de serviço gerido"** para a conectividade PolyBase para trabalhar com a conta de armazenamento Azure garantida à VNet.

   1. Crie uma fonte de dados externa com `abfss://` esquema de ligação à sua conta de armazenamento v2 de fim geral utilizando a PolyBase:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Se já tiver tabelas externas associadas à conta de armazenamento v1 ou blob de uso geral, deve primeiro deixar cair essas tabelas externas e depois deixar cair a correspondente fonte de dados externos. Em seguida, crie uma fonte de dados externa com `abfss://` o esquema de ligação à conta de armazenamento v2 de fim geral como acima e recobrir todas as tabelas externas utilizando esta nova fonte de dados externo. Pode utilizar [o '' 'Gerar e Publicar scripts'](/sql/ssms/scripting/generate-and-publish-scripts-wizard) para gerar scripts para todas as tabelas externas para facilitar.
       > - Para obter mais informações sobre `abfss://` o esquema, consulte este [guia.](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md)
       > - Para obter mais informações sobre CREATE EXTERNAL DATA SOURCE, consulte este [guia](/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Consulta normal usando [tabelas externas](/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Auditoria de Blob de Base de Dados Azure SQL

A auditoria blob empurra os registos de auditoria para a sua própria conta de armazenamento. Se esta conta de armazenamento utilizar a função ponto final do Serviço VNet, então a conectividade da Base de Dados Azure SQL para a conta de armazenamento quebrará.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Adicionar uma regra VNet Firewall ao seu servidor sem ligar pontos finais de serviço VNet

Há muito tempo, antes de esta funcionalidade ser melhorada, foi-lhe exigido que ligasse os pontos finais do serviço VNet antes de poder implementar uma regra VNet ao vivo na Firewall. Os pontos finais diziam respeito a uma sub-rede VNet a uma base de dados na Base de Dados Azure SQL. Mas agora, a partir de janeiro de 2018, pode contornar este requisito definindo a bandeira **IgnoreMissingVNetServiceEndpoint.**

Apenas definir uma regra de Firewall não ajuda a proteger o servidor. Também tem de ligar os pontos finais do serviço VNet para que a segurança produza efeitos. Quando liga os pontos finais de serviço, a sua sub-rede VNet experimenta tempo de inatividade até completar a transição de Off para On. Isto é especialmente verdade no contexto dos grandes VNets. Pode utilizar a bandeira **IgnoreMissingVNetServiceEndpoint** para reduzir ou eliminar o tempo de inatividade durante a transição.

Pode configurar a bandeira **IgnoreMissingVNetServiceEndpoint** utilizando o PowerShell. Para mais informações, consulte [o PowerShell para criar um ponto final de serviço de Rede Virtual e regra para a Base de Dados Azure SQL][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Erros 40914 e 40615

O erro de ligação 40914 diz respeito às *regras de rede virtuais,* conforme especificado no painel firewall do portal Azure. O erro 40615 é semelhante, exceto que diz respeito às *regras* de endereço IP na Firewall.

### <a name="error-40914"></a>Erro 40914

*Texto de mensagem:* Não é possível abrir o servidor *' [nome do servidor]* ' solicitado pelo login. O cliente não está autorizado a aceder ao servidor.

*Descrição do erro:* O cliente está numa sub-rede que tem pontos finais de servidor de rede virtual. Mas o servidor não tem nenhuma regra de rede virtual que conceda à sub-rede o direito de comunicar com a base de dados.

*Resolução de erros:* No painel firewall do portal Azure, utilize o controlo de regras de rede virtual para [adicionar uma regra de rede virtual](#anchor-how-to-by-using-firewall-portal-59j) para a sub-rede.

### <a name="error-40615"></a>Erro 40615

*Texto de mensagem:* Não é possível abrir o servidor {0} ' solicitado pelo login. O cliente com endereço IP {1} ' ' não está autorizado a aceder ao servidor.

*Descrição do erro:* O cliente está a tentar ligar-se a partir de um endereço IP que não está autorizado a ligar-se ao servidor. A firewall do servidor não tem nenhuma regra de endereço IP que permita a um cliente comunicar a partir do endereço IP dado para a base de dados.

*Resolução de erros:* Insira o endereço IP do cliente como regra ip. Faça isso através do painel Firewall no portal do Azure.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="portal-can-create-a-virtual-network-rule"></a>Portal pode criar uma regra de rede virtual

Esta secção ilustra como pode utilizar o [portal Azure][http-azure-portal-link-ref-477t] para criar uma *regra de rede virtual* na sua base de dados na Base de Dados Azure SQL. A regra diz à sua base de dados para aceitar a comunicação a partir de uma determinada sub-rede que foi marcada como sendo um *ponto final de serviço de Rede Virtual* .

> [!NOTE]
> Se pretender adicionar um ponto final de serviço às regras de firewall VNet do seu servidor, certifique-se primeiro de que os pontos finais de serviço estão ligados para a sub-rede.
>
> Se os pontos finais de serviço não estiverem ligados para a sub-rede, o portal pede-lhe para os ativar. Clique no botão **Ativar** na mesma lâmina na qual adiciona a regra.

## <a name="powershell-alternative"></a>Alternativa PowerShell

Um script também pode criar regras de rede virtuais usando PowerShell cmdlet **New-AzSqlServerVirtualNetworkRule** ou [vnet de rede az criar](/cli/azure/network/vnet#az-network-vnet-create). Se estiver interessado, consulte [o PowerShell para criar um ponto final de serviço de Rede Virtual e regra para a Base de Dados Azure SQL][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>Rest API alternativa

Internamente, os cmdlets PowerShell para as ações SQL VNet chamam APIs de REST. Pode ligar diretamente para as APIs REST.

- [Regras da Rede Virtual: Operações][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Pré-requisitos

Já deve ter uma sub-rede que está marcada com o nome de *tipo de ponto* final de serviço de rede virtual relevante para a Base de Dados Azure SQL.

- O nome do tipo ponto final relevante é **Microsoft.Sql** .
- Se a sua sub-rede não estiver marcada com o nome do tipo, consulte [Verifique se a sua sub-rede é um ponto final][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Passos do portal Azure

1. Inicie sessão no [Portal do Azure][http-azure-portal-link-ref-477t].

2. Procure e selecione **servidores SQL** e, em seguida, selecione o seu servidor. Em **Segurança** , selecione **Firewalls e redes virtuais** .

3. Desconfiem do controlo **de serviços Azure** para OFF.

    > [!IMPORTANT]
    > Se deixar o conjunto de controlo para ON, o seu servidor aceita a comunicação a partir de qualquer sub-rede dentro do limite Azure, ou seja, originário de um dos endereços IP que é reconhecido como aqueles dentro dos intervalos definidos para centros de dados Azure. Deixar o controlo definido para ON pode ser um acesso excessivo do ponto de vista de segurança. A funcionalidade de ponto final do serviço de rede virtual Microsoft Azure, em coordenação com a funcionalidade de regra de rede virtual da BASE de Dados SQL, em conjunto pode reduzir a sua área de superfície de segurança.

4. Clique no controlo + Adicionar o controlo **existente,** na secção **redes Virtuais.**

    ![Clique em adicionar o ponto final existente (ponto final da sub-rede, como regra SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. No novo painel **Criar/Atualizar,** preencha os controlos com os nomes dos seus recursos Azure.

    > [!TIP]
    > Tem de incluir o **prefixo de endereço** correto para a sua sub-rede. Pode encontrar o valor no portal.
    > Navegar **por todos os recursos** Todos os &gt; **tipos** de redes &gt; **virtuais.** O filtro exibe as suas redes virtuais. Clique na sua rede virtual e, em seguida, clique em **Sub-redes** . A coluna **ADDRESS RANGE** tem o prefixo de endereço de que necessita.

    ![Preencha os campos para uma nova regra.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Clique no botão **OK** perto da parte inferior do painel.

7. Consulte a regra da rede virtual resultante no painel de firewall.

    ![Veja a nova regra, no painel de firewall.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Os seguintes estatutos ou estados aplicam-se às regras:
>
> - **Pronto:** Indica que a operação que iniciou foi bem sucedida.
> - **Falhou:** Indica que a operação que iniciou falhou.
> - **Eliminado:** Aplica-se apenas à operação Eliminar e indica que a regra foi eliminada e já não se aplica.
> - **InProgress:** Indica que a operação está em andamento. A regra antiga aplica-se enquanto a operação está neste estado.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Artigos relacionados

- [Pontos finais de serviço de rede virtual Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Regras de firewall ao nível do servidor e de nível de base de dados][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Passos seguintes

- [Utilize o PowerShell para criar um ponto final de serviço de rede virtual e, em seguida, uma regra de rede virtual para Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Regras de Rede Virtual: Operações][rest-api-virtual-network-rules-operations-862r] com APIs REST

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]:../index.yml
[rbac-what-is-813s]:../../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]:firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]:scripts/vnet-service-endpoint-rule-powershell-create.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]:scripts/vnet-service-endpoint-rule-powershell-create.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[vpn-gateway-indexmd-608y]: ../../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: /rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->