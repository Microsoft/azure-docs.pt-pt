---
title: 'Tutorial: VMs do Balanceador de carga dentro de uma zona – portal do Azure'
titlesuffix: Azure Load Balancer
description: Este tutorial demonstra como criar um Balanceador de Carga Standard com front-end zonal para fazer o balanceamento de carga das VMs dentro de uma zona de disponibilidade através do portal do Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: 5f3b9b48fc5f15738c3de9928ca0bb220a66db12
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985993"
---
# <a name="tutorial-load-balance-vms-within-an-availability-zone-with-standard-load-balancer-by-using-the-azure-portal"></a>Tutorial: Balancear carga de VMs dentro de uma zona de disponibilidade com o Balanceador de carga Standard com o portal do Azure

Este tutorial cria uma [instância do Balanceador de Carga Standard do Azure](https://aka.ms/azureloadbalancerstandard) com um front-end zonal que utiliza um endereço IP público padrão através do portal do Azure. Neste cenário, vai especificar uma determinada zona para as instâncias de front-end e back-end, para alinhar o caminho dos dados e os recursos a uma zona específica. Vai aprender a realizar as funções seguintes:

> [!div class="checklist"]
> * Criar uma instância do Balanceador de Carga Standard com um front-end zonal.
> * Criar grupos de segurança de rede para definir regras de tráfego de entrada.
> * Criar máquinas virtuais (VMs) zonais e anexá-las a um balanceador de carga.
> * Criar uma sonda de estado de funcionamento do balanceador de carga.
> * Criar regras de tráfego do balanceador de carga.
> * Criar um site de Serviços de Informação Internet (IIS) simples.
> * Ver um balanceador de carga em ação.

Para obter mais informações sobre a utilização das zonas de disponibilidade com o Balanceador de Carga Standard, veja [Standard Load Balancer and Availability Zones](load-balancer-standard-availability-zones.md) (Balanceador de Carga Standard e Zonas de Disponibilidade).

Se preferir, utilize a [CLI do Azure](load-balancer-standard-public-zonal-cli.md) para concluir este tutorial.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-standard-load-balancer-instance"></a>Criar uma instância do Balanceador de Carga Standard pública

O Balanceador de Carga Standard só suporta endereços IP públicos padrão. Quando cria um novo IP público durante a criação do balanceador de carga, o mesmo é configurado, de forma automática, como uma versão de SKU Standard. Também tem redundância de zona automaticamente.

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso** > **Rede** > **Balanceador de Carga**.
2. Na **Noções básicas** separador da **criar Balanceador de carga** página, introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **rever + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **criar novo** e escreva *MyResourceGroupZLB* na caixa de texto.|
    | Name                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Type          | Selecione **público**.                                        |
    | SKU           | Selecione **padrão**.                          |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público              | Tipo *myPublicIP* na caixa de texto.   |
    |Zona de disponibilidade| Selecione **1**.    |
3. Na **rever + criar** separador, clique em **criar**.   

 ## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta secção, vai criar uma rede virtual. Também vai criar duas máquinas virtuais na mesma zona (nomeadamente, na zona 1) da região para adicionar ao conjunto de back-ends do seu balanceador de carga. Em seguida, vai instalar o IIS nas máquinas virtuais para ajudar a testar o balanceador de carga com redundância entre zonas. Se uma VM falhar, a sonda de estado de funcionamento da VM na mesma zona falha. O tráfego continua a ser servido pelas outras VMs na mesma zona.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
1. No canto superior esquerdo do ecrã, selecione **Criar um recurso** > **Rede** > **Rede virtual**.  Introduza estes valores para a rede virtual:
    - **myVNet**, no nome da rede virtual.
    - **myResourceGroupZLB**, no nome do grupo de recursos existente.
    - **myBackendSubnet**, no nome da sub-rede.
2. Selecione **Criar** para criar a rede virtual.

    ![Criar uma rede virtual](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso**. Na caixa de pesquisa, introduza **Grupo de Segurança de Rede**. Na página do grupo de segurança de rede, selecione **Criar**.
2. Na página **Criar grupo de segurança de rede**, introduza estes valores:
    - **myNetworkSecurityGroup**, no nome do grupo de segurança de rede.
    - **myResourceGroupLBAZ**, no nome do grupo de recursos existente.
   
     ![Criar um grupo de segurança de rede](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>Criar regras de NSG

Nesta secção, vai criar regras de NSG para permitir ligações de entrada que utilizem os protocolos HTTP e RDP (Microsoft Remote Desktop Protocol) no portal do Azure.

1. No portal do Azure, selecione **Todos os recursos**, no menu mais à esquerda. Em seguida, procure e selecione **myNetworkSecurityGroup**. Está localizado no grupo de recursos **myResourceGroupZLB**.
2. Em **Definições**, selecione **Regras de segurança de entrada**. Em seguida, selecione **Adicionar**.
3. Introduza estes valores para a regra de segurança de entrada com o nome **myHTTPRule** para permitir ligações HTTP de entrada que utilizem a porta 80:
    - **Service Tag**, na **Origem**.
    - **Internet**, na **Etiqueta do serviço de origem**.
    - **80**, nos **Intervalos de portas de destino**.
    - **vTCP**, no **Protocolo**.
    - **Allow**, na **Ação**.
    - **100**, na **Prioridade**.
    - **myHTTPRule**, no **Nome**.
    - **Allow HTTP**, na **Descrição**.
4. Selecione **OK**.
 
   ![Criar regras de NSG](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)

5. Repita os passos 2 a 4 para criar outra regra com o nome **myRDPRule**. Esta regra permite uma ligação RDP de entrada que utiliza a porta 3389, com os seguintes valores:
    - **Service Tag**, na **Origem**.
    - **Internet**, na **Etiqueta do serviço de origem**.
    - **3389**, nos **Intervalos de portas de destino**.
    - **TCP**, no **Protocolo**.
    - **Allow**, na **Ação**.
    - **200**, na **Prioridade**.
    - **myRDPRule**, no **Nome**.
    - **Allow RDP**, na **Descrição**.

      ![Criar uma regra RDP](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso** > **Computação** > **Windows Server 2016 Datacenter**. Introduza estes valores para a máquina virtual:
    - **myVM1**, no nome da máquina virtual.        
    - **azureuser**, no nome de utilizador do administrador.    
    - **myResourceGroupLB**, no **Grupo de recursos**. Selecione **Utilizar existente** e, em seguida, **myResourceGroupZLB**.
2. Selecione **OK**.
3. Selecione **DS1_V2** para o tamanho da máquina virtual. Escolha **Selecionar**.
4. Introduza estes valores para as definições de VM:
    - **zone 1**, na Zona de Disponibilidade onde vai colocar a VM.
    -  **myVNet**. Certifique-se de que está selecionada como a rede virtual.
    - **myVM1PIP**, no endereço IP público padrão que criar. Selecione **Criar novo**. Em seguida, no tipo de nome, selecione **myVM1PIP**. Em **Zona**, selecione **1**. O SKU do endereço IP é o standard por predefinição.
    - **myBackendSubnet**. Certifique-se de que está selecionada como a sub-rede.
    - **myNetworkSecurityGroup**, no nome da firewall do grupo de segurança de rede que já existe.
5. Selecione **Desativado** para desativar o diagnóstico de arranque.
6. Selecione **OK**. Reveja as definições na página de resumo. Em seguida, selecione **Criar**.
7. Repita os passos 1 a 6 para criar uma segunda VM, com o nome **myVM2**, na Zona 1. Faça de **myVnet** a rede virtual. Faça de **myVM2PIP** o endereço IP público padrão. Faça de **myBackendSubnet** a sub-rede. Por fim, faça de **myNetworkSecurityGroup** o grupo de segurança de rede.

    ![Criar máquinas virtuais](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>Instalar o IIS nas VMs

1. Selecione **Todos os recursos**, no menu mais à esquerda. Em seguida, na lista de recursos, selecione **myVM1**. Está localizado no grupo de recursos **myResourceGroupZLB**.
2. Na página **Descrição geral**, selecione **Ligar** para utilizar RDP para aceder à VM.
3. Inicie sessão na VM com o nome de utilizador e a palavra-passe que especificou quando a criou. Para especificar as credenciais que introduziu quando criou a VM, poderá ter de selecionar **Mais opções**. Depois, selecione **Utilizar uma conta diferente**. Por fim, selecione **OK**. Poderá receber um aviso de certificado durante o processo de início de sessão. Selecione **Sim** para continuar a ligação.
4. No ambiente de trabalho do servidor, navegue para **Ferramentas Administrativas do Windows** > **Windows PowerShell**.
6. Na janela do **PowerShell**, execute os seguintes comandos para instalar o servidor IIS. Estes comandos também removem o ficheiro iisstart.htm predefinido e, em seguida, adicione um ficheiro iisstart.htm novo que apresente o nome da VM:

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
7. Fechar a sessão RDP com **myVM1**.
8. Repita os passos 1 a 7 para instalar o IIS no **myVM2**.

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta secção, vai configurar as definições do balanceador de carga de um conjunto de endereços de back-end e de uma sonda de estado de funcionamento. Também vai especificar as regras do balanceador de carga e de tradução de endereços de rede.


### <a name="create-a-backend-address-pool"></a>Criar um conjunto de endereços de back-end

Para distribuir o tráfego pelas VMs, um conjunto de endereços de back-end contém os endereços IP das placas de interface de rede que estão ligadas ao balanceador de carga. Crie o conjunto de endereços de back-end **myBackendPool** de forma a incluir **VM1** e **VM2**.

1. Selecione **Todos os recursos**, no menu mais à esquerda. Em seguida, selecione **myLoadBalancer** a partir da lista de recursos.
2. Em **Definições**, selecione **Conjuntos de back-end**. Em seguida, selecione **Adicionar**.
3. Na página **Adicionar um conjunto de back-ends**, realize as ações seguintes:
    - No nome, introduza **myBackEndPool** como o nome do conjunto de back-ends.
    - Em **Rede virtual**, no menu pendente, selecione **myVNet**. 
    - Em **Máquina virtual** e **Endereço IP**, adicione **myVM1** e **myVM2**, assim como os endereços IP públicos correspondentes.
4. Selecione **Adicionar**.
5. Confirme que a definição do conjunto de back-ends do balanceador de carga apresenta ambas as VMs, **myVM1** e **myVM2**.
 
    ![Criar um conjunto de back-ends](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Utilize uma sonda de estado de funcionamento para que o balanceador de carga possa monitorizar o estado da sua aplicação. A sonda de estado de funcionamento adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na respetiva resposta às verificações de estado de funcionamento. Crie uma sonda de estado de funcionamento, **myHealthProbe**, para monitorizar o estado de funcionamento das VMs.

1. Selecione **Todos os recursos**, no menu mais à esquerda. Em seguida, selecione **myLoadBalancer** a partir da lista de recursos.
2. Em **Definições**, selecione **Sondas de estado de funcionamento**. Em seguida, selecione **Adicionar**.
3. Utilize estes valores para criar a sonda de estado de funcionamento:
    - **myHealthProbe**, no nome da sonda de estado de funcionamento.
    - **HTTP**, no tipo de protocolo.
    - **80**, no número da porta.
    - **15**, no número do **Intervalo**, em segundos, entre tentativas da sonda.
    - **2**, no número do **Limiar de mau estado de funcionamento** ou das falhas consecutivas da sonda que têm de ocorrer para que uma VM seja considerada como estando em mau estado de funcionamento.
4. Selecione **OK**.

   ![Adicionar uma sonda de estado de funcionamento](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Crie uma regra de balanceador de carga

As regras de balanceador de carga definem a forma como o tráfego é distribuído pelas VMs. Pode definir a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Crie a regra de balanceador de carga **myLoadBalancerRuleWeb**, para escutar na porta 80 no front-end **FrontendLoadBalancer**. A regra envia o tráfego de rede com balanceamento de carga para o conjunto de endereços de back-end **myBackEndPool**, também através da porta 80. 

1. Selecione **Todos os recursos**, no menu mais à esquerda. Em seguida, selecione **myLoadBalancer** a partir da lista de recursos.
2. Em **Definições**, selecione **Regras de balanceamento de carga**. Em seguida, selecione **Adicionar**.
3. Utilize estes valores para configurar a regra de balanceamento de carga:
    - **myHTTPRule**, no nome da regra de balanceamento de carga.
    - **TCP**, no tipo de protocolo.
    - **80**, no número da porta.
    - **80**, na porta do back-end.
    - **myBackendPool**, no nome do conjunto de back-ends.
    - **myHealthProbe**, no nome da sonda de estado de funcionamento.
4. Selecione **OK**.
    
    ![Adicionar uma regra de balanceamento de carga](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga
1. Localize o endereço IP público do balanceador de carga no ecrã **Descrição geral**. Selecione **Todos os recursos**. Em seguida, selecione **myPublicIP**. 

2. Copie o endereço IP público. Em seguida, cole-o na barra de endereço do browser. A página predefinida que inclui o nome da página do servidor Web é apresentada no browser.

      ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. Para ver o balanceador de carga em ação, force a paragem da VM que é apresentada. Atualize o browser para ver o nome do outro servidor mostrado no browser.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, elimine o grupo de recursos, o balanceador de carga e todos os recursos relacionados. Selecione o grupo de recursos que contém o balanceador de carga. Em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais o [Balanceador de Carga Standard](load-balancer-standard-overview.md).
- [VMs de balanceamento de carga em zonas de disponibilidade](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
