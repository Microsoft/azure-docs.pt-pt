---
title: Criar uma rede virtual - quickstart - Portal Azure
titlesuffix: Azure Virtual Network
description: Neste início rápido, vai aprender a criar uma rede virtual com o Portal do Azure. Uma rede virtual permite que os recursos do Azure, como máquinas virtuais, comuniquem-se de forma segura uns com os outros e com a internet
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393126"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Início Rápido: criar uma rede virtual com o Portal do Azure

Uma rede virtual é o bloco de construção fundamental para a sua rede privada em Azure. Permite que os recursos do Azure, como máquinas virtuais (VMs), se comuniquem de forma segura entre si e com a internet. Neste Quickstart, você vai aprender a criar uma rede virtual usando o portal Azure. Em seguida, pode implantar dois VMs na rede virtual, comunicar de forma segura entre os dois VMs e ligar-se aos VMs a partir da internet.


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. A partir do menu do portal Azure, selecione **Criar um recurso**.

2. A partir do Mercado Azure, selecione **Networking** > **Rede Virtual.**

3. Na **Create rede virtual,** introduza ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Introduza *a minha Rede Virtual*. |
    | Espaço de endereços | Insira *10.1.0.0/16*. |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | Selecione **Criar novo,** introduza *o meu Grupo de Recursos*e, em seguida, selecione **OK**. |
    | Localização | Selecione **E.U.A. Leste**.|
    | Subnet - Nome | Insira *a minha VirtualSubnet*. |
    | Sub-rede - Intervalo de endereços | Insira *10.1.0.0/24*. |

4. Deixe o resto como padrão e selecione **Criar**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual:

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. A partir do menu do portal Azure, selecione **Criar um recurso**.

2. A partir do Azure Marketplace, **selecione Compute** > **Windows Server 2019 Datacenter**.

3. Em **Criar uma máquina virtual - Básicos,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior. |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVm1*. |
    | Região | Selecione **E.U.A. Leste**. |
    | Opções de disponibilidade | Deixar o padrão Não é necessário um despedimento de **infraestrutura.** |
    | Imagem | Deixe o Datacenter predefinido **do Windows Server 2019**. |
    | Tamanho | Deixe o **Padrão DS1 v2**padrão padrão padrão . |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de utilizador | Introduza um nome de utilizador à sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar palavra-passe | Reintroduza a senha. |
    | **REGRAS PORTUÁRIAS DE ENTRADA** |  |
    | Portos de entrada pública | Deixe o padrão **Nenhum**. |
    | **POUPE DINHEIRO** |  |
    | Já tem licença do Windows? | Deixe o padrão **Nº**. |

4. Selecione **Seguinte: Discos**.

5. Em **Criar uma máquina virtual - Discos,** deixe as predefinições e selecione Seguinte : **Networking**.

6. Em **Criar uma máquina virtual - Networking,** selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe a **minha Rede Virtual**por defeito. |
    | Subrede | Deixe a **myVirtualSubnet por defeito (10.1.0.0/24)** . |
    | IP público | Deixe o **myVm-ip padrão (novo)** |
    | Portos de entrada pública | **Selecione Permitir portas selecionadas**. |
    | Selecione portas de entrada | Selecione **HTTP** e **RDP**.

7. Selecione **Seguinte : Gestão**.

8. Em **Criar uma máquina virtual - Gestão,** para conta de armazenamento de **diagnósticos,** selecione **Criar Novo**.

9. Na **conta de armazenamento Criar,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira a *conta myvmstorage.* Se este nome for tomado, crie um nome único.|
    | Tipo de conta | Deixe o Armazenamento predefinido **(finalidade geral v1)** . |
    | Desempenho | Deixe o **Padrão**padrão . |
    | Replicação | Deixe o armazenamento por defeito **localmente redundante (LRS)** . |

10. Selecione **OK**

11. Selecione **Rever + criar**. É levado para o **Review + criar** página onde o Azure valida a sua configuração.

12. Quando vir a **mensagem de validação passada,** selecione **Criar**.

### <a name="create-the-second-vm"></a>Criar a segunda VM

1. Etapas completas 1 e 9 de cima.

    > [!NOTE]
    > No passo 2, para o nome da **máquina Virtual,** introduza *myVm2*.
    >
    > No passo 7, para a conta de **armazenamento de diagnóstico,** certifique-se de selecionar a **conta myvmstorage**.

2. Selecione **Rever + criar**. É levado para a página **Review + criar** e o Azure valida a sua configuração.

3. Quando vir a **mensagem de validação passada,** selecione **Criar**.

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Depois de criar o *myVm1,* ligue-se à internet.

1. Na barra de pesquisa do portal, entre no *myVm1*.

2. Selecione o botão **Ligar**.

    ![Ligar a uma máquina virtual](./media/quick-create-portal/connect-to-virtual-machine.png)

    Depois de selecionar o botão **Ligar,** **ligue-se à máquina virtual.**

3. Selecione **Download RDP File**. O Azure cria um ficheiro Remote Desktop *(.rdp)* e transfere-o para o computador.

4. Abra o ficheiro *.rdp* descarregado.

    1. Se lhe for pedido, selecione **Ligar**.

    2. Introduza o nome de utilizador e a palavra-passe especificado ao criar o VM.

        > [!NOTE]
        > Poderá ter de selecionar **Mais escolhas** > **Utilize uma conta diferente,** para especificar as credenciais que inseriu quando criou o VM.

5. Selecione **OK**.

6. Poderá receber um aviso de certificado durante o sinal em processo. Se receber um aviso de certificado, selecione **Sim** ou **Continue**.

7. Assim que o ambiente de trabalho vM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.

## <a name="communicate-between-vms"></a>Comunicar entre VMs

1. No Ambiente de Trabalho Remoto do *myVm1,* abra powerShell.

2. Introduza `ping myVm2`.

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

    O `ping` falha, porque `ping` utiliza o Protocolo de Mensagem de Controlo de Internet (ICMP). Por padrão, o ICMP não é permitido através da firewall do Windows.

3. Para permitir que *o myVm2* ping *myVm1* em um passo posterior, insira este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Este comando permite a entrada do ICMP através da firewall do Windows:

4. Feche a ligação de ambiente de trabalho remoto à *myVm1*.

5. Conclua novamente os passos em [Ligar a uma VM a partir da Internet](#connect-to-a-vm-from-the-internet), mas ligue à *myVm2*.

6. Numa linha de comandos, introduza `ping myvm1`.

    Vais ter de volta algo como esta mensagem:

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

    Recebe respostas do *myVm1,* porque permitiu que o ICMP passasse pela firewall do Windows no *myVm1* VM no passo 3.

7. Feche a ligação de ambiente de trabalho remoto à *myVm2*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar a utilização da rede virtual e dos VMs, elimine o grupo de recursos e todos os recursos que contém:

1. Introduza o *myResourceGroup* na caixa **de pesquisa** na parte superior do portal e selecione **myResourceGroup** a partir dos resultados da pesquisa.

2. Selecione **Eliminar grupo de recursos**.

3. Introduza *o myResourceGroup* para **ESCREVER O NOME DE GRUPO DE RECURSOS** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste Quickstart, criou uma rede virtual padrão e dois VMs. Ligou-se a um VM da internet e comunicou-se de forma segura entre os dois VMs. Para saber mais sobre as definições de rede virtual, veja [Gerir uma rede virtual](manage-virtual-network.md).

Por padrão, o Azure permite uma comunicação segura sem restrições entre VMs. Inversamente, apenas permite ligações remotas de ambiente de trabalho para VMs do Windows a partir da internet. Para saber mais sobre a configuração de diferentes tipos de comunicações de rede VM, vá ao tutorial de tráfego da [rede Filter.](tutorial-filter-network-traffic.md)
