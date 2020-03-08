---
title: Pontos finais vNet e regras para bases de dados individuais e reunidas
description: Marque uma subnetcomo ponto final de serviço de Rede Virtual. Em seguida, o ponto final como regra de rede virtual para a ACL a sua Base de Dados Azure SQL. A Base de Dados SQL aceita então a comunicação de todas as máquinas virtuais e outros nós na subnet.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 0562d609231d69d95f1d2b5b838663b704f8f2f3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358458"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>Utilize pontos finais e regras de serviço de rede virtual para servidores de base de dados

*As regras* de rede virtual são uma característica de segurança de firewall que controla se o servidor de base de dados para as suas bases de dados únicas e um pool elástico na Base de Dados Azure [SQL](sql-database-technical-overview.md) ou para as suas bases de dados no [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) aceita comunicações enviadas a partir de determinadas redes virtuais. Este artigo explica porque é que a funcionalidade de regra da rede virtual é, por vezes, a sua melhor opção para permitir a comunicação com segurança para a sua Base de Dados Azure SQL e SQL Data Warehouse.

> [!IMPORTANT]
> Este artigo aplica-se ao servidor Azure SQL e tanto às bases de dados SQL como ao SQL Data Warehouse que são criados no servidor Azure SQL. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse. Este artigo *não* se aplica a uma implantação de **instância gerida** na Base de Dados Azure SQL porque não tem um ponto final de serviço associado ao mesmo.

Para criar uma regra de rede virtual, tem primeiro de existir um ponto final do serviço de [rede virtual][vm-virtual-network-service-endpoints-overview-649d] para a regra de referência.

## <a name="how-to-create-a-virtual-network-rule"></a>Como criar uma regra de rede virtual

Se criar apenas uma regra de rede virtual, pode saltar para a frente para os passos e explicação [mais tarde neste artigo](#anchor-how-to-by-using-firewall-portal-59j).

<!--<a name="anch-details-about-vnet-rules-38q"/> -->

## <a name="details-about-virtual-network-rules"></a>Detalhes sobre as regras da rede virtual

Esta secção descreve vários detalhes sobre as regras da rede virtual.

### <a name="only-one-geographic-region"></a>Apenas uma região geográfica

Cada ponto final do serviço de Rede Virtual aplica-se a apenas uma região azure. O objetivo final não permite que outras regiões aceitem a comunicação da sub-rede.

Qualquer regra de rede virtual limita-se à região a que o seu ponto final subjacente se aplica.

### <a name="server-level-not-database-level"></a>Nível de servidor, não nível de base de dados

Cada regra de rede virtual aplica-se a todo o seu servidor de base de dados Azure SQL, e não apenas a uma determinada base de dados no servidor. Por outras palavras, a regra da rede virtual aplica-se ao nível do servidor, e não ao nível da base de dados.

- Em contrapartida, as regras de PI podem ser aplicadas em ambos os níveis.

### <a name="security-administration-roles"></a>Funções de administração de segurança

Existe uma separação das funções de segurança na administração de pontos finais de serviço da Rede Virtual. São necessárias ações de cada uma das seguintes funções:

- **Administração da rede:** &nbsp; ligar o ponto final.
- **Administração da base de dados:** &nbsp; Atualize a lista de controlo de acesso (ACL) para adicionar a subnet dada ao servidor de base de dados SQL.

*Alternativa RBAC:*

Os papéis da Administração de Rede e da Base de Dados Admin têm mais capacidades do que as necessárias para gerir as regras de rede virtuais. Só é necessário um subconjunto das suas capacidades.

Tem a opção de utilizar o controlo de [acesso baseado em funções (RBAC)][rbac-what-is-813s] em Azure para criar uma única função personalizada que tenha apenas o subconjunto necessário de capacidades. O papel personalizado poderia ser usado em vez de envolver o Administrador da Rede ou o Administrador de Base de Dados. A área de superfície da sua exposição à segurança é menor se adicionar um utilizador a uma função personalizada, versus adicionar o utilizador às outras duas principais funções de administrador.

> [!NOTE]
> Em alguns casos, a Base de Dados Azure SQL e a vNet-subnet estão em diferentes subscrições. Nestes casos deve assegurar as seguintes configurações:
> - Ambas as subscrições devem estar no mesmo inquilino do Azure Ative Directory.
> - O utilizador possui as permissões necessárias para iniciar operações, tais como permitir pontos finais de serviço e adicionar uma sub-rede VNet ao servidor dado.
> - Ambas as subscrições devem ter o fornecedor Microsoft.Sql registado.

## <a name="limitations"></a>Limitações

Para a Base de Dados Azure SQL, a funcionalidade de regras de rede virtual tem as seguintes limitações:

- Na firewall para a sua Base de Dados SQL, cada regra de rede virtual refere uma subnet. Todas estas subredes referenciadas devem ser alojadas na mesma região geográfica que acolhe a Base de Dados SQL.

- Cada servidor de base de dados Azure SQL pode ter até 128 entradas ACL para qualquer rede virtual.

- As regras de rede virtual aplicam-se apenas às redes virtuais do Gestor de Recursos Do Azure; e não para redes de modelos de [implantação clássicas.][arm-deployment-model-568f]

- Ligar os pontos finais do serviço de rede virtual para a Base de Dados Azure SQL também permite os pontos finais dos serviços MySQL e PostgreSQL Azure. No entanto, com os pontos finais ON, as tentativas de ligação dos pontos finais aos seus casos MySQL ou PostgreSQL podem falhar.
  - A razão subjacente é que mySQL e PostgreSQL provavelmente não têm uma regra de rede virtual configurada. Tem de configurar uma regra de rede virtual para a Base de Dados Azure para MySQL e PostgreSQL e a ligação terá sucesso.

- Na firewall, as gamas de endereços IP aplicam-se aos seguintes itens de rede, mas as regras de rede virtuais não:
  - [Rede privada virtual site-to-site (S2S) (VPN)][vpn-gateway-indexmd-608y]
  - No local via [ExpressRoute][expressroute-indexmd-744v]

### <a name="considerations-when-using-service-endpoints"></a>Considerações ao utilizar pontos finais de serviço

Ao utilizar pontos finais de serviço para a Base de Dados Azure SQL, reveja as seguintes considerações:

- É necessário sair para os **IPs públicos da Base de Dados Azure SQL:** Os Grupos de Segurança da Rede (NSGs) devem ser abertos aos IPs de base de dados Azure SQL para permitir a conectividade. Pode fazê-lo utilizando [etiquetas](../virtual-network/security-overview.md#service-tags) de serviço NSG para base de dados Azure SQL.

### <a name="expressroute"></a>ExpressRoute

Se estiver a utilizar o [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a partir das suas instalações, para espreitar público ou espreitar a Microsoft, terá de identificar os endereços IP NAT que são utilizados. Para peering público, cada circuito ExpressRoute, por predefinição, utiliza dois endereços IP NAT que são aplicados ao tráfego de serviço do Azure quando o tráfego entra no backbone de rede do Microsoft Azure. Para peering da Microsoft, o(s) endereço(s) IP NAT que são utilizados são fornecidos pelo cliente ou são fornecidos pelo fornecedor de serviços. Para permitir o acesso aos recursos de serviço, tem de permitir estes endereços IP públicos na definição da firewall do IP dos recursos. Para localizar os endereços IP do circuito ExpressRoute de peering público, [abra um pedido de suporte no ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) através do portal do Azure. Saiba mais sobre [NAT para peering público e da Microsoft do ExpressRoute.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
Para permitir a comunicação do seu circuito à Base de Dados Azure SQL, tem de criar regras de rede IP para os endereços IP públicos do seu NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Impacto da utilização de pontos finais de serviço VNet com armazenamento Azure

O Azure Storage implementou a mesma funcionalidade que lhe permite limitar a conectividade à sua conta de Armazenamento Azure. Se optar por utilizar esta funcionalidade com uma conta de Armazenamento Azure que está a ser utilizada pelo Azure SQL Server, pode encontrar problemas. Segue-se uma lista e discussão das funcionalidades azure SQL Database e Azure SQL Data Warehouse que são impactadas por esta.

### <a name="azure-sql-data-warehouse-polybase"></a>Azure SQL Data Warehouse PolyBase

A PolyBase é comumente usada para carregar dados no Armazém de Dados Azure SQL a partir de contas de Armazenamento Azure. Se a conta de Armazenamento Azure que está a carregar dados dos limites aceder apenas a um conjunto de subredes VNet, a conectividade da PolyBase para a Conta quebrar-se-á. Para permitir tanto os cenários de importação e exportação da PolyBase com o Azure SQL Data Warehouse que está ligado ao Armazenamento Azure que está seguro à VNet, siga os passos indicados abaixo:

#### <a name="prerequisites"></a>Pré-requisitos

- Instale o Azure PowerShell utilizando este [guia](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Se tiver uma conta de armazenamento v1 ou blob de uso geral, tem primeiro de atualizar para v2 de uso geral utilizando este [guia](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
- Deve permitir **que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento** ligada no menu de definições de **firewalls e configurações** de redes virtuais da conta Azure. Consulte este [guia](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) para mais informações.

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para mais informações sobre a sua compatibilidade, consulte [A introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Passos

1. Na PowerShell, **registe o seu Servidor Azure SQL** que acolhe a sua instância de Armazém de Dados Azure SQL com o Azure Ative Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

1. Crie uma **Conta de Armazenamento v2** de uso geral utilizando este [guia](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

   > [!NOTE]
   > - Se tiver uma conta de armazenamento v1 ou blob de uso geral, tem primeiro de **atualizar para v2** utilizando este [guia](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
   > - Para questões conhecidas com o Azure Data Lake Storage Gen2, consulte este [guia](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues).
    
1. Na sua conta de armazenamento, navegue para o Controlo de **Acesso (IAM)** e clique em **Adicionar a atribuição de funções**. Atribuir papel de RBAC do Colaborador de **Dados blob** de armazenamento ao seu Servidor Azure SQL que acolhe o seu Armazém de Dados Azure SQL, que registou com o Azure Ative Directory (AAD) como no passo#1.

   > [!NOTE]
   > Só os membros com privilégio proprietário podem executar este passo. Para várias funções incorporadas para os recursos Azure, consulte este [guia.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)
  
1. **Conectividade polibase com a conta de Armazenamento Azure:**

   1. Crie uma **[chave de base](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** de dados se não tiver criado uma anterior:

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Criar credenciais com código de base de dados com **identidade = 'Identidade de serviço gerida':**

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       > - Não há necessidade de especificar SECRET com a chave de acesso ao Armazenamento Azure porque este mecanismo utiliza [identidade gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) sob as capas.
       > - O nome DE IDENTIDADE deve ser **"Identidade de Serviço Gerida"** para a conectividade PolyBase para trabalhar com a conta de Armazenamento Azure garantida à VNet.

   1. Crie fonte de dados externacom `abfss://` esquema de ligação à sua conta de armazenamento v2 de uso da PolyBase:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       > - Se já tem tabelas externas associadas à conta de armazenamento v1 ou blob de uso geral, deve primeiro largar essas tabelas externas e, em seguida, deixar cair a fonte de dados externa correspondente. Em seguida, crie uma fonte externa de dados com `abfss://` esquema que ligue à conta de armazenamento v2 de uso geral como acima e recrie todas as tabelas externas utilizando esta nova fonte de dados externa. Pode utilizar [o Generate and Publish Scripts Wizard](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard) para gerar scripts de criação para todas as tabelas externas para facilitar.
       > - Para mais informações sobre `abfss://` esquema, consulte este [guia](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri).
       > - Para mais informações sobre a CREATE EXTERNAL DATA SOURCE, consulte este [guia](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Consulta normal utilizando [tabelas externas](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Auditoria blob da base de dados Azure SQL

A auditoria blob empurra os registos de auditoria para a sua própria conta de armazenamento. Se esta conta de armazenamento utilizar a funcionalidade de pontos finais do Serviço VNet, a conectividade da Base de Dados Azure SQL para a conta de armazenamento quebrar-se-á.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Adicionar uma regra de Firewall VNet ao seu servidor sem ligar pontos finais do serviço VNet

Há muito tempo, antes de esta funcionalidade ser melhorada, foi obrigado a ligar os pontos finais do serviço VNet antes de poder implementar uma regra VNet ao vivo na Firewall. Os pontos finais relacionavam uma dada sub-rede VNet a uma base de dados Azure SQL. Mas agora, a partir de janeiro de 2018, pode contornar esta exigência definindo a bandeira **IgnoreMissingVNetServiceEndpoint.**

Apenas definir uma regra de firewall não ajuda a proteger o servidor. Também deve ligar os pontos finais do serviço VNet para que a segurança entre em vigor. Quando liga os pontos finais do serviço Ligados, a sua subnet VNet experimenta tempo de inatividade até completar a transição de Off para On. Isto é especialmente verdade no contexto dos grandes VNets. Pode utilizar a bandeira **IgnoreMissingVNetServiceEndpoint** para reduzir ou eliminar o tempo de inatividade durante a transição.

Pode definir a bandeira **IgnoreMissingVNetServiceEndpoint** utilizando o PowerShell. Para mais detalhes, consulte o PowerShell para criar um ponto final de serviço de Rede Virtual e a regra para a Base de [Dados Azure SQL][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Erros 40914 e 40615

O erro de ligação 40914 diz respeito às regras de *rede virtuais,* conforme especificado no painel firewall no portal Azure. O erro 40615 é semelhante, exceto que se relaciona com as regras de *endereço IP* na Firewall.

### <a name="error-40914"></a>Erro 40914

*Texto de mensagem:* Não é possível abrir o servidor *' [nome do servidor]* solicitado pelo login. O cliente não está autorizado a aceder ao servidor.

*Descrição do erro:* O cliente está numa subnet que tem pontos finais de servidor de rede virtual. No entanto, o servidor da Base de Dados SQL do Azure não tem nenhuma regra de rede virtual que concede à sub-rede o direito de comunicar com a Base de Dados SQL.

*Resolução de erros:* No painel firewall do portal Azure, utilize o controlo de regras da rede virtual para [adicionar uma regra de rede virtual](#anchor-how-to-by-using-firewall-portal-59j) para a sub-rede.

### <a name="error-40615"></a>Erro 40615

*Texto de mensagem:* Não é possível abrir o '{0}' do servidor ' solicitado pelo login. O cliente com endereço IP '{1}' não está autorizado a aceder ao servidor.

*Descrição do erro:* O cliente está a tentar ligar-se a partir de um endereço IP que não está autorizado a ligar-se ao servidor de base de dados Azure SQL. O servidor da firewall não dispõe de qualquer regra de endereço IP que permita a um cliente comunicar entre esse mesmo endereço IP e a Base de Dados SQL.

*Resolução de erros:* Insira o endereço IP do cliente como regra IP. Faça isso através do painel Firewall no portal do Azure.

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portal pode criar uma regra de rede virtual

Esta secção ilustra como pode utilizar o [portal Azure][http-azure-portal-link-ref-477t] para criar uma regra de *rede virtual* na sua Base de Dados Azure SQL. A regra diz à sua Base de Dados SQL para aceitar a comunicação de uma determinada subnet que foi marcada como sendo um ponto final de *serviço de Rede Virtual*.

> [!NOTE]
> Se pretender adicionar um ponto final de serviço às regras de firewall VNet do seu servidor de base de dados Azure SQL, certifique-se primeiro de que os pontos finais do serviço estão ligados para a subrede.
>
> Se os pontos finais do serviço não estiverem ligados para a subrede, o portal pede-lhe que os ative. Clique no botão **'Activar'** na mesma lâmina em que adiciona a regra.

## <a name="powershell-alternative"></a>Alternativa PowerShell

Um script também pode criar regras de rede virtuais usando powerShell cmdlet **New-AzSqlServerVirtualNetworkRule** ou [az network vnet criar](/cli/azure/network/vnet#az-network-vnet-create). Se estiver interessado, consulte o PowerShell para criar um ponto final de serviço de Rede Virtual e a regra para a Base de [Dados Azure SQL][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>Alternativa REST API

Internamente, os cmdlets PowerShell para as ações SQL VNet chamam APIs REST. Pode ligar diretamente para as APIs do REST.

- [Regras de Rede Virtual: Operações][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Pré-requisitos

Já deve ter uma sub-rede que esteja marcada com o nome final do tipo de ponto final do *serviço* de rede virtual relevante para a Base de Dados Azure SQL.

- O nome do tipo final relevante é **Microsoft.Sql**.
- Se a sua sub-rede não estiver marcada com o nome do tipo, consulte Verificar se a [sua sub-rede é um ponto final][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Passos do portal Azure

1. Inicie sessão no [portal do Azure][http-azure-portal-link-ref-477t].

2. Procure e selecione **servidores SQL**e, em seguida, selecione o seu servidor. Em **Segurança,** selecione **Firewalls e redes virtuais.**

3. Detete o controlo de **serviços Do IA** para OFF.

    > [!IMPORTANT]
    > Se deixar o conjunto de controlo definido para ON, o seu servidor de base de dados Azure SQL aceita a comunicação de qualquer subrede dentro do limite Azure, ou seja, originário de um dos endereços IP que é reconhecido como aqueles dentro dos intervalos definidos para centros de dados Azure. Deixar o controlo definido para on pode ser um acesso excessivo do ponto de vista de segurança. A funcionalidade de ponto final do serviço microsoft Azure Virtual Network, em coordenação com a função de regra de rede virtual da Base de Dados SQL, em conjunto pode reduzir a sua área de superfície de segurança.

4. Clique no + Adicionar o controlo **existente,** na secção **redes Virtuais.**

    ![Clique em adicionar o ponto final existente (ponto final da subnet, como regra SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. No novo painel **Create/Update,** preencha os controlos com os nomes dos seus recursos Azure.

    > [!TIP]
    > Deve incluir o **prefixo** de endereço correto para a sua sub-rede. Pode encontrar o valor no portal.
    > Navegue **todos os recursos** &gt; Todos os **tipos** &gt; **redes virtuais.** O filtro exibe as suas redes virtuais. Clique na sua rede virtual e, em seguida, clique em **Subnets**. A coluna **ADDRESS RANGE** tem o prefixo de endereço de que necessita.

    ![Preencha os campos para uma nova regra.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Clique no botão **OK** perto da parte inferior do painel.

7. Consulte a regra de rede virtual resultante no painel de firewall.

    ![Veja a nova regra, no painel de firewall.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Os seguintes estatutos ou Estados aplicam-se às regras:
> - **Pronto:** Indica que a operação que iniciou foi bem sucedida.
> - **Falhou:** Indica que a operação que iniciou falhou.
> - **Eliminado:** Aplica-se apenas à operação Delete e indica que a regra foi suprimida e já não se aplica.
> - **Inprogress:** Indica que a operação está em andamento. A velha regra aplica-se enquanto a operação está neste estado.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Artigos relacionados

- [Pontos finais do serviço de rede virtual Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Regras de firewall de nível de servidor e de nível de base de dados Azure SQL][sql-db-firewall-rules-config-715d]

A funcionalidade de regra da rede virtual para a Base de Dados Azure SQL ficou disponível no final de setembro de 2017.

## <a name="next-steps"></a>Passos seguintes

- [Utilize o PowerShell para criar um ponto final de serviço de rede virtual e, em seguida, uma regra de rede virtual para a Base de Dados Azure SQL.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Regras da Rede Virtual: Operações][rest-api-virtual-network-rules-operations-862r] com APIs REST

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]: ../expressroute/index.yml
[rbac-what-is-813s]:../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md
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
