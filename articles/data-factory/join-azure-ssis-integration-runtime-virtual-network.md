---
title: Unir o tempo de execução de integração do Azure-SSIS a uma rede virtual | Microsoft Docs
description: Saiba como unir o tempo de execução de integração do Azure-SSIS a uma rede virtual do Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 49422d73a63f1bcde267aac3a9b75e9977970cc9
ms.sourcegitcommit: acffa72239413c62662febd4e39ebcb6c6c0dd00
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68951934"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Unir um tempo de execução de integração do Azure-SSIS a uma rede virtual
Ao usar o SQL Server Integration Services (SSIS) no Azure Data Factory (ADF), você deve ingressar seu IR (Integration Runtime do Azure-SSIS) em uma rede virtual do Azure nos seguintes cenários: 

- Você deseja se conectar a armazenamentos de dados locais de pacotes do SSIS em execução no IR do Azure-SSIS sem configurar/gerenciar um IR auto-hospedado como proxy. 

- Você está hospedando o SSISDB (banco de dados de catálogo do SSIS) no banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual/Instância Gerenciada em uma rede virtual. 

O ADF permite unir o IR do Azure-SSIS a uma rede virtual criada por meio do modelo de implantação clássico ou do modelo de implantação de Azure Resource Manager. 

> [!IMPORTANT]
> A rede virtual clássica está sendo preterida, portanto, use o Azure Resource Manager rede virtual em vez disso.  Se você já usa a rede virtual clássica, mude para usar o Azure Resource Manager rede virtual assim que possível.

## <a name="access-to-on-premises-data-stores"></a>Acesso a armazenamentos de dados locais
Se seus pacotes SSIS acessarem somente armazenamentos de dados de nuvem pública, você não precisará unir o IR do Azure-SSIS a uma rede virtual. Se os pacotes do SSIS acessarem os armazenamentos de dados locais, você poderá ingressar o IR do Azure-SSIS em uma rede virtual que esteja conectada à rede local ou configurar/gerenciar um IR do modo auto-hospedado como proxy para o IR do Azure-SSIS, consulte [Configurar o ir auto-hospedado como um artigo sobre proxy para IR do Azure-SSIS](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) . Ao unir seu IR do Azure-SSIS a uma rede virtual, há alguns pontos importantes a serem observados: 

- Se não houver nenhuma rede virtual conectada à sua rede local, primeiro crie uma [rede virtual Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) ou uma [rede virtual clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para o tempo de execução de integração do Azure-SSIS para ingressar. Em seguida, configure uma conexão de gateway de [VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) site a site ou conexão de [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) dessa rede virtual para sua rede local. 

- Se houver uma rede virtual Azure Resource Manager ou clássica existente conectada à sua rede local no mesmo local que o IR do Azure-SSIS, você poderá unir o IR a essa rede virtual. 

- Se houver uma rede virtual clássica existente conectada à sua rede local em um local diferente do IR do Azure-SSIS, você poderá primeiro criar uma [rede virtual clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para o ir do Azure-SSIS para ingressar. Em seguida, configure uma conexão de [rede virtual clássica-to-clássico](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) . Ou você pode criar uma [rede virtual Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para que o tempo de execução de integração do Azure-SSIS ingresse. Em seguida, configure uma conexão de [rede virtual clássica para Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) . 
 
- Se houver uma rede virtual Azure Resource Manager existente conectada à sua rede local em um local diferente do IR do Azure-SSIS, você poderá primeiro criar uma [rede virtual Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) para que o ir do Azure-SSIS ingresse. Em seguida, configure uma conexão de rede virtual de Azure Resource Manager para Azure Resource Manager. Ou você pode criar uma [rede virtual clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para o ir do Azure-SSIS ingressar. Em seguida, configure uma conexão de [rede virtual clássica para Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) . 

## <a name="host-the-ssis-catalog-database-in-azure-sql-database-with-virtual-network-service-endpointsmanaged-instance"></a>Hospedar o banco de dados do catálogo do SSIS no banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual/Instância Gerenciada
Se o catálogo do SSIS estiver hospedado no banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual ou Instância Gerenciada em uma rede virtual, você poderá unir seu IR do Azure-SSIS para: 

- A mesma rede virtual 
- Uma rede virtual diferente que tem uma conexão de rede para rede com aquela usada para o Instância Gerenciada 

Se você hospedar seu catálogo do SSIS no banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual, certifique-se de unir seu IR do Azure-SSIS à mesma rede virtual e sub-rede.

Se você ingressar o IR do Azure-SSIS para a mesma rede virtual que o Instância Gerenciada, verifique se o IR do Azure-SSIS está em uma sub-rede diferente da Instância Gerenciada. Se você unir o IR do Azure-SSIS a uma rede virtual diferente da Instância Gerenciada, recomendamos o emparelhamento de rede virtual (que é limitado à mesma região) ou uma rede virtual à conexão de rede virtual. Consulte [conectar seu aplicativo ao instância gerenciada do banco de dados SQL do Azure](../sql-database/sql-database-managed-instance-connect-app.md).

Em todos os casos, a rede virtual só pode ser implantada por meio do modelo de implantação Azure Resource Manager.

As seções a seguir fornecem mais detalhes. 

## <a name="requirements-for-virtual-network-configuration"></a>Requisitos para a configuração de rede virtual
-   Certifique-se `Microsoft.Batch` de que seja um provedor registrado na assinatura da sua sub-rede de rede virtual que hospeda o ir do Azure-SSIS. Se você estiver usando a rede virtual clássica, ingresse `MicrosoftAzureBatch` também na função de colaborador da máquina virtual clássica para essa rede virtual. 

-   Verifique se você tem as permissões necessárias. Consulte [as permissões necessárias](#perms).

-   Selecione a sub-rede apropriada para hospedar o IR do Azure-SSIS. Consulte [selecionar a sub-rede](#subnet). 

-   Se você estiver usando seu próprio servidor DNS (serviços de nome de domínio) na rede virtual, consulte [servidor de serviços de nome de domínio](#dns_server). 

-   Se você estiver usando um NSG (grupo de segurança de rede) na sub-rede, consulte [grupo de segurança de rede](#nsg) 

-   Se você estiver usando o Azure Express Route ou configurando a UDR (rota definida pelo usuário), consulte [usar o Azure ExpressRoute ou a rota definida pelo usuário](#route). 

-   Verifique se o grupo de recursos da rede virtual pode criar e excluir determinados recursos de rede do Azure. Consulte [os requisitos para o grupo de recursos](#resource-group). 

-   Se você personalizar o IR do Azure-SSIS conforme descrito no artigo [configuração personalizada para ir do Azure-](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup) SSIS, seus nós ir do Azure-SSIS serão alocados endereços IP privados de um intervalo predefinido de 172.16.0.0-172.31.255.255. portanto, verifique se o IP privado os intervalos de endereços de suas redes virtuais/locais não colidem com esse intervalo.

Aqui está um diagrama que mostra as conexões necessárias para o IR do Azure-SSIS:

![Runtime de integração Azure-SSIS](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>Permissões necessárias

O usuário que cria o Integration Runtime do Azure-SSIS deve ter as seguintes permissões:

- Se você estiver unindo o IR do SSIS a uma rede virtual Azure Resource Manager, terá duas opções:

  - Use a função de *colaborador de rede* interna. Essa função vem com a permissão _Microsoft. Network\* /_ , que tem um escopo muito maior do que o necessário.

  - Crie uma função personalizada que inclua apenas a permissão _Microsoft. Network/virtualNetworks/\*/Join/Action_ necessária. 

- Se você estiver unindo o IR do SSIS a uma rede virtual clássica, recomendamos que você use a função de *colaborador de máquina virtual clássica* interna. Caso contrário, você precisa definir uma função personalizada que inclui a permissão para ingressar na rede virtual.

### <a name="subnet"></a>Selecionar a sub-rede
-   Não selecione o GatewaySubnet para implantar um Integration Runtime do Azure-SSIS, pois ele é dedicado para gateways de rede virtual. 

-   Verifique se a sub-rede selecionada tem espaço de endereço disponível suficiente para uso do IR do Azure-SSIS. Deixe pelo menos 2 * número de nó IR em endereços IP disponíveis. O Azure reserva alguns endereços IP dentro de cada sub-rede e esses endereços não podem ser usados. O primeiro e o último endereços IP das sub-redes são reservados para a conformidade do protocolo, juntamente com mais três endereços usados para os serviços do Azure. Para obter mais informações, consulte [existem restrições sobre como usar endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets). 

-   Não use uma sub-rede que seja ocupada exclusivamente por outros serviços do Azure (por exemplo, Instância Gerenciada do Banco de Dados SQL, serviço de aplicativo, etc.). 

### <a name="dns_server"></a>Servidor de serviços de nome de domínio 
Se você precisar usar seu próprio servidor DNS (serviços de nome de domínio) em uma rede virtual unida pelo tempo de execução de integração do Azure-SSIS, verifique se ele pode resolver nomes de host do Azure globais (por exemplo, `<your storage account>.blob.core.windows.net`um nome de blob de armazenamento do Azure,). 

As etapas a seguir são recomendadas: 

-   Configure o DNS personalizado para encaminhar solicitações para o DNS do Azure. Você pode encaminhar registros DNS não resolvidos para o endereço IP dos resolvedores recursivos do Azure (168.63.129.16) em seu próprio servidor DNS. 

-   Configure o DNS personalizado como primário e o DNS do Azure como secundário para a rede virtual. Registre o endereço IP dos resolvedores recursivos do Azure (168.63.129.16) como um servidor DNS secundário caso seu próprio servidor DNS não esteja disponível. 

Para obter mais informações, consulte [resolução de nomes que usa seu próprio servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a>Grupo de segurança de rede
Se você precisar implementar um NSG (grupo de segurança de rede) para a sub-rede usada pelo tempo de execução de integração do Azure-SSIS, permita o tráfego de entrada/saída pelas seguintes portas: 

| Direction | Protocolo de transporte | Source | Intervalo de portas de origem | Destino | Intervalo de portas de destino | Comentários |
|---|---|---|---|---|---|---|
| Entrada | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (se você unir o IR a uma Azure Resource Manager rede virtual) <br/><br/>10100, 20100, 30100 (se você unir o IR a uma rede virtual clássica)| O serviço de Data Factory usa essas portas para se comunicar com os nós de seu tempo de execução de integração do Azure-SSIS na rede virtual. <br/><br/> Se você criar um NSG no nível de sub-rede ou não, Data Factory sempre configurará um NSG no nível das NICs (placas de interface de rede) conectadas às máquinas virtuais que hospedam o IR do Azure-SSIS. Somente o tráfego de entrada de endereços IP Data Factory nas portas especificadas é permitido por esse NSG de nível de NIC. Mesmo se você abrir essas portas para o tráfego de Internet no nível de sub-rede, o tráfego de endereços IP que não são Data Factory endereços IP é bloqueado no nível de NIC. |
| Saída | TCP | VirtualNetwork | * | AzureCloud<br/>(ou escopo maior como Internet) | 443 | Os nós de seu tempo de execução de integração do Azure-SSIS na rede virtual usam essa porta para acessar os serviços do Azure, como o armazenamento do Azure e os hubs de eventos do Azure. |
| Saída | TCP | VirtualNetwork | * | Internet | 80 | Os nós de seu tempo de execução de integração do Azure-SSIS na rede virtual usam essa porta para baixar a lista de certificados revogados da Internet. |
| Saída | TCP | VirtualNetwork | * | SQL<br/>(ou escopo maior como Internet) | 1433, 11000-11999 | Os nós de seu tempo de execução de integração do Azure-SSIS na rede virtual usam essas portas para acessar o SSISDB hospedado pelo seu servidor de banco de dados SQL do Azure. Se a política de conexão do servidor do banco de dados SQL do Azure estiver definida como **proxy** em vez de redirecionar, somente a porta 1433 será necessária. Essa regra de segurança de saída não é aplicável ao SSISDB hospedado pelo seu Instância Gerenciada na rede virtual. |
||||||||

### <a name="route"></a>Usar o Azure ExpressRoute ou a rota definida pelo usuário
Você pode conectar um circuito [do Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) à sua infraestrutura de rede virtual para estender sua rede local para o Azure. 

Uma configuração comum é usar o túnel forçado (anunciar uma rota BGP, 0.0.0.0/0 para a rede virtual) que força o tráfego de Internet de saída do fluxo de rede virtual para o dispositivo de rede local para inspeção e registro em log. Esse fluxo de tráfego interrompe a conectividade entre o IR do Azure-SSIS na rede virtual com serviços dependentes de Azure Data Factory. A solução é definir uma (ou mais) [UDRs (rotas definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md) ) na sub-rede que contém o ir do Azure-SSIS. Um UDR define rotas específicas de sub-rede que são respeitadas em vez da rota BGP. 

Ou você pode definir UDRs (rotas definidas pelo usuário) para forçar o tráfego de Internet de saída da sub-rede que hospeda o IR do Azure-SSIS para outra sub-rede, que hospeda um dispositivo de rede virtual como um firewall ou um host DMZ para inspeção e registro em log. 

Em ambos os casos, aplique uma rota 0.0.0.0/0 com o tipo do próximo salto como **Internet** na sub-rede que hospeda o ir do Azure-SSIS, para que a comunicação entre o serviço de data Factory e o Azure-SSIS possa ter sucesso. 

![Adicionar uma rota](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Se estiver preocupado em perder a capacidade de inspecionar o tráfego de Internet de saída dessa sub-rede, você também poderá adicionar uma regra NSG na sub-rede para restringir os destinos de saída para os [endereços IP do Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653). 

Consulte [este script do PowerShell](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c) para obter um exemplo. Você precisa executar o script semanalmente para manter a lista de endereços IP do Azure data center atualizada. 

### <a name="resource-group"></a>Requisitos para o grupo de recursos
-   O IR do Azure-SSIS precisa criar determinados recursos de rede no mesmo grupo de recursos que a rede virtual. Esses recursos incluem o seguinte:
    -   Um Azure Load Balancer, com o nome  *\<GUID >-azurebatch-cloudserviceloadbalancer*.
    -   Um endereço IP público do Azure, com o nome  *\<GUID >-azurebatch-cloudservicepublicip*.
    -   Um grupo de segurança de trabalho de rede, com o nome  *\<GUID >-azurebatch-cloudservicenetworksecuritygroup*. 

-   Certifique-se de que você não tem nenhum bloqueio de recurso no grupo de recursos ou assinatura ao qual a rede virtual pertence. Se você configurar um bloqueio somente leitura ou um bloqueio de exclusão, iniciar e parar o IR poderá falhar ou parar de responder. 

-   Certifique-se de que você não tem uma política do Azure que impede que os seguintes recursos sejam criados sob o grupo de recursos ou assinatura ao qual a rede virtual pertence: 
    -   Microsoft.Network/LoadBalancers 
    -   Microsoft.Network/NetworkSecurityGroups 
    -   Microsoft.Network/PublicIPAddresses 

## <a name="azure-portal-data-factory-ui"></a>Portal do Azure (interface do usuário do Data Factory)
Esta seção mostra como unir um tempo de execução do Azure-SSIS existente a uma rede virtual (clássica ou Azure Resource Manager) usando o portal do Azure e a interface do usuário do Data Factory. Primeiro, você precisa configurar a rede virtual adequadamente antes de unir o IR do Azure-SSIS a ela. Percorra uma das próximas duas seções com base no tipo de sua rede virtual (clássica ou Azure Resource Manager). Em seguida, continue com a terceira seção para unir seu IR do Azure-SSIS à rede virtual. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Usar o portal para configurar uma rede virtual Azure Resource Manager
Você precisa configurar uma rede virtual antes de poder adicionar um IR do Azure-SSIS a ela. 

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, a interface do usuário Data Factory tem suporte apenas nesses navegadores da Web. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

1. Selecione **mais serviços**. Filtre e selecione **redes virtuais**. 

1. Filtre e selecione sua rede virtual na lista. 

1. Na página **rede virtual** , selecione **Propriedades**. 

1. Selecione o botão de cópia para a **ID de recurso** para copiar a ID de recurso da rede virtual para a área de transferência. Salve a ID da área de transferência no OneNote ou em um arquivo. 

1. Selecione **sub-redes** no menu à esquerda. Verifique se o número de **endereços disponíveis** é maior do que os nós em seu tempo de execução de integração do Azure-SSIS. 

1. Verifique se o provedor do lote do Azure está registrado na assinatura do Azure que tem a rede virtual. Ou registre o provedor do lote do Azure. Se você já tiver uma conta do lote do Azure em sua assinatura, sua assinatura será registrada para o lote do Azure. (Se você criar o IR do Azure-SSIS no portal de Data Factory, o provedor do lote do Azure será registrado automaticamente para você.) 

   a. Em portal do Azure, selecione **assinaturas** no menu à esquerda. 

   b. Selecione a sua subscrição. 

   c. Selecione **provedores de recursos** à esquerda e confirme se **Microsoft. batch** é um provedor registrado. 

   ![Confirmação do status "registrado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se você não vir o **Microsoft. batch** na lista, para registrá-lo, [crie uma conta do lote do Azure vazia](../batch/batch-account-create-portal.md) em sua assinatura. Você pode excluí-lo mais tarde. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Usar o portal para configurar uma rede virtual clássica
Você precisa configurar uma rede virtual antes de poder adicionar um IR do Azure-SSIS a ela. 

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, a interface do usuário Data Factory tem suporte apenas nesses navegadores da Web. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

1. Selecione **mais serviços**. Filtre e selecione **redes virtuais (clássicas)** . 

1. Filtre e selecione sua rede virtual na lista. 

1. Na página **rede virtual (clássica)** , selecione **Propriedades**. 

   ![ID de recurso de rede virtual clássica](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Selecione o botão Copiar para a **ID de recurso** para copiar a ID de recurso da rede clássica para a área de transferência. Salve a ID da área de transferência no OneNote ou em um arquivo. 

1. Selecione **sub-redes** no menu à esquerda. Verifique se o número de **endereços disponíveis** é maior do que os nós em seu tempo de execução de integração do Azure-SSIS. 

   ![Número de endereços disponíveis na rede virtual](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Ingresse **MicrosoftAzureBatch** na função **colaborador de máquina virtual clássica** para a rede virtual. 

    a. Selecione **controle de acesso (iam)** no menu à esquerda e selecione a guia atribuições de **função** . 

    ![Botões "controle de acesso" e "Adicionar"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Selecione **adicionar atribuição de função**.

    c. Na página **Adicionar atribuição de função** , selecione **colaborador de máquina virtual clássica** para **função**. Cole **ddbf3205-c6bd-46ae-8127-60eb93363864** na caixa de **seleção** e, em seguida, selecione **lote do Microsoft Azure** na lista de resultados da pesquisa. 

    ![Resultados da pesquisa na página "Adicionar atribuição de função"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. Selecione **salvar** para salvar as configurações e fechar a página. 

    ![Salvar configurações de acesso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. Confirme que você vê **lote do Microsoft Azure** na lista de colaboradores. 

    ![Confirmar o acesso ao lote do Azure](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Verifique se o provedor do lote do Azure está registrado na assinatura do Azure que tem a rede virtual. Ou registre o provedor do lote do Azure. Se você já tiver uma conta do lote do Azure em sua assinatura, sua assinatura será registrada para o lote do Azure. (Se você criar o IR do Azure-SSIS no portal de Data Factory, o provedor do lote do Azure será registrado automaticamente para você.) 

   a. Em portal do Azure, selecione **assinaturas** no menu à esquerda. 

   b. Selecione a sua subscrição. 

   c. Selecione **provedores de recursos** à esquerda e confirme se **Microsoft. batch** é um provedor registrado. 

   ![Confirmação do status "registrado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se você não vir o **Microsoft. batch** na lista, para registrá-lo, [crie uma conta do lote do Azure vazia](../batch/batch-account-create-portal.md) em sua assinatura. Você pode excluí-lo mais tarde. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Unir o IR do Azure-SSIS a uma rede virtual
1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, a interface do usuário Data Factory tem suporte apenas nesses navegadores da Web. 

1. No [portal do Azure](https://portal.azure.com), selecione **Data factories** no menu à esquerda. Se você não vir **fábricas de dados** no menu, selecione **mais serviços**e as fábricas de **dados** de seleção na seção **inteligência + análise** . 

   ![Lista de fábricas de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selecione seu data factory com o tempo de execução de integração do Azure-SSIS na lista. Você verá o home page para seu data factory. Selecione o bloco **criar & implantar** . Você verá a interface do usuário do Data Factory em uma guia separada. 

   ![Home page da fábrica de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Na interface do usuário do Data Factory, alterne para a guia **Editar** , selecione **conexões**e alterne para a guia **tempos de execução de integração** . 

   ![Guia "tempos de execução de integração"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Se o IR do Azure-SSIS estiver em execução, na lista de tempo de execução de integração, selecione o botão **parar** na coluna **ações** para o ir do Azure-SSIS. Não é possível editar um IR até que você o interrompa. 

   ![Parar o IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Na lista de tempo de execução de integração, selecione o botão **Editar** na coluna **ações** para o ir do Azure-SSIS. 

   ![Editar o Integration Runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. No painel **de configuração do Integration Runtime** , avance pelas páginas **configurações gerais** e **configurações do SQL** clicando no botão **Avançar** . 

1. Na página **Configurações avançadas** , execute as seguintes ações: 

   a. Marque a caixa de seleção **selecionar uma VNet...** . 

   b. Para **assinatura**, selecione sua assinatura do Azure sob a qual você pode, por sua vez, selecionar uma rede virtual existente. 
  
   c. Para **nome da VNet**, selecione sua rede virtual. 

   d. Para **nome da sub-rede**, selecione sua sub-rede na rede virtual. 

   e. Se você também quiser configurar/gerenciar um IR auto-hospedado como proxy para o IR do Azure-SSIS, marque a caixa de seleção configurar o auto-hospedado **...** e consulte [Configurar o ir auto-hospedado como um proxy para o artigo ir do Azure-SSIS](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) .

   f. Clique no botão **validação de VNet** e, se for bem-sucedido, clique no botão **Avançar** . 

   ![Configurações avançadas para a instalação do IR](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. Na página **Resumo** , examine todas as configurações do ir do Azure-SSIS e clique no botão **Atualizar** .

1. Agora, você pode iniciar o IR do Azure-SSIS clicando no botão **Iniciar** na coluna **ações** para o ir do Azure-SSIS. Leva aproximadamente de 20 a 30 minutos para iniciar o IR do Azure-SSIS que une uma rede virtual. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>Configurar uma rede virtual
Você precisa configurar uma rede virtual antes de poder unir seu IR do Azure-SSIS a ela. Para configurar automaticamente as permissões/configurações de rede virtual para o tempo de execução de integração do Azure-SSIS para ingressar na rede virtual, adicione o seguinte script:

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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Criar um IR do Azure-SSIS e associá-lo a uma rede virtual
Você pode criar um IR do Azure-SSIS e associá-lo a uma rede virtual ao mesmo tempo. Para obter o script completo e as instruções, consulte [criar um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Unir um IR do Azure-SSIS existente a uma rede virtual
O script no artigo [criar um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md) mostra como criar um ir do Azure-SSIS e associá-lo a uma rede virtual no mesmo script. Se você tiver um IR do Azure-SSIS existente, execute as seguintes etapas para associá-lo à rede virtual: 
1. Pare o IR do Azure-SSIS. 
1. Configure o IR do Azure-SSIS para ingressar na rede virtual. 
1. Inicie o IR do Azure-SSIS. 

### <a name="define-the-variables"></a>Definir as variáveis
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Parar o IR do Azure-SSIS
Pare o tempo de execução de integração do Azure-SSIS antes de poder associá-lo a uma rede virtual. Este comando libera todos os seus nós e interrompe a cobrança:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Definir configurações de rede virtual para o IR do Azure-SSIS para ingressar
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

### <a name="configure-the-azure-ssis-ir"></a>Configurar o IR do Azure-SSIS
Para configurar o tempo de execução de integração do Azure-SSIS para ingressar na `Set-AzDataFactoryV2IntegrationRuntime` rede virtual, execute o comando: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Iniciar o IR do Azure-SSIS
Para iniciar o tempo de execução de integração do Azure-SSIS, execute o seguinte comando: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

Esse comando leva de 20 a 30 minutos para ser concluído.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre o tempo de execução de integração do Azure-SSIS, consulte os seguintes tópicos: 
- [Tempo de execução de integração do Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais sobre os tempos de execução de integração em geral, incluindo o IR do Azure-SSIS. 
- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). Este tutorial fornece instruções passo a passo para criar seu IR do Azure-SSIS. Ele usa o banco de dados SQL do Azure para hospedar o catálogo do SSIS. 
- [Criar um integration runtime do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar o banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual/Instância Gerenciada em uma rede virtual para hospedar o catálogo do SSIS e ingressar o IR do Azure-SSIS em uma rede virtual. 
- [Monitorizar um Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como recuperar informações sobre o IR do Azure-SSIS e fornece descrições de status nas informações retornadas. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra como parar, iniciar ou excluir seu IR do Azure-SSIS. Ele também mostra como escalar horizontalmente o IR do Azure-SSIS adicionando nós.
