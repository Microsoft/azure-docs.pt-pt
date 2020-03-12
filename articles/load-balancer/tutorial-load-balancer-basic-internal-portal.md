---
title: 'Tutorial: Criar um equilibrador de carga interna - portal Azure'
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
ms.openlocfilehash: 6f62771d707d1aebccbfaf809dee7d0dedf5fefa
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096125"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-basic-load-balancer-in-the-azure-portal"></a>Tutorial: Balanceamento interno de carga de tráfego com um balanceador de carga básico no portal do Azure

Balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por máquinas virtuais (VMs). Pode utilizar o portal do Azure para criar um balanceador de carga básico e balancear o tráfego entre VMs interno. Este tutorial mostra-lhe como criar e configurar um balanceador de carga interno, servidores de back-end e recursos de rede ao escalão de preço básico.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

Se preferir, pode fazer estes passos utilizando o [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) ou [o Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) em vez do portal.

Para fazer os passos usando este tutorial, inscreva-se no portal Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>Criar uma VNet, servidores de back-end e uma VM de teste

Primeiro, crie uma rede virtual (VNet). Crie a VNet, duas VMs a utilizar para o conjunto de back-end do Balanceador de carga básico e uma terceira VM para utilizar para testar o Balanceador de carga. 

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > rede de **rede** > **rede virtual.**
   
1. No painel de **rede virtual Criar,** digite ou selecione estes valores:
   
   - **Nome**: Type *MyVNet*.
   - **ResourceGroup**: **Selecione Criar novo,** depois introduza *o MyResourceGroupLB,* e selecione **OK**. 
   - **Subnet** > **Nome**: Type *MyBackendSubnet*.
   
1. Selecione **Criar**.

   ![Criar uma rede virtual](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Compute** > **Windows Server 2016 Datacenter**. 
   
1. Em **Criar uma máquina virtual,** digite ou selecione os seguintes valores no separador **Basics:**
   - **Grupo de** **recursos** > de assinatura : Desça e selecione **MyResourceGroupLB**.
   - **Detalhes** > **nome virtual**da máquina : Tipo *MyVM1*.
   - **Detalhes da > ** **Opções de Disponibilidade:** 
     1. Desça e selecione **Conjunto de Disponibilidade**. 
     2. Selecione **Criar novo**, escreva *MyAvailabilitySet,* e selecione **OK**.
   
1. Selecione o separador **de rede** ou selecione **Seguinte: Discos,** em seguida **Seguinte: Networking**. 
   
   Certifique-se de que estão selecionadas as seguintes:
   - **Rede virtual**: **MyVNet**
   - **Subnet**: **MyBackendSubnet**
   
   No **âmbito do Grupo de Segurança da Rede:**
   1. Selecione **Avançadas**. 
   1. Despete o grupo de segurança da **rede Configure** e selecione **Nenhum**. 
   
1. Selecione o separador **Gestão** ou selecione **Next** > **Management**. Sob **monitorização,** descoloque os **diagnósticos da bota** para **desligar**.
   
1. Selecione **Rever + criar**.
   
1. Reveja as definições e, em seguida, selecione **Criar**. 

1. Siga os passos para criar um segundo VM chamado *MyVM2,* com todas as outras definições iguais às do MyVM1. 

1. Siga novamente os passos para criar um terceiro VM chamado *MyTestVM*. 

## <a name="create-a-basic-load-balancer"></a>Criar um balanceador de carga Básico

Crie um balanceador de carga interno básico com o portal. O nome e endereço IP que cria são automaticamente configurados como front-end de Balanceador de carga.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **Balanceador de Carga**.
   
2. No separador **Basics** da página **'Criar balanceor de carga',** introduzir ou selecionar as seguintes informações, aceitar as predefinições para as restantes definições e, em seguida, selecionar **Rever + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **Criar novo** e digitar *MyResourceGroupLB* na caixa de texto.|
    | Nome                   | *myLoadBalancer*                                   |
    | Região         | Selecione **E.U.A. Leste 2**.                                        |
    | Tipo          | Selecione **Internal**.                                        |
    | SKU           | Selecione **Básico**.                          |
    | Rede virtual           | Selecione *MyVNet*.                          |    
    | Atribuição de endereçoIP              | Selecione **Static**.   |
    | Endereço IP privado|Digite um endereço que se encontra no espaço de endereço da sua rede virtual e subnet, por exemplo *10.3.0.7*.  |

3. No **'Rever + criar** separador', clique em **Criar**. 
   

## <a name="create-basic-load-balancer-resources"></a>Criar recursos do Balanceador de carga básico

Nesta secção, configura as definições do equilíbrio de carga para um conjunto de endereços traseiros e uma sonda de saúde e especifica as regras do equilíbrio de carga.

### <a name="create-a-back-end-address-pool"></a>Criar um conjunto de endereços de back-end

Para distribuir o tráfego para as VMs, o Balanceador de carga utiliza um conjunto de endereços de back-end. O conjunto de endereços de back-end contém os endereços IP das interfaces de rede virtuais (NICs) que estão ligadas ao balanceador de carga. 

**Para criar um conjunto de endereços de back-end que inclua VM1 e VM2:**

1. **Selecione todos os recursos** no menu esquerdo e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Conjuntos de back-end** e, em seguida, selecione **Adicionar**.
   
1. Na página **de piscina adicionar uma reserva de backend,** escreva ou selecione os seguintes valores:
   
   - **Nome**: Type *MyBackendPool*.
   - **Associado a:** Baixar e selecionar **Máquina Virtual**.
   
   
1. Selecione **Máquina Virtual**. 
   1. Adicione **MyVM1** e **MyVM2** à piscina traseira.
   2. Depois de adicionar cada máquina, desça e selecione a **configuração IP da rede**. 
   
   >[!NOTE]
   >Não adicione **MyTestVM** à piscina. 
   
1. Selecione **OK**.
   
   ![Adicionar o conjunto de endereços de back-end](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)
   
1. Na página **backend pools,** expanda **myBackendPool** e certifique-se de que tanto **vM1** como **VM2** estão listados.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir que o Balanceador de carga monitorizar o estado da VM, pode utilizar uma sonda de estado de funcionamento. A sonda de estado de funcionamento adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na respetiva resposta às verificações de estado de funcionamento. 

**Para criar uma sonda de saúde para monitorizar a saúde dos VMs:**

1. **Selecione todos os recursos** no menu esquerdo e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Sondas de estado de funcionamento** e, em seguida, selecione **Adicionar**.
   
1. Na página Adicionar uma sonda de **saúde,** digite ou selecione os seguintes valores:
   
   - **Nome**: *Digite MyHealthProbe*.
   - **Protocolo**: Desça e selecione **HTTP**. 
   - **Porta**: Tipo *80*. 
   - **Caminho**: Aceite */* para o URI padrão. Pode substituir este valor com qualquer outro URI. 
   - **Intervalo**: Tipo *15*. O intervalo é o número de segundos entre tentativas da sonda.
   - **Limiar pouco saudável**: Tipo *2*. Este valor é o número de falhas consecutivas da sonda que ocorrem antes de uma VM é considerada em mau estado de funcionamento.
   
1. Selecione **OK**.
   
   ![Adicionar uma sonda](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Crie uma regra de balanceador de carga

As regras de balanceador de carga definem a forma como o tráfego é distribuído pelas VMs. A regra define a configuração de IP Front-end para tráfego de entrada, o conjunto IP de back-end para receber o tráfego e as portas de origem e de destino necessárias. 

A regra do equilíbrio de carga denominada **MyLoadBalancerRule** ouve a porta 80 na extremidade frontal **LoadBalancerFrontEnd**. A regra envia tráfego de rede para a piscina de endereços back-end **MyBackendPool**, também no porto 80. 

**Para criar a regra do equilíbrio de carga:**

1. **Selecione todos os recursos** no menu esquerdo e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Regras de balanceamento de carga** e, em seguida, selecione **Adicionar**.
   
1. Na página de regra de equilíbrio de **carga Adicionar,** digite ou selecione os seguintes valores, se ainda não estiverpresente:
   
   - **Nome**: Type *MyLoadBalancerRule*.
   - **Endereço IP frontend:** Tipo *LoadBalancerFrontEnd* se não estiver presente.
   - **Protocolo**: Selecione **TCP**.
   - **Porta**: Tipo *80*.
   - **Porta de backend**: Tipo *80*.
   - **Piscina de backend**: Selecione **MyBackendPool**.
   - **Sonda de saúde**: Selecione **MyHealthProbe**. 
   
1. Selecione **OK**.
   
   ![Adicionar uma regra de Balanceador de carga](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Instalar serviços de informação Internet (IIS) nos servidores de back-end, em seguida, utilizar MyTestVM para testar o Balanceador de carga com o respetivo endereço IP privado. Cada VM de back-end funciona de uma versão diferente do que a página de web do IIS predefinida, para que possa ver o Balanceador de carga a distribuir os pedidos entre as duas VMs.

No portal, na página **de visão geral** do **MyLoadBalancer,** encontre o seu endereço IP em **endereço IP privado**. Passe por cima do endereço e selecione o ícone **Copiar** para copiá-lo. Neste exemplo, é **10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Ligar as VMs com RDP

Em primeiro lugar, ligue-se a todas as três VMs com RDP (Remote Desktop). 

>[!NOTE]
>Por predefinição, os VMs já têm a porta **RDP** (Remote Desktop) aberta para permitir o acesso remoto ao ambiente de trabalho. 

**Para o ambiente de trabalho remoto (RDP) nos VMs:**

1. No portal, selecione **Todos os recursos** no menu esquerdo. A partir da lista de recursos, selecione cada VM no grupo de recursos **MyResourceGroupLB.**
   
1. Na página **'Overview',** selecione **Connect**, e, em seguida, selecione **download ficheiro RDP**. 
   
1. Abra o ficheiro RDP que descarregou e selecione **Connect**.
   
1. No ecrã de Segurança do Windows, selecione **Mais escolhas** e, em seguida, **use uma conta diferente**. 
   
   Introduza o nome de utilizador e a palavra-passe e, em seguida, selecione **OK**.
   
1. Responda **Sim** a qualquer pedido de certificado. 
   
   A área de trabalho da VM abre-se numa nova janela. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>Instalar o IIS e substitua a página predefinida do IIS, nas VMs de back-end

Em cada servidor de back-end, utilize o PowerShell para instalar o IIS e substitua a página de web do IIS predefinida de uma página personalizada.

>[!NOTE]
>Também pode utilizar o **Assistente de Adicionar e Funcionalidades** no Servidor **Manager** para instalar o IIS. 

**Para instalar o IIS e atualizar a página web predefinida com o PowerShell:**

1. No MyVM1 e no MyVM2, lança o **Windows PowerShell** a partir do menu **Iniciar.** 

2. Execute os seguintes comandos para instalar o IIS e substitua a página de web do IIS predefinida:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. Feche as ligações RDP com MyVM1 e MyVM2 selecionando **Desligar**. Não encerre as VMs.

### <a name="test-the-load-balancer"></a>Testar o balanceador de carga

1. No MyTestVM, abra **o Internet Explorer**e responda **OK** a quaisquer instruções de configuração. 
   
1. Colhe ou escreva o endereço IP privado do equilibrador de carga *(10.3.0.7*) na barra de endereços do navegador. 
   
   A página de predefinição do servidor de web IIS personalizada é apresentada no browser. A mensagem **lê-se ou Hello World from MyVM1**, ou **Hello World from MyVM2**.
   
1. Atualize o browser para ver o Balanceador de carga a distribuir tráfego pelas VMs. Também terá de limpar a cache do browser entre tentativas.

   Por vezes aparece a página **MyVM1,** e outras vezes aparece a página **MyVM2,** uma vez que o equilibrador de carga distribui os pedidos por cada VM de back-end. 

   ![Nova página de padrão do IIS](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Limpar recursos

Para eliminar o equilibrador de carga e todos os recursos relacionados quando já não precisar, abra o grupo de recursos **MyResourceGroupLB** e selecione Eliminar o **grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um balanceador de carga interno de escalão Basic. Criar e configurar recursos de rede, servidores de back-end, uma sonda de estado de funcionamento e regras de Balanceador de carga. Instalou o IIS nas VMs de back-end e utilizado uma VM de teste para testar o Balanceador de carga no browser. 

Em seguida, saiba como balancear carga de VMs por zonas de disponibilidade.

> [!div class="nextstepaction"]
> [Balancear carga de VMs por zonas de disponibilidade](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
