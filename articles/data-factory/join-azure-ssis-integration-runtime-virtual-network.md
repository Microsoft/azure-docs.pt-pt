---
title: Unir um tempo de execução de integração do Azure-SSIS a uma rede virtual | Microsoft Docs
description: Saiba como unir um tempo de execução de integração do Azure-SSIS a uma rede virtual do Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 92687b7cb8cdad8612f5a44833efcca351c45a43
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114747"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Unir um tempo de execução de integração do Azure-SSIS a uma rede virtual
Ao usar o SQL Server Integration Services (SSIS) no Azure Data Factory, você deve unir seu IR (Integration Runtime) do Azure-SSIS a uma rede virtual do Azure nos seguintes cenários: 

- Você deseja se conectar a armazenamentos de dados locais de pacotes do SSIS que são executados no seu Azure-SSIS IR sem configurar ou gerenciar um IR autohospedado como um proxy. 

- Você deseja se conectar aos recursos de serviço do Azure com suporte com pontos de extremidade de serviço de rede virtual de pacotes do SSIS que são executados no seu Azure-SSIS IR.

- Você está hospedando um SSISDB (banco de dados de catálogo do SSIS) no banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual ou instância gerenciada em uma rede virtual. 

Data Factory permite que você ingresse seu Azure-SSIS IR em uma rede virtual criada por meio do modelo de implantação clássico ou do modelo de implantação de Azure Resource Manager. 

> [!IMPORTANT]
> A rede virtual clássica está sendo preterida, portanto, use a rede virtual Azure Resource Manager em vez disso.  Se você já usa a rede virtual clássica, alterne para a rede virtual Azure Resource Manager assim que possível.

## <a name="access-to-on-premises-data-stores"></a>Acesso a armazenamentos de dados locais
Se seus pacotes SSIS acessarem armazenamentos de dados locais, você poderá ingressar seu Azure-SSIS IR em uma rede virtual que esteja conectada à rede local. Ou você pode configurar ou gerenciar um IR auto-hospedado como um proxy para seu Azure-SSIS IR. Para obter mais informações, consulte [configurar um ir auto-hospedado como um proxy para um Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

Ao ingressar seu Azure-SSIS IR em uma rede virtual, lembre-se destes pontos importantes: 

- Se nenhuma rede virtual estiver conectada à sua rede local, primeiro crie uma [rede virtual Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para que seu Azure-SSIS ir ingresse. Em seguida, configure uma [conexão de gateway de VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) site a site ou conexão de [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) dessa rede virtual para sua rede local. 

- Se uma rede virtual Azure Resource Manager já estiver conectada à sua rede local no mesmo local que o Azure-SSIS IR, você poderá unir o IR à rede virtual. 

- Se uma rede virtual clássica já estiver conectada à sua rede local em um local diferente do seu Azure-SSIS IR, você poderá criar uma [rede virtual Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para que seu Azure-SSIS ir ingresse. Em seguida, configure uma conexão de [rede virtual clássica para Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) . 
 
- Se uma rede virtual Azure Resource Manager já estiver conectada à sua rede local em um local diferente do seu Azure-SSIS IR, você poderá primeiro criar uma [rede virtual Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) para sua Azure-SSIS ir para ingressar. Em seguida, configure uma conexão de rede virtual de Azure Resource Manager para Azure Resource Manager. 

## <a name="access-to-azure-services"></a>Acesso aos serviços do Azure
Se os pacotes do SSIS acessarem os recursos de serviço do Azure com suporte nos [pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) e você quiser proteger esses recursos para Azure-SSIS ir, você poderá ingressar o Azure-SSIS ir na sub-rede da rede virtual configurada com a rede virtual pontos de extremidade de serviço. Enquanto isso, adicione uma regra de rede virtual aos recursos de serviço do Azure para permitir o acesso da mesma sub-rede.

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hospedando o catálogo do SSIS no banco de dados SQL
Se você hospedar seu catálogo do SSIS no banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual, certifique-se de ingressar seu Azure-SSIS IR na mesma rede virtual e sub-rede.

Para unir seu Azure-SSIS IR à mesma rede virtual que a instância gerenciada, verifique se o Azure-SSIS IR está em uma sub-rede diferente da instância gerenciada. Para unir seu Azure-SSIS IR a uma rede virtual diferente da instância gerenciada, recomendamos o emparelhamento de rede virtual (que é limitado à mesma região) ou uma conexão da rede virtual à rede virtual. Para obter mais informações, consulte [conectar seu aplicativo à instância gerenciada do banco de dados SQL do Azure](../sql-database/sql-database-managed-instance-connect-app.md).

Em todos os casos, a rede virtual pode ser implantada somente por meio do modelo de implantação Azure Resource Manager.

As seções a seguir fornecem mais detalhes. 

## <a name="virtual-network-configuration"></a>Configuração de rede virtual

Configure sua rede virtual para atender a estes requisitos: 

-   Verifique se o `Microsoft.Batch` é um provedor registrado na assinatura da sub-rede da sua rede virtual que hospeda o Azure-SSIS ir. Se você usar uma rede virtual clássica, ingresse `MicrosoftAzureBatch` também na função de colaborador da máquina virtual clássica para essa rede virtual. 

-   Verifique se você tem as permissões necessárias. Para obter mais informações, consulte [configurar permissões](#perms).

-   Selecione a sub-rede apropriada para hospedar o Azure-SSIS IR. Para obter mais informações, consulte [selecionar a sub-rede](#subnet). 

-   Se você usar seu próprio servidor DNS (sistema de nomes de domínio) na rede virtual, consulte [Configurar o servidor DNS](#dns_server). 

-   Se você usar um NSG (grupo de segurança de rede) na sub-rede, consulte [configurar um NSG](#nsg). 

-   Se você usar o Azure ExpressRoute ou uma UDR (rota definida pelo usuário), consulte [usar o Azure expressroute ou um UDR](#route). 

-   Verifique se o grupo de recursos da rede virtual pode criar e excluir determinados recursos de rede do Azure. Para obter mais informações, consulte [Configurar o grupo de recursos](#resource-group). 

-   Se você personalizar seu Azure-SSIS IR conforme descrito em [instalação personalizada para Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup), seus nós de Azure-SSIS ir obterão endereços IP privados de um intervalo predefinido de 172.16.0.0 para 172.31.255.255. Portanto, certifique-se de que os intervalos de endereços IP privados de suas redes virtuais ou locais não colidem com esse intervalo.

Este diagrama mostra as conexões necessárias para seu Azure-SSIS IR:

![Runtime de integração Azure-SSIS](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>Configurar permissões

O usuário que cria a Azure-SSIS IR deve ter as seguintes permissões:

- Se você estiver unindo o IR do SSIS a uma rede virtual Azure Resource Manager, terá duas opções:

  - Use a função de colaborador de rede interna. Essa função vem com a permissão _Microsoft. Network\* /_ , que tem um escopo muito maior do que o necessário.

  - Crie uma função personalizada que inclua apenas a permissão _Microsoft. Network/virtualNetworks/\*/Join/Action_ necessária. 

- Se você estiver unindo o IR do SSIS a uma rede virtual clássica, recomendamos que você use a função de colaborador de máquina virtual clássica interna. Caso contrário, você precisa definir uma função personalizada que inclui a permissão para ingressar na rede virtual.

### <a name="subnet"></a>Selecionar a sub-rede

Ao escolher uma sub-rede: 

-   Não selecione GatewaySubnet para implantar um Azure-SSIS IR. Ele é dedicado a gateways de rede virtual. 

-   Verifique se a sub-rede selecionada tem espaço de endereço disponível suficiente para o Azure-SSIS IR usar. Deixe os endereços IP disponíveis pelo menos duas vezes o número do nó IR. O Azure reserva alguns endereços IP em cada sub-rede. Esses endereços não podem ser usados. O primeiro e o último endereços IP das sub-redes são reservados para a conformidade do protocolo e mais três endereços são usados para os serviços do Azure. Para obter mais informações, consulte [existem restrições sobre como usar endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

-   Não use uma sub-rede que esteja exclusivamente ocupada por outros serviços do Azure (por exemplo, instância gerenciada do banco de dados SQL, serviço de aplicativo e assim por diante). 

### <a name="dns_server"></a>Configurar o servidor DNS 
Se você precisar usar seu próprio servidor DNS em uma rede virtual unida pelo seu Azure-SSIS IR, verifique se ele pode resolver nomes de host do Azure globais (por exemplo, um blob de armazenamento `<your storage account>.blob.core.windows.net`do Azure denominado). 

As etapas a seguir são recomendadas: 

-   Configure o DNS personalizado para encaminhar solicitações para o DNS do Azure. Você pode encaminhar registros DNS não resolvidos para o endereço IP dos resolvedores recursivos do Azure (168.63.129.16) em seu próprio servidor DNS. 

-   Configure o DNS personalizado como o servidor DNS primário para a rede virtual. Configure o DNS do Azure como o servidor DNS secundário. Registre o endereço IP dos resolvedores recursivos do Azure (168.63.129.16) como um servidor DNS secundário caso seu próprio servidor DNS não esteja disponível. 

Para obter mais informações, consulte [resolução de nomes que usa seu próprio servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a>Configurar um NSG
Se você precisar implementar um NSG para a sub-rede usada pelo seu Azure-SSIS IR, permita o tráfego de entrada e de saída por meio das seguintes portas: 

| Direction | Protocolo de transporte | Source | Intervalo de portas de origem | Destino | Destination port range | Comentários |
|---|---|---|---|---|---|---|
| Entrada | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (se você unir o IR a uma rede virtual do Resource Manager) <br/><br/>10100, 20100, 30100 (se você unir o IR a uma rede virtual clássica)| O serviço de Data Factory usa essas portas para se comunicar com os nós de sua Azure-SSIS IR na rede virtual. <br/><br/> Se você criar ou não um NSG no nível de sub-rede, Data Factory sempre configurará um NSG no nível das NICs (placas de interface de rede) conectadas às máquinas virtuais que hospedam o Azure-SSIS IR. Somente o tráfego de entrada de endereços IP Data Factory nas portas especificadas é permitido por esse NSG de nível de NIC. Mesmo se você abrir essas portas para o tráfego de Internet no nível de sub-rede, o tráfego de endereços IP que não são Data Factory endereços IP é bloqueado no nível de NIC. |
| Saída | TCP | VirtualNetwork | * | AzureCloud | 443 | Os nós de seu Azure-SSIS IR na rede virtual usam essa porta para acessar os serviços do Azure, como o armazenamento do Azure e os hubs de eventos do Azure. |
| Saída | TCP | VirtualNetwork | * | Internet | 80 | Os nós de seu Azure-SSIS IR na rede virtual usam essa porta para baixar uma lista de certificados revogados da Internet. |
| Saída | TCP | VirtualNetwork | * | SQL | 1433, 11000-11999 | Os nós de seu Azure-SSIS IR na rede virtual usam essas portas para acessar um SSISDB hospedado pelo servidor do banco de dados SQL. Se a política de conexão do servidor do banco de dados SQL estiver definida como **proxy** em vez de redirecionar, somente a porta 1433 será necessária. Essa regra de segurança de saída não é aplicável a um SSISDB hospedado por sua instância gerenciada na rede virtual. |
||||||||

### <a name="route"></a>Usar o Azure ExpressRoute ou um UDR
Quando você conecta um circuito [do Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) à sua infraestrutura de rede virtual para estender sua rede local para o Azure, uma configuração comum usa o túnel forçado (anunciando uma rota BGP, 0.0.0.0/0, para a rede virtual). Esse túnel força o tráfego de saída da Internet do fluxo de rede virtual para um dispositivo de rede local para inspeção e registro em log. 
 
Ou você pode definir [UDRs](../virtual-network/virtual-networks-udr-overview.md) para forçar o tráfego de Internet de saída da sub-rede que hospeda o Azure-SSIS ir para outra sub-rede que hospeda uma NVA (solução de virtualização de rede) como um firewall ou firewall do Azure para inspeção e registro em log. 

Em ambos os casos, a rota de tráfego interromperá a conectividade de entrada necessária dos serviços dependentes do Azure Data Factory (especificamente, os serviços de gerenciamento do lote do Azure) para o Azure-SSIS IR na rede virtual. Para evitar isso, defina um ou mais UDRs na sub-rede que contém o Azure-SSIS IR. 

Você pode aplicar uma rota 0.0.0.0/0 com o tipo do próximo salto como **Internet** na sub-rede que hospeda o Azure-SSIS ir em um cenário do Azure ExpressRoute. Ou você pode modificar a rota 0.0.0.0/0 existente do tipo do próximo salto como **dispositivo virtual** para **Internet** em um cenário de NVA.

![Adicionar uma rota](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Se estiver preocupado em perder a capacidade de inspecionar o tráfego de Internet de saída dessa sub-rede, você poderá definir UDRs específicos para rotear o tráfego somente entre os serviços de gerenciamento do lote do Azure e o Azure-SSIS IR com um tipo de próximo salto como **Internet**.

Por exemplo, se o Azure-SSIS ir estiver localizado em `UK South`, você obteria uma lista de intervalos de `BatchNodeManagement.UKSouth` serviço do intervalo de IP de [marcas de serviço download de intervalo de IP](https://www.microsoft.com/en-us/download/details.aspx?id=56519) ou por meio da API de descoberta de marca de [serviço](https://aka.ms/discoveryapi). Em seguida, aplique os seguintes UDRs de rotas de intervalo de IP relacionadas com o tipo do próximo salto como **Internet**.

![Configurações de UDR do lote do Azure](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Essa abordagem gera um custo de manutenção adicional. Verifique regularmente o intervalo de IP e adicione novos intervalos de IP em seu UDR para evitar a interrupção do Azure-SSIS IR. É recomendável verificar o intervalo de IP mensalmente, porque quando o novo IP aparece na marca de serviço, o IP levará outro mês para entrar em vigor. 

### <a name="resource-group"></a>Configurar o grupo de recursos
O Azure-SSIS IR precisa criar determinados recursos de rede no mesmo grupo de recursos que a rede virtual. Esses recursos incluem:
   -   Um Azure Load Balancer, com o nome  *\<GUID >-azurebatch-cloudserviceloadbalancer*.
   -   Um endereço IP público do Azure, com o nome  *\<GUID >-azurebatch-cloudservicepublicip*.
   -   Um grupo de segurança de trabalho de rede, com o nome  *\<GUID >-azurebatch-cloudservicenetworksecuritygroup*. 

Esses recursos serão criados quando o IR for iniciado. Eles serão excluídos quando o IR parar. Para evitar o bloqueio da parada do IR, não reutilize esses recursos de rede em seus outros recursos. 

Certifique-se de que você não tem nenhum bloqueio de recurso no grupo de recursos ou assinatura ao qual a rede virtual pertence. Se você configurar um bloqueio somente leitura ou um bloqueio de exclusão, iniciar e parar o IR poderá falhar, ou o IR poderá parar de responder. 

Certifique-se de que você não tem uma política do Azure que impede que os seguintes recursos sejam criados sob o grupo de recursos ou assinatura ao qual a rede virtual pertence: 
   -   Microsoft.Network/LoadBalancers 
   -   Microsoft.Network/NetworkSecurityGroups 
   -   Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a>PERGUNTAS FREQÜENTES

- Como posso proteger o endereço IP público exposto no Azure-SSIS IR para conexão de entrada? É possível remover o endereço IP público?
 
    No momento, um endereço IP público será criado automaticamente quando o Azure-SSIS IR ingressar na rede virtual. Temos um NSG no nível de NIC para permitir que apenas os serviços de gerenciamento do lote do Azure façam a conexão de entrada com o Azure-SSIS IR. Você também pode especificar um NSG no nível de sub-rede para a proteção de entrada.

    Se você não quiser que o endereço IP público seja exposto, considere [Configurar o ir auto-hospedado como um proxy para o Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) em vez da rede virtual, se isso se aplicar ao seu cenário.
 
- Posso adicionar o endereço IP estático do Azure-SSIS IR à lista de permissões do firewall para a fonte de dados?
 
    - Se sua fonte de dados for local, depois de conectar a rede virtual à sua rede local e ingressar seu Azure-SSIS IR na sub-rede da rede virtual, você poderá adicionar o intervalo de IP dessa sub-rede à lista de permissões.
    - Se sua fonte de dados for um serviço do Azure com suporte com um ponto de extremidade de serviço de rede virtual, você poderá configurar um ponto de serviço de rede virtual em sua rede virtual e ingressar seu Azure-SSIS IR nessa sub-rede da rede virtual. Em seguida, você pode permitir o acesso usando a regra de rede virtual dos serviços do Azure em vez do intervalo de IP.
    - Se sua fonte de dados for um tipo diferente de fonte de dados de nuvem, você poderá usar UDR para rotear o tráfego de saída do Azure-SSIS IR para o NVA ou para o Firewall do Azure usando um endereço IP público estático. Você pode adicionar o endereço IP público do NVA ou do firewall do Azure à lista de permissões.
    - Se as respostas anteriores não atenderem às suas necessidades, considere fornecer acesso à fonte de dados Configurando [um ir auto-hospedado como um proxy para o Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Em seguida, você pode adicionar o endereço IP do computador que hospeda o IR auto-hospedado à lista de permissões em vez de ingressar o Azure-SSIS IR na rede virtual.

## <a name="azure-portal-data-factory-ui"></a>Portal do Azure (interface do usuário do Data Factory)
Esta seção mostra como unir um Azure-SSIS IR existente a uma rede virtual (clássica ou Azure Resource Manager) usando a interface do usuário portal do Azure e Data Factory. 

Antes de ingressar seu Azure-SSIS IR à rede virtual, você precisa configurar corretamente a rede virtual. Siga as etapas na seção que se aplica ao seu tipo de rede virtual (clássica ou Azure Resource Manager). Em seguida, siga as etapas na terceira seção para ingressar seu Azure-SSIS IR na rede virtual. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Configurar uma rede virtual Azure Resource Manager

Use o portal para configurar uma rede virtual Azure Resource Manager antes de tentar adicionar uma Azure-SSIS IR a ela.

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, somente esses navegadores da Web dão suporte à interface do usuário do Data Factory. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

1. Selecione **mais serviços**. Filtre e selecione **redes virtuais**. 

1. Filtre e selecione sua rede virtual na lista. 

1. Na página **rede virtual** , selecione **Propriedades**. 

1. Selecione o botão de cópia para a **ID de recurso** para copiar a ID de recurso da rede virtual para a área de transferência. Salve a ID da área de transferência no OneNote ou em um arquivo. 

1. No menu à esquerda, selecione **sub-redes**. Verifique se o número de endereços disponíveis é maior do que os nós em seu Azure-SSIS IR. 

1. Verifique se o provedor do lote do Azure está registrado na assinatura do Azure que tem a rede virtual. Ou registre o provedor do lote do Azure. Se você já tiver uma conta do lote do Azure em sua assinatura, sua assinatura será registrada para o lote do Azure. (Se você criar o Azure-SSIS IR no portal do Data Factory, o provedor do lote do Azure será registrado automaticamente para você.) 

   a. No portal do Azure, no menu à esquerda, selecione **assinaturas**. 

   b. Selecione a sua subscrição. 

   c. À esquerda, selecione **provedores de recursos**e confirme se **Microsoft. batch** é um provedor registrado. 

   ![Confirmação do status "registrado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se você não vir o **Microsoft. batch** na lista, para registrá-lo, [crie uma conta do lote do Azure vazia](../batch/batch-account-create-portal.md) em sua assinatura. Você pode excluí-lo mais tarde. 

### <a name="configure-a-classic-virtual-network"></a>Configurar uma rede virtual clássica
Use o portal para configurar uma rede virtual clássica antes de tentar ingressar em um Azure-SSIS IR a ela. 

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, somente esses navegadores da Web dão suporte à interface do usuário do Data Factory. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

1. Selecione **mais serviços**. Filtre e selecione **redes virtuais (clássicas)** . 

1. Filtre e selecione sua rede virtual na lista. 

1. Na página **rede virtual (clássica)** , selecione **Propriedades**. 

   ![ID de recurso de rede virtual clássica](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Selecione o botão Copiar para a **ID de recurso** para copiar a ID de recurso da rede clássica para a área de transferência. Salve a ID da área de transferência no OneNote ou em um arquivo. 

1. No menu à esquerda, selecione **sub-redes**. Verifique se o número de endereços disponíveis é maior do que os nós em seu Azure-SSIS IR. 

   ![Número de endereços disponíveis na rede virtual](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Ingresse **MicrosoftAzureBatch** na função **colaborador de máquina virtual clássica** para a rede virtual. 

    a. No menu à esquerda, selecione **controle de acesso (iam)** e selecione a guia atribuições de **função** . 

    ![Botões "controle de acesso" e "Adicionar"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Selecione **adicionar atribuição de função**.

    c. Na página **Adicionar atribuição de função** , para **função**, selecione **colaborador de máquina virtual clássica**. Na caixa **selecionar** , Cole **ddbf3205-c6bd-46ae-8127-60eb93363864**e, em seguida, selecione **lote do Microsoft Azure** na lista de resultados da pesquisa. 

    ![Resultados da pesquisa na página "Adicionar atribuição de função"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. Selecione **salvar** para salvar as configurações e fechar a página. 

    ![Salvar configurações de acesso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. Confirme que você vê **lote do Microsoft Azure** na lista de colaboradores. 

    ![Confirmar o acesso ao lote do Azure](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Verifique se o provedor do lote do Azure está registrado na assinatura do Azure que tem a rede virtual. Ou registre o provedor do lote do Azure. Se você já tiver uma conta do lote do Azure em sua assinatura, sua assinatura será registrada para o lote do Azure. (Se você criar o Azure-SSIS IR no portal do Data Factory, o provedor do lote do Azure será registrado automaticamente para você.) 

   a. No portal do Azure, no menu à esquerda, selecione **assinaturas**. 

   b. Selecione a sua subscrição. 

   c. À esquerda, selecione **provedores de recursos**e confirme se **Microsoft. batch** é um provedor registrado. 

   ![Confirmação do status "registrado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se você não vir o **Microsoft. batch** na lista, para registrá-lo, [crie uma conta do lote do Azure vazia](../batch/batch-account-create-portal.md) em sua assinatura. Você pode excluí-lo mais tarde. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Ingressar o Azure-SSIS IR em uma rede virtual

Depois de configurar sua rede virtual Azure Resource Manager ou rede virtual clássica, você pode unir o Azure-SSIS IR à rede virtual:

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, somente esses navegadores da Web dão suporte à interface do usuário do Data Factory. 

1. No [portal do Azure](https://portal.azure.com), no menu à esquerda, selecione **Data factories**. Se você não vir **fábricas de dados** no menu, selecione **mais serviços**e, na seção **inteligência + análise** , selecione **Data factories**. 

   ![Lista de fábricas de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selecione o data factory com o Azure-SSIS IR na lista. Você verá o home page para seu data factory. Selecione o bloco **criar & implantar** . Você verá a interface do usuário do Data Factory em uma guia separada. 

   ![Home page da fábrica de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Na interface do usuário do Data Factory, alterne para a guia **Editar** , selecione **conexões**e alterne para a guia **tempos de execução de integração** . 

   ![Guia "tempos de execução de integração"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Se o Azure-SSIS IR estiver em execução, na lista de **tempos de execução de integração** , na coluna **ações** , selecione o botão **parar** para seu Azure-SSIS ir. Não é possível editar um IR até que você o interrompa. 

   ![Parar o IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Na lista de **tempos de execução de integração** , na coluna **ações** , selecione o botão **Editar** para seu Azure-SSIS ir. 

   ![Editar o Integration Runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. No painel **de configuração do Integration Runtime** , avance pelas páginas **configurações gerais** e **configurações do SQL** selecionando o botão **Avançar** . 

1. Na página **Configurações avançadas** : 

   a. Marque a caixa de seleção ao lado de **selecionar uma VNet**. 

   b. Para **assinatura**, selecione sua assinatura do Azure. Na assinatura, você pode selecionar uma rede virtual existente. 
  
   c. Para **nome da VNet**, selecione sua rede virtual. 

   d. Para **nome da sub-rede**, selecione sua sub-rede na rede virtual. 

   e. Se você também quiser configurar ou gerenciar um IR auto-hospedado como um proxy para seu Azure-SSIS IR, marque a caixa de seleção **Configurar auto-hospedados** . Para obter mais informações, consulte [configurar um ir auto-hospedado como um proxy para um Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).

   f. Selecione o botão de **validação VNet** . Se a validação for bem-sucedida, selecione o botão **Avançar** . 

   ![Configurações avançadas para a instalação do IR](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. Na página **Resumo** , examine todas as configurações de seu Azure-SSIS ir. Em seguida, selecione o botão **Atualizar** .

1. Inicie sua Azure-SSIS IR selecionando o botão **Iniciar** na coluna **ações** para sua Azure-SSIS ir. Leva cerca de 20 a 30 minutos para iniciar o Azure-SSIS IR que une uma rede virtual. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>Configurar uma rede virtual
Antes de poder ingressar seu Azure-SSIS IR em uma rede virtual, você precisa configurar a rede virtual. Para configurar automaticamente as permissões e as configurações de rede virtual para seu Azure-SSIS IR para ingressar na rede virtual, adicione o seguinte script:

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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Criar um Azure-SSIS IR e associá-lo a uma rede virtual
Você pode criar um Azure-SSIS IR e associá-lo a uma rede virtual ao mesmo tempo. Para obter o script completo e as instruções, consulte [criar um Azure-SSIS ir](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Ingressar um Azure-SSIS IR existente em uma rede virtual
O artigo [criar um Azure-SSIS ir](create-azure-ssis-integration-runtime.md) mostra como criar um Azure-SSIS ir e associá-lo a uma rede virtual no mesmo script. Se você já tiver um Azure-SSIS IR, siga estas etapas para associá-lo à rede virtual: 
1. Pare o Azure-SSIS IR. 
1. Configure o Azure-SSIS IR para ingressar na rede virtual. 
1. Inicie o Azure-SSIS IR. 

### <a name="define-the-variables"></a>Definir as variáveis
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Parar o Azure-SSIS IR
Você precisa parar o Azure-SSIS IR antes de poder associá-lo a uma rede virtual. Este comando libera todos os seus nós e interrompe a cobrança:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Definir configurações de rede virtual para o Azure-SSIS IR a ser associado

Para definir as configurações para a rede virtual que o Azure-SSIS ingressará, use este script: 

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

### <a name="configure-the-azure-ssis-ir"></a>Configurar o Azure-SSIS IR
Para configurar o Azure-SSIS ir para ingressar na rede virtual, execute `Set-AzDataFactoryV2IntegrationRuntime` o comando: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Iniciar o Azure-SSIS IR
Para iniciar o Azure-SSIS IR, execute o seguinte comando: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

Esse comando leva de 20 a 30 minutos para ser concluído.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre Azure-SSIS IR, consulte os seguintes artigos: 
- [Azure-SSIS ir](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais gerais sobre o IRs, incluindo Azure-SSIS IR. 
- [Tutorial: Implante pacotes do SSIS no](tutorial-create-azure-ssis-runtime-portal.md)Azure. Este tutorial fornece instruções passo a passo para criar seu Azure-SSIS IR. Ele usa o banco de dados SQL do Azure para hospedar o catálogo do SSIS. 
- [Crie um Azure-SSIS ir](create-azure-ssis-integration-runtime.md). Este artigo se expande no tutorial. Ele fornece instruções sobre como usar o banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual ou instância gerenciada em uma rede virtual para hospedar o catálogo do SSIS. Ele mostra como unir seu Azure-SSIS IR a uma rede virtual. 
- [Monitorizar um Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como obter informações sobre seu Azure-SSIS IR. Ele fornece descrições de status para as informações retornadas. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra como parar, iniciar ou excluir seu Azure-SSIS IR. Ele também mostra como escalar horizontalmente sua Azure-SSIS IR adicionando nós.
