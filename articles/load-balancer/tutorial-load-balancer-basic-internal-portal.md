---
title: 'Tutorial: criar um balanceador de carga interno-portal do Azure'
titleSuffix: Azure Load Balancer
description: Este tutorial mostra-lhe como criar um balanceador de carga básico interno com o portal do Azure.
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
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 1b9d943f540a0132abc6a70eba888aa5f8f46093
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225210"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-basic-load-balancer-in-the-azure-portal"></a>Tutorial: Balanceamento interno de carga de tráfego com um balanceador de carga básico no portal do Azure

Balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por máquinas virtuais (VMs). Pode utilizar o portal do Azure para criar um balanceador de carga básico e balancear o tráfego entre VMs interno. Este tutorial mostra-lhe como criar e configurar um balanceador de carga interno, servidores de back-end e recursos de rede ao escalão de preço básico.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

Se preferir, você pode executar essas etapas usando o [CLI do Azure](load-balancer-get-started-ilb-arm-cli.md) ou [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) em vez do Portal.

Para executar as etapas usando este tutorial, entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>Criar uma VNet, servidores de back-end e uma VM de teste

Primeiro, crie uma rede virtual (VNet). Crie a VNet, duas VMs a utilizar para o conjunto de back-end do Balanceador de carga básico e uma terceira VM para utilizar para testar o Balanceador de carga. 

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No lado superior esquerdo do portal, selecione **criar um recurso** > **rede** > **rede virtual**.
   
1. No painel **criar rede virtual** , digite ou selecione estes valores:
   
   - **Nome**: digite *MyVNet*.
   - **Resourcegroup**: selecione **criar novo**, em seguida, insira *MyResourceGroupLB*e selecione **OK**. 
   - **Nome**da > de **sub-rede** : digite *MyBackendSubnet*.
   
1. Selecione **Criar**.

   ![Criar uma rede virtual](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. No lado superior esquerdo do portal, selecione **criar um recurso** > **computação** > **Windows Server 2016 datacenter**. 
   
1. Em **criar uma máquina virtual**, digite ou selecione os seguintes valores na guia **noções básicas** :
   - **Assinatura** > **grupo de recursos**: menu suspenso e selecione **MyResourceGroupLB**.
   - **Detalhes da instância** > **nome da máquina virtual**: digite *MyVM1*.
   - **Detalhes da instância** > **Opções de disponibilidade**: 
     1. Menu suspenso e selecione **conjunto de disponibilidade**. 
     2. Selecione **criar novo**, digite *myavailabilityset*e selecione **OK**.
   
1. Selecione a guia **rede** ou selecione **Avançar: discos**e **Avançar: rede**. 
   
   Certifique-se de que estão selecionadas as seguintes:
   - **Rede virtual**: **MyVNet**
   - **Sub-rede**: **MyBackendSubnet**
   
   Em **grupo de segurança de rede**:
   1. Selecione **Avançadas**. 
   1. Menu suspenso **Configurar o grupo de segurança de rede** e selecione **nenhum**. 
   
1. Selecione a guia **Gerenciamento** ou selecione **próximo** **Gerenciamento**de > . Em **monitoramento**, defina **diagnóstico de inicialização** como **desativado**.
   
1. Selecione **Rever + criar**.
   
1. Examine as configurações e, em seguida, selecione **criar**. 

1. Siga as etapas para criar uma segunda VM denominada *MyVM2*, com todas as outras configurações iguais a MyVM1. 

1. Siga as etapas novamente para criar uma terceira VM chamada *MyTestVM*. 

## <a name="create-a-basic-load-balancer"></a>Criar um balanceador de carga Básico

Crie um balanceador de carga interno básico com o portal. O nome e endereço IP que cria são automaticamente configurados como front-end de Balanceador de carga.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **Balanceador de Carga**.
   
2. Na guia **noções básicas** da página **criar balanceador de carga** , insira ou selecione as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **revisar + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **criar novo** e digite *MyResourceGroupLB* na caixa de texto.|
    | Nome                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Tipo          | Selecione **interno**.                                        |
    | SKU           | Selecione **Básico**.                          |
    | Rede virtual           | Selecione *MyVNet*.                          |    
    | Atribuição de endereço IP              | Selecione **estático**.   |
    | Endereço IP privado|Digite um endereço que esteja no espaço de endereço de sua rede virtual e sub-rede, por exemplo *10.3.0.7*.  |

3. Na guia **revisar + criar** , clique em **criar**. 
   

## <a name="create-basic-load-balancer-resources"></a>Criar recursos do Balanceador de carga básico

Nesta seção, você define as configurações do balanceador de carga para um pool de endereços de back-end e uma investigação de integridade e especifica regras de balanceador de carga.

### <a name="create-a-back-end-address-pool"></a>Criar um conjunto de endereços de back-end

Para distribuir o tráfego para as VMs, o Balanceador de carga utiliza um conjunto de endereços de back-end. O conjunto de endereços de back-end contém os endereços IP das interfaces de rede virtuais (NICs) que estão ligadas ao balanceador de carga. 

**Para criar um pool de endereços de back-end que inclui VM1 e VM2:**

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Conjuntos de back-end** e, em seguida, selecione **Adicionar**.
   
1. Na página **Adicionar um pool de back-end** , digite ou selecione os seguintes valores:
   
   - **Nome**: digite *MyBackendPool*.
   - **Associado a**: menu suspenso e selecione **conjunto de disponibilidade**.
   - **Conjunto de disponibilidade**: selecione **myavailabilityset**.
   
1. Selecione **Adicionar uma configuração de IP de rede de destino**. 
   1. Adicione **MyVM1** e **MyVM2** ao pool de back-ends.
   2. Depois de adicionar cada máquina, clique na lista suspensa e selecione sua **configuração de IP de rede**. 
   
   >[!NOTE]
   >Não adicione **MyTestVM** ao pool. 
   
1. Selecione **OK**.
   
   ![Adicionar o conjunto de endereços de back-end](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)
   
1. Na página **pools de back-end** , expanda **MyBackendPool** e verifique se **VM1** e **VM2** estão listados.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir que o Balanceador de carga monitorizar o estado da VM, pode utilizar uma sonda de estado de funcionamento. A sonda de estado de funcionamento adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na respetiva resposta às verificações de estado de funcionamento. 

**Para criar uma investigação de integridade para monitorar a integridade das VMs:**

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Sondas de estado de funcionamento** e, em seguida, selecione **Adicionar**.
   
1. Na página **Adicionar uma investigação de integridade** , digite ou selecione os seguintes valores:
   
   - **Nome**: digite *MyHealthProbe*.
   - **Protocolo**: menu suspenso e selecione **http**. 
   - **Porta**: digite *80*. 
   - **Caminho**: aceite */* para o URI padrão. Pode substituir este valor com qualquer outro URI. 
   - **Intervalo**: tipo *15*. O intervalo é o número de segundos entre tentativas da sonda.
   - **Limite não íntegro**: tipo *2*. Este valor é o número de falhas consecutivas da sonda que ocorrem antes de uma VM é considerada em mau estado de funcionamento.
   
1. Selecione **OK**.
   
   ![Adicionar uma sonda](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

As regras de balanceador de carga definem a forma como o tráfego é distribuído pelas VMs. A regra define a configuração de IP Front-end para tráfego de entrada, o conjunto IP de back-end para receber o tráfego e as portas de origem e de destino necessárias. 

A regra do balanceador de carga chamada **MyLoadBalancerRule** escuta a porta 80 no **LoadBalancerFrontEnd**de front-end. A regra envia o tráfego de rede para o pool de endereços de back-end **MyBackendPool**, também na porta 80. 

**Para criar a regra do balanceador de carga:**

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Regras de balanceamento de carga** e, em seguida, selecione **Adicionar**.
   
1. Na página **Adicionar regra de balanceamento de carga** , digite ou selecione os seguintes valores, se ainda não estiverem presentes:
   
   - **Nome**: digite *MyLoadBalancerRule*.
   - **Endereço IP de front-end:** Digite *LoadBalancerFrontEnd* se não estiver presente.
   - **Protocolo**: selecione **TCP**.
   - **Porta**: digite *80*.
   - **Porta de back-end**: digite *80*.
   - **Pool de back-end**: selecione **MyBackendPool**.
   - **Investigação de integridade**: selecione **MyHealthProbe**. 
   
1. Selecione **OK**.
   
   ![Adicionar uma regra de Balanceador de carga](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Instalar serviços de informação Internet (IIS) nos servidores de back-end, em seguida, utilizar MyTestVM para testar o Balanceador de carga com o respetivo endereço IP privado. Cada VM de back-end funciona de uma versão diferente do que a página de web do IIS predefinida, para que possa ver o Balanceador de carga a distribuir os pedidos entre as duas VMs.

No portal, na página **visão geral** de **MyLoadBalancer**, localize seu endereço IP em **endereço IP privado**. Passe o mouse sobre o endereço e selecione o ícone de **cópia** para copiá-lo. Neste exemplo, é **10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Ligar as VMs com RDP

Em primeiro lugar, ligue-se a todas as três VMs com RDP (Remote Desktop). 

>[!NOTE]
>Por padrão, as VMs já têm a porta **RDP** (área de trabalho remota) aberta para permitir o acesso à área de trabalho remota. 

**Para a área de trabalho remota (RDP) nas VMs:**

1. No portal, selecione **todos os recursos** no menu à esquerda. Na lista de recursos, selecione cada VM no grupo de recursos **MyResourceGroupLB** .
   
1. Na página **visão geral** , selecione **conectar**e, em seguida, selecione **baixar arquivo RDP**. 
   
1. Abra o arquivo RDP baixado e selecione **conectar**.
   
1. Na tela segurança do Windows, selecione **mais opções** e, em seguida, **use uma conta diferente**. 
   
   Insira o nome de usuário e a senha e, em seguida, selecione **OK**.
   
1. Responda **Sim** para qualquer prompt de certificado. 
   
   A área de trabalho da VM abre-se numa nova janela. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>Instalar o IIS e substitua a página predefinida do IIS, nas VMs de back-end

Em cada servidor de back-end, utilize o PowerShell para instalar o IIS e substitua a página de web do IIS predefinida de uma página personalizada.

>[!NOTE]
>Você também pode usar o **Assistente para adicionar funções e recursos** no **Gerenciador do servidor** para instalar o IIS. 

**Para instalar o IIS e atualizar a página da Web padrão com o PowerShell:**

1. No MyVM1 e no MyVM2, inicie o **Windows PowerShell** no menu **Iniciar** . 

2. Execute os seguintes comandos para instalar o IIS e substitua a página de web do IIS predefinida:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. Feche as conexões RDP com MyVM1 e MyVM2 selecionando **Desconectar**. Não encerre as VMs.

### <a name="test-the-load-balancer"></a>Testar o balanceador de carga

1. No MyTestVM, abra o **Internet Explorer**e responda **OK** em todos os prompts de configuração. 
   
1. Cole ou digite o endereço IP privado do balanceador de carga (*10.3.0.7*) na barra de endereços do navegador. 
   
   A página de predefinição do servidor de web IIS personalizada é apresentada no browser. A mensagem lê **Olá, mundo de MyVM1**ou **Olá, mundo de MyVM2**.
   
1. Atualize o browser para ver o Balanceador de carga a distribuir tráfego pelas VMs. Também terá de limpar a cache do browser entre tentativas.

   Às vezes, a página **MyVM1** é exibida e, em outras ocasiões, a página **MyVM2** é exibida, pois o balanceador de carga distribui as solicitações para cada VM de back-end. 

   ![Nova página de padrão do IIS](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Limpar recursos

Para excluir o balanceador de carga e todos os recursos relacionados quando você não precisar mais deles, abra o grupo de recursos **MyResourceGroupLB** e selecione **excluir grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um balanceador de carga interno de escalão Basic. Criar e configurar recursos de rede, servidores de back-end, uma sonda de estado de funcionamento e regras de Balanceador de carga. Instalou o IIS nas VMs de back-end e utilizado uma VM de teste para testar o Balanceador de carga no browser. 

Em seguida, saiba como balancear carga de VMs por zonas de disponibilidade.

> [!div class="nextstepaction"]
> [Balancear carga de VMs por zonas de disponibilidade](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
