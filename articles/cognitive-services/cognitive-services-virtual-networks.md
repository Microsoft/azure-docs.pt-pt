---
title: Redes Virtuais
titleSuffix: Azure Cognitive Services
description: Configure a segurança da rede em camadas para os seus recursos de Serviços Cognitivos.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: aahi
ms.openlocfilehash: 3b6c2a5a50cedadd8818eae735df55b661e794ef
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97034025"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Configurar redes virtuais dos Serviços Cognitivos do Azure

A Azure Cognitive Services fornece um modelo de segurança em camadas. Este modelo permite-lhe proteger as suas contas dos Serviços Cognitivos para um subconjunto específico de redes. Quando as regras de rede são configuradas, apenas as aplicações que pedem dados sobre o conjunto especificado de redes podem aceder à conta. Pode limitar o acesso aos recursos com a filtragem de pedidos. Permitindo apenas pedidos originários de endereços IP especificados, intervalos IP ou de uma lista de sub-redes em [Redes Virtuais Azure](../virtual-network/virtual-networks-overview.md).

Uma aplicação que acede a um recurso de Serviços Cognitivos quando as regras da rede estão em vigor requer autorização. A autorização é suportada com credenciais [Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) ou com uma chave API válida.

> [!IMPORTANT]
> Ligar as regras de firewall para a sua conta Serviços Cognitivos bloqueia pedidos de dados por padrão. Para permitir a passagem dos pedidos, é necessário reunir uma das seguintes condições:

> * O pedido deve ter origem num serviço que opera dentro de uma Rede Virtual Azure (VNet) na lista de sub-redes permitida da conta de Serviços Cognitivos alvo. O ponto final dos pedidos originados pela VNet tem de ser definido como o [subdomínio personalizado](cognitive-services-custom-subdomains.md) da sua conta de Serviços Cognitivos.
> * Ou o pedido deve ter origem numa lista permitida de endereços IP.
>
> Os pedidos que estão bloqueados incluem os de outros serviços Azure, do portal Azure, de serviços de registo e métricas, e assim por diante.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Cenários

Para garantir o seu recurso de Serviços Cognitivos, deve primeiro configurar uma regra para negar o acesso ao tráfego de todas as redes (incluindo o tráfego de internet) por padrão. Em seguida, deve configurar regras que concedem acesso ao tráfego a partir de VNets específicos. Esta configuração permite-lhe construir um limite de rede seguro para as suas aplicações. Também pode configurar regras para permitir o acesso ao tráfego a partir de intervalos de endereços IP de internet pública selecionados, permitindo ligações a clientes específicos da Internet ou no local.

As regras de rede são aplicadas em todos os protocolos de rede aos Serviços Cognitivos Azure, incluindo REST e WebSocket. Para aceder a dados utilizando ferramentas como as consolas de teste Azure, as regras explícitas de rede devem ser configuradas. Pode aplicar regras de rede aos recursos existentes dos Serviços Cognitivos, ou quando criar novos recursos de Serviços Cognitivos. Uma vez aplicadas as regras da rede, são aplicadas a todos os pedidos.

## <a name="supported-regions-and-service-offerings"></a>Regiões apoiadas e ofertas de serviços

As redes virtuais (VNETs) são suportadas em [regiões onde os Serviços Cognitivos estão disponíveis.](https://azure.microsoft.com/global-infrastructure/services/) Os Serviços Cognitivos suportam tags de serviço para a configuração das regras de rede. Os serviços listados abaixo estão incluídos na etiqueta de serviço **CognitiveServicesManagement.**

> [!div class="checklist"]
> * Detetor de Anomalias
> * Imagem Digitalizada
> * Content Moderator
> * Visão Personalizada
> * Rostos
> * Reconhecedor de Formato
> * Leitura Avançada
> * Compreensão de Idiomas (LUIS)
> * Personalizador
> * Serviços de Voz
> * Análise de Texto
> * Criador de FAQ
> * Tradução de Texto


> [!NOTE]
> Se estiver a utilizar o LUIS, a etiqueta **CognitiveServicesManagement** apenas permite a utilização do serviço utilizando a API SDK ou REST. Para aceder e utilizar o portal LUIS a partir de uma rede virtual, terá de utilizar as seguintes tags:  
> * **AzureActiveDirectory**
> * **AzureFrontDoor.Frontend**
> * **AzureResourceManager** 
> * **CognitiveServicesManagement**



## <a name="change-the-default-network-access-rule"></a>Change the default network access rule (Alterar a regra de acesso de rede predefinida)

Por padrão, os recursos dos Serviços Cognitivos aceitam ligações de clientes em qualquer rede. Para limitar o acesso às redes selecionadas, primeiro tem de alterar a ação predefinida.

> [!WARNING]
> Fazer alterações às regras de rede pode afetar a capacidade das suas aplicações de se ligarem aos Serviços Cognitivos Azure. A definição da regra de rede padrão para **negar** bloqueia todos os acessos aos dados, a menos que sejam também aplicadas regras específicas de rede que **concedem** o acesso. Certifique-se de que concede acesso a quaisquer redes permitidas através das regras de rede antes de alterar a regra predefinida para negar o acesso. Se permitir a listagem de endereços IP para a sua rede no local, certifique-se de adicionar todos os possíveis endereços IP públicos de saída da sua rede de acesso.

### <a name="managing-default-network-access-rules"></a>Gerir regras de acesso de rede predefinidas

Pode gerir as regras de acesso à rede padrão para recursos de Serviços Cognitivos através do portal Azure, PowerShell ou do Azure CLI.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. Vá ao recurso dos Serviços Cognitivos que pretende proteger.

1. Selecione o menu **GESTÃO DE RECURSOS** chamado **rede Virtual.**

   ![Opção de rede virtual](media/vnet/virtual-network-blade.png)

1. Para negar o acesso por padrão, opte por permitir o acesso a partir de **redes Selecionadas.** Com a definição de **redes selecionadas** sozinha, não acompanhada por **redes Virtuais** configuradas ou **intervalos de Endereços** - todo o acesso é efetivamente negado. Quando todo o acesso é negado, os pedidos para tentar consumir o recurso dos Serviços Cognitivos não são permitidos. O portal Azure, Azure PowerShell ou Azure CLI ainda podem ser usados para configurar o recurso Serviços Cognitivos.
1. Para permitir tráfego de todas as redes, opte por permitir o acesso a partir de **Todas as redes**.

   ![Redes virtuais negam](media/vnet/virtual-network-deny.png)

1. Selecione **Guardar** para aplicar as suas alterações.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Instale o [Azure PowerShell](/powershell/azure/install-az-ps) e [inscreva-se em](/powershell/azure/authenticate-azureps), ou selecione **Experimente-o**.

1. Mostrar o estado da regra padrão para o recurso Serviços Cognitivos.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Desacorde a regra padrão para negar o acesso à rede por predefinição.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Desafine a regra padrão para permitir o acesso à rede por predefinição.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instale o [Azure CLI](/cli/azure/install-azure-cli) e [faça o súmis](/cli/azure/authenticate-azure-cli)ou selecione **Experimente-o**.

1. Mostrar o estado da regra padrão para o recurso Serviços Cognitivos.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Desacorde a regra padrão para negar o acesso à rede por predefinição.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Desafine a regra padrão para permitir o acesso à rede por predefinição.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

**_

## <a name="grant-access-from-a-virtual-network"></a>Conceder acesso a partir de uma rede virtual

Pode configurar recursos dos Serviços Cognitivos para permitir o acesso apenas a partir de sub-redes específicas. As sub-redes permitidas podem pertencer a um VNet na mesma subscrição, ou numa subscrição diferente, incluindo subscrições pertencentes a um inquilino azure ative inativo diferente.

Ativar um [ponto final de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) para os Serviços Cognitivos Azure dentro do VNet. O ponto final de serviço encaminha o tráfego do VNet através de um caminho ideal para o serviço Azure Cognitive Services. As identidades da sub-rede e da rede virtual também são transmitidas a cada pedido. Os administradores podem então configurar regras de rede para o recurso Serviços Cognitivos que permitem que os pedidos sejam recebidos a partir de sub-redes específicas numa VNet. Os clientes que tenham acesso através destas regras de rede devem continuar a cumprir os requisitos de autorização do recurso serviços cognitivos para aceder aos dados.

Cada recurso de Serviços Cognitivos suporta até 100 regras de rede virtuais, que podem ser combinadas com [regras de rede IP](#grant-access-from-an-internet-ip-range).

### <a name="required-permissions"></a>Permissões obrigatórias

Para aplicar uma regra de rede virtual a um recurso de Serviços Cognitivos, o utilizador deve ter as permissões adequadas para a adição das sub-redes. A permissão necessária é o papel de _Contributor* ou o papel *de Contribuinte de Serviços Cognitivos.* As permissões necessárias também podem ser adicionadas às definições de funções personalizadas.

O recurso de Serviços Cognitivos e as redes virtuais de acesso podem estar em diferentes subscrições, incluindo subscrições que fazem parte de um inquilino AD Azure diferente.

> [!NOTE]
> A configuração de regras que concedem acesso a sub-redes em redes virtuais que fazem parte de um inquilino diferente do Azure Ative Directory são atualmente apenas suportados através de ApIs Powershell, CLI e REST. Tais regras não podem ser configuradas através do portal Azure, embora possam ser vistas no portal.

### <a name="managing-virtual-network-rules"></a>Gerir regras de rede virtual

Pode gerir as regras de rede virtual para recursos de Serviços Cognitivos através do portal Azure, PowerShell ou do Azure CLI.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. Vá ao recurso dos Serviços Cognitivos que pretende proteger.

1. Selecione o menu **GESTÃO DE RECURSOS** chamado **rede Virtual.**

1. Verifique se selecionou para permitir o acesso a partir de **redes selecionadas.**

1. Para conceder acesso a uma rede virtual com uma regra de rede existente, em **redes Virtuais,** selecione **Adicionar a rede virtual existente**.

   ![Adicionar vNet existente](media/vnet/virtual-network-add-existing.png)

1. Selecione as **opções de redes virtuais** e **sub-redes** e, em seguida, selecione **Ative**.

   ![Adicionar detalhes vNet existentes](media/vnet/virtual-network-add-existing-details.png)

1. Para criar uma nova rede virtual e conceder-lhe acesso, **selecione Adicionar nova rede virtual.**

   ![Adicionar novo vNet](media/vnet/virtual-network-add-new.png)

1. Forneça as informações necessárias para criar a nova rede virtual e, em seguida, selecione **Criar**.

   ![Criar vNet](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Se um ponto final de serviço para a Azure Cognitive Services não foi previamente configurado para a rede virtual selecionada e sub-redes, pode configurá-lo como parte desta operação.
    >
    > Atualmente, apenas as redes virtuais pertencentes ao mesmo inquilino do Azure Ative Directory são mostradas para seleção durante a criação de regras. Para conceder acesso a uma sub-rede numa rede virtual pertencente a outro inquilino, utilize as APIs powershell, CLI ou REST.

1. Para remover uma regra de rede virtual ou sub-rede, selecione... para abrir o menu de contexto para a rede virtual ou sub-rede e selecione **Remover**. 

   ![Remover vNet](media/vnet/virtual-network-remove.png)

1. Selecione **Guardar** para aplicar as suas alterações.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Instale o [Azure PowerShell](/powershell/azure/install-az-ps) e [inscreva-se em](/powershell/azure/authenticate-azureps), ou selecione **Experimente-o**.

1. Listar as regras de rede virtual.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Ativar o ponto final do serviço para os Serviços Cognitivos Azure numa rede virtual e sub-rede existentes.

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
    > Para adicionar uma regra de rede para uma sub-rede num VNet pertencente a outro inquilino AD Azure, utilize um parâmetro **VirtualNetworkResourceId** totalmente qualificado no formulário "/subscrições/subscrições/subscrição-ID/resourceGroups/resourceGroup/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instale o [Azure CLI](/cli/azure/install-azure-cli) e [faça o súmis](/cli/azure/authenticate-azure-cli)ou selecione **Experimente-o**.

1. Listar as regras de rede virtual.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Ativar o ponto final do serviço para os Serviços Cognitivos Azure numa rede virtual e sub-rede existentes.

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
    > Para adicionar uma regra para uma sub-rede num VNet pertencente a outro inquilino AD Azure, utilize um ID de sub-rede totalmente qualificado no formulário "/subscrições/subscrições/subscrição-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNet-name/subnets/subnets/subnet-name".
    > 
    > Pode utilizar o parâmetro **de subscrição** para recuperar o ID da sub-rede para um VNet pertencente a outro inquilino AD AZure.

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

**_

> [!IMPORTANT]
> Certifique-se de [definir a regra padrão](#change-the-default-network-access-rule) para _*deny**, ou as regras de rede não têm efeito.

## <a name="grant-access-from-an-internet-ip-range"></a>Conceder acesso a partir de um intervalo de IP da Internet

Pode configurar recursos dos Serviços Cognitivos para permitir o acesso a partir de intervalos específicos de endereços IP da internet pública. Esta configuração permite o acesso a serviços específicos e redes no local, bloqueando efetivamente o tráfego geral de internet.

Forneça intervalos de endereços de internet permitidos utilizando [a notação CIDR](https://tools.ietf.org/html/rfc4632) no formulário `16.17.18.0/24` ou como endereços IP individuais como `16.17.18.19` .

   > [!Tip]
   > Não são suportadas pequenas gamas de endereços que utilizem tamanhos de prefixo "/31" ou "/32". Estas gamas devem ser configuradas utilizando regras individuais de endereço IP.

As regras da rede IP só são permitidas para endereços IP **de internet pública.** Os intervalos de endereços IP reservados para redes privadas (tal como definido no [RFC 1918)](https://tools.ietf.org/html/rfc1918#section-3)não são permitidos nas regras de IP. As redes privadas incluem endereços que começam com `10.*` `172.16.*`  -  `172.31.*` , e `192.168.*` .

   > [!NOTE]
   > As regras da rede IP não têm qualquer efeito sobre os pedidos originários da mesma região do Azure que o recurso serviços cognitivos. Utilize [regras de rede virtuais](#grant-access-from-a-virtual-network) para permitir pedidos da mesma região.

Neste momento, apenas os endereços IPV4 são suportados. Cada recurso de Serviços Cognitivos suporta até 100 regras de rede IP, que podem ser combinadas com [regras de rede Virtual](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Configurar o acesso a partir de redes no local

Para conceder acesso das suas redes no local ao seu recurso Serviços Cognitivos com uma regra de rede IP, tem de identificar os endereços IP virados para a Internet utilizados pela sua rede. Contacte o administrador da rede para obter ajuda.

Se estiver a utilizar [o ExpressRoute](../expressroute/expressroute-introduction.md) no local para espreitar publicamente ou para o microsoft espreitar, terá de identificar os endereços IP do NAT. Para espreitar o público, cada circuito ExpressRoute por predefinição utiliza dois endereços IP NAT. Cada um é aplicado ao tráfego de serviço Azure quando o tráfego entra na espinha dorsal da rede Microsoft Azure. Para o estojo da Microsoft, os endereços NAT IP utilizados são fornecidos pelo cliente ou são fornecidos pelo prestador de serviços. Para permitir o acesso aos recursos de serviço, tem de permitir estes endereços IP públicos na definição da firewall do IP dos recursos. Para localizar os endereços IP do circuito ExpressRoute de peering público, [abra um pedido de suporte no ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) através do portal do Azure. Saiba mais sobre [NAT para peering público e da Microsoft do ExpressRoute.](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Gestão das regras de rede IP

Pode gerir as regras de rede IP para recursos de Serviços Cognitivos através do portal Azure, PowerShell ou do Azure CLI.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. Vá ao recurso dos Serviços Cognitivos que pretende proteger.

1. Selecione o menu **GESTÃO DE RECURSOS** chamado **rede Virtual.**

1. Verifique se selecionou para permitir o acesso a partir de **redes selecionadas.**

1. Para conceder acesso a uma gama IP da Internet, insira o endereço IP ou o intervalo de endereços (em [formato CIDR](https://tools.ietf.org/html/rfc4632)) no Campo de **Endereços de Firewall**  >  . Apenas são aceites endereços IP públicos válidos (não reservados).

   ![Adicionar gama IP](media/vnet/virtual-network-add-ip-range.png)

1. Para remover uma regra de rede IP, selecione o ícone do caixote do lixo <span class="docon docon-delete x-hidden-focus"></span> ao lado do intervalo de endereços.

   ![Eliminar a gama IP](media/vnet/virtual-network-delete-ip-range.png)

1. Selecione **Guardar** para aplicar as suas alterações.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Instale o [Azure PowerShell](/powershell/azure/install-az-ps) e [inscreva-se em](/powershell/azure/authenticate-azureps), ou selecione **Experimente-o**.

1. Listar as regras da rede IP.

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instale o [Azure CLI](/cli/azure/install-azure-cli) e [faça o súmis](/cli/azure/authenticate-azure-cli)ou selecione **Experimente-o**.

1. Listar as regras da rede IP.

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

**_

> [!IMPORTANT]
> Certifique-se de [definir a regra padrão](#change-the-default-network-access-rule) para _*deny**, ou as regras de rede não têm efeito.

## <a name="use-private-endpoints"></a>Utilizar pontos finais privados

Pode utilizar [pontos finais privados](../private-link/private-endpoint-overview.md) para os seus recursos de Serviços Cognitivos para permitir que os clientes de uma rede virtual (VNet) acedam de forma segura a dados por uma [Ligação Privada.](../private-link/private-link-overview.md) O ponto final privado utiliza um endereço IP a partir do espaço de endereço VNet para o seu recurso De Serviços Cognitivos. O tráfego de rede entre os clientes no VNet e o recurso atravessa o VNet e uma ligação privada na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública.

Os pontos finais privados para os recursos dos Serviços Cognitivos permitem-lhe:

* Proteja o seu recurso de Serviços Cognitivos configurando a firewall para bloquear todas as ligações no ponto final público para o serviço de Serviços Cognitivos.
* Aumentar a segurança para o VNet, permitindo-lhe bloquear a exfiltração de dados a partir do VNet.
* Conectar-se seguramente aos recursos dos Serviços Cognitivos a partir de redes no local que se conectam ao VNet [usando VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoutes](../expressroute/expressroute-locations.md) com o seu olhar privado.

### <a name="conceptual-overview"></a>Descrição geral conceptual

Um ponto final privado é uma interface de rede especial para um recurso Azure no seu [VNet](../virtual-network/virtual-networks-overview.md). A criação de um ponto final privado para o seu recurso Serviços Cognitivos proporciona uma conectividade segura entre os clientes no seu VNet e o seu recurso. O ponto final privado é atribuído um endereço IP a partir do intervalo de endereço IP do seu VNet. A ligação entre o ponto final privado e o serviço de Serviços Cognitivos utiliza uma ligação privada segura.

As aplicações no VNet podem ligar-se ao serviço através do ponto final privado de forma perfeita, utilizando as mesmas cordas de ligação e mecanismos de autorização que utilizariam de outra forma. A exceção são os Serviços de Fala, que requerem um ponto final separado. Consulte a secção sobre [pontos finais privados com os Serviços de Fala](#private-endpoints-with-the-speech-services). Os pontos finais privados podem ser utilizados com todos os protocolos suportados pelo recurso Serviços Cognitivos, incluindo REST.

Os pontos finais privados podem ser criados em sub-redes que utilizam [pontos finais de serviço](../virtual-network/virtual-network-service-endpoints-overview.md). Os clientes numa sub-rede podem ligar-se a um recurso de Serviços Cognitivos utilizando o ponto final privado, enquanto utilizam pontos finais de serviço para aceder a outros.

Quando cria um ponto final privado para um recurso de Serviços Cognitivos no seu VNet, é enviado um pedido de consentimento para aprovação ao proprietário de recursos dos Serviços Cognitivos. Se o utilizador que solicita a criação do ponto final privado for também proprietário do recurso, este pedido de consentimento é automaticamente aprovado.

Os proprietários de recursos dos Serviços Cognitivos podem gerir os pedidos de consentimento e os pontos finais privados, através do separador '*Pontos finais Privados'* para o recurso serviços cognitivos no [portal Azure.](https://portal.azure.com)

### <a name="private-endpoints"></a>Pontos finais privados

Ao criar o ponto final privado, deve especificar o recurso serviços cognitivos a que se conecta. Para obter mais informações sobre a criação de um ponto final privado, consulte:

* [Criar um ponto final privado utilizando o Private Link Center no portal Azure](../private-link/create-private-endpoint-portal.md)
* [Criar um ponto final privado usando O Azure CLI](../private-link/create-private-endpoint-cli.md)
* [Criar um ponto final privado usando a Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Ligação a pontos finais privados

Os clientes de um VNet que utilizem o ponto final privado devem utilizar a mesma cadeia de ligação para o recurso Serviços Cognitivos que os clientes que se ligam ao ponto final público. A exceção são os Serviços de Fala, que requerem um ponto final separado. Consulte a secção sobre [pontos finais privados com os Serviços de Fala](#private-endpoints-with-the-speech-services). Contamos com a resolução do DNS para encaminhar automaticamente as ligações do VNet para o recurso Serviços Cognitivos por uma ligação privada. 

Criamos uma [zona de DNS privada](../dns/private-dns-overview.md) anexada ao VNet com as atualizações necessárias para os pontos finais privados, por padrão. No entanto, se estiver a utilizar o seu próprio servidor DNS, poderá ter de es fazer alterações adicionais na sua configuração DNS. A secção sobre [as alterações do DNS](#dns-changes-for-private-endpoints) abaixo descreve as atualizações necessárias para os pontos finais privados.

### <a name="private-endpoints-with-the-speech-services"></a>Pontos finais privados com os Serviços de Fala

Consulte [a utilização de serviços de fala com pontos finais privados fornecidos pela Azure Private Link](Speech-Service/speech-services-private-link.md).

### <a name="dns-changes-for-private-endpoints"></a>DNS alterações para pontos finais privados

Quando cria um ponto final privado, o registo de recursos DNS CNAME para o recurso Serviços Cognitivos é atualizado para um pseudónimo num subdomínio com o prefixo '*privatelink*'. Por padrão, também criamos uma [zona privada de DNS,](../dns/private-dns-overview.md)correspondente ao subdomínio '*privatelink*', com os registos de recursos DNS A para os pontos finais privados.

Quando resolve o URL de ponto final de fora do VNet com o ponto final privado, resolve-se para o ponto final público do recurso Serviços Cognitivos. Quando resolvido a partir do VNet que hospeda o ponto final privado, o URL do ponto final resolve-se para o endereço IP do ponto final privado.

Esta abordagem permite o acesso ao recurso Serviços Cognitivos utilizando a mesma cadeia de ligação para clientes no VNet que hospeda os pontos finais privados e clientes fora do VNet.

Se estiver a utilizar um servidor DNS personalizado na sua rede, os clientes devem ser capazes de resolver o nome de domínio totalmente qualificado (FQDN) para o ponto final de recursos dos Serviços Cognitivos para o endereço IP do ponto final privado. Configure o seu servidor DNS para delegar o seu subdomínio de ligação privada para a zona privada de DNS para o VNet.

> [!TIP]
> Ao utilizar um servidor DNS personalizado ou no local, deverá configurar o seu servidor DNS para resolver o nome do recurso Serviços Cognitivos no subdomínio 'privatelink' para o endereço IP do ponto final privado. Pode fazê-lo delegando o subdomínio 'privatelink' para a zona privada de DNS do VNet, ou configurando a zona DNS no seu servidor DNS e adicionando os registos DNS A.

Para obter mais informações sobre a configuração do seu próprio servidor DNS para suportar pontos finais privados, consulte os seguintes artigos:

* [Name resolution for resources in Azure virtual networks](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) (Resolução de nomes para recursos em redes virtuais do Azure)
* [Configuração DE DNS para pontos finais privados](../private-link/private-endpoint-overview.md#dns-configuration)

### <a name="pricing"></a>Preços

Para obter detalhes sobre os preços, consulte [os preços do Azure Private Link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Passos seguintes

* Explore os vários [Serviços Cognitivos Azure](./what-are-cognitive-services.md)
* Saiba mais sobre [os pontos finais do Serviço de Rede Virtual Azure](../virtual-network/virtual-network-service-endpoints-overview.md)