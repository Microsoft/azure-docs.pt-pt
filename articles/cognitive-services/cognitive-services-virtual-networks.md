---
title: Redes Virtuais
titleSuffix: Azure Cognitive Services
description: Configure a segurança da rede em camadas para os seus recursos de Serviços Cognitivos.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 0988c8154c63bb408493edf3243078e625c80d53
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79371227"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Configure Redes virtuais dos Serviços Cognitivos Azure

A Azure Cognitive Services fornece um modelo de segurança em camadas. Este modelo permite-lhe assegurar as suas contas de Serviços Cognitivos a um subconjunto específico de redes. Quando as regras de rede são configuradas, apenas as aplicações que solicitam dados sobre o conjunto especificado de redes podem aceder à conta. Pode limitar o acesso aos seus recursos com filtragem de pedido. Permitindo apenas pedidos originários de endereços IP especificados, intervalos IP ou de uma lista de subredes em [Redes Virtuais Azure](../virtual-network/virtual-networks-overview.md). Se estiver interessado nesta oferta, terá de solicitar acesso de [pré-visualização.](https://aka.ms/cog-svc-vnet-signup)

Uma aplicação que acede a um recurso dos Serviços Cognitivos quando as regras da rede estão em vigor requer autorização. A autorização é suportada com credenciais [azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) ou com uma chave API válida.

> [!IMPORTANT]
> Ligar as regras de firewall para a sua conta De Serviços Cognitivos bloqueia pedidos de dados por padrão. Para permitir a passagem de pedidos, é necessário cumprir uma das seguintes condições:
> * O pedido deve ter origem num serviço que opera dentro de uma Rede Virtual Azure (VNet) na lista de sub-rede permitida da conta de Serviços Cognitivos alvo. O ponto final nos pedidos originados da VNet tem de ser definido como o [subdomínio personalizado](cognitive-services-custom-subdomains.md) da sua conta de Serviços Cognitivos.
> * Ou o pedido deve ter origem numa lista permitida de endereços IP.
>
> Os pedidos que estão bloqueados incluem os de outros serviços Azure, do portal Azure, dos serviços de exploração madeireira e métrica, e assim por diante.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Cenários

Para garantir o seu recurso de Serviços Cognitivos, deve primeiro configurar uma regra para negar o acesso ao tráfego de todas as redes (incluindo o tráfego de internet) por padrão. Em seguida, deve configurar regras que concedam acesso ao tráfego a partir de VNets específicos. Esta configuração permite-lhe construir um limite de rede seguro para as suas aplicações. Também pode configurar regras para conceder acesso ao tráfego a partir de gamas de endereços IP de internet pública selecionadas, permitindo ligações de clientes específicos da Internet ou no local.

As regras de rede são aplicadas em todos os protocolos de rede aos Serviços Cognitivos Azure, incluindo REST e WebSocket. Para aceder a dados utilizando ferramentas como as consolas de teste Azure, devem ser configuradas regras explícitas de rede. Pode aplicar regras de rede aos recursos dos Serviços Cognitivos existentes, ou quando cria novos recursos dos Serviços Cognitivos. Uma vez aplicadas as regras da rede, são aplicadas para todos os pedidos.

## <a name="supported-regions-and-service-offerings"></a>Regiões apoiadas e ofertas de serviços

O apoio à rede virtual para serviços cognitivos listados abaixo está limitado às regiões *central dos EUA EUA,* *Centro-Sul*dos *EUA*, Oeste dos *EUA 2*, Norte da *Europa*, África do *Sul Norte,* Europa *Ocidental,* *Índia Central,* *Austrália Leste,* *Oeste dos EUA,* e *regiões norte-americanas Gov Virginia* Azure. Se a oferta de serviços não estiver listada aqui, não suporta redes virtuais.

> [!div class="checklist"]
> * [Detetor de Anomalias](./anomaly-detector/index.yml)
> * [Imagem Digitalizada](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Visão Personalizada](./custom-vision-service/index.yml)
> * [Rostos](./face/index.yml)
> * [Reconhecedor de Formato](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personalizador](./personalizer/index.yml)
> * [Análise de Texto](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)

O suporte de rede virtual para serviços cognitivos listados abaixo está limitado às regiões *central dos EUA EUA,* *Centro-Sul dos EUA*, Leste dos *EUA,* *Oeste DOS 2*, *Global*e NORTE *Gov Virginia* Azure.
> [!div class="checklist"]
> * [Texto do Tradutor](./translator/index.yml)

## <a name="service-tags"></a>Etiquetas de Serviço
Além de apoiar pontos finais de serviço de rede virtual para os serviços acima, a Cognitive Services também suporta uma etiqueta de serviço para configuração de regras de rede de saída. Os seguintes serviços estão incluídos na etiqueta de serviço CognitiveServicesManagement.
> [!div class="checklist"]
> * [Detetor de Anomalias](./anomaly-detector/index.yml)
> * [Imagem Digitalizada](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Visão Personalizada](./custom-vision-service/index.yml)
> * [Rostos](./face/index.yml)
> * [Reconhecedor de Formato](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personalizador](./personalizer/index.yml)
> * [Análise de Texto](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)
> * [Texto do Tradutor](./translator/index.yml)
> * [Serviço de Voz](./speech-service/index.yml)

## <a name="change-the-default-network-access-rule"></a>Change the default network access rule (Alterar a regra de acesso de rede predefinida)

Por padrão, os recursos dos Serviços Cognitivos aceitam ligações de clientes em qualquer rede. Para limitar o acesso às redes selecionadas, primeiro tem de alterar a ação predefinida.

> [!WARNING]
> Fazer alterações às regras da rede pode afetar a capacidade das suas aplicações de se conectarem aos Serviços Cognitivos Azure. Definir a regra da rede predefinida para **negar** os bloqueios a todos os acessos aos dados, a menos que sejam também aplicadas regras específicas de rede que **concedam** acesso. Certifique-se de que concede acesso a quaisquer redes permitidas através das regras de rede antes de alterar a regra predefinida para negar o acesso. Se estiver a permitir a listagem de endereços IP para a sua rede no local, certifique-se de adicionar todos os possíveis endereços IP públicos de saída da sua rede no local.

### <a name="managing-default-network-access-rules"></a>Gerir regras de acesso de rede predefinidas

Pode gerir as regras de acesso à rede padrão para os recursos dos Serviços Cognitivos através do portal Azure, PowerShell ou do Azure CLI.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. Vá ao recurso dos Serviços Cognitivos que queira assegurar.

1. Selecione o menu de **GESTÃO DE RECURSOS** chamado **rede Virtual**.

   ![Opção de rede virtual](media/vnet/virtual-network-blade.png)

1. Para negar o acesso por padrão, opte por permitir o acesso a partir de **redes Selecionadas**. Com a definição das **redes Selecionadas** sozinha, não acompanhada por **redes virtuais** configuradas ou intervalos de **Endereços** configurados - todo o acesso é efetivamente negado. Quando todo o acesso é negado, não são permitidos pedidos que tentem consumir o recurso dos Serviços Cognitivos. O portal Azure, Azure PowerShell ou, Azure CLI ainda pode ser usado para configurar o recurso dos Serviços Cognitivos.
1. Para permitir tráfego de todas as redes, opte por permitir o acesso a partir de **Todas as redes**.

   ![Redes virtuais negam](media/vnet/virtual-network-deny.png)

1. Selecione **Guardar** para aplicar as suas alterações.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Instale o [PowerShell Azure](/powershell/azure/install-az-ps) e [inscreva-se,](/powershell/azure/authenticate-azureps)ou selecione **Experimente-**

1. Mostrar o estado da regra padrão para o recurso dos Serviços Cognitivos.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Desdefinir a regra predefinida para negar o acesso à rede por defeito.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Desdefinir a regra predefinida para permitir o acesso à rede por defeito.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instale o [Azure CLI](/cli/azure/install-azure-cli) e [inscreva-se,](/cli/azure/authenticate-azure-cli)ou selecione **Experimente**.

1. Mostrar o estado da regra padrão para o recurso dos Serviços Cognitivos.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Desdefinir a regra predefinida para negar o acesso à rede por defeito.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Desdefinir a regra predefinida para permitir o acesso à rede por defeito.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>Conceder acesso a partir de uma rede virtual

Pode configurar os recursos dos Serviços Cognitivos para permitir o acesso apenas a partir de subredes específicas. As subredes permitidas podem pertencer a um VNet na mesma subscrição, ou numa subscrição diferente, incluindo subscrições pertencentes a um inquilino do Diretório Ativo Azure diferente.

Ative um [ponto final](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço para serviços cognitivos Azure dentro do VNet. O ponto final de serviço percorre o tráfego a partir do VNet através de um caminho ideal para o serviço de Serviços Cognitivos Azure. As identidades da subnet e da rede virtual também são transmitidas a cada pedido. Os administradores podem então configurar as regras de rede para o recurso Dos Serviços Cognitivos que permitem que os pedidos sejam recebidos de subredes específicas num VNet. Os clientes que tenham acesso através destas regras de rede devem continuar a cumprir os requisitos de autorização do recurso dos Serviços Cognitivos para aceder aos dados.

Cada recurso dos Serviços Cognitivos suporta até 100 regras de rede virtual, que podem ser combinadas com as regras da [rede IP.](#grant-access-from-an-internet-ip-range)

### <a name="required-permissions"></a>Permissões obrigatórias

Para aplicar uma regra de rede virtual a um recurso dos Serviços Cognitivos, o utilizador deve ter as permissões adequadas para que as subredes sejam adicionadas. A permissão necessária é a função de *Contribuinte* padrão, ou o papel de Colaborador de *Serviços Cognitivos.* As permissões necessárias também podem ser adicionadas às definições de papéis personalizados.

O recurso dos Serviços Cognitivos e as redes virtuais que tiveram acesso podem estar em diferentes subscrições, incluindo subscrições que fazem parte de um inquilino azure diferente.

> [!NOTE]
> A configuração de regras que concedem acesso a subredes em redes virtuais que fazem parte de um diferente inquilino do Diretório Ativo Azure são atualmente apenas suportadas através de APIs Powershell, CLI e REST. Tais regras não podem ser configuradas através do portal Azure, embora possam ser vistas no portal.

### <a name="managing-virtual-network-rules"></a>Gestão de regras de rede virtual

Pode gerir as regras de rede virtual para os recursos dos Serviços Cognitivos através do portal Azure, PowerShell ou do Azure CLI.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. Vá ao recurso dos Serviços Cognitivos que queira assegurar.

1. Selecione o menu de **GESTÃO DE RECURSOS** chamado **rede Virtual**.

1. Verifique se selecionou para permitir o acesso a partir de **redes Selecionadas**.

1. Para garantir o acesso a uma rede virtual com uma regra de rede existente, sob **redes Virtuais,** selecione **Adicionar rede virtual existente**.

   ![Adicionar vNet existente](media/vnet/virtual-network-add-existing.png)

1. Selecione as opções de **redes virtuais** e **subnets** e, em seguida, selecione **Ativar**.

   ![Adicione os detalhes vNet existentes](media/vnet/virtual-network-add-existing-details.png)

1. Para criar uma nova rede virtual e conceder-lhe acesso, selecione **Adicionar nova rede virtual**.

   ![Adicione novo vNet](media/vnet/virtual-network-add-new.png)

1. Forneça as informações necessárias para criar a nova rede virtual e, em seguida, selecione **Criar**.

   ![Criar vNet](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Se um ponto final de serviço para os Serviços Cognitivos Azure não estivesse previamente configurado para a rede virtual selecionada e subnets, pode configurá-lo como parte desta operação.
    >
    > Atualmente, apenas redes virtuais pertencentes ao mesmo inquilino azure Ative Directory são mostradas para seleção durante a criação de regras. Para conceder acesso a uma subneta numa rede virtual pertencente a outro inquilino, utilize apis Powershell, CLI ou REST.

1. Para remover uma regra de rede virtual ou sub-rede, selecione... para abrir o menu de contexto para a rede virtual ou subnet, e selecione **Remover**. **...**

   ![Remover vNet](media/vnet/virtual-network-remove.png)

1. Selecione **Guardar** para aplicar as suas alterações.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Instale o [PowerShell Azure](/powershell/azure/install-az-ps) e [inscreva-se,](/powershell/azure/authenticate-azureps)ou selecione **Experimente-**

1. Listar as regras da rede virtual.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Ativar o ponto final de serviço para os Serviços Cognitivos Azure numa rede virtual existente e subnet.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Adicione uma regra de rede para uma rede virtual e subnet.

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
    > Para adicionar uma regra de rede para uma subnet a uma VNet pertencente a outro inquilino DaD Azure, utilize um parâmetro **VirtualNetworkResourceId** totalmente qualificado no formulário "/subscrições/subscrições/subscrição-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".

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

1. Instale o [Azure CLI](/cli/azure/install-azure-cli) e [inscreva-se,](/cli/azure/authenticate-azure-cli)ou selecione **Experimente**.

1. Listar as regras da rede virtual.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Ativar o ponto final de serviço para os Serviços Cognitivos Azure numa rede virtual existente e subnet.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Adicione uma regra de rede para uma rede virtual e subnet.

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
    > Para adicionar uma regra para uma sub-rede num VNet pertencente a outro inquilino DaD Azure, utilize um ID de sub-rede totalmente qualificado no formulário "/subscrições/subscrição-ID/recursosGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".
    > 
    > Você pode usar o parâmetro de **subscrição** para recuperar o ID da subnet para um VNet pertencente a outro inquilino Azure AD.

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
> Certifique-se de [que estabelece a regra padrão](#change-the-default-network-access-rule) para **negar**, ou as regras da rede não têm qualquer efeito.

## <a name="grant-access-from-an-internet-ip-range"></a>Conceder acesso a partir de uma gama IP da Internet

Pode configurar os recursos dos Serviços Cognitivos para permitir o acesso a partir de gamas específicas de endereços IP da Internet pública. Esta configuração permite o acesso a serviços específicos e redes no local, bloqueando efetivamente o tráfego geral da Internet.

Fornecer gamas de endereços de internet `16.17.18.0/24` permitidas utilizando [notação CIDR](https://tools.ietf.org/html/rfc4632) no formulário ou como endereços IP individuais como `16.17.18.19`.

   > [!Tip]
   > Não são suportadas gamas de endereços pequenas utilizando tamanhos de prefixo "/31" ou "/32". Estas gamas devem ser configuradas utilizando regras individuais de endereço IP.

As regras da rede IP só são permitidas para endereços IP da **internet pública.** As gamas de endereços IP reservadas para redes privadas (tal como definidas no [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) não são permitidas nas regras ip. As redes privadas incluem `172.16.*`  -  `172.31.*`endereços que começam com, `192.168.*` `10.*`e .

   > [!NOTE]
   > As regras da rede IP não têm qualquer efeito sobre os pedidos originários da mesma região do Azure que o recurso dos Serviços Cognitivos. Utilize regras de [rede virtuais](#grant-access-from-a-virtual-network) para permitir pedidos na mesma região.

Apenas os endereços IPV4 são suportados neste momento. Cada recurso dos Serviços Cognitivos suporta até 100 regras de rede IP, que podem ser combinadas com regras de [rede virtuais.](#grant-access-from-a-virtual-network)

### <a name="configuring-access-from-on-premises-networks"></a>Configurar o acesso a partir de redes no local

Para garantir o acesso das suas redes no local ao seu recurso de Serviços Cognitivos com uma regra de rede IP, deve identificar os endereços IP virados para a Internet utilizados pela sua rede. Contacte o seu administrador de rede para obter ajuda.

Se estiver a utilizar o [ExpressRoute](../expressroute/expressroute-introduction.md) no local para espreitar público ou espreitar a Microsoft, terá de identificar os endereços IP na NAT. Para o público, cada circuito ExpressRoute por padrão utiliza dois endereços IP NAT. Cada um é aplicado ao tráfego de serviços Azure quando o tráfego entra na espinha dorsal da rede Microsoft Azure. Para o peering da Microsoft, os endereços IP NAT que são utilizados são fornecidos pelo cliente ou são fornecidos pelo prestador de serviços. Para permitir o acesso aos recursos de serviço, tem de permitir estes endereços IP públicos na definição da firewall do IP dos recursos. Para localizar os endereços IP do circuito ExpressRoute de peering público, [abra um pedido de suporte no ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) através do portal do Azure. Saiba mais sobre [NAT para peering público e da Microsoft do ExpressRoute.](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Gestão das regras da rede IP

Pode gerir as regras da rede IP para recursos de Serviços Cognitivos através do portal Azure, PowerShell ou do Azure CLI.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. Vá ao recurso dos Serviços Cognitivos que queira assegurar.

1. Selecione o menu de **GESTÃO DE RECURSOS** chamado **rede Virtual**.

1. Verifique se selecionou para permitir o acesso a partir de **redes Selecionadas**.

1. Para garantir o acesso a uma gama IP da Internet, introduza o endereço IP ou a gama de endereços (em [formato CIDR)](https://tools.ietf.org/html/rfc4632)no âmbito do Alcance do**Endereço** **firewall** > . São aceites apenas endereços públicos válidos (não reservados).

   ![Adicionar gama IP](media/vnet/virtual-network-add-ip-range.png)

1. Para remover uma regra de rede <span class="docon docon-delete x-hidden-focus"></span> IP, selecione o ícone do caixote do lixo ao lado do intervalo de endereços.

   ![Eliminar gama IP](media/vnet/virtual-network-delete-ip-range.png)

1. Selecione **Guardar** para aplicar as suas alterações.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Instale o [PowerShell Azure](/powershell/azure/install-az-ps) e [inscreva-se,](/powershell/azure/authenticate-azureps)ou selecione **Experimente-**

1. Lista rita as regras da rede IP.

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

1. Adicione uma regra de rede para um intervalo de endereçoIP.

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

1. Remova uma regra de rede para um intervalo de endereçoIP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Instale o [Azure CLI](/cli/azure/install-azure-cli) e [inscreva-se,](/cli/azure/authenticate-azure-cli)ou selecione **Experimente**.

1. Lista rita as regras da rede IP.

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

1. Adicione uma regra de rede para um intervalo de endereçoIP.

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

1. Remova uma regra de rede para um intervalo de endereçoIP.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> Certifique-se de [que estabelece a regra padrão](#change-the-default-network-access-rule) para **negar**, ou as regras da rede não têm qualquer efeito.

## <a name="next-steps"></a>Passos seguintes

* Explore os vários [Serviços Cognitivos Azure](welcome.md)
* Saiba mais sobre [os pontos finais do serviço](../virtual-network/virtual-network-service-endpoints-overview.md) de rede virtual Azure