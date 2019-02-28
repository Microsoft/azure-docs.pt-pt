---
title: 'Tutorial: VMs de Balanceador de carga entre zonas de disponibilidade - portal do Azure'
titlesuffix: Azure Load Balancer
description: Este tutorial demonstra como criar um Balanceador de Carga Standard com front-end com redundância entre zonas para balancear a carga de VMs em múltiplas zonas de disponibilidade, através do portal do Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines across availability zones in a region, so that the customers can still access the web service if a datacenter is unavailable.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: 2b37d77e00595be125490431694f4549f61fced6
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56982798"
---
# <a name="tutorial-load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Tutorial: Balancear carga de VMs por zonas de disponibilidade com um balanceador de carga Standard com o portal do Azure

O balanceamento de carga oferece um nível mais elevado de disponibilidade ao propagar os pedidos recebidos por várias máquinas virtuais. Este tutorial mostra a criação de um Standard Balanceador de carga público de carregamento faz o balanceamento de VMs por zonas de disponibilidade. Isto ajuda a proteger as aplicações e os dados de uma falha pouco provável ou da perda de um datacenter completo. Com a redundância de zona, uma ou mais zonas de disponibilidade podem falhar e o caminho de dados mantém-se desde que uma zona na região permaneça em bom estado de funcionamento. Saiba como:

> [!div class="checklist"]
> * Criar um Balanceador de Carga Standard
> * Criar grupos de segurança de rede para definir regras de tráfego de entrada
> * Criar VMs com redundância entre várias zonas e ligar a um balanceador de carga
> * Criar uma sonda de estado de funcionamento do balanceador de carga
> * Criar regras de tráfego do balanceador de carga
> * Criar um site do IIS básico
> * Ver um balanceador de carga em ação

Para obter mais informações sobre a utilização das Zonas de disponibilidade com o Balanceador de Carga Standard, veja [Balanceador de Carga Standard e Zonas de Disponibilidade](load-balancer-standard-availability-zones.md).

Se preferir, pode concluir este tutorial com a [CLI do Azure](load-balancer-standard-public-zone-redundant-cli.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Criar um Balanceador de Carga Standard

O Balanceador de Carga Standard só suporta endereços IP Públicos Standard. Ao criar um novo IP público durante a criação do balanceador de carga, é automaticamente configurado como uma versão de SKU Standard e tem também com redundância entre zonas automaticamente.

1. No canto superior esquerdo do ecrã, clique em **Criar um recurso** > **Rede** > **Balanceador de Carga**.
2. Na **Noções básicas** separador da **criar Balanceador de carga** página, introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **rever + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **criar novo** e escreva *MyResourceGroupLBAZ* na caixa de texto.|
    | Name                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Type          | Selecione **público**.                                        |
    | SKU           | Selecione **padrão**.                          |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público              | Tipo *myPublicIP* na caixa de texto.   |
    |Zona de disponibilidade| Selecione **redundância de zona**.    |
   

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta secção, vai criar uma rede virtual, máquinas virtuais em diferentes zonas na região e, em seguida, instalar o IIS nas máquinas virtuais para o ajudar a testar o balanceador de carga com redundância entre zonas. Por conseguinte, se uma zona falhar, a sonda de estado de funcionamento da VM na mesma zona falhará e o tráfego continuará a ser servido por VMs nas outras zonas.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie uma rede virtual para implementar os seus servidores de back-end.

1. No canto superior esquerdo do ecrã, clique em **Criar um recurso** > **Rede** > **Rede virtual** e introduza estes valores para a rede virtual:
    - *myVnet* - no nome da rede virtual.
    - *myResourceGroupLBAZ* - no nome do grupo de recursos existente.
    - *myBackendSubnet* - no nome da sub-rede.
2. Clique em **Criar** para criar a rede virtual.

    ![Criar uma rede virtual](./media/load-balancer-standard-public-availability-zones-portal/2-load-balancer-virtual-network.png)

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede para definir ligações recebidas para a sua rede virtual.

1. No canto superior esquerdo do ecrã, clique em **Criar um recurso**, no tipo de caixa de pesquisa *Grupo de Segurança de Rede* e, na página do grupo de segurança de rede, clique em **Criar**.
2. Na página Criar grupo de segurança de rede, introduza estes valores:
    - *myNetworkSecurityGroup* – no nome do grupo de segurança de rede.
    - *myResourceGroupLBAZ* – no nome do grupo de recursos existente.
   
![Criar uma rede virtual](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-network-security-group-rules"></a>Criar regras do grupo de segurança de rede

Nesta secção, irá criar regras do grupo de segurança de rede para permitir ligações de entrada através de HTTP e RDP no portal do Azure.

1. No portal do Azure, clique em **Todos os recursos**, no menu da esquerda e, em seguida, clique em **myNetworkSecurityGroup**, que se encontra no grupo de recursos **myResourceGroupLBAZ**.
2. Em **Definições**, clique em **Regras de segurança de entrada** e, em seguida, clique em**Adicionar**.
3. Introduza estes valores para a regra de segurança de entrada com o nome *myHTTPRule* para permitir ligações HTTP de entrada através da porta 80:
    - *Service Tag* - na **Origem**.
    - *Internet* - na **Etiqueta do serviço de origem**
    - *80* - nos **Intervalos de portas de destino**
    - *TCP* - no **Protocolo**
    - *Allow* - na **Ação**
    - *100* - na **Prioridade**
    - *myHTTPRule* – no nome da regra do balanceador de carga.
    - *Allow HTTP* – na descrição da regra do balanceador de carga.
4. Clique em **OK**.
 
 ![Criar uma rede virtual](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. Repita os passos 2 a 4 para criar outra regra com o nome *myRDPRule* para permitir uma ligação RDP de entrada através da porta 3389 com os seguintes valores:
    - *Service Tag* - na **Origem**.
    - *Internet* - na **Etiqueta do serviço de origem**
    - *3389* - nos **Intervalos de portas de destino**
    - *TCP* - no **Protocolo**
    - *Allow* - na **Ação**
    - *200* - na **Prioridade**
    - *myRDPRule* - no nome
    - *Allow RDP* - na descrição

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie máquinas virtuais em diferentes zonas (zona 1, zona 2 e zona 3) na região, para que funcionem como servidores de back-end para o balanceador de carga.

1. No canto superior esquerdo do ecrã, clique em **Criar um recurso** > **Computação** > **Windows Server 2016 Datacenter** e introduza estes valores para a máquina virtual:
    - *myVM1* - no nome da máquina virtual.        
    - *azureuser* - no nome de utilizador do administrador.    
    - *myResourceGroupLBAZ* - para o **Grupo de recursos**, selecione **Utilizar existente** e, em seguida, selecione *myResourceGroupLBAZ*.
2. Clique em **OK**.
3. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **Selecionar**.
4. Introduza estes valores para as definições de VM:
    - *zone 1* - na zona onde colocar a VM.
    -  *myVNet* - certifique-se de que está selecionado como a rede virtual.
    - *myBackendSubnet* - certifique-se de que está selecionado como a sub-rede.
    - *myNetworkSecurityGroup* - no nome do grupo de segurança de rede (firewall).
5. Clique em **Desativado** para desativar o diagnóstico de arranque.
6. Clique em **OK**, reveja as definições na página de resumo e, em seguida, clique em **Criar**.
  
 ![Criar uma máquina virtual](./media/load-balancer-standard-public-availability-zones-portal/create-vm-standard-ip.png)

7. Crie uma segunda VM, com o nome *VM2* na Zona 2, e uma terceira VM na Zona 3, com *myVnet* como rede virtual, *myBackendSubnet* como sub-rede e **myNetworkSecurityGroup* como grupo de segurança de rede através dos passos 1 a 6.

### <a name="install-iis-on-vms"></a>Instalar o IIS nas VMs

1. Clique em **Todos os recursos**, no menu da esquerda e, na lista de recursos, clique em **myVM1**, que se encontra no grupo de recursos *myResourceGroupLBAZ*.
2. Na página **Descrição geral**, clique em **Ligar** para estabelecer o RDP para a VM.
3. Inicie sessão na VM com o nome de utilizador *azureuser*.
4. No ambiente de trabalho do servidor, navegue para **Ferramentas Administrativas do Windows**>**Windows PowerShell**.
5. Na Janela do PowerShell, execute os seguintes comandos para instalar o servidor IIS, remova o ficheiro iisstart.htm predefinido e, em seguida, adicione um novo ficheiro iisstart.htm que apresenta o nome da VM:
   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
6. Fechar a sessão RDP com *myVM1*.
7. Repita os passos 1 a 6 para instalar o IIS e o ficheiro iisstart.htm atualizado em *myVM2* e *myVM3*.

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta secção, vai configurar as definições de balanceador de carga de um conjunto de endereços de back-end e de uma sonda de estado de funcionamento, bem como especificar o balanceador de carga e regras NAT.


### <a name="create-a-backend-address-pool"></a>Criar um conjunto de endereços de back-end

Para distribuir o tráfego pelas VMs, um conjunto de endereços de back-end contém os endereços IP das placas virtuais (NICs) ligadas ao balanceador de carga. Crie o conjunto de endereços de back-end *myBackendPool* para incluir *VM1*, *VM2* e *VM3*.

1. Clique em **Todos os recursos**, no menu da esquerda, e clique em **myLoadBalancer** na lista de recursos.
2. Em **Definições**, clique em **Conjuntos de back-end** e, em seguida, clique em **Adicionar**.
3. Na página **Adicionar um conjunto de back-end**, faça o seguinte:
    - No nome, escreva *myBackEndPool* como o nome do conjunto de back-end.
    - Para **Rede virtual**, no menu pendente, clique em **myVNet**
    - Para **Máquina virtual**, no menu pendente, clique em **myVM1**.
    - Para **Endereço IP**, no menu pendente, clique no endereço IP de myVM1.
4. Clique em **Adicionar novo recurso de back-end** para adicionar cada máquina virtual (*myVM2* e *myVM3*) para o conjunto de back-end do balanceador de carga.
5. Clique em **Adicionar**.

    ![Adicionar ao conjunto de endereços back-end -](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Confirme se a definição do conjunto de back-end do balanceador de carga apresenta as três VMs – **myVM1**, **myVM2** e **myVM3**.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir ao balanceador de carga monitorizar o estado da aplicação, pode utilizar uma sonda de estado de funcionamento. A sonda de estado de funcionamento adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na respetiva resposta às verificações de estado de funcionamento. Crie uma sonda de estado de funcionamento, *myHealthProbe*, para monitorizar o estado de funcionamento das VMs.

1. Clique em **Todos os recursos**, no menu da esquerda, e clique em **myLoadBalancer** na lista de recursos.
2. Em **Definições**, clique em **Sondas de estado de funcionamento** e, em seguida, clique em **Adicionar**.
3. Utilize estes valores para criar a sonda de estado de funcionamento:
    - *myHealthProbe* - no nome da sonda de estado de funcionamento.
    - **HTTP** - no tipo de protocolo.
    - *80* - no número da porta.
    - *15* - no número do **Intervalo**, em segundos, entre tentativas da sonda.
    - *2* - no número do **Limiar de mau estado de funcionamento** ou das falhas consecutivas da sonda que têm de ocorrer para que uma VM seja considerada como estando em mau estado de funcionamento.
4. Clique em **OK**.

   ![Adicionar uma sonda](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Crie uma regra de balanceador de carga

É utilizada uma regra de balanceador de carga para definir a forma como o tráfego é distribuído pelas VMs. Pode definir a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Crie uma regra de balanceador de carga *myLoadBalancerRuleWeb* para escutar na porta 80 no front-end *FrontendLoadBalancer* e enviar o tráfego de rede com balanceamento de carga para o conjunto de endereços de back-end *myBackEndPool* também através da porta 80. 

1. Clique em **Todos os recursos**, no menu da esquerda, e clique em **myLoadBalancer** na lista de recursos.
2. Em **Definições**, clique em **Regras de balanceamento de carga** e, em seguida, clique em **Adicionar**.
3. Utilize estes valores para configurar a regra de balanceamento de carga:
    - *myHTTPRule* - no nome da regra de balanceamento de carga.
    - **TCP** - no tipo de protocolo.
    - *80* - no número da porta.
    - *80* - na porta de back-end.
    - *myBackendPool* - no nome do conjunto de back-end.
    - *myHealthProbe* - no nome da sonda de estado de funcionamento.
4. Clique em **OK**.
    
    ![Adicionar uma regra de balanceamento de carga](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga
1. Localize o endereço IP público do Balanceador de Carga no ecrã **Descrição geral**. Clique em **Todos os recursos** e, em seguida, clique em **myPublicIP**.

2. Copie o endereço IP público e cole-o na barra de endereço do browser. A página predefinida do servidor Web do IIS é apresentada no browser.

      ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver o balanceador de carga a distribuir tráfego nas VMs ao longo da zona, pode forçar a atualização do browser.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, o balanceador de carga e todos os recursos relacionados. Para tal, selecione o grupo de recursos que contém o balanceador de carga e clique em **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais o [Balanceador de Carga Standard](load-balancer-standard-overview.md).
