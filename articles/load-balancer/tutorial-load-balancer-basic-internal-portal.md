---
title: 'Tutorial: Criar um balanceador de carga interno – portal do Azure'
titlesuffix: Azure Load Balancer
description: Este tutorial mostra-lhe como criar um balanceador de carga básico interno com o portal do Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internal traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: b8600ed03140e302c730d44c6410d2020b7c48a3
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56983188"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-basic-load-balancer-in-the-azure-portal"></a>Tutorial: Carga de tráfego interno de saldo com um balanceador de carga básico no portal do Azure

Balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por máquinas virtuais (VMs). Pode utilizar o portal do Azure para criar um balanceador de carga básico e balancear o tráfego entre VMs interno. Este tutorial mostra-lhe como criar e configurar um balanceador de carga interno, servidores de back-end e recursos de rede ao escalão de preço básico.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

Se preferir, pode fazer estes passos com o [CLI do Azure](load-balancer-get-started-ilb-arm-cli.md) ou [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) em vez do portal.

Para efetuar os passos neste tutorial, inicie sessão no portal do Azure em [ https://portal.azure.com ](https://portal.azure.com).

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>Criar uma VNet, servidores de back-end e uma VM de teste

Primeiro, crie uma rede virtual (VNet). Crie a VNet, duas VMs a utilizar para o conjunto de back-end do Balanceador de carga básico e uma terceira VM para utilizar para testar o Balanceador de carga. 

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No lado do canto superior esquerdo do portal, selecione **criar um recurso** > **rede** > **rede Virtual**.
   
1. Na **criar rede virtual** painel, escreva ou selecione estes valores:
   
   - **Nome**: Tipo *MyVNet*.
   - **ResourceGroup**: Selecione **criar novo**, em seguida, introduza *MyResourceGroupLB*e selecione **OK**. 
   - **Sub-rede** > **nome**: Tipo *MyBackendSubnet*.
   
1. Selecione **Criar**.

   ![Criar uma rede virtual](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. No lado do canto superior esquerdo do portal, selecione **criar um recurso** > **computação** > **Windows Server 2016 Datacenter**. 
   
1. Na **criar uma máquina virtual**, escreva ou selecione os seguintes valores no **Noções básicas** separador:
   - **Subscrição** > **grupo de recursos**: Lista pendente e selecione **MyResourceGroupLB**.
   - **Detalhes de instância** > **nome da Máquina Virtual**: Tipo *MyVM1*.
   - **Detalhes de instância** > **opções de disponibilidade**: 
     1. Lista pendente e selecione **conjunto de disponibilidade**. 
     2. Selecione **criar novo**, tipo *MyAvailabilitySet*e selecione **OK**.
   
1. Selecione o **Networking** separador ou selecione **seguinte: Discos**, em seguida, **seguinte: Funcionamento em rede**. 
   
   Certifique-se de que estão selecionadas as seguintes:
   - **Rede virtual**: **MyVNet**
   - **Sub-rede**: **MyBackendSubnet**
   
   Sob **grupo de segurança de rede**:
   1. Selecione **Avançadas**. 
   1. Menu pendente **configurar grupo de segurança de rede** e selecione **nenhum**. 
   
1. Selecione o **gerenciamento** separador ou selecione **próxima** > **gestão**. Sob **monitorização**, defina **diagnósticos de arranque** para **desativar**.
   
1. Selecione **Rever + criar**.
   
1. Reveja as definições e, em seguida, selecione **criar**. 

1. Siga os passos para criar uma segunda VM denominada *MyVM2*, com todas as outras definições igual MyVM1. 

1. Siga os passos para criar uma terceira VM com o nome *MyTestVM*. 

## <a name="create-a-basic-load-balancer"></a>Criar um balanceador de carga Básico

Crie um balanceador de carga interno básico com o portal. O nome e endereço IP que cria são automaticamente configurados como front-end de Balanceador de carga.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **Balanceador de Carga**.
   
2. Na **Noções básicas** separador da **criar Balanceador de carga** página, introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **rever + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **criar novo** e escreva *MyResourceGroupLB* na caixa de texto.|
    | Name                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Type          | Selecione **público**.                                        |
    | SKU           | Selecione **Básico**.                          |
    | Rede virtual           | Selecione *MyVNet*.                          |    
| Endereço IP público | Selecione **Criar novo**. |
    | Atribuição de endereços IP pública              | Selecione **estático**.   |
    | Endereço IP privado|Escreva um endereço que está no espaço de endereços da sua rede virtual e sub-rede, por exemplo *10.3.0.7*.  |

3. Na **rever + criar** separador, clique em **criar**. 
   

## <a name="create-basic-load-balancer-resources"></a>Criar recursos do Balanceador de carga básico

Nesta secção, configurar definições de Balanceador de carga para um conjunto de endereços de back-end e uma sonda de estado de funcionamento e especifique as regras do Balanceador de carga.

### <a name="create-a-back-end-address-pool"></a>Criar um conjunto de endereços de back-end

Para distribuir o tráfego para as VMs, o Balanceador de carga utiliza um conjunto de endereços de back-end. O conjunto de endereços de back-end contém os endereços IP das interfaces de rede virtuais (NICs) que estão ligadas ao balanceador de carga. 

**Para criar um conjunto de endereços de back-end que inclua a VM1 e VM2:**

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Conjuntos de back-end** e, em seguida, selecione **Adicionar**.
   
1. Sobre o **adicionar um conjunto de back-end** página, escreva ou selecione os seguintes valores:
   
   - **Nome**: Tipo *MyBackendPool*.
   - **Associados a**: Lista pendente e selecione **conjunto de disponibilidade**.
   - **Conjunto de disponibilidade**: Selecione **MyAvailabilitySet**.
   
1. Selecione **adicionar uma configuração de IP de rede de destino**. 
   1. Adicione **MyVM1** e **MyVM2** para o conjunto de back-end.
   2. Depois de adicionar cada máquina, o menu pendente e selecione seu **configuração do IP de rede**. 
   
   >[!NOTE]
   >Não adicione **MyTestVM** ao conjunto. 
   
1. Selecione **OK**.
   
   ![Adicionar o conjunto de endereços de back-end](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)
   
1. Sobre o **conjuntos de back-end** página, expanda **MyBackendPool** e certifique-se de ambos **VM1** e **VM2** estão listados.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir que o Balanceador de carga monitorizar o estado da VM, pode utilizar uma sonda de estado de funcionamento. A sonda de estado de funcionamento adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na respetiva resposta às verificações de estado de funcionamento. 

**Para criar uma sonda de estado de funcionamento para monitorizar o estado de funcionamento das VMs:**

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Sondas de estado de funcionamento** e, em seguida, selecione **Adicionar**.
   
1. Sobre o **adicionar uma sonda de estado de funcionamento** página, escreva ou selecione os seguintes valores:
   
   - **Nome**: Tipo *MyHealthProbe*.
   - **Protocolo**: Lista pendente e selecione **HTTP**. 
   - **Porta**: Tipo *80*. 
   - **Caminho**: Aceite */* para o URI predefinido. Pode substituir este valor com qualquer outro URI. 
   - **Intervalo**: Tipo *15*. O intervalo é o número de segundos entre tentativas da sonda.
   - **Limiar de mau estado de funcionamento**: Tipo *2*. Este valor é o número de falhas consecutivas da sonda que ocorrem antes de uma VM é considerada em mau estado de funcionamento.
   
1. Selecione **OK**.
   
   ![Adicionar uma sonda](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Crie uma regra de balanceador de carga

As regras de balanceador de carga definem a forma como o tráfego é distribuído pelas VMs. A regra define a configuração de IP Front-end para tráfego de entrada, o conjunto IP de back-end para receber o tráfego e as portas de origem e de destino necessárias. 

A regra de Balanceador de carga com o nome **MyLoadBalancerRule** escuta na porta 80 no front-end **LoadBalancerFrontEnd**. A regra envia o tráfego de rede para o conjunto de endereços de back-end **MyBackendPool**, também na porta 80. 

**Para criar a regra de Balanceador de carga:**

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Regras de balanceamento de carga** e, em seguida, selecione **Adicionar**.
   
1. Sobre o **Adicionar regra de balanceamento de carga** página, escreva ou selecione os seguintes valores, se não estiver já presente:
   
   - **Nome**: Tipo *MyLoadBalancerRule*.
   - **Endereço IP de front-end:** Tipo *LoadBalancerFrontEnd* se não estiver presente.
   - **Protocolo**: Selecione **TCP**.
   - **Porta**: Tipo *80*.
   - **Porta de back-end**: Tipo *80*.
   - **Conjunto back-end**: Selecione **MyBackendPool**.
   - **Sonda de estado de funcionamento**: Select **MyHealthProbe**. 
   
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
    
    #Add custom htm file
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

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou um balanceador de carga interno de escalão Basic. Criar e configurar recursos de rede, servidores de back-end, uma sonda de estado de funcionamento e regras de Balanceador de carga. Instalou o IIS nas VMs de back-end e utilizado uma VM de teste para testar o Balanceador de carga no browser. 

Em seguida, saiba como balancear carga de VMs por zonas de disponibilidade.

> [!div class="nextstepaction"]
> [Balancear carga de VMs por zonas de disponibilidade](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
