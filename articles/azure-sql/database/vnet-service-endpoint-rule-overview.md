---
title: Pontos finais de rede virtual e regras para bases de dados em Azure SQL Database
description: Marque uma sub-rede como um ponto final de serviço de rede virtual. Em seguida, adicione o ponto final como regra de rede virtual à ACL para a sua base de dados. A sua base de dados aceita então a comunicação de todas as máquinas virtuais e outros nós na sub-rede.
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
ms.openlocfilehash: 67e807e948caf1fec014457814c1b7f105630f9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784430"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Utilizar pontos finais de serviço e regras de rede virtual para servidores na Base de Dados SQL do Azure

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*As regras de rede virtual* são uma funcionalidade de segurança de firewall que controla se o servidor das suas bases de dados e piscinas elásticas na [Base de Dados Azure SQL](sql-database-paas-overview.md) ou para as bases de dados dedicadas sql (anteriormente SQL DW) no [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) aceita comunicações que são enviadas a partir de sub-redes específicas em redes virtuais. Este artigo explica porque é que as regras de rede virtual são, por vezes, a sua melhor opção para permitir a comunicação seguramente à sua base de dados na BASE de Dados SQL e na Azure Synapse Analytics.

> [!NOTE]
> Este artigo aplica-se tanto à BASE de Dados SQL como à Azure Synapse Analytics. Para simplificar, a *base de dados* de termo refere-se a ambas as bases de dados na Base de Dados SQL e Azure Synapse Analytics. Da mesma forma, quaisquer referências ao *servidor* referem-se ao [servidor lógico SQL](logical-servers.md) que acolhe a SQL Database e a Azure Synapse Analytics.

Para criar uma regra de rede virtual, deve primeiro existir um [ponto final de serviço de rede virtual][vm-virtual-network-service-endpoints-overview-649d] para a regra a referenciar.

## <a name="create-a-virtual-network-rule"></a>Criar uma regra de rede virtual

Se quiser criar apenas uma regra de rede virtual, pode adiantar-se aos passos e explicações [mais tarde neste artigo.](#anchor-how-to-by-using-firewall-portal-59j)

## <a name="details-about-virtual-network-rules"></a>Detalhes sobre regras de rede virtual

Esta secção descreve vários detalhes sobre as regras de rede virtual.

### <a name="only-one-geographic-region"></a>Apenas uma região geográfica

Cada ponto final de serviço de rede virtual aplica-se apenas a uma região de Azure. O ponto final não permite que outras regiões aceitem a comunicação a partir da sub-rede.

Qualquer regra de rede virtual limita-se à região a que se aplica o seu ponto final subjacente.

### <a name="server-level-not-database-level"></a>Nível de servidor, não nível de base de dados

Cada regra de rede virtual aplica-se a todo o servidor, e não apenas a uma base de dados específica no servidor. Por outras palavras, as regras de rede virtuais aplicam-se ao nível do servidor, não ao nível da base de dados.

Em contrapartida, as regras de PI podem aplicar-se a ambos os níveis.

### <a name="security-administration-roles"></a>Funções de administração de segurança

Há uma separação de papéis de segurança na administração de pontos finais de serviço de rede virtual. São necessárias medidas de cada uma das seguintes funções:

- **Administração de rede [(função de contribuinte de rede):](../../role-based-access-control/built-in-roles.md#network-contributor)** &nbsp; Ligue o ponto final.
- **Administração de dados [(função de contribuinte do servidor SQL):](../../role-based-access-control/built-in-roles.md#sql-server-contributor)** &nbsp; Atualize a lista de controlo de acesso (ACL) para adicionar a sub-rede dada ao servidor.

#### <a name="azure-rbac-alternative"></a>Alternativa Azure RBAC

Os papéis de Administração de Rede e Administração de Bases de Dados têm mais capacidades do que as necessárias para gerir as regras de rede virtuais. Apenas um subconjunto das suas capacidades é necessário.

Tem a opção de usar o controlo de acesso baseado em [funções (RBAC)][rbac-what-is-813s] em Azure para criar um único papel personalizado que tenha apenas o subconjunto de capacidades necessário. O papel personalizado poderia ser usado em vez de envolver o Administrador de Rede ou o Administrador de Base de Dados. A área de superfície da sua exposição à segurança é menor se adicionar um utilizador a uma função personalizada versus adicionar o utilizador às outras duas principais funções de administrador.

> [!NOTE]
> Em alguns casos, a base de dados na Base de Dados SQL e a sub-rede de rede virtual estão em diferentes subscrições. Nestes casos, deve assegurar as seguintes configurações:
>
> - Ambas as subscrições devem estar no mesmo inquilino do Azure Ative Directory (Azure AD).
> - O utilizador dispõe das permissões necessárias para iniciar operações, tais como ativar pontos finais de serviço e adicionar uma sub-rede de rede virtual ao servidor dado.
> - Ambas as subscrições devem ter o fornecedor Microsoft.Sql registado.

## <a name="limitations"></a>Limitações

Para a Base de Dados SQL, a funcionalidade de regras de rede virtual tem as seguintes limitações:

- Na firewall da sua base de dados na Base de Dados SQL, cada regra de rede virtual faz referência a uma sub-rede. Todas estas sub-redes referenciadas devem ser aloiadas na mesma região geográfica que acolhe a base de dados.
- Cada servidor pode ter até 128 entradas ACL para qualquer rede virtual.
- As regras de rede virtuais aplicam-se apenas às redes virtuais do Azure Resource Manager e não às redes [de modelos de implantação clássicas.][arm-deployment-model-568f]
- Ligar os pontos finais do serviço de rede virtual para a Base de Dados SQL também permite os pontos finais da Base de Dados Azure para o MySQL e a Azure Database para PostgreSQL. Com os pontos finais definidos para **ON,** as tentativas de ligação dos pontos finais à sua Base de Dados Azure para o MySQL ou Azure Database para instâncias postgreSQL podem falhar.
  - A razão subjacente é que a Base de Dados Azure para o MySQL e a Azure Database para PostgreSQL provavelmente não tem uma regra de rede virtual configurada. Tem de configurar uma regra de rede virtual para Azure Database para MySQL e Azure Database para PostgreSQL, e a ligação terá sucesso.
  - Para definir as regras de firewall de rede virtual num servidor lógico SQL que já está configurado com pontos finais privados, defina **o acesso da rede pública** ao **Nº**.
- Na firewall, as gamas de endereços IP aplicam-se aos seguintes itens de rede, mas as regras de rede virtuais não:
  - [Rede privada virtual local (S2S) (VPN)][vpn-gateway-indexmd-608y]
  - No local via [Azure ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-you-use-service-endpoints"></a>Considerações quando utiliza pontos finais de serviço

Quando utilizar os pontos finais de serviço para a Base de Dados SQL, reveja as seguintes considerações:

- **É necessária a saída para a Base de Dados Azure SQL, é necessária uma iPs pública.** Os grupos de segurança da rede (NSGs) devem ser abertos aos IPs da base de dados SQL para permitir a conectividade. Pode fazê-lo utilizando [etiquetas de serviço](../../virtual-network/network-security-groups-overview.md#service-tags) NSG para base de dados SQL.

### <a name="expressroute"></a>ExpressRoute

Se utilizar [o ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a partir das suas instalações, para espreitar publicamente ou para olhando a Microsoft, terá de identificar os endereços IP NAT utilizados. Para peering público, cada circuito ExpressRoute, por predefinição, utiliza dois endereços IP NAT que são aplicados ao tráfego de serviço do Azure quando o tráfego entra no backbone de rede do Microsoft Azure. Para o estojo da Microsoft, os endereços NAT IP utilizados são fornecidos pelo cliente ou pelo prestador de serviços. Para permitir o acesso aos recursos de serviço, tem de permitir estes endereços IP públicos na definição da firewall do IP dos recursos. Para localizar os endereços IP do circuito ExpressRoute de peering público, [abra um pedido de suporte no ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) através do portal do Azure. Para saber mais sobre o NAT para o público ExpressRoute e o microsoft espreitando, consulte [os requisitos da NAT para o azure peering público](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

Para permitir a comunicação do seu circuito até à Base de Dados SQL, tem de criar regras de rede IP para os endereços IP públicos do seu NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>Impacto da utilização de pontos finais de serviço de rede virtual com armazenamento Azure

O Armazenamento do Microsoft Azure implementou a mesma funcionalidade que lhe permite limitar a conectividade à conta do Armazenamento do Microsoft Azure. Se optar por utilizar esta funcionalidade com uma conta de Armazenamento Azure que a SQL Database está a utilizar, pode encontrar problemas. Segue-se uma lista e discussão das funcionalidades SQL Database e Azure Synapse Analytics que são afetadas por isso.

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Azure Synapse Analytics PolyBase e COPY statement

A PolyBase e a declaração COPY são comumente usadas para carregar dados em Azure Synapse Analytics a partir de Azure Storage contas para ingestão de dados de produção elevada. Se a conta de Armazenamento Azure que está a carregar dados de limites acessa apenas a um conjunto de sub-redes de rede virtuais, a conectividade quando utilizar o PolyBase e a declaração COPY para a conta de armazenamento quebrar-se-á. Para permitir cenários de importação e exportação utilizando COPY e PolyBase com Azure Synapse Analytics conectando-se ao Azure Storage que está seguro a uma rede virtual, siga os passos nesta secção.

#### <a name="prerequisites"></a>Pré-requisitos

- Instale a Azure PowerShell utilizando [este guia](/powershell/azure/install-az-ps).
- Se tiver uma conta de armazenamento V1 ou Azure Blob de propósito geral, deve primeiro atualizar para v2 para fins gerais, seguindo os passos de [Upgrade para uma conta de armazenamento V2 para fins gerais](../../storage/common/storage-account-upgrade.md).
- Deve ter **permitido que serviços fidedignos da Microsoft acedam a esta conta de armazenamento** ligada no menu de **firewalls e configurações** de redes virtuais da conta Azure. Ativar esta configuração permitirá que a PolyBase e a declaração COPY se conectem à conta de armazenamento utilizando uma autenticação forte onde o tráfego de rede permanece na espinha dorsal do Azure. Para mais informações, consulte [este guia.](../../storage/common/storage-network-security.md#exceptions)

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela BASE de Dados SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de erros até pelo menos dezembro de 2020. Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre a sua compatibilidade, consulte [a introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Passos

1. Se tiver uma piscina SQL dedicada autónoma, registe o seu servidor SQL com Azure AD utilizando o PowerShell:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Este passo não é necessário para piscinas SQL dedicadas dentro de um espaço de trabalho Azure Synapse Analytics.

1. Se tiver um espaço de trabalho Azure Synapse Analytics, registe a identidade gerida pelo sistema do seu espaço de trabalho:

   1. Vá ao seu espaço de trabalho Azure Synapse Analytics no portal Azure.
   2. Vá ao painel **de identidades geridas.**
   3. Certifique-se de que a opção **Permitir gasodutos** está ativada.
   
1. Criar uma **conta de armazenamento v2 para fins gerais** seguindo os passos na [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md).

   > [!NOTE]
   >
   > - Se tiver uma conta de armazenamento v1 ou Blob para fins gerais, tem primeiro de *atualizar para v2* seguindo os passos de [Upgrade para uma conta de armazenamento V2 para fins gerais](../../storage/common/storage-account-upgrade.md).
   > - Para questões conhecidas com a Azure Data Lake Storage Gen2, consulte [questões conhecidas com a Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-known-issues.md).

1. Na sua conta de armazenamento, aceda ao **Controlo de Acesso (IAM)** e selecione **Adicionar a atribuição de funções**. Atribua o papel Azure **do Colaborador de Dados de Armazenamento** ao servidor ou espaço de trabalho que hospeda a sua piscina DE SQL dedicada, que registou no Azure AD.

   > [!NOTE]
   > Apenas os membros com privilégio proprietário na conta de armazenamento podem realizar este passo. Para vários papéis embutidos em Azure, consulte [as funções incorporadas do Azure.](../../role-based-access-control/built-in-roles.md)
  
1. Para ativar a conectividade PolyBase na conta de Armazenamento Azure:

   1. Crie uma [chave principal](/sql/t-sql/statements/create-master-key-transact-sql) de base de dados se não tiver criado uma antes.

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Criar uma credencial de base de dados com **IDENTIDADE = 'Identidade de serviço gerida'.**

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Não há necessidade de especificar SECRET com uma chave de acesso Azure Storage porque este mecanismo utiliza [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md) sob as capas.
       > - O nome IDENTITY deve ser **"Identidade de serviço gerido"** para a conectividade PolyBase para trabalhar com uma conta de Armazenamento Azure protegida a uma rede virtual.

   1. Crie uma fonte de dados externa com o `abfss://` esquema de ligação à sua conta de armazenamento v2 de uso da PoliBase.

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Se já tiver tabelas externas associadas a uma conta v1 ou Blob De uso geral, deve primeiro deixar cair essas tabelas externas. Em seguida, deixe cair a fonte de dados externa correspondente. Em seguida, crie uma fonte de dados externa com o `abfss://` esquema que se conecta a uma conta de armazenamento v2 de uso geral, como anteriormente mostrado. Em seguida, reumo todas as tabelas externas utilizando esta nova fonte de dados externo. Pode utilizar o [''' Gerar e Publicar Scripts Wizard'](/sql/ssms/scripting/generate-and-publish-scripts-wizard) para gerar scripts de criação para todas as tabelas externas para facilitar.
       > - Para obter mais informações sobre o `abfss://` esquema, consulte [o Azure Data Lake Storage Gen2 URI](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md).
       > - Para obter mais informações sobre CREATE EXTERNAL DATA SOURCE, consulte [este guia.](/sql/t-sql/statements/create-external-data-source-transact-sql)

   1. Consulta normal utilizando [tabelas externas.](/sql/t-sql/statements/create-external-table-transact-sql)

### <a name="sql-database-blob-auditing"></a>Auditoria de blob sql database

A auditoria blob empurra os registos de auditoria para a sua própria conta de armazenamento. Se esta conta de armazenamento utilizar a funcionalidade de pontos finais do serviço de rede virtual, a conectividade da Base de Dados SQL para a conta de armazenamento quebrar-se-á.

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>Adicione uma regra de firewall de rede virtual ao seu servidor

Há muito tempo, antes de esta funcionalidade ser melhorada, era-lhe exigido que ligasse os pontos finais do serviço de rede virtual antes de poder implementar uma regra de rede virtual ao vivo na firewall. Os pontos finais diziam respeito a uma sub-rede de rede virtual a uma base de dados na Base de Dados SQL. A partir de janeiro de 2018, pode contornar este requisito definindo a bandeira **IgnoreMissingVNetServiceEndpoint.** Agora, pode adicionar uma regra de firewall de rede virtual ao seu servidor sem ligar os pontos finais do serviço de rede virtual.

Apenas definir uma regra de firewall não ajuda a proteger o servidor. Também deve ligar os pontos finais do serviço de rede virtual para que a segurança produza efeitos. Quando liga os pontos finais de serviço, a sua sub-rede de rede virtual experimenta tempo de inatividade até completar a transição de desligado para ligado. Este período de inatividade é especialmente verdadeiro no contexto das grandes redes virtuais. Pode utilizar a bandeira **IgnoreMissingVNetServiceEndpoint** para reduzir ou eliminar o tempo de inatividade durante a transição.

Pode configurar a bandeira **IgnoreMissingVNetServiceEndpoint** utilizando o PowerShell. Para obter mais informações, consulte [o PowerShell para criar um ponto final de serviço de rede virtual e regra para a Base de Dados SQL][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Erros 40914 e 40615

O erro de ligação 40914 diz respeito às *regras de rede virtuais,* conforme especificado no painel **firewall** do portal Azure. O erro 40615 é semelhante, exceto que diz respeito às *regras* de endereço IP na firewall.

### <a name="error-40914"></a>Erro 40914

**Texto de mensagem:** "Não é possível abrir o servidor *[ nome do servidor]*' solicitado pelo login. O cliente não está autorizado a aceder ao servidor."

**Descrição do erro:** O cliente está numa sub-rede que tem pontos finais de servidor de rede virtual. Mas o servidor não tem nenhuma regra de rede virtual que conceda à sub-rede o direito de comunicar com a base de dados.

**Resolução de erros:** No painel **firewall** do portal Azure, utilize o controlo de regras de rede virtual para [adicionar uma regra de rede virtual](#anchor-how-to-by-using-firewall-portal-59j) para a sub-rede.

### <a name="error-40615"></a>Erro 40615

**Texto de mensagem:** "Não é possível abrir {0} o servidor" solicitado pelo login. O cliente com endereço IP {1} ' não está autorizado a aceder ao servidor."

**Descrição do erro:** O cliente está a tentar ligar-se a partir de um endereço IP que não está autorizado a ligar-se ao servidor. A firewall do servidor não tem nenhuma regra de endereço IP que permita a um cliente comunicar a partir do endereço IP dado para a base de dados.

**Resolução de erros:** Insira o endereço IP do cliente como regra ip. Utilize o painel **de firewall** no portal Azure para fazer este passo.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>Use o portal para criar uma regra de rede virtual

Esta secção ilustra como pode utilizar o [portal Azure][http-azure-portal-link-ref-477t] para criar uma *regra de rede virtual* na sua base de dados na Base de Dados SQL. A regra diz à sua base de dados para aceitar a comunicação de uma determinada sub-rede que foi marcada como sendo um *ponto final de serviço de rede virtual*.

> [!NOTE]
> Se pretender adicionar um ponto final de serviço às regras de firewall de rede virtual do seu servidor, certifique-se primeiro de que os pontos finais de serviço estão ligados para a sub-rede.
>
> Se os pontos finais de serviço não estiverem ligados para a sub-rede, o portal pede-lhe para os ativar. Selecione o botão **Ativar** no mesmo painel em que adiciona a regra.

## <a name="powershell-alternative"></a>Alternativa PowerShell

Um script também pode criar regras de rede virtuais utilizando o cmdlet PowerShell **New-AzSqlServerVirtualNetworkRule** ou [vnet de rede az.](/cli/azure/network/vnet#az_network_vnet_create) Se estiver interessado, consulte [o PowerShell para criar um ponto final de serviço de rede virtual e regra para a Base de Dados SQL][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>Rest API alternativa

Internamente, os cmdlets PowerShell para ações de rede virtual SQL chamam APIs de REST. Pode ligar diretamente para as APIs REST.

- [Regras de rede virtual: Operações][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Pré-requisitos

Já deve ter uma sub-rede que está marcada com o nome de *tipo de ponto* final de serviço de rede virtual relevante para a Base de Dados SQL.

- O nome do tipo ponto final relevante é **Microsoft.Sql**.
- Se a sua sub-rede não estiver marcada com o nome do tipo, consulte [Verifique se a sua sub-rede é um ponto final][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Passos do portal Azure

1. Inicie sessão no [portal do Azure][http-azure-portal-link-ref-477t].

1. Procure e selecione **servidores SQL** e, em seguida, selecione o seu servidor. Em **Segurança**, selecione **Firewalls e redes virtuais**.

1. Definir **Deixe o acesso aos serviços Azure** para **OFF**.

    > [!IMPORTANT]
    > Se deixar o controlo definido para **ON,** o seu servidor aceita a comunicação a partir de qualquer sub-rede dentro do limite Azure. Isto é comunicação que se origina de um dos endereços IP que é reconhecido como aqueles dentro dos intervalos definidos para centros de dados Azure. Deixar o controlo definido para **ON** pode ser um acesso excessivo do ponto de vista de segurança. A funcionalidade de ponto final do serviço de rede virtual Microsoft Azure em coordenação com a funcionalidade de regras de rede virtual da SQL Database em conjunto pode reduzir a sua área de superfície de segurança.

1. **Selecione + Adicione a existência** na secção redes **Virtuais.**

    ![Screenshot que mostra a seleção + Adicionar o ponto final existente (ponto final da sub-rede, como regra SQL).][image-portal-firewall-vnet-add-existing-10-png]

1. No novo painel **Criar/Atualizar,** preencha as caixas com os nomes dos seus recursos Azure.

    > [!TIP]
    > Tem de incluir o prefixo de endereço correto para a sua sub-rede. Pode encontrar o valor **do prefixo do Endereço** no portal. Ir a **todos os recursos** Todos os &gt; **tipos** de &gt; **redes virtuais.** O filtro exibe as suas redes virtuais. Selecione a sua rede virtual e, em seguida, selecione **Subnets**. A coluna **ADDRESS RANGE** tem o prefixo de endereço de que necessita.

    ![Screenshot que mostra preencher caixas para a nova regra.][image-portal-firewall-create-update-vnet-rule-20-png]

1. Selecione o botão **OK** perto da parte inferior do painel.

1. Consulte a regra da rede virtual resultante no painel **de firewall.**

    ![Screenshot que mostra a nova regra no painel de firewall.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Os seguintes estatutos ou estados aplicam-se às regras:
>
> - **Pronto**: Indica que a operação iniciada foi bem sucedida.
> - **Falha:** Indica que a operação iniciada falhou.
> - **Suprimimento**: Aplica-se apenas à operação Eliminar e indica que a regra foi eliminada e já não se aplica.
> - **InProgress**: Indica que a operação está em curso. A regra antiga aplica-se enquanto a operação está neste estado.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Artigos relacionados

- [Pontos finais de serviço de rede virtual Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Regras de firewall ao nível do servidor e de nível de base de dados][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Passos seguintes

- [Use o PowerShell para criar um ponto final de serviço de rede virtual e, em seguida, uma regra de rede virtual para base de dados SQL][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Regras de rede virtual: Operações][rest-api-virtual-network-rules-operations-862r] com APIs REST

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
