---
title: Criar uma rede virtual do Azure (clássica) com várias sub-redes | Microsoft Docs
description: Saiba como criar uma rede virtual (clássica) com várias sub-redes no Azure.
services: virtual-network
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: ''
ms.openlocfilehash: d934386a47c339cd3abdf72578736b44d40e7952
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059000"
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Criar uma rede virtual (clássica) com várias sub-redes

> [!IMPORTANT]
> O Azure tem dois [modelos de implantação diferentes](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criar e trabalhar com recursos: Resource Manager e clássico. Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda criar a maioria das novas redes virtuais por meio do modelo de implantação do [Resource Manager](quick-create-portal.md) .

Neste tutorial, saiba como criar uma rede virtual básica do Azure (clássica) que tenha sub-redes públicas e privadas separadas. Você pode criar recursos do Azure, como máquinas virtuais e serviços de nuvem em uma sub-rede. Os recursos criados em redes virtuais (clássicas) podem se comunicar entre si e com recursos em outras redes conectadas a uma rede virtual.

Saiba mais sobre todas as configurações de [rede virtual](manage-virtual-network.md) e de [sub-rede](virtual-network-manage-subnet.md) .

> [!WARNING]
> As redes virtuais (clássicas) são excluídas imediatamente pelo Azure quando uma [assinatura é desabilitada](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). As redes virtuais (clássicas) são excluídas independentemente de existirem recursos na rede virtual. Se você reabilitar a assinatura mais tarde, os recursos que existiam na rede virtual deverão ser recriados.

Você pode criar uma rede virtual (clássica) usando o [portal do Azure](#portal), a [CLI (interface de linha de comando) do Azure 1,0](#azure-cli)ou o [PowerShell](#powershell).

## <a name="portal"></a>Portal

1. Em um navegador da Internet, vá para a [portal do Azure](https://portal.azure.com). Faça logon usando sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, pode inscrever-se para obter uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Clique em **criar um recurso** no Portal.
3. Insira *rede virtual* na caixa **Pesquisar no Marketplace** na parte superior do painel **novo** que aparece. Clique em **rede virtual** quando ela aparecer nos resultados da pesquisa.
4. Selecione **clássico** na caixa **selecionar um modelo de implantação** no painel **rede virtual** que aparece e clique em **criar**. 
5. Insira os seguintes valores no painel **criar rede virtual (clássico)** e, em seguida, clique em **criar**:

    |Definição|Valor|
    |---|---|
    |Name|myVnet|
    |Espaço de endereços|10.0.0.0/16|
    |Nome de sub-rede|Público|
    |Intervalo de endereços de sub-rede|10.0.0.0/24|
    |Resource group|Deixe **criar novo** selecionado e, em seguida, insira **MyResource**.|
    |Assinatura e local|Selecione sua assinatura e o local.

    Se você for novo no Azure, saiba mais sobre [grupos de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [assinaturas](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)e [locais](https://azure.microsoft.com/regions) (também conhecidos como *regiões*).
4. No portal, você pode criar apenas uma sub-rede ao criar uma rede virtual. Neste tutorial, você criará uma segunda sub-rede depois de criar a rede virtual. Posteriormente, você poderá criar recursos acessíveis pela Internet na sub-rede **pública** . Você também pode criar recursos que não podem ser acessados pela Internet na sub-rede **privada** . Para criar a segunda sub-rede, insira **myVnet** na caixa **Pesquisar recursos** na parte superior da página. Clique em **myVnet** quando ele aparecer nos resultados da pesquisa.
5. Clique em **sub-redes** (na seção **configurações** ) no painel **criar rede virtual (clássica)** que aparece.
6. Clique em **+ Adicionar** no painel **myVnet-sub-redes** que aparece.
7. Insira **privado** para **nome** no painel **Adicionar sub-rede** . Insira **10.0.1.0/24** para o **intervalo de endereços**.  Clique em **OK**.
8. No painel **myVnet – sub-redes** , você pode ver as sub-redes **públicas** e **privadas** que você criou.
9. **Opcional**: Ao concluir este tutorial, talvez você queira excluir os recursos que criou, para que não incorra em encargos de uso:
    - Clique em **visão geral** no painel **myVnet** .
    - Clique no ícone **excluir** no painel **myVnet** .
    - Para confirmar a exclusão, clique em **Sim** na caixa **excluir rede virtual** .

## <a name="azure-cli"></a>CLI do Azure

1. Você pode [instalar e configurar o CLI do Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ou usar a CLI dentro do Azure cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Para obter ajuda para comandos da CLI, `azure <command> --help`digite. 
2. Em uma sessão da CLI, faça logon no Azure com o comando a seguir. Se você clicar em **Experimente** na caixa abaixo, um Cloud shell será aberto. Você pode fazer logon em sua assinatura do Azure, sem inserir o seguinte comando:

    ```azurecli-interactive
    azure login
    ```

3. Para garantir que a CLI esteja no modo de gerenciamento de serviços, digite o seguinte comando:

    ```azurecli-interactive
    azure config mode asm
    ```

4. Crie uma rede virtual com uma sub-rede privada:

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Crie uma sub-rede pública dentro da rede virtual:

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Examine a rede virtual e as sub-redes:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Opcional**: Talvez você queira excluir os recursos que criou ao concluir este tutorial, para que você não incorra em encargos de uso:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> Embora você não possa especificar um grupo de recursos para criar uma rede virtual (clássica) no usando a CLI, o Azure cria a rede virtual em um grupo de recursos chamado *Default-Networking*.

## <a name="powershell"></a>PowerShell

1. Instale a versão mais recente do módulo PowerShell do [Azure](https://www.powershellgallery.com/packages/Azure) . Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell.
3. No PowerShell, inicie sessão no Azure introduzindo o comando `Add-AzureAccount`.
4. Altere o caminho e o nome de arquivo a seguir, conforme apropriado, e exporte o arquivo de configuração de rede existente:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Para criar uma rede virtual com sub-redes públicas e privadas, use qualquer editor de texto para adicionar o elemento **VirtualNetworkSite** que segue ao arquivo de configuração de rede.

    ```xml
    <VirtualNetworkSite name="myVnet" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Private">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Public">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    ```

    Examine o esquema completo do [arquivo de configuração de rede](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. Importe o arquivo de configuração de rede:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > A importação de um arquivo de configuração de rede alterado pode causar alterações em redes virtuais existentes (clássicas) em sua assinatura. Certifique-se de adicionar apenas a rede virtual anterior e de que você não altere ou remova as redes virtuais existentes da sua assinatura. 

7. Examine a rede virtual e as sub-redes:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Opcional**: Talvez você queira excluir os recursos que criou ao concluir este tutorial, para que você não incorra em encargos de uso. Para excluir a rede virtual, conclua as etapas 4-6 novamente, desta vez removendo o elemento **VirtualNetworkSite** que você adicionou na etapa 5.
 
> [!NOTE]
> Embora você não possa especificar um grupo de recursos para criar uma rede virtual (clássica) no usando o PowerShell, o Azure cria a rede virtual em um grupo de recursos chamado *Default-Networking*.

---

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre todas as configurações de rede virtual e sub-rede, consulte [gerenciar redes virtuais](manage-virtual-network.md) e [gerenciar sub-redes de rede virtual](virtual-network-manage-subnet.md). Você tem várias opções para usar redes virtuais e sub-redes em um ambiente de produção para atender a diferentes requisitos.
- Crie uma máquina virtual do [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou do [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e conecte-a a uma rede virtual existente.
- Para conectar duas redes virtuais no mesmo local do Azure, crie um [emparelhamento de rede virtual](create-peering-different-deployment-models.md) entre as redes virtuais. Você pode emparelhar uma rede virtual (Resource Manager) a uma rede virtual (clássica), mas não pode criar um emparelhamento entre duas redes virtuais (clássicas).
- Conecte a rede virtual a uma rede local usando um [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou um circuito [do Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .
