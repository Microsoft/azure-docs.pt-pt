---
title: 'Tutorial: configurar encaminhamento de porta-portal do Azure'
titleSuffix: Azure Load Balancer
description: Este tutorial mostra como configurar o encaminhamento de porta usando Azure Load Balancer para criar conexões com VMs em uma rede virtual do Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to configure port forwarding in Azure Load Balancer to remotely connect to VMs in an Azure virtual network.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 6dda01543a6a7f447adefcc6cc3cfa3ea5da5492
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048850"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>Tutorial: configurar o encaminhamento de porta no Azure Load Balancer usando o portal

O encaminhamento de porta permite que você se conecte a VMs (máquinas virtuais) em uma rede virtual do Azure usando um Azure Load Balancer endereço IP público e o número da porta. 

Neste tutorial, você configura o encaminhamento de porta em um Azure Load Balancer. Saiba como:

> [!div class="checklist"]
> * Crie um balanceador de carga padrão público para balancear o tráfego de rede em VMs. 
> * Crie uma rede virtual e VMs com uma regra de NSG (grupo de segurança de rede). 
> * Adicione as VMs ao pool de endereços de back-end do balanceador de carga.
> * Crie uma investigação de integridade e regras de tráfego do balanceador de carga.
> * Criar regras de encaminhamento de porta NAT de entrada do balanceador de carga.
> * Instale e configure o IIS nas VMs para exibir o balanceamento de carga e o encaminhamento de porta em ação.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

Para todas as etapas deste tutorial, entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Criar um balanceador de carga padrão

Primeiro, crie um balanceador de carga padrão público que possa balancear a carga de tráfego em VMs. Um balanceador de carga padrão dá suporte apenas a um endereço IP público padrão. Ao criar um balanceador de carga padrão, você também cria um novo endereço IP público padrão, que é configurado como o front-end do balanceador de carga e chamado **LoadBalancerFrontEnd** por padrão. 

1. No canto superior esquerdo do ecrã, clique em **Criar um recurso** > **Rede** > **Balanceador de Carga**.
2. Na guia **noções básicas** da página **criar balanceador de carga** , insira ou selecione as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **revisar + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **criar novo** e digite *MyResourceGroupLB* na caixa de texto.|
    | Nome                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Tipo          | Selecione **público**.                                        |
    | SKU           | Selecione **padrão**.                          |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público              | Digite *myPublicIP* na caixa de texto.   |
    |Zona de disponibilidade| Selecione a **zona com redundância**.    |
     
    >[!NOTE]
     >Certifique-se de criar seu Load Balancer e todos os recursos para ele em um local que ofereça suporte a Zonas de Disponibilidade. Para obter mais informações, consulte [regiões que dão suporte a zonas de disponibilidade](../availability-zones/az-overview.md#services-support-by-region). 

3. Na guia **revisar + criar** , clique em **criar**.  
  
## <a name="create-and-configure-back-end-servers"></a>Criar e configurar servidores back-end

Crie uma rede virtual com duas máquinas virtuais e adicione as VMs ao pool de back-end do balanceador de carga. 

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No lado do canto superior esquerdo do portal, selecione **criar um recurso** > **rede** > **rede Virtual**.
   
1. Na **criar rede virtual** painel, escreva ou selecione estes valores:
   
   - **Nome**: tipo *MyVNet*.
   - **ResourceGroup**: lista pendente **selecionar existente** e selecione **MyResourceGroupLB**. 
   - **Sub-rede** > **nome**: tipo *MyBackendSubnet*.
   
1. Selecione **Criar**.

   ![Criar uma rede virtual](./media/tutorial-load-balancer-port-forwarding-portal/2-load-balancer-virtual-network.png)

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>Criar VMs e adicioná-las ao pool de back-end do balanceador de carga

1. No lado do canto superior esquerdo do portal, selecione **criar um recurso** > **computação** > **Windows Server 2016 Datacenter**. 
   
1. Na **criar uma máquina virtual**, escreva ou selecione os seguintes valores no **Noções básicas** separador:
   - **Subscrição** > **grupo de recursos**: Drop baixo e selecione **MyResourceGroupLB**.
   - **Nome da máquina virtual**: digite *MyVM1*.
   - **Região**: selecione **Europa Ocidental**. 
   - **Nome de usuário**: digite *azureuser*.
   - **Senha**: digite *Azure1234567*. 
     Digite a senha novamente no campo **Confirmar senha** .
   
1. Selecione o **Networking** separador ou selecione **próximo: discos**, em seguida, **seguinte: redes**. 
   
   Certifique-se de que estão selecionadas as seguintes:
   - **Rede virtual**: **MyVNet**
   - **Sub-rede**: **MyBackendSubnet**
   
1. Em **IP público**, selecione **criar novo**, selecione **padrão** na página **criar endereço IP público** e selecione **OK**. 
   
1. Em **grupo de segurança de rede**, selecione **avançado** para criar um novo NSG (grupo de segurança de rede), um tipo de firewall. 
   1. Na **configurar grupo de segurança de rede** campo, selecione **criar nova**. 
   1. Tipo *MyNetworkSecurityGroup*e selecione **OK**. 
   
   >[!NOTE]
   >Observe que, por padrão, o NSG já tem uma regra de entrada para abrir a porta 3389, a porta da área de trabalho remota (RDP).
   
1. Adicione a VM a um pool de back-end do balanceador de carga que você criar:
   
   1. Em **balanceamento de carga** > **Coloque esta máquina virtual atrás de uma solução de balanceamento de carga existente?** , selecione **Sim**. 
   1. Para **Opções de balanceamento de carga**, menu suspenso e selecione **Azure Load Balancer**. 
   1. Para **selecionar um balanceador de carga**, clique no menu suspenso e selecione **MyLoadBalancer**. 
   1. Em **selecionar um pool de back-end**, selecione **criar novo**, digite *MyBackendPool*e selecione **criar**. 
   
   ![Criar uma rede virtual](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. Selecione o **gerenciamento** separador ou selecione **próxima** > **gestão**. Sob **monitorização**, defina **diagnósticos de arranque** para **desativar**.
   
1. Selecione **Rever + criar**.
   
1. Examine as configurações e, quando a validação tiver sucesso, selecione **criar**. 

1. Siga os passos para criar uma segunda VM denominada *MyVM2*, com todas as outras definições igual MyVM1. 
   
   Para o **grupo de segurança de rede**, depois de selecionar **avançado**, menu suspenso e selecione o **MyNetworkSecurityGroup** que você já criou. 
   
   Em **selecionar um pool de back-end**, verifique se **MyBackendPool** está selecionado. 

### <a name="create-an-nsg-rule-for-the-vms"></a>Criar uma regra de NSG para as VMs

Crie uma regra de NSG (grupo de segurança de rede) para as VMs para permitir conexões de Internet (HTTP) de entrada.

>[!NOTE]
>Por padrão, o NSG já tem uma regra que abre a porta 3389, a porta da área de trabalho remota (RDP).

1. Selecione **Todos os recursos** no menu esquerdo. Na lista de recursos, selecione **MyNetworkSecurityGroup** no **MyResourceGroupLB** grupo de recursos.
   
1. Em **Definições**, selecione **Regras de segurança de entrada** e, em seguida, selecione **Adicionar**.
   
1. Na caixa de diálogo **Adicionar regra de segurança de entrada** , digite ou selecione o seguinte:
   
   - **Origem**: selecione **etiqueta de serviço**.  
   - **Etiqueta de serviço de origem**: selecione **Internet**. 
   - **Intervalos de portas de destino**: tipo *80*.
   - **Protocolo**: selecione **TCP**. 
   - **Ação**: selecione **permitir**.  
   - **Prioridade**: tipo *100*. 
   - **Nome**: tipo *MyHTTPRule*. 
   - **Descrição**: tipo *permitir HTTP*. 
   
1. Selecione **Adicionar**. 
   
   ![Criar uma regra NSG](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta seção, você inspeciona o pool de back-end do balanceador de carga e configura as regras de tráfego e investigação de integridade do balanceador de carga.

### <a name="view-the-back-end-address-pool"></a>Exibir o pool de endereços de back-end

Para distribuir o tráfego para as VMs, o balanceador de carga usa um pool de endereços de back-end, que contém os endereços IP das NICs (interfaces de rede virtual) que estão conectadas ao balanceador de carga. 

Você criou o pool de back-end do balanceador de carga e adicionou VMs a ele quando você criou as VMs. Você também pode criar pools de back-end e adicionar ou remover VMs da página **pools de back-end** do balanceador de carga. 

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Conjuntos de back-end**.
   
1. Sobre o **conjuntos de back-end** página, expanda **MyBackendPool** e certifique-se de ambos **VM1** e **VM2** estão listados.

1. Selecione **MyBackendPool**. 
   
   Na página **MyBackendPool** , em **máquina virtual** e **endereço IP**, você pode remover ou adicionar VMs disponíveis ao pool.

Você pode criar novos pools de back-end selecionando **Adicionar** na página **pools de back-end** .

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir que o Balanceador de carga monitorizar o estado da VM, pode utilizar uma sonda de estado de funcionamento. A sonda de estado de funcionamento adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na respetiva resposta às verificações de estado de funcionamento. 

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Sondas de estado de funcionamento** e, em seguida, selecione **Adicionar**.
   
1. Na página **Adicionar investigação de integridade** , digite ou selecione os seguintes valores:
   
   - **Nome**: tipo *MyHealthProbe*.
   - **Protocolo**: remova baixo e selecione **HTTP**. 
   - **Porta**: tipo *80*. 
   - **Caminho**: aceite */* para o URI predefinido. Pode substituir este valor com qualquer outro URI. 
   - **Intervalo**: tipo *15*. O intervalo é o número de segundos entre tentativas da sonda.
   - **Limiar de mau estado de funcionamento**: tipo *2*. Este valor é o número de falhas consecutivas da sonda que ocorrem antes de uma VM é considerada em mau estado de funcionamento.
   
1. Selecione **OK**.
   
   ![Adicionar uma sonda](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

As regras de balanceador de carga definem a forma como o tráfego é distribuído pelas VMs. A regra define a configuração de IP Front-end para tráfego de entrada, o conjunto IP de back-end para receber o tráfego e as portas de origem e de destino necessárias. 

A regra de Balanceador de carga com o nome **MyLoadBalancerRule** escuta na porta 80 no front-end **LoadBalancerFrontEnd**. A regra envia o tráfego de rede para o conjunto de endereços de back-end **MyBackendPool**, também na porta 80. 

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Regras de balanceamento de carga** e, em seguida, selecione **Adicionar**.
   
1. Sobre o **Adicionar regra de balanceamento de carga** página, escreva ou selecione os seguintes valores:
   
   - **Nome**: tipo *MyLoadBalancerRule*.
   - **Protocolo**: selecione **TCP**.
   - **Porta**: tipo *80*.
   - **Porta de back-end**: tipo *80*.
   - **Conjunto back-end**: selecione **MyBackendPool**.
   - **Sonda de estado de funcionamento**: selecione **MyHealthProbe**. 
   
1. Selecione **OK**.
   
   ![Adicionar uma regra de Balanceador de carga](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>Criar uma regra de encaminhamento de porta NAT de entrada

Crie uma regra NAT (conversão de endereço de rede) de entrada do balanceador de carga para encaminhar o tráfego de uma porta específica do endereço IP de front-end para uma porta específica de uma VM de back-end.

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **configurações**, selecione **regras de NAT de entrada**e, em seguida, selecione **Adicionar**. 
   
1. Na página **Adicionar regra NAT de entrada** , digite ou selecione os seguintes valores:
   
   - **Nome**: digite *MyNATRuleVM1*.
   - **Porta**: digite *4221*.
   - **Máquina virtual de destino**: selecione **MyVM1** na lista suspensa.
   - **Configuração de IP de rede**: selecione **ipconfig1** na lista suspensa.
   - **Mapeamento de porta**: selecione **personalizado**.
   - **Porta de destino**: tipo *3389*.
   
1. Selecione **OK**.
   
1. Repita as etapas para adicionar uma regra NAT de entrada chamada *MyNATRuleVM2*, usando a **porta**: *4222* e a **máquina virtual de destino**: **MyVM2**.

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Nesta seção, você instalará Serviços de Informações da Internet (IIS) nos servidores back-end e personalizará a página da Web padrão para mostrar o nome do computador. Em seguida, você usará o endereço IP público do balanceador de carga para testar o balanceador de carga. 

Cada VM de back-end funciona de uma versão diferente do que a página de web do IIS predefinida, para que possa ver o Balanceador de carga a distribuir os pedidos entre as duas VMs.

### <a name="connect-to-the-vms-with-rdp"></a>Ligar as VMs com RDP

Conecte-se a cada VM com Área de Trabalho Remota (RDP). 

1. No portal, selecione **todos os recursos** no menu da esquerda. Na lista de recursos, selecione cada VM no **MyResourceGroupLB** grupo de recursos.
   
1. Sobre o **descrição geral** página, selecione **Connect**e, em seguida, selecione **ficheiro RDP transferir**. 
   
1. Abra o RDP de ficheiros que transferiu e selecione **Connect**.
   
1. No ecrã de segurança do Windows, selecione **mais escolhas** e, em seguida **utilizar uma conta diferente**. 
   
   Insira o nome de usuário *azureuser* e a senha *Azure1234567*e selecione **OK**.
   
1. Responder **Sim** para qualquer linha de comandos do certificado. 
   
   A área de trabalho da VM abre-se numa nova janela. 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>Instalar o IIS e substituir a página da Web padrão do IIS 

Use o PowerShell para instalar o IIS e substituir a página da Web padrão do IIS por uma página que exibe o nome da VM.

1. MyVM1 e MyVM2, inicie **Windows PowerShell** partir a **iniciar** menu. 

2. Execute os seguintes comandos para instalar o IIS e substitua a página de web do IIS predefinida:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. Fechar as ligações de RDP com MyVM1 e MyVM2 selecionando **desligar**. Não desligue as VMs.

### <a name="test-load-balancing"></a>Testar balanceamento de carga

1. No portal, na página **visão geral** de **MyLoadBalancer**, copie o endereço IP público em **endereço IP público**. Coloque o cursor sobre o endereço e selecione o **cópia** ícone para copiá-lo. Neste exemplo, é **40.67.218.235**. 
   
1. Cole ou digite o endereço IP público do balanceador de carga (*40.67.218.235*) na barra de endereços do seu navegador da Internet. 
   
   A página de predefinição do servidor de web IIS personalizada é apresentada no browser. A mensagem lê um **Olá, mundo de MyVM1**, ou **Hello World do MyVM2**.
   
   ![Nova página de padrão do IIS](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. Atualize o browser para ver o Balanceador de carga a distribuir tráfego pelas VMs. Por vezes, o **MyVM1** é apresentada a página e outras vezes a **MyVM2** página for apresentada, conforme o Balanceador de carga distribui os pedidos para cada VM de back-end.
   
   >[!NOTE]
   >Talvez seja necessário limpar o cache do navegador ou abrir uma nova janela do navegador entre as tentativas.

## <a name="test-port-forwarding"></a>Testar o reencaminhamento de portas

Com o encaminhamento de porta, você pode usar a área de trabalho remota para uma VM de back-end usando o endereço IP do balanceador de carga e o valor da porta de front-end definido na regra NAT. 

1. No portal, na página **visão geral** de **MyLoadBalancer**, copie seu endereço IP público. Coloque o cursor sobre o endereço e selecione o **cópia** ícone para copiá-lo. Neste exemplo, é **40.67.218.235**. 
   
1. Abra um prompt de comando e use o comando a seguir para criar uma sessão de área de trabalho remota com MyVM2, usando o endereço IP público do balanceador de carga e a porta de front-end que você definiu na regra NAT da VM. 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. Abra o arquivo RDP baixado e selecione **conectar**.
   
1. No ecrã de segurança do Windows, selecione **mais escolhas** e, em seguida **utilizar uma conta diferente**. 
   
   Insira o nome de usuário *azureuser* e a senha *Azure1234567*e selecione **OK**.
   
1. Responder **Sim** para qualquer linha de comandos do certificado. 
   
   A área de trabalho do MyVM2 é aberta em uma nova janela. 

A conexão RDP é realizada com sucesso, pois a regra NAT de entrada **MyNATRuleVM2** direciona o tráfego da porta de front-end do balanceador de carga 4222 para a porta MyVM2's 3389 (a porta RDP).

## <a name="clean-up-resources"></a>Limpar recursos

Para eliminar o Balanceador de carga e todos os recursos relacionados, quando já não precisar delas, abra a **MyResourceGroupLB** recursos de grupo e selecione **eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você criou um balanceador de carga público padrão. Criar e configurar recursos de rede, servidores de back-end, uma sonda de estado de funcionamento e regras de Balanceador de carga. Você instalou o IIS nas VMs de back-end e usou o endereço IP público do balanceador de carga para testar o balanceador de carga. Você configura e testou o encaminhamento de porta de uma porta especificada no balanceador de carga para uma porta em uma VM de back-end. 

Para saber mais sobre Azure Load Balancer, continue para obter mais tutoriais do balanceador de carga.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
