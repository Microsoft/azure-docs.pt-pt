---
title: 'Tutorial: Configurar o encaminhamento de porta no balanceador de carga do Azure no portal do Azure'
titlesuffix: Azure Load Balancer
description: Este tutorial mostra como configurar o encaminhamento de porta a utilizar o Balanceador de carga do Azure para criar ligações para as VMs numa rede virtual do Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: As an IT administrator, I want to configure port forwarding in Azure Load Balancer to remotely connect to VMs in an Azure virtual network.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2018
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: e376011feacd59c84686aa3d60a7e79e07d61dbd
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672955"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>Tutorial: Configurar o encaminhamento de porta no balanceador de carga do Azure com o portal

Encaminhamento de porta permite-lhe ligar às máquinas virtuais (VMs) numa rede virtual do Azure, utilizando um balanceador de carga do Azure público endereço IP e porta número. 

Neste tutorial, vai configurar o encaminhamento de porta num Balanceador de carga do Azure. Saiba como:

> [!div class="checklist"]
> * Crie um balanceador de carga Standard público para equilibrar o tráfego de rede a ativação pós-falha nas VMs. 
> * Crie uma rede virtual e as VMs com uma regra de grupo (NSG) de segurança de rede. 
> * Adicione as VMs para o conjunto de endereços de back-end de Balanceador de carga.
> * Crie um Estado de funcionamento do Balanceador de carga com regras de tráfego e a sonda.
> * Criar Balanceador de carga regras de encaminhamento de porta NAT de entrada.
> * Instalar e configurar o IIS nas VMs para balanceamento de carga de modo de exibição e o encaminhamento de porta em ação.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

Para obter todos os passos neste tutorial, inicie sessão no portal do Azure em [ https://portal.azure.com ](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Criar um balanceador de carga Standard

Primeiro, crie um balanceador de carga Standard público que pode balancear a carga de tráfego através de VMs. Um balanceador de carga Standard oferece suporte a apenas um endereço IP público Standard. Quando cria um balanceador de carga Standard, também é criar um novo endereço IP público Standard, que é configurado como o load balancer front-end e com o nome **LoadBalancerFrontEnd** por predefinição. 

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **Balanceador de Carga**.
   
1. Na **criar Balanceador de carga** painel, escreva ou selecione estes valores:
   
   - **Nome**: Tipo *MyLoadBalancer*.
   - **Tipo de**: Selecione **público**. 
   - **SKU**: Selecione **padrão**.
   - **Endereço IP público**: Selecione **criar novo**, em seguida, escreva *MyPublicIP* no campo.
   - **Configurar o endereço IP público** > **zona de disponibilidade**: Selecione **redundância de zona**.
   - **ResourceGroup**: Selecione **criar novo**, em seguida, introduza *MyResourceGroupLB*e selecione **OK**. 
   - **Localização**: Selecione **Europa Ocidental**. 
     
     >[!NOTE]
     >Certifique-se criar o Balanceador de carga e todos os recursos para o mesmo num local que suporta zonas de disponibilidade. Para obter mais informações, consulte [regiões que suportam as zonas de disponibilidade](../availability-zones/az-overview.md#regions-that-support-availability-zones). 
   
1. Selecione **Criar**.
   
![Criar um balanceador de carga](./media/tutorial-load-balancer-port-forwarding-portal/1-load-balancer.png)

## <a name="create-and-configure-back-end-servers"></a>Criar e configurar servidores back-end

Criar uma rede virtual com duas máquinas virtuais e adicionar as VMs para o conjunto de back-end do seu Balanceador de carga. 

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No lado do canto superior esquerdo do portal, selecione **criar um recurso** > **rede** > **rede Virtual**.
   
1. Na **criar rede virtual** painel, escreva ou selecione estes valores:
   
   - **Nome**: Tipo *MyVNet*.
   - **ResourceGroup**: Menu pendente **selecionar existente** e selecione **MyResourceGroupLB**. 
   - **Sub-rede** > **nome**: Tipo *MyBackendSubnet*.
   
1. Selecione **Criar**.

   ![Criar uma rede virtual](./media/tutorial-load-balancer-port-forwarding-portal/2-load-balancer-virtual-network.png)

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>Criar VMs e adicioná-los para o conjunto de back-end de Balanceador de carga

1. No lado do canto superior esquerdo do portal, selecione **criar um recurso** > **computação** > **Windows Server 2016 Datacenter**. 
   
1. Na **criar uma máquina virtual**, escreva ou selecione os seguintes valores no **Noções básicas** separador:
   - **Subscrição** > **grupo de recursos**: Lista pendente e selecione **MyResourceGroupLB**.
   - **Nome da máquina virtual**: Tipo *MyVM1*.
   - **Região**: Selecione **Europa Ocidental**. 
   - **Nome de utilizador**: Type *azureuser*.
   - **palavra-passe**: Tipo *Azure1234567*. 
     Volte a escrever a palavra-passe no **Confirmar palavra-passe** campo.
   
1. Selecione o **Networking** separador ou selecione **seguinte: Discos**, em seguida, **seguinte: Funcionamento em rede**. 
   
   Certifique-se de que estão selecionadas as seguintes:
   - **Rede virtual**: **MyVNet**
   - **Sub-rede**: **MyBackendSubnet**
   
1. Sob **IP público**, selecione **criar novo**, selecione **padrão** sobre a **Criar endereço IP público** página e, em seguida, selecione **OK**. 
   
1. Sob **grupo de segurança de rede**, selecione **avançadas** para criar um novo grupo de segurança rede (NSG), um tipo de firewall. 
   1. Na **configurar grupo de segurança de rede** campo, selecione **criar nova**. 
   1. Tipo *MyNetworkSecurityGroup*e selecione **OK**. 
   
   >[!NOTE]
   >Observe que, por predefinição, o NSG já tem uma regra de entrada para abrir a porta 3389, a porta de área de trabalho remota (RDP).
   
1. Adicione a VM para um conjunto de back-end de Balanceador de carga que criou:
   
   1. Sob **BALANCEAMENTO de carga** > **colocar esta máquina virtual por trás de uma solução de balanceamento de carga existente?**, selecione **Sim**. 
   1. Para **opções de balanceamento de carga**, menu pendente e selecione **Balanceador de carga do Azure**. 
   1. Para **selecionar um balanceador de carga**, menu pendente e selecione **MyLoadBalancer**. 
   1. Sob **selecionar um conjunto de back-end**, selecione **criar nova**, em seguida, escreva *MyBackendPool*e selecione **criar**. 
   
   ![Criar uma rede virtual](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. Selecione o **gerenciamento** separador ou selecione **próxima** > **gestão**. Sob **monitorização**, defina **diagnósticos de arranque** para **desativar**.
   
1. Selecione **Rever + criar**.
   
1. Reveja as definições e, quando a validação tiver êxito, selecione **criar**. 

1. Siga os passos para criar uma segunda VM denominada *MyVM2*, com todas as outras definições igual MyVM1. 
   
   Para **grupo de segurança de rede**, depois de selecionar **avançadas**, o menu pendente e selecione o **MyNetworkSecurityGroup** que já criou. 
   
   Sob **selecionar um conjunto de back-end**, certifique-se **MyBackendPool** está selecionada. 

### <a name="create-an-nsg-rule-for-the-vms"></a>Criar uma regra NSG para as VMs

Crie uma regra de grupo (NSG) de segurança de rede para as VMs permitir ligações de internet (HTTP) de entrada.

>[!NOTE]
>Por predefinição, o NSG já tem uma regra que abre a porta 3389, a porta de ambiente de trabalho remoto (RDP).

1. Selecione **Todos os recursos** no menu da esquerda. Na lista de recursos, selecione **MyNetworkSecurityGroup** no **MyResourceGroupLB** grupo de recursos.
   
1. Em **Definições**, selecione **Regras de segurança de entrada** e, em seguida, selecione **Adicionar**.
   
1. Na **Adicionar regra de segurança de entrada** caixa de diálogo, escreva ou selecione o seguinte:
   
   - **origem**: Selecione **etiqueta de serviço**.  
   - **Etiqueta de serviço de origem**: Selecione **Internet**. 
   - **Intervalos de portas de destino**: Tipo *80*.
   - **Protocolo**: Selecione **TCP**. 
   - **Ação**: Selecione **permitir**.  
   - **Prioridade**: Tipo *100*. 
   - **Nome**: Tipo *MyHTTPRule*. 
   - **Descrição**: Tipo *permitir HTTP*. 
   
1. Selecione **Adicionar**. 
   
   ![Criar uma regra NSG](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta secção, inspecionar o conjunto de back-end de Balanceador de carga e configurar um balanceador estado de funcionamento sonda e o tráfego de regras de carga.

### <a name="view-the-back-end-address-pool"></a>Ver o conjunto de endereços de back-end

Para distribuir o tráfego para as VMs, o Balanceador de carga utiliza um conjunto de endereços de back-end, que contém os endereços IP das interfaces de rede virtuais (NICs) que estão ligadas ao balanceador de carga. 

Criou o seu conjunto de back-end de Balanceador de carga e adicionar VMs ao mesmo ao criar as VMs. Também pode criar conjuntos de back-end e adicionar ou remover as VMs do Balanceador de carga **conjuntos de back-end** página. 

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Conjuntos de back-end**.
   
1. Sobre o **conjuntos de back-end** página, expanda **MyBackendPool** e certifique-se de ambos **VM1** e **VM2** estão listados.

1. Selecione **MyBackendPool**. 
   
   Sobre o **MyBackendPool** página, em **máquina VIRTUAL** e **endereço IP**, pode remover ou adicionar VMs disponíveis para o conjunto.

Pode criar novos conjuntos de back-end, selecionando **adicionar** sobre o **conjuntos back-end** página.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir que o Balanceador de carga monitorizar o estado da VM, pode utilizar uma sonda de estado de funcionamento. A sonda de estado de funcionamento adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na respetiva resposta às verificações de estado de funcionamento. 

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Sondas de estado de funcionamento** e, em seguida, selecione **Adicionar**.
   
1. Sobre o **adicionar sonda de estado de funcionamento** página, escreva ou selecione os seguintes valores:
   
   - **Nome**: Tipo *MyHealthProbe*.
   - **Protocolo**: Lista pendente e selecione **HTTP**. 
   - **Porta**: Tipo *80*. 
   - **Caminho**: Aceite */* para o URI predefinido. Pode substituir este valor com qualquer outro URI. 
   - **Intervalo**: Tipo *15*. O intervalo é o número de segundos entre tentativas da sonda.
   - **Limiar de mau estado de funcionamento**: Tipo *2*. Este valor é o número de falhas consecutivas da sonda que ocorrem antes de uma VM é considerada em mau estado de funcionamento.
   
1. Selecione **OK**.
   
   ![Adicionar uma sonda](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Crie uma regra de balanceador de carga

As regras de balanceador de carga definem a forma como o tráfego é distribuído pelas VMs. A regra define a configuração de IP Front-end para tráfego de entrada, o conjunto IP de back-end para receber o tráfego e as portas de origem e de destino necessárias. 

A regra de Balanceador de carga com o nome **MyLoadBalancerRule** escuta na porta 80 no front-end **LoadBalancerFrontEnd**. A regra envia o tráfego de rede para o conjunto de endereços de back-end **MyBackendPool**, também na porta 80. 

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Regras de balanceamento de carga** e, em seguida, selecione **Adicionar**.
   
1. Sobre o **Adicionar regra de balanceamento de carga** página, escreva ou selecione os seguintes valores:
   
   - **Nome**: Tipo *MyLoadBalancerRule*.
   - **Protocolo**: Selecione **TCP**.
   - **Porta**: Tipo *80*.
   - **Porta de back-end**: Tipo *80*.
   - **Conjunto back-end**: Selecione **MyBackendPool**.
   - **Sonda de estado de funcionamento**: Select **MyHealthProbe**. 
   
1. Selecione **OK**.
   
  ![Adicionar uma regra de Balanceador de carga](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>Criar regras de encaminhamento de porta NAT de entrada

Crie uma regra de tradução (NAT) de endereço de rede de entrada de Balanceador do carregamento para reencaminhar o tráfego de uma porta específica do endereço IP Front-end para uma porta específica de uma VM de back-end.

1. Selecione **todos os recursos** no menu do lado esquerdo e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Sob **configurações**, selecione **regras NAT de entrada**e, em seguida, selecione **Add**. 
   
1. Sobre o **Adicionar regra NAT de entrada** página, escreva ou selecione os seguintes valores:
   
   - **Nome**: Tipo *MyNATRuleVM1*.
   - **Porta**: Tipo *4221*.
   - **Máquina virtual de destino**: Selecione **MyVM1** na lista suspensa.
   - **Mapeamento de portas**: Selecione **personalizado**.
   - **Porta de destino**: Tipo *3389*.
   
1. Selecione **OK**.
   
1. Repita os passos para adicionar um NAT de entrada com o nome da regra *MyNATRuleVM2*, utilizando **porta**: *4222* e **máquina virtual de destino**: **MyVM2**.

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Nesta secção, irá instalar os serviços de informação Internet (IIS) nos servidores de back-end e personalizar a página da web padrão para mostrar o nome da máquina. Em seguida, irá utilizar o endereço IP público do Balanceador de carga para testar o Balanceador de carga. 

Cada VM de back-end funciona de uma versão diferente do que a página de web do IIS predefinida, para que possa ver o Balanceador de carga a distribuir os pedidos entre as duas VMs.

### <a name="connect-to-the-vms-with-rdp"></a>Ligar as VMs com RDP

Ligar a cada VM com o ambiente de trabalho remoto (RDP). 

1. No portal, selecione **todos os recursos** no menu da esquerda. Na lista de recursos, selecione cada VM no **MyResourceGroupLB** grupo de recursos.
   
1. Sobre o **descrição geral** página, selecione **Connect**e, em seguida, selecione **ficheiro RDP transferir**. 
   
1. Abra o RDP de ficheiros que transferiu e selecione **Connect**.
   
1. No ecrã de segurança do Windows, selecione **mais escolhas** e, em seguida **utilizar uma conta diferente**. 
   
   Introduza o nome de utilizador *azureuser* e a palavra-passe *Azure1234567*e selecione **OK**.
   
1. Responder **Sim** para qualquer linha de comandos do certificado. 
   
   A área de trabalho da VM abre-se numa nova janela. 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>Instalar o IIS e substitua a página de web do IIS predefinida 

Utilize o PowerShell para instalar o IIS e substitua a página de web do IIS predefinida de uma página que apresenta o nome da VM.

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
   
1. Fechar as ligações de RDP com MyVM1 e MyVM2 selecionando **desligar**. Não encerre as VMs.

### <a name="test-load-balancing"></a>Balanceamento de carga de teste

1. No portal, no **descrição geral** página **MyLoadBalancer**, copie o endereço IP público em **endereço IP público**. Coloque o cursor sobre o endereço e selecione o **cópia** ícone para copiá-lo. Neste exemplo, é **40.67.218.235**. 
   
1. Cole ou introduza o endereço IP público do Balanceador de carga (*40.67.218.235*) na barra de endereço do seu browser da internet. 
   
   A página de predefinição do servidor de web IIS personalizada é apresentada no browser. A mensagem lê um **Olá, mundo de MyVM1**, ou **Hello World do MyVM2**.
   
   ![Nova página de padrão do IIS](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. Atualize o browser para ver o Balanceador de carga a distribuir tráfego pelas VMs. Por vezes, o **MyVM1** é apresentada a página e outras vezes a **MyVM2** página for apresentada, conforme o Balanceador de carga distribui os pedidos para cada VM de back-end.
   
   >[!NOTE]
   >Terá de limpar a cache do browser ou abra uma nova janela de browser entre tentativas.

## <a name="test-port-forwarding"></a>Testar o reencaminhamento de portas

Com o encaminhamento de porta, pode ambiente de trabalho remoto a uma VM de back-end utilizando o endereço IP do Balanceador de carga e o valor da porta de front-end definidos na regra NAT. 

1. No portal, no **descrição geral** página **MyLoadBalancer**, copie o endereço IP público. Coloque o cursor sobre o endereço e selecione o **cópia** ícone para copiá-lo. Neste exemplo, é **40.67.218.235**. 
   
1. Abra uma linha de comandos e utilize o seguinte comando para criar uma sessão área de trabalho remota com MyVM2, usando o endereço IP público do Balanceador de carga e a porta de front-end que definiu na regra NAT da VM. 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. Abra o ficheiro RDP transferido e selecione **Connect**.
   
1. No ecrã de segurança do Windows, selecione **mais escolhas** e, em seguida **utilizar uma conta diferente**. 
   
   Introduza o nome de utilizador *azureuser* e a palavra-passe *Azure1234567*e selecione **OK**.
   
1. Responder **Sim** para qualquer linha de comandos do certificado. 
   
   A área de trabalho MyVM2 abre-se numa nova janela. 

A ligação de RDP é bem-sucedida, porque a regra NAT de entrada **MyNATRuleVM2** direciona o tráfego da porta de front-end do Balanceador de carga 4222 para a porta do MyVM2. 3389 (a porta RDP).

## <a name="clean-up-resources"></a>Limpar recursos

Para eliminar o Balanceador de carga e todos os recursos relacionados, quando já não precisar delas, abra a **MyResourceGroupLB** recursos de grupo e selecione **eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou um balanceador de carga público Standard. Criar e configurar recursos de rede, servidores de back-end, uma sonda de estado de funcionamento e regras de Balanceador de carga. Instalou o IIS nas VMs de back-end e usado o endereço IP público do Balanceador de carga para testar o Balanceador de carga. Configurar e testar o encaminhamento de porta de uma porta especificada no balanceador de carga para uma porta numa VM de back-end. 

Para saber mais sobre o Balanceador de carga do Azure, avance para mais tutoriais de Balanceador de carga.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
