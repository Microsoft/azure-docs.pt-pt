---
title: Configurar o armazenamento do Azure firewalls e redes virtuais | Documentos da Microsoft
description: Configure a segurança de rede em camadas para a sua conta de armazenamento.
services: storage
author: cbrooksmsft
ms.service: storage
ms.topic: article
ms.date: 02/22/2019
ms.author: cbrooks
ms.subservice: common
ms.openlocfilehash: c7f7768406ae64615b46abeb396b5469caf2f6e9
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750655"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Configurar o armazenamento do Azure firewalls e redes virtuais

O armazenamento do Azure fornece um modelo de segurança em camadas. Este modelo permite-lhe proteger as contas de armazenamento para um conjunto específico de redes suportados. Quando as regras de rede estiverem configuradas, apenas as aplicações pedir dados de mais de mil o conjunto especificado de redes podem aceder a uma conta de armazenamento.

Uma aplicação que acede a uma conta de armazenamento quando as regras de rede estão em vigor requer autorização adequada no pedido. Há suporte para autorização com credenciais do Azure Active Directory (AD) (para blobs e filas) (pré-visualização), uma chave de acesso de conta válido ou um token SAS.

> [!IMPORTANT]
> Ativar as regras de firewall para a sua conta de armazenamento bloqueia pedidos de entrada de dados por predefinição, a menos que os pedidos provenientes de um serviço que está a funcionar dentro de uma rede Virtual do Azure (VNet). Pedidos que estão bloqueados incluem os de outros serviços do Azure, do portal do Azure, do Registro em log e serviços de métricas e assim por diante.
>
> Pode conceder acesso aos serviços do Azure que funcionam de dentro de uma VNet, permitindo que a sub-rede da instância do serviço. Ativar um número limitado de cenários por meio da [exceções](#exceptions) mecanismo descrito na secção seguinte. Para aceder ao portal do Azure, terá de estar numa máquina dentro do limite confiável (IP ou VNet) que configurou.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Cenários

Configure contas de armazenamento para negar o acesso ao tráfego de todas as redes (incluindo o tráfego de internet) por predefinição. Em seguida, conceda acesso ao tráfego de VNets específico. Esta configuração permite-lhe criar um limite de rede segura para as suas aplicações. Também pode conceder acesso à internet pública intervalos de endereços IP, habilitar conexões de clientes de internet ou no local específicos.

Regras de rede são impostas em todos os protocolos de rede ao armazenamento do Azure, incluindo o SMB e REST. Para acessar os dados com ferramentas como o portal do Azure, o Explorador de armazenamento e o AZCopy, são necessárias regras de rede explícita.

Pode aplicar regras de rede para contas de armazenamento existentes ou quando criar novas contas de armazenamento.

Assim que as regras de rede são aplicadas, elas são impostas para todos os pedidos. Tokens SAS que concedem acesso a um endereço IP específico servem para limitar o acesso do token portador, mas não a conceder acesso de novo para além de regras de rede configuradas.

Tráfego de disco de máquina virtual (incluindo montar e desmontar as operações de e/s de disco) não é afetado pelas regras de rede. Acesso REST aos blobs de página está protegido por regras de rede.

Contas de armazenamento clássicas não suportam firewalls e redes virtuais.

Pode utilizar discos não geridos nas contas de armazenamento com regras de rede aplicadas às VMs de cópia de segurança e restauro através da criação de uma exceção. Esse processo está documentado no [exceções](#exceptions) seção deste artigo. Exceções de firewall não são aplicáveis com discos geridos à medida que já sejam geridos pelo Azure.

## <a name="change-the-default-network-access-rule"></a>Alterar a regra de acesso de rede predefinida

Por predefinição, as contas de armazenamento aceitam ligações de clientes em qualquer rede. Para limitar o acesso a redes selecionadas, primeiro tem de alterar a ação predefinida.

> [!WARNING]
> Fazer alterações às regras de rede pode afetar a capacidade de seus aplicativos para se ligar ao armazenamento do Azure. Definir a regra de rede predefinido para **negar** todos os blocos de acesso aos dados, a menos que as regras de rede específicas para **conceder** acesso também são aplicadas. Certifique-se de que conceder acesso a quaisquer redes permitidas utilizar regras de rede antes de alterar a regra predefinida para negar o acesso.

### <a name="managing-default-network-access-rules"></a>Gerir regras de acesso de rede predefinidas

Pode gerir as regras de acesso de rede predefinido para contas de armazenamento através do portal do Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá para a conta de armazenamento que pretende proteger.

1. Clique no menu de definições denominado **Firewalls e redes virtuais**.

1. Para negar o acesso por predefinição, optar por permitir acesso a partir **redes selecionadas**. Para permitir tráfego de todas as redes, optar por permitir acesso a partir **todas as redes**.

1. Clique em **guardar** para aplicar as alterações.

#### <a name="powershell"></a>PowerShell

1. Instalar o [do Azure PowerShell](/powershell/azure/install-Az-ps) e [iniciar sessão](/powershell/azure/authenticate-azureps).

1. Apresenta o estado da regra predefinida para a conta de armazenamento.

    ```PowerShell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Defina a regra predefinida para negar o acesso à rede por predefinição.

    ```PowerShell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Defina a regra predefinida para permitir o acesso à rede por predefinição.

    ```PowerShell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Instalar o [CLI do Azure](/cli/azure/install-azure-cli) e [iniciar sessão](/cli/azure/authenticate-azure-cli).

1. Apresenta o estado da regra predefinida para a conta de armazenamento.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Defina a regra predefinida para negar o acesso à rede por predefinição.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Defina a regra predefinida para permitir o acesso à rede por predefinição.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Conceder acesso a partir de uma rede virtual

Pode configurar contas de armazenamento para permitir o acesso apenas a partir de VNets específico.

Ativar uma [ponto final de serviço](/azure/virtual-network/virtual-network-service-endpoints-overview) do armazenamento do Azure dentro da VNet. Este ponto final permite tráfego de uma rota ideal para o serviço de armazenamento do Azure. As identidades de rede virtual e sub-rede também são transmitidas com cada solicitação. Os administradores podem, em seguida, configurar regras de rede para a conta de armazenamento que permitem que pedidos a serem recebidos de sub-redes específicas na VNet. Os clientes concedido o acesso através destas regras de rede têm de continuar para cumprir os requisitos de autorização da conta de armazenamento para acessar os dados.

Cada conta de armazenamento suporta até 100 regras de rede virtual, que podem ser combinadas com [regras de rede IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Regiões de rede virtual disponível

Em geral, os pontos finais de serviço funcionam entre redes virtuais e instâncias do serviço na mesma região do Azure. Quando utilizar pontos finais de serviço com o armazenamento do Azure, este âmbito cresce para incluir o [região emparelhada](/azure/best-practices-availability-paired-regions). Pontos finais de serviço permitem a continuidade durante uma ativação pós-falha e o acesso a instâncias de armazenamento georredundante de só de leitura (RA-GRS). Regras de rede que concedam acesso a partir de uma rede virtual para uma conta de armazenamento também concedem acesso a qualquer instância de RA-GRS.

Quando planear a recuperação após desastre durante uma falha regional, deve criar as VNets na região emparelhada com antecedência. Ative pontos finais de serviço para o armazenamento do Azure, com regras de rede, conceder acesso a partir destas redes virtuais alternativas. Em seguida, aplica estas regras para as suas contas de armazenamento georredundante.

> [!NOTE]
> Pontos finais de serviço não se aplicam ao tráfego fora da região de rede virtual e o par de regiões designado. Só pode aplicar regras de rede, conceder acesso a partir de redes virtuais para contas de armazenamento na região primária de uma conta de armazenamento ou na região emparelhada designado.

### <a name="required-permissions"></a>Permissões obrigatórias

Para aplicar uma regra de rede virtual a uma conta de armazenamento, o utilizador tem de ter as permissões adequadas para as sub-redes que está a ser adicionadas. A permissão necessário é *aderir ao serviço a uma sub-rede* e está incluído nos *contribuinte de conta de armazenamento* função incorporada. Também podem ser adicionada às definições de função personalizada.

Conta de armazenamento e as redes virtuais concedido o acesso pode ser em subscrições diferentes, mas essas subscrições têm de fazer parte do mesmo inquilino do Azure AD.

### <a name="managing-virtual-network-rules"></a>Gerir regras de rede virtual

Pode gerir regras de rede virtual para contas de armazenamento através do portal do Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá para a conta de armazenamento que pretende proteger.

1. Clique no menu de definições denominado **Firewalls e redes virtuais**.

1. Verifique se selecionou para permitir o acesso a partir **redes selecionadas**.

1. Para conceder acesso a uma rede virtual com uma nova regra de rede, em **redes virtuais**, clique em **adicionar rede virtual existente**, selecione **redes virtuais** e **Sub-redes** opções e clique em **adicionar**. Para criar uma nova rede virtual e conceder acesso, clique em **Adicionar nova rede virtual**. Forneça as informações necessárias para criar a nova rede virtual e, em seguida, clique em **criar**.

    > [!NOTE]
    > Se um ponto final de serviço do armazenamento do Azure não foi configurado anteriormente para a rede virtual selecionada e sub-redes, pode configurá-lo como parte desta operação.

1. Para remover uma rede virtual ou a regra de sub-rede, clique em **...**  para abrir o menu de contexto para a rede virtual ou sub-rede e clique em **remover**.

1. Clique em **guardar** para aplicar as alterações.

#### <a name="powershell"></a>PowerShell

1. Instalar o [do Azure PowerShell](/powershell/azure/install-Az-ps) e [iniciar sessão](/powershell/azure/authenticate-azureps).

1. Lista de regras de rede virtual.

    ```PowerShell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Ative o ponto final de serviço do armazenamento do Azure numa rede virtual existente e a sub-rede.

    ```PowerShell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Adicione uma regra de rede para uma rede virtual e uma sub-rede.

    ```PowerShell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

1. Remova uma regra de rede para uma rede virtual e uma sub-rede.

    ```PowerShell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Não se esqueça [definir a regra predefinida](#change-the-default-network-access-rule) ao **negar**, ou as regras de rede não têm qualquer efeito.

#### <a name="cliv2"></a>CLIv2

1. Instalar o [CLI do Azure](/cli/azure/install-azure-cli) e [iniciar sessão](/cli/azure/authenticate-azure-cli).

1. Lista de regras de rede virtual.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Ative o ponto final de serviço do armazenamento do Azure numa rede virtual existente e a sub-rede.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Adicione uma regra de rede para uma rede virtual e uma sub-rede.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

1. Remova uma regra de rede para uma rede virtual e uma sub-rede.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Não se esqueça [definir a regra predefinida](#change-the-default-network-access-rule) ao **negar**, ou as regras de rede não têm qualquer efeito.

## <a name="grant-access-from-an-internet-ip-range"></a>Conceder acesso a partir de um internet intervalo IP

Pode configurar contas de armazenamento para permitir o acesso a partir da internet pública específica intervalos de endereços IP. Esta configuração concede acesso a serviços específicos baseados na internet e redes no local e bloqueia o tráfego de internet geral.

Fornecer internet permitidos intervalos de endereços usando [notação CIDR](https://tools.ietf.org/html/rfc4632) sob a forma *16.17.18.0/24* ou como individual IP, como endereços *16.17.18.19*.

   > [!NOTE]
   > Intervalos de endereços pequenos com "/ 31" ou "/ 32" prefixo tamanhos não são suportados. Estes intervalos devem ser configurados com regras de endereços IP individuais.

Regras de rede IP só são permitidas para **internet pública** endereços IP. Reservado para redes privadas de intervalos de endereços de IP (conforme definido na [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) não são permitidos em regras de IP. Redes privadas incluem endereços que comecem com _10.*_, _172.16. *_ - _172.31. *_, e _192.168. *_.

   > [!NOTE]
   > Regras de rede IP não têm qualquer efeito nos pedidos com origem na mesma região do Azure que a conta de armazenamento. Uso [regras de rede Virtual](#grant-access-from-a-virtual-network) para permitir pedidos da mesma região.

Apenas endereços IPV4 são suportados neste momento.

Cada conta de armazenamento suporta até 100 regras de rede IP, que podem ser combinadas com [regras de rede Virtual](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Configurar o acesso a partir de redes no local

Para conceder acesso a partir de suas redes no local para a sua conta de armazenamento com uma regra de rede IP, tem de identificar endereços IP utilizados pela sua rede de acesso à internet. Contacte o administrador de rede para obter ajuda.

Pode usar [ExpressRoute](/azure/expressroute/expressroute-introduction) para ligar a sua rede para a rede do Azure. Aqui, cada circuito está configurado com dois endereços IP públicos. Estes podem ser encontradas no Microsoft Edge e utilizar [Peering público do Azure](/azure/expressroute/expressroute-circuit-peerings) para ligar ao Microsoft Services, como o armazenamento do Azure. Para permitir a comunicação com o armazenamento do Azure, crie regras de rede IP para os endereços IP públicos dos seus circuitos. Para localizar os endereços IP públicos do seu circuito do ExpressRoute, [abra um pedido de suporte no ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) através do portal do Azure.

### <a name="managing-ip-network-rules"></a>Gerir regras de rede IP

Pode gerir regras de rede IP para as contas de armazenamento através do portal do Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá para a conta de armazenamento que pretende proteger.

1. Clique no menu de definições denominado **Firewalls e redes virtuais**.

1. Verifique se selecionou para permitir o acesso a partir **redes selecionadas**.

1. Para conceder acesso a um internet intervalo de IP, introduza o endereço IP ou intervalo de endereços (no formato CIDR) sob **Firewall** > **intervalo de endereços**.

1. Para remover uma regra de rede IP, clique no ícone de caixote do lixo junto o intervalo de endereços.

1. Clique em **guardar** para aplicar as alterações.

#### <a name="powershell"></a>PowerShell

1. Instalar o [do Azure PowerShell](/powershell/azure/install-Az-ps) e [iniciar sessão](/powershell/azure/authenticate-azureps).

1. Listar regras de rede IP.

    ```PowerShell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Adicione uma regra de rede para um endereço IP individual.

    ```PowerShell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Adicione uma regra de rede para um intervalo de endereços IP.

    ```PowerShell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Remova uma regra de rede para um endereço IP individual.

    ```PowerShell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Remova uma regra de rede para um intervalo de endereços IP.

    ```PowerShell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Não se esqueça [definir a regra predefinida](#change-the-default-network-access-rule) ao **negar**, ou as regras de rede não têm qualquer efeito.

#### <a name="cliv2"></a>CLIv2

1. Instalar o [CLI do Azure](/cli/azure/install-azure-cli) e [iniciar sessão](/cli/azure/authenticate-azure-cli).

1. Listar regras de rede IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Adicione uma regra de rede para um endereço IP individual.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Adicione uma regra de rede para um intervalo de endereços IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Remova uma regra de rede para um endereço IP individual.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Remova uma regra de rede para um intervalo de endereços IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Não se esqueça [definir a regra predefinida](#change-the-default-network-access-rule) ao **negar**, ou as regras de rede não têm qualquer efeito.

## <a name="exceptions"></a>Exceções

Regras de rede podem ativar uma configuração de rede segura para a maioria dos cenários. No entanto, existem alguns casos em que as exceções devem ser concedidas para ativar a funcionalidade completa. Pode configurar contas de armazenamento com exceções para serviços Microsoft fidedignos e para acesso a dados de análise de armazenamento.

### <a name="trusted-microsoft-services"></a>Serviços Microsoft fidedignos

Alguns serviços da Microsoft que interagem com contas de armazenamento operam de redes que não não possível conceder acesso por meio de regras de rede.

Para ajudar a este tipo de trabalho do serviço conforme pretendido, permitir que o conjunto de serviços Microsoft fidedignos para ignorar as regras de rede. Estes serviços, em seguida, irão utilizar a autenticação segura para aceder à conta de armazenamento.

Se ativar o **permitir confiável a serviços da Microsoft...**  exceção, os seguintes serviços (quando é registado na sua subscrição), é concedido acesso à conta de armazenamento:

|Serviço|Nome do fornecedor de recursos|Objetivo|
|:------|:---------------------|:------|
|Azure Backup|Microsoft.Backup|Executar cópias de segurança e restauros de discos não geridos em máquinas de virtuais IAAS. (não necessário para discos geridos). [Saiba mais](/azure/backup/backup-introduction-to-azure-backup).|
|Azure Site Recovery|Microsoft.SiteRecovery |Configure recuperação após desastre ao ativar a replicação para máquinas de virtuais de IaaS do Azure. Isto é necessário se estiver a utilizar a conta de armazenamento de cache de firewall ativada ou a conta de armazenamento de origem ou a conta de armazenamento de destino.  [Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication).|
|Azure DevTest Labs|Microsoft.DevTestLab|Instalação de Federação e de criação de imagem personalizada. [Saiba mais](/azure/devtest-lab/devtest-lab-overview).|
|Azure Data Box|Microsoft.DataBox|Permite que a importação de dados para o Azure com o Data Box. [Saiba mais](/azure/databox/data-box-overview).|
|Azure Event Grid|Microsoft.EventGrid|Ativar a publicação de eventos de armazenamento de BLOBs e permitir que o Event Grid publicar em filas do armazenamento. Saiba mais sobre [eventos de armazenamento de BLOBs](/azure/event-grid/event-sources) e [publicação para filas](/azure/event-grid/event-handlers).|
|Azure Event Hubs|Microsoft.EventHub|Arquivar dados com a captura de Hubs de eventos. [Saiba mais](/azure/event-hubs/event-hubs-capture-overview).|
|Redes do Azure|Microsoft.Networking|Store e analisar registos de tráfego de rede. [Saiba mais](/azure/network-watcher/network-watcher-packet-capture-overview).|
|Azure Monitor|Microsoft. insights|Permite a escrita de dados para uma conta de armazenamento seguro de monitorização [Saiba mais](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security).|
|Azure SQL Data Warehouse|Microsoft.Sql|Permite que a importação e exportação de cenários com o PolyBase. [Saiba mais](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).|

### <a name="storage-analytics-data-access"></a>Acesso de dados de análise de armazenamento

Em alguns casos, o acesso para ler os registos de diagnóstico e métricas é necessário a partir de fora do limite de rede. Pode conceder exceções para as regras de rede para permitir o acesso de leitura aos ficheiros de registo de conta de armazenamento, as tabelas de métricas ou ambos. [Saiba mais sobre como trabalhar com a análise de armazenamento.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Gestão de exceções

Pode gerir as exceções de regra de rede através do portal do Azure, o PowerShell ou a CLI do Azure v2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá para a conta de armazenamento que pretende proteger.

1. Clique no menu de definições denominado **Firewalls e redes virtuais**.

1. Verifique se selecionou para permitir o acesso a partir **redes selecionadas**.

1. Sob **exceções**, selecione as exceções que pretende conceder.

1. Clique em **guardar** para aplicar as alterações.

#### <a name="powershell"></a>PowerShell

1. Instalar o [do Azure PowerShell](/powershell/azure/install-Az-ps) e [iniciar sessão](/powershell/azure/authenticate-azureps).

1. Apresente as exceções para as regras de rede da conta de armazenamento.

    ```PowerShell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Configure as exceções às regras de rede de conta de armazenamento.

    ```PowerShell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Remova as exceções às regras de rede de conta de armazenamento.

    ```PowerShell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Não se esqueça [definir a regra predefinida](#change-the-default-network-access-rule) ao **negar**, ou remoção de exceções não têm qualquer efeito.

#### <a name="cliv2"></a>CLIv2

1. Instalar o [CLI do Azure](/cli/azure/install-azure-cli) e [iniciar sessão](/cli/azure/authenticate-azure-cli).

1. Apresente as exceções para as regras de rede da conta de armazenamento.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Configure as exceções às regras de rede de conta de armazenamento.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Remova as exceções às regras de rede de conta de armazenamento.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Não se esqueça [definir a regra predefinida](#change-the-default-network-access-rule) ao **negar**, ou remoção de exceções não têm qualquer efeito.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre pontos finais de serviço de rede do Azure no [pontos finais de serviço](/azure/virtual-network/virtual-network-service-endpoints-overview).

Aprofunde os seus conhecimentos sobre a segurança de armazenamento do Azure no [guia de segurança do armazenamento do Azure](storage-security-guide.md).
