---
title: Conecte redes virtuais com o peering VNet - tutorial - Portal Azure
description: Neste tutorial, irá aprender a ligar redes virtuais com o peering de rede virtual, utilizando o Portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: b7fcf7f60b18d0d44ded67cb5b22bcdcdcd56a77
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059331"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Tutorial: ligar redes virtuais com o peering de rede virtual, utilizando o Portal do Azure

Pode ligar redes virtuais entre si com o peering de rede virtual. Estas redes virtuais podem estar na mesma região ou em regiões diferentes (também conhecidas como o peering de VNet Global). Depois de as redes virtuais estarem em modo de peering, os recursos nas duas redes virtuais conseguem comunicar entre si, com a mesma latência e largura de banda, como se os recursos estivessem na mesma rede virtual. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar duas redes virtuais
> * Ligar duas redes virtuais com um peering de rede virtual
> * Implementar uma máquina virtual (VM) em cada rede virtual
> * Comunicar entre VMs

Se preferir, pode concluir este tutorial com a [CLI do Azure](tutorial-connect-virtual-networks-cli.md) ou o [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, precisa de uma conta Azure com uma subscrição ativa. Se não tiver uma, pode [criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-virtual-networks"></a>Criar redes virtuais

1. No portal Azure, selecione **Criar um recurso**.
2. Selecione **Redes** e, em seguida, selecione **Rede virtual**.
3. No separador **Básicos,** introduza ou selecione as seguintes informações e aceite as padrão para as definições restantes:

    |Definição|Valor|
    |---|---|
    |Subscrição| Selecione a sua subscrição.|
    |Grupo de recursos| Selecione **Criar novo** e introduza *myResourceGroup*.|
    |Region| Selecione **East US**.|
    |Name|myVirtualNetwork1|

4. No separador **endereços IP, insira** 10.0.0.0/16 para o campo **Address Space.** Clique no botão **de sub-rede Adicionar** abaixo e introduza *a Sub-rede1* para **nome da sub-rede** e 10.0.0.0/24 para **a gama de endereços sub-rede**.
5. Selecione **'Rever + Criar'** e, em seguida, selecione **Criar.**
   
5. Execute novamente os passos 1 a 5, com as seguintes alterações:

    |Definição|Valor|
    |---|---|
    |Nome|myVirtualNetwork2|
    |Espaço de endereços|10.1.0.0/16|
    |Grupo de recursos| Selecione **Utilizar existente** e, em seguida, selecione **myResourceGroup**.|
    |Nome da sub-rede | Subnet2|
    |Intervalo de Endereços da Sub-rede|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Colocar redes virtuais em modo de peering

1. Na caixa Pesquisar na parte superior do Portal do Azure, comece a escrever *MyVirtualNetwork1*. Quando a **myVirtualNetwork1** aparecer nos resultados da pesquisa, selecione-a.
2. Selecione **Peerings**, em **Definições**, e, em seguida, selecione **Adicionar**, como mostrado na seguinte imagem:

    ![Criar peering](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **OK**.

    |Definição|Valor|
    |---|---|
    |Nome do espreitamento do myVirtualNetwork1 para rede virtual remota|myVirtualNetwork1-myVirtualNetwork2 - Quando a página for carregada pela primeira vez, verá a frase "rede virtual remota" aqui. Depois de escolher a rede virtual remota, a expressão "rede virtual remota" será substituída pelo nome da rede virtual remota.|
    |Subscrição| Selecione a sua subscrição.|
    |Rede virtual|myVirtualNetwork2 - Para selecionar a rede virtual *myVirtualNetwork2,* selecione **rede Virtual,** selecione **myVirtualNetwork2 (myResourceGroup)**. Pode selecionar uma rede virtual na mesma região ou numa região diferente.|
    |Nome do peering da myVirtualNetwork2 para myVirtualNetwork1|myVirtualNetwork2-myVirtualNetwork1|

    ![Definições de peering](./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional.png)

    O **ESTADO DE PEERING** está *ligado,* como mostra a seguinte imagem:

    ![Estado de peering](./media/tutorial-connect-virtual-networks-portal/peering-status-connected.png)

    Se não vir o estado, atualize o browser.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie uma VM em cada rede virtual, para que possa comunicar entre as mesmas num passo posterior.

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. No portal Azure, selecione **Criar um recurso**.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. Pode selecionar um sistema operativo diferente, mas os restantes passos partem do princípio de que selecionou **Windows Server 2016 Datacenter**. 
3. Introduza ou selecione as seguintes informações em **Informações Básicas**, aceite as predefinições das restantes definições e, em seguida, selecione **Criar**:

    |Definição|Valor|
    |---|---|
    |Grupo de recursos| Selecione **Utilizar existente** e, em seguida, selecione **myResourceGroup**.|
    |Name|myVm1|
    |Localização| Selecione **East US**.|
    |Nome de utilizador| Introduza um nome de utilizador à sua escolha.|
    |Palavra-passe| Introduza uma palavra-passe à sua escolha. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   
4. Selecione um tamanho VM para a opção **Tamanho.**
5. Selecione os seguintes valores para em **Rede:**

    |Definição|Valor|
    |---|---|
    |Rede virtual| myVirtualNetwork1 - Se ainda não estiver selecionado, selecione **a rede Virtual** e, em seguida, selecione **myVirtualNetwork1**.|
    |Sub-rede| Sub-rede1 - Se ainda não estiver selecionado, selecione **Sub-rede** e, em seguida, selecione **Subnet1**.|
   
6. Selecione **Rede**. Escolha **Permitir portas selecionadas** para a opção **portas de entrada pública.** Escolha **RDP** para a opção **Portas de entrada Select** abaixo desta. 

7. Selecione o botão **'Rever + Criar'** no canto inferior esquerdo para iniciar a implantação de VM.

### <a name="create-the-second-vm"></a>Criar a segunda VM

Conclua os passos 1 a 6 novamente, com as seguintes alterações:

|Definição|Valor|
|---|---|
|Nome | myVm2|
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
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
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

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos e todos os recursos contidos no mesmo: 

1. Introduza *myResourceGroup* na caixa **Pesquisar**, na parte superior do portal. Quando vir o **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Introduza *myResourceGroup* em **ESCREVER O NOME DO GRUPO DE RECURSOS:** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre olhando para a rede virtual](virtual-network-peering-overview.md)


