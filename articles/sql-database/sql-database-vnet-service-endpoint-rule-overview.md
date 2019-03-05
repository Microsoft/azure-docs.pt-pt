---
title: VNet pontos finais e regras para únicos e em pool bases de dados SQL do Azure | Documentos da Microsoft
description: Marca uma sub-rede como um ponto de extremidade do serviço de rede Virtual. Em seguida, o ponto de extremidade como uma regra de rede virtual para a ACL de seu banco de dados do SQL do Azure. A base de dados SQL, em seguida, aceita comunicações de todas as máquinas virtuais e outros nós na sub-rede.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: vanto, genemi
manager: craigg
ms.date: 02/20/2019
ms.openlocfilehash: 15ca464e8e44183b445bfdabe9abf5dd560a4f70
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57312267"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>Utilizar pontos finais de serviço de rede virtual e regras para servidores de base de dados

*Regras de rede virtual* são um recurso de segurança de firewall que controla se o servidor de base de dados para as suas bases de dados individuais e o conjunto elástico no Azure [base de dados SQL](sql-database-technical-overview.md) ou nas bases de dados no [dados do SQL Armazém](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) aceita comunicações que são enviadas as sub-redes específicas nas redes virtuais. Este artigo explica por que o recurso de regra de rede virtual, às vezes, é sua melhor opção de forma segura que permite a comunicação para a sua base de dados do Azure SQL e SQL Data Warehouse.

> [!IMPORTANT]
> Este artigo aplica-se ao servidor SQL do Azure e a base de dados SQL e SQL Data Warehouse bases de dados que são criadas no servidor SQL do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse. Este artigo faz *não* aplicam-se a um **instância gerida** implementação na base de dados do Azure SQL porque não tem um ponto de extremidade de serviço associado ao mesmo.

Para criar uma regra de rede virtual, tem primeiro de existir uma [ponto final de serviço de rede virtual] [ vm-virtual-network-service-endpoints-overview-649d] para a regra fazer referência.

## <a name="how-to-create-a-virtual-network-rule"></a>Como criar uma regra de rede virtual

Se apenas criar uma regra de rede virtual, pode avançar diretamente para os passos e a explicação [mais adiante neste artigo](#anchor-how-to-by-using-firewall-portal-59j).

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologia e descrição

**Rede virtual:** Pode ter redes virtuais associadas à subscrição do Azure.

**Sub-rede:** Contém uma rede virtual **sub-redes**. Quaisquer máquinas virtuais (VMs) do Azure que tem são atribuídas a sub-redes. Uma sub-rede pode conter várias VMs ou outros nós de computação. Computação de nós que estão fora da sua rede virtual não é possível aceder à sua rede virtual, a menos que configura a sua segurança para permitir o acesso.

**Endpoint de serviço de rede virtual:** R [ponto final de serviço de rede Virtual] [ vm-virtual-network-service-endpoints-overview-649d] é uma sub-rede cujos valores de propriedade incluem um ou mais nomes de tipo de serviço do Azure formal. Neste artigo estamos interessados em nome do tipo de **Microsoft. SQL**, que faz referência ao serviço do Azure com o nome da base de dados SQL.

**Regra de rede virtual:** Uma regra de rede virtual para o servidor de base de dados SQL é uma sub-rede que está listada na lista de controle de acesso (ACL) do seu servidor de base de dados SQL. Para ser na ACL para base de dados SQL, a sub-rede tem de conter o **Microsoft. SQL** nome do tipo.

Uma regra de rede virtual informa ao seu servidor de base de dados SQL para aceitar comunicações de cada nó que está na sub-rede.

<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Benefícios de uma regra de rede virtual

Até que o faça, as VMs nas suas sub-redes não consegue comunicar com a base de dados SQL. Uma ação que estabelece a comunicação é a criação de uma regra de rede virtual. A lógica para escolher a abordagem de regra de VNet requer uma discussão de comparação e contraste que envolvem as opções de segurança concorrentes oferecidas pelo firewall.

### <a name="a-allow-access-to-azure-services"></a>R. Permitir acesso aos serviços do Azure

O painel de firewall tem um **ON/OFF** botão assinalada como **permitir o acesso aos serviços do Azure**. O **ON** definição permite comunicações a partir de todos os endereços IP do Azure e todas as sub-redes do Azure. Estes IPs do Azure ou a sub-redes não podem ser propriedade por si. Isso **ON** definição é provavelmente mais aberta que pretende que a base de dados do SQL ser. O recurso de regra de rede virtual oferece muito controle mais granular.

### <a name="b-ip-rules"></a>B. Regras de IP

A firewall de base de dados SQL permite-lhe especificar os intervalos de endereços IP do qual as comunicações são aceites na base de dados do SQL. Esta abordagem é adequada para os endereços IP estáveis que estão fora da rede privada do Azure. Mas muitos de nós dentro da rede privada do Azure estão configurados com *dinâmica* endereços IP. Endereços IP dinâmicos podem ser alteradas, por exemplo, quando a sua VM é reiniciada. Seria ilusório para especificar um endereço IP dinâmico numa regra de firewall, num ambiente de produção.

Pode recuperá-los a opção de IP, obtendo uma *estático* endereço IP para a sua VM. Para obter detalhes, consulte [configurar endereços IP privados para uma máquina virtual com o portal do Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

No entanto, a abordagem IP estática pode se tornar difíceis de gerenciar, e é dispendioso, quando Feito em escala. Regras de rede virtual são mais fáceis de estabelecer e gerir.

> [!NOTE]
> Ainda não pode ter a base de dados SQL numa sub-rede. Se o servidor de base de dados do Azure SQL era um nó numa sub-rede na sua rede virtual, todos os nós na rede virtual foi possível comunicar com a base de dados SQL. Neste caso, as suas VMs, foi possível comunicar com a base de dados SQL sem a necessidade de quaisquer regras IP ou regras de rede virtual.

No entanto a partir de Setembro de 2017, o serviço de base de dados do Azure SQL não é ainda entre os serviços que podem ser atribuídos a uma sub-rede.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Detalhes sobre regras de rede virtual

Esta secção descreve vários detalhes sobre regras de rede virtual.

### <a name="only-one-geographic-region"></a>Apenas uma região geográfica

Cada ponto de extremidade do serviço de rede Virtual aplica-se apenas numa região do Azure. O ponto final não ativa noutras regiões aceitar comunicações de sub-rede.

Qualquer regra de rede virtual é limitada à região que o seu ponto de extremidade subjacente aplica-se a.

### <a name="server-level-not-database-level"></a>Ao nível do servidor, não ao nível da base de dados

Cada regra de rede virtual aplica-se ao seu servidor de base de dados do Azure SQL completo, não apenas para um determinado banco de dados no servidor. Em outras palavras, a regra de rede virtual aplica-se no nível do servidor, não ao nível da base de dados.

- Por outro lado, as regras de IP podem aplicar em qualquer nível.

### <a name="security-administration-roles"></a>Funções de administração de segurança

Existe uma separação de funções de segurança na administração de pontos finais de serviço de rede Virtual. É necessária ação da cada uma das seguintes funções:

- **Administrador de rede:** &nbsp; Ative o ponto final.
- **Administrador da base de dados:** &nbsp; Atualize a lista de controlo de acesso (ACL) para adicionar a sub-rede especificada para o servidor de base de dados SQL.

*Alternativa RBAC:*

As funções de administrador de rede e o administrador de banco de dados têm mais recursos do que são necessárias para gerir as regras de rede virtual. É necessário apenas um subconjunto de seus recursos.

Tem a opção de usar [controlo de acesso baseado em funções (RBAC)] [ rbac-what-is-813s] no Azure para criar uma única função personalizada que tem apenas o subconjunto de capacidades necessário. A função personalizada poderia ser usada em vez de envolvendo o administrador de rede ou o administrador da base de dados. A área de superfície de sua exposição de segurança é mais baixa de se adicionar um utilizador a uma função personalizada, em vez de adicionar o utilizador para as outras duas funções de administrador principais.

> [!NOTE]
> Em alguns casos a base de dados do SQL do Azure e a sub-rede de VNet estão em subscrições diferentes. Nestes casos, deve verificar as seguintes configurações:
> - Ambas as subscrições têm de estar no mesmo inquilino do Azure Active Directory.
> - O utilizador tem as permissões necessárias para iniciar as operações, como ativar os pontos finais de serviço e adicionar uma sub-rede de VNet para o servidor especificado.
> - Ambas as subscrições tem de ter registado o fornecedor de Microsoft. SQL.

## <a name="limitations"></a>Limitações

Para a base de dados SQL do Azure, a funcionalidade de regras de rede virtual tem as seguintes limitações:

- Uma aplicação Web pode ser mapeada para um IP privado na VNet/subrede. Mesmo que os pontos finais de serviço são ativados da VNet/subrede determinado, ligações a partir da aplicação Web para o servidor terá uma origem de IP pública do Azure, não uma origem de VNet/subrede. Para ativar a conectividade de uma aplicação Web para um servidor que tem regras de firewall da VNet, deve **dos serviços do Azure permitem ao servidor de acesso** no servidor.

- Na firewall da base de dados SQL, cada regra de rede virtual faz referência a uma sub-rede. Todas essas sub-redes referenciadas tem de estar alojadas na mesma região geográfica que aloja a base de dados SQL.

- Cada servidor de base de dados do Azure SQL pode ter até 128 entradas ACL para qualquer determinada rede virtual.

- Regras de rede virtual são aplicadas apenas a redes virtuais do Azure Resource Manager; e não à [modelo de implementação clássica] [ arm-deployment-model-568f] redes.

- Pontos finais de serviço de rede virtual Diante de ativar a base de dados do Azure SQL também permite que os pontos finais para os serviços MySQL e PostgreSQL Azure. No entanto, com Diante de pontos de extremidade, tentativas para ligar a partir de pontos de extremidade às instâncias de MySQL ou PostgreSQL poderão falhar.
  - O motivo é que MySQL e PostgreSQL provável que não têm uma regra de rede virtual configurada. Tem de configurar uma regra de rede virtual para a base de dados do Azure para MySQL e PostgreSQL e a ligação serão bem sucedida.

- Na firewall, intervalos de endereços IP que se aplicam para os seguintes itens de rede, mas as regras de rede virtual não:
  - [Rede privada virtual (VPN) site a Site (S2S)][vpn-gateway-indexmd-608y]
  - No local através de [ExpressRoute][expressroute-indexmd-744v]

### <a name="considerations-when-using-service-endpoints"></a>Considerações sobre quando utilizar pontos finais de serviço

Quando utilizar pontos finais de serviço para a base de dados do Azure SQL, reveja as seguintes considerações:

- **Saída para IPs públicos do Azure SQL da base de dados é necessária**: Grupos de segurança de rede (NSGs) devem ser abertos para IPs de base de dados SQL do Azure para permitir a ligação. Pode fazê-lo ao utilizar o NSG [etiquetas de serviço](../virtual-network/security-overview.md#service-tags) para a base de dados do Azure SQL.

### <a name="expressroute"></a>ExpressRoute

Se estiver a utilizar [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) no local, para peering público ou peering da Microsoft, terá de identificar os endereços NAT IP que são utilizados. Para peering público, cada circuito ExpressRoute, por predefinição, utiliza dois endereços IP NAT que são aplicados ao tráfego de serviço do Azure quando o tráfego entra no backbone de rede do Microsoft Azure. Para peering da Microsoft, o(s) endereço(s) IP NAT que são utilizados são fornecidos pelo cliente ou são fornecidos pelo fornecedor de serviços. Para permitir o acesso aos recursos de serviço, tem de permitir estes endereços IP públicos na definição da firewall do IP dos recursos. Para localizar os endereços IP do circuito ExpressRoute de peering público, [abra um pedido de suporte no ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) através do portal do Azure. Saiba mais sobre [NAT para peering público e da Microsoft do ExpressRoute.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
Para permitir a comunicação do seu circuito para a base de dados do Azure SQL, tem de criar regras de rede IP para os endereços IP públicos de sua NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-azure-services-to-access-server"></a>Impacto da remoção de 'Serviços do Azure permitem que acedam ao servidor'

Número de utilizadores que pretende remover **dos serviços do Azure permitem ao servidor de acesso** de seus servidores de SQL do Azure e substituí-lo com uma regra de Firewall da VNet.
No entanto, a remover Isto afeta as seguintes funcionalidades:

### <a name="import-export-service"></a>Serviço de exportação de importação

O Azure SQL Database importar exportar Service é executada em VMs no Azure. Estas VMs não estão na sua VNet e, por conseguinte, obtém um IP do Azure ao ligar à base de dados. Na remoção **dos serviços do Azure permitem ao servidor de acesso** estas VMs não será possível aceder aos seus bancos de dados.
Pode contornar o problema. Executar a importação BACPAC ou exportar diretamente em seu código com a API de DACFx. Certifique-se de que isso é implementado numa VM que está na sub-rede de VNet para o qual tem de definir a regra de firewall.

### <a name="sql-database-query-editor"></a>Editor de consultas de base de dados SQL

O Editor de consultas de base de dados de SQL do Azure é implementado em VMs no Azure. Estas VMs não estão na sua VNet. Por conseguinte, as VMs obtém um IP do Azure ao ligar à base de dados. Na remoção **dos serviços do Azure permitem ao servidor de acesso**, essas VMs não será possível aceder aos seus bancos de dados.

### <a name="table-auditing"></a>Auditoria de tabelas

Neste momento, existem duas formas de ativar a auditoria na base de dados SQL. Auditoria de tabelas falha após ativar pontos finais de serviço no seu servidor SQL do Azure. Mitigação aqui é mover para auditoria de Blobs.

### <a name="impact-on-data-sync"></a>Impacto na sincronização de dados

Base de dados SQL do Azure tem a funcionalidade de sincronização de dados que se liga a suas bases de dados utilizam IPs do Azure. Quando utilizar pontos finais de serviço, é provável que irá desativar **dos serviços do Azure permitem ao servidor de acesso** acesso ao seu servidor de base de dados SQL. Tal irá interromper a funcionalidade de sincronização de dados.

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Impacto da utilização de pontos finais de serviço de VNet com o armazenamento do Azure

O armazenamento do Azure implementou a mesma funcionalidade que permite limitar a conectividade à sua conta de armazenamento do Azure. Se optar por utilizar esta funcionalidade com uma conta de armazenamento do Azure que está a ser utilizada pelo Azure SQL Server, poderá ter problemas. Em seguida, há uma lista e a discussão sobre recursos do Azure SQL Database e o Azure SQL Data Warehouse que são influenciados por isto.

### <a name="azure-sql-data-warehouse-polybase"></a>PolyBase do armazém de dados SQL do Azure

Normalmente é utilizar o PolyBase para carregar dados para o Azure SQL Data Warehouse a partir de contas de armazenamento do Azure. Se a conta de armazenamento do Azure que está a carregar dados de limite o acesso apenas a um conjunto de sub-redes da VNet, irá interromper a conectividade entre o PolyBase e a conta. Para ativar as duas PolyBase importar e exportar cenários com o Azure SQL Data Warehouse ao ligar ao armazenamento do Azure que está protegida a VNet, siga os passos indicados abaixo:

#### <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Instalar o Azure PowerShell através desta [guia](https://docs.microsoft.com/powershell/azure/install-az-ps).
2.  Se tiver uma conta de armazenamento para fins gerais v1 ou BLOBs, tem de atualizar primeiro para fins gerais v2 através desta [guia](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
3.  Tem de ter **permitir confiável a serviços da Microsoft para aceder a esta conta de armazenamento** ativada na conta de armazenamento do Azure **Firewalls e redes virtuais** menu definições. Consulte este [guia](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) para obter mais informações.
 
#### <a name="steps"></a>Passos
1.  No PowerShell, **registar o servidor de base de dados SQL** com o Azure Active Directory (AAD):

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionId your-subscriptionId
    Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
    ```
    
 1. Criar uma **para fins gerais v2 conta de armazenamento** usando essa [guia](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

    > [!NOTE]
    > - Se tiver uma conta de armazenamento para fins gerais v1 ou BLOBs, tem **atualizar primeiro para v2** usando essa [guia](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
    > - Para problemas conhecidos com geração 2 de armazenamento do Azure Data Lake, consulte este [guia](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues).
    
1.  Na sua conta de armazenamento, navegue até **controlo de acesso (IAM)** e clique em **adicionar atribuição de função**. Atribua **contribuinte de dados de Blob de armazenamento (pré-visualização)** função RBAC para o seu servidor de base de dados SQL.

    > [!NOTE] 
    > Apenas os membros com privilégios de proprietário podem efetuar este passo. Para várias funções incorporadas para recursos do Azure, consulte este [guia](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  
1.  **Polybase a conectividade à conta de armazenamento do Azure:**

    1. Criar uma base de dados **[chave mestra](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql?view=sql-server-2017)** se ainda não criou uma anteriormente:
        ```SQL
        CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
        ```
    
    1. Criar credencial com âmbito de base de dados com **IDENTITY = "Identidade do serviço gerido"**:

        ```SQL
        CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
        ```
        > [!NOTE] 
        > - Não é necessário especificar o segredo com chave de acesso de armazenamento do Azure porque usa esse mecanismo [identidade gerido](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) nos bastidores.
        > - Nome de identidade deve ser **"Identidade do serviço gerido"** para conectividade de PolyBase trabalhar com a conta de armazenamento do Azure obtida para a VNet.    
    
    1. Criar origem de dados externa com abfss: / / esquema para ligar à sua conta de armazenamento para fins gerais v2 com o PolyBase:

        ```SQL
        CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
        ```
        > [!NOTE] 
        > - Se já tiver associadas à conta de armazenamento para fins gerais v1 ou blob de tabelas externas, deve primeiro remover essas tabelas externas e, em seguida, remover a origem de dados externa correspondente. Em seguida, criar origem de dados externa com abfss: / / esquema ligar à conta de armazenamento para fins gerais v2 conforme apresentado acima e voltar a criar todas as tabelas externas usando essa nova fonte de dados externa. Poderia usar [gerar e o Assistente de publicação de Scripts](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard?view=sql-server-2017) para gerar scripts de criar para todas as tabelas externas para facilitar.
        > - Para obter mais informações sobre abfss: / / esquema, consulte este [guia](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri).
        > - Para obter mais informações sobre criar origem de dados externa, consulte este [guia](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql).
        
    1. Consulta como normal usando [tabelas externas](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Base de dados SQL do Azure a auditoria de BLOBs

Auditoria de BLOBs envia por push de registos de auditoria para a sua própria conta de armazenamento. Se esta conta de armazenamento utiliza a funcionalidade de pontos finais de serviço de VNet, em seguida, irá interromper a conectividade da base de dados do Azure SQL para a conta de armazenamento.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Adicionar uma regra de Firewall da VNet ao seu servidor sem ativar na VNet pontos finais de serviço

Há muito tempo, antes desta funcionalidade foi melhorada, era necessário para ativar a pontos finais de serviço de VNet antes de poderia implementar uma regra de VNet em direto na Firewall. Os pontos finais relacionados com uma determinada sub-rede de VNet para uma base de dados do SQL do Azure. Mas agora a partir de Janeiro de 2018, pode contornar este requisito, definindo a **IgnoreMissingVNetServiceEndpoint** sinalizador.

Simplesmente definir uma regra de Firewall não ajudam a proteger o servidor. Tem também de ativar pontos finais de serviço de VNet para garantir a segurança entrem em vigor. Quando ativar pontos finais de serviço, a sub-rede da VNet experiências de tempo de inatividade até concluir a transição de desativado para no. Isso é especialmente verdadeiro no contexto de VNets grandes. Pode utilizar o **IgnoreMissingVNetServiceEndpoint** sinalizador para reduzir ou eliminar o período de indisponibilidade durante a transição.

Pode definir o **IgnoreMissingVNetServiceEndpoint** sinalizador com o PowerShell. Para obter detalhes, consulte [PowerShell para criar um ponto final de serviço de rede Virtual e uma regra para o Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Erros 40914 e 40615

Erro de ligação 40914 está relacionado à *regras de rede virtual*, conforme especificado no painel de Firewall no portal do Azure. Erro 40615 é semelhante, exceto se relaciona com o *regras de endereços IP* na Firewall.

### <a name="error-40914"></a>Erro 40914

*Texto da mensagem:* Não é possível abrir o servidor de '*[nome do servidor]*"pedida pelo início de sessão. Cliente não tem permissão para aceder ao servidor.

*Descrição do erro:* O cliente está numa sub-rede que tem pontos finais do servidor de rede virtual. Mas o servidor de base de dados do Azure SQL não tem nenhuma regra de rede virtual que concede à sub-rede à direita para comunicar com a base de dados SQL.

*Resolução de erro:* No painel de Firewall do portal do Azure, utilize o controlo de regras de rede virtual para [adicionar uma regra de rede virtual](#anchor-how-to-by-using-firewall-portal-59j) para a sub-rede.

### <a name="error-40615"></a>Erro 40615

*Texto da mensagem:* Não é possível abrir o servidor '{0}"pedida pelo início de sessão. Cliente com o endereço IP{1}' não tem permissão para aceder ao servidor.

*Descrição do erro:* O cliente está a tentar ligar a partir de um endereço IP que não está autorizado a ligar ao servidor de base de dados do Azure SQL. O firewall do servidor não tem nenhuma regra de endereço IP que permite que um cliente de comunicação entre o endereço IP indicado para a base de dados SQL.

*Resolução de erro:* Introduza o endereço IP do cliente como uma regra de IP. Tal, utilize o painel de Firewall no portal do Azure.

Uma lista de várias mensagens de erro de base de dados SQL está documentada [aqui][sql-database-develop-error-messages-419g].

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portal pode criar uma regra de rede virtual

Esta secção ilustra como pode usar o [portal do Azure] [ http-azure-portal-link-ref-477t] para criar um *regra de rede virtual* na base de dados SQL do Azure. A regra diz ao seu banco de dados SQL para aceitar comunicações de uma sub-rede específica que tenha sido identificada como sendo um *ponto final de serviço de rede Virtual*.

> [!NOTE]
> Se pretender adicionar um ponto de extremidade de serviço para as regras de firewall da VNet do seu servidor de base de dados do Azure SQL, primeiro certifique-se de que o serviço pontos finais estão ativados para a sub-rede.
>
> Se a pontos finais de serviço não estão ativados para a sub-rede, o portal do pede-lhe para ativá-las. Clique nas **ativar** botão no painel do mesmo no qual adicionar a regra.

## <a name="powershell-alternative"></a>Alternativa de PowerShell

Um script do PowerShell, também pode criar regras de rede virtual. O cmdlet crucial **New-AzSqlServerVirtualNetworkRule**. Se estiver interessado, consulte [PowerShell para criar um ponto final de serviço de rede Virtual e uma regra para o Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>Alternativa de REST API

Internamente, os cmdlets do PowerShell para ações de VNet de SQL chamar as APIs REST. Pode chamar as APIs REST diretamente.

- [Regras de rede virtual: Operações][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Pré-requisitos

Já tem de ter uma sub-rede que está marcada com o ponto de extremidade de serviço de rede Virtual específico *nome do tipo* relevantes para a base de dados do Azure SQL.

- É o nome do tipo de ponto final relevante **Microsoft. SQL**.
- Se a sub-rede não pode ser marcada com o nome do tipo, consulte [Certifique-se de que a sub-rede é um ponto final][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Passos do portal do Azure

1. Inicie sessão no [Portal do Azure][http-azure-portal-link-ref-477t].

2. Em seguida, navegue até ao portal para **servidores SQL** &gt; **Firewall / redes virtuais**.

3. Definir o **permitir o acesso aos serviços do Azure** controle como OFF.

    > [!IMPORTANT]
    > Se deixar o controle definido como ON, o seu servidor de base de dados do Azure SQL aceita comunicações de qualquer sub-rede. Deixar o controle definido como ON, poderá ser excessivo acesso a partir de um ponto de vista da segurança. Em conjunto, a funcionalidade de ponto final de serviço de rede Virtual do Microsoft Azure, em coordenação com o recurso de regra de rede virtual da base de dados SQL, pode reduzir a área de superfície de segurança.

4. Clique no **+ adicionar existente** controlar, além da **redes virtuais** secção.

    ![Clique em add existente (sub-rede ponto final, como uma regra SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. No novo **Create/Update** painel, preencha os controles com os nomes dos seus recursos do Azure.

    > [!TIP]
    > Tem de incluir o correto **prefixo de endereço** para a sua sub-rede. Pode encontrar o valor no portal.
    > Navegue até **todos os recursos** &gt; **todos os tipos** &gt; **redes virtuais**. O filtro mostra suas redes virtuais. Clique em sua rede virtual e, em seguida, clique em **sub-redes**. O **intervalo de endereços** coluna tem o prefixo de endereço que precisa.

    ![Preencha os campos para nova regra.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Clique nas **OK** botão junto à parte inferior do painel.

7. Vê a regra de rede virtual resultante no painel da firewall.

    ![Ver a nova regra, no painel de firewall.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Os seguintes Estados ou Estados aplicam às regras:
> - **Pronto:** Indica que a operação iniciada foi concluída com êxito.
> - **Falhou:** Indica que a operação iniciada falhou.
> - **Eliminado:** Apenas aplica-se para a operação de eliminação e indica que a regra foi eliminada e já não se aplica.
> - **InProgress:** Indica que a operação está em curso. Se aplica a regra antiga enquanto a operação estiver neste estado.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Artigos relacionados

- [Pontos finais de serviço de rede virtual do Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Regras de firewall ao nível do servidor e ao nível da base de dados de base de dados SQL do Azure][sql-db-firewall-rules-config-715d]

O recurso de regra de rede virtual para o Azure SQL Database tornou-se disponível no final de Setembro de 2017.

## <a name="next-steps"></a>Passos Seguintes

- [Utilize o PowerShell para criar um ponto de extremidade do serviço de rede virtual e, em seguida, uma regra de rede virtual para a base de dados do Azure SQL.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Regras de rede virtual: Operations] [ rest-api-virtual-network-rules-operations-862r] com as APIs REST

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
