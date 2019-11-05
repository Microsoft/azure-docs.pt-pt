---
title: Configurar redes virtuais e firewalls de armazenamento do Azure | Microsoft Docs
description: Configure a segurança de rede em camadas para sua conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: e7f4d58ceab78aea7031d2c706504bdcb99434c6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520641"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Configurar redes virtuais e firewalls de armazenamento do Azure

O armazenamento do Azure fornece um modelo de segurança em camadas. Esse modelo permite que você proteja e controle o nível de acesso às suas contas de armazenamento que seus aplicativos e ambientes empresariais exigem, com base no tipo e no subconjunto de redes usadas. Quando as regras de rede são configuradas, somente os aplicativos que solicitam dados no conjunto especificado de redes podem acessar uma conta de armazenamento. Você pode limitar o acesso à sua conta de armazenamento a solicitações originadas de endereços IP especificados, intervalos de IP ou de uma lista de sub-redes em uma VNet (rede virtual) do Azure.

As contas de armazenamento têm um ponto de extremidade público que pode ser acessado pela Internet. Você também pode criar [pontos de extremidade privados para sua conta de armazenamento](storage-private-endpoints.md), que atribui um endereço IP privado de sua vnet à conta de armazenamento e protege todo o tráfego entre sua vnet e a conta de armazenamento por um link privado. O Firewall do armazenamento do Azure fornece acesso de controle de acesso para o ponto de extremidade público da sua conta de armazenamento. Você também pode usar o firewall para bloquear todo o acesso por meio do ponto de extremidade público ao usar pontos de extremidades privados. A configuração do firewall de armazenamento também permite selecionar serviços confiáveis da plataforma Azure para acessar a conta de armazenamento com segurança.

Um aplicativo que acessa uma conta de armazenamento quando as regras de rede estão em vigor ainda requer autorização adequada para a solicitação. Há suporte para autorização com as credenciais do Azure Active Directory (Azure AD) para BLOBs e filas, com uma chave de acesso de conta válida ou com um token SAS.

> [!IMPORTANT]
> A ativação de regras de firewall para sua conta de armazenamento bloqueia solicitações de entrada de dados por padrão, a menos que as solicitações sejam originadas de um serviço operando em uma VNet (rede virtual) do Azure ou de endereços IP públicos permitidos. As solicitações que estão bloqueadas incluem aquelas de outros serviços do Azure, da portal do Azure, do log e dos serviços de métrica e assim por diante.
>
> Você pode conceder acesso aos serviços do Azure que operam de dentro de uma VNet, permitindo o tráfego da sub-rede que hospeda a instância do serviço. Você também pode habilitar um número limitado de cenários por meio do mecanismo de [exceções](#exceptions) descrito abaixo. Para acessar dados da conta de armazenamento por meio do portal do Azure, você precisa estar em um computador dentro do limite confiável (IP ou VNet) que você configurou.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Cenários

Para proteger sua conta de armazenamento, primeiro você deve configurar uma regra para negar o acesso ao tráfego de todas as redes (incluindo o tráfego da Internet) no ponto de extremidade público, por padrão. Em seguida, você deve configurar regras que concedem acesso ao tráfego de VNets específicas. Você também pode configurar regras para conceder acesso ao tráfego de selecionar intervalos de endereços IP públicos da Internet, permitindo conexões de clientes locais ou da Internet específicos. Essa configuração permite que você crie um limite de rede seguro para seus aplicativos.

Você pode combinar as regras de firewall que permitem o acesso de redes virtuais específicas e de intervalos de endereços IP públicos na mesma conta de armazenamento. As regras de firewall de armazenamento podem ser aplicadas a contas de armazenamento existentes ou ao criar novas contas de armazenamento.

As regras de firewall de armazenamento se aplicam ao ponto de extremidade público de uma conta de armazenamento. Você não precisa de nenhuma regra de acesso de firewall para permitir o tráfego para pontos de extremidade privados de uma conta de armazenamento. O processo de aprovar a criação de um ponto de extremidade privado concede acesso implícito ao tráfego da sub-rede que hospeda o ponto de extremidade privado.

As regras de rede são impostas em todos os protocolos de rede para o armazenamento do Azure, incluindo REST e SMB. Para acessar dados usando ferramentas como o portal do Azure, Gerenciador de Armazenamento e AZCopy, as regras de rede explícitas devem ser configuradas.

Depois que as regras de rede são aplicadas, elas são impostas para todas as solicitações. Os tokens SAS que concedem acesso a um endereço IP específico servem para limitar o acesso do proprietário do token, mas não concedem novo acesso além das regras de rede configuradas.

O tráfego de disco de máquina virtual (incluindo as operações de montagem e desmontagem e a e/s de disco) não é afetado pelas regras de rede. O acesso REST aos blobs de página é protegido por regras de rede.

As contas de armazenamento clássicas não dão suporte a firewalls e redes virtuais.

Você pode usar discos não gerenciados em contas de armazenamento com regras de rede aplicadas a backup e restauração de VMs criando uma exceção. Esse processo está documentado na seção [exceções](#exceptions) deste artigo. As exceções de firewall não são aplicáveis a discos gerenciados, pois já são gerenciadas pelo Azure.

## <a name="change-the-default-network-access-rule"></a>Alterar a regra de acesso de rede padrão

Por padrão, as contas de armazenamento aceitam conexões de clientes em qualquer rede. Para limitar o acesso às redes selecionadas, você deve primeiro alterar a ação padrão.

> [!WARNING]
> Fazer alterações nas regras de rede pode afetar a capacidade dos seus aplicativos de se conectar ao armazenamento do Azure. A definição da regra de rede padrão para **negar** bloqueia todo o acesso aos dados, a menos que regras de rede específicas que **concedem** acesso também sejam aplicadas. Certifique-se de conceder acesso a qualquer rede permitida usando regras de rede antes de alterar a regra padrão para negar acesso.

### <a name="managing-default-network-access-rules"></a>Gerenciando regras de acesso de rede padrão

Você pode gerenciar regras de acesso de rede padrão para contas de armazenamento por meio do portal do Azure, do PowerShell ou do CLIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá para a conta de armazenamento que você deseja proteger.

1. Clique no menu configurações chamado **firewalls e redes virtuais**.

1. Para negar acesso por padrão, escolha permitir o acesso de **redes selecionadas**. Para permitir o tráfego de todas as redes, escolha permitir o acesso de **todas as redes**.

1. Clique em **salvar** para aplicar suas alterações.

#### <a name="powershell"></a>PowerShell

1. Instale o [Azure PowerShell](/powershell/azure/install-Az-ps) e [entre](/powershell/azure/authenticate-azureps).

1. Exiba o status da regra padrão para a conta de armazenamento.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Defina a regra padrão para negar o acesso à rede por padrão.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Defina a regra padrão para permitir o acesso à rede por padrão.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Instale o [CLI do Azure](/cli/azure/install-azure-cli) e [entre](/cli/azure/authenticate-azure-cli).

1. Exiba o status da regra padrão para a conta de armazenamento.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Defina a regra padrão para negar o acesso à rede por padrão.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Defina a regra padrão para permitir o acesso à rede por padrão.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Conceder acesso de uma rede virtual

Você pode configurar contas de armazenamento para permitir o acesso somente de sub-redes específicas. As sub-redes permitidas podem pertencer a uma VNet na mesma assinatura ou àquelas em uma assinatura diferente, incluindo assinaturas que pertencem a um locatário de Azure Active Directory diferente.

Habilite um [ponto de extremidade de serviço](/azure/virtual-network/virtual-network-service-endpoints-overview) para o armazenamento do Azure na VNet. O ponto de extremidade de serviço roteia o tráfego da VNet por meio de um caminho ideal para o serviço de armazenamento do Azure. As identidades da sub-rede e da rede virtual também são transmitidas com cada solicitação. Os administradores podem configurar as regras de rede para a conta de armazenamento que permitem que as solicitações sejam recebidas de sub-redes específicas em uma VNet. Os clientes com acesso concedido por essas regras de rede devem continuar atendendo aos requisitos de autorização da conta de armazenamento para acessar os dados.

Cada conta de armazenamento dá suporte a até 100 regras de rede virtual, que podem ser combinadas com [regras de rede IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Regiões de rede virtual disponíveis

Em geral, os pontos de extremidade de serviço funcionam entre redes virtuais e instâncias de serviço na mesma região do Azure. Ao usar pontos de extremidade de serviço com o armazenamento do Azure, esse escopo cresce para incluir a [região emparelhada](/azure/best-practices-availability-paired-regions). Os pontos de extremidade de serviço permitem a continuidade durante um failover regional e o acesso a instâncias de armazenamento com redundância geográfica (RA-GRS) somente leitura. As regras de rede que concedem acesso de uma rede virtual a uma conta de armazenamento também concedem acesso a qualquer instância de RA-GRS.

Ao planejar a recuperação de desastre durante uma interrupção regional, você deve criar o VNets na região emparelhada com antecedência. Habilite os pontos de extremidade de serviço para o armazenamento do Azure, com as regras de rede concedendo acesso a essas redes virtuais alternativas. Em seguida, aplique essas regras às suas contas de armazenamento com redundância geográfica.

> [!NOTE]
> Os pontos de extremidade de serviço não se aplicam ao tráfego fora da região da rede virtual e do par de regiões designado. Você só pode aplicar as regras de rede que concedem acesso de redes virtuais a contas de armazenamento na região primária de uma conta de armazenamento ou na região emparelhada designada.

### <a name="required-permissions"></a>Permissões obrigatórias

Para aplicar uma regra de rede virtual a uma conta de armazenamento, o usuário deve ter as permissões apropriadas para as sub-redes que estão sendo adicionadas. A permissão necessária é *unir o serviço a uma sub-rede* e está incluída na função interna de *colaborador da conta de armazenamento* . Ele também pode ser adicionado às definições de função personalizadas.

A conta de armazenamento e as redes virtuais com acesso concedido podem estar em assinaturas diferentes, incluindo assinaturas que fazem parte de um locatário diferente do Azure AD.

> [!NOTE]
> A configuração de regras que concedem acesso a sub-redes em redes virtuais que fazem parte de um locatário Azure Active Directory diferente atualmente só tem suporte por meio do PowerShell, da CLI e de APIs REST. Essas regras não podem ser configuradas por meio do portal do Azure, embora possam ser exibidas no Portal.

### <a name="managing-virtual-network-rules"></a>Gerenciando regras de rede virtual

Você pode gerenciar regras de rede virtual para contas de armazenamento por meio do portal do Azure, do PowerShell ou do CLIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá para a conta de armazenamento que você deseja proteger.

1. Clique no menu configurações chamado **firewalls e redes virtuais**.

1. Verifique se você selecionou para permitir o acesso de **redes selecionadas**.

1. Para conceder acesso a uma rede virtual com uma nova regra de rede, em **redes virtuais**, clique em **Adicionar rede virtual existente**, selecione opções de **redes virtuais** e **sub-redes** e clique em **Adicionar**. Para criar uma nova rede virtual e conceder acesso a ela, clique em **Adicionar nova rede virtual**. Forneça as informações necessárias para criar a nova rede virtual e, em seguida, clique em **criar**.

    > [!NOTE]
    > Se um ponto de extremidade de serviço para o armazenamento do Azure não foi configurado anteriormente para a rede virtual e as sub-redes selecionadas, você poderá configurá-lo como parte dessa operação.
    >
    > Atualmente, somente as redes virtuais que pertencem ao mesmo locatário Azure Active Directory são mostradas para seleção durante a criação da regra. Para conceder acesso a uma sub-rede em uma rede virtual que pertence a outro locatário, use o PowerShell, a CLI ou as APIs REST.

1. Para remover uma rede virtual ou regra de sub-rede, clique em **...** para abrir o menu de contexto da rede virtual ou sub-rede e clique em **remover**.

1. Clique em **salvar** para aplicar suas alterações.

#### <a name="powershell"></a>PowerShell

1. Instale o [Azure PowerShell](/powershell/azure/install-Az-ps) e [entre](/powershell/azure/authenticate-azureps).

1. Listar regras de rede virtual.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Habilite o ponto de extremidade de serviço para o armazenamento do Azure em uma rede virtual e sub-rede existentes.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Adicione uma regra de rede para uma rede virtual e sub-rede.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Para adicionar uma regra de rede para uma sub-rede em uma VNet que pertence a outro locatário do Azure AD, use um parâmetro **VirtualNetworkResourceId** totalmente qualificado no formato "/subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/Providers/Microsoft.Network/virtualNetworks/vNet-Name/Subnets/subnet-Name".

1. Remova uma regra de rede para uma rede virtual e sub-rede.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Certifique-se de [definir a regra padrão](#change-the-default-network-access-rule) como **negar**ou se as regras de rede não têm nenhum efeito.

#### <a name="cliv2"></a>CLIv2

1. Instale o [CLI do Azure](/cli/azure/install-azure-cli) e [entre](/cli/azure/authenticate-azure-cli).

1. Listar regras de rede virtual.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Habilite o ponto de extremidade de serviço para o armazenamento do Azure em uma rede virtual e sub-rede existentes.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Adicione uma regra de rede para uma rede virtual e sub-rede.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Para adicionar uma regra para uma sub-rede em uma VNet que pertence a outro locatário do Azure AD, use uma ID de sub-rede totalmente qualificada no formato "/subscriptions/\<Subscription-ID\>/resourceGroups/\<resourcegroup-Name\>/providers/Microsoft.Network/virtualNetworks/\<vNet-Name\>/Subnets/\<subnet-Name\>".
    > 
    > Você pode usar o parâmetro de **assinatura** para recuperar a ID de sub-rede de uma VNet que pertence a outro locatário do Azure AD.

1. Remova uma regra de rede para uma rede virtual e sub-rede.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Certifique-se de [definir a regra padrão](#change-the-default-network-access-rule) como **negar**ou se as regras de rede não têm nenhum efeito.

## <a name="grant-access-from-an-internet-ip-range"></a>Conceder acesso de um intervalo de IP da Internet

Você pode configurar contas de armazenamento para permitir o acesso de intervalos de endereços IP de Internet pública específicos. Essa configuração concede acesso a serviços específicos baseados na Internet e redes locais e bloqueia o tráfego geral da Internet.

Forneça intervalos de endereços de Internet permitidos usando a [notação CIDR](https://tools.ietf.org/html/rfc4632) no formato *16.17.18.0/24* ou como endereços IP individuais, como *16.17.18.19*.

   > [!NOTE]
   > Não há suporte para os intervalos de endereços pequenos usando os tamanhos de prefixo "/31" ou "/32". Esses intervalos devem ser configurados usando regras de endereço IP individuais.

As regras de rede IP só são permitidas para endereços IP **públicos da Internet** . Os intervalos de endereços IP reservados para redes privadas (conforme definido no [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) não são permitidos em regras de IP. Redes privadas incluem endereços que começam com _10. *_ , _172,16. *_  - _172,31. *_ e _192,168. *_ .

   > [!NOTE]
   > As regras de rede IP não têm nenhum efeito em solicitações provenientes da mesma região do Azure que a conta de armazenamento. Use [regras de rede virtual](#grant-access-from-a-virtual-network) para permitir solicitações de mesma região.

  > [!NOTE]
  > Os serviços implantados na mesma região que a conta de armazenamento usam endereços IP privados do Azure para comunicação. Portanto, você não pode restringir o acesso a serviços específicos do Azure com base em seu intervalo de endereços IP de entrada públicos.

Somente endereços IPV4 têm suporte para a configuração de regras de firewall de armazenamento.

Cada conta de armazenamento dá suporte a até 100 regras de rede IP.

### <a name="configuring-access-from-on-premises-networks"></a>Configurando o acesso de redes locais

Para conceder acesso de suas redes locais à sua conta de armazenamento com uma regra de rede IP, você deve identificar os endereços IP voltados para a Internet usados pela sua rede. Contate o administrador de rede para obter ajuda.

Se você estiver usando o [ExpressRoute](/azure/expressroute/expressroute-introduction) de seu local, para emparelhamento público ou emparelhamento da Microsoft, será necessário identificar os endereços IP de NAT que são usados. Para peering público, cada circuito ExpressRoute, por predefinição, utiliza dois endereços IP NAT que são aplicados ao tráfego de serviço do Azure quando o tráfego entra no backbone de rede do Microsoft Azure. Para o emparelhamento da Microsoft, os endereços IP de NAT usados são fornecidos pelo cliente ou são fornecidos pelo provedor de serviços. Para permitir o acesso aos recursos de serviço, tem de permitir estes endereços IP públicos na definição da firewall do IP dos recursos. Para localizar os endereços IP do circuito ExpressRoute de peering público, [abra um pedido de suporte no ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) através do portal do Azure. Saiba mais sobre [NAT para peering público e da Microsoft do ExpressRoute.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Gerenciando regras de rede IP

Você pode gerenciar regras de rede IP para contas de armazenamento por meio do portal do Azure, do PowerShell ou do CLIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá para a conta de armazenamento que você deseja proteger.

1. Clique no menu configurações chamado **firewalls e redes virtuais**.

1. Verifique se você selecionou para permitir o acesso de **redes selecionadas**.

1. Para conceder acesso a um intervalo IP da Internet, insira o endereço IP ou o intervalo de endereços (no formato CIDR) em **Firewall** > **intervalo de endereços**.

1. Para remover uma regra de rede IP, clique no ícone de Lixeira ao lado do intervalo de endereços.

1. Clique em **salvar** para aplicar suas alterações.

#### <a name="powershell"></a>PowerShell

1. Instale o [Azure PowerShell](/powershell/azure/install-Az-ps) e [entre](/powershell/azure/authenticate-azureps).

1. Listar regras de rede IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Adicione uma regra de rede para um endereço IP individual.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Adicione uma regra de rede para um intervalo de endereços IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Remova uma regra de rede para um endereço IP individual.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Remova uma regra de rede para um intervalo de endereços IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Certifique-se de [definir a regra padrão](#change-the-default-network-access-rule) como **negar**ou se as regras de rede não têm nenhum efeito.

#### <a name="cliv2"></a>CLIv2

1. Instale o [CLI do Azure](/cli/azure/install-azure-cli) e [entre](/cli/azure/authenticate-azure-cli).

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
> Certifique-se de [definir a regra padrão](#change-the-default-network-access-rule) como **negar**ou se as regras de rede não têm nenhum efeito.

## <a name="exceptions"></a>Exceções

As regras de rede ajudam a criar um ambiente seguro para conexões entre seus aplicativos e seus dados para a maioria dos cenários. No entanto, alguns aplicativos usam serviços que não podem ser isolados exclusivamente por meio de regras de rede virtual ou de endereço IP. Mas esses serviços devem ser concedidos ao armazenamento para habilitar a funcionalidade completa do aplicativo. Nessas situações, você pode usar a configuração ***permitir que serviços confiáveis da Microsoft...*** para habilitar o acesso aos seus dados, logs ou análises.

### <a name="trusted-microsoft-services"></a>Serviços confiáveis da Microsoft

Alguns serviços da Microsoft operam de redes que não podem ser incluídas em suas regras de rede. Você pode conceder a um subconjunto desses serviços confiáveis da Microsoft acesso à conta de armazenamento, mantendo as regras de rede para outros aplicativos. Esses serviços confiáveis podem usar a autenticação forte para se conectar à sua conta de armazenamento com segurança. Habilitamos dois tipos de acesso confiável para serviços da Microsoft.

- Os recursos de alguns serviços, **se registrados em sua assinatura**, podem acessar contas de armazenamento **na mesma assinatura** somente para operações SELECT, como gravar logs ou para backup.
- As instâncias de recurso de alguns serviços podem receber acesso explícito à sua conta de armazenamento [**atribuindo uma função de RBAC**](storage-auth-aad.md#assign-rbac-roles-for-access-rights) à instância de recurso.


Quando você habilita a exceção **permitir serviços confiáveis da Microsoft...** , esses serviços (se registrados em sua assinatura) recebem acesso à conta de armazenamento para operações SELECT, conforme descrito:

| Serviço                  | Nome do provedor de recursos     | Objetivo                            |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft. Recoveryservices | Execute backups e restaurações de discos não gerenciados em máquinas virtuais IAAS. (não é necessário para discos gerenciados). [Saiba mais](/azure/backup/backup-introduction-to-azure-backup). |
| Azure Data Box           | Microsoft. Data Box          | Permite a importação de dados para o Azure usando Data Box. [Saiba mais](/azure/databox/data-box-overview). |
| Azure DevTest Labs       | Microsoft. DevTestLab       | Criação de imagem personalizada e instalação de artefato. [Saiba mais](/azure/devtest-lab/devtest-lab-overview). |
| Azure Event Grid         | Microsoft. EventGrid        | Habilite a publicação de eventos do armazenamento de BLOBs e permita que a grade de eventos publique nas filas de armazenamento. Saiba mais sobre [eventos de armazenamento de BLOBs](/azure/event-grid/event-sources) e [publicação em filas](/azure/event-grid/event-handlers). |
| Azure Event Hubs         | Microsoft. EventHub         | Arquive dados com a captura de hubs de eventos. [Saiba Mais](/azure/event-hubs/event-hubs-capture-overview). |
| Azure File Sync          | Microsoft. StorageSync      | Permite transformar seu servidor de arquivos local em um cache para compartilhamentos de arquivos do Azure. Permitir a sincronização de vários sites, recuperação rápida de desastres e backup no lado da nuvem. [Saber mais](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft. HDInsight        | Provisione o conteúdo inicial do sistema de arquivos padrão para um novo cluster HDInsight. [Saiba mais](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/). |
| Azure Machine Learning | Microsoft.MachineLearningServices | Os espaços de trabalho Azure Machine Learning autorizados gravam a saída, os modelos e os logs do experimento no armazenamento de BLOBs. [Saiba mais](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace).   
| Azure Monitor            | Microsoft. insights         | Permite gravar dados de monitoramento em uma conta de armazenamento protegida [saiba mais](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Redes do Azure         | Microsoft.Network          | Armazene e analise os logs de tráfego de rede. [Saiba mais](/azure/network-watcher/network-watcher-packet-capture-overview). |
| Azure Site Recovery      | Microsoft. SiteRecovery     | Habilite a replicação para recuperação de desastre de máquinas virtuais IaaS do Azure ao usar as contas de armazenamento de cache, origem ou destino habilitadas para firewall.  [Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

A exceção **permitir serviços confiáveis da Microsoft...** permite que uma instância específica dos serviços abaixo acesse a conta de armazenamento, se você atribuir explicitamente uma função RBAC à [identidade gerenciada atribuída pelo sistema](../../active-directory/managed-identities-azure-resources/overview.md) para essa instância de recurso.

| Serviço                        | Nome do provedor de recursos          | Objetivo                            |
| :----------------------------- | :------------------------------ | :--------------------------------- |
| Azure Data Factory             | Microsoft. datafactory/fábricas | Permite o acesso a contas de armazenamento por meio do tempo de execução do ADF. |
| Azure Logic Apps               | Microsoft. Logic/workflows       | Permite que os aplicativos lógicos acessem contas de armazenamento. |
| Serviço Azure Machine Learning | Microsoft.MachineLearningServices | Os espaços de trabalho Azure Machine Learning autorizados gravam a saída, os modelos e os logs do experimento no armazenamento de BLOBs. [Saiba mais](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). | 
| Azure SQL Data Warehouse       | Microsoft.Sql                   | Permite a importação e a exportação de dados de instâncias específicas do banco do dados SQL usando o polybase. [Saiba mais](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Azure Stream Analytics         | Microsoft. StreamAnalytics       | Permite que os dados de um trabalho de streaming sejam gravados no armazenamento de BLOBs. Esta funcionalidade encontra-se em pré-visualização. [Saiba mais](../../stream-analytics/blob-output-managed-identity.md). |


### <a name="storage-analytics-data-access"></a>Acesso a dados da análise de armazenamento

Em alguns casos, o acesso para ler os logs de diagnóstico e as métricas é necessário fora do limite de rede. Ao configurar o acesso de serviços confiáveis à conta de armazenamento, você pode permitir o acesso de leitura para os arquivos de log, as tabelas de métricas ou ambos. [Saiba mais sobre como trabalhar com a análise de armazenamento.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Gerenciando exceções

Você pode gerenciar exceções de regra de rede por meio do portal do Azure, PowerShell ou CLI do Azure v2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá para a conta de armazenamento que você deseja proteger.

1. Clique no menu configurações chamado **firewalls e redes virtuais**.

1. Verifique se você selecionou para permitir o acesso de **redes selecionadas**.

1. Em **exceções**, selecione as exceções que você deseja conceder.

1. Clique em **salvar** para aplicar suas alterações.

#### <a name="powershell"></a>PowerShell

1. Instale o [Azure PowerShell](/powershell/azure/install-Az-ps) e [entre](/powershell/azure/authenticate-azureps).

1. Exiba as exceções para as regras de rede da conta de armazenamento.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Configure as exceções para as regras de rede da conta de armazenamento.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Remova as exceções para as regras de rede da conta de armazenamento.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Certifique-se de [definir a regra padrão](#change-the-default-network-access-rule) como **negar**ou se a remoção de exceções não tem efeito.

#### <a name="cliv2"></a>CLIv2

1. Instale o [CLI do Azure](/cli/azure/install-azure-cli) e [entre](/cli/azure/authenticate-azure-cli).

1. Exiba as exceções para as regras de rede da conta de armazenamento.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Configure as exceções para as regras de rede da conta de armazenamento.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Remova as exceções para as regras de rede da conta de armazenamento.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Certifique-se de [definir a regra padrão](#change-the-default-network-access-rule) como **negar**ou se a remoção de exceções não tem efeito.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os pontos de extremidade de serviço de rede do Azure em [pontos de extremidade de serviço](/azure/virtual-network/virtual-network-service-endpoints-overview).

Aprofunde-se na segurança de armazenamento do Azure no [Guia de segurança do armazenamento do Azure](storage-security-guide.md).
