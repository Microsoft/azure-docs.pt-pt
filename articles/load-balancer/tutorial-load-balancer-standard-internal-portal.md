---
title: 'Tutorial: criar um balanceador de carga interno-portal do Azure'
titleSuffix: Azure Load Balancer
description: Este tutorial mostra como criar um balanceador de carga padrão interno usando o portal do Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internal traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2020
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 918a7700df6b5be3ebca7949875127e42f8d3a91
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780382"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-standard-load-balancer-in-the-azure-portal"></a>Tutorial: balancear a carga de tráfego interno com um balanceador de carga padrão no portal do Azure

Balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por máquinas virtuais (VMs). Você pode usar o portal do Azure para criar um balanceador de carga padrão e balancear o tráfego interno entre VMs. Este tutorial mostra como criar e configurar um balanceador de carga interno, servidores back-end e recursos de rede no tipo de preço Standard.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

Se preferir, pode fazer estes passos com o [CLI do Azure](load-balancer-get-started-ilb-arm-cli.md) ou [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) em vez do portal.

Para efetuar os passos neste tutorial, inicie sessão no portal do Azure em [ https://portal.azure.com ](https://portal.azure.com).

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>Criar uma VNet, servidores de back-end e uma VM de teste

Primeiro, crie uma rede virtual (VNet). Na VNet, crie duas VMs a serem usadas para o pool de back-end do balanceador de carga padrão e uma terceira VM a ser usada para testar o balanceador de carga. 

### <a name="create-a-virtual-network"></a>Criar rede virtual

1. No lado do canto superior esquerdo do portal, selecione **criar um recurso** > **rede** > **rede Virtual**.
   
1. Na **criar rede virtual** painel, escreva ou selecione estes valores:
   
   - **Nome**: tipo **MyVNet**.
   - **ResourceGroup**: selecione **criar novo**, em seguida, introduza **MyResourceGroupLB**e selecione **OK**. 
   - **Sub-rede** > **nome**: tipo **MyBackendSubnet**.
   
1. Selecione **Criar**.

   ![Criar rede virtual](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. No lado do canto superior esquerdo do portal, selecione **criar um recurso** > **computação** > **Windows Server 2016 Datacenter**. 
   
1. Na **criar uma máquina virtual**, escreva ou selecione os seguintes valores no **Noções básicas** separador:
   - **Subscrição** > **grupo de recursos**: Drop baixo e selecione **MyResourceGroupLB**.
   - **Detalhes de instância** > **nome da Máquina Virtual**: tipo **MyVM1**.
   - **Detalhes da instância** > **região**: selecione **leste dos EUA 2**.
  
   
1. Selecione o **Networking** separador ou selecione **próximo: discos**, em seguida, **seguinte: redes**. 
   
   Certifique-se de que estão selecionadas as seguintes:
   - **Rede virtual**: **MyVNet**
   - **Sub-rede**: **MyBackendSubnet**
   - **Grupo de segurança de rede NIC**: selecione **básico**.
   - > **IP público** selecione **criar novo** e insira os seguintes valores e selecione **OK**:
       - **Nome**: **MyVM1-IP**
       - **SKU**: selecione **Standard**
   - **Portas de entrada públicas**: selecione **permitir portas selecionadas**.
   - **Selecione portas de entrada**: lista suspensa e selecione **RDP (3389)**

   
   
1. Selecione o **gerenciamento** separador ou selecione **próxima** > **gestão**. Sob **monitorização**, defina **diagnósticos de arranque** para **desativar**.
   
1. Selecione **Rever + criar**.
   
1. Reveja as definições e, em seguida, selecione **criar**. 

1. Siga os passos para criar uma segunda VM denominada **MyVM2**, com todas as outras definições igual MyVM1. 

1. Siga os passos para criar uma terceira VM com o nome **MyTestVM**. 

## <a name="create-a-standard-load-balancer"></a>Criar um balanceador de carga padrão

Crie um balanceador de carga interno padrão usando o Portal. O nome e endereço IP que cria são automaticamente configurados como front-end de Balanceador de carga.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **Balanceador de Carga**.
   
2. Na guia **noções básicas** da página **criar balanceador de carga** , insira ou selecione as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **revisar + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **criar novo** e digite *MyResourceGroupLB* na caixa de texto.|
    | Nome                   | *myLoadBalancer*                                   |
    | Região         | Selecione **E.U.A. Leste 2**.                                        |
    | Tipo          | Selecione **interno**.                                        |
    | SKU           | Selecione **padrão**.                          |
    | Rede virtual           | Selecione *MyVNet*.                          |    
    | Atribuição de endereço IP              | Selecione **Estático**.   |
    | Endereço IP privado|Digite um endereço que esteja no espaço de endereço de sua rede virtual e sub-rede, por exemplo *10.3.0.7*.  |

3. Na guia **revisar + criar** , clique em **criar**. 
   

## <a name="create-standard-load-balancer-resources"></a>Criar recursos de balanceador de carga padrão

Nesta seção, você define as configurações do balanceador de carga para um pool de endereços de back-end e uma investigação de integridade e especifica regras de balanceador de carga.

### <a name="create-a-back-end-address-pool"></a>Criar um conjunto de endereços de back-end

Para distribuir o tráfego para as VMs, o Balanceador de carga utiliza um conjunto de endereços de back-end. O conjunto de endereços de back-end contém os endereços IP das interfaces de rede virtuais (NICs) que estão ligadas ao balanceador de carga. 

**Para criar um conjunto de endereços de back-end que inclua a VM1 e VM2:**

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Conjuntos de back-end** e, em seguida, selecione **Adicionar**.
   
1. Sobre o **adicionar um conjunto de back-end** página, escreva ou selecione os seguintes valores:
   
   - **Nome**: tipo **MyBackendPool**.
   
1. Em **máquinas virtuais**. 
   1. Adicione **MyVM1** e **MyVM2** para o conjunto de back-end.
   2. Depois de adicionar cada máquina, o menu pendente e selecione seu **configuração do IP de rede**. 
     
1. Selecione **Adicionar**.
   
   ![Adicionar o conjunto de endereços de back-end](./media/tutorial-load-balancer-standard-internal-portal/3-load-balancer-backend-02.png)
   
1. Sobre o **conjuntos de back-end** página, expanda **MyBackendPool** e certifique-se de ambos **VM1** e **VM2** estão listados.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir que o Balanceador de carga monitorizar o estado da VM, pode utilizar uma sonda de estado de funcionamento. A sonda de estado de funcionamento adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na respetiva resposta às verificações de estado de funcionamento. 

**Para criar uma sonda de estado de funcionamento para monitorizar o estado de funcionamento das VMs:**

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Sondas de estado de funcionamento** e, em seguida, selecione **Adicionar**.
   
1. Sobre o **adicionar uma sonda de estado de funcionamento** página, escreva ou selecione os seguintes valores:
   
   - **Nome**: tipo **MyHealthProbe**.
   - **Protocolo**: remova baixo e selecione **HTTP**. 
   - **Porta**: tipo **80**. 
   - **Caminho**: aceite **/** para o URI predefinido. Pode substituir este valor com qualquer outro URI. 
   - **Intervalo**: tipo **15**. O intervalo é o número de segundos entre tentativas da sonda.
   - **Limiar de mau estado de funcionamento**: tipo **2**. Este valor é o número de falhas consecutivas da sonda que ocorrem antes de uma VM é considerada em mau estado de funcionamento.
   
1. Selecione **OK**.
   
   ![Adicionar uma sonda](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

As regras de balanceador de carga definem a forma como o tráfego é distribuído pelas VMs. A regra define a configuração de IP Front-end para tráfego de entrada, o conjunto IP de back-end para receber o tráfego e as portas de origem e de destino necessárias. 

A regra de Balanceador de carga com o nome **MyLoadBalancerRule** escuta na porta 80 no front-end **LoadBalancerFrontEnd**. A regra envia o tráfego de rede para o conjunto de endereços de back-end **MyBackendPool**, também na porta 80. 

**Para criar a regra de Balanceador de carga:**

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Regras de balanceamento de carga** e, em seguida, selecione **Adicionar**.
   
1. Sobre o **Adicionar regra de balanceamento de carga** página, escreva ou selecione os seguintes valores, se não estiver já presente:
   
   - **Nome**: tipo **MyLoadBalancerRule**.
   - **Endereço IP de front-end:** tipo **LoadBalancerFrontEnd** se não estiver presente.
   - **Protocolo**: selecione **TCP**.
   - **Porta**: tipo **80**.
   - **Porta de back-end**: tipo **80**.
   - **Conjunto back-end**: selecione **MyBackendPool**.
   - **Sonda de estado de funcionamento**: selecione **MyHealthProbe**. 
   
1. Selecione **OK**.
   
   ![Adicionar uma regra de Balanceador de carga](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Instalar serviços de informação Internet (IIS) nos servidores de back-end, em seguida, utilizar MyTestVM para testar o Balanceador de carga com o respetivo endereço IP privado. Cada VM de back-end funciona de uma versão diferente do que a página de web do IIS predefinida, para que possa ver o Balanceador de carga a distribuir os pedidos entre as duas VMs.

No portal, no **descrição geral** página **MyLoadBalancer**, encontrar o seu endereço IP em **Private IP Address**. Coloque o cursor sobre o endereço e selecione o **cópia** ícone para copiá-lo. Neste exemplo, é **10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Ligar as VMs com RDP

Em primeiro lugar, ligue-se a todas as três VMs com RDP (Remote Desktop). 

>[!NOTE]
>Por predefinição, as VMs já têm o **RDP** abrir o porta (ambiente de trabalho remoto) para permitir o acesso de ambiente de trabalho remoto. 

**Para o ambiente de trabalho remoto (RDP) para as VMs:**

1. No portal, selecione **todos os recursos** no menu da esquerda. Na lista de recursos, selecione cada VM no **MyResourceGroupLB** grupo de recursos.
   
1. Sobre o **descrição geral** página, selecione **Connect**e, em seguida, selecione **ficheiro RDP transferir**. 
   
1. Abra o RDP de ficheiros que transferiu e selecione **Connect**.
   
1. No ecrã de segurança do Windows, selecione **mais escolhas** e, em seguida **utilizar uma conta diferente**. 
   
   Introduza o nome de utilizador e palavra-passe e, em seguida, selecione **OK**.
   
1. Responder **Sim** para qualquer linha de comandos do certificado. 
   
   A área de trabalho da VM abre-se numa nova janela. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>Instalar o IIS e substitua a página predefinida do IIS, nas VMs de back-end

Em cada servidor de back-end, utilize o PowerShell para instalar o IIS e substitua a página de web do IIS predefinida de uma página personalizada.

>[!NOTE]
>Também pode utilizar o **Assistente Adicionar funções e funcionalidades** na **Gestor de servidor** para instalar o IIS. 

**Para instalar o IIS e atualize a página web predefinida com o PowerShell:**

1. MyVM1 e MyVM2, inicie **Windows PowerShell** partir a **iniciar** menu. 

2. Execute os seguintes comandos para instalar o IIS e substitua a página de web do IIS predefinida:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
      remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add custom htm file
      Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. Fechar as ligações de RDP com MyVM1 e MyVM2 selecionando **desligar**. Não encerre as VMs.

### <a name="test-the-load-balancer"></a>Testar o balanceador de carga

1. No MyTestVM, abra **Internet Explorer**e responder **OK** para qualquer configuração pede-lhe. 
   
1. Cole ou introduza o endereço IP privado do Balanceador de carga (*10.3.0.7*) na barra de endereço do browser. 
   
   A página de predefinição do servidor de web IIS personalizada é apresentada no browser. A mensagem lê um **Olá, mundo de MyVM1**, ou **Hello World do MyVM2**.
   
1. Atualize o browser para ver o Balanceador de carga a distribuir tráfego pelas VMs. Também terá de limpar a cache do browser entre tentativas.

   Por vezes, o **MyVM1** é apresentada a página e outras vezes a **MyVM2** página for apresentada, conforme o Balanceador de carga distribui os pedidos para cada VM de back-end. 

   ![Nova página de padrão do IIS](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Limpar recursos

Para eliminar o Balanceador de carga e todos os recursos relacionados, quando já não precisar delas, abra a **MyResourceGroupLB** recursos de grupo e selecione **eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você criou um balanceador de carga interno padrão. Criar e configurar recursos de rede, servidores de back-end, uma sonda de estado de funcionamento e regras de Balanceador de carga. Instalou o IIS nas VMs de back-end e utilizado uma VM de teste para testar o Balanceador de carga no browser. 

Em seguida, saiba como balancear carga de VMs por zonas de disponibilidade.

> [!div class="nextstepaction"]
> [Balancear carga de VMs por zonas de disponibilidade](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
