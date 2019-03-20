---
title: Ligar redes virtuais com o peering de rede virtual - tutorial - Portal do Azure | Microsoft Docs
description: Neste tutorial, irá aprender a ligar redes virtuais com o peering de rede virtual, utilizando o Portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 08/16/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: e66747cd350d10a5a66ec54b9aae9e9b485b0ba2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58014488"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Tutorial: Ligar redes virtuais com o peering de rede virtual com o portal do Azure

Pode ligar redes virtuais entre si com o peering de rede virtual. Estas redes virtuais podem estar na mesma região ou em regiões diferentes (também conhecidas como o peering de VNet Global). Depois de as redes virtuais estarem em modo de peering, os recursos nas duas redes virtuais conseguem comunicar entre si, com a mesma latência e largura de banda, como se os recursos estivessem na mesma rede virtual. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar duas redes virtuais
> * Ligar duas redes virtuais com um peering de rede virtual
> * Implementar uma máquina virtual (VM) em cada rede virtual
> * Comunicar entre VMs

Se preferir, pode concluir este tutorial com a [CLI do Azure](tutorial-connect-virtual-networks-cli.md) ou o [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-virtual-networks"></a>Criar redes virtuais

1. Selecione **+ Criar um recurso**, no canto superior esquerdo do Portal do Azure.
2. Selecione **Redes** e, em seguida, selecione **Rede virtual**.
3. Introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **Criar**:

    |Definição|Valor|
    |---|---|
    |Name|myVirtualNetwork1|
    |Espaço de endereços|10.0.0.0/16|
    |Subscrição| Selecione a sua subscrição.|
    |Grupo de recursos| Selecione **Criar novo** e introduza *myResourceGroup*.|
    |Localização| Selecione **E.U.A. Leste**.|
    |Nome da sub-rede|Subnet1|
    |Intervalo de endereços da sub-rede|10.0.0.0/24|

      ![Criar uma rede virtual](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Conclua os passos 1 a 3 novamente, com as seguintes alterações:

    |Definição|Valor|
    |---|---|
    |Name|myVirtualNetwork2|
    |Espaço de endereços|10.1.0.0/16|
    |Grupo de recursos| Selecione **Utilizar existente** e, em seguida, selecione **myResourceGroup**.|
    |Intervalo de Endereços da Sub-rede|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Colocar redes virtuais em modo de peering

1. Na caixa Pesquisar na parte superior do Portal do Azure, comece a escrever *MyVirtualNetwork1*. Quando a **myVirtualNetwork1** aparecer nos resultados da pesquisa, selecione-a.
2. Selecione **Peerings**, em **DEFINIÇÕES** e, em seguida, selecione **+ Adicionar**, conforme mostrado na imagem seguinte:

    ![Criar peering](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **OK**.

    |Definição|Valor|
    |---|---|
    |Name|myVirtualNetwork1-myVirtualNetwork2|
    |Subscrição| Selecione a sua subscrição.|
    |Rede virtual|myVirtualNetwork2 - para selecionar a rede virtual *myVirtualNetwork2*, selecione **Rede virtual** e, em seguida, selecione **myVirtualNetwork2**. Pode selecionar uma rede virtual na mesma região ou numa região diferente.|

    ![Definições de peering](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    O **ESTADO DE PEERING** é *iniciado*, conforme mostrado na imagem seguinte:

    ![Estado de peering](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Se não vir o estado, atualize o browser.

4. Na caixa **Pesquisar** na parte superior do Portal do Azure, comece a escrever *MyVirtualNetwork2*. Quando a **myVirtualNetwork2** aparecer nos resultados da pesquisa, selecione-a.
5. Conclua os passos 2 a 3 novamente, com as seguintes alterações e, em seguida, selecione **OK**:

    |Definição|Valor|
    |---|---|
    |Name|myVirtualNetwork2-myVirtualNetwork1|
    |Rede virtual|myVirtualNetwork1|

    O **ESTADO DE PEERING** é *Ligado*. O Azure também alterou o estado de peering das *myVirtualNetwork2-myVirtualNetwork1*, de *Iniciado* para *Ligado.* O peering de rede virtual não é totalmente estabelecido até que o estado do peering para ambas as redes virtuais seja *Ligado.* 

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie uma VM em cada rede virtual, para que possa comunicar entre as mesmas num passo posterior.

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. Selecione **+ Criar um recurso**, no canto superior esquerdo do Portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. Pode selecionar um sistema operativo diferente, mas os restantes passos partem do princípio de que selecionou **Windows Server 2016 Datacenter**. 
3. Introduza ou selecione as seguintes informações em **Informações Básicas**, aceite as predefinições das restantes definições e, em seguida, selecione **Criar**:

    |Definição|Valor|
    |---|---|
    |Name|myVm1|
    |Nome de utilizador| Introduza um nome de utilizador à sua escolha.|
    |Palavra-passe| Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupo de recursos| Selecione **Utilizar existente** e, em seguida, selecione **myResourceGroup**.|
    |Localização| Selecione **E.U.A. Leste**.|
4. Selecione um tamanho de VM em **Escolher um tamanho**.
5. Selecione os seguintes valores para **Definições** e, em seguida, selecione **OK**:

    |Definição|Valor|
    |---|---|
    |Rede virtual| myVirtualNetwork1 - se ainda não estiver selecionado, selecione **Rede virtual** e, em seguida, selecione **myVirtualNetwork1** em **Escolher rede virtual**.|
    |Subrede| Subnet1 - se ainda não estiver selecionado, selecione **Sub-rede** e, em seguida, selecione **Subnet1** em **Escolher sub-rede**.|
    
    ![Definições da máquina virtual](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. Em **Criar** no **Resumo**, selecione **Criar** para iniciar a implementação da VM.

### <a name="create-the-second-vm"></a>Criar a segunda VM

Conclua os passos 1 a 6 novamente, com as seguintes alterações:

|Definição|Valor|
|---|---|
|Name | myVm2|
|Rede virtual | myVirtualNetwork2|

A criação das VMs demora alguns minutos. Não prossiga com os restantes passos até que ambas as VMs tenham sido criadas.

## <a name="communicate-between-vms"></a>Comunicar entre VMs

1. Na caixa *Pesquisar* na parte superior do Portal do Azure, comece a escrever *myVm1*. Quando a **myVm1** aparecer nos resultados da pesquisa, selecione-a.
2. Crie uma ligação de ambiente de trabalho remoto à VM *myVm1*, selecionando **Ligar**, conforme mostrado na imagem seguinte:

    ![Conectar à máquina virtual](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Para ligar à VM, abra o ficheiro RDP transferido. Se lhe for pedido, selecione **Ligar**.
4. Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM (poderá ter de selecionar **Mais opções** e **Utilizar uma conta diferente** para especificar as credenciais que introduziu quando criou a VM) e, em seguida, selecione **OK**.
5. Poderá receber um aviso de certificado durante o processo de início de sessão. Selecione **Sim** para continuar a ligação.
6. Num passo posterior, o ping é utilizado para comunicar com a VM *myVm2* a partir da VM *myVm1*. O ping utiliza o protocolo ICMP (Internet Control Message Protocol), que é negado através da firewall do Windows, por predefinição. Na VM *myVm1*, ative o ICMP através da firewall do Windows, para que consiga enviar pings para esta VM a partir da *myVm2* num passo posterior, com o PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```
    
    Apesar de o ping ser utilizado para comunicar entre VMs neste tutorial, não é recomendado permitir o ICMP através da Firewall do Windows para implementações de produção.

7. Para ligar à VM *myVm2*, introduza o seguinte comando numa linha de comandos da VM *myVm1*:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Uma vez que ativou o ping na *myVm1*, pode agora fazer ping por endereço IP:

    ```
    ping 10.0.0.4
    ```
    
9. Desligue as sessões RDP das *myVm1* e *myVm2*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos e todos os recursos contidos no mesmo: 

1. Introduza *myResourceGroup* na caixa **Pesquisar**, na parte superior do portal. Quando vir o **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Introduza *myResourceGroup* em **ESCREVER O NOME DO GRUPO DE RECURSOS:** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a ligar duas redes na mesma região do Azure, com o peering de rede virtual. Também pode ligar em modo de peering máquinas virtuais em diferentes [regiões suportadas](virtual-network-manage-peering.md#cross-region) e em [diferentes subscrições do Azure](create-peering-different-subscriptions.md#portal), bem como criar [designs de rede hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com peering. Para obter mais informações sobre o peering de rede virtual, veja [Descrição geral do peering de rede virtual](virtual-network-peering-overview.md) e [Gerir peerings de rede virtual](virtual-network-manage-peering.md).

Para ligar o seu computador a uma rede virtual através de uma VPN e interagir com os recursos numa rede virtual ou em redes virtuais em modo de peering, veja [Ligar o computador a uma rede virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
