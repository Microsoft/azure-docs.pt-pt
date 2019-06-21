---
title: Criar uma rede virtual do Azure (clássica) com várias sub-redes | Documentos da Microsoft
description: Saiba como criar uma rede virtual (clássica) com várias sub-redes no Azure.
services: virtual-network
documentationcenter: ''
author: genlin
manager: cshepard
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
ms.openlocfilehash: e40648ef47b108050486d43eefdb1564786c053e
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202879"
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Criar uma rede virtual (clássica) com várias sub-redes

> [!IMPORTANT]
> O Azure tem dois [modelos de implementação diferentes](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criar e trabalhar com recursos: Resource Manager e clássica. Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda a criar a maioria das novas redes virtuais através da [Resource Manager](quick-create-portal.md) modelo de implementação.

Neste tutorial, saiba como criar uma básica rede virtual do Azure (clássica) com sub-redes separadas, públicas e privadas. Pode criar recursos do Azure, como máquinas virtuais e serviços em nuvem numa sub-rede. Recursos criados nas redes virtuais (clássicos) podem comunicar entre si e com recursos de outras redes ligadas a uma rede virtual.

Saiba mais sobre todos os [rede virtual](manage-virtual-network.md) e [sub-rede](virtual-network-manage-subnet.md) definições.

> [!WARNING]
> Redes virtuais (clássico) são imediatamente eliminados pelo Azure quando uma [subscrição está desativada](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). Redes virtuais (clássicas) são eliminados, independentemente de se recursos existem na rede virtual. Se mais tarde voltar a ativar a subscrição, tem de ser recriados recursos que existiam na rede virtual.

Pode criar uma rede virtual (clássico) com o [portal do Azure](#portal), o [interface de linha de comandos (CLI) 1.0 do Azure](#azure-cli), ou [PowerShell](#powershell).

## <a name="portal"></a>Portal

1. Num browser da Internet, vá para o [portal do Azure](https://portal.azure.com). Inicie sessão com a sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, pode inscrever-se para obter uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Clique em **criar um recurso** no portal.
3. Introduza *rede Virtual* no **pesquisar no Marketplace** caixa na parte superior do **New** painel que aparece. Clique em **rede Virtual** quando for apresentada nos resultados da pesquisa.
4. Selecione **clássica** no **selecionar um modelo de implementação** caixa o **rede Virtual** painel apresentado, clique em **criar**. 
5. Introduza os seguintes valores **criar rede virtual (clássico)** painel e clique em **criar**:

    |Definição|Valor|
    |---|---|
    |Name|myVnet|
    |Espaço de endereços|10.0.0.0/16|
    |Nome da sub-rede|Público|
    |Intervalo de endereços da sub-rede|10.0.0.0/24|
    |Grupo de recursos|Deixe **criar novo** selecionada e, em seguida, introduza **myResourceGroup**.|
    |Subscrição e localização|Selecione a sua subscrição e localização.

    Se estiver familiarizado com o Azure, saiba mais sobre [grupos de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [subscrições](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), e [localizações](https://azure.microsoft.com/regions) (também conhecido como *regiões*).
4. No portal, pode criar apenas uma sub-rede quando criar uma rede virtual. Neste tutorial, vai criar uma segunda sub-rede depois de criar a rede virtual. Mais tarde poderá criar recursos acessível pela Internet na **público** sub-rede. Também pode criar recursos que não estão acessíveis a partir da Internet no **privada** sub-rede. Para criar a segunda sub-rede, introduza **myVnet** no **procurar recursos** caixa na parte superior da página. Clique em **myVnet** quando for apresentada nos resultados da pesquisa.
5. Clique em **sub-redes** (no **definições** secção) no **criar rede virtual (clássico)** painel que aparece.
6. Clique em **+ adicionar** sobre o **myVnet - sub-redes** painel que aparece.
7. Introduza **privada** para **nome** sobre o **adicionar sub-rede** painel. Introduza **10.0.1.0/24** para **intervalo de endereços**.  Clique em **OK**.
8. Sobre o **myVnet - sub-redes** painel, pode ver a **público** e **privada** sub-redes que criou.
9. **Opcional**: Quando terminar este tutorial, pode querer eliminar os recursos que criou, para que não a incorrer em custos de utilização:
    - Clique em **descrição geral** sobre o **myVnet** painel.
    - Clique a **elimine** ícone na **myVnet** painel.
    - Para confirmar a eliminação, clique em **Sim** no **rede virtual de eliminação** caixa.

## <a name="azure-cli"></a>CLI do Azure

1. Pode [instalar e configurar a CLI do Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), ou utilizar a CLI do Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Para obter ajuda para comandos da CLI, escreva `azure <command> --help`. 
2. Numa sessão do CLI, inicie sessão no Azure com o comando seguinte. Se clicar **experimente** na caixa abaixo, uma Cloud Shell é aberto. Pode iniciar sessão sua subscrição do Azure, sem introduzir o seguinte comando:

    ```azurecli-interactive
    azure login
    ```

3. Para garantir que a CLI está no modo de gestão do serviço, introduza o seguinte comando:

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
    
6. Reveja a rede virtual e sub-redes:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Opcional**: Pode querer eliminar os recursos que criou quando terminar este tutorial, para que não a incorrer em custos de utilização:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> Apesar de não é possível especificar um grupo de recursos para criar uma rede virtual (clássica) com a CLI, o Azure cria a rede virtual no grupo de recursos chamado *sistema de rede padrão*.

## <a name="powershell"></a>PowerShell

1. Instalar a versão mais recente do PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) módulo. Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell.
3. No PowerShell, inicie sessão no Azure introduzindo o comando `Add-AzureAccount`.
4. Altere o seguinte caminho e nome de ficheiro, conforme apropriado, em seguida, exportar o ficheiro de configuração de rede existente:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Para criar uma rede virtual com a sub-redes públicas e privadas, utilizar o editor de texto para adicionar o **VirtualNetworkSite** elemento que se segue para o ficheiro de configuração de rede.

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

    Reveja o completo [esquema de ficheiro de configuração de rede](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. Importe o ficheiro de configuração de rede:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > Importar um ficheiro de configuração de rede alterada pode fazer com que as alterações às redes virtuais existentes (clássicas) na sua subscrição. Certifique-se de que só é adicionar a rede virtual anterior e que não alterar ou remover quaisquer redes virtuais existentes da sua subscrição. 

7. Reveja a rede virtual e sub-redes:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Opcional**: Pode querer eliminar os recursos que criou quando terminar este tutorial, para que não a incorrer em custos de utilização. Para eliminar a rede virtual, concluído os passos 4 a 6 novamente, este tempo, remover os **VirtualNetworkSite** elemento que adicionou no passo 5.
 
> [!NOTE]
> Apesar de não é possível especificar um grupo de recursos para criar uma rede virtual (clássica) com o PowerShell, o Azure cria a rede virtual no grupo de recursos chamado *sistema de rede padrão*.

---

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre todas as definições de sub-rede e rede virtual, veja [gerir redes virtuais](manage-virtual-network.md) e [gerir sub-redes da rede virtual](virtual-network-manage-subnet.md). Tem várias opções para utilizar redes virtuais e sub-redes num ambiente de produção para atender aos requisitos diferentes.
- Criar uma [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou uma [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) máquina virtual e, em seguida, ligá-la a uma rede virtual existente.
- Para ligar duas redes virtuais na mesma localização do Azure, crie uma [peering de rede virtual](create-peering-different-deployment-models.md) entre as redes virtuais. Pode configurar o peering entre uma rede virtual (Resource Manager) a uma rede virtual (clássica), mas não é possível criar um peering entre duas redes virtuais (clássicas).
- Ligar a rede virtual a uma rede no local com uma [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) circuito.
