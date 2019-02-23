---
title: 'Tutorial: Balanceamento de carga de tráfego da internet para VMs - portal do Azure'
titlesuffix: Azure Load Balancer
description: Este tutorial mostra como criar e gerir um Balanceador de Carga Standard com o portal do Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create and Standard Load balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2018
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: 7caddde5c7695d0c572dc139b52cd0743e39d778
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672004"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Tutorial: Balanceamento de carga de tráfego da internet para VMs com o portal do Azure

O balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por várias máquinas virtuais. Neste tutorial, ficará a conhecer os diferentes componentes do Balanceador de carga Standard do Azure que distribuem o tráfego de internet para VMs e proporcionar elevada disponibilidade. Saiba como:


> [!div class="checklist"]
> * Criar um balanceador de carga do Azure
> * Criar máquinas virtuais e instalar o servidor IIS
> * Criar recursos do balanceador de carga
> * Ver um balanceador de carga em ação
> * Adicionar e remover VMs de um balanceador de carga

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no Portal do Azure em [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Criar um Balanceador de Carga Standard

Nesta secção, vai criar um balanceador de carga público que ajuda a fazer o balanceamento de carga de máquinas virtuais. O Balanceador de Carga Standard só suporta endereços IP Públicos Standard. Quando cria um Balanceador de Carga Standard, também tem de criar um novo endereço IP Público Standard que seja configurado como o front-end (denominado *LoadBalancerFrontend* por predefinição) para o mesmo. 

1. No canto superior esquerdo do ecrã, clique em **Criar um recurso** > **Rede** > **Balanceador de Carga**.
2. Na página **Criar balanceador de carga**, introduza ou selecione as informações seguintes, aceite as predefinições das definições restantes e selecione **Criar**:
    
    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Name                   | *myLoadBalancer*                                   |
    | Type          | Público                                        |
    | SKU           | Standard                          |
    | Endereço IP público | Selecione **Criar novo** e escreva *myPublicIP* na caixa de texto. O SKU Standard para o endereço IP Público é selecionado por predefinição. Em **Zona de disponibilidade**, selecione **Com Redundância entre Zonas**. |
    | Subscrição               | Selecione a sua subscrição.    |
    |Grupo de recursos | Selecione **Criar novo** e, em seguida, escreva *myResourceGroupSLB*.    |
    | Localização           | Selecione **Europa Ocidental**.                          |
    

![Criar um balanceador de carga](./media/load-balancer-standard-public-portal/create-load-balancer.png)
   
## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta secção, vai criar uma rede virtual, três máquinas virtuais para o conjunto de back-end do balanceador de carga e, em seguida, instalar o IIS nas máquinas virtuais para o ajudar a testar o balanceador de carga.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
1. No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Rede** > **Rede virtual** e introduza estes valores para a rede virtual:
    |Definição|Valor|
    |---|---|
    |Name|Introduza *myVNet*.|
    |Subscrição| Selecione a sua subscrição.|
    |Grupo de recursos| Selecione **Utilizar existente** e, em seguida, selecione *myResourceGroupSLB*.|
    |Nome da sub-rede| Introduza *myBackendSubnet*.|
    
2. Clique em **Criar** para criar a rede virtual.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Computação** > **Windows Server 2016 Datacenter** e introduza estes valores para a máquina virtual:
    1. Para o nome da máquina virtual, introduza *myVM1*.        
    2. Para **Grupo de recursos**, selecione **Utilizar existente** e, em seguida, selecione *myResourceGroupSLB*.
2. Clique em **OK**.
3. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **Selecionar**.
4. Introduza estes valores para as definições de VM:
    1. Certifique-se de que *myVNet* está selecionado para a rede virtual e que *myBackendSubnet* está selecionado como sub-rede.
    2. Para **Endereço IP público**, no painel **Criar endereço IP público**, selecione **Standard** e, em seguida, selecione **OK**.
    3. Para o **Grupo de Segurança de Rede**, selecione **Avançado** e, em seguida, faça o seguinte:
        1. Selecione *Grupo de segurança de rede (firewall) e, na página **Escolher grupo de segurança de rede**, selecione **Criar novo**. 
        2. Na página **Escolher grupo de segurança de rede**, para **Nome**, introduza *myNetworkSecurityGroup* como nome do novo grupo de segurança de rede e, em seguida, selecione **OK**.
5. Clique em **Desativado** para desativar o diagnóstico de arranque.
6. Clique em **OK**, reveja as definições na página de resumo e, em seguida, clique em **Criar**.
7. Crie mais duas VMs, com os nomes *VM2* e *VM3* com *myVnet* como a rede virtual, *myBackendSubnet* como a sub-rede e *myNetworkSecurityGroup* como o grupo de segurança de rede (veja os passos 1 a 6). 

### <a name="create-network-security-group-rule"></a>Criar regra de grupo de segurança de rede

Nesta secção, vai criar uma regra de NSG para permitir ligações de entrada através de HTTP.

1. Clique em **Todos os recursos**, no menu da esquerda, e, na lista de recursos, clique em **myNetworkSecurityGroup**, que se encontra no grupo de recursos **myResourceGroupSLB**.
2. Em **Definições**, clique em **Regras de segurança de entrada** e, em seguida, clique em**Adicionar**.
3. Introduza estes valores para a regra de segurança de entrada com o nome *myHTTPRule* para permitir ligações HTTP de entrada através da porta 80:
    - *Service Tag* - na **Origem**.
    - *Internet* - na **Etiqueta do serviço de origem**
    - *80* - nos **Intervalos de portas de destino**
    - *TCP* - no **Protocolo**
    - *Allow* - na **Ação**
    - *100* - na **Prioridade**
    - *myHTTPRule* - no nome
    - *Allow HTTP* - na descrição
4. Selecione **Adicionar**.

### <a name="install-iis-on-vms"></a>Instalar o IIS nas VMs

1. Clique em **Todos os recursos** no menu esquerdo e, na lista de recursos, clique em **myVM1**, que se encontra no grupo de recursos *myResourceGroupSLB*.
2. Na página **Descrição geral**, clique em **Ligar** para estabelecer o RDP para a VM.
3. Na janela de pop-up **Ligar à máquina virtual**, selecione **Transferir Ficheiro RDP** e, em seguida, abra o ficheiro RDP transferido.
4. Na janela **Ligação de Ambiente de Trabalho Remoto**, clique em **Ligar**.
5. Inicie sessão na VM com as credenciais que forneceu durante a criação desta VM. Esta ação inicia uma sessão de ambiente de trabalho remoto com a máquina virtual *myVM1*.
6. No ambiente de trabalho do servidor, navegue para **Ferramentas Administrativas do Windows**>**Windows PowerShell**.
7. Na Janela do PowerShell, execute os seguintes comandos para instalar o servidor IIS, remova o ficheiro iisstart.htm predefinido e, em seguida, adicione um novo ficheiro iisstart.htm que apresenta o nome da VM:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Fechar a sessão RDP com *myVM1*.
7. Repita os passos 1 a 6 para instalar o IIS e o ficheiro iisstart.htm atualizado em *myVM2* e *myVM3*.

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta secção, irá configurar as definições de balanceador de carga de um conjunto de endereços de back-end e de uma sonda de estado de funcionamento, bem como especificar uma regra de balanceador.

### <a name="create-a-backend-address-pool"></a>Criar um conjunto de endereços de back-end

Para distribuir o tráfego pelas VMs, um conjunto de endereços de back-end contém os endereços IP das placas virtuais (NICs) ligadas ao balanceador de carga. Crie o conjunto de endereços de back-end *myBackendPool* de forma a incluir *VM1* e *VM2*.

1. Clique em **Todos os recursos**, no menu da esquerda, e clique em **myLoadBalancer** na lista de recursos.
2. Em **Definições**, clique em **Conjuntos de back-end** e, em seguida, clique em **Adicionar**.
3. Na página **Adicionar um conjunto de back-end**, faça o seguinte:
   - No nome, escreva *myBackendPool* como o nome do conjunto de back-end.
   - Para **Rede virtual**, selecione *myVNet*.
   - Adicione *myVM1*, *myVM2* e *my VM3* em **Máquina Virtual** juntamente com os endereços IP correspondentes e, em seguida, selecione **Adicionar**.
4. Confirme se a definição do conjunto de back-end do balanceador de carga apresenta as três VMs, *myVM1*, *myVM2* e *myVM3*, e clique em **OK**.

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

   ![Adicionar uma sonda](./media/load-balancer-standard-public-portal/4-load-balancer-probes.png)

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

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga
1. Localize o endereço IP público do Balanceador de Carga no ecrã **Descrição geral**. Clique em **Todos os recursos** e, em seguida, clique em **myPublicIP**.

2. Copie o endereço IP público e cole-o na barra de endereço do browser. A página predefinida do servidor Web do IIS é apresentada no browser.

      ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver o balanceador de carga a distribuir tráfego pelas três VMs que estão a executar a aplicação, pode forçar a atualização do browser.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Remover ou adicionar VMs do conjunto de back-end
Pode ter de realizar a manutenção nas VMs que estão a executar a aplicação, como instalar atualizações do SO. Para lidar com maior volume de tráfego para a aplicação, pode ter de adicionar mais VMs. Esta secção mostra como adicionar ou remover uma VM do balanceador de carga.

1. Clique em **Todos os recursos**, no menu da esquerda, e clique em **myLoadBalancer** na lista de recursos.
2. Em **Definições**, clique em **Conjuntos de back-end** e, em seguida, na lista de conjuntos de back-end, clique em **myBackendPool**.
3. Na página **myBackendPool**, em **Configurações de IP da rede de destino**, para remover *VM1* do back-end, clique no ícone Eliminar junto a **Máquina virtual:myVM1**

Como *myVM1* já não está no conjunto de endereços de back-end, pode realizar quaisquer tarefas de manutenção na *myVM1*, como a instalação de atualizações de software. Na ausência da *VM1**, a carga é agora balanceada entre *myVM2* e *myVM3*. 

Para adicionar *myVM1* novamente ao conjunto de back-end, siga o procedimento na secção *Adicionar VMs ao conjunto de back-end* deste artigo.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, elimine o grupo de recursos, o balanceador de carga e todos os recursos relacionados. Para tal, selecione o grupo de recursos que contém o balanceador de carga e clique em **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou um Balanceador de Carga Standard, ligou VMs ao mesmo, configurou a regra de tráfego do balanceador de carga, a sonda de estado de funcionamento e, por fim, testou o balanceador de carga. Além disso, removeu uma VM do conjunto com balanceamento de carga e adicionou a VM de novo ao conjunto de endereços de back-end. Para saber mais sobre o Balanceador de Carga do Azure, avance para os tutoriais do Balanceador de Carga do Azure.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
