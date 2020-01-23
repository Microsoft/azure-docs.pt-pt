---
title: Configurar o armazenamento do Azure firewalls e redes virtuais | Documentos da Microsoft
description: Configure a segurança de rede em camadas para a sua conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 2c3b329aa767fbe9795c90ca236008210576fe12
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514736"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Configurar o armazenamento do Azure firewalls e redes virtuais

O armazenamento do Azure fornece um modelo de segurança em camadas. Esse modelo permite que você proteja e controle o nível de acesso às suas contas de armazenamento que seus aplicativos e ambientes empresariais exigem, com base no tipo e no subconjunto de redes usadas. Quando as regras de rede são configuradas, somente os aplicativos que solicitam dados no conjunto especificado de redes podem acessar uma conta de armazenamento. Você pode limitar o acesso à sua conta de armazenamento a solicitações originadas de endereços IP especificados, intervalos de IP ou de uma lista de sub-redes em uma VNet (rede virtual) do Azure.

As contas de armazenamento têm um ponto de extremidade público que pode ser acessado pela Internet. Você também pode criar [pontos de extremidade privados para sua conta de armazenamento](storage-private-endpoints.md), que atribui um endereço IP privado de sua vnet à conta de armazenamento e protege todo o tráfego entre sua vnet e a conta de armazenamento por um link privado. O Firewall do armazenamento do Azure fornece acesso de controle de acesso para o ponto de extremidade público da sua conta de armazenamento. Você também pode usar o firewall para bloquear todo o acesso por meio do ponto de extremidade público ao usar pontos de extremidades privados. A configuração do firewall de armazenamento também permite selecionar serviços confiáveis da plataforma Azure para acessar a conta de armazenamento com segurança.

Um aplicativo que acessa uma conta de armazenamento quando as regras de rede estão em vigor ainda requer autorização adequada para a solicitação. Há suporte para autorização com as credenciais do Azure Active Directory (Azure AD) para BLOBs e filas, com uma chave de acesso de conta válida ou com um token SAS.

> [!IMPORTANT]
> A ativação de regras de firewall para sua conta de armazenamento bloqueia solicitações de entrada de dados por padrão, a menos que as solicitações sejam originadas de um serviço operando em uma VNet (rede virtual) do Azure ou de endereços IP públicos permitidos. Pedidos que estão bloqueados incluem os de outros serviços do Azure, do portal do Azure, do Registro em log e serviços de métricas e assim por diante.
>
> Você pode conceder acesso aos serviços do Azure que operam de dentro de uma VNet, permitindo o tráfego da sub-rede que hospeda a instância do serviço. Você também pode habilitar um número limitado de cenários por meio do mecanismo de [exceções](#exceptions) descrito abaixo. Para acessar dados da conta de armazenamento por meio do portal do Azure, você precisa estar em um computador dentro do limite confiável (IP ou VNet) que você configurou.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Cenários

Para proteger sua conta de armazenamento, primeiro você deve configurar uma regra para negar o acesso ao tráfego de todas as redes (incluindo o tráfego da Internet) no ponto de extremidade público, por padrão. Em seguida, você deve configurar regras que concedem acesso ao tráfego de VNets específicas. Você também pode configurar regras para conceder acesso ao tráfego de selecionar intervalos de endereços IP públicos da Internet, permitindo conexões de clientes locais ou da Internet específicos. Esta configuração permite-lhe criar um limite de rede segura para as suas aplicações.

Você pode combinar as regras de firewall que permitem o acesso de redes virtuais específicas e de intervalos de endereços IP públicos na mesma conta de armazenamento. As regras de firewall de armazenamento podem ser aplicadas a contas de armazenamento existentes ou ao criar novas contas de armazenamento.

As regras de firewall de armazenamento se aplicam ao ponto de extremidade público de uma conta de armazenamento. Você não precisa de nenhuma regra de acesso de firewall para permitir o tráfego para pontos de extremidade privados de uma conta de armazenamento. O processo de aprovar a criação de um ponto de extremidade privado concede acesso implícito ao tráfego da sub-rede que hospeda o ponto de extremidade privado.

Regras de rede são impostas em todos os protocolos de rede ao armazenamento do Azure, incluindo o SMB e REST. Para acessar dados usando ferramentas como o portal do Azure, Gerenciador de Armazenamento e AZCopy, as regras de rede explícitas devem ser configuradas.

Assim que as regras de rede são aplicadas, elas são impostas para todos os pedidos. Tokens SAS que concedem acesso a um endereço IP específico servem para limitar o acesso do token portador, mas não a conceder acesso de novo para além de regras de rede configuradas.

Tráfego de disco de máquina virtual (incluindo montar e desmontar as operações de e/s de disco) não é afetado pelas regras de rede. Acesso REST aos blobs de página está protegido por regras de rede.

Contas de armazenamento clássicas não suportam firewalls e redes virtuais.

Pode utilizar discos não geridos nas contas de armazenamento com regras de rede aplicadas às VMs de cópia de segurança e restauro através da criação de uma exceção. Esse processo está documentado no [exceções](#exceptions) seção deste artigo. Exceções de firewall não são aplicáveis com discos geridos à medida que já sejam geridos pelo Azure.

## <a name="change-the-default-network-access-rule"></a>Alterar a regra de acesso de rede predefinida

Por predefinição, as contas de armazenamento aceitam ligações de clientes em qualquer rede. Para limitar o acesso a redes selecionadas, primeiro tem de alterar a ação predefinida.

> [!WARNING]
> Fazer alterações às regras de rede pode afetar a capacidade de seus aplicativos para se ligar ao armazenamento do Azure. A definição da regra de rede padrão para **negar** bloqueia todo o acesso aos dados, a menos que regras de rede específicas que **concedem** acesso também sejam aplicadas. Certifique-se de que conceder acesso a quaisquer redes permitidas utilizar regras de rede antes de alterar a regra predefinida para negar o acesso.

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

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Defina a regra predefinida para negar o acesso à rede por predefinição.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Defina a regra predefinida para permitir o acesso à rede por predefinição.

    ```powershell
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

Você pode configurar contas de armazenamento para permitir o acesso somente de sub-redes específicas. As sub-redes permitidas podem pertencer a uma VNet na mesma assinatura ou àquelas em uma assinatura diferente, incluindo assinaturas que pertencem a um locatário de Azure Active Directory diferente.

Ativar uma [ponto final de serviço](/azure/virtual-network/virtual-network-service-endpoints-overview) do armazenamento do Azure dentro da VNet. O ponto de extremidade de serviço roteia o tráfego da VNet por meio de um caminho ideal para o serviço de armazenamento do Azure. As identidades da sub-rede e da rede virtual também são transmitidas com cada solicitação. Os administradores podem configurar as regras de rede para a conta de armazenamento que permitem que as solicitações sejam recebidas de sub-redes específicas em uma VNet. Os clientes concedido o acesso através destas regras de rede têm de continuar para cumprir os requisitos de autorização da conta de armazenamento para acessar os dados.

Cada conta de armazenamento suporta até 100 regras de rede virtual, que podem ser combinadas com [regras de rede IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Regiões de rede virtual disponível

Em geral, os pontos finais de serviço funcionam entre redes virtuais e instâncias do serviço na mesma região do Azure. Quando utilizar pontos finais de serviço com o armazenamento do Azure, este âmbito cresce para incluir o [região emparelhada](/azure/best-practices-availability-paired-regions). Pontos finais de serviço permitem a continuidade durante uma ativação pós-falha e o acesso a instâncias de armazenamento georredundante de só de leitura (RA-GRS). Regras de rede que concedam acesso a partir de uma rede virtual para uma conta de armazenamento também concedem acesso a qualquer instância de RA-GRS.

Quando planear a recuperação após desastre durante uma falha regional, deve criar as VNets na região emparelhada com antecedência. Ative pontos finais de serviço para o armazenamento do Azure, com regras de rede, conceder acesso a partir destas redes virtuais alternativas. Em seguida, aplica estas regras para as suas contas de armazenamento georredundante.

> [!NOTE]
> Pontos finais de serviço não se aplicam ao tráfego fora da região de rede virtual e o par de regiões designado. Só pode aplicar regras de rede, conceder acesso a partir de redes virtuais para contas de armazenamento na região primária de uma conta de armazenamento ou na região emparelhada designado.

### <a name="required-permissions"></a>Permissões obrigatórias

Para aplicar uma regra de rede virtual a uma conta de armazenamento, o utilizador tem de ter as permissões adequadas para as sub-redes que está a ser adicionadas. A permissão necessário é *aderir ao serviço a uma sub-rede* e está incluído nos *contribuinte de conta de armazenamento* função incorporada. Também podem ser adicionada às definições de função personalizada.

A conta de armazenamento e as redes virtuais com acesso concedido podem estar em assinaturas diferentes, incluindo assinaturas que fazem parte de um locatário diferente do Azure AD.

> [!NOTE]
> A configuração de regras que concedem acesso a sub-redes em redes virtuais que fazem parte de um locatário Azure Active Directory diferente atualmente só tem suporte por meio do PowerShell, da CLI e de APIs REST. Essas regras não podem ser configuradas por meio do portal do Azure, embora possam ser exibidas no Portal.

### <a name="managing-virtual-network-rules"></a>Gerir regras de rede virtual

Pode gerir regras de rede virtual para contas de armazenamento através do portal do Azure, PowerShell ou CLIv2.

#### <a name="azure-portal"></a>Portal do Azure

1. Vá para a conta de armazenamento que pretende proteger.

1. Clique no menu de definições denominado **Firewalls e redes virtuais**.

1. Verifique se selecionou para permitir o acesso a partir **redes selecionadas**.

1. Para conceder acesso a uma rede virtual com uma nova regra de rede, em **redes virtuais**, clique em **adicionar rede virtual existente**, selecione **redes virtuais** e **Sub-redes** opções e clique em **adicionar**. Para criar uma nova rede virtual e conceder acesso, clique em **Adicionar nova rede virtual**. Forneça as informações necessárias para criar a nova rede virtual e, em seguida, clique em **criar**.

    > [!NOTE]
    > Se um ponto final de serviço do armazenamento do Azure não foi configurado anteriormente para a rede virtual selecionada e sub-redes, pode configurá-lo como parte desta operação.
    >
    > Atualmente, somente as redes virtuais que pertencem ao mesmo locatário Azure Active Directory são mostradas para seleção durante a criação da regra. Para conceder acesso a uma sub-rede em uma rede virtual que pertence a outro locatário, use o PowerShell, a CLI ou as APIs REST.

1. Para remover uma rede virtual ou a regra de sub-rede, clique em **...**  para abrir o menu de contexto para a rede virtual ou sub-rede e clique em **remover**.

1. Clique em **guardar** para aplicar as alterações.

#### <a name="powershell"></a>PowerShell

1. Instalar o [do Azure PowerShell](/powershell/azure/install-Az-ps) e [iniciar sessão](/powershell/azure/authenticate-azureps).

1. Lista de regras de rede virtual.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Ative o ponto final de serviço do armazenamento do Azure numa rede virtual existente e a sub-rede.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Adicione uma regra de rede para uma rede virtual e uma sub-rede.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Para adicionar uma regra de rede para uma sub-rede em uma VNet que pertence a outro locatário do Azure AD, use um parâmetro **VirtualNetworkResourceId** totalmente qualificado no formato "/subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/Providers/Microsoft.Network/virtualNetworks/vNet-Name/Subnets/subnet-Name".

1. Remova uma regra de rede para uma rede virtual e uma sub-rede.

    ```powershell
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

    > [!TIP]
    > Para adicionar uma regra para uma sub-rede em uma VNet que pertence a outro locatário do Azure AD, use uma ID de sub-rede totalmente qualificada no formato "/subscriptions/\<Subscription-ID\>/resourceGroups/\<resourcegroup-Name\>/providers/Microsoft.Network/virtualNetworks/\<vNet-Name\>/Subnets/\<subnet-Name\>".
    >
    > Você pode usar o parâmetro de **assinatura** para recuperar a ID de sub-rede de uma VNet que pertence a outro locatário do Azure AD.

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

Regras de rede IP só são permitidas para **internet pública** endereços IP. Reservado para redes privadas de intervalos de endereços de IP (conforme definido na [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) não são permitidos em regras de IP. Redes privadas incluem endereços que comecem com _10.*_ , _172.16. *_  - _172.31. *_ , e _192.168. *_ .

   > [!NOTE]
   > Regras de rede IP não têm qualquer efeito nos pedidos com origem na mesma região do Azure que a conta de armazenamento. Uso [regras de rede Virtual](#grant-access-from-a-virtual-network) para permitir pedidos da mesma região.

  > [!NOTE]
  > Os serviços implantados na mesma região que a conta de armazenamento usam endereços IP privados do Azure para comunicação. Portanto, você não pode restringir o acesso a serviços específicos do Azure com base em seu intervalo de endereços IP de entrada públicos.

Somente endereços IPV4 têm suporte para a configuração de regras de firewall de armazenamento.

Cada conta de armazenamento dá suporte a até 100 regras de rede IP.

### <a name="configuring-access-from-on-premises-networks"></a>Configurar o acesso a partir de redes no local

Para conceder acesso a partir de suas redes no local para a sua conta de armazenamento com uma regra de rede IP, tem de identificar endereços IP utilizados pela sua rede de acesso à internet. Contacte o administrador de rede para obter ajuda.

Se você estiver usando o [ExpressRoute](/azure/expressroute/expressroute-introduction) de seu local, para emparelhamento público ou emparelhamento da Microsoft, será necessário identificar os endereços IP de NAT que são usados. Para peering público, cada circuito ExpressRoute, por predefinição, utiliza dois endereços IP NAT que são aplicados ao tráfego de serviço do Azure quando o tráfego entra no backbone de rede do Microsoft Azure. Para o emparelhamento da Microsoft, os endereços IP de NAT usados são fornecidos pelo cliente ou são fornecidos pelo provedor de serviços. Para permitir o acesso aos recursos de serviço, tem de permitir estes endereços IP públicos na definição da firewall do IP dos recursos. Para localizar os endereços IP do circuito ExpressRoute de peering público, [abra um pedido de suporte no ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) através do portal do Azure. Saiba mais sobre [NAT para peering público e da Microsoft do ExpressRoute.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

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

As regras de rede ajudam a criar um ambiente seguro para conexões entre seus aplicativos e seus dados para a maioria dos cenários. No entanto, alguns aplicativos dependem de serviços do Azure que não podem ser isolados exclusivamente por meio de regras de endereço IP ou rede virtual. Mas esses serviços devem ser concedidos ao armazenamento para habilitar a funcionalidade completa do aplicativo. Nessas situações, você pode usar a configuração ***permitir que serviços confiáveis da Microsoft...*** para permitir que esses serviços acessem seus dados, logs ou análises.

### <a name="trusted-microsoft-services"></a>Serviços Microsoft fidedignos

Alguns serviços da Microsoft operam de redes que não podem ser incluídas em suas regras de rede. Você pode conceder a um subconjunto desses serviços confiáveis da Microsoft acesso à conta de armazenamento, mantendo as regras de rede para outros aplicativos. Esses serviços confiáveis usarão a autenticação forte para se conectar à sua conta de armazenamento com segurança. Habilitamos dois modos de acesso confiável para serviços da Microsoft.

- Os recursos de alguns serviços, **quando registrados em sua assinatura**, podem acessar sua conta de armazenamento **na mesma assinatura** para operações SELECT, como gravar logs ou backup.
- Os recursos de alguns serviços podem receber acesso explícito à sua conta de armazenamento **atribuindo uma função de RBAC** à sua identidade gerenciada atribuída pelo sistema.


Quando você habilita a configuração **permitir serviços confiáveis da Microsoft...** , os recursos dos seguintes serviços registrados na mesma assinatura que sua conta de armazenamento recebem acesso a um conjunto limitado de operações, conforme descrito:

| Serviço                  | Nome do fornecedor de recursos     | Operações permitidas                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | Executar cópias de segurança e restauros de discos não geridos em máquinas de virtuais IAAS. (não necessário para discos geridos). [Saiba mais](/azure/backup/backup-introduction-to-azure-backup). |
| Azure Data Box           | Microsoft.DataBox          | Permite a importação de dados para o Azure usando Data Box. [Saiba mais](/azure/databox/data-box-overview). |
| Laboratórios DevTest do Azure       | Microsoft.DevTestLab       | Instalação de Federação e de criação de imagem personalizada. [Saiba mais](/azure/devtest-lab/devtest-lab-overview). |
| Azure Event Grid         | Microsoft.EventGrid        | Ativar a publicação de eventos de armazenamento de BLOBs e permitir que o Event Grid publicar em filas do armazenamento. Saiba mais sobre [eventos de armazenamento de BLOBs](/azure/event-grid/event-sources) e [publicação para filas](/azure/event-grid/event-handlers). |
| Hubs de Eventos do Azure         | Microsoft.EventHub         | Arquivar dados com a captura de Hubs de eventos. [Saiba mais](/azure/event-hubs/event-hubs-capture-overview). |
| Azure File Sync          | Microsoft.StorageSync      | Permite transformar seu servidor de arquivos local em um cache para compartilhamentos de arquivos do Azure. Permitir a sincronização de vários sites, recuperação rápida de desastres e backup no lado da nuvem. [Saiba mais](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Provisione o conteúdo inicial do sistema de arquivos padrão para um novo cluster HDInsight. [Saiba mais](/azure/hdinsight/hdinsight-hadoop-use-blob-storage). |
| Exportação de importação do Azure      | Microsoft.ImportExport     | Permite a importação de dados para o Azure e a exportação de dados do Azure usando o serviço de importação/exportação. [Saiba mais](/azure/storage/common/storage-import-export-service).  |
| Monitor do Azure            | Microsoft. insights         | Permite gravar dados de monitoramento em uma conta de armazenamento protegida, incluindo logs de diagnóstico de recursos, Azure Active Directory de entrada e logs de auditoria e logs de Microsoft Intune. [Saiba mais](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Redes do Azure         | Microsoft.Network          | Store e analisar registos de tráfego de rede. [Saiba mais](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). |
| Recuperação de Site do Azure      | Microsoft.SiteRecovery     | Habilite a replicação para recuperação de desastre de máquinas virtuais IaaS do Azure ao usar as contas de armazenamento de cache, origem ou destino habilitadas para firewall.  [Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

A configuração **permitir serviços confiáveis da Microsoft...** também permite que uma instância específica dos serviços a seguir acesse a conta de armazenamento, se você [atribuir explicitamente uma função RBAC](storage-auth-aad.md#assign-rbac-roles-for-access-rights) à [identidade gerenciada atribuída pelo sistema](../../active-directory/managed-identities-azure-resources/overview.md) para essa instância de recurso. Nesse caso, o escopo de acesso para a instância corresponde à função RBAC atribuída à identidade gerenciada.

| Serviço                        | Nome do fornecedor de recursos          | Finalidade            |
| :----------------------------- | :------------------------------------- | :---------- |
| Tarefas do Azure Container Registry | Microsoft.ContainerRegistry/registries | As tarefas de ACR podem acessar contas de armazenamento ao criar imagens de contêiner. |
| Azure Data Factory             | Microsoft.DataFactory/factories        | Permite o acesso a contas de armazenamento por meio do tempo de execução do ADF. |
| Azure Logic Apps               | Microsoft.Logic/workflows              | Permite que os aplicativos lógicos acessem contas de armazenamento. [Saiba mais](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Azure Machine Learning | Microsoft.MachineLearningServices      | Os espaços de trabalho Azure Machine Learning autorizados gravam a saída, os modelos e os logs do experimento no armazenamento de BLOBs. [Saiba mais](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). |
| Armazém de Dados SQL do Azure       | Microsoft.Sql                          | Permite a importação e a exportação de dados de instâncias específicas do banco do dados SQL usando o polybase. [Saiba mais](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Azure Stream Analytics         | Microsoft.StreamAnalytics             | Permite que os dados de um trabalho de streaming sejam gravados no armazenamento de BLOBs. Esta funcionalidade encontra-se em pré-visualização. [Saiba mais](/azure/stream-analytics/blob-output-managed-identity). |
| Azure Synapse Analytics        | Microsoft. Synapse/Workspaces          | Habilita o acesso a dados no armazenamento do Azure do Synapse Analytics. |


### <a name="storage-analytics-data-access"></a>Acesso de dados de análise de armazenamento

Em alguns casos, o acesso para ler os registos de diagnóstico e métricas é necessário a partir de fora do limite de rede. Ao configurar o acesso de serviços confiáveis à conta de armazenamento, você pode permitir o acesso de leitura para os arquivos de log, as tabelas de métricas ou ambos. [Saiba mais sobre como trabalhar com a análise de armazenamento.](/azure/storage/storage-analytics)

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

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Configure as exceções às regras de rede de conta de armazenamento.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Remova as exceções às regras de rede de conta de armazenamento.

    ```powershell
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

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre pontos finais de serviço de rede do Azure no [pontos finais de serviço](/azure/virtual-network/virtual-network-service-endpoints-overview).

Aprofunde os seus conhecimentos sobre a segurança de armazenamento do Azure no [guia de segurança do armazenamento do Azure](../blobs/security-recommendations.md).
