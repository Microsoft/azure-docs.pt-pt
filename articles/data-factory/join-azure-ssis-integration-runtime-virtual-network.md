---
title: Associar o runtime de integração do Azure-SSIS a uma rede virtual | Documentos da Microsoft
description: Saiba como associar o runtime de integração Azure-SSIS a uma rede virtual do Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/08/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 6c01232c9bdb685fbc54e5ebe1e1f9fa83073dc2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58107802"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Junte-se a um runtime de integração Azure-SSIS a uma rede virtual
Associe o runtime de integração (IR) Azure-SSIS a uma rede virtual do Azure nos seguintes cenários: 

- Pretende ligar a arquivos de dados no local a partir de pacotes de SSIS em execução no integration runtime do Azure-SSIS. 

- Está a alojar o SQL Server Integration Services (SSIS) catálogo da base de dados na base de dados do Azure SQL com pontos finais do serviço de rede virtual/gerida instância. 

  O Azure Data Factory permite-lhe associar o runtime de integração Azure-SSIS a uma rede virtual criada através do modelo de implementação clássica ou o modelo de implementação Azure Resource Manager. 

> [!IMPORTANT]
> A rede virtual clássica está atualmente a ser preterida, pelo que deve utilizar o Azure Resource Manager virtual de rede em vez disso.  Se já utilizar a rede virtual clássica, mude para utilizar a rede virtual do Azure Resource Manager logo que possível.

## <a name="access-to-on-premises-data-stores"></a>Acesso a arquivos de dados no local
Se os pacotes do SSIS acessar arquivos de dados única cloud pública, não precisa de associar o IR Azure-SSIS a uma rede virtual. Se os pacotes SSIS acessar arquivos de dados no local, tem de associar o IR Azure-SSIS a uma rede virtual que está ligada à rede no local. 

Aqui estão alguns pontos importantes a serem observados: 

- Se houver, não há nada a rede virtual ligada à sua rede no local, primeiro crie uma [rede virtual do Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) ou uma [rede virtual clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para a sua integração de SSIS do Azure tempo de execução para associar. Em seguida, configure uma site a site [ligação de gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) ou [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) ligação de rede virtual à sua rede no local. 

- Se houver uma existente do Azure Resource Manager ou a rede virtual clássica ligado à sua rede no local na mesma localização que o runtime de integração Azure-SSIS, pode associar o IR para essa rede virtual. 

- Se existir uma rede virtual clássica existente ligada à sua rede no local numa localização diferente a partir o runtime de integração Azure-SSIS, primeiro pode criar uma [rede virtual clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para o runtime de integração Azure-SSIS associar. Em seguida, configure uma [rede virtual clássica para clássica](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) ligação. Ou pode criar uma [rede virtual do Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para o runtime de integração Azure-SSIS associar. Em seguida, configure uma [rede virtual do clássico para o Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) ligação. 
 
- Se houver uma existente do Azure Resource Manager rede virtual ligada à sua rede no local numa localização diferente do que o runtime de integração Azure-SSIS, primeiro pode criar uma [rede virtual do Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) para o Azure-SSIS Runtime de integração para aderir. Em seguida, configure uma ligação de rede virtual do Azure Resource Manager para o Azure Resource Manager. Em alternativa, pode criar uma [rede virtual clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para o runtime de integração Azure-SSIS associar. Em seguida, configure uma [rede virtual do clássico para o Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) ligação. 

## <a name="host-the-ssis-catalog-database-in-azure-sql-database-with-virtual-network-service-endpointsmanaged-instance"></a>Alojar o catálogo de SSIS base de dados na base de dados do Azure SQL com pontos finais do serviço de rede virtual/gerida instância
Se o catálogo de SSIS está alojado na base de dados do Azure SQL com pontos finais de serviço de rede virtual ou instância gerida, pode associar o runtime de integração Azure-SSIS para: 

- Da mesma rede virtual 
- Uma rede virtual diferente que tenha uma ligação de rede de rede com aquele que é utilizado para a instância gerida 

Se alojar o seu catálogo SSIS na base de dados do Azure SQL com pontos finais de serviço de rede virtual, certifique-se de que se associe o runtime de integração Azure-SSIS à mesma rede virtual e sub-rede.

Se associar o runtime de integração Azure-SSIS à mesma rede virtual que a instância gerida, certifique-se de que o IR Azure-SSIS é numa sub-rede diferente, que a instância gerida. Se associar o runtime de integração Azure-SSIS a uma rede virtual diferente do que a instância gerida, recomendamos o peering de rede virtual (que está limitado à mesma região) ou uma rede virtual para a ligação de rede virtual. Ver [ligar a sua aplicação para a instância gerida da base de dados SQL do Azure](../sql-database/sql-database-managed-instance-connect-app.md).

Em todos os casos, a rede virtual só pode ser implementada por meio do modelo de implementação Azure Resource Manager.

As secções seguintes fornecem mais detalhes. 

## <a name="requirements-for-virtual-network-configuration"></a>Requisitos para configuração de rede virtual
-   Certifique-se de que `Microsoft.Batch` é um fornecedor registado na subscrição da sua sub-rede de rede virtual que aloja o ir Azure-SSIS. Se estiver a utilizar rede virtual clássica, também associar `MicrosoftAzureBatch` para a função de contribuinte de Máquina Virtual clássica para essa rede virtual. 

-   Certifique-se de que tem as permissões necessárias. Ver [permissões obrigatórias](#perms).

-   Selecione a sub-rede adequada para alojar o ir Azure-SSIS. Ver [selecione a sub-rede](#subnet). 

-   Se estiver a utilizar o seu próprio servidor de serviços de nomes de domínio (DNS) na rede virtual, veja [servidor de serviços de nome de domínio](#dns_server). 

-   Se estiver a utilizar um grupo de segurança de rede (NSG) na sub-rede, consulte o artigo [grupo de segurança de rede](#nsg) 

-   Se estiver usando o Express Route do Azure ou a configuração de rota definida pelo utilizador (UDR), veja [Use Azure ExpressRoute ou de utilizador definidas rotas](#route). 

-   Certifique-se de que o grupo de recursos da rede virtual pode criar e eliminar determinados recursos de rede do Azure. Ver [os requisitos para o grupo de recursos](#resource-group). 

Este é um diagrama que mostra as conexões necessárias para o runtime de integração Azure-SSIS:

![Runtime de integração Azure-SSIS](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a> Permissões necessárias

O utilizador que cria o Runtime de integração Azure-SSIS tem de ter as seguintes permissões:

- Se estiver ingressando no runtime de integração de SSIS a uma rede virtual do Azure Resource Manager, tem duas opções:

  - Utilizar o incorporado *contribuinte de rede* função. Esta função é fornecido com o _Microsoft.Network/\*_  permissão, que tem um âmbito muito maior do que o necessário.

  - Criar uma função personalizada que inclui apenas as informações necessárias _Microsoft.Network/virtualNetworks/\*/associação/ação_ permissão. 

- Se estiver ingressando no runtime de integração de SSIS a uma rede virtual clássica, recomendamos que utilize o incorporado *contribuinte de Máquina Virtual clássica* função. Caso contrário, terá de definir uma função personalizada que inclua a permissão para associar a rede virtual.

### <a name="subnet"></a> Selecione a sub-rede
-   Não selecione o GatewaySubnet para implementar um Runtime de integração Azure-SSIS, pois é dedicado para gateways de rede virtual. 

-   Certifique-se de que a sub-rede que selecionar tem suficiente espaço de endereços disponíveis para IR Azure-SSIS a utilizar. Mantenha, pelo menos, 2 * número de nós do Runtime de integração em endereços IP disponíveis. O Azure reserva alguns endereços IP em cada sub-rede e não não possível utilizar estes endereços. Os endereços IP primeiros e últimos das sub-redes são reservados para conformidade com o protocolo, juntamente com três outros endereços utilizados para serviços do Azure. Para obter mais informações, consulte [existem restrições sobre como utilizar endereços IP dentro destas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets). 

-   Não utilize uma sub-rede que exclusivamente está ocupada por outros serviços do Azure (por exemplo, a instância de gerida da base de dados do SQL, serviço de aplicações, etc.). 

### <a name="dns_server"></a> Domain Name Services server 
Se precisar de utilizar o seu próprio servidor de serviços de nomes de domínio (DNS) numa rede virtual associada ao seu runtime de integração Azure-SSIS, certifique-se de que consegue resolver nomes de anfitrião do Azure público (por exemplo, um nome de blob Storage do Azure, `<your storage account>.blob.core.windows.net`). 

Os seguintes passos são recomendados: 

-   Configure DNS personalizado para reencaminhar pedidos ao DNS do Azure. Pode reencaminhar os registos DNS não resolvidos para o endereço IP de resoluções de recursiva do Azure (168.63.129.16) no seu próprio servidor DNS. 

-   Configure o DNS personalizado como principal e o DNS do Azure como secundário para a rede virtual. Registe o endereço IP de resoluções de recursiva do Azure (168.63.129.16) como um servidor DNS secundário no caso de seu próprio servidor DNS não está disponível. 

Para mais informações, veja [resolução que utiliza o seu próprio servidor DNS de nomes](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a> Grupo de segurança de rede
Se precisar de implementar um grupo de segurança de rede (NSG) para a sub-rede utilizada pelo seu runtime de integração Azure-SSIS, permitir o tráfego de entrada/saída pelas seguintes portas: 

| Direção | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino | Intervalo de portas de destino | Comentários |
|---|---|---|---|---|---|---|
| Entrada | TCP | AzureCloud<br/>(ou um âmbito maior, como a Internet) | * | VirtualNetwork | 29876, 29877 (se associar o IR a uma rede virtual do Azure Resource Manager) <br/><br/>10100, 20100, 30100 (se associar o IR a uma rede virtual clássica)| O serviço Data Factory utiliza estas portas para comunicar com os nós do Azure-SSIS integration runtime na rede virtual. <br/><br/> Se criar um NSG de nível de sub-rede ou não, o Data Factory sempre configura um NSG no nível dos cartões de interface de rede (NICs) anexados às máquinas virtuais que alojam o ir Azure-SSIS. Apenas o tráfego de entrada a partir de endereços IP de fábrica de dados nas portas especificados é permitido por NSG nesse nível do NIC. Mesmo que abra estas portas para o tráfego de Internet ao nível da sub-rede, o tráfego a partir de endereços IP que não sejam endereços de IP da fábrica de dados é bloqueado ao nível do NIC. |
| Saída | TCP | VirtualNetwork | * | AzureCloud<br/>(ou um âmbito maior, como a Internet) | 443 | Os nós do Azure-SSIS integration runtime na rede virtual utilizam esta porta para aceder aos serviços do Azure, como o armazenamento do Azure e os Hubs de eventos do Azure. |
| Saída | TCP | VirtualNetwork | * | Internet | 80 | Os nós do Azure-SSIS integration runtime na rede virtual utilizam esta porta Transferir lista de revogação de certificados a partir da Internet. |
| Saída | TCP | VirtualNetwork | * | SQL<br/>(ou um âmbito maior, como a Internet) | 1433, 11000-11999, 14000-14999 | Os nós do Azure-SSIS integration runtime na utilização de rede virtual, estas portas para acessar o SSISDB alojadas pelo seu servidor de base de dados do Azure SQL - esse objetivo não é aplicável ao SSISDB alojado por instância gerida. |
||||||||

### <a name="route"></a> Utilizar o Azure ExpressRoute ou rotas definidas pelo utilizador
Pode ligar uma [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) circuito para a sua infraestrutura de rede virtual para expandir a sua rede no local para o Azure. 

Uma configuração comum é utilizar o túnel forçado (anunciar uma rota do BGP, 0.0.0.0/0 para a rede virtual) que força o tráfego de Internet de saída do fluxo de rede virtual para a aplicação de rede no local para inspeção e registo. Este fluxo de tráfego quebras de conectividade entre o IR Azure-SSIS na rede virtual com serviços dependentes do Azure Data Factory. A solução é definir uma (ou mais) [rotas definidas pelo utilizador (UDRs)](../virtual-network/virtual-networks-udr-overview.md) na sub-rede que contém o ir Azure-SSIS. Um UDR define rotas de sub-rede específica, que são respeitadas em vez da rota do BGP. 

Ou pode definir as rotas definidas pelo utilizador (UDRs) para forçar o tráfego de Internet de saída da sub-rede que aloja o runtime de integração de SSIS do Azure para outra sub-rede que aloja uma aplicação de rede Virtual como uma firewall ou um anfitrião de rede de Perímetro para inspeção e registo. 

Em ambos os casos, aplicar uma rota de 0.0.0.0/0 com o tipo de próximo salto como **Internet** na sub-rede que aloja o IR Azure-SSIS, para que a comunicação entre o serviço Data Factory e o IR do Azure-SSIS é pode ter êxito. 

![Adicionar uma rota](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Se estiver preocupado com perder a capacidade de inspecionar o tráfego de Internet de saída do que a sub-rede, também pode adicionar uma regra de NSG da sub-rede para restringir os destinos de saída para [endereços IP do Centro de dados do Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

Ver [este script do PowerShell](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c) para obter um exemplo. Tem de executar o script semanalmente para manter a lista de endereços IP de centro de dados do Azure atualizados. 

### <a name="resource-group"></a> Requisitos para o grupo de recursos
-   O IR Azure-SSIS tem de criar determinados recursos de rede no mesmo grupo de recursos que a rede virtual. Estes recursos incluem o seguinte:
    -   Um balanceador de carga do Azure, com o nome  *<Guid>cloudserviceloadbalancer - azurebatch*.
    -   Um endereço público do Azure IP, com o nome  *<Guid>cloudservicepublicip - azurebatch*.
    -   Um grupo de segurança de trabalho de rede, com o nome  *<Guid>cloudservicenetworksecuritygroup - azurebatch*. 

-   Certifique-se de que não tem nenhum bloqueio de recursos no grupo de recursos ou subscrição a que pertence a rede virtual. Se configurar um bloqueio só de leitura ou de um bloqueio de eliminação, iniciar e parar o runtime de integração podem falhar ou bloqueado. 

-   Certifique-se de que não tem uma política do Azure que impede que os seguintes recursos de que está sendo criado sob o grupo de recursos ou subscrição a que pertence a rede virtual: 
    -   Microsoft.Network/LoadBalancers 
    -   Microsoft.Network/NetworkSecurityGroups 
    -   Microsoft.Network/PublicIPAddresses 

## <a name="azure-portal-data-factory-ui"></a>Portal do Azure (IU do Data Factory)
Esta secção mostra como associar um runtime Azure-SSIS existente a uma rede virtual (clássico ou do Azure Resource Manager) com o portal do Azure e a IU do Data Factory. Em primeiro lugar, terá de configurar a rede virtual adequadamente antes de associar o runtime de integração Azure-SSIS à mesma. Aceda através de uma das duas secções seguintes com base no tipo de sua rede virtual (clássico ou do Azure Resource Manager). Em seguida, continue com a terceira seção para associar o runtime de integração Azure-SSIS para a rede virtual. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Utilizar o portal para configurar uma rede virtual do Azure Resource Manager
Tem de configurar uma rede virtual antes de pode associar um IR Azure-SSIS à mesma. 

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, a IU do Data Factory é suportado apenas nesses navegadores da web. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

1. Selecione **mais serviços**. Filtrar e selecione **redes virtuais**. 

1. Filtrar e selecione a rede virtual na lista. 

1. Sobre o **rede Virtual** página, selecione **propriedades**. 

1. Selecione o botão de cópia para **ID de recurso** para copiar o ID de recurso para a rede virtual para a área de transferência. Guarde o ID da área de transferência no OneNote ou um ficheiro. 

1. Selecione **sub-redes** no menu da esquerda. Certifique-se de que o número de **endereços disponíveis** é maior do que os nós no seu runtime de integração Azure-SSIS. 

1. Certifique-se de que o fornecedor do Azure Batch está registado na subscrição do Azure com a rede virtual. Em alternativa, registar o fornecedor do Azure Batch. Se já tiver uma conta do Azure Batch na sua subscrição, a sua subscrição está registada para o Azure Batch. (Se criar o IR Azure-SSIS no portal do Data Factory, o fornecedor do Azure Batch é registrado automaticamente para.) 

   a. No portal do Azure, selecione **subscrições** no menu da esquerda. 

   b. Selecione a sua subscrição. 

   c. Selecione **fornecedores de recursos** à esquerda e confirme se **batch** é um fornecedor registado. 

   ![Confirmação de estado "Registado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se não vir **batch** na lista, registrá-lo, [criar uma conta do Azure Batch vazia](../batch/batch-account-create-portal.md) na sua subscrição. Pode eliminá-la mais tarde. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Utilizar o portal para configurar uma rede virtual clássica
Tem de configurar uma rede virtual antes de pode associar um IR Azure-SSIS à mesma. 

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, a IU do Data Factory é suportado apenas nesses navegadores da web. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

1. Selecione **mais serviços**. Filtrar e selecione **redes virtuais (clássico)**. 

1. Filtrar e selecione a rede virtual na lista. 

1. Sobre o **rede Virtual (clássico)** página, selecione **propriedades**. 

   ![ID de recurso de rede virtual clássica](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Selecione o botão de cópia para **ID de recurso** para copiar o ID de recurso para a rede de clássico para a área de transferência. Guarde o ID da área de transferência no OneNote ou um ficheiro. 

1. Selecione **sub-redes** no menu da esquerda. Certifique-se de que o número de **endereços disponíveis** é maior do que os nós no seu runtime de integração Azure-SSIS. 

   ![Número de endereços disponíveis na rede virtual](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Junte-se **MicrosoftAzureBatch** para o **contribuinte de Máquina Virtual clássica** função para a rede virtual. 

    a. Selecione **controlo de acesso (IAM)** no menu à esquerda e selecione o **atribuições de funções** separador. 

    !["Controlo de acesso" e "Adicionar" botões](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Selecione **adicionar atribuição de função**.

    c. Sobre o **adicionar atribuição de função** página, selecione **contribuinte de Máquina Virtual clássica** para **função**. Colar **ddbf3205-c6bd-46ae-8127-60eb93363864** no **selecione** caixa e, em seguida, selecione **Microsoft Azure Batch** na lista de resultados da pesquisa. 

    ![Resultados da pesquisa na página "Adicionar atribuição de função"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. Selecione **guardar** para guardar as definições e fechar a página. 

    ![Guardar as definições de acesso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. Confirme que vê **Microsoft Azure Batch** na lista de colaboradores. 

    ![Confirmar o acesso do Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Certifique-se de que o fornecedor do Azure Batch está registado na subscrição do Azure com a rede virtual. Em alternativa, registar o fornecedor do Azure Batch. Se já tiver uma conta do Azure Batch na sua subscrição, a sua subscrição está registada para o Azure Batch. (Se criar o IR Azure-SSIS no portal do Data Factory, o fornecedor do Azure Batch é registrado automaticamente para.) 

   a. No portal do Azure, selecione **subscrições** no menu da esquerda. 

   b. Selecione a sua subscrição. 

   c. Selecione **fornecedores de recursos** à esquerda e confirme se **batch** é um fornecedor registado. 

   ![Confirmação de estado "Registado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se não vir **batch** na lista, registrá-lo, [criar uma conta do Azure Batch vazia](../batch/batch-account-create-portal.md) na sua subscrição. Pode eliminá-la mais tarde. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Associar o IR Azure-SSIS a uma rede virtual
1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, a IU do Data Factory é suportado apenas nesses navegadores da web. 

1. Na [portal do Azure](https://portal.azure.com), selecione **fábricas de dados** no menu da esquerda. Se não vir **fábricas de dados** no menu, selecione **mais serviços**e selecione **fábricas de dados** no **INTELIGÊNCIA + análise**secção. 

   ![Lista de fábricas de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Na lista, selecione a fábrica de dados com o runtime de integração Azure-SSIS. Ver a home page da sua fábrica de dados. Selecione o **criar e implementar** mosaico. Pode ver a IU do Data Factory num separador à parte. 

   ![Home page da fábrica de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Na IU do Data Factory, mude para o **edite** separador, selecione **ligações**e mude para o **Runtimes de integração** separador. 

   ![Separador de "Integration runtimes"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Se o runtime de integração Azure-SSIS está em execução, na lista de runtime de integração, selecione o **parar** botão no **ações** coluna para o ir Azure-SSIS. Não é possível editar um IR até, impedi-lo. 

   ![Parar o runtime de integração](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Na lista de runtime de integração, selecione o **edite** botão no **ações** coluna para o ir Azure-SSIS. 

   ![Editar o runtime de integração](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Sobre o **definições gerais** página do **configuração do Runtime de integração** janela, selecione **seguinte**. 

   ![Definições gerais para a configuração do Runtime de integração](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)

1. Sobre o **definições do SQL** página, introduza a palavra-passe de administrador e selecione **próxima**. 

   ![Definições do SQL Server para a configuração do Runtime de integração](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)

1. Sobre o **definições avançadas** página, efetue as seguintes ações: 

   a. Selecione a caixa de verificação **selecionar uma VNet para o Runtime de integração Azure-SSIS associar e permitir que os serviços do Azure para configurar permissões/definições da VNet**. 

   b. Para **tipo**, selecione se a rede virtual é uma rede virtual clássica ou uma rede virtual do Azure Resource Manager. 

   c. Para **nome da VNet**, selecione a rede virtual. 

   d. Para **nome da sub-rede**, selecione a sub-rede na rede virtual. 

   e. Clique em **validação de VNet** e se tiver êxito, clique em **atualização**. 

   ![Definições avançadas para a configuração do Runtime de integração](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. Agora, pode começar a resposta a incidentes, utilizando o **começar** botão no **ações** coluna para o ir Azure-SSIS. Demora cerca de 20 a 30 minutos para iniciar um ir Azure-SSIS. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>Configurar uma rede virtual
Tem de configurar uma rede virtual antes de pode associar o runtime de integração Azure-SSIS à mesma. Para configurar automaticamente permissões/definições de rede virtual para o runtime de integração Azure-SSIS associar a rede virtual, adicione o seguinte script:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Criar um IR Azure-SSIS e associe-a uma rede virtual
Pode criar um IR Azure-SSIS e associe-a uma rede virtual ao mesmo tempo. Para o script completo e informações, veja [criar um runtime de integração Azure-SSIS](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Associar um IR Azure-SSIS existente a uma rede virtual
O script na [criar um runtime de integração Azure-SSIS](create-azure-ssis-integration-runtime.md) artigo mostra-lhe como criar um IR Azure-SSIS e associe-a uma rede virtual no mesmo script. Se tiver um IR Azure-SSIS existentes, execute os seguintes passos para a associar à rede virtual: 
1. Parar o ir Azure-SSIS. 
1. Configure o IR Azure-SSIS para associar a rede virtual. 
1. Iniciar o ir Azure-SSIS. 

### <a name="define-the-variables"></a>Definir as variáveis
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Parar o IR Azure-SSIS
Pare o runtime de integração Azure-SSIS antes de poder associar a uma rede virtual. Este comando libera todos os nós e deixa de faturação:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Configurar definições de rede virtual para o runtime de integração Azure-SSIS associar
```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Configurar o IR Azure-SSIS
Para configurar o runtime de integração Azure-SSIS para associar a rede virtual, execute o `Set-AzDataFactoryV2IntegrationRuntime` comando: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Iniciar o IR Azure-SSIS
Para iniciar o runtime de integração Azure-SSIS, execute o seguinte comando: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

Este comando demora 20 a 30 minutos a concluir.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre o tempo de execução do Azure-SSIS, consulte os seguintes tópicos: 
- [Runtime de integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais sobre runtimes de integração em geral, incluindo o ir Azure-SSIS. 
- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo fornece instruções passo a passo para criar um ir Azure-SSIS. Ele usa o SQL Database do Azure para alojar o catálogo SSIS. 
- [Criar um integration runtime do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo é uma continuação do tutorial e fornece instruções sobre como utilizar a base de dados do Azure SQL com pontos finais do serviço de rede virtual/gerida instância para alojar o catálogo SSIS e associar o IR a uma rede virtual. 
- [Monitorizar um Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como obter informações sobre um IR Azure-SSIS e descrições dos estados nas informações devolvidas. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, iniciar ou remover um IR Azure-SSIS. Ele também mostra como aumentar horizontalmente o runtime de integração Azure-SSIS ao adicionar nós. 
