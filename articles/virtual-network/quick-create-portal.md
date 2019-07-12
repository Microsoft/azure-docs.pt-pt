---
title: Criar uma rede virtual - início rápido - portal do Azure
titlesuffix: Azure Virtual Network
description: Neste início rápido, vai aprender a criar uma rede virtual com o Portal do Azure. Uma rede virtual permite que os recursos do Azure, como as máquinas virtuais, comunicar de forma segura entre si e com a internet
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can securely communicate with each other and with the internet.
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 07/08/2019
ms.author: kumud
ms.openlocfilehash: bbc40ae358a6ac7f58e01de997728db21c7eb3bc
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839718"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Início rápido: Criar uma rede virtual com o portal do Azure

Uma rede virtual é o bloco de construção fundamental para a sua rede privada no Azure. Permite que os recursos do Azure, como as máquinas virtuais (VMs), para comunicar de forma segura entre si e com a internet. Neste início rápido, irá aprender como criar uma rede virtual com o portal do Azure. Em seguida, pode implementar duas VMs na rede virtual, com segurança comunicar entre as duas VMs e ligar às VMs a partir da internet.


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No lado do canto superior esquerdo do ecrã, selecione **criar um recurso** > **rede** > **rede Virtual**.

1. Na **criar rede virtual**, introduza ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Introduza *myVirtualNetwork*. |
    | Espaço de endereços | Enter *10.1.0.0/16*. |
    | Subscription | Selecione a sua subscrição.|
    | Resource group | Selecione **criar novo**, introduza *myResourceGroup*, em seguida, selecione **OK**. |
    | Location | Selecione **E.U.A. Leste**.|
    | Sub-rede - nome | Introduza *myVirtualSubnet*. |
    | Sub-rede - Intervalo de endereços | Enter *10.1.0.0/24*. |

1. Deixe as restantes como padrão e selecione **criar**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual:

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. No lado do canto superior esquerdo do ecrã, selecione **criar um recurso** > **computação** > **2019 do Windows Server Datacenter**.

1. Na **criar uma máquina virtual - Noções básicas**, introduza ou selecione estas informações:

    | Definição | Value |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscription | Selecione a sua subscrição. |
    | Resource group | Selecione **myResourceGroup**. Criou na secção anterior. |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Introduza *myVm1*. |
    | Região | Selecione **E.U.A. Leste**. |
    | Opções de disponibilidade | Deixe a predefinição **nenhuma redundância de infraestrutura necessária**. |
    | Image | Deixe a predefinição **Centro de dados do Windows Server 2019**. |
    | Size | Deixe a predefinição **Standard DS1 v2**. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de utilizador | Introduza um nome de utilizador à sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar palavra-passe | Reintroduza a palavra-passe. |
    | **REGRAS DE PORTA DE ENTRADA** |  |
    | Portas de entrada públicas | Deixe a predefinição **None**. |
    | **POUPE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe a predefinição **não**. |

1. Selecione **seguinte: Discos**.

1. Na **criar uma máquina virtual - discos**, deixe as predefinições e selecione **seguinte: Funcionamento em rede**.

1. Na **criar uma máquina virtual - rede**, selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe a predefinição **myVirtualNetwork**. |
    | Subnet | Deixe a predefinição **myVirtualSubnet (10.1.0.0/24)** . |
    | IP público | Deixe a predefinição **(novo) myVm-ip**. |
    | Portas de entrada públicas | Selecione **permitir portas selecionadas**. |
    | Selecione as portas de entrada | Selecione **HTTP** e **RDP**.

1. Selecione **seguinte: Management**.

1. Na **criar uma máquina virtual - gerenciamento**, para **conta de armazenamento de diagnóstico**, selecione **criar novo**.

1. Na **criar conta de armazenamento**, introduza ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Introduza *myvmstorageaccount*. Se este nome é utilizado, crie um nome exclusivo.|
    | Tipo de conta | Deixe a predefinição **armazenamento (v1 de fins gerais)** . |
    | Desempenho | Deixe a predefinição **padrão**. |
    | Replicação | Deixe a predefinição **armazenamento localmente redundante (LRS)** . |

1. Selecione **OK**

1. Selecione **Rever + criar**. É levado para o **rever + criar** página em que o Azure valida a configuração.

1. Quando vir o **a validação passada** mensagem, selecione **criar**.

### <a name="create-the-second-vm"></a>Criar a segunda VM

1. Conclua os passos 1 e 9 acima.

    > [!NOTE]
    > No passo 2, para o **nome da Máquina Virtual**, introduza *myVm2*.
    >
    > No passo 7, para **conta de armazenamento de diagnóstico**, certifique-se de que seleciona **myvmstorageaccount**.

1. Selecione **Rever + criar**. É levado para o **rever + criar** página e o Azure valida a configuração.

1. Quando vir o **a validação passada** mensagem, selecione **criar**.

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Depois de criar *myVm1*, ligar à internet.

1. Na barra de pesquisa do portal, introduza *myVm1*.

1. Selecione o botão **Ligar**.

    ![Ligar a uma máquina virtual](./media/quick-create-portal/connect-to-virtual-machine.png)

    Depois de selecionar o **Connect** botão **ligar a máquina virtual** abre.

1. Selecione **transferir ficheiro RDP**. O Azure cria um protocolo de ambiente de trabalho remoto ( *. rdp*) de ficheiros e transfere-o para o seu computador.

1. Abra o transferido *. rdp* ficheiro.

    1. Se lhe for pedido, selecione **Ligar**.

    1. Introduza o nome de utilizador e palavra-passe que especificou quando criou a VM.

        > [!NOTE]
        > Poderá ter de selecionar **mais escolhas** > **utilizar uma conta diferente**, para especificar as credenciais que introduziu quando criou a VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o início de sessão no processo. Se receber um aviso de certificado, selecione **Sim** ou **continuar**.

1. Assim que for apresentada a área de trabalho VM, minimizá-lo para voltar à sua área de trabalho local.

## <a name="communicate-between-vms"></a>Comunicar entre VMs

1. Na área de trabalho remota de *myVm1*, abra o PowerShell.

1. Introduza `ping myVm2`.

    Receberá uma mensagem semelhante a esta:

    ```powershell
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    O `ping` falhar, uma vez que `ping` usa o controle de mensagem ICMP (Internet Protocol). Por predefinição, o ICMP não é permitido através da firewall do Windows.

1. Para permitir *myVm2* fazer ping *myVm1* num passo posterior, introduzir este comando:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Este comando permite que o ICMP entrada através da firewall do Windows:

1. Feche a ligação de ambiente de trabalho remoto à *myVm1*.

1. Conclua novamente os passos em [Ligar a uma VM a partir da Internet](#connect-to-a-vm-from-the-internet), mas ligue à *myVm2*.

1. Numa linha de comandos, introduza `ping myvm1`.

    Obterá algo semelhante a esta mensagem:

    ```powershell
    Pinging myVm1.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    ```

    Vai receber respostas partir *myVm1*, porque permitiu o ICMP através da firewall do Windows no *myVm1* VM no passo 3.

1. Feche a ligação de ambiente de trabalho remoto à *myVm2*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar com a rede virtual e as VMs, elimine o grupo de recursos e todos os recursos que contém:

1. Introduza *myResourceGroup* no **pesquisa** caixa na parte superior do portal e selecione **myResourceGroup** resultados da pesquisa.

1. Selecione **Eliminar grupo de recursos**.

1. Introduza *myResourceGroup* para **tipo o nome de grupo de recursos** e selecione **eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou uma rede virtual predefinida e duas VMs. Ligado a uma VM a partir da internet e comunicada de forma segura entre as duas VMs. Para saber mais sobre as definições de rede virtual, veja [Gerir uma rede virtual](manage-virtual-network.md).

Por predefinição, o Azure permite uma comunicação segura sem restrições entre VMs. Por outro lado, ele só permite ligações de ambiente de trabalho remotas entrada às VMs do Windows da internet. Para saber mais sobre como configurar diferentes tipos de comunicações de rede VM, vá para o [filtrar o tráfego de rede](tutorial-filter-network-traffic.md) tutorial.
