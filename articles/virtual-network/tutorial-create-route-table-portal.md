---
title: Tráfego de rede de rotas - tutorial - Portal Azure
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
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 854bf2d1ed2155290c2ecebd17695049fdd0c8bb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81314289"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Tutorial: Encaminhar o tráfego de rede com uma tabela de rotas através do portal do Azure

O Azure encaminha o tráfego entre todas as subredes dentro de uma rede virtual, por padrão. Pode criar as sua próprias rotas para substituir o encaminhamento predefinido do Azure. As rotas personalizadas são úteis quando, por exemplo, pretende encaminhar o tráfego entre subredes através de um aparelho virtual de rede (NVA). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma NVA que encaminha o tráfego
> * Criar uma tabela de rotas
> * Criar uma rota
> * Associar uma tabela de rotas a uma sub-rede
> * Implementar máquinas virtuais (VMs) em sub-redes diferentes
> * Encaminhar o tráfego de uma sub-rede para outra através de uma NVA

Este tutorial utiliza o [portal Azure.](https://portal.azure.com) Também pode utilizar [o Azure CLI](tutorial-create-route-table-cli.md) ou [o Azure PowerShell.](tutorial-create-route-table-powershell.md)

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-nva"></a>Criar uma NVA

Os aparelhos virtuais de rede (NVAs) são máquinas virtuais que ajudam nas funções da rede, tais como o encaminhamento e a otimização da firewall. Este tutorial assume que está a utilizar o **Windows Server 2016 Datacenter**. Pode selecionar um sistema operativo diferente, se quiser.

1. No menu do [portal Azure](https://portal.azure.com) ou na página **Inicial,** selecione **Criar um recurso**.

1. Escolha o Datacenter do Servidor do Windows de **Segurança** > **2016**.

    ![Windows Server 2016 Datacenter, Criar um VM, portal Azure](./media/tutorial-create-route-table-portal/vm-ws2016-datacenter.png)

1. Na página **Criar uma máquina virtual,** no **Basics,** introduza ou selecione esta informação:

    | Section | Definição | Ação |
    | ------- | ------- | ----- |
    | **Detalhes do projeto** | Subscrição | Escolha a sua subscrição. |
    | | Grupo de recursos | Selecione **Criar novo,** introduza *o meu ResourceGroup,* e selecione **OK**. |
    | **Detalhes da instância** | Nome da máquina virtual | Insira *myVmNva.* |
    | | Região | Escolha **(EUA) Leste dos EUA**. |
    | | Opções de disponibilidade | Escolha não é necessário um despedimento de **infraestrutura.** |
    | | Imagem | Escolha **o Datacenter do Windows Server 2016**. |
    | | Tamanho | Mantenha o padrão, **Standard DS1 v2**. |
    | **Conta de administrador** | Nome de utilizador | Introduza um nome de utilizador à sua escolha. |
    | | Palavra-passe | Introduza uma palavra-passe à sua escolha, que deve ter pelo menos 12 caracteres de comprimento e satisfazer os requisitos de [complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). |
    | | Confirmar Palavra-passe | Introduza a senha novamente. |
    | **Regras da porta de entrada** | Portos de entrada pública | Escolha **nenhum.** |
    | **Poupe dinheiro** | Já tem uma licença do Windows Server? | Escolha **Nº.** |

    ![Básicos, Criar uma máquina virtual, portal Azure](./media/tutorial-create-route-table-portal/basics-create-virtual-machine.png)

    Em seguida, selecione **Seguinte: Discos >**.

1. Em **Discos,** selecione as definições certas para as suas necessidades e, em seguida, selecione **Next : Networking >**.

1. Em **rede:**

    1. Para **a rede Virtual,** selecione **Criar nova**.
    
    1. Na caixa de diálogo de **rede virtual Create,** em **Nome,** introduza *a minha Rede Virtual*.

    1. No **espaço Endereço,** substitua a gama de endereços existente por *10.0.0.0.0/16*.

    1. Em **Subnets,** selecione o ícone **Delete** para eliminar a sub-rede existente e, em seguida, introduza as seguintes combinações de **nome subnet** e gama de **endereços**. Uma vez introduzido um nome e alcance válidos, aparece uma nova linha vazia por baixo.

        | Nome da sub-rede | Intervalo de endereços |
        | ----------- | ------------- |
        | *Público* | *10.0.0.0/24* |
        | *Privado* | *10.0.1.0/24* |
        | *Rede de Perímetro* | *10.0.2.0/24* |

    1. Selecione **OK** para sair da caixa de diálogo.

    1. Na **Subnet,** escolha **DMZ (10.0.2.0/24)**.

    1. No **IP público**, escolha **Nenhum**, uma vez que este VM não se liga à internet.

    1. Selecione **Seguinte : >de gestão **.

1. Sob **Gestão:**

    1. Na **conta de armazenamento de Diagnósticos,** selecione Criar **Novo**.
    
    1. Na caixa de diálogo da **conta de armazenamento Criar,** insira ou selecione estas informações:

        | Definição | Valor |
        | ------- | ----- |
        | Nome | *conta de depósito de minva* |
        | Tipo de conta | **Armazenamento (finalidade geral v1)** |
        | Desempenho | **Standard** |
        | Replicação | **Armazenamento localmente redundante (LRS)** |
    
    1. Selecione **OK** para sair da caixa de diálogo.

    1. Selecione **Rever + criar**. É levado para a página **Review + criar** e o Azure valida a sua configuração.

1. Quando vir a **mensagem de validação passada,** selecione **Criar**.

    A criação da VM demora alguns minutos. Espere até que Azure termine de criar o VM. A página **de implantação da Sua está em andamento** mostra-lhe detalhes de implementação.

1. Quando o seu VM estiver pronto, selecione **Ir para o recurso**.

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

1. No menu do [portal Azure](https://portal.azure.com) ou na página **Inicial,** selecione **Criar um recurso**.

2. Na caixa de pesquisa, introduza a *tabela Rota.* Quando a **tabela Rota** aparecer nos resultados da pesquisa, selecione-a.

3. Na página da **tabela Rota,** selecione **Criar**.

4. Na **Tabela de rotas Criar,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | *myRouteTablePublic* |
    | Subscrição | A sua subscrição |
    | Grupo de recursos | **myResourceGroup** |
    | Localização | **(EUA) Leste dos EUA** |
    | Propagação da rota de gateway da rede virtual | **Ativado** |

    ![Criar mesa de rota, portal Azure](./media/tutorial-create-route-table-portal/create-route-table.png)

5. Selecione **Criar**.

## <a name="create-a-route"></a>Criar uma rota

1. Vá ao [portal Azure](https://portal.azure.com) para gerir a sua mesa de rotas. Procure e selecione **tabelas De Rota.**

1. Escolha o nome da sua tabela de rotas **(myRouteTablePublic).**

1. Escolha **rotas** > **Adicionar**.

    ![Adicionar rota, tabela de rotas, portal Azure](./media/tutorial-create-route-table-portal/add-route.png)

1. Na **rota Adicionar,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome da rota | *ToPrivateSubnet* |
    | Prefixo de endereço | *10.0.1.0/24* (a gama de endereços da subnet *privada* criada anteriormente) |
    | Tipo de salto seguinte | **Aparelho virtual** |
    | Endereço do próximo salto | *10.0.2.4* (um endereço dentro da gama de endereços da sub-rede *DMZ)* |

1. Selecione **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

1. Vá ao [portal Azure](https://portal.azure.com) para gerir a sua rede virtual. Procure e selecione **redes Virtuais.**

1. Escolha o nome da sua rede virtual **(myVirtualNetwork).**

1. Na barra de menus da rede virtual, escolha **Subnets.**

1. Na lista de sub-redes da rede virtual, escolha **Public**.

1. Na **tabela Rota,** escolha a tabela de rotas que criou **(myRouteTablePublic)** e, em seguida, selecione **Guardar** para associar a sua tabela de rotas à subnet *pública.*

    ![Tabela de rotas associada, lista de subredes, rede virtual, portal Azure](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="turn-on-ip-forwarding"></a>Ligue o encaminhamento ip

Em seguida, ligue o IP reencaminhamento para a sua nova máquina virtual NVA, *myVmNva*. Quando o Azure envia tráfego de rede para *myVmNva*, se o tráfego estiver destinado a um endereço IP diferente, o encaminhamento IP envia o tráfego para a localização correta.

1. Vá ao [portal Azure](https://portal.azure.com) para gerir o seu VM. Procure e selecione **máquinas Virtuais**.

1. Escolha o nome do seu VM **(myVmNva).**

1. Na barra de menu da sua máquina virtual NVA, selecione **Networking**.

1. Selecione **myvmnva123**. É a interface de rede que o Azure criou para o seu VM. Azure adiciona números para garantir um nome único.

    ![Máquina virtual de rede , aparelho virtual de rede (NVA) (VM), portal Azure](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. Na barra de menu de interface de rede, selecione **configurações IP**.

1. Na página de **configurações IP,** delineie **o reinício ip** para **Ativado**e selecione **Save**.

    ![Ativar o encaminhamento IP, configurações IP, interface de rede, máquina virtual de aparelhos de rede (NVA) (VM), portal Azure](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Criar máquinas virtuais públicas e privadas

Crie um VM público e um VM privado na rede virtual. Mais tarde, irá usá-los para ver que azure encaminha o tráfego de sub-rede *pública* para a subnet *privada* através da NVA.

Para criar o VM público e o VM privado, siga os passos de [Criar um NVA](#create-an-nva) mais cedo. Não precisa esperar que a implantação termine ou vá ao recurso VM. Utilizará a maioria das mesmas definições, exceto as descritas abaixo.

Antes de selecionar **Criar** para criar o VM público ou privado, vá às duas subsecções seguintes ([VM Público](#public-vm) e [VM Privada),](#private-vm)que mostram os valores que têm de ser diferentes. Pode continuar até ao troço seguinte ( Tráfego de rota através de[um NVA](#route-traffic-through-an-nva)) depois de o Azure terminar a implantação de ambos os VMs.

### <a name="public-vm"></a>VM público

| Tecla de Tabulação | Definição | Valor |
| --- | ------- | ----- |
| Noções básicas | Grupo de recursos | **myResourceGroup** |
| | Nome da máquina virtual | *myVmPublic* |
| | Portos de entrada pública | **Permitir portas selecionadas** |
| | Selecione portas de entrada | **RDP** |
| Redes | Rede virtual | **myVirtualNetwork** |
| | Subrede | **Público (10.0.0.0.0/24)** |
| | Endereço IP público | O padrão |
| Gestão | Conta de armazenamento de diagnóstico | **conta de depósito de minva** |

### <a name="private-vm"></a>VM privado

| Tecla de Tabulação | Definição | Valor |
| --- | ------- | ----- |
| Noções básicas | Grupo de recursos | **myResourceGroup** |
| | Nome da máquina virtual | *myVmPrivate* |
| | Portos de entrada pública | **Permitir portas selecionadas** |
| | Selecione portas de entrada | **RDP** |
| Redes | Rede virtual | **myVirtualNetwork** |
| | Subrede | **Privado (10.0.1.0/24)** |
| | Endereço IP público | O padrão |
| Gestão | Conta de armazenamento de diagnóstico | **conta de depósito de minva** |

## <a name="route-traffic-through-an-nva"></a>Encaminhar o tráfego através de uma NVA

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Inscreva-se no myVmPrivate sobre desktop remoto

1. Vá ao [portal Azure](https://portal.azure.com) para gerir o seu VM privado. Procure e selecione **máquinas Virtuais**.

1. Escolha o nome do seu VM privado **(myVmPrivate).**

1. Na barra de menuS VM, selecione **Connect** para criar uma ligação remota ao ambiente de trabalho remoto com o VM privado.

1. Na página **Connect with RDP,** selecione **Download RDP File**. O Azure cria um ficheiro Remote Desktop *(.rdp)* e transfere-o para o computador.

1. Abra o ficheiro *.rdp* descarregado. Se lhe for pedido, selecione **Ligar**. Selecione **Mais escolhas** > **Utilize uma conta diferente**e introduza o nome de utilizador e a palavra-passe especificado ao criar o VM privado.

1. Selecione **OK**.

1. Se receber um aviso de certificado durante o processo de entrada, selecione **Sim** para ligar ao VM.

### <a name="enable-icmp-through-the-windows-firewall"></a>Ativar o ICMP através da firewall do Windows

Num passo posterior, utilizará a ferramenta de rota de rastreio para testar o encaminhamento. A rota de rastreio utiliza o Protocolo de Mensagem de Controlo de Internet (ICMP), que o Windows Firewall nega por padrão. Ativar o ICMP através da firewall do Windows.

1. No Ambiente de Trabalho Remoto do *myVmPrivate,* abra a PowerShell.

1. Insira este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Vais usar a rota do rasto para testar o encaminhamento neste tutorial. Para ambientes de produção, não recomendamos permitir o ICMP através do Firewall do Windows.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Ligue o iMIp a encaminhar-se dentro do myVmNva

[Ligou o encaminhamento IP](#turn-on-ip-forwarding) para a interface de rede do VM utilizando o Azure. O sistema operativo da VM também tem de encaminhar o tráfego da rede. Ligue o encaminhamento IP para o sistema operativo *myVmNva* VM com estes comandos.

1. A partir de um pedido de comando no *myVmPrivate* VM, abra um ambiente de trabalho remoto para o *myVmNva* VM:

    ```cmd
    mstsc /v:myvmnva
    ```

1. A partir da PowerShell no *myVmNva* VM, insira este comando para ligar o reencaminhamento IP:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Reinicie o *myVmNva* VM: Da barra de tarefas, selecione **Start** > **Power**, Outros **(Planeado)** > **Continue**.

    Isto também desliga a sessão remota de ambiente de trabalho.

1. Depois do *myVmNva* VM reiniciar, crie uma sessão remota de desktop para o *myVmPublic* VM. Enquanto ainda estiver ligado ao *myVmPrivate* VM, abra um pedido de comando e execute este comando:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. No ambiente de trabalho remoto do *myVmPublic,* abra a PowerShell.

1. Ativar o ICMP através da firewall do Windows, entrando neste comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Testar o encaminhamento do tráfego de rede

Primeiro, vamos testar o encaminhamento do tráfego de rede desde o *myVmPublic* VM até ao *myVmPrivate* VM.

1. A partir da PowerShell no *myVmPublic* VM, introduza este comando:

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

    Pode ver que o primeiro salto é de 10.0.2.4, que é o endereço IP privado da NVA. O segundo salto é para o endereço IP privado do *myVmPrivate* VM: 10.0.1.4. Anteriormente, adicionou a rota à tabela de rotas *myRouteTablePublic* e associou-a à subnet *pública.* Como resultado, o Azure enviou o tráfego através da NVA e não diretamente para a subnet *privada.*

1. Feche a sessão de ambiente de trabalho remoto para a VM *myVmPublic*, o que mantém a ligação à VM *myVmPrivate* ativa.

1. A partir de um pedido de comando no *myVmPrivate* VM, insira este comando:

    ```cmd
    tracert myVmPublic
    ```

    Este comando testa o encaminhamento do tráfego de rede desde o *myVmPrivate* VM até ao *myVmPublic* VM. A resposta é semelhante a este exemplo:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Pode ver que o Azure encaminha o tráfego diretamente do *myVmPrivate* VM para o *myVmPublic* VM. Por predefinição, o Azure encaminha o tráfego diretamente entre sub-redes.

1. Feche a sessão de ambiente de trabalho remoto para a VM *myVmPrivate*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando o grupo de recursos já não for necessário, elimine o *myResourceGroup* e todos os recursos que tem:

1. Vá ao [portal Azure](https://portal.azure.com) para gerir o seu grupo de recursos. Procure e selecione **grupos de Recursos**.

1. Escolha o nome do seu grupo de recursos **(myResourceGroup).**

1. Selecione **Eliminar grupo de recursos**.

1. Na caixa de diálogo de confirmação, introduza o *myResourceGroup* para **ESCREVER O NOME DE GRUPO DE RECURSOS**, e, em seguida, selecione **Apagar**. O Azure elimina o *myResourceGroup* e todos os recursos ligados a esse grupo de recursos, incluindo as suas tabelas de rotas, contas de armazenamento, redes virtuais, VMs, interfaces de rede e endereços IP públicos.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma tabela de rotas e associou-a a uma sub-rede. Criou uma NVA simples que encaminhou o tráfego de uma sub-rede pública para uma privada. Agora pode implementar diferentes NVAs pré-reconfigurados do [Azure Marketplace,](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)que fornecem muitas funções úteis de rede. Para saber mais sobre o encaminhamento, veja [Descrição geral do encaminhamento](virtual-networks-udr-overview.md) e [Manage a route table](manage-route-table.md) (Gerir uma tabela de rotas).

Embora possa utilizar muitos recursos Azure dentro de uma rede virtual, o Azure não pode implementar recursos para alguns serviços PaaS numa rede virtual. É possível restringir o acesso aos recursos de alguns serviços Azure PaaS, embora a restrição só deve ser o tráfego de uma subnet de rede virtual. Para aprender a restringir o acesso à rede aos recursos do Azure PaaS, consulte o próximo tutorial.

> [!div class="nextstepaction"]
> [Restrict network access to PaaS resources](tutorial-restrict-network-access-to-resources.md) (Restringir o acesso de rede a recursos de PaaS)
