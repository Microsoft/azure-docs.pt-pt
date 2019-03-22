---
title: 'Tutorial: Balanceamento de carga de tráfego da internet para VMs - portal do Azure'
titlesuffix: Azure Load Balancer
description: Este tutorial mostra como criar e gerir um Balanceador de Carga Standard com o portal do Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create and Standard Load Balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: 78266e447d1ddf6daf5a9b0ad9172ab6470bf0c6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57845210"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Tutorial: Balanceamento de carga de tráfego da internet para VMs com o portal do Azure

O balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por várias máquinas virtuais. Neste tutorial, ficará a conhecer os diferentes componentes do Balanceador de carga Standard do Azure que distribuem o tráfego de internet para VMs e proporcionar elevada disponibilidade. Saiba como:


> [!div class="checklist"]
> * Criar um balanceador de carga do Azure
> * Criar recursos do Balanceador de carga
> * Criar máquinas virtuais e instalar o servidor IIS
> * Ver o Balanceador de carga em ação
> * Adicionar e remover VMs de um balanceador de carga

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Criar um Balanceador de Carga Standard

Nesta secção, vai criar um balanceador de carga Standard que ajuda a máquinas virtuais de balanceamento de carga. O Balanceador de Carga Standard só suporta endereços IP Públicos Standard. Quando cria um Balanceador de Carga Standard, também tem de criar um novo endereço IP Público Standard que seja configurado como o front-end (denominado *LoadBalancerFrontend* por predefinição) para o mesmo. 

1. No canto superior esquerdo do ecrã, clique em **Criar um recurso** > **Rede** > **Balanceador de Carga**.
2. Na **Noções básicas** separador da **criar Balanceador de carga** página, introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **rever + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **criar novo** e escreva *myResourceGroupSLB* na caixa de texto.|
    | Name                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Type          | Selecione **público**.                                        |
    | SKU           | Selecione **padrão**.                          |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público              | Tipo *myPublicIP* na caixa de texto.   |
    |Zona de disponibilidade| Selecione **redundância de zona**.    |

3. Na **rever + criar** separador, clique em **criar**.

   ![Criar um Balanceador de Carga Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Criar recursos do Balanceador de carga

Nesta secção, configure as definições de Balanceador de carga para um conjunto de endereços de back-end, uma sonda de estado de funcionamento e especificar uma regra de Balanceador.

### <a name="create-a-backend-address-pool"></a>Criar um conjunto de endereços de back-end

Para distribuir o tráfego para as VMs, um conjunto de endereços de back-end contém o IP de endereços virtuais (NICs) ligadas ao balanceador de carga. Criar o conjunto de endereços de back-end *myBackendPool* para incluir máquinas virtuais para o tráfego de internet de balanceamento de carga.

1. Selecione **todos os serviços** no menu da esquerda, selecione **todos os recursos**e, em seguida, clique em **myLoadBalancer** na lista de recursos.
2. Em **Definições**, clique em **Conjuntos de back-end** e, em seguida, clique em **Adicionar**.
3. Sobre o **adicionar um conjunto de back-end** página, para nome, tipo *myBackendPool*, como o nome para seu conjunto de back-end e, em seguida, selecione **Add**.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir que o Balanceador de carga monitorizar o estado da sua aplicação, pode utilizar uma sonda de estado de funcionamento. A sonda de estado de funcionamento dinamicamente adiciona ou remove as VMs a rotação do Balanceador de carga com base na respetiva resposta às verificações do Estado de funcionamento. Crie uma sonda de estado de funcionamento, *myHealthProbe*, para monitorizar o estado de funcionamento das VMs.

1. Selecione **todos os serviços** no menu da esquerda, selecione **todos os recursos**e, em seguida, clique em **myLoadBalancer** na lista de recursos.
2. Em **Definições**, clique em **Sondas de estado de funcionamento** e, em seguida, clique em **Adicionar**.
3. Utilize estes valores para criar a sonda de estado de funcionamento:
     
    | Definição | Valor |
    | ------- | ----- |
    | Name | Introduza *myHealthProbe*. |
    | Protocolo | Selecione **HTTP**. |
    | Porta | Enter *80*.|
    | Intervalo | Introduza *15* para o número de **intervalo** em segundos, entre tentativas da sonda. |
    | Limiar com funcionamento incorreto | Selecione *2* para o número de **limiar de mau estado de funcionamento** ou falhas consecutivas da sonda que devem ocorrer antes de uma VM é considerada em mau estado de funcionamento.|
    | Sonda de estado de funcionamento | Selecione *myHealthProbe*. |
    
4. Selecione **OK**.

### <a name="create-a-load-balancer-rule"></a>Crie uma regra de Balanceador de Carga

Pode utilizar uma regra de Balanceador de Carga para definir a forma como o tráfego é distribuído pelas VMs. Pode definir a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Criar uma regra de Balanceador de carga *myLoadBalancerRuleWeb* para escutar a porta 80 no front-end *FrontendLoadBalancer* e enviar o tráfego de rede com balanceamento de carga para o conjunto de endereços de back-end *myBackEndPool* também através da porta 80.

1. Selecione **todos os serviços** no menu da esquerda, selecione **todos os recursos**e, em seguida, clique em **myLoadBalancer** na lista de recursos.
2. Em **Definições**, clique em **Regras de balanceamento de carga** e, em seguida, clique em **Adicionar**.
3. Utilize estes valores para configurar a regra de balanceamento de carga:

    | Definição | Valor |
    | ------- | ----- |
    | Name | Introduza *myHTTPRule*. |
    | Protocolo | Selecione **TCP**. |
    | Porta | Enter *80*.|
    | Porta back-end | Enter *80*. |
    | Conjunto back-end | Selecione *myBackendPool*.|
    | Sonda de estado de funcionamento | Selecione *myHealthProbe*. |
    
4. Deixe o resto das predefinições e selecione **OK**.

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta secção, criar uma rede virtual, crie três máquinas virtuais para o conjunto de back-end do Balanceador de carga e, em seguida, instalar o IIS nas máquinas virtuais para o ajudar a testar o Balanceador de carga.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No lado do canto superior esquerdo do ecrã, selecione **criar um recurso** > **rede** > **rede Virtual**.
2. Na **criar rede virtual**, introduza ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Name | Introduza *myVNet*. |
    | Espaço de endereços | Enter *10.1.0.0/16*. |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | Selecione o recurso existente - *myResourceGroupSLB*. |
    | Localização | Selecione **Europa Ocidental**.|
    | Sub-rede - nome | Introduza *myBackendSubnet*. |
    | Sub-rede - Intervalo de endereços | Enter *10.1.0.0/24*. |
    
3. Deixe o resto dos padrões e selecione **criar**.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Balanceador de carga Standard só suporta VMs com endereços de IP padrão no conjunto de back-end. Nesta secção, vai criar três VMs (*myVM1*, *myVM2*, e *myVM3*) com um endereço IP público Standard em três zonas diferentes (*zona 1*, *Zona 2*, e *zona 3*) que são adicionados ao agrupamento de back-end de Balanceador de carga Standard, que foi criado anteriormente.

1. No lado do canto superior esquerdo do portal, selecione **criar um recurso** > **computação** > **Windows Server 2016 Datacenter**. 
   
1. Na **criar uma máquina virtual**, escreva ou selecione os seguintes valores no **Noções básicas** separador:
   - **Subscrição** > **grupo de recursos**: Selecione **myResourceGroupSLB**.
   - **Detalhes de instância** > **nome da Máquina Virtual**: Tipo *myVM1*.
   - **Detalhes de instância** > **região** > selecione **Europa Ocidental**.
   - **Detalhes de instância** > **opções de disponibilidade** > selecione **zonas de disponibilidade**. 
   - **Detalhes de instância** > **zona de disponibilidade** > selecione **1**.
  
1. Selecione o **Networking** separador ou selecione **seguinte: Discos**, em seguida, **seguinte: Funcionamento em rede**. 
   
   - Certifique-se de que estão selecionadas as seguintes:
       - **Rede virtual**: **myVnet**
       - **Sub-rede**: **myBackendSubnet**
       - **IP público** > selecione **criar novo**e, no **Criar endereço IP público** janela, para **SKU**, selecione **padrão**, e para **zona de disponibilidade**, selecione **redundância de zona**
      
   - Para criar um novo grupo de segurança rede (NSG), um tipo de firewall, em **grupo de segurança de rede**, selecione **avançadas**. 
       1. Na **configurar grupo de segurança de rede** campo, selecione **criar nova**. 
       1. Tipo *myNetworkSecurityGroup*e selecione **OK**.

   - Para garantir a VM uma parte do conjunto de back-end do Balanceador de carga, conclua os seguintes passos:
        - Na **balanceamento de carga**, para **colocar esta máquina virtual por trás de uma solução de balanceamento de carga existente?**, selecione **Sim**.
        - Na **definições de balanceamento de carga**, para **opções de balanceamento de carga**, selecione **Balanceador de carga do Azure**.
        - Para **selecionar um balanceador de carga**, *myLoadBalancer*. 
1. Selecione o **gerenciamento** separador ou selecione **próxima** > **gestão**. Sob **monitorização**, defina **diagnósticos de arranque** para **desativar**. 
1. Selecione **Rever + criar**.   
1. Reveja as definições e, em seguida, selecione **criar**.
1. Siga os passos para criar duas VMs adicionais - *myVM2* e *myVM3*, com um endereço IP público de Standard SKU na **zona de disponibilidade** **2** e **3** respectivamente e todas as outras definições igual *myVM1*.  

### <a name="create-network-security-group-rule"></a>Criar regra de grupo de segurança de rede

Nesta secção, vai criar uma regra de grupo de segurança de rede para permitir ligações de entrada através de HTTP.

1. Selecione **todos os serviços** no menu da esquerda, selecione **todos os recursos**e, em seguida, na lista de recursos clique **myNetworkSecurityGroup** que se encontra no **myResourceGroupSLB** grupo de recursos.
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

1. Selecione **todos os serviços** no menu da esquerda, selecione **todos os recursos**e, em seguida, na lista de recursos clique **myVM1** que se encontra no  *myResourceGroupSLB* grupo de recursos.
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

## <a name="test-the-load-balancer"></a>Testar o Balanceador de carga
1. Localize o endereço IP público do Balanceador de Carga no ecrã **Descrição geral**. Selecione **todos os serviços** no menu da esquerda, selecione **todos os recursos**e, em seguida, clique em **myPublicIP**.

2. Copie o endereço IP público e cole-o na barra de endereço do browser. A página predefinida do servidor Web do IIS é apresentada no browser.

      ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver o Balanceador de carga a distribuir tráfego pelas três VMs a executar o seu aplicativo, pode forçar a atualização seu navegador da web.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Remover ou adicionar VMs do conjunto de back-end
Pode ter de realizar a manutenção nas VMs que estão a executar a aplicação, como instalar atualizações do SO. Para lidar com maior volume de tráfego para a aplicação, pode ter de adicionar mais VMs. Esta secção mostra como remover ou adicionar uma VM (*myVM1*) do Balanceador de carga.

### <a name="remove-vm-from-a-backend-pool"></a>Remover VM a partir de um conjunto de back-end
Para remover *myVM1* do conjunto de back-end, conclua os seguintes passos:

1. Selecione **todos os serviços** no menu da esquerda, selecione **todos os recursos**e, em seguida, clique em **myLoadBalancer** na lista de recursos.
2. Em **Definições**, clique em **Conjuntos de back-end** e, em seguida, na lista de conjuntos de back-end, clique em **myBackendPool**.
3. Sobre o **myBackendPool** página, para remover *VM1* selecione o ícone Eliminar no final da linha que apresenta *myVM1*e, em seguida, clique em **guardar**.

Como *myVM1* já não está no conjunto de endereços de back-end, pode realizar quaisquer tarefas de manutenção na *myVM1*, como a instalação de atualizações de software. Na ausência *VM1*, a carga agora é balanceada entre *myVM2* e *myVM3*. 

### <a name="add-vm-to-a-backend-pool"></a>Adicionar a VM para um conjunto de back-end
Para adicionar *myVM1* volta para o conjunto de back-end, conclua os seguintes passos:

1. Selecione **todos os serviços** no menu da esquerda, selecione **todos os recursos**e, em seguida, selecione **myVM1** na lista de recursos.
2. Na **VM1** página, em **definições**, selecione **redes**.
3. Na **Networking** página, selecione a **balanceamento de carga** separador e, em seguida, selecione **adicionar balanceamento de carga**.
4. Na **adicione o balanceamento de carga** página, efetue o seguinte:
   1. Para **opções de balanceamento de carga**, selecione **Balanceador de carga do Azure**.
   2. Para **selecionar um balanceador de carga**, selecione *myLoadBalancer*.
   3. Para **selecionar um conjunto de back-end**, selecione *myBackendPool*. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, Balanceador de carga e todos os recursos relacionados. Para tal, selecione o *myResouceGroupSLB* grupo de recursos que contém o Balanceador de carga e, em seguida, selecione **eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou um Standard Balanceador de carga, anexou VMs ao mesmo, configurou a regra de tráfego do Balanceador de carga, sonda de estado de funcionamento e, em seguida, testou o Balanceador de carga. Além disso, removeu uma VM do conjunto com balanceamento de carga e adicionou a VM de novo ao conjunto de endereços de back-end. Para saber mais sobre o Balanceador de Carga do Azure, avance para os tutoriais do Balanceador de Carga do Azure.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
