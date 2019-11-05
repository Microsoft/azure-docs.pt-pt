---
title: Criar uma rede virtual-início rápido-portal do Azure
titlesuffix: Azure Virtual Network
description: Neste início rápido, vai aprender a criar uma rede virtual com o Portal do Azure. Uma rede virtual permite que recursos do Azure, como máquinas virtuais, se comuniquem com segurança entre si e com a Internet
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
ms.openlocfilehash: d8e95f9c345a943eb458800b852640e3f1fde907
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488494"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Início Rápido: criar uma rede virtual com o Portal do Azure

Uma rede virtual é o bloco de construção fundamental para sua rede privada no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), comuniquem-se com segurança entre si e com a Internet. Neste guia de início rápido, você aprenderá a criar uma rede virtual usando o portal do Azure. Em seguida, você pode implantar duas VMs na rede virtual, comunicar-se com segurança entre as duas VMs e conectar-se às VMs da Internet.


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Iniciar sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No menu portal do Azure, selecione **criar um recurso**.

2. No Azure Marketplace **, escolha rede** > **rede virtual**.

3. Em **criar rede virtual**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *myVirtualNetwork*. |
    | Espaço de endereços | Insira *10.1.0.0/16*. |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | Selecione **criar novo**, insira *MyResource*e, em seguida, selecione **OK**. |
    | Localização | Selecione **E.U.A. Leste**.|
    | Nome da sub-rede | Insira *myVirtualSubnet*. |
    | Sub-rede - Intervalo de endereços | Insira *10.1.0.0/24*. |

4. Deixe o restante como padrão e selecione **criar**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual:

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. No menu portal do Azure, selecione **criar um recurso**.

2. No Azure Marketplace, selecione **computação** > **Windows Server 2019 datacenter**.

3. Em **criar uma máquina virtual-noções básicas**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **Myresourceattribute**. Você criou isso na seção anterior. |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVm1*. |
    | Região | Selecione **E.U.A. Leste**. |
    | Opções de disponibilidade | Deixe o padrão **nenhuma redundância de infraestrutura necessária**. |
    | Imagem | Deixe o **Windows Server 2019 datacenter**padrão. |
    | Tamanho | Deixe o **DS1 v2**padrão. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de utilizador | Insira um nome de usuário de sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar senha | Digite a senha novamente. |
    | **REGRAS DE PORTA DE ENTRADA** |  |
    | Portas de entrada públicas | Deixe o padrão **nenhum**. |
    | **ECONOMIZE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o padrão **não**. |

4. Selecione **Avançar: discos**.

5. Em **criar uma máquina virtual-discos**, deixe os padrões e selecione **Avançar: rede**.

6. Em **criar uma máquina virtual-rede**, selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe o **myVirtualNetwork**padrão. |
    | Subrede | Deixe o **myVirtualSubnet padrão (10.1.0.0/24)** . |
    | IP público | Deixe o padrão **(novo) myVm-IP**. |
    | Portas de entrada públicas | Selecione **permitir portas selecionadas**. |
    | Selecionar portas de entrada | Selecione **http** e **RDP**.

7. Selecione **Avançar: gerenciamento**.

8. Em **criar uma máquina virtual-gerenciamento**, para **conta de armazenamento de diagnóstico**, selecione **criar novo**.

9. Em **criar conta de armazenamento**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *myvmstorageaccount*. Se esse nome for obtido, crie um nome exclusivo.|
    | Tipo de conta | Deixe o **armazenamento padrão (uso geral v1)** . |
    | Desempenho | Deixe o **padrão**padrão. |
    | Replicação | Deixe o **armazenamento padrão com redundância local (LRS)** . |

10. Selecione **OK**

11. Selecione **Rever + criar**. Você é levado para a página **revisar + criar** , na qual o Azure valida sua configuração.

12. Quando você vir a mensagem **validação aprovada** , selecione **criar**.

### <a name="create-the-second-vm"></a>Criar a segunda VM

1. Conclua as etapas 1 e 9 acima.

    > [!NOTE]
    > Na etapa 2, para o **nome da máquina virtual**, digite *myVm2*.
    >
    > Na etapa 7, para **conta de armazenamento de diagnóstico**, certifique-se de selecionar **myvmstorageaccount**.

2. Selecione **Rever + criar**. Você é levado para a página **revisar + criar** e o Azure valida sua configuração.

3. Quando você vir a mensagem **validação aprovada** , selecione **criar**.

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Depois de criar o *myVm1*, conecte-se à Internet.

1. Na barra de pesquisa do portal, insira *myVm1*.

2. Selecione o botão **Ligar**.

    ![Ligar a uma máquina virtual](./media/quick-create-portal/connect-to-virtual-machine.png)

    Depois de selecionar o botão **conectar** , **Conecte-se à máquina virtual** é aberto.

3. Selecione **baixar arquivo RDP**. O Azure cria um arquivo protocolo RDP ( *. rdp*) e o baixa em seu computador.

4. Abra o arquivo *. rdp* baixado.

    1. Se lhe for pedido, selecione **Ligar**.

    2. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Talvez seja necessário selecionar **mais escolhas** > **usar uma conta diferente**, para especificar as credenciais inseridas quando você criou a VM.

5. Selecione **OK**.

6. Você pode receber um aviso de certificado durante o processo de entrada. Se você receber um aviso de certificado, selecione **Sim** ou **continuar**.

7. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para a área de trabalho local.

## <a name="communicate-between-vms"></a>Comunicar entre VMs

1. No Área de Trabalho Remota do *myVm1*, abra o PowerShell.

2. Introduza `ping myVm2`.

    Você receberá uma mensagem semelhante a esta:

    ```powershell
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    A `ping` falha, porque `ping` usa o protocolo ICMP. Por padrão, o ICMP não é permitido por meio do firewall do Windows.

3. Para permitir que o *myVm2* execute ping em *myVm1* em uma etapa posterior, digite este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Esse comando permite o ICMP de entrada por meio do firewall do Windows:

4. Feche a ligação de ambiente de trabalho remoto à *myVm1*.

5. Conclua novamente os passos em [Ligar a uma VM a partir da Internet](#connect-to-a-vm-from-the-internet), mas ligue à *myVm2*.

6. Numa linha de comandos, introduza `ping myvm1`.

    Você receberá algo parecido com esta mensagem:

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

    Você recebe respostas de *myVm1*, porque você permitiu o ICMP por meio do firewall do Windows na VM *myVm1* na etapa 3.

7. Feche a ligação de ambiente de trabalho remoto à *myVm2*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar de usar a rede virtual e as VMs, exclua o grupo de recursos e todos os recursos que ele contém:

1. Insira *MyResource* The na caixa de **pesquisa** na parte superior do portal e selecione **MyResource** Bedos resultados da pesquisa.

2. Selecione **Eliminar grupo de recursos**.

3. Insira *MyResource* Group para **digite o nome do grupo de recursos** e selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou uma rede virtual padrão e duas VMs. Você se conectou a uma VM da Internet e se comunica com segurança entre as duas VMs. Para saber mais sobre as definições de rede virtual, veja [Gerir uma rede virtual](manage-virtual-network.md).

Por padrão, o Azure permite uma comunicação segura irrestrita entre as VMs. Por outro lado, ele permite apenas conexões de área de trabalho remota de entrada para VMs do Windows da Internet. Para saber mais sobre como configurar diferentes tipos de comunicações de rede de VM, vá para o tutorial [Filtrar tráfego de rede](tutorial-filter-network-traffic.md) .
