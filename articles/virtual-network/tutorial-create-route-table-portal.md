---
title: Rotear tráfego de rede-tutorial-portal do Azure
titlesuffix: Azure Virtual Network
description: Neste tutorial, saiba como encaminhar o tráfego de rede com uma tabela de rotas através do portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2019
ms.author: kumud
ms.openlocfilehash: 96b6788e48b845ef7f0add11767eb36b47cac36b
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775286"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Tutorial: Encaminhar o tráfego de rede com uma tabela de rotas através do portal do Azure

Por padrão, o Azure roteia o tráfego entre todas as sub-redes em uma rede virtual. Pode criar as sua próprias rotas para substituir o encaminhamento predefinido do Azure. A capacidade de criar rotas personalizadas é útil se, por exemplo, pretender encaminhar o tráfego entre sub-redes através de uma aplicação virtual de rede (NVA). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma tabela de rotas
> * Criar uma rota
> * Criar uma rede virtual com várias sub-redes
> * Associar uma tabela de rotas a uma sub-rede
> * Criar uma NVA que encaminha o tráfego
> * Implementar máquinas virtuais (VMs) em sub-redes diferentes
> * Encaminhar o tráfego de uma sub-rede para outra através de uma NVA

Se preferir, você pode concluir este tutorial usando o [CLI do Azure](tutorial-create-route-table-cli.md) ou [Azure PowerShell](tutorial-create-route-table-powershell.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

1. No menu portal do Azure, selecione **criar um recurso**.
2. Na caixa de pesquisa, introduza a *tabela Rota.* Quando a **tabela Rota** aparecer nos resultados da pesquisa, selecione-a.
3. Na página da **tabela Rota,** selecione **Criar**.
4. Em **criar tabela de rotas**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *myRouteTablePublic*. |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **criar novo**, insira *MyResource*e selecione *OK*. |
    | Localização | Selecione **E.U.A. Leste**.
    | Propagação de rota de gateway de rede virtual | Deixe o padrão **habilitado**. |
5. Selecione **Criar**.

## <a name="create-a-route"></a>Criar uma rota

1. Na barra de pesquisa do portal, insira *myRouteTablePublic*.

1. Quando **myRouteTablePublic** aparecer nos resultados da pesquisa, selecione-a.

1. Em **myRouteTablePublic** em **configurações**, selecione **rotas** >  **+ Adicionar**.

    ![Adicionar rota](./media/tutorial-create-route-table-portal/add-route.png)

1. Em **Adicionar rota**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome da rota | Insira *ToPrivateSubnet*. |
    | Prefixo de endereço | Insira *10.0.1.0/24*. |
    | Tipo de salto seguinte | Selecione **Aplicação virtual**. |
    | Endereço do próximo salto | Enter *10.0.2.4*. |

1. Selecione **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Antes de poder associar uma tabela de rotas a uma sub-rede, você precisa criar uma rede virtual e uma sub-rede.

### <a name="create-a-virtual-network"></a>Criar rede virtual

1. No lado superior esquerdo da tela, selecione **criar um recurso** > **rede** > **rede virtual**.

1. Em **criar rede virtual**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *myVirtualNetwork*. |
    | Espaço de endereços | Digite *10.0.0.0/16*. |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione ***selecionar existente*** > **myresourceobject**. |
    | Localização | Deixe o **leste dos EUA**padrão. |
    | Nome da sub-rede | Insira *público*. |
    | Sub-rede - Intervalo de endereços | Digite *10.0.0.0/24*. |

1. Deixe o restante dos padrões e selecione **criar**.

### <a name="add-subnets-to-the-virtual-network"></a>Adicionar sub-redes à rede virtual

1. Na barra de pesquisa do portal, insira *myVirtualNetwork*.

1. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione a mesma.

1. Em **myVirtualNetwork**, em **configurações**, selecione sub- **redes** >  **+ sub-rede**.

    ![Adicionar sub-rede](./media/tutorial-create-route-table-portal/add-subnet.png)

1. Em **Adicionar sub-rede**, insira estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *privado*. |
    | Espaço de endereços | Insira *10.0.1.0/24*. |

1. Deixe o resto das predefinições e selecione **OK**.

1. Selecione **+ sub-rede** novamente. Desta vez, insira estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira a *DMZ*. |
    | Espaço de endereços | Insira *10.0.2.0/24*. |

1. Como na última vez, deixe o restante dos padrões e selecione **OK**.

    O Azure mostra as três sub-redes: **pública**, **privada**e **DMZ**.

### <a name="associate-myroutetablepublic-to-your-public-subnet"></a>Associar o myRouteTablePublic à sua sub-rede pública

1. Selecione **Public**.

1. Em **público**, selecione **tabela de rotas** > **MyRouteTablePublic** > **salvar**.

    ![Associar a tabela de rotas](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="create-an-nva"></a>Criar uma NVA

NVAs são VMs que ajudam com funções de rede como roteamento e otimização de firewall. Você pode selecionar um sistema operacional diferente, se desejar. Este tutorial pressupõe que você esteja usando o **Windows Server 2016 datacenter**.

1. No lado superior esquerdo da tela, selecione **criar um recurso** > **computação** > **Windows Server 2016 datacenter**.

1. Em **criar uma máquina virtual-noções básicas**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **Myresourceattribute**. |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVmNva*. |
    | Região | Selecione **E.U.A. Leste**. |
    | Opções de disponibilidade | Deixe o padrão **nenhuma redundância de infraestrutura necessária**. |
    | Imagem | Deixe o **Windows Server 2016 datacenter**padrão. |
    | Tamanho | Deixe o **DS1 v2**padrão. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de utilizador | Introduza um nome de utilizador à sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar palavra-passe | Digite a senha novamente. |
    | **REGRAS DE PORTA DE ENTRADA** |  |
    | Portos de entrada pública | Selecione **Nenhum**.
    | **ECONOMIZE DINHEIRO** |  |
    | Já tem licença do Windows? | Deixe o padrão **não**. |

1. Selecione **Avançar: discos**.

1. Em **criar uma máquina virtual-discos**, selecione as configurações que são certas para suas necessidades.

1. Selecione **Avançar: rede**.

1. Em **criar uma máquina virtual-rede**, selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe o **myVirtualNetwork**padrão. |
    | Subrede | Selecione **DMZ (10.0.2.0/24)** . |
    | IP público | Selecione **Nenhum**. Você não precisa de um endereço IP público. A VM não se conectará pela Internet.|

1. Deixe o restante dos padrões e selecione **Avançar: gerenciamento**.

1. Em **criar uma máquina virtual-gerenciamento**, para **conta de armazenamento de diagnóstico**, selecione **criar novo**.

1. Em **criar conta de armazenamento**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *mynvastorageaccount*. |
    | Tipo de conta | Deixe o **armazenamento padrão (uso geral v1)** . |
    | Desempenho | Deixe o **padrão**padrão. |
    | Replicação | Deixe o **armazenamento padrão com redundância local (LRS)** .

1. Selecione **OK**

1. Selecione **Rever + criar**. Você é levado para a página **revisar + criar** e o Azure valida sua configuração.

1. Quando você vir essa **validação aprovada**, selecione **criar**.

    A criação da VM demora alguns minutos. Não continue indo até que o Azure termine de criar a VM. A página **sua implantação está em andamento** mostrará os detalhes da implantação.

1. Quando sua VM estiver pronta, selecione **ir para o recurso**.

## <a name="turn-on-ip-forwarding"></a>Ativar encaminhamento de IP

Ative o encaminhamento de IP para *myVmNva*. Quando o Azure envia tráfego de rede para *myVmNva*, se o tráfego for destinado a um endereço IP diferente, o encaminhamento de IP enviará o tráfego para o local correto.

1. Em **myVmNva**, em **configurações**, selecione **rede**.

1. Selecione **myvmnva123**. Essa é a interface de rede criada pelo Azure para sua VM. Ele terá uma cadeia de números para torná-lo exclusivo para você.

    ![Redes de VM](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. Em **configurações**, selecione **configurações de IP**.

1. Em **myvmnva123-configurações de IP**, para **encaminhamento de IP**, selecione **habilitado** e, em seguida, selecione **salvar**.

    ![Ativar o reencaminhamento de IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Criar máquinas virtuais públicas e privadas

Crie uma VM pública e uma VM privada na rede virtual. Posteriormente, você os usará para ver que o Azure roteia o tráfego de sub-rede *pública* para a sub-rede *privada* por meio do NVA.

Conclua as etapas 1-12 de [criar um NVA](#create-an-nva). Use a maioria das mesmas configurações. Esses valores são aqueles que precisam ser diferentes:

| Definição | Valor |
| ------- | ----- |
| **VM PÚBLICA** | |
| BÁSICOS |  |
| Nome da máquina virtual | Insira *myVmPublic*. |
| NETWORKING | |
| Subrede | Selecione **público (10.0.0.0/24)** . |
| Endereço IP público | Aceite o padrão. |
| Portos de entrada pública | Selecione **permitir portas selecionadas**. |
| Selecione portas de entrada | Selecione **http** e **RDP**. |
| GESTÃO | |
| Conta de armazenamento de diagnóstico | Deixe o **mynvastorageaccount**padrão. |
| **VM PRIVADA** | |
| BÁSICOS |  |
| Nome da máquina virtual | Insira *myVmPrivate*. |
| NETWORKING | |
| Subrede | Selecione **privado (10.0.1.0/24)** . |
| Endereço IP público | Aceite o padrão. |
| Portos de entrada pública | Selecione **permitir portas selecionadas**. |
| Selecione portas de entrada | Selecione **http** e **RDP**. |
| GESTÃO | |
| Conta de armazenamento de diagnóstico | Deixe o **mynvastorageaccount**padrão. |

Pode criar a VM *myVmPrivate* enquanto o Azure cria a VM *myVmPublic*. Não continue com o restante das etapas até que o Azure termine de criar ambas as VMs.

## <a name="route-traffic-through-an-nva"></a>Encaminhar o tráfego através de uma NVA

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Entrar no myVmPrivate sobre a área de trabalho remota

1. Na barra de pesquisa do portal, insira *myVmPrivate*.

1. Quando a VM **myVmPrivate** aparecer nos resultados da pesquisa, selecione-a.

1. Selecione **conectar** para criar uma conexão de área de trabalho remota para a VM *myVmPrivate* .

1. Em **conectar à máquina virtual**, selecione **baixar arquivo RDP**. O Azure cria um arquivo protocolo RDP ( *. rdp*) e o baixa em seu computador.

1. Abra o arquivo *. rdp* baixado.

    1. Se lhe for pedido, selecione **Ligar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM privada.

    1. Talvez seja necessário selecionar **mais opções** > **usar uma conta diferente**, para usar as credenciais de VM privada.

1. Selecione **OK**.

    Você pode receber um aviso de certificado durante o processo de entrada.

1. Selecione **Sim** para se conectar à VM.

### <a name="enable-icmp-through-the-windows-firewall"></a>Habilitar o ICMP por meio do firewall do Windows

Em uma etapa posterior, você usará a ferramenta rastrear rota para testar o roteamento. A rota de rastreamento usa o protocolo ICMP, que o Firewall do Windows nega por padrão. Habilite o ICMP por meio do firewall do Windows.

1. No Área de Trabalho Remota do *myVmPrivate*, abra o PowerShell.

1. Digite este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Você está usando a rota de rastreamento para testar o roteamento neste tutorial. Para ambientes de produção, não é recomendável permitir o ICMP por meio do firewall do Windows.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Ativar o encaminhamento de IP no myVmNva

Você [ativou o encaminhamento de IP](#turn-on-ip-forwarding) para a interface de rede da VM usando o Azure. O sistema operacional da VM também precisa encaminhar o tráfego de rede. Ative o encaminhamento de IP para o sistema operacional da VM *myVmNva* com estes comandos.

1. Em um prompt de comando na VM *myVmPrivate* , abra uma área de trabalho remota para a VM *myVmNva* :

    ```cmd
    mstsc /v:myvmnva
    ```

1. No PowerShell no *myVmNva*, digite este comando para ativar o encaminhamento de IP:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Reinicie a VM *myVmNva* . Na barra de tarefas, selecione o **botão iniciar** > **botão de energia**, **outro (planejado)**  > **continuar**.

    Isso também desconecta a sessão da área de trabalho remota.

1. Depois que a VM *myVmNva* for reiniciada, crie uma sessão de área de trabalho remota para a VM *myVmPublic* . Enquanto ainda estiver conectado à VM *myVmPrivate* , abra um prompt de comando e execute este comando:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. No Área de Trabalho Remota do *myVmPublic*, abra o PowerShell.

1. Habilite o ICMP por meio do firewall do Windows digitando este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Testar o roteamento do tráfego de rede

Primeiro, vamos testar o roteamento do tráfego de rede da VM *myVmPublic* para a VM *myVmPrivate* .

1. No PowerShell na VM *myVmPublic* , digite este comando:

    ```powershell
    tracert myVmPrivate
    ```

    A resposta é semelhante a este exemplo:

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    Você pode ver que o primeiro salto é 10.0.2.4. É o endereço IP privado NVA's. O segundo salto é para o endereço IP privado da VM *myVmPrivate* : 10.0.1.4. Anteriormente, você adicionou a rota à tabela de rotas *myRouteTablePublic* e a associou à sub-rede *pública* . Como resultado, o Azure enviou o tráfego por meio do NVA e não diretamente para a sub-rede *privada* .

1. Feche a sessão de ambiente de trabalho remoto para a VM *myVmPublic*, o que mantém a ligação à VM *myVmPrivate* ativa.

1. Em um prompt de comando na VM *myVmPrivate* , digite este comando:

    ```cmd
    tracert myVmPublic
    ```

    Ele testa o roteamento do tráfego de rede da VM *myVmPrivate* para a VM *myVmPublic* . A resposta é semelhante a este exemplo:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Você pode ver o tráfego de rotas do Azure diretamente da VM *myVmPrivate* para a VM *myVmPublic* . Por predefinição, o Azure encaminha o tráfego diretamente entre sub-redes.

1. Feche a sessão de ambiente de trabalho remoto para a VM *myVmPrivate*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos e todos os recursos que ele tem:

1. Na barra de pesquisa do portal, insira *MyResource*.

1. Quando vir o **myResourceGroup** nos resultados da pesquisa, selecione-o.

1. Selecione **Eliminar grupo de recursos**.

1. Introduza *myResourceGroup* em **ESCREVER O NOME DO GRUPO DE RECURSOS:** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma tabela de rotas e associou-a a uma sub-rede. Criou uma NVA simples que encaminhou o tráfego de uma sub-rede pública para uma privada. Agora que você sabe como fazer isso, você pode implantar diferentes NVAs pré-configuradas no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Eles executam muitas funções de rede que você encontrará úteis. Para saber mais sobre o encaminhamento, veja [Descrição geral do encaminhamento](virtual-networks-udr-overview.md) e [Manage a route table](manage-route-table.md) (Gerir uma tabela de rotas).

Embora você possa implantar muitos recursos do Azure em uma rede virtual, o Azure não pode implantar recursos para alguns serviços de PaaS em uma rede virtual. É possível restringir o acesso aos recursos de alguns serviços de PaaS do Azure. No entanto, a restrição deve ser apenas o tráfego de uma sub-rede de rede virtual. Para saber como restringir o acesso de rede a recursos de PaaS do Azure, avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Restrict network access to PaaS resources](tutorial-restrict-network-access-to-resources.md) (Restringir o acesso de rede a recursos de PaaS)
