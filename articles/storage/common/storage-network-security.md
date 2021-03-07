---
title: Configure Firewalls de armazenamento Azure e redes virtuais | Microsoft Docs
description: Configure a segurança da rede em camadas para a sua conta de armazenamento utilizando firewalls de armazenamento Azure e Rede Virtual Azure.
services: storage
author: santoshc
ms.service: storage
ms.topic: how-to
ms.date: 03/05/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 9e395ce996bf7e6889a27fcb04b0e643cf63c58b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430892"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Configurar as firewalls e as redes virtuais do Armazenamento do Microsoft Azure

O Armazenamento do Azure fornece um modelo de segurança em camadas. Este modelo permite-lhe garantir e controlar o nível de acesso às suas contas de armazenamento que as suas aplicações e ambientes empresariais exigem, com base no tipo e subconjunto de redes ou recursos utilizados. Quando as regras de rede são configuradas, apenas as aplicações que solicitam dados sobre o conjunto especificado de redes ou através do conjunto especificado de recursos Azure podem aceder a uma conta de armazenamento. Pode limitar o acesso à sua conta de armazenamento a pedidos originários de endereços IP especificados, intervalos IP, sub-redes numa Rede Virtual Azure (VNet) ou instâncias de recursos de alguns serviços Azure.

As contas de armazenamento têm um ponto final público que é acessível através da internet. Também pode criar [Pontos Finais Privados para a sua conta de armazenamento,](storage-private-endpoints.md)que atribui um endereço IP privado do seu VNet à conta de armazenamento, e protege todo o tráfego entre o seu VNet e a conta de armazenamento por um link privado. A firewall de armazenamento Azure fornece controlo de acesso para o ponto final público da sua conta de armazenamento. Também pode utilizar a firewall para bloquear todo o acesso através do ponto final público quando utilizar pontos finais privados. A configuração da firewall de armazenamento também permite selecionar serviços de plataforma fidedignas Azure para aceder à conta de armazenamento de forma segura.

Uma aplicação que acede a uma conta de armazenamento quando as regras da rede estão em vigor ainda requer uma autorização adequada para o pedido. A autorização é suportada com credenciais do Azure Ative Directory (Azure AD) para bolhas e filas, com uma chave de acesso à conta válida, ou com um token SAS.

> [!IMPORTANT]
> A ligação das regras de firewall para a sua conta de armazenamento bloqueia os pedidos de dados por padrão, a menos que os pedidos sejam originários de um serviço que opera dentro de uma Rede Virtual Azure (VNet) ou de endereços IP públicos permitidos. Os pedidos que estão bloqueados incluem os de outros serviços Azure, do portal Azure, de serviços de registo e métricas, e assim por diante.
>
> Pode conceder acesso aos serviços Azure que operam a partir de um VNet, permitindo o tráfego a partir da sub-rede que hospeda a instância de serviço. Também pode ativar um número limitado de cenários através do mecanismo de exceções descrito abaixo. Para aceder aos dados da conta de armazenamento através do portal Azure, teria de estar numa máquina dentro do limite fidedigno (IP ou VNet) que configura.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Cenários

Para garantir a sua conta de armazenamento, deve primeiro configurar uma regra para negar o acesso ao tráfego de todas as redes (incluindo o tráfego de internet) no ponto final público, por padrão. Em seguida, deve configurar regras que concedem acesso ao tráfego a partir de VNets específicos. Também pode configurar regras para permitir o acesso ao tráfego a partir de intervalos de endereços IP públicos selecionados, permitindo ligações a clientes específicos da Internet ou no local. Esta configuração permite-lhe construir um limite de rede seguro para as suas aplicações.

Pode combinar regras de firewall que permitem o acesso a partir de redes virtuais específicas e de intervalos de endereços IP públicos na mesma conta de armazenamento. As regras de firewall de armazenamento podem ser aplicadas às contas de armazenamento existentes ou ao criar novas contas de armazenamento.

As regras de firewall de armazenamento aplicam-se ao ponto final público de uma conta de armazenamento. Não precisa de regras de acesso a firewall para permitir tráfego para pontos finais privados de uma conta de armazenamento. O processo de aprovação da criação de um ponto final privado concede acesso implícito ao tráfego a partir da sub-rede que acolhe o ponto final privado.

As regras de rede são aplicadas em todos os protocolos de rede para armazenamento Azure, incluindo REST e SMB. Para aceder a dados utilizando ferramentas como o portal Azure, Storage Explorer e AZCopy, as regras explícitas de rede devem ser configuradas.

Uma vez aplicadas as regras da rede, são aplicadas a todos os pedidos. Os tokens SAS que concedem acesso a um endereço IP específico servem para limitar o acesso do titular do token, mas não concedem um novo acesso além das regras de rede configuradas.

O tráfego de discos de máquinas virtuais (incluindo operações de montagem e desmontagem e IO do disco) não é afetado pelas regras da rede. O acesso do REST às bolhas de página está protegido pelas regras da rede.

As contas de armazenamento clássicas não suportam firewalls e redes virtuais.

Pode utilizar discos não geridos em contas de armazenamento com regras de rede aplicadas para fazer o back up e restaurar vMs criando uma exceção. Este processo está documentado na secção ['Gerir Excepções'](#manage-exceptions) deste artigo. As exceções à firewall não são aplicáveis com discos geridos, uma vez que já são geridos pelo Azure.

## <a name="change-the-default-network-access-rule"></a>Change the default network access rule (Alterar a regra de acesso de rede predefinida)

Por predefinição, as contas de armazenamento aceitam ligações de clientes em qualquer rede. Para limitar o acesso às redes selecionadas, primeiro tem de alterar a ação predefinida.

> [!WARNING]
> Fazer alterações às regras de rede pode afetar a capacidade de ligação das suas aplicações ao Armazenamento do Azure. A definição da regra de rede padrão para **negar** bloqueia todos os acessos aos dados, a menos que sejam também aplicadas regras específicas de rede que **concedem** o acesso. Certifique-se de que concede acesso a quaisquer redes permitidas através das regras de rede antes de alterar a regra predefinida para negar o acesso.

### <a name="managing-default-network-access-rules"></a>Gerir regras de acesso de rede predefinidas

Pode gerir as regras de acesso à rede padrão para contas de armazenamento através do portal Azure, PowerShell ou CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Aceda à conta de armazenamento que pretende proteger.

2. Selecione no menu de definições chamado **Networking**.

3. Para negar o acesso por padrão, opte por permitir o acesso a partir de **redes Selecionadas.** Para permitir tráfego de todas as redes, opte por permitir o acesso a partir de **Todas as redes**.

4. Selecione **Guardar** para aplicar as suas alterações.

<a id="powershell"></a>

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Instale o [Azure PowerShell](/powershell/azure/install-Az-ps) e [inscreva-se em](/powershell/azure/authenticate-azureps).

2. Mostrar o estado da regra predefinida para a conta de armazenamento.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

3. Desacorde a regra padrão para negar o acesso à rede por predefinição.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

4. Desafine a regra padrão para permitir o acesso à rede por predefinição.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instale o [Azure CLI](/cli/azure/install-azure-cli) e [inscreva-se .](/cli/azure/authenticate-azure-cli)

2. Mostrar o estado da regra predefinida para a conta de armazenamento.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

3. Desacorde a regra padrão para negar o acesso à rede por predefinição.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

4. Desafine a regra padrão para permitir o acesso à rede por predefinição.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```
---

## <a name="grant-access-from-a-virtual-network"></a>Conceder acesso a partir de uma rede virtual

Pode configurar contas de armazenamento para permitir o acesso apenas a partir de sub-redes específicas. As sub-redes permitidas podem pertencer a um VNet na mesma subscrição, ou a uma subscrição diferente, incluindo subscrições pertencentes a um inquilino azure ative inativo diferente.

Ativar um [ponto final de serviço](../../virtual-network/virtual-network-service-endpoints-overview.md) para o Armazenamento Azure dentro do VNet. O ponto final de serviço encaminha o tráfego do VNet através de um caminho ideal para o serviço de Armazenamento Azure. As identidades da sub-rede e da rede virtual também são transmitidas a cada pedido. Os administradores podem então configurar regras de rede para a conta de armazenamento que permitem que os pedidos sejam recebidos a partir de sub-redes específicas numa VNet. Os clientes que tenham acesso através destas regras de rede devem continuar a cumprir os requisitos de autorização da conta de armazenamento para aceder aos dados.

Cada conta de armazenamento suporta até 200 regras de rede virtuais, que podem ser combinadas com [regras de rede IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Regiões de rede virtual disponíveis

Em geral, os pontos finais de serviço funcionam entre redes virtuais e instâncias de serviço na mesma região de Azure. Ao utilizar pontos finais de serviço com a Azure Storage, este âmbito cresce para incluir a [região emparelhada](../../best-practices-availability-paired-regions.md). Os pontos finais de serviço permitem a continuidade durante uma falha regional e o acesso a instâncias de armazenamento apenas geo-redundantes de leitura (RA-GRS). As regras de rede que concedem acesso de uma rede virtual a uma conta de armazenamento também concedem acesso a qualquer instância RA-GRS.

Ao planear a recuperação de desastres durante uma paragem regional, deve criar os VNets na região emparelhada com antecedência. Ativar os pontos finais de serviço para o Azure Storage, com regras de rede que concedem acesso a partir destas redes virtuais alternativas. Em seguida, aplique estas regras nas suas contas de armazenamento geo-redundantes.

> [!NOTE]
> Os pontos finais de serviço não se aplicam ao tráfego fora da região da rede virtual e do par de regiões designados. Só é possível aplicar regras de rede que concedem acesso de redes virtuais a contas de armazenamento na região primária de uma conta de armazenamento ou na região de emparelhamento designada.

### <a name="required-permissions"></a>Permissões obrigatórias

Para aplicar uma regra de rede virtual a uma conta de armazenamento, o utilizador deve ter as permissões adequadas para a adição das sub-redes. A aplicação de uma regra pode ser executada por um [Contribuinte de Conta de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor) ou por um utilizador que tenha sido autorizado a operar o fornecedor de recursos `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` [Azure](../../role-based-access-control/resource-provider-operations.md#microsoftnetwork) através de uma função Azure personalizada.

A conta de armazenamento e as redes virtuais de acesso podem estar em diferentes subscrições, incluindo subscrições que fazem parte de um inquilino AD Azure diferente.

> [!NOTE]
> A configuração de regras que concedem acesso a sub-redes em redes virtuais que fazem parte de um inquilino diferente do Azure Ative Directory são atualmente apenas suportados através de ApIs Powershell, CLI e REST. Tais regras não podem ser configuradas através do portal Azure, embora possam ser vistas no portal.

### <a name="managing-virtual-network-rules"></a>Gerir regras de rede virtual

Pode gerir as regras de rede virtuais para contas de armazenamento através do portal Azure, PowerShell ou CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Aceda à conta de armazenamento que pretende proteger.

2. Selecione no menu de definições chamado **Networking**.

3. Verifique se selecionou para permitir o acesso a partir de **redes selecionadas.**

4. Para conceder acesso a uma rede virtual com uma nova regra de rede, em **redes Virtuais**, selecione Adicionar rede **virtual existente,** selecione **redes virtuais** e opções **de Subnets** e, em seguida, selecione **Adicionar**. Para criar uma nova rede virtual e conceder-lhe acesso, **selecione Adicionar nova rede virtual.** Forneça as informações necessárias para criar a nova rede virtual e, em seguida, selecione **Criar**.

    > [!NOTE]
    > Se um ponto final de serviço para o Azure Storage não foi previamente configurado para a rede virtual e sub-redes selecionadas, pode configurá-lo como parte desta operação.
    >
    > Atualmente, apenas as redes virtuais pertencentes ao mesmo inquilino do Azure Ative Directory são mostradas para seleção durante a criação de regras. Para conceder acesso a uma sub-rede numa rede virtual pertencente a outro inquilino, utilize as APIs powershell, CLI ou REST.

5. Para remover uma regra de rede virtual ou sub-rede, selecione... para abrir o menu de contexto para a rede virtual ou sub-rede e selecione **Remover**. 

6. **selecione Guardar** para aplicar as suas alterações.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Instale o [Azure PowerShell](/powershell/azure/install-Az-ps) e [inscreva-se em](/powershell/azure/authenticate-azureps).

2. Listar as regras de rede virtual.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

3. Ativar o ponto final do serviço para o Azure Storage numa rede virtual e numa sub-rede existentes.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

4. Adicione uma regra de rede para uma rede virtual e sub-rede.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Para adicionar uma regra de rede para uma sub-rede num VNet pertencente a outro inquilino AD Azure, utilize um parâmetro **VirtualNetworkResourceId** totalmente qualificado no formulário "/subscrições/subscrições/subscrição-ID/resourceGroups/resourceGroup/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".

5. Remova uma regra de rede para uma rede virtual e sub-rede.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Certifique-se de [que define a regra padrão](#change-the-default-network-access-rule) para **negar**, ou as regras de rede não têm efeito.

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instale o [Azure CLI](/cli/azure/install-azure-cli) e [inscreva-se .](/cli/azure/authenticate-azure-cli)

2. Listar as regras de rede virtual.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

3. Ativar o ponto final do serviço para o Azure Storage numa rede virtual e numa sub-rede existentes.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

4. Adicione uma regra de rede para uma rede virtual e sub-rede.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Para adicionar uma regra para uma sub-rede num VNet pertencente a outro inquilino AD Azure, utilize um ID de sub-rede totalmente qualificado no formulário "/subscrições/ \<subscription-ID\> /resourceGroups/ \<resourceGroup-Name\> /providers/Microsoft.Network/virtualNetworks/ \<vNet-name\> /subnets/ \<subnet-name\> ".
    >
    > Pode utilizar o parâmetro **de subscrição** para recuperar o ID da sub-rede para um VNet pertencente a outro inquilino AD AZure.

5. Remova uma regra de rede para uma rede virtual e sub-rede.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Certifique-se de [que define a regra padrão](#change-the-default-network-access-rule) para **negar**, ou as regras de rede não têm efeito.

---

## <a name="grant-access-from-an-internet-ip-range"></a>Conceder acesso a partir de um intervalo de IP da Internet

Pode configurar contas de armazenamento para permitir o acesso a partir de intervalos específicos de endereços IP da Internet pública. Esta configuração permite o acesso a serviços específicos baseados na Internet e redes no local e bloqueia o tráfego geral de internet.

Fornecer intervalos de endereços de internet permitidos utilizando [a notação CIDR](https://tools.ietf.org/html/rfc4632) no formulário *16.17.18.0/24* ou como endereços IP individuais como *16.17.18.19*.

   > [!NOTE]
   > Não são suportadas pequenas gamas de endereços que utilizem tamanhos de prefixo "/31" ou "/32". Estas gamas devem ser configuradas utilizando regras individuais de endereço IP.

As regras da rede IP só são permitidas para endereços IP **de internet pública.** Os intervalos de endereços IP reservados para redes privadas (tal como definido no [RFC 1918)](https://tools.ietf.org/html/rfc1918#section-3)não são permitidos nas regras de IP. As redes privadas incluem endereços que começam com _10.*_, _172.16.*_  -  _172.31.*_ e _192.168.*_.

   > [!NOTE]
   > As regras da rede IP não têm qualquer efeito sobre os pedidos originários da mesma região de Azure que a conta de armazenamento. Utilize [regras de rede virtuais](#grant-access-from-a-virtual-network) para permitir pedidos da mesma região.

  > [!NOTE]
  > Os serviços implantados na mesma região que a conta de armazenamento utilizam endereços IP privados Azure para comunicação. Assim, não é possível restringir o acesso a serviços Azure específicos com base na sua gama de endereços IP de saída pública.

Apenas endereços IPV4 são suportados para configuração de regras de firewall de armazenamento.

Cada conta de armazenamento suporta até 200 regras de rede IP.

### <a name="configuring-access-from-on-premises-networks"></a>Configurar o acesso a partir de redes no local

Para conceder acesso das suas redes no local à sua conta de armazenamento com uma regra de rede IP, tem de identificar os endereços IP virados para a Internet utilizados pela sua rede. Contacte o administrador da rede para obter ajuda.

Se estiver a utilizar o [ExpressRoute](../../expressroute/expressroute-introduction.md) a partir das suas instalações, para espreitar publicamente ou para o microsoft espreitar, terá de identificar os endereços IP NAT utilizados. Para peering público, cada circuito ExpressRoute, por predefinição, utiliza dois endereços IP NAT que são aplicados ao tráfego de serviço do Azure quando o tráfego entra no backbone de rede do Microsoft Azure. Para o estojo da Microsoft, os endereços IP NAT utilizados são fornecidos pelo cliente ou são fornecidos pelo prestador de serviços. Para permitir o acesso aos recursos de serviço, tem de permitir estes endereços IP públicos na definição da firewall do IP dos recursos. Para localizar os endereços IP do circuito ExpressRoute de peering público, [abra um pedido de suporte no ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) através do portal do Azure. Saiba mais sobre [NAT para peering público e da Microsoft do ExpressRoute.](../../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Gestão das regras de rede IP

Pode gerir as regras da rede IP para contas de armazenamento através do portal Azure, PowerShell ou CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Aceda à conta de armazenamento que pretende proteger.

2. Selecione no menu de definições chamado **Networking**.

3. Verifique se selecionou para permitir o acesso a partir de **redes selecionadas.**

4. Para conceder acesso a uma gama IP da Internet, insira o endereço IP ou o intervalo de endereços (em formato CIDR) no Âmbito de **Endereços de Firewall**  >  .

5. Para remover uma regra de rede IP, selecione o ícone do caixote do lixo ao lado do intervalo de endereços.

6. Selecione **Guardar** para aplicar as suas alterações.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Instale o [Azure PowerShell](/powershell/azure/install-Az-ps) e [inscreva-se em](/powershell/azure/authenticate-azureps).

2. Listar as regras da rede IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

3. Adicione uma regra de rede para um endereço IP individual.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

4. Adicione uma regra de rede para um intervalo de endereços IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

5. Remova uma regra de rede para um endereço IP individual.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

6. Remova uma regra de rede para um intervalo de endereços IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Certifique-se de [que define a regra padrão](#change-the-default-network-access-rule) para **negar**, ou as regras de rede não têm efeito.

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instale o [Azure CLI](/cli/azure/install-azure-cli) e [inscreva-se .](/cli/azure/authenticate-azure-cli)

1. Listar as regras da rede IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

2. Adicione uma regra de rede para um endereço IP individual.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

3. Adicione uma regra de rede para um intervalo de endereços IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

4. Remova uma regra de rede para um endereço IP individual.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

5. Remova uma regra de rede para um intervalo de endereços IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Certifique-se de [que define a regra padrão](#change-the-default-network-access-rule) para **negar**, ou as regras de rede não têm efeito.

---

<a id="grant-access-specific-instances"></a>

## <a name="grant-access-from-azure-resource-instances-preview"></a>Conceder acesso a partir de instâncias de recursos Azure (pré-visualização)

Em alguns casos, uma aplicação pode depender de recursos Azure que não podem ser isolados através de uma rede virtual ou uma regra de endereço IP. No entanto, ainda gostaria de garantir e restringir o acesso da conta de armazenamento apenas aos recursos Azure da sua aplicação. Pode configurar contas de armazenamento para permitir o acesso a casos específicos de recursos de alguns serviços Azure, criando uma regra de instância de recursos. 

Os tipos de operações que uma instância de recursos pode realizar nos dados da conta de armazenamento são determinados pelas atribuições de [funções Azure](storage-auth-aad.md#assign-azure-roles-for-access-rights) da instância de recursos. As instâncias de recursos devem ser do mesmo inquilino que a sua conta de armazenamento, mas podem pertencer a qualquer subscrição no arrendatário.

> [!NOTE]
> Esta funcionalidade está em pré-visualização pública e está disponível em todas as regiões de nuvem pública.

> [!NOTE]
> Atualmente, as regras de instância de recursos são apenas suportadas para a Azure Synapse. O suporte a outros serviços Azure listados no [acesso Fidedigno com base na](#trusted-access-system-assigned-managed-identity) secção de identidade gerida atribuída pelo sistema deste artigo estará disponível nas próximas semanas.


### <a name="portal"></a>[Portal](#tab/azure-portal)

Pode adicionar ou remover as regras da rede de recursos no portal Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) para começar.

2. Localize a sua conta de armazenamento e apresente a visão geral da conta.

3. Selecione **Networking** para exibir a página de configuração para networking.

4. Na lista de drop-down **do tipo de recurso,** escolha o tipo de recurso da sua instância de recursos. 

5. Na lista de drop-down de **nomes de instância,** escolha a instância de recurso. Também pode optar por incluir todas as instâncias de recursos no inquilino ativo, subscrição ou grupo de recursos.

6. Selecione **Guardar** para aplicar as suas alterações. A instância de recursos aparece na secção **de instâncias de recursos** da página de definições de rede. 

Para remover a instância do recurso, selecione o ícone de eliminação :::image type="icon" source="media/storage-network-security/delete-icon.png"::: () ao lado da instância do recurso.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pode utilizar comandos PowerShell para adicionar ou remover as regras da rede de recursos.

> [!IMPORTANT]
> Certifique-se de [que define a regra padrão](#change-the-default-network-access-rule) para **negar**, ou as regras de rede não têm efeito.

#### <a name="install-the-preview-module"></a>Instale o módulo de pré-visualização

Instale a versão mais recente do módulo PowershellGet. Em seguida, feche e reabra a consola PowerShell.

```powershell
install-Module PowerShellGet –Repository PSGallery –Force  
```

Instale o módulo de pré-visualização **do armazenamento Az.**

```powershell
Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.0.1-preview -AllowClobber -AllowPrerelease -Force 
```

Para obter mais informações sobre como instalar módulos PowerShell, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps)

#### <a name="grant-access"></a>Conceder acesso

Adicione uma regra de rede que concede acesso a partir de uma instância de recursos.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId

```

Especifique vários casos de recursos de uma só vez modificando o conjunto de regras de rede.

```powershell
$resourceId1 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$resourceId2 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mySQLServer"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule (@{ResourceId=$resourceId1;TenantId=$tenantId},@{ResourceId=$resourceId2;TenantId=$tenantId}) 
```

#### <a name="remove-access"></a>Remover o acesso

Remova uma regra de rede que concede acesso a partir de uma instância de recursos.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Remove-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId  
```

Remova todas as regras de rede que concedem acesso a instâncias de recursos.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule @()  
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Ver uma lista de instâncias de recursos permitidas

Veja uma lista completa de casos de recursos a quem foi concedido acesso à conta de armazenamento.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

$rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
$rule.ResourceAccessRules 
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pode utilizar comandos Azure CLI para adicionar ou remover as regras da rede de recursos.

#### <a name="install-the-preview-extension"></a>Instale a extensão de pré-visualização

1. Abra o [Azure Cloud Shell](../../cloud-shell/overview.md), ou se [instalou](/cli/azure/install-azure-cli) o Azure CLI localmente, abra uma aplicação de consola de comando como o Windows PowerShell.

2. Em seguida, verifique se a versão do Azure CLI que instalou é `2.13.0` ou superior utilizando o seguinte comando.

   ```azurecli
   az --version
   ```

   Se a sua versão do Azure CLI for inferior `2.13.0` a , então instale uma versão posterior. Consulte [a Instalação do Azure CLI](/cli/azure/install-azure-cli).

3. Digite o seguinte comando para instalar a extensão de pré-visualização.

   ```azurecli
   az extension add -n storage-preview
   ```

#### <a name="grant-access"></a>Conceder acesso

Adicione uma regra de rede que concede acesso a partir de uma instância de recursos.

```azurecli
az storage account network-rule add \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="remove-access"></a>Remover o acesso

Remova uma regra de rede que concede acesso a partir de uma instância de recursos.

```azurecli
az storage account network-rule remove \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Ver uma lista de instâncias de recursos permitidas

Veja uma lista completa de casos de recursos a quem foi concedido acesso à conta de armazenamento.

```azurecli
az storage account network-rule list \
    -g myResourceGroup \
    --account-name mystorageaccount
```

---

<a id="exceptions"></a>
<a id="trusted-microsoft-services"></a>

## <a name="grant-access-to-trusted-azure-services"></a>Conceder acesso a serviços de confiança da Azure 

Alguns serviços Azure operam a partir de redes que não podem ser incluídas nas suas regras de rede. Pode conceder a um subconjunto de serviços Azure de confiança o acesso à conta de armazenamento, mantendo ao mesmo tempo regras de rede para outras aplicações. Estes serviços fidedignos utilizarão então uma autenticação forte para se ligarem de forma segura à sua conta de armazenamento.

Pode conceder acesso a serviços de Azure confiáveis criando uma exceção à regra da rede. Para obter orientação passo a passo, consulte a secção [de exceções](#manage-exceptions) de Gestão deste artigo.

Quando concede acesso a serviços fidedignos da Azure, concede os seguintes tipos de acesso:

- Acesso fidedigno para operações selecionadas a recursos registados na sua subscrição.
- Acesso confiável a recursos baseados na identidade gerida atribuída pelo sistema.

<a id="trusted-access-resources-in-subscription"></a>

### <a name="trusted-access-for-resources-registered-in-your-subscription"></a>Acesso confiável para recursos registados na sua subscrição

Os recursos de alguns serviços, **quando registados na sua subscrição,** podem aceder à sua conta de armazenamento **na mesma subscrição** para operações selecionadas, como escrever registos ou backup.  A tabela seguinte descreve cada serviço e as operações permitidas. 

| Serviço                  | Nome do fornecedor de recursos     | Operações permitidas                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | Executar backups e restauros de discos não geridos em máquinas virtuais IAAS. (não é necessário para discos geridos). [Saiba mais](../../backup/backup-overview.md). |
| Azure Data Box           | Microsoft.DataBox          | Permite a importação de dados para a Azure usando a Data Box. [Saiba mais](../../databox/data-box-overview.md). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Criação de imagem personalizada e instalação de artefactos. [Saiba mais](../../devtest-labs/devtest-lab-overview.md). |
| Azure Event Grid         | Microsoft.EventGrid        | Permitir a publicação do evento Blob Storage e permitir que a Grade de Eventos publique para as filas de armazenamento. Saiba mais sobre [eventos de armazenamento de bolhas](../../event-grid/overview.md#event-sources) e [publicação em filas.](../../event-grid/event-handlers.md) |
| Azure Event Hubs         | Microsoft.EventHub         | Arquivar dados com a captura de centros de eventos. [Saiba Mais](../../event-hubs/event-hubs-capture-overview.md). |
| Azure File Sync          | Microsoft.StorageSync      | Permite-lhe transformar o seu servidor de ficheiros on-prem para um cache para ações do Ficheiro Azure. Permitindo sincronização multi-site, rápida recuperação de desastres e backup do lado da nuvem. [Saiba mais](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Fornecendo o conteúdo inicial do sistema de ficheiros predefinidos para um novo cluster HDInsight. [Saiba mais](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). |
| Exportação de Importação de Azure      | Microsoft.ImportExport     | Permite a importação de dados para a Azure Storage ou exportação de dados da Azure Storage utilizando o serviço de importação/exportação de armazenamento Azure. [Saiba mais](../../import-export/storage-import-export-service.md).  |
| Azure Monitor            | Microsoft.Insights         | Permite escrever dados de monitorização para uma conta de armazenamento segura, incluindo registos de recursos, registos de login e auditoria do Azure Ative Directory e registos de auditoria. [Saiba mais](../../azure-monitor/roles-permissions-security.md). |
| Rede Azure         | Microsoft.Network          | Armazenar e analisar registos de tráfego de rede, incluindo através dos serviços de Network Watcher e Traffic Analytics. [Saiba mais](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Ativar a replicação para a recuperação de desastres de máquinas virtuais Azure IaaS quando utilizar contas de armazenamento ativadas por firewall ou alvo.  [Saiba mais](../../site-recovery/azure-to-azure-tutorial-enable-replication.md). |

<a id="trusted-access-system-assigned-managed-identity"></a>

### <a name="trusted-access-based-on-system-assigned-managed-identity"></a>Acesso fidedigno com base na identidade gerida atribuída pelo sistema

A tabela que se segue lista os serviços que podem ter acesso aos dados da sua conta de armazenamento se os casos de recursos desses serviços receberem a permissão adequada. Para conceder permissão, deve atribuir explicitamente [uma função Azure](storage-auth-aad.md#assign-azure-roles-for-access-rights) à [identidade gerida atribuída pelo sistema](../../active-directory/managed-identities-azure-resources/overview.md) para cada instância de recursos. Neste caso, o âmbito de acesso, por exemplo, corresponde à função Azure atribuída à identidade gerida. 

> [!TIP]
> A forma recomendada de conceder acesso a recursos específicos é utilizar regras de instância de recursos. Para conceder acesso a instâncias específicas de recursos, consulte o acesso do Grant a partir da secção [de instâncias de recursos Azure (pré-visualização)](#grant-access-specific-instances) deste artigo.


| Serviço                        | Nome do fornecedor de recursos                 | Objetivo            |
| :----------------------------- | :------------------------------------- | :----------------- |
| API Management do Azure           | Microsoft.ApiManagement/service        | Permite o acesso do serviço Api Management às contas de armazenamento por trás do firewall utilizando políticas. [Saiba mais](../../api-management/api-management-authentication-policies.md#use-managed-identity-in-send-request-policy). |
| Azure Cognitive Search         | Microsoft.Search/searchServices        | Permite que os serviços de Pesquisa Cognitiva acedam a contas de armazenamento para indexar, processar e consultar. |
| Serviços Cognitivos do Azure       | Microsoft.CognitiveService/contas    | Permite que os Serviços Cognitivos acedam a contas de armazenamento. |
| Tarefas do Azure Container Registry | Microsoft.ContainerRegistry/registries | As tarefas ACR podem aceder às contas de armazenamento ao construir imagens de contentores. |
| Azure Data Factory             | Microsoft.DataFactory/fábricas        | Permite o acesso às contas de armazenamento através do tempo de execução da ADF. |
| Azure Data Share               | Microsoft.DataShare/contas           | Permite o acesso às contas de armazenamento através do Data Share. |
| Azure DevTest Labs             | Microsoft.DevTestLab/laboratórios              | Permite o acesso às contas de armazenamento através da DevTest Labs. |
| Azure IoT Hub                  | Microsoft.Devices/IotHubs              | Permite que os dados de um hub IoT sejam escritos para o armazenamento blob. [Saiba mais](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Azure Logic Apps               | Microsoft.Logic/workflows              | Permite que as aplicações lógicas acedam a contas de armazenamento. [Saiba mais](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Serviço Azure Machine Learning | Microsoft.MachineLearningServices      | Os espaços de trabalho autorizados de Aprendizagem automática Azure escrevem a saída de experiências, modelos e registos para o armazenamento blob e lêem os dados. [Saiba mais](../../machine-learning/how-to-network-security-overview.md#secure-the-workspace-and-associated-resources). |
| Serviços de Multimédia do Azure           | Microsoft.Media/mediaservices          | Permite o acesso a contas de armazenamento através dos Serviços de Media. |
| Azure Migrate                  | Microsoft.Migrate/migrateprojects      | Permite o acesso às contas de armazenamento através do Azure Migrate. |
| Azure Purview                  | Microsoft.Purview/contas             | Permite que o Purview aceda às contas de armazenamento. |
| Azure Remote Rendering         | Microsoft.MixedReality/remoteRenderIngS | Permite o acesso às contas de armazenamento através da Renderização Remota. |
| Azure Site Recovery            | Microsoft.RecoveryServices/cofres      | Permite o acesso às contas de armazenamento através da Recuperação do Site. |
| Base de Dados SQL do Azure             | Microsoft.Sql                          | Permite [escrever](../../azure-sql/database/audit-write-storage-account-behind-vnet-firewall.md) dados de auditoria para contas de armazenamento por trás da firewall. |
| Azure Synapse Analytics        | Microsoft.Sql                          | Permite a importação e exportação de dados a partir de bases de dados SQL específicas utilizando a declaração COPY ou PolyBase (em pool dedicado), ou a `openrowset` função e tabelas externas em pool sem servidor. [Saiba mais](../../azure-sql/database/vnet-service-endpoint-rule-overview.md). |
| Azure Stream Analytics         | Microsoft.StreamAnalytics              | Permite que os dados de um trabalho de streaming sejam escritos para o armazenamento blob. [Saiba mais](../../stream-analytics/blob-output-managed-identity.md). |
| Azure Synapse Analytics        | Microsoft.Synapse/workspaces           | Permite o acesso aos dados no Azure Storage a partir da Azure Synapse Analytics. |

## <a name="grant-access-to-storage-analytics"></a>Conceder acesso à análise de armazenamento

Em alguns casos, o acesso a registos de recursos e métricas de leitura é necessário fora do limite da rede. Ao configurar o acesso de serviços fidedignos à conta de armazenamento, pode permitir o acesso de leitura para os ficheiros de registo, tabelas de métricas ou ambos, criando uma exceção à regra da rede. Para obter orientação passo a passo, consulte a secção **de exceções de Gestão** abaixo. Para saber mais sobre como trabalhar com a análise de armazenamento, consulte [a analítica de armazenamento Azure para recolher dados de registos e métricas.](./storage-analytics.md) 

<a id="manage-exceptions"></a>

## <a name="manage-exceptions"></a>Gerir exceções

Pode gerir exceções de regras de rede através do portal Azure, PowerShell ou Azure CLI v2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Aceda à conta de armazenamento que pretende proteger.

2. Selecione no menu de definições chamado **Networking**.

3. Verifique se selecionou para permitir o acesso a partir de **redes selecionadas.**

4. Em **Exceções,** selecione as exceções que pretende conceder.

5. Selecione **Guardar** para aplicar as suas alterações.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Instale o [Azure PowerShell](/powershell/azure/install-Az-ps) e [inscreva-se em](/powershell/azure/authenticate-azureps).

2. Exiba as exceções para as regras da rede de conta de armazenamento.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

3. Configure as exceções às regras da rede de contas de armazenamento.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

4. Remova as exceções às regras da rede de conta de armazenamento.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Certifique-se de [que define a regra de incumprimento](#change-the-default-network-access-rule) para **negar,** ou remover exceções não tem efeito.

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instale o [Azure CLI](/cli/azure/install-azure-cli) e [inscreva-se .](/cli/azure/authenticate-azure-cli)

2. Exiba as exceções para as regras da rede de conta de armazenamento.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

3. Configure as exceções às regras da rede de contas de armazenamento.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

4. Remova as exceções às regras da rede de conta de armazenamento.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Certifique-se de [que define a regra de incumprimento](#change-the-default-network-access-rule) para **negar,** ou remover exceções não tem efeito.

---

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os pontos finais do serviço Azure Network nos [pontos finais do Serviço](../../virtual-network/virtual-network-service-endpoints-overview.md).

Investigue mais profundamente a segurança do Azure Storage no [guia de segurança do Azure Storage](../blobs/security-recommendations.md).