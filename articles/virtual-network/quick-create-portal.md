---
title: Criar uma rede virtual - quickstart - Portal Azure
titleSuffix: Azure Virtual Network
description: 'Início rápido: Criar uma rede virtual no portal Azure. Estas redes permitem que os recursos do Azure, como os VMs, se comuniquem de forma segura entre si e com a internet.'
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2020
ms.author: kumud
ms.openlocfilehash: cc3ba3a0519400368e0cbfec7abe2d9bd1731b34
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217639"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Início Rápido: criar uma rede virtual com o Portal do Azure

Neste arranque rápido, aprende-se a criar uma rede virtual utilizando o portal Azure. Coloca-se duas máquinas virtuais (VMs). Em seguida, você comunica de forma segura entre VMs e conecta-se a VMs a partir da internet. Uma rede virtual é o bloco de construção fundamental para a sua rede privada em Azure. Permite que os recursos da Azure, como os VMs, se comuniquem de forma segura entre si e com a internet.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No menu do portal do Azure, selecione **Criar um recurso**. A partir do Mercado Azure, **selecione rede** Virtual de Rede  >  .

1. Na **Criar rede virtual,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | **Selecione Criar novo,** insira *o myResourceGroup* e, em seguida, selecione **OK**. |
    | Name | Insira *a minha RedeTual*. |
    | A localização | Selecione **East US**.|

1. Selecione **Seguinte: Endereços IP**, e para **o espaço de endereço IPv4**, insira *10.1.0.0/16*.

1. **Selecione Adicionar a sub-rede**, em seguida, insira *myVirtualSubnet* para **o nome da sub-rede** e *10.1.0.0/24* para **intervalo de endereços sub-rede**.

1. Selecione **Adicionar** e, em seguida, selecione **Rever + criar**. Deixe o resto como padrão e **selecione Criar**.

1. In **Create virtual network,** selecione **Create**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual:

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. No menu do portal do Azure, selecione **Criar um recurso**.

1. A partir do Azure Marketplace, **selecione Compute**  >  **Windows Server 2019 Datacenter**. Selecione **Criar**.

1. Na **Criar uma máquina virtual - Básicos, insira** ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou este grupo de recursos na secção anterior. |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira *o myVm1*. |
    | Region | Selecione **East US**. |
    | Opções de disponibilidade | Incumprimento de **não necessidade de redundância de infraestrutura**. |
    | Imagem | Padrão para **o Centro de Dados 2019 do Windows Server 2019**. |
    | Tamanho | Predefinição para **Standard DS1 v2**. |
    | **Conta de administrador** |  |
    | Nome de utilizador | Insira um nome de utilizador à sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Palavra-passe | Reintrodua senha. |
    | **Regras portuárias de entrada** |  |
    | Portas de entrada públicas | Selecione **Deixe as portas selecionadas.** |
    | Selecione as portas de entrada | Insira *HTTP (80)* e *RDP (3389)*. |
    | **Poupe dinheiro** |  |
    | Já tem uma licença do Windows? | Predefinição para **Nº**. |

1. Selecione **Seguinte: Discos**.

1. Em **Criar uma máquina virtual - Discos**, mantenha as predefinições e selecione **Seguinte: Networking**.

1. Na **Criação de uma máquina virtual - Networking,** selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Rede virtual | Predefinição da **minha Rede Virtual**. |
    | Sub-rede | Predefinição **da myVirtualSubnet (10.1.0.0/24)**. |
    | IP público | Predefinição para **(novo) myVm-ip**. |
    | Grupo de segurança de rede NIC | Predefinição ao **Básico**. |
    | Portas de entrada públicas | Predefinição para **Permitir portas selecionadas**. |
    | Selecione as portas de entrada | Predefinição para **HTTP** e **RDP**.

1. Selecione **Seguinte: Gestão**.

1. In **Create a virtual machine - Management**, for **Diagnostics storage account**, select Create **New**.

1. Na **conta de armazenamento,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *a conta myvmstorage.* Se este nome for tomado, crie um nome único.|
    | Tipo de conta | Predefinição ao **Armazenamento (finalidade geral v1)**. |
    | Desempenho | Predefinição da **Norma**. |
    | Replicação | Predefinição do **armazenamento localmente redundante (LRS)**. |

1. Selecione **OK,** em seguida, **selecione Review + create**. Acedeu à página **Rever + criar**, onde o Azure valida a sua configuração.

1. Quando vir a mensagem **A validação passou**, selecione **Criar**.

### <a name="create-the-second-vm"></a>Criar a segunda VM

Repita o procedimento na secção anterior para criar outra máquina virtual.

> [!IMPORTANT]
> Para o nome da **máquina Virtual,** insira *o myVm2*.
>
> Para **a conta de armazenamento de diagnóstico,** certifique-se de selecionar a contagem de **myvmstorageac,** em vez de criar uma.

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Depois de criar *o myVm1,* ligue-se à internet.

1. No portal Azure, procure e selecione *myVm1*.

1. Selecione **Connect**, em **seguida, RDP**.

    ![Ligar a uma máquina virtual](./media/quick-create-portal/connect-to-virtual-machine.png)

    A página **'Ligar'** abre.

1. Selecione **Transferir Ficheiro RDP**. O Azure cria um ficheiro Remote Desktop Protocol *(.rdp)* e transfere-o para o computador.

1. Abra o ficheiro RDP. Se lhe for pedido, selecione **Ligar**.

1. Introduza o nome de utilizador e a palavra-passe que especificou ao criar o VM.

    > [!NOTE]
    > Poderá ter de selecionar **Mais escolhas**  >  **Utilize uma conta diferente**, para especificar as credenciais que introduziu quando criou o VM.

1. Selecione **OK**.

1. Pode receber um aviso de certificado quando iniciar sação. Se recebeu um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Assim que o ambiente de trabalho em VM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.

## <a name="communicate-between-vms"></a>Comunicar entre VMs

1. No Ambiente de Trabalho Remoto do *myVm1,* abra o PowerShell.

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

    Falha, `ping` porque `ping` utiliza o Protocolo de Mensagem de Controlo de Internet (ICMP). Por padrão, o ICMP não é permitido através da firewall do Windows.

1. Para permitir que *o myVm2* ping *myVm1* em um passo posterior, insira este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Este comando permite a entrada do ICMP através da firewall do Windows:

1. Feche a ligação de ambiente de trabalho remoto à *myVm1*.

1. Conclua novamente os passos em [Ligar a uma VM a partir da Internet](#connect-to-a-vm-from-the-internet), mas ligue à *myVm2*.

1. Numa linha de comandos, introduza `ping myvm1`.

    Receberá uma mensagem destas:

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

Quando terminar de usar a rede virtual e os VMs, elimine o grupo de recursos e todos os recursos que contém:

1. Procure e selecione *myResourceGroup*.

1. Selecione **Eliminar grupo de recursos**.

1. Introduza *o myResourceGroup* para **type the RESOURCE GROUP NAME** e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as definições de rede virtual, consulte [Criar, alterar ou eliminar uma rede virtual.](manage-virtual-network.md)

Por predefinição, o Azure permite uma comunicação segura entre VMs. O Azure só permite ligações remotas de ambiente de trabalho ao Windows VMs a partir da internet. Para saber mais sobre tipos de comunicações de rede VM, consulte [o tráfego da rede Filter](tutorial-filter-network-traffic.md).

> [!NOTE] 
> Os serviços da Azure custam dinheiro. A Azure Cost Management ajuda-o a definir orçamentos e a configurar alertas para manter os gastos sob controlo. Analise, gerencie e otimize os seus custos Azure com a Gestão de Custos. Para saber mais, consulte o [quickstart na análise dos seus custos.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)