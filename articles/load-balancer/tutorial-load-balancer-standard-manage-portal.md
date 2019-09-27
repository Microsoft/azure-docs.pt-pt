---
title: 'Tutorial: Balancear a carga do tráfego de Internet para VMs-portal do Azure'
titlesuffix: Azure Load Balancer
description: Este tutorial mostra como criar e gerir um Balanceador de Carga Standard com o portal do Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create and Standard Load Balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: a0887a66b6cb886419f86d93973b991151141e0a
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327210"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Tutorial: Balancear a carga do tráfego de Internet para VMs usando o portal do Azure

O balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por várias máquinas virtuais. Neste tutorial, você aprende sobre os diferentes componentes do Standard Load Balancer do Azure que distribuem o tráfego de Internet para as VMs e fornecem alta disponibilidade. Saiba como:


> [!div class="checklist"]
> * Criar um Azure Load Balancer
> * Criar Load Balancer recursos
> * Criar máquinas virtuais e instalar o servidor IIS
> * Exibir Load Balancer em ação
> * Adicionar e remover VMs de um Load Balancer

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Criar um Balanceador de Carga Standard

Nesta seção, você cria um Standard Load Balancer que ajuda a balancear a carga de máquinas virtuais. O Balanceador de Carga Standard só suporta endereços IP Públicos Standard. Quando cria um Balanceador de Carga Standard, também tem de criar um novo endereço IP Público Standard que seja configurado como o front-end (denominado *LoadBalancerFrontend* por predefinição) para o mesmo. 

1. No canto superior esquerdo do ecrã, clique em **Criar um recurso** > **Rede** > **Balanceador de Carga**.
2. Na guia **noções básicas** da página **criar balanceador de carga** , insira ou selecione as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione revisar **+ criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscription               | Selecione a sua subscrição.    |    
    | Resource group         | Selecione **criar novo** e digite *myResourceGroupSLB* na caixa de texto.|
    | Name                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Type          | Selecione **público**.                                        |
    | SKU           | Selecione **padrão**.                          |
    | Endereço IP Público | Selecione **Criar novo**. |
    | Nome do endereço IP público              | Digite *myPublicIP* na caixa de texto.   |
    |Zona de disponibilidade| Selecione a **zona com redundância**.    |

3. Na guia **revisar + criar** , clique em **criar**.

   ![Criar um Balanceador de Carga Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Criar Load Balancer recursos

Nesta seção, você define Load Balancer configurações para um pool de endereços de back-end, uma investigação de integridade e especifica uma regra de balanceador.

### <a name="create-a-backend-address-pool"></a>Criar um conjunto de endereços de back-end

Para distribuir o tráfego para as VMs, um pool de endereços de back-end contém os endereços IP das NICs (virtual) conectados à Load Balancer. Crie o pool de endereços de back-end *myBackendPool* para incluir máquinas virtuais para tráfego de Internet de balanceamento de carga.

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos**e clique em **myLoadBalancer** na lista de recursos.
2. Em **Definições**, clique em **Conjuntos de back-end** e, em seguida, clique em **Adicionar**.
3. Na página **Adicionar um pool de back-end** , em nome, digite *myBackendPool*, como o nome do pool de back-end e, em seguida, selecione **Adicionar**.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir que o Load Balancer monitore o status do seu aplicativo, use uma investigação de integridade. A investigação de integridade adiciona ou remove dinamicamente as VMs da rotação de Load Balancer com base em sua resposta às verificações de integridade. Crie uma sonda de estado de funcionamento, *myHealthProbe*, para monitorizar o estado de funcionamento das VMs.

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos**e clique em **myLoadBalancer** na lista de recursos.
2. Em **Definições**, clique em **Sondas de estado de funcionamento** e, em seguida, clique em **Adicionar**.
3. Utilize estes valores para criar a sonda de estado de funcionamento:
     
    | Definição | Valor |
    | ------- | ----- |
    | Name | Insira *myHealthProbe*. |
    | Protocol | Selecione **http**. |
    | Port | Insira *80*.|
    | Interval | Digite *15* para o número de **intervalo** em segundos entre as tentativas de investigação. |
    | Limiar com funcionamento incorreto | Selecione *2* para número de **limites** não íntegros ou falhas de investigação consecutivas que devem ocorrer antes que uma VM seja considerada não íntegra.|
    
4. Selecione **OK**.

### <a name="create-a-load-balancer-rule"></a>Crie uma regra de Balanceador de Carga

Pode utilizar uma regra de Balanceador de Carga para definir a forma como o tráfego é distribuído pelas VMs. Pode definir a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Crie uma regra de Load Balancer *myLoadBalancerRuleWeb* para ouvir a porta 80 no front-end *FrontendLoadBalancer* e enviar o tráfego de rede com balanceamento de carga para o pool de endereços de back-end *myBackEndPool* também usando a porta 80.

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos**e clique em **myLoadBalancer** na lista de recursos.
2. Em **Definições**, clique em **Regras de balanceamento de carga** e, em seguida, clique em **Adicionar**.
3. Use estes valores para configurar a regra de balanceamento de carga:

    | Definição | Valor |
    | ------- | ----- |
    | Name | Insira *myHTTPRule*. |
    | Protocol | Selecione **TCP**. |
    | Port | Insira *80*.|
    | Porta back-end | Insira *80*. |
    | Conjunto back-end | Selecione *myBackendPool*.|
    | Sonda de estado de funcionamento | Selecione *myHealthProbe*. |
    
4. Deixe o resto das predefinições e selecione **OK**.

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, você criará uma rede virtual, criará três máquinas virtuais para o pool de back-end do Load Balancer e, em seguida, instalará o IIS nas máquinas virtuais para ajudar a testar o Load Balancer.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No lado superior esquerdo da tela, selecione **criar um recurso** >  > rede redes**virtuais**.
2. Em **criar rede virtual**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Name | Introduza *myVNet*. |
    | Espaço de endereços | Enter *10.1.0.0/16*. |
    | Subscription | Selecione a sua subscrição.|
    | Resource group | Selecione o recurso existente- *myResourceGroupSLB*. |
    | Location | Selecione **Europa Ocidental**.|
    | Nome da sub-rede | Introduza *myBackendSubnet*. |
    | Sub-rede - Intervalo de endereços | Enter *10.1.0.0/24*. |
    
3. Deixe o restante dos padrões e selecione **criar**.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Standard Load Balancer só dá suporte a VMs com endereços IP padrão no pool de back-end. Nesta seção, você criará três VMs (*myVM1*, *myVM2*e *MYVM3*) com um endereço IP público padrão em três zonas diferentes (*zona 1*, *zona 2*e *zona 3*) que são adicionadas ao pool de back-end do padrão Load Balancer que foi criado anteriormente.

1. No lado do canto superior esquerdo do portal, selecione **criar um recurso** > **computação** > **Windows Server 2016 Datacenter**. 
   
1. Na **criar uma máquina virtual**, escreva ou selecione os seguintes valores no **Noções básicas** separador:
   -  > **Grupo de recursos**de assinatura: Selecione **myResourceGroupSLB**.
   - **Detalhes da instância** **nome da máquina virtual:**  >  Digite *myVM1*.
   -  > A**região** detalhes da instância > selecione **Europa Ocidental**.
   -  > **Opções de disponibilidade** de detalhes da instância > Selecionar **zonas de disponibilidade**. 
   - Detalhes > da instância >**zona de disponibilidade** selecione **1**.
  
1. Selecione a guia **rede** ou selecione **avançar: Discos**e, **em seguida: Rede**. 
   
   - Certifique-se de que estão selecionadas as seguintes:
       - **Rede virtual**: **myVnet**
       - **Sub-rede**: **myBackendSubnet**
       - **> IP público** selecione **criar novo**e, na janela **criar endereço IP público** , para **SKU**, selecione **Standard**e para zona de **disponibilidade**, selecione **com redundância de zona**
      
   - Para criar um novo grupo de segurança rede (NSG), um tipo de firewall, em **grupo de segurança de rede**, selecione **avançadas**. 
       1. Na **configurar grupo de segurança de rede** campo, selecione **criar nova**. 
       1. Digite *myNetworkSecurityGroup*e selecione **OK**.

   - Para tornar a VM uma parte do pool de back-end Load Balancer, conclua as seguintes etapas:
        - No **balanceamento de carga**, para **posicionar esta máquina virtual atrás de uma solução de balanceamento de carga existente?** , selecione **Sim**.
        - Em **configurações de balanceamento**de carga, para **Opções de balanceamento de carga**, selecione **Azure Load**Balancer.
        - Para **selecionar um balanceador de carga**, *myLoadBalancer*. 
1. Selecione o **gerenciamento** separador ou selecione **próxima** > **gestão**. Sob **monitorização**, defina **diagnósticos de arranque** para **desativar**. 
1. Selecione **Rever + criar**.   
1. Reveja as definições e, em seguida, selecione **criar**.
1. Siga as etapas para criar duas VMs adicionais- *myVM2* e *myVM3*, com um endereço IP público de SKU padrão na **zona de disponibilidade** **2** e **3** , respectivamente, e todas as outras configurações, como *myVM1*.  

### <a name="create-network-security-group-rule"></a>Criar regra de grupo de segurança de rede

Nesta seção, você cria uma regra de grupo de segurança de rede para permitir conexões de entrada usando HTTP.

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos**e, na lista recursos, clique em **myNetworkSecurityGroup** que está localizado no grupo de recursos **myResourceGroupSLB** .
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

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos**e, na lista recursos, clique em **myVM1** que está localizado no grupo de recursos *myResourceGroupSLB* .
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

## <a name="test-the-load-balancer"></a>Testar o Load Balancer
1. Localize o endereço IP público do Balanceador de Carga no ecrã **Descrição geral**. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos**e clique em **myPublicIP**.

2. Copie o endereço IP público e cole-o na barra de endereço do browser. A página predefinida do servidor Web do IIS é apresentada no browser.

      ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver a Load Balancer distribuir o tráfego entre as três VMs que executam seu aplicativo, você pode forçar a atualização do navegador da Web.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Remover ou adicionar VMs do conjunto de back-end
Pode ter de realizar a manutenção nas VMs que estão a executar a aplicação, como instalar atualizações do SO. Para lidar com maior volume de tráfego para a aplicação, pode ter de adicionar mais VMs. Esta seção mostra como remover ou adicionar uma VM (*myVM1*) da Load Balancer.

### <a name="remove-vm-from-a-backend-pool"></a>Remover VM de um pool de back-end
Para remover o *myVM1* do pool de back-end, conclua as seguintes etapas:

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos**e clique em **myLoadBalancer** na lista de recursos.
2. Em **Definições**, clique em **Conjuntos de back-end** e, em seguida, na lista de conjuntos de back-end, clique em **myBackendPool**.
3. Na página **myBackendPool** , para remover *VM1* , selecione o ícone Excluir no final da linha que exibe *myVM1*e, em seguida, clique em **salvar**.

Como *myVM1* já não está no conjunto de endereços de back-end, pode realizar quaisquer tarefas de manutenção na *myVM1*, como a instalação de atualizações de software. Na ausência de *VM1*, a carga agora é balanceada entre *myVM2* e *myVM3*. 

### <a name="add-vm-to-a-backend-pool"></a>Adicionar VM a um pool de back-end
Para adicionar o *myVM1* de volta ao pool de back-end, conclua as seguintes etapas:

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos**e, em seguida, selecione **myVM1** na lista de recursos.
2. Na página **VM1** , em **configurações**, selecione **rede**.
3. Na página **rede** , selecione a guia **balanceamento de carga** e, em seguida, selecione **Adicionar balanceamento de carga**.
4. Na página **Adicionar balanceamento de carga** , faça o seguinte:
   1. Para **Opções de balanceamento de carga**, selecione **Azure Load Balancer**.
   2. Para **selecionar um balanceador de carga**, selecione *myLoadBalancer*.
   3. Para **selecionar um pool de back-end**, selecione *myBackendPool*. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando eles não forem mais necessários, exclua o grupo de recursos, Load Balancer e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos *myResouceGroupSLB* que contém o Load Balancer e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você criou um Standard Load Balancer, as VMs anexadas a ele, configurou a regra de tráfego Load Balancer, a investigação de integridade e testou a Load Balancer. Além disso, removeu uma VM do conjunto com balanceamento de carga e adicionou a VM de novo ao conjunto de endereços de back-end. Para saber mais sobre o Balanceador de Carga do Azure, avance para os tutoriais do Balanceador de Carga do Azure.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
