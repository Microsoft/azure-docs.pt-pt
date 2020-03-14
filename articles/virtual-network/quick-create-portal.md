---
title: Criar uma rede virtual - quickstart - Portal Azure
titleSuffix: Azure Virtual Network
description: 'Quickstart: Criar uma rede virtual no portal Azure. Estas redes permitem que os recursos do Azure, como os VMs, se comuniquem de forma segura entre si e com a internet.'
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2020
ms.author: kumud
ms.openlocfilehash: 1a1593566b8bdb72f322d64c1ee99c7018f49329
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240073"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Início Rápido: criar uma rede virtual com o Portal do Azure

Neste arranque rápido, aprende-se a criar uma rede virtual utilizando o portal Azure. Implementa duas máquinas virtuais (VMs). Em seguida, comunica-se com segurança entre VMs e liga-se a VMs a partir da internet. Uma rede virtual é o bloco de construção fundamental para a sua rede privada em Azure. Permite que os recursos do Azure, como os VMs, se comuniquem de forma segura entre si e com a internet.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. A partir do menu do portal Azure, selecione **Criar um recurso**. A partir do Mercado Azure, selecione **Networking** > **Rede Virtual.**

1. Na **Create rede virtual,** introduza ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | Selecione **Criar novo,** introduza *o meu Grupo de Recursos*e, em seguida, selecione **OK**. |
    | Nome | Introduza *a minha Rede Virtual*. |
    | Localização | Selecione **E.U.A. Leste**.|

1. Selecione **Seguinte: Endereços IP,** e para o espaço de **endereçoI4,** introduza *10.1.0.0/16*.

1. **Selecione Adicionar sub-rede,** em seguida, introduza *a minha Subrede Virtual* para o nome **subnet** e *10.1.0.0/24* para a gama de **endereços Subnet**.

1. **Selecione Adicionar,** edepois selecione **Rever + criar**. Deixe o resto como padrão e selecione **Criar**.

1. Na **Create rede virtual,** selecione **Criar**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual:

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. A partir do menu do portal Azure, selecione **Criar um recurso**.

1. A partir do Azure Marketplace, **selecione Compute** > **Windows Server 2019 Datacenter**. Selecione **Criar**.

1. Em **Criar uma máquina virtual - Básicos,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou este grupo de recursos na secção anterior. |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira *myVm1*. |
    | Região | Selecione **E.U.A. Leste**. |
    | Opções de disponibilidade | Padrão para nenhum despedimento de **infraestrutura necessário**. |
    | Imagem | Padrão para **Windows Server 2019 Datacenter**. |
    | Tamanho | Predefinição do **Standard DS1 v2**. |
    | **Conta de administrador** |  |
    | Nome de utilizador | Introduza um nome de utilizador à sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar palavra-passe | Reintroduza a senha. |
    | **Regras portuárias de entrada** |  |
    | Portos de entrada pública | **Selecione Permitir portas selecionadas**. |
    | Selecione portas de entrada | Insira *HTTP (80)* e *RDP (3389)* . |
    | **Poupe dinheiro** |  |
    | Já tem licença do Windows? | Padrão para **Nº**. |

1. Selecione **Seguinte: Discos**.

1. Em **Criar uma máquina virtual - Discos,** mantenha as predefinições e selecione **Seguinte: Networking**.

1. Em **Criar uma máquina virtual - Networking,** selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | Rede virtual | Padrão para **a minha Rede Virtual**. |
    | Subrede | Predefinição para **myVirtualSubnet (10.1.0.0/24)** . |
    | IP público | Padrão para **(novo) myVm-ip**. |
    | Grupo de segurança da rede NIC | Padrão para **Básico**. |
    | Portos de entrada pública | Predefinição para **permitir portas selecionadas**. |
    | Selecione portas de entrada | Padrão para **HTTP** e **RDP**.

1. Selecione **Seguinte: Gestão**.

1. Em **Criar uma máquina virtual - Gestão,** para conta de armazenamento de **diagnósticos,** selecione **Criar Novo**.

1. Na **conta de armazenamento Criar,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira a *conta myvmstorage.* Se este nome for tomado, crie um nome único.|
    | Tipo de conta | Padrão de **armazenamento (finalidade geral v1)** . |
    | Desempenho | Padrão para **Standard**. |
    | Replicação | Padrão para **armazenamento localmente redundante (LRS)** . |

1. Selecione **OK,** em seguida, selecione **Rever + criar**. É levado para o **Review + criar** página onde o Azure valida a sua configuração.

1. Quando vir a **mensagem de validação passada,** selecione **Criar**.

### <a name="create-the-second-vm"></a>Criar a segunda VM

Repita o procedimento na secção anterior para criar outra máquina virtual.

> [!IMPORTANT]
> Para o nome da **máquina Virtual,** introduza *myVm2*.
>
> Para a conta de **armazenamento de diagnóstico,** certifique-se de selecionar a **conta myvmstorage,** em vez de criar uma.

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Depois de criar o *myVm1,* ligue-se à internet.

1. No portal Azure, procure e selecione *myVm1*.

1. Selecione **Ligar,** depois **RDP**.

    ![Ligar a uma máquina virtual](./media/quick-create-portal/connect-to-virtual-machine.png)

    A página **Connect** abre.

1. Selecione **Download RDP File**. O Azure cria um ficheiro Remote Desktop *(.rdp)* e transfere-o para o computador.

1. Abra o ficheiro RDP. Se lhe for pedido, selecione **Ligar**.

1. Introduza o nome de utilizador e a palavra-passe especificado ao criar o VM.

    > [!NOTE]
    > Poderá ter de selecionar **Mais escolhas** > **Utilize uma conta diferente,** para especificar as credenciais que inseriu quando criou o VM.

1. Selecione **OK**.

1. Pode receber um aviso de certificado quando iniciar a sua inscrição. Se receber um aviso de certificado, selecione **Sim** ou **Continue**.

1. Assim que o ambiente de trabalho vM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.

## <a name="communicate-between-vms"></a>Comunicar entre VMs

1. No Ambiente de Trabalho Remoto do *myVm1,* abra powerShell.

1. Introduza `ping myVm2`.

    Receberá uma mensagem semelhante a esta saída:

    ```output
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    O `ping` falha, porque `ping` utiliza o Protocolo de Mensagem de Controlo de Internet (ICMP). Por padrão, o ICMP não é permitido através da firewall do Windows.

1. Para permitir que *o myVm2* ping *myVm1* em um passo posterior, insira este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Este comando permite a entrada do ICMP através da firewall do Windows:

1. Feche a ligação de ambiente de trabalho remoto à *myVm1*.

1. Conclua novamente os passos em [Ligar a uma VM a partir da Internet](#connect-to-a-vm-from-the-internet), mas ligue à *myVm2*.

1. Numa linha de comandos, introduza `ping myvm1`.

    Vais ter de volta algo como esta mensagem:

    ```output
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

    Recebe respostas do *myVm1,* porque permitiu que o ICMP passasse pela firewall do Windows no *myVm1* VM no passo 3.

1. Feche a ligação de ambiente de trabalho remoto à *myVm2*.

## <a name="clean-up-resources"></a>Limpar recursos

Neste início rápido, criou uma rede virtual predefinida e duas VMs. Ligou-se a um VM da internet e comunicou-se de forma segura entre os dois VMs.

Quando terminar a utilização da rede virtual e dos VMs, elimine o grupo de recursos e todos os recursos que contém:

1. Procure e selecione *myResourceGroup*.

1. Selecione **Eliminar grupo de recursos**.

1. Introduza *o myResourceGroup* para **ESCREVER O NOME DE GRUPO DE RECURSOS** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as definições de rede virtual, consulte [Criar, alterar ou eliminar uma rede virtual](manage-virtual-network.md).

Por padrão, o Azure permite uma comunicação segura entre VMs. O Azure só permite ligações remotas de ambiente de trabalho à entrada para VMs do Windows a partir da internet. Para saber mais sobre tipos de comunicações de rede VM, consulte o tráfego da [rede Filter](tutorial-filter-network-traffic.md).
