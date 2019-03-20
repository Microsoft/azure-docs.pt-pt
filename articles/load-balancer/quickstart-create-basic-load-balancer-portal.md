---
title: 'Início rápido: Criar um balanceador de carga básico público com o portal do Azure'
titlesuffix: Azure Load Balancer
description: Este guia de início rápido mostra como criar um balanceador de carga Básico público com o portal do Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: fe095b8f5a0080c0f28ec570303c9dc23962dfc8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57869817"
---
# <a name="quickstart-create-a-basic-load-balancer-by-using-the-azure-portal"></a>Início rápido: Criar um balanceador de carga básico com o portal do Azure

Balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por máquinas virtuais (VMs). Pode utilizar o portal do Azure para criar um balanceador de carga e balancear o tráfego entre VMs. Este guia de introdução mostra-lhe como criar e configurar um balanceador de carga, servidores de back-end e recursos de rede ao escalão de preço básico.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

Para fazer as tarefas neste início rápido, inicie sessão para o [portal do Azure](https://portal.azure.com).

## <a name="create-a-basic-load-balancer"></a>Criar um Balanceador de Carga Básico

Primeiro, crie um balanceador de carga básico público com o portal. O nome e endereço IP público que criou são automaticamente configurados como front-end de Balanceador de carga.

1. No canto superior esquerdo do ecrã, clique em **Criar um recurso** > **Rede** > **Balanceador de Carga**.
2. Na **Noções básicas** separador da **criar Balanceador de carga** página, introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **rever + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **criar novo** e escreva *MyResourceGroupLB* na caixa de texto.|
    | Name                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Type          | Selecione **público**.                                        |
    | SKU           | Selecione **Básico**.                          |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público              | *MyPublicIP*   |
    | Atribuição| Estático|

3. Na **rever + criar** separador, clique em **criar**.   


## <a name="create-back-end-servers"></a>Criar servidores de back-end

Em seguida, crie uma rede virtual e duas máquinas virtuais para o conjunto de back-end do seu Balanceador de carga básico. 

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No lado do canto superior esquerdo do portal, selecione **criar um recurso** > **rede** > **rede Virtual**.
   
1. Na **criar rede virtual** painel, escreva ou selecione estes valores:
   
   - **Nome**: Tipo *MyVnet*.
   - **ResourceGroup**: Menu pendente **selecionar existente** e selecione **MyResourceGroupLB**. 
   - **Sub-rede** > **nome**: Tipo *MyBackendSubnet*.
   
1. Selecione **Criar**.

   ![Criar uma rede virtual](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

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

1. Selecione **Todos os recursos** no menu da esquerda. Na lista de recursos, selecione **MyNetworkSecurityGroup** no **MyResourceGroupLB** grupo de recursos.
   
1. Em **Definições**, selecione **Regras de segurança de entrada** e, em seguida, selecione **Adicionar**.
   
1. Na **Adicionar regra de segurança de entrada** caixa de diálogo, para a regra HTTP, escreva ou selecione o seguinte:
   
   - **origem**: Selecione **etiqueta de serviço**.  
   - **Etiqueta de serviço de origem**: Selecione **Internet**. 
   - **Intervalos de portas de destino**: Tipo *80*.
   - **Protocolo**: Selecione **TCP**. 
   - **Ação**: Selecione **permitir**.  
   - **Prioridade**: Tipo *100*. 
   - **Nome**: Tipo *MyHTTPRule*. 
   - **Descrição**: Tipo *permitir HTTP*. 
   
1. Selecione **Adicionar**. 
   
   ![Criar uma regra NSG](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
   
1. Repita os passos para a regra de RDP de entrada, com os seguintes valores diferentes:
   - **Intervalos de portas de destino**: Tipo *3389*.
   - **Prioridade**: Tipo *200*. 
   - **Nome**: Tipo *MyRDPRule*. 
   - **Descrição**: Tipo *permitir RDP*. 

## <a name="create-resources-for-the-load-balancer"></a>Criar recursos para o Balanceador de carga

Nesta secção, irá configurar as definições de Balanceador de carga para um conjunto de endereços de back-end, uma sonda de estado de funcionamento e uma regra de Balanceador de carga.

### <a name="create-a-backend-address-pool"></a>Criar um conjunto de endereços de back-end

Para distribuir o tráfego para as VMs, o Balanceador de carga utiliza um conjunto de endereços de back-end. O conjunto de endereços de back-end contém os endereços IP das interfaces de rede virtuais (NICs) que estão ligadas ao balanceador de carga. 

**Para criar um conjunto de endereços de back-end que inclua a VM1 e VM2:**

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Conjuntos de back-end** e, em seguida, selecione **Adicionar**.
   
1. Sobre o **adicionar um conjunto de back-end** página, escreva ou selecione os seguintes valores:
   
   - **Nome**: Tipo *MyBackEndPool*.
   - **Associados a**: Lista pendente e selecione **conjunto de disponibilidade**.
   - **Conjunto de disponibilidade**: Selecione **MyAvailabilitySet**.
   
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
   
   - **Nome**: Tipo *MyHealthProbe*.
   - **Protocolo**: Lista pendente e selecione **HTTP**. 
   - **Porta**: Tipo *80*. 
   - **Caminho**: Aceite */* para o URI predefinido. Pode substituir este valor com qualquer outro URI. 
   - **Intervalo**: Tipo *15*. O intervalo é o número de segundos entre tentativas da sonda.
   - **Limiar de mau estado de funcionamento**: Tipo *2*. Este valor é o número de falhas consecutivas da sonda que ocorrem antes de uma VM é considerada em mau estado de funcionamento.
   
1. Selecione **OK**.
   
   ![Adicionar uma sonda](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Crie uma regra de balanceador de carga

As regras de balanceador de carga definem a forma como o tráfego é distribuído pelas VMs. A regra define a configuração de IP Front-end para tráfego de entrada, o conjunto IP de back-end para receber o tráfego e as portas de origem e de destino necessárias. 

A regra de Balanceador de carga com o nome **MyLoadBalancerRule** escuta na porta 80 no front-end **LoadBalancerFrontEnd**. A regra envia o tráfego de rede para o conjunto de endereços de back-end **MyBackEndPool**, também na porta 80. 

**Para criar a regra de Balanceador de carga:**


1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Regras de balanceamento de carga** e, em seguida, selecione **Adicionar**.
   
1. Sobre o **Adicionar regra de balanceamento de carga** página, escreva ou selecione os seguintes valores:
   
   - **Nome**: Tipo *MyLoadBalancerRule*.
   - **Endereço IP de front-end:** Type *LoadBalancerFrontend*.
   - **Protocolo**: Selecione **TCP**.
   - **Porta**: Tipo *80*.
   - **Porta de back-end**: Tipo *80*.
   - **Conjunto back-end**: Selecione **MyBackendPool**.
   - **Sonda de estado de funcionamento**: Select **MyHealthProbe**. 
   
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
   
**Para instalar o IIS na VM:**

1. Se **Gestor de servidores** não é estiver aberto no ambiente de trabalho de servidor, navegue até **ferramentas administrativas do Windows** > **Gestor de servidor**.
   
1. Na **Gestor de servidores**, selecione **para adicionar funções e funcionalidades**.
   
   ![Adicionar função de Gestor de servidor](./media/load-balancer-get-started-internet-portal/servermanager.png)
   
1. Na **Assistente para adicionar funções e funcionalidades**:
   1. Na página **Selecionar tipo de instalação**, selecione **Instalação baseada em funções ou baseada em funcionalidades**.
   1. Sobre o **selecionar servidor de destino** , selecione **MyVM1**.
   1. Na página **Selecionar função de servidor**, selecione **Servidor Web (IIS)**. 
   1. Na linha de comandos para instalar as ferramentas necessárias, selecione **adicionar funcionalidades**. 
   1. Aceite as predefinições e selecione **instalar**. 
   1. Quando terminar as funcionalidades a instalar, selecione **fechar**. 
   
1. Repita os passos para a máquina virtual **MyVM2**, exceto a definir o servidor de destino **MyVM2**.

### <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Abra um browser e cole o endereço IP público do seu Balanceador de carga na barra de endereço do browser. A página predefinida do IIS web server deve aparecer no navegador.

![Servidor Web do IIS](./media/load-balancer-get-started-internet-portal/9-load-balancer-test.png)

Para ver o balanceador de carga a distribuir tráfego pelas três VMs que estão a executar a aplicação, pode forçar a atualização do browser.
## <a name="clean-up-resources"></a>Limpar recursos

Para eliminar o Balanceador de carga e todos os recursos relacionados, quando já não precisar delas, abra a **MyResourceGroupLB** recursos de grupo e selecione **eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um balanceador de carga de escalão Basic. Criar e configurar um grupo de recursos, recursos de rede, servidores de back-end, uma sonda de estado de funcionamento e regras para utilizar com o Balanceador de carga. Instalou o IIS nas VMs e utilizado para testar o Balanceador de carga. 

Para saber mais sobre o Balanceador de carga do Azure, avance para os tutoriais.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-basic-internal-portal.md)
