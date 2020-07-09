---
title: 'Tutorial: Criar um equilibrador de carga interno - Portal Azure'
titleSuffix: Azure Load Balancer
description: Este tutorial mostra-lhe como criar um balanceador de carga Standard interno utilizando o portal Azure.
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
ms.openlocfilehash: b8fcef13fbe41ac26b2a31d6871896428649eaa1
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85920850"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-standard-load-balancer-in-the-azure-portal"></a>Tutorial: Equilibrar carga de tráfego interno com um balanceador de carga Standard no portal Azure

O equilíbrio de carga proporciona um maior nível de disponibilidade e escala, espalhando pedidos de entrada através de máquinas virtuais (VMs). Pode utilizar o portal Azure para criar um balanceador de carga Standard e equilibrar o tráfego interno entre VMs. Este tutorial mostra-lhe como criar e configurar um equilibrador de carga interno, servidores de back-end e recursos de rede no nível de preços padrão.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

Se preferir, pode fazer estes passos utilizando o [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) ou [o Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) em vez do portal.

Para fazer os passos usando este tutorial, inscreva-se no portal Azure em [https://portal.azure.com](https://portal.azure.com) .

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>Crie um VNet, servidores de back-end e um VM de teste

Primeiro, criar uma rede virtual (VNet). No VNet, crie dois VMs para utilizar para o pool de backend do seu balanceador de carga Standard e um terceiro VM para utilizar para testar o equilibrador de carga. 

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No lado superior esquerdo do portal, **selecione Criar uma**rede virtual  >  **de rede de rede de**recursos  >  **Virtual network**.
   
1. No painel **de rede virtual Create,** escreva ou selecione estes valores:
   
   - **Nome**: Tipo **MyVNet**.
   - **Grupo de Recursos**: Selecione **Criar novo,** em seguida, insira **o MyResourceGroupLB**e selecione **OK**. 
   - **Sub-rede**  >  **Nome**: **Digite MyBackendSubnet**.
   
1. Selecione **Criar**.

   ![Criar uma rede virtual](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. No lado superior esquerdo do portal, selecione **Criar um**  >  **datacenter Compute**  >  **Windows Server 2016**. 
   
1. Na **Criação de uma máquina virtual,** escreva ou selecione os seguintes valores no **separador Básicos:**
   - **Assinatura**  >  **Grupo de Recursos**: Desça e selecione **MyResourceGroupLB**.
   - **Detalhes da**  >  instância **Nome da máquina virtual**: Tipo **MyVM1**.
   - **Detalhes da**  >  instância **Região**: Selecione **Leste US 2**.
  
   
1. Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos**e, em seguida, **seguinte: Networking**. 
   
   Certifique-se de que estão selecionados:
   - **Rede virtual**: **MyVNet**
   - **Sub-rede**: **MyBackendSubnet**
   - **Grupo de segurança da rede NIC**: Selecione **Basic**.
   - **IP público** > Selecione **Criar novos** valores e insira os seguintes valores e selecione **OK**:
       - **Nome**: **MyVM1-IP**
       - **SKU**: Selecione **Standard**
   - **Portas de entrada pública**: Selecione **Deixe as portas selecionadas.**
   - **Selecione portas de entrada**: Desça e selecione **RDP (3389)**

   
   
1. Selecione o separador **Gestão** ou selecione **'Gestão**  >  **Seguinte'.** Em **Monitorização**, desemisse os **diagnósticos** **da**Bota .
   
1. Selecione **Rever + criar**.
   
1. Reveja as definições e, em seguida, **selecione Criar**. 

1. Siga os passos para criar um segundo VM chamado **MyVM2,** com todas as outras definições iguais às myVM1. 

1. Siga novamente os passos para criar um terceiro VM chamado **MyTestVM**. 

## <a name="create-a-standard-load-balancer"></a>Criar um equilibrador de carga standard

Crie um balanceador de carga interno padrão utilizando o portal. O nome e o endereço IP que cria são configurados automaticamente como a extremidade frontal do balançador de carga.

1. No lado superior esquerdo do portal, **selecione Criar um**  >  equilibrador de carga de**rede de**recursos  >  **Load Balancer**.
   
2. No separador **Básico da** página **'Criar balanceador** de carga', introduzir ou selecionar as seguintes informações, aceitar as predefinições para as definições restantes e, em seguida, selecionar **Rever + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | **Selecione Criar novo** e digitar *MyResourceGroupLB* na caixa de texto.|
    | Name                   | *myLoadBalancer*                                   |
    | Região         | Selecione **E.U.A. Leste 2**.                                        |
    | Tipo          | Selecione **Interno**.                                        |
    | SKU           | Selecione **Standard**.                          |
    | Rede virtual           | Selecione *MyVNet*.                          |    
    | Atribuição de endereços IP              | Selecione **Estático**.   |
    | Endereço IP privado|Digite um endereço que esteja no espaço de endereço da sua rede virtual e sub-rede, por exemplo *10.3.0.7*.  |

3. No **separador 'Rever +' criar,** clique em **Criar.** 
   

## <a name="create-standard-load-balancer-resources"></a>Criar recursos equilibradores de carga padrão

Nesta secção, configura as definições do balançador de carga para um pool de endereços back-end e uma sonda de saúde, e especifica as regras do balançador de carga.

### <a name="create-a-back-end-address-pool"></a>Criar um conjunto de endereços de back-end

Para distribuir o tráfego para os VMs, o equilibrador de carga utiliza uma piscina de endereços back-end. O conjunto de endereços back-end contém os endereços IP das interfaces de rede virtuais (NICs) que estão ligados ao equilibrador de carga. 

**Para criar uma piscina de endereços back-end que inclui VM1 e VM2:**

1. Selecione **todos os recursos** no menu esquerdo e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Conjuntos de back-end** e, em seguida, selecione **Adicionar**.
   
1. Na página **de pool de backend,** escreva ou selecione os seguintes valores:
   
   - **Nome**: **Digite MyBackendPool**.
   
1. Em **máquinas virtuais.** 
   1. Adicione **MyVM1** e **MyVM2** à piscina traseira.
   2. Depois de adicionar cada máquina, desça e selecione a sua **configuração IP de rede**. 
     
1. Selecione **Adicionar**.
   
   ![Adicione a piscina de endereços back-end](./media/tutorial-load-balancer-standard-internal-portal/3-load-balancer-backend-02.png)
   
1. Na página de **pools backend,** expanda o **MyBackendPool** e certifique-se de que tanto **o VM1** como **o VM2** estão listados.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir que o balanceador de carga monitorize o estado de VM, utilize uma sonda de saúde. A sonda de estado de funcionamento adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na respetiva resposta às verificações de estado de funcionamento. 

**Criar uma sonda de saúde para monitorizar a saúde dos VMs:**

1. Selecione **todos os recursos** no menu esquerdo e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Sondas de estado de funcionamento** e, em seguida, selecione **Adicionar**.
   
1. Na página **de sonda de saúde,** digite ou selecione os seguintes valores:
   
   - **Nome**: **Digite MyHealthProbe**.
   - **Protocolo**: Desça e selecione **HTTP**. 
   - **Porta**: Tipo **80**. 
   - **Caminho**: Aceite **/** para o URI predefinido. Pode substituir este valor por qualquer outro URI. 
   - **Intervalo**: Tipo **15**. O intervalo é o número de segundos entre as tentativas da sonda.
   - **Limiar pouco saudável**: Tipo **2**. Este valor é o número de falhas consecutivas da sonda que ocorrem antes de um VM ser considerado insalubre.
   
1. Selecione **OK**.
   
   ![Adicione uma sonda](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

As regras de balanceador de carga definem a forma como o tráfego é distribuído pelas VMs. A regra define a configuração IP frontal para o tráfego de entrada, o pool IP back-end para receber o tráfego, e as portas de origem e destino necessárias. 

A regra do balanceador de carga chamada **MyLoadBalancerRule** ouve a porta 80 na frente-end **LoadBalancerFrontEnd**. A regra envia tráfego de rede para o pool de endereços back-end **MyBackendPool**, também na porta 80. 

**Para criar a regra do balançador de carga:**

1. Selecione **todos os recursos** no menu esquerdo e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Regras de balanceamento de carga** e, em seguida, selecione **Adicionar**.
   
1. Na página de **regra de equilíbrio de carga adicionar,** digite ou selecione os seguintes valores, se ainda não estiverem presentes:
   
   - **Nome**: **Digite MyLoadBalancerule**.
   - **Endereço IP frontend:** Digite **LoadBalancerFrontEnd** se não estiver presente.
   - **Protocolo**: Selecione **TCP**.
   - **Porta**: Tipo **80**.
   - **Porta de backend**: Tipo **80**.
   - **Backend pool**: Selecione **MyBackendPool**.
   - **Sonda de saúde**: Selecione **MyHealthProbe**. 
   
Para configurar portas [de alta disponibilidade](load-balancer-ha-ports-overview.md) utilizando o portal Azure, selecione a caixa de verificação das portas **HA.** Quando selecionada, a configuração da porta e do protocolo relacionada é automaticamente povoada. 

1. Selecione **OK**.
   
   ![Adicionar uma regra de balanceador de carga](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Instale os Serviços de Informação da Internet (IIS) nos servidores de back-end e, em seguida, utilize o MyTestVM para testar o equilibrador de carga utilizando o seu endereço IP privado. Cada VM de back-end serve uma versão diferente da página web padrão do IIS, para que possa ver os pedidos de distribuição do balanceador de carga entre os dois VMs.

No portal, na página **'Visão Geral'** **do MyLoadBalancer,** encontre o seu endereço IP no **Endereço IP Privado**. Passe por cima do endereço e selecione o ícone **Copy** para copiá-lo. Neste exemplo, é **10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Ligue-se aos VMs com RDP

Em primeiro lugar, ligue-se aos três VMs com Desktop Remoto (RDP). 

>[!NOTE]
>Por predefinição, os VMs já têm a porta **RDP** (Remote Desktop) aberta para permitir o acesso remoto ao ambiente de trabalho. 

**Para o ambiente de trabalho remoto (PDR) nos VMs:**

1. No portal, selecione **Todos os recursos** no menu esquerdo. Na lista de recursos, selecione cada VM no grupo de recursos **MyResourceGroupLB.**
   
1. Na página **'Vista Geral',** selecione **Connect**e, em seguida, selecione **Download RDP file**. 
   
1. Abra o ficheiro RDP que descarregou e selecione **Connect**.
   
1. No ecrã de Segurança do Windows, selecione **Mais escolhas** e, em seguida, **Utilize uma conta diferente**. 
   
   Introduza o nome de utilizador e a palavra-passe e, em seguida, selecione **OK**.
   
1. Responda **Sim** a qualquer pedido de certificado. 
   
   O ambiente de trabalho em VM abre-se numa nova janela. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>Instale o IIS e substitua a página IIS predefinido nos VMs de back-end

Em cada servidor de back-end, utilize o PowerShell para instalar o IIS e substitua a página web do IIS por padrão por uma página personalizada.

>[!NOTE]
>Também pode utilizar o **Assistente de Funcionalidades e Funcionalidades de Adicionar** no Gestor do **Servidor** para instalar o IIS. 

**Para instalar o IIS e atualizar a página web predefinida com o PowerShell:**

1. No MyVM1 e no MyVM2, lance **o Windows PowerShell** a partir do menu **Iniciar.** 

2. Executar os seguintes comandos para instalar o IIS e substituir a página web do IIS predefinido:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
      remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add custom htm file
      Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. Feche as ligações RDP com MyVM1 e MyVM2 selecionando **Disconnect**. Não desligue os VMs.

### <a name="test-the-load-balancer"></a>Testar o balanceador de carga

1. No MyTestVM, abra o **Internet Explorer**e responda **OK** a quaisquer solicitações de configuração. 
   
1. Cole ou digite o endereço IP privado do balanceador de carga *(10.3.0.7*) na barra de endereço do navegador. 
   
   A página padrão personalizada do servidor web IIS aparece no navegador. A mensagem diz **hello world from MyVM1,** ou **Hello World from MyVM2**.
   
1. Refresque o navegador para ver o balanceador de carga distribuir tráfego através de VMs. Também pode precisar de limpar a cache do seu navegador entre tentativas.

   Por vezes aparece a página **MyVM1** e, outras vezes, aparece a página **MyVM2,** uma vez que o equilibrador de carga distribui os pedidos a cada VM de back-end. 

   ![Nova página padrão do IIS](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Limpar recursos

Para eliminar o equilibrador de carga e todos os recursos relacionados quando já não precisar deles, abra o grupo de recursos **MyResourceGroupLB** e selecione **Delete resource group**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um balanceador interno Standard. Criou e configura recursos de rede, servidores de back-end, uma sonda de saúde e regras para o equilibrador de carga. Instalou iIS nos VMs de back-end e usou um VM de teste para testar o equilibrador de carga no navegador. 

Em seguida, aprenda a carregar VMs de equilíbrio em zonas de disponibilidade.

> [!div class="nextstepaction"]
> [Balancear carga de VMs por zonas de disponibilidade](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
