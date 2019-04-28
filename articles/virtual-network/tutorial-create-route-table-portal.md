---
title: Rota rede tráfego - tutorial - portal do Azure
titlesuffix: Azure Virtual Network
description: Neste tutorial, saiba como encaminhar o tráfego de rede com uma tabela de rotas através do portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/12/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 855adccf036f731de12810fe0f5287186048ddb0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098646"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Tutorial: Encaminhar o tráfego de rede com uma tabela de rotas com o portal do Azure

O Azure encaminha o tráfego entre todas as sub-redes numa rede virtual, por predefinição. Pode criar as sua próprias rotas para substituir o encaminhamento predefinido do Azure. A capacidade de criar rotas personalizadas é útil se, por exemplo, pretender encaminhar o tráfego entre sub-redes através de uma aplicação virtual de rede (NVA). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma tabela de rotas
> * Criar uma rota
> * Criar uma rede virtual com várias sub-redes
> * Associar uma tabela de rotas a uma sub-rede
> * Criar uma NVA que encaminha o tráfego
> * Implementar máquinas virtuais (VMs) em sub-redes diferentes
> * Encaminhar o tráfego de uma sub-rede para outra através de uma NVA

Se preferir, pode concluir este tutorial com o [CLI do Azure](tutorial-create-route-table-cli.md) ou [Azure PowerShell](tutorial-create-route-table-powershell.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

1. No lado do canto superior esquerdo do ecrã, selecione **criar um recurso** > **rede** > **tabela de rotas**.

1. Na **criar tabela de rotas**, introduza ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Name | Introduza *myRouteTablePublic*. |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **criar novo**, introduza *myResourceGroup*e selecione *OK*. |
    | Location | Deixe a predefinição **E.U.A. Leste**.
    | Propagação de rotas BGP | Deixe a predefinição **ativado**. |

1. Selecione **Criar**.

## <a name="create-a-route"></a>Criar uma rota

1. Na barra de pesquisa do portal, introduza *myRouteTablePublic*.

1. Quando **myRouteTablePublic** aparecer nos resultados da pesquisa, selecione-a.

1. Na **myRouteTablePublic** sob **definições**, selecione **rotas** > **+ adicionar**.

    ![Adicionar rota](./media/tutorial-create-route-table-portal/add-route.png)

1. Na **adicionar rota**, introduza ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome da rota | Introduza *ToPrivateSubnet*. |
    | Prefixo de endereço | Enter *10.0.1.0/24*. |
    | Tipo de salto seguinte | Selecione **Aplicação virtual**. |
    | Endereço do próximo salto | Enter *10.0.2.4*. |

1. Selecione **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Antes de poder associar uma tabela de rotas a uma sub-rede, tem de criar uma rede virtual e uma sub-rede.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No lado do canto superior esquerdo do ecrã, selecione **criar um recurso** > **rede** > **rede Virtual**.

1. Na **criar rede virtual**, introduza ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Name | Introduza *myVirtualNetwork*. |
    | Espaço de endereços | Enter *10.0.0.0/16*. |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione ***selecionar existente*** > **myResourceGroup**. |
    | Location | Deixe a predefinição **E.U.A. Leste**. |
    | Sub-rede - nome | Introduza *público*. |
    | Sub-rede - Intervalo de endereços | Enter *10.0.0.0/24*. |

1. Deixe o resto dos padrões e selecione **criar**.

### <a name="add-subnets-to-the-virtual-network"></a>Adicionar sub-redes para a rede virtual

1. Na barra de pesquisa do portal, introduza *myVirtualNetwork*.

1. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-a.

1. Na **myVirtualNetwork**, em **definições**, selecione **sub-redes** > **+ sub-rede**.

    ![Adicionar sub-rede](./media/tutorial-create-route-table-portal/add-subnet.png)

1. Na **adicionar sub-rede**, introduza estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Name | Introduza *privada*. |
    | Espaço de endereços | Enter *10.0.1.0/24*. |

1. Deixe o resto das predefinições e selecione **OK**.

1. Selecione **+ sub-rede** novamente. Desta vez, introduza estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Name | Enter *DMZ*. |
    | Espaço de endereços | Enter *10.0.2.0/24*. |

1. Como a última vez, deixe o restante dos padrões e selecione **OK**.

    Azure mostra três sub-redes: **Público**, **privada**, e **DMZ**.

### <a name="associate-myroutetablepublic-to-your-public-subnet"></a>Associar myRouteTablePublic à sua sub-rede pública

1. Selecione **público**.

1. Na **pública**, selecione **tabela de rotas** > **MyRouteTablePublic** > **guardar**.

    ![Associar a tabela de rotas](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="create-an-nva"></a>Criar uma NVA

NVAs são VMs que o ajudam a com as funções de rede, como a otimização de encaminhamento e firewall. Pode selecionar um sistema operacional diferente, se desejar. Este tutorial parte do princípio de que está a utilizar **Windows Server 2016 Datacenter**.

1. No lado do canto superior esquerdo do ecrã, selecione **criar um recurso** > **computação** > **Windows Server 2016 Datacenter**.

1. Na **criar uma máquina virtual - Noções básicas**, introduza ou selecione estas informações:

    | Definição | Value |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Enter *myVmNva*. |
    | Região | Selecione **E.U.A. Leste**. |
    | Opções de disponibilidade | Deixe a predefinição **nenhuma redundância de infraestrutura necessária**. |
    | Image | Deixe a predefinição **Windows Server 2016 Datacenter**. |
    | Tamanho | Deixe a predefinição **Standard DS1 v2**. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de utilizador | Introduza um nome de utilizador à sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Palavra-passe | Reintroduza a palavra-passe. |
    | **REGRAS DE PORTA DE ENTRADA** |  |
    | Portas de entrada públicas | Deixe a predefinição **None**.
    | **POUPE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe a predefinição **não**. |

1. Selecione **seguinte: Discos**.

1. Na **criar uma máquina virtual - discos**, selecione as definições que são adequadas para as suas necessidades.

1. Selecione **seguinte: Funcionamento em rede**.

1. Na **criar uma máquina virtual - rede**, selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe a predefinição **myVirtualNetwork**. |
    | Subrede | Selecione **DMZ (10.0.2.0/24)**. |
    | IP público | Selecione **None**. Não tem um endereço IP público. A VM não ligar através da internet.|

1. Deixe o resto dos padrões e selecione **seguinte: Management**.

1. Na **criar uma máquina virtual - gerenciamento**, para **conta de armazenamento de diagnóstico**, selecione **criar novo**.

1. Na **criar conta de armazenamento**, introduza ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Name | Introduza *mynvastorageaccount*. |
    | Tipo de conta | Deixe a predefinição **armazenamento (v1 de fins gerais)**. |
    | Desempenho | Deixe a predefinição **padrão**. |
    | Replicação | Deixe a predefinição **armazenamento localmente redundante (LRS)**.

1. Selecione **OK**

1. Selecione **Rever + criar**. É levado para o **rever + criar** página e o Azure valida a configuração.

1. Quando vir que **a validação passada**, selecione **criar**.

    A criação da VM demora alguns minutos. Não continue até que o Azure acaba de criar a VM. O **sua implementação está em curso** página irá mostrar os detalhes da implementação.

1. Quando a VM estiver pronta, selecione **Ir para recurso**.

## <a name="turn-on-ip-forwarding"></a>Ativar o reencaminhamento de IP

Ativar o reencaminhamento para de IPs *myVmNva*. Quando o Azure envia o tráfego de rede para *myVmNva*, se o tráfego é destinado a um endereço IP diferente, o reencaminhamento de IP enviará o tráfego para a localização correta.

1. No **myVmNva**, em **definições**, selecione **redes**.

1. Selecione **myvmnva123**. Que é a interface de rede que Azure criado para a sua VM. Ela terá uma cadeia de números para o tornar único para.

    ![Redes de VM](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. Sob **configurações**, selecione **configurações de IP**.

1. No **myvmnva123 - configurações de IP**, para **reencaminhamento de IP**, selecione **ativado** e, em seguida, selecione **guardar**.

    ![Ativar o reencaminhamento de IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Crie máquinas virtuais públicas e privadas

Crie uma VM pública e uma privada VM na rede virtual. Mais tarde, vai usá-los para ver que o Azure encaminha o *pública* tráfego de sub-rede para o *privada* através da NVA.

Conclua os passos 1 a 12 [criar uma NVA](#create-an-nva). Usar a maior parte as mesmas definições. Estes valores são aqueles que têm de ser diferentes:

| Definição | Value |
| ------- | ----- |
| **VM PÚBLICA** | |
| NOÇÕES BÁSICAS |  |
| Nome da máquina virtual | Introduza *myVmPublic*. |
| FUNCIONAMENTO EM REDE | |
| Subrede | Selecione **público (10.0.0.0/24)**. |
| Endereço IP público | Aceite a predefinição. |
| Portas de entrada públicas | Selecione **permitir portas selecionadas**. |
| Selecione as portas de entrada | Selecione **HTTP** e **RDP**. |
| GESTÃO | |
| Conta de armazenamento de diagnósticos | Deixe a predefinição **mynvastorageaccount**. |
| **VM PRIVADA** | |
| NOÇÕES BÁSICAS |  |
| Nome da máquina virtual | Introduza *myVmPrivate*. |
| FUNCIONAMENTO EM REDE | |
| Subrede | Selecione **privada (10.0.1.0/24)**. |
| Endereço IP público | Aceite a predefinição. |
| Portas de entrada públicas | Selecione **permitir portas selecionadas**. |
| Selecione as portas de entrada | Selecione **HTTP** e **RDP**. |
| GESTÃO | |
| Conta de armazenamento de diagnósticos | Deixe a predefinição **mynvastorageaccount**. |

Pode criar a VM *myVmPrivate* enquanto o Azure cria a VM *myVmPublic*. Não continue com o resto dos passos até que o Azure acaba de criar ambas as VMs.

## <a name="route-traffic-through-an-nva"></a>Encaminhar o tráfego através de uma NVA

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Inicie sessão no myVmPrivate ao longo do ambiente de trabalho remoto

1. Na barra de pesquisa do portal, introduza *myVmPrivate*.

1. Quando a VM **myVmPrivate** aparecer nos resultados da pesquisa, selecione-a.

1. Selecione **Connect** para criar uma ligação de ambiente de trabalho remoto para o *myVmPrivate* VM.

1. Na **ligar à máquina virtual**, selecione **baixar arquivo RDP**. O Azure cria um protocolo de ambiente de trabalho remoto (*. rdp*) de ficheiros e transfere-o para o seu computador.

1. Abra o transferido *. rdp* ficheiro.

    1. Se lhe for pedido, selecione **Ligar**.

    1. Introduza o nome de utilizador e palavra-passe que especificou quando criou a VM de privados.

    1. Poderá ter de selecionar **mais escolhas** > **utilizar uma conta diferente**, para utilizar as credenciais de VM privada.

1. Selecione **OK**.

    Poderá receber um aviso de certificado durante o início de sessão no processo.

1. Selecione **Sim** para ligar à VM.

### <a name="enable-icmp-through-the-windows-firewall"></a>Ativar o ICMP através da firewall do Windows

Num passo posterior, usará a ferramenta de encaminhamento de rastreio para testar o encaminhamento. Encaminhamento de rastreio utiliza o controle de mensagem ICMP (Internet Protocol), que nega o Firewall do Windows por predefinição. Ative o ICMP através da firewall do Windows.

1. Na área de trabalho remota de *myVmPrivate*, abra o PowerShell.

1. Introduza este comando:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Está a utilizar o encaminhamento de rastreio para testar o encaminhamento neste tutorial. Para ambientes de produção, recomendamos que não permitir o ICMP através da Firewall do Windows.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Ativar o reencaminhamento de IP no myVmNva

[Ativada para o reencaminhamento de IP](#turn-on-ip-forwarding) para interface de rede da VM a utilizar o Azure. Sistema de operativo da VM também tem de reencaminhar o tráfego de rede. Ativar o reencaminhamento para de IPs *myVmNva* VM operativo do sistema com estes comandos.

1. Na linha de comandos no *myVmPrivate* VM, abra um ambiente de trabalho remoto para o *myVmNva* VM:

    ```cmd
    mstsc /v:myvmnva
    ```

1. Do PowerShell no *myVmNva*, introduzir este comando para ativar o reencaminhamento de IP:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Reinicie o *myVmNva* VM. Na barra de tarefas, selecione **botão de início** > **botão de energia**, **outra (planeada)** > **continuar**.

    Que também desliga a sessão de área de trabalho remota.

1. Depois do *myVmNva* reinícios da VM, criar uma sessão de ambiente de trabalho remota para o *myVmPublic* VM. Ainda ligado para o *myVmPrivate* VM, abra uma linha de comandos e execute este comando:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. Na área de trabalho remota de *myVmPublic*, abra o PowerShell.

1. Ative o ICMP através da firewall do Windows ao introduzir este comando:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Testar o encaminhamento de tráfego de rede

Em primeiro lugar, vamos testar o encaminhamento do tráfego de rede dos *myVmPublic* VM para o *myVmPrivate* VM.

1. Do PowerShell no *myVmPublic* VM, introduzir este comando:

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

    Pode ver que o primeiro salto é 10.0.2.4. Ele é o endereço IP privado da NVA. O segundo salto é o endereço IP privado dos *myVmPrivate* VM: 10.0.1.4. Anteriormente, adicionou a rota para o *myRouteTablePublic* tabela de rotas e associou-o para o *público* sub-rede. Como resultado, o Azure enviado o tráfego através da NVA e não diretamente para o *privada* sub-rede.

1. Feche a sessão de ambiente de trabalho remoto para a VM *myVmPublic*, o que mantém a ligação à VM *myVmPrivate* ativa.

1. Na linha de comandos no *myVmPrivate* VM, introduzir este comando:

    ```cmd
    tracert myVmPublic
    ```

    Ele testa o encaminhamento de tráfego de rede a partir do *myVmPrivate* VM para o *myVmPublic* VM. A resposta é semelhante a este exemplo:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Pode ver o Azure encaminha o tráfego diretamente a partir da *myVmPrivate* VM para o *myVmPublic* VM. Por predefinição, o Azure encaminha o tráfego diretamente entre sub-redes.

1. Feche a sessão de ambiente de trabalho remoto para a VM *myVmPrivate*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos e todos os recursos que tem:

1. Na barra de pesquisa do portal, introduza *myResourceGroup*.

1. Quando vir o **myResourceGroup** nos resultados da pesquisa, selecione-o.

1. Selecione **Eliminar grupo de recursos**.

1. Introduza *myResourceGroup* em **ESCREVER O NOME DO GRUPO DE RECURSOS:** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma tabela de rotas e associou-a a uma sub-rede. Criou uma NVA simples que encaminhou o tráfego de uma sub-rede pública para uma privada. Agora que sabe como fazer isso, pode implementar as NVAs pré-configuradas diferentes do [do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Eles realizar muitas funções de rede que lhe serão úteis. Para saber mais sobre o encaminhamento, veja [Descrição geral do encaminhamento](virtual-networks-udr-overview.md) e [Manage a route table](manage-route-table.md) (Gerir uma tabela de rotas).

Embora possa implementar muitos recursos do Azure numa rede virtual, o Azure não é possível implementar recursos para alguns serviços de PaaS numa rede virtual. É possível restringir o acesso aos recursos de alguns serviços PaaS do Azure. No entanto, a restrição só deve ser tráfego a partir de uma sub-rede de rede virtual. Para saber como restringir o acesso de rede a recursos de PaaS do Azure, avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Restrict network access to PaaS resources](tutorial-restrict-network-access-to-resources.md) (Restringir o acesso de rede a recursos de PaaS)
