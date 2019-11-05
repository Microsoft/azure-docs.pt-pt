---
title: Redes Virtuais
titleSuffix: Azure Cognitive Services
description: Configure a segurança de rede em camadas para seus recursos de serviços cognitivas.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 36e0742101203764a731dd2007d239593a500bfa
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507926"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Configurar redes virtuais de serviços cognitivas do Azure

Os serviços cognitivas do Azure fornecem um modelo de segurança em camadas. Esse modelo permite que você proteja suas contas de serviços cognitivas para um subconjunto específico de redes. Quando as regras de rede são configuradas, somente os aplicativos que solicitam dados no conjunto especificado de redes podem acessar a conta. Você pode limitar o acesso a seus recursos com a filtragem de solicitações. Permitir somente solicitações provenientes de endereços IP especificados, intervalos de IP ou de uma lista de sub-redes em [redes virtuais do Azure](../virtual-network/virtual-networks-overview.md). Se você estiver interessado nessa oferta, precisará [solicitar acesso de visualização](https://aka.ms/cog-svc-vnet-signup).

Um aplicativo que acessa um recurso de serviços cognitivas quando as regras de rede estão em vigor requer autorização. Há suporte para autorização com as credenciais do [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) ou com uma chave de API válida.

> [!IMPORTANT]
> A ativação de regras de firewall para sua conta de serviços cognitivas bloqueia solicitações de entrada de dados por padrão. Para permitir solicitações por meio do, uma das seguintes condições precisa ser atendida:
> * A solicitação deve originar-se de um serviço operando em uma rede virtual do Azure (VNet) na lista de sub-rede permitida da conta de serviços cognitivas de destino.
> * Ou a solicitação deve originar-se de uma lista de endereços IP permitidos.
>
> As solicitações que estão bloqueadas incluem aquelas de outros serviços do Azure, da portal do Azure, do log e dos serviços de métrica e assim por diante.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Cenários

Para proteger seu recurso de serviços cognitivas, primeiro você deve configurar uma regra para negar o acesso ao tráfego de todas as redes (incluindo o tráfego de Internet) por padrão. Em seguida, você deve configurar regras que concedem acesso ao tráfego de VNets específicas. Essa configuração permite que você crie um limite de rede seguro para seus aplicativos. Você também pode configurar regras para conceder acesso ao tráfego de selecionar intervalos de endereços IP públicos da Internet, permitindo conexões de clientes locais ou da Internet específicos.

As regras de rede são impostas em todos os protocolos de rede para serviços cognitivas do Azure, incluindo REST e WebSocket. Para acessar dados usando ferramentas como os consoles de teste do Azure, regras de rede explícitas devem ser configuradas. Você pode aplicar regras de rede a recursos de serviços cognitivas existentes ou ao criar novos recursos de serviços cognitivas. Depois que as regras de rede são aplicadas, elas são impostas para todas as solicitações.

## <a name="supported-regions-and-service-offerings"></a>Regiões e ofertas de serviço com suporte

O suporte de rede virtual para serviços cognitivas é limitado às regiões do Azure *canário-EUA Central EUAP*, *Europa Ocidental*e *oeste dos EUA 2* . Além disso, nem todas as ofertas de serviço cognitiva dão suporte a redes virtuais. As ofertas de serviço cognitiva a seguir permitem o uso de redes virtuais. Se a oferta de serviço não estiver listada aqui, ela não oferecerá suporte a redes virtuais.

> [!div class="checklist"]
> * [Detector de anomalias](./anomaly-detector/index.yml)
> * [Imagem Digitalizada](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Visão Personalizada](./custom-vision-service/index.yml)
> * [Sorridente](./face/index.yml)
> * [Reconhecedor de formulário](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personalizador](./personalizer/index.yml)
> * [Análise de Texto](./text-analytics/index.yml)

## <a name="change-the-default-network-access-rule"></a>Alterar a regra de acesso de rede padrão

Por padrão, os recursos de serviços cognitivas aceitam conexões de clientes em qualquer rede. Para limitar o acesso às redes selecionadas, você deve primeiro alterar a ação padrão.

> [!WARNING]
> Fazer alterações nas regras de rede pode afetar a capacidade dos seus aplicativos de se conectar aos serviços cognitivas do Azure. A definição da regra de rede padrão para **negar** bloqueia todo o acesso aos dados, a menos que regras de rede específicas que **concedem** acesso também sejam aplicadas. Certifique-se de conceder acesso a qualquer rede permitida usando regras de rede antes de alterar a regra padrão para negar acesso. Se você estiver permitindo a listagem de endereços IP para sua rede local, certifique-se de adicionar todos os endereços IP públicos de saída possíveis de sua rede local.

### <a name="managing-default-network-access-rules"></a>Gerenciando regras de acesso de rede padrão

Você pode gerenciar regras de acesso de rede padrão para recursos de serviços cognitivas por meio do portal do Azure, do PowerShell ou do CLI do Azure.

# <a name="azure-portaltabportal"></a>[Portal do Azure](#tab/portal)

1. Vá para o recurso de serviços cognitivas que você deseja proteger.

1. Selecione o menu **Gerenciamento de recursos** chamado **rede virtual**.

   ![Opção de rede virtual](media/vnet/virtual-network-blade.png)

1. Para negar acesso por padrão, escolha permitir o acesso de **redes selecionadas**. Com a configuração de **redes selecionadas** sozinha, não acompanhada por **redes virtuais** ou **intervalos de endereços** configurados, todo o acesso é efetivamente negado. Quando todo o acesso é negado, as solicitações que tentam consumir o recurso de serviços cognitivas não são permitidas. O portal do Azure, Azure PowerShell ou CLI do Azure ainda pode ser usado para configurar o recurso de serviços cognitivas.
1. Para permitir o tráfego de todas as redes, escolha permitir o acesso de **todas as redes**.

   ![Negação de redes virtuais](media/vnet/virtual-network-deny.png)

1. Selecione **salvar** para aplicar suas alterações.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

1. Instale o [Azure PowerShell](/powershell/azure/install-az-ps) e [entre](/powershell/azure/authenticate-azureps)ou selecione **experimentar**.

1. Exiba o status da regra padrão para o recurso de serviços cognitivas.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Defina a regra padrão para negar o acesso à rede por padrão.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Defina a regra padrão para permitir o acesso à rede por padrão.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instale o [CLI do Azure](/cli/azure/install-azure-cli) e [entre](/cli/azure/authenticate-azure-cli)ou selecione **experimentar**.

1. Exiba o status da regra padrão para o recurso de serviços cognitivas.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Defina a regra padrão para negar o acesso à rede por padrão.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Defina a regra padrão para permitir o acesso à rede por padrão.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>Conceder acesso de uma rede virtual

Você pode configurar recursos de serviços cognitivas para permitir o acesso somente de sub-redes específicas. As sub-redes permitidas podem pertencer a uma VNet na mesma assinatura ou em uma assinatura diferente, incluindo assinaturas que pertencem a um locatário de Azure Active Directory diferente.

Habilite um [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) para serviços cognitivas do Azure na VNet. O ponto de extremidade de serviço roteia o tráfego da VNet por meio de um caminho ideal para o serviço de serviços cognitivas do Azure. As identidades da sub-rede e da rede virtual também são transmitidas com cada solicitação. Os administradores podem configurar as regras de rede para o recurso de serviços cognitivas que permitem que as solicitações sejam recebidas de sub-redes específicas em uma VNet. Os clientes com acesso concedido por essas regras de rede devem continuar atendendo aos requisitos de autorização do recurso de serviços cognitivas para acessar os dados.

Cada recurso de serviços cognitivas dá suporte a até 100 regras de rede virtual, que podem ser combinadas com [regras de rede IP](#grant-access-from-an-internet-ip-range).

### <a name="required-permissions"></a>Permissões obrigatórias

Para aplicar uma regra de rede virtual a um recurso de serviços cognitivas, o usuário deve ter as permissões apropriadas para as sub-redes que estão sendo adicionadas. A permissão necessária é a função *colaborador* padrão ou a função *colaborador de serviços cognitivas* . As permissões necessárias também podem ser adicionadas às definições de função personalizadas.

O recurso de serviços cognitivas e as redes virtuais com acesso concedido podem estar em assinaturas diferentes, incluindo assinaturas que fazem parte de um locatário diferente do Azure AD.

> [!NOTE]
> A configuração de regras que concedem acesso a sub-redes em redes virtuais que fazem parte de um locatário Azure Active Directory diferente atualmente só tem suporte por meio do PowerShell, da CLI e de APIs REST. Essas regras não podem ser configuradas por meio do portal do Azure, embora possam ser exibidas no Portal.

### <a name="managing-virtual-network-rules"></a>Gerenciando regras de rede virtual

Você pode gerenciar regras de rede virtual para recursos de serviços cognitivas por meio do portal do Azure, do PowerShell ou do CLI do Azure.

# <a name="azure-portaltabportal"></a>[Portal do Azure](#tab/portal)

1. Vá para o recurso de serviços cognitivas que você deseja proteger.

1. Selecione o menu **Gerenciamento de recursos** chamado **rede virtual**.

1. Verifique se você selecionou para permitir o acesso de **redes selecionadas**.

1. Para conceder acesso a uma rede virtual com uma regra de rede existente, em **redes virtuais**, selecione **Adicionar rede virtual existente**.

   ![Adicionar vNet existente](media/vnet/virtual-network-add-existing.png)

1. Selecione as opções **redes virtuais** e **sub-redes** e, em seguida, selecione **habilitar**.

   ![Adicionar detalhes de vNet existentes](media/vnet/virtual-network-add-existing-details.png)

1. Para criar uma nova rede virtual e conceder acesso a ela, selecione **Adicionar nova rede virtual**.

   ![Adicionar nova vNet](media/vnet/virtual-network-add-new.png)

1. Forneça as informações necessárias para criar a nova rede virtual e, em seguida, selecione **criar**.

   ![Criar vNet](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Se um ponto de extremidade de serviço para serviços cognitivas do Azure não foi configurado anteriormente para a rede virtual e as sub-redes selecionadas, você poderá configurá-lo como parte dessa operação.
    >
    > Atualmente, somente as redes virtuais que pertencem ao mesmo locatário Azure Active Directory são mostradas para seleção durante a criação da regra. Para conceder acesso a uma sub-rede em uma rede virtual que pertence a outro locatário, use o PowerShell, a CLI ou as APIs REST.

1. Para remover uma rede virtual ou regra de sub-rede, selecione **...** para abrir o menu de contexto da rede virtual ou sub-rede e selecione **remover**.

   ![Remover vNet](media/vnet/virtual-network-remove.png)

1. Selecione **salvar** para aplicar suas alterações.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

1. Instale o [Azure PowerShell](/powershell/azure/install-az-ps) e [entre](/powershell/azure/authenticate-azureps)ou selecione **experimentar**.

1. Listar regras de rede virtual.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Habilite o ponto de extremidade de serviço para serviços cognitivas do Azure em uma rede virtual e sub-rede existentes.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Adicione uma regra de rede para uma rede virtual e sub-rede.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > Para adicionar uma regra de rede para uma sub-rede em uma VNet que pertence a outro locatário do Azure AD, use um parâmetro **VirtualNetworkResourceId** totalmente qualificado no formato "/subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/Providers/Microsoft.Network/virtualNetworks/vNet-Name/Subnets/subnet-Name".

1. Remova uma regra de rede para uma rede virtual e sub-rede.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instale o [CLI do Azure](/cli/azure/install-azure-cli) e [entre](/cli/azure/authenticate-azure-cli)ou selecione **experimentar**.

1. Listar regras de rede virtual.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Habilite o ponto de extremidade de serviço para serviços cognitivas do Azure em uma rede virtual e sub-rede existentes.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Adicione uma regra de rede para uma rede virtual e sub-rede.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > Para adicionar uma regra para uma sub-rede em uma VNet que pertence a outro locatário do Azure AD, use uma ID de sub-rede totalmente qualificada no formato "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".
    > 
    > Você pode usar o parâmetro de **assinatura** para recuperar a ID de sub-rede de uma VNet que pertence a outro locatário do Azure AD.

1. Remova uma regra de rede para uma rede virtual e sub-rede.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```
***

> [!IMPORTANT]
> Certifique-se de [definir a regra padrão](#change-the-default-network-access-rule) como **negar**ou se as regras de rede não têm nenhum efeito.

## <a name="grant-access-from-an-internet-ip-range"></a>Conceder acesso de um intervalo de IP da Internet

Você pode configurar recursos de serviços cognitivas para permitir o acesso de intervalos de endereços IP de Internet pública específicos. Essa configuração concede acesso a serviços específicos e redes locais, bloqueando efetivamente o tráfego geral da Internet.

Forneça intervalos de endereços de Internet permitidos usando a [notação CIDR](https://tools.ietf.org/html/rfc4632) no formato `16.17.18.0/24` ou como endereços IP individuais como `16.17.18.19`.

   > [!Tip]
   > Não há suporte para os intervalos de endereços pequenos usando os tamanhos de prefixo "/31" ou "/32". Esses intervalos devem ser configurados usando regras de endereço IP individuais.

As regras de rede IP só são permitidas para endereços IP **públicos da Internet** . Os intervalos de endereços IP reservados para redes privadas (conforme definido no [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) não são permitidos em regras de IP. As redes privadas incluem endereços que começam com `10.*`, `172.16.*` - `172.31.*`e `192.168.*`.

   > [!NOTE]
   > As regras de rede IP não têm nenhum efeito em solicitações provenientes da mesma região do Azure que o recurso serviços cognitivas. Use [regras de rede virtual](#grant-access-from-a-virtual-network) para permitir solicitações de mesma região.

Somente os endereços IPV4 têm suporte no momento. Cada recurso de serviços cognitivas dá suporte a até 100 regras de rede IP, que podem ser combinadas com [regras de rede virtual](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Configurando o acesso de redes locais

Para conceder acesso de suas redes locais para o recurso de serviços cognitivas com uma regra de rede IP, você deve identificar os endereços IP voltados para a Internet usados pela sua rede. Contate o administrador de rede para obter ajuda.

Se você estiver usando o [ExpressRoute](../expressroute/expressroute-introduction.md) local para emparelhamento público ou emparelhamento da Microsoft, será necessário identificar os endereços IP de NAT. Para o emparelhamento público, cada circuito do ExpressRoute, por padrão, usa dois endereços IP NAT. Cada um é aplicado ao tráfego de serviço do Azure quando o tráfego entra no backbone de rede Microsoft Azure. Para o emparelhamento da Microsoft, os endereços IP de NAT que são usados são fornecidos pelo cliente ou são fornecidos pelo provedor de serviços. Para permitir o acesso aos recursos de serviço, tem de permitir estes endereços IP públicos na definição da firewall do IP dos recursos. Para localizar os endereços IP do circuito ExpressRoute de peering público, [abra um pedido de suporte no ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) através do portal do Azure. Saiba mais sobre [NAT para peering público e da Microsoft do ExpressRoute.](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Gerenciando regras de rede IP

Você pode gerenciar regras de rede IP para recursos de serviços cognitivas por meio do portal do Azure, do PowerShell ou do CLI do Azure.

# <a name="azure-portaltabportal"></a>[Portal do Azure](#tab/portal)

1. Vá para o recurso de serviços cognitivas que você deseja proteger.

1. Selecione o menu **Gerenciamento de recursos** chamado **rede virtual**.

1. Verifique se você selecionou para permitir o acesso de **redes selecionadas**.

1. Para conceder acesso a um intervalo IP da Internet, insira o endereço IP ou o intervalo de endereços (no [formato CIDR](https://tools.ietf.org/html/rfc4632)) em **Firewall** > **intervalo de endereços**. Somente endereços IP públicos válidos (não reservados) são aceitos.

   ![Adicionar intervalo de IP](media/vnet/virtual-network-add-ip-range.png)

1. Para remover uma regra de rede IP, selecione o <span class="docon docon-delete x-hidden-focus"></span> ícone de Lixeira ao lado do intervalo de endereços.

   ![Excluir intervalo de IP](media/vnet/virtual-network-delete-ip-range.png)

1. Selecione **salvar** para aplicar suas alterações.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

1. Instale o [Azure PowerShell](/powershell/azure/install-az-ps) e [entre](/powershell/azure/authenticate-azureps)ou selecione **experimentar**.

1. Listar regras de rede IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Adicione uma regra de rede para um endereço IP individual.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Adicione uma regra de rede para um intervalo de endereços IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Remova uma regra de rede para um endereço IP individual.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Remova uma regra de rede para um intervalo de endereços IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instale o [CLI do Azure](/cli/azure/install-azure-cli) e [entre](/cli/azure/authenticate-azure-cli)ou selecione **experimentar**.

1. Listar regras de rede IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Adicione uma regra de rede para um endereço IP individual.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Adicione uma regra de rede para um intervalo de endereços IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Remova uma regra de rede para um endereço IP individual.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Remova uma regra de rede para um intervalo de endereços IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> Certifique-se de [definir a regra padrão](#change-the-default-network-access-rule) como **negar**ou se as regras de rede não têm nenhum efeito.

## <a name="next-steps"></a>Passos seguintes

* Explore os vários [Serviços cognitivas do Azure](welcome.md)
* Saiba mais sobre os [pontos de extremidade de serviço de rede virtual do Azure](../virtual-network/virtual-network-service-endpoints-overview.md)