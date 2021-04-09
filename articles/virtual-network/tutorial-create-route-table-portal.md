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
ms.date: 03/16/2021
ms.author: kumud
ms.openlocfilehash: 63ea6821097327498803abc6577f28e595883bc0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932935"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Tutorial: Encaminhar o tráfego de rede com uma tabela de rotas através do portal do Azure

A Azure encaminha o tráfego entre todas as sub-redes dentro de uma rede virtual, por padrão. Pode criar as sua próprias rotas para substituir o encaminhamento predefinido do Azure. As rotas personalizadas são úteis quando, por exemplo, pretender encaminhar o tráfego entre sub-redes através de um aparelho virtual de rede (NVA). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma NVA que encaminha o tráfego
> * Criar uma tabela de rotas
> * Criar uma rota
> * Associar uma tabela de rotas a uma sub-rede
> * Implementar máquinas virtuais (VMs) em sub-redes diferentes
> * Encaminhar o tráfego de uma sub-rede para outra através de uma NVA

Este tutorial utiliza o [portal Azure.](https://portal.azure.com) Também pode utilizar [Azure CLI](tutorial-create-route-table-cli.md) ou [Azure PowerShell](tutorial-create-route-table-powershell.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, precisa de uma conta Azure com uma subscrição ativa. Se não tiver uma, pode [criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No menu do portal do Azure, selecione **Criar um recurso**. A partir do Azure Marketplace, selecione rede Virtual **de Rede,**  >  ou procure **rede virtual** na caixa de pesquisa.

2. Selecione **Criar**.

2. Na **Criar rede virtual,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | **Selecione Criar novo,** insira **o myResourceGroup**. </br> Selecione **OK**. |
    | Name | Insira **a minha RedeTual**. |
    | Localização | Selecione **(EUA) Leste DOS** EUA .|

3. Selecione o separador **endereços IP** ou selecione o botão **Seguinte: Endereços IP** na parte inferior da página.

4. No **espaço de endereço IPv4**, selecione o espaço de endereço existente e altere-o para **10.0.0.0/16**.

4. **Selecione + Adicionar sub-rede,** em seguida, insira o nome do **sub-rede** e **10.0.0.0/24** para **intervalo de endereços sub-rede**. 

5. Selecione **Adicionar**.

6. **Selecione + Adicionar sub-rede,** em seguida, insira **private** para nome **da sub-rede** e **10.0.0.0/24** para **intervalo de endereços sub-rede**.

7. Selecione **Adicionar**.

8. **Selecione + Adicionar sub-rede,** em seguida, introduza **DMZ** para **o nome da sub-rede** e **10.0.0.0/24** para **a gama de endereços Sub-rede**.

9. Selecione **Adicionar**.

10. Selecione o separador **Segurança** ou selecione o botão **Seguinte: Segurança** na parte inferior da página.

11. Em **BastionHost**, selecione **Enable**. Insira esta informação:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Nome de bastião | Insira **myBastionHost** |
    | Espaço de endereço AzureBastionSubnet | Insira **10.0.3.0/24** |
    | Endereço IP Público | Selecione **Criar novo**. </br> Para **nome,** insira **myBastionIP**. </br> Selecione **OK**. |

12. Selecione o **separador 'Rever +' ou** selecionar o botão **'Rever +' criar.**

13. Selecione **Criar**.

## <a name="create-an-nva"></a>Criar uma NVA

Os aparelhos virtuais de rede (NVAs) são máquinas virtuais que ajudam nas funções de rede, tais como o encaminhamento e a otimização de firewall. Este tutorial pressupõe que está a utilizar **o Centro de Dados 2019 do Windows Server 2019.** Pode selecionar um sistema operativo diferente, se quiser.

1. No lado superior esquerdo do portal, selecione **Criar uma** máquina Virtual  >  **compute de** recurso  >  . 
   
2. Na **Criação de uma máquina virtual,** escreva ou selecione os valores no **separador Básicos:**

    | Definição | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscrição | selecione a subscrição do Azure |
    | Grupo de Recursos | Selecione **myResourceGroup** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **o myVMNVA** |
    | Region | Selecione **(EUA) Leste dos EUA** |
    | Opções de Disponibilidade | Selecione **Não é necessário um despedimento de infraestrutura** |
    | Imagem | Selecione **o Centro de Dados 2019 do Windows Server 2019** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho VM ou tome a definição predefinida |
    | **Conta de administrador** |  |
    | Nome de utilizador | Insira um nome de utilizador |
    | Palavra-passe | Introduza uma senha |
    | Confirmar palavra-passe | Reintroduza a palavra-passe |
    | **Regras portuárias de entrada** |    |
    | Portas de entrada públicas | Selecione **Nenhuma**. |
    |

3. Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos** e, em seguida, **seguinte: Networking**.
  
4. No separador Networking, selecione ou introduza:

    | Definição | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | Selecione **myVirtualNetwork**. |
    | Sub-rede | Selecione **DMZ** |
    | IP público | Selecione **Nenhum** |
    | Grupo de segurança de rede NIC | Selecione **Basic**|
    | Rede pública de portos de entrada | Selecione **Nenhuma**. |
   
5. Selecione o **separador 'Rever +' ou** selecione o botão **'Avaliação azul+'** na parte inferior da página.
  
6. Reveja as definições e, em seguida, **selecione Criar**.

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

1. No menu do [portal do Azure](https://portal.azure.com) ou a partir da **Home page**, selecione **Criar um recurso**.

2. Na caixa de pesquisa, introduza a **tabela Rota**. Quando **a tabela Rota** aparecer nos resultados da pesquisa, selecione-a.

3. Na página da **tabela Rota,** selecione **Criar**.

4. Na **tabela de rotas Criar** no separador **Básicos, insira** ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** |   |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | Selecione **myResourceGroup**. |
    | **Detalhes da instância** |    |
    | Region | Selecione **East US**. |
    | Name | Insira **myRouteTablePublic**. |
    | Rotas de gateway de propagação | Selecione **Yes** (Sim). |

    :::image type="content" source="./media/tutorial-create-route-table-portal/create-route-table.png" alt-text="Criar mesa de rota, portal Azure." border="true":::

5. Selecione o **separador 'Rever +' ou** selecione o botão **'Avaliação azul+'** na parte inferior da página.

## <a name="create-a-route"></a>Criar uma rota

1. Vá ao [portal Azure](https://portal.azure.com) para gerir a sua tabela de rotas. Procure e selecione **tabelas de rotas**.

2. Selecione o nome da sua tabela de rotas **myRouteTablePublic**.

3. Na página **myRouteTablePublic,** na secção **Definições,** selecione **Rotas**.

4. Na página rotas, selecione o botão **+ Adicionar.**

5. Na **rota Adicionar,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome da rota | **Insira toPrivateSubnet** |
    | Prefixo de endereço | Insira **10.0.1.0/24** (O intervalo de endereços da sub-rede **Privada** criada anteriormente) |
    | Tipo de salto seguinte | Selecione **Aplicação virtual**. |
    | Endereço do próximo salto | Insira **10.0.2.4** (Um endereço dentro do intervalo de endereço da **sub-rede DMZ)** |

6. Selecione **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

1. Vá ao [portal Azure](https://portal.azure.com) para gerir a sua rede virtual. Procure e selecione **redes Virtuais.**

2. Selecione o nome da sua rede virtual **myVirtualNetwork**.

3. Na página **myVirtualNetwork,** na secção **Definições,** selecione **Sub-redes**.

4. Na lista de sub-redes da rede virtual, selecione **Public**.

5. Na **tabela Route**, escolha a tabela de rotas que criou **myRouteTablePublic**. 

6. **Selecione Guardar** para associar a sua tabela de rotas à sub-rede **Pública.**

    :::image type="content" source="./media/tutorial-create-route-table-portal/associate-route-table.png" alt-text="Tabela de rotas associada, lista de sub-redes, rede virtual, portal Azure." border="true":::

## <a name="turn-on-ip-forwarding"></a>Ligue o encaminhamento IP

Em seguida, ligue o encaminhamento IP para a sua nova máquina virtual NVA, **myVMNVA**. Quando o Azure envia tráfego de rede para **o myVMNVA,** se o tráfego estiver destinado a um endereço IP diferente, o encaminhamento IP envia o tráfego para o local correto.

1. Vá ao [portal Azure](https://portal.azure.com) para gerir o seu VM. Procure e selecione **máquinas Virtuais.**

2. Selecione o nome da sua máquina virtual **myVMNVA**.

3. Na página de visão geral **myVMNVA,** em **Definições,** selecione **Networking**.

4. Na página **de networking** do **myVMNVA,** selecione a interface de rede ao lado **da Interface de Rede**.  O nome da interface começará com **myvmnva**.

    :::image type="content" source="./media/tutorial-create-route-table-portal/virtual-machine-networking.png" alt-text="Máquina virtual de rede, aparelho virtual (NVA) (VM), portal Azure" border="true":::

5. Na página geral da interface de rede, em **Definições,** selecione **configurações IP**.

6. Na página de **configurações IP,** despasse **o encaminhamento IP** para **Ativado,** selecione **Guardar**.

    :::image type="content" source="./media/tutorial-create-route-table-portal/enable-ip-forwarding.png" alt-text="Ativar o reencaminhamento de IP" border="true":::

## <a name="create-public-and-private-virtual-machines"></a>Criar máquinas virtuais públicas e privadas

Crie um VM público e um VM privado na rede virtual. Mais tarde, você vai usá-los para ver que a Azure encaminha o tráfego **público** de sub-rede para a sub-rede **Privada** através da NVA.


### <a name="public-vm"></a>VM público

1. No lado superior esquerdo do portal, selecione **Criar uma** máquina Virtual  >  **compute de** recurso  >  . 
   
2. Na **Criação de uma máquina virtual,** escreva ou selecione os valores no **separador Básicos:**

    | Definição | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscrição | selecione a subscrição do Azure |
    | Grupo de Recursos | Selecione **myResourceGroup** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **myVMPublic** |
    | Region | Selecione **(EUA) Leste dos EUA** |
    | Opções de Disponibilidade | Selecione **Não é necessário um despedimento de infraestrutura** |
    | Imagem | Selecione **o Centro de Dados 2019 do Windows Server 2019** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho VM ou tome a definição predefinida |
    | **Conta de administrador** |  |
    | Nome de utilizador | Insira um nome de utilizador |
    | Palavra-passe | Introduza uma senha |
    | Confirmar palavra-passe | Reintroduza a palavra-passe |
    | **Regras portuárias de entrada** |    |
    | Portas de entrada públicas | Selecione **Nenhuma**. |
    |

3. Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos** e, em seguida, **seguinte: Networking**.
  
4. No separador Networking, selecione ou introduza:

    | Definição | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | Selecione **myVirtualNetwork**. |
    | Sub-rede | Selecione **Público** |
    | IP público | Selecione **Nenhum** |
    | Grupo de segurança de rede NIC | Selecione **Basic**|
    | Rede pública de portos de entrada | Selecione **Nenhuma**. |
   
5. Selecione o **separador 'Rever +' ou** selecione o botão **'Avaliação azul+'** na parte inferior da página.
  
6. Reveja as definições e, em seguida, **selecione Criar**.

### <a name="private-vm"></a>VM privado

1. No lado superior esquerdo do portal, selecione **Criar uma** máquina Virtual  >  **compute de** recurso  >  . 
   
2. Na **Criação de uma máquina virtual,** escreva ou selecione os valores no **separador Básicos:**

    | Definição | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscrição | selecione a subscrição do Azure |
    | Grupo de Recursos | Selecione **myResourceGroup** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **myVMPrivate** |
    | Region | Selecione **(EUA) Leste dos EUA** |
    | Opções de Disponibilidade | Selecione **Não é necessário um despedimento de infraestrutura** |
    | Imagem | Selecione **o Centro de Dados 2019 do Windows Server 2019** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho VM ou tome a definição predefinida |
    | **Conta de administrador** |  |
    | Nome de utilizador | Insira um nome de utilizador |
    | Palavra-passe | Introduza uma senha |
    | Confirmar palavra-passe | Reintroduza a palavra-passe |
    | **Regras portuárias de entrada** |    |
    | Portas de entrada públicas | Selecione **Nenhuma**. |
    |

3. Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos** e, em seguida, **seguinte: Networking**.
  
4. No separador Networking, selecione ou introduza:

    | Definição | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | Selecione **myVirtualNetwork**. |
    | Sub-rede | Selecione **Privado** |
    | IP público | Selecione **Nenhum** |
    | Grupo de segurança de rede NIC | Selecione **Basic**|
    | Rede pública de portos de entrada | Selecione **Nenhuma**. |
   
5. Selecione o **separador 'Rever +' ou** selecione o botão **'Avaliação azul+'** na parte inferior da página.
  
6. Reveja as definições e, em seguida, **selecione Criar**.

## <a name="route-traffic-through-an-nva"></a>Encaminhar o tráfego através de uma NVA

### <a name="sign-in-to-private-vm"></a>Inscreva-se no vm privado

1. Vá ao [portal Azure](https://portal.azure.com) para gerir o seu VM privado. Procure e selecione **máquinas Virtuais.**

2. Escolha o nome da sua máquina virtual privada **myVmPrivate**.

3. Na barra de menus VM, selecione **Connect** e, em seguida, selecione **Bastion**.

4. Na página **'Ligar',** selecione o botão **azul Utilizar bastion.**

5. Na página **Bastion,** insira o nome de utilizador e a palavra-passe que criou para a máquina virtual anteriormente.

6. Selecione **Ligar**.

### <a name="configure-firewall"></a>Configurar a firewall

Num passo posterior, utilizará a ferramenta de rota de rastreio para testar o encaminhamento. A rota de rastreio utiliza o Protocolo de Mensagem de Controlo de Internet (ICMP), que o Windows Firewall nega por padrão. 

Ativar o ICMP através da firewall do Windows.

1. Na ligação bastonária do **myVMPrivate,** abra a PowerShell com privilégios administrativos.

2. Insira este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Vais usar a rota de rastreio para testar o encaminhamento neste tutorial. Para ambientes de produção, não recomendamos permitir o ICMP através do Windows Firewall.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Ligue o encaminhamento IP dentro do myVMNVA

Ligou o [encaminhamento IP](#turn-on-ip-forwarding) para a interface de rede do VM utilizando o Azure. O sistema operativo da máquina virtual também tem de encaminhar o tráfego de rede. 

Ligue o encaminhamento IP para **o myVMNVA** com estes comandos.

1. A partir do PowerShell no **myVMPrivate** VM, abra um ambiente de trabalho remoto para o **myVMNVA** VM:

    ```powershell
    mstsc /v:myvmnva
    ```

2. A partir de PowerShell no **myVMNVA** VM, insira este comando para ligar o encaminhamento IP:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

3. Reiniciar **o myVMNVA**.

    ```powershell
    Restart-Computer
    ```

4. Depois do **meuVMNVA** reiniciar, crie uma sessão de desktop remota para **o myVMPublic**. 
    
    Enquanto ainda estiver ligado ao **myVMPrivate,** abra o PowerShell e execute este comando:

    ```powershell
    mstsc /v:myvmpublic
    ```
5. No ambiente de trabalho remoto do **myVMPublic,** abra o PowerShell.

6. Ativar o ICMP através da firewall do Windows introduzindo este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Testar o encaminhamento do tráfego de rede

Em primeiro lugar, vamos testar o encaminhamento do tráfego de rede do **myVMPublic** para **o myVMPrivate**.

1. A partir de PowerShell no **myVMPublic,** insira este comando:

    ```powershell
    tracert myvmprivate
    ```

    A resposta é semelhante a este exemplo:

    ```powershell
    Tracing route to myvmprivate.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

      1     1 ms     *        2 ms  myvmnva.internal.cloudapp.net [10.0.2.4]
      2     2 ms     1 ms     1 ms  myvmprivate.internal.cloudapp.net [10.0.1.4]

    Trace complete.
    ```

    * Pode ver que o primeiro salto é de 10.0.2.4, que é o endereço IP privado **da myVMNVA.** 

    * O segundo salto é para o endereço IP privado do **myVMPrivate:** 10.0.1.4. 

    Anteriormente, adicionou a rota para a tabela de rotas **myRouteTablePublic** e associou-a à sub-rede *Pública.* A Azure enviou o tráfego através da NVA e não diretamente para a sub-rede *Privada.*

2. Feche a sessão de desktop remota ao **myVMPublic,** o que o deixa ainda ligado ao **myVMPrivate**.

3. Open PowerShell no **myVMPrivate,** insira este comando:

    ```powershell
    tracert myvmpublic
    ```

    Este comando testa o encaminhamento do tráfego de rede desde o *myVmPrivate* VM até ao *VM myVmPublic.* A resposta é semelhante a este exemplo:

    ```cmd
    Tracing route to myvmpublic.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

      1     1 ms     1 ms     1 ms  myvmpublic.internal.cloudapp.net [10.0.0.4]

    Trace complete.
    ```

    Pode ver que a Azure liga o tráfego diretamente do *myVMPrivate* para *o myVMPublic*. Por predefinição, o Azure encaminha o tráfego diretamente entre sub-redes.

4. Feche a sessão de bastião ao **myVMPrivate**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o grupo de recursos já não for necessário, elimine **o myResourceGroup** e todos os recursos que contém:

1. Vá ao [portal Azure](https://portal.azure.com) para gerir o seu grupo de recursos. Procure e selecione **grupos de recursos.**

2. Selecione o nome do seu grupo de recursos **myResourceGroup**.

3. Selecione **Eliminar grupo de recursos**.

4. Na caixa de diálogo de confirmação, insira **o myResourceGroup** para **TYPE THE RESOURCE GROUP NAME** e, em seguida, selecione **Delete**. 


## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

* Criei uma tabela de rotas e associou-a a uma sub-rede.
* Criou um simples NVA que encaminhou o tráfego de uma sub-rede pública para uma sub-rede privada. 

Pode implementar diferentes NVAs pré-configurados do [Azure Marketplace,](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)que fornecem muitas funções de rede úteis. 

Para saber mais sobre o encaminhamento, veja [Descrição geral do encaminhamento](virtual-networks-udr-overview.md) e [Manage a route table](manage-route-table.md) (Gerir uma tabela de rotas).

Para filtrar o tráfego de rede numa rede virtual, consulte:
> [!div class="nextstepaction"]
> [Tutorial: Filtrar o tráfego da rede com um grupo de segurança de rede utilizando o portal Azure](tutorial-filter-network-traffic.md)
