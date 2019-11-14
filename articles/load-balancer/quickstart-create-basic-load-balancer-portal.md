---
title: 'Início rápido: criar um Load Balancer básico público-portal do Azure'
titleSuffix: Azure Load Balancer
description: Este guia de início rápido mostra como criar um balanceador de carga Básico público com o portal do Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 3cbb4271909cf739dc3ce13712e388f2fc8e20a5
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048691"
---
# <a name="quickstart-create-a-basic-load-balancer-by-using-the-azure-portal"></a>Início rápido: criar um Load Balancer básico usando o portal do Azure

Balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por máquinas virtuais (VMs). Pode utilizar o portal do Azure para criar um balanceador de carga e balancear o tráfego entre VMs. Este guia de introdução mostra-lhe como criar e configurar um balanceador de carga, servidores de back-end e recursos de rede ao escalão de preço básico.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

Para fazer as tarefas neste início rápido, inicie sessão para o [portal do Azure](https://portal.azure.com).

## <a name="create-a-basic-load-balancer"></a>Criar um Balanceador de Carga Básico

Primeiro, crie um Load Balancer básico público usando o Portal. O nome e endereço IP público que criou são automaticamente configurados como front-end de Balanceador de carga.

1. No canto superior esquerdo do ecrã, clique em **Criar um recurso** > **Rede** > **Balanceador de Carga**.
2. Na guia **noções básicas** da página **criar balanceador de carga** , insira ou selecione as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **revisar + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **criar novo** e digite *MyResourceGroupLB* na caixa de texto.|
    | Nome                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Tipo          | Selecione **público**.                                        |
    | SKU           | Selecione **Básico**.                          |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público              | *MyPublicIP*   |
    | Design| Estático|

3. Na guia **revisar + criar** , clique em **criar**.   


## <a name="create-back-end-servers"></a>Criar servidores de back-end

Em seguida, crie uma rede virtual e duas máquinas virtuais para o conjunto de back-end do seu Balanceador de carga básico. 

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No lado do canto superior esquerdo do portal, selecione **criar um recurso** > **rede** > **rede Virtual**.
   
1. Na **criar rede virtual** painel, escreva ou selecione estes valores:
   
   - **Nome**: tipo *MyVnet*.
   - **ResourceGroup**: lista pendente **selecionar existente** e selecione **MyResourceGroupLB**. 
   - **Sub-rede** > **nome**: tipo *MyBackendSubnet*.
   
1. Selecione **Criar**.

   ![Criar uma rede virtual](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. No lado do canto superior esquerdo do portal, selecione **criar um recurso** > **computação** > **Windows Server 2016 Datacenter**. 
   
1. Na **criar uma máquina virtual**, escreva ou selecione os seguintes valores no **Noções básicas** separador:
   - **Subscrição** > **grupo de recursos**: Drop baixo e selecione **MyResourceGroupLB**.
   - **Detalhes de instância** > **nome da Máquina Virtual**: tipo *MyVM1*.
   - **Detalhes de instância** > **opções de disponibilidade**: 
     1. Lista pendente e selecione **conjunto de disponibilidade**. 
     2. Selecione **criar novo**, tipo *MyAvailabilitySet*e selecione **OK**.
  
1. Selecione o **Networking** separador ou selecione **próximo: discos**, em seguida, **seguinte: redes**. 
   
   Certifique-se de que estão selecionadas as seguintes:
   - **Rede virtual**: **MyVnet**
   - **Sub-rede**: **MyBackendSubnet**
   - **IP público**: **MyVM1-ip**
   
   Para criar um novo grupo de segurança rede (NSG), um tipo de firewall, em **grupo de segurança de rede**, selecione **avançadas**. 
   1. Na **configurar grupo de segurança de rede** campo, selecione **criar nova**. 
   1. Tipo *MyNetworkSecurityGroup*e selecione **OK**. 
   
1. Selecione o **gerenciamento** separador ou selecione **próxima** > **gestão**. Sob **monitorização**, defina **diagnósticos de arranque** para **desativar**.
   
1. Selecione **Rever + criar**.
   
1. Reveja as definições e, em seguida, selecione **criar**. 

1. Siga os passos para criar uma segunda VM denominada *MyVM2*, com um **IP público** endereço da *MyVM2-ip*e todas as outras definições igual MyVM1. 

### <a name="create-nsg-rules-for-the-vms"></a>Criar regras de NSG para as VMs

Nesta secção, vai criar segurança de rede regras de grupo (NSG) para as VMs, para permitir ligações de ambiente de trabalho remoto (RDP) e de internet de entrada (HTTP).

1. Selecione **Todos os recursos** no menu esquerdo. Na lista de recursos, selecione **MyNetworkSecurityGroup** no **MyResourceGroupLB** grupo de recursos.
   
1. Em **Definições**, selecione **Regras de segurança de entrada** e, em seguida, selecione **Adicionar**.
   
1. Na **Adicionar regra de segurança de entrada** caixa de diálogo, para a regra HTTP, escreva ou selecione o seguinte:
   
   - **Origem**: selecione **etiqueta de serviço**.  
   - **Etiqueta de serviço de origem**: selecione **Internet**. 
   - **Intervalos de portas de destino**: tipo *80*.
   - **Protocolo**: selecione **TCP**. 
   - **Ação**: selecione **permitir**.  
   - **Prioridade**: tipo *100*. 
   - **Nome**: tipo *MyHTTPRule*. 
   - **Descrição**: tipo *permitir HTTP*. 
   
1. Selecione **Adicionar**. 
   
   ![Criar uma regra NSG](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
   
1. Repita os passos para a regra de RDP de entrada, com os seguintes valores diferentes:
   - **Intervalos de portas de destino**: tipo *3389*.
   - **Prioridade**: tipo *200*. 
   - **Nome**: tipo *MyRDPRule*. 
   - **Descrição**: tipo *permitir RDP*. 

## <a name="create-resources-for-the-load-balancer"></a>Criar recursos para o Balanceador de carga

Nesta secção, irá configurar as definições de Balanceador de carga para um conjunto de endereços de back-end, uma sonda de estado de funcionamento e uma regra de Balanceador de carga.

### <a name="create-a-backend-address-pool"></a>Criar um conjunto de endereços de back-end

Para distribuir o tráfego para as VMs, o Balanceador de carga utiliza um conjunto de endereços de back-end. O conjunto de endereços de back-end contém os endereços IP das interfaces de rede virtuais (NICs) que estão ligadas ao balanceador de carga. 

**Para criar um conjunto de endereços de back-end que inclua a VM1 e VM2:**

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Conjuntos de back-end** e, em seguida, selecione **Adicionar**.
   
1. Sobre o **adicionar um conjunto de back-end** página, escreva ou selecione os seguintes valores:
   
   - **Nome**: tipo *MyBackEndPool*.
   - **Associados**: remova baixo e selecione **conjunto de disponibilidade**.
   - **Conjunto de disponibilidade**: selecione **MyAvailabilitySet**.
   
1. Selecione **adicionar uma configuração de IP de rede de destino**. 
   1. Adicionar cada máquina virtual (**MyVM1** e **MyVM2**) que criou para o conjunto de back-end.
   2. Depois de adicionar cada máquina, o menu pendente e selecione seu **configuração do IP de rede**. 
   
1. Selecione **OK**.
   
   ![Adicionar o conjunto de endereços de back-end](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)
   
1. Sobre o **conjuntos de back-end** página, expanda **MyBackendPool** e certifique-se de ambos **VM1** e **VM2** estão listados.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir que o Balanceador de carga monitorizar o estado da VM, pode utilizar uma sonda de estado de funcionamento. A sonda de estado de funcionamento adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na respetiva resposta às verificações de estado de funcionamento. 

**Para criar uma sonda de estado de funcionamento para monitorizar o estado de funcionamento das VMs:**

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Sondas de estado de funcionamento** e, em seguida, selecione **Adicionar**.
   
1. Sobre o **adicionar uma sonda de estado de funcionamento** página, escreva ou selecione os seguintes valores:
   
   - **Nome**: tipo *MyHealthProbe*.
   - **Protocolo**: remova baixo e selecione **HTTP**. 
   - **Porta**: tipo *80*. 
   - **Caminho**: aceite */* para o URI predefinido. Pode substituir este valor com qualquer outro URI. 
   - **Intervalo**: tipo *15*. O intervalo é o número de segundos entre tentativas da sonda.
   - **Limiar de mau estado de funcionamento**: tipo *2*. Este valor é o número de falhas consecutivas da sonda que ocorrem antes de uma VM é considerada em mau estado de funcionamento.
   
1. Selecione **OK**.
   
   ![Adicionar uma sonda](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

As regras de balanceador de carga definem a forma como o tráfego é distribuído pelas VMs. A regra define a configuração de IP Front-end para tráfego de entrada, o conjunto IP de back-end para receber o tráfego e as portas de origem e de destino necessárias. 

A regra de Balanceador de carga com o nome **MyLoadBalancerRule** escuta na porta 80 no front-end **LoadBalancerFrontEnd**. A regra envia o tráfego de rede para o conjunto de endereços de back-end **MyBackEndPool**, também na porta 80. 

**Para criar a regra de Balanceador de carga:**


1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Regras de balanceamento de carga** e, em seguida, selecione **Adicionar**.
   
1. Sobre o **Adicionar regra de balanceamento de carga** página, escreva ou selecione os seguintes valores:
   
   - **Nome**: tipo *MyLoadBalancerRule*.
   - **Endereço IP de front-end:** tipo *LoadBalancerFrontend*.
   - **Protocolo**: selecione **TCP**.
   - **Porta**: tipo *80*.
   - **Porta de back-end**: tipo *80*.
   - **Conjunto back-end**: selecione **MyBackendPool**.
   - **Sonda de estado de funcionamento**: selecione **MyHealthProbe**. 
   
1. Selecione **OK**.
   
   ![Adicionar uma regra de Balanceador de carga](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Usará o endereço IP público para testar o Balanceador de carga nas VMs. 

No portal, no **descrição geral** página **MyLoadBalancer**, encontrar o endereço IP público em **endereço IP público**. Coloque o cursor sobre o endereço e selecione o **cópia** ícone para copiá-lo. 

### <a name="install-iis-on-the-vms"></a>Instalar o IIS nas VMs

Instale serviços de informação Internet (IIS) em máquinas virtuais para o ajudar a testar o Balanceador de carga.

**Para o ambiente de trabalho remoto (RDP) na VM:**

1. No portal, selecione **todos os recursos** no menu da esquerda. Na lista de recursos, selecione **MyVM1** no **MyResourceGroupLB** grupo de recursos.
   
1. Sobre o **descrição geral** página, selecione **Connect**e, em seguida, selecione **ficheiro RDP transferir**. 
   
1. Abra o RDP de ficheiros que transferiu e selecione **Connect**.
   
1. No ecrã de segurança do Windows, selecione **mais escolhas** e, em seguida **utilizar uma conta diferente**. 
   
   Introduza o nome de utilizador e palavra-passe e selecione **OK**.
   
1. Responder **Sim** para qualquer linha de comandos do certificado. 
   
   A área de trabalho da VM abre-se numa nova janela. 
   
**Para instalar o IIS**

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos**e, em seguida, na lista recursos, selecione **myVM1** que está localizado no grupo de recursos *myResourceGroupSLB* .
2. Na página **Descrição geral**, selecione **Ligar** para estabelecer o RDP para a VM.
5. Inicie sessão na VM com as credenciais que forneceu durante a criação desta VM. Esta ação inicia uma sessão de ambiente de trabalho remoto com a máquina virtual *myVM1*.
6. No ambiente de trabalho do servidor, navegue para **Ferramentas Administrativas do Windows**>**Windows PowerShell**.
7. Na Janela do PowerShell, execute os seguintes comandos para instalar o servidor IIS, remova o ficheiro iisstart.htm predefinido e, em seguida, adicione um novo ficheiro iisstart.htm que apresenta o nome da VM:

   ```azurepowershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
    remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Fechar a sessão RDP com *myVM1*.
7. Repita os passos 1 a 6 para instalar o IIS e o ficheiro iisstart.htm atualizado em *myVM2*.
   
1. Repita os passos para a máquina virtual **MyVM2**, exceto a definir o servidor de destino **MyVM2**.

### <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Abra um browser e cole o endereço IP público do seu Balanceador de carga na barra de endereço do browser. A página predefinida do IIS web server deve aparecer no navegador.

![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver o balanceador de carga a distribuir tráfego pelas VMs que estão a executar a aplicação, pode forçar a atualização do browser.
## <a name="clean-up-resources"></a>Limpar recursos

Para eliminar o Balanceador de carga e todos os recursos relacionados, quando já não precisar delas, abra a **MyResourceGroupLB** recursos de grupo e selecione **eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou um balanceador de carga de escalão Basic. Criar e configurar um grupo de recursos, recursos de rede, servidores de back-end, uma sonda de estado de funcionamento e regras para utilizar com o Balanceador de carga. Instalou o IIS nas VMs e utilizado para testar o Balanceador de carga. 

Para saber mais sobre o Balanceador de carga do Azure, avance para os tutoriais.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-basic-internal-portal.md)
