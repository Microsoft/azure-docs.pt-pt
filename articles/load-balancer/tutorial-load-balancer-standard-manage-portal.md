---
title: 'Tutorial: Carregar o tráfego de internet para VMs - Portal Azure'
titleSuffix: Azure Load Balancer
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
ms.openlocfilehash: 8961a50490bdbf8b456e87e1c00577c2c8afd050
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80240366"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Tutorial: Carregar o tráfego de internet para VMs usando o portal Azure

O balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por várias máquinas virtuais. Neste tutorial, você aprende sobre os diferentes componentes do Balancer de Carga Padrão Azure que distribuem o tráfego de internet para VMs e fornecem alta disponibilidade. Saiba como:


> [!div class="checklist"]
> * Criar um Balancer de Carga Azure
> * Criar recursos de Balancer de Carga
> * Criar máquinas virtuais e instalar o servidor IIS
> * Ver Balancer de carga em ação
> * Adicione e remova VMs de um Balancer de carga

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Criar um Balanceador de Carga Standard

Nesta secção, cria-se um Balancer de Carga Padrão que ajuda a carregar máquinas virtuais de equilíbrio. O Balanceador de Carga Standard só suporta endereços IP Públicos Standard. Quando cria um Balanceador de Carga Standard, também tem de criar um novo endereço IP Público Standard que seja configurado como o front-end (denominado *LoadBalancerFrontend* por predefinição) para o mesmo. 

1. No lado superior esquerdo do ecrã, clique em **Criar um equilíbrio** > de**carga**de**rede** > de recursos .
2. No separador **Basics** da página **'Criar balanceor de carga',** introduzir ou selecionar as seguintes informações, aceitar as predefinições para as restantes definições e, em seguida, selecionar **Rever + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **Criar novo** e digitar *o myResourceGroupSLB* na caixa de texto.|
    | Nome                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Tipo          | Selecione **Public**.                                        |
    | SKU           | Selecione **Standard**.                          |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público              | Digite *o meu PublicIP* na caixa de texto.   |
    |Zona de disponibilidade| Selecione **Zona redundante**.    |

3. No **'Rever + criar** separador', clique em **Criar**.

   ![Criar um Balanceador de Carga Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Criar recursos de Balancer de Carga

Nesta secção, configura as definições do Balancer de Carga para um conjunto de endereços de backend, uma sonda de saúde e especifica uma regra de equilíbrio.

### <a name="create-a-backend-address-pool"></a>Criar um conjunto de endereços de back-end

Para distribuir o tráfego para os VMs, um conjunto de endereços de backend contém os endereços IP do virtual (NICs) ligados ao Balanceor de Carga. Crie o conjunto de endereços backend *myBackendPool* para incluir máquinas virtuais para equilibrar o tráfego de internet de equilíbrio de carga.

1. Selecione **todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, clique em **myLoadBalancer** na lista de recursos.
2. Em **Definições**, clique em **Conjuntos de back-end** e, em seguida, clique em **Adicionar**.
3. Na página adicionar uma página **de piscina de backend,** para nome, digite *myBackendPool,* como o nome para a sua piscina de backend, e, em seguida, selecione **Adicionar**.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir que o Balancer de carga monitorize o estado da sua aplicação, utilize uma sonda de saúde. A sonda de saúde adiciona ou remove dinamicamente os VMs da rotação do Balancer de Carga com base na sua resposta aos controlos de saúde. Crie uma sonda de estado de funcionamento, *myHealthProbe*, para monitorizar o estado de funcionamento das VMs.

1. Selecione **todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, clique em **myLoadBalancer** na lista de recursos.
2. Em **Definições**, clique em **Sondas de estado de funcionamento** e, em seguida, clique em **Adicionar**.
3. Utilize estes valores para criar a sonda de estado de funcionamento:
     
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Introduza *o myHealthProbe*. |
    | Protocolo | Selecione **HTTP**. |
    | Porta | Insira *80*.|
    | Intervalo | Introduza *15* para o número de **intervalos** em segundos entre as tentativas de sonda. |
    | Limiar com funcionamento incorreto | Selecione *2* para o número de falhas de **limiar não saudáveis** ou de sonda consecutivas que devem ocorrer antes de um VM ser considerado insalubre.|
    
4. Selecione **OK**.

### <a name="create-a-load-balancer-rule"></a>Crie uma regra de Balanceador de Carga

Pode utilizar uma regra de Balanceador de Carga para definir a forma como o tráfego é distribuído pelas VMs. Pode definir a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Crie uma regra de Balancer de Carga *myLoadBalancerRuleWeb* para ouvir a porta 80 no *FrontendLoadBalancer* e enviar tráfego de rede equilibrado para o conjunto de endereços backend *myBackEndPool* também usando a porta 80.

1. Selecione **todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, clique em **myLoadBalancer** na lista de recursos.
2. Em **Definições**, clique em **Regras de balanceamento de carga** e, em seguida, clique em **Adicionar**.
3. Utilize estes valores para configurar a regra de equilíbrio de carga:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Introduza *o meuHTTPRule*. |
    | Protocolo | Selecione **TCP**. |
    | Porta | Insira *80*.|
    | Porto backend | Insira *80*. |
    | Conjunto de back-end | Selecione *myBackendPool*.|
    | Sonda de estado de funcionamento | Selecione *myHealthProbe*. |
    
4. Deixe o resto das predefinições e selecione **OK**.

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta secção, cria-se uma rede virtual, cria três máquinas virtuais para a piscina de backend do Balancer de Carga e, em seguida, instala o IIS nas máquinas virtuais para ajudar a testar o Balancer de Carga.

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta secção terá de substituir os seguintes parâmetros nos passos com as informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<>de nome de grupo de recursos**  | myResourceGroupSLB (Selecione grupo de recursos existentes) |
| **\<>de nome de rede virtual** | myVNet          |
| **\<>de nome da região**          | Europa ocidental      |
| **\<>espaço de endereçoI4**   | 10.1.0.0/16          |
| **\<>de nome de subnet**          | mySubnet        |
| **\<>de endereços-endereço de subnet** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

O Standard Load Balancer apenas suporta VMs com endereços IP standard na piscina de backend. Nesta secção, você vai criar três VMs (*myVM1,* *myVM2*, e *myVM3*) com um endereço IP público standard em três zonas diferentes *(Zona 1,* *Zona 2,* e *Zona 3*) que são adicionados ao pool de backend do Standard Load Balancer que foi criado anteriormente.

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Compute** > **Windows Server 2016 Datacenter**. 
   
1. Em **Criar uma máquina virtual,** digite ou selecione os seguintes valores no separador **Basics:**
   - **Subscription** > **Grupo de recursos**de subscrição : Selecione **myResourceGroupSLB**.
   - **Detalhes da** > ocorrência**Nome da máquina virtual**: Digite *myVM1*.
   - **Detalhes de exemplo****Região** > selecionar Europa Ocidental . **West Europe** > 
   - **Detalhes da** > ocorrência**Opções** de disponibilidade > Selecione **zonas de disponibilidade**. 
   - **Detalhes da** > ocorrência**A zona** de disponibilidade > Selecione **1**.
  
1. Selecione o separador **de rede** ou selecione **Seguinte: Discos,** em seguida **Seguinte: Networking**. 
   
   - Certifique-se de que são selecionados os seguintes:
       - **Rede virtual**: **myVnet**
       - **Subnet**: **myBackendSubnet**
       - **Ip público** > selecione **Criar nova**, e na janela de **endereços IP públicos,** para **SKU**, selecione **Standard**, e para zona **de disponibilidade**, selecione **Zone-redundante**
      
   - Para criar um novo grupo de segurança de rede (NSG), um tipo de firewall, no **Network Security Group,** selecione **Advanced**. 
       1. No campo do grupo de segurança de **rede Configure,** selecione **Criar nova**. 
       1. Digite *myNetworkSecurityGroup*, e selecione **OK**.

   - Para fazer do VM uma parte da piscina de backend do Balancer de Carga, complete os seguintes passos:
        - Em **Balanceamento de Carga**, para colocar esta máquina **Yes** **virtual atrás de uma solução de equilíbrio**de carga existente?
        - Nas **definições de equilíbrio de carga**, para opções de equilíbrio de **carga,** selecione o equilíbrio de **carga Azure**.
        - Para **selecionar um equilíbrior**de carga, *myLoadBalancer*. 
1. Selecione o separador **Gestão** ou selecione **Next** > **Management**. Sob **monitorização,** descoloque os **diagnósticos da bota** para **desligar**. 
1. Selecione **Rever + criar**.   
1. Reveja as definições e, em seguida, selecione **Criar**.
1. Siga os passos para criar dois VMs adicionais - *myVM2* e *myVM3*, com um endereço IP público Padrão SKU na zona de **disponibilidade** **2** e **3,** respectivamente, e todas as outras configurações as mesmas que *myVM1*.  

### <a name="create-network-security-group-rule"></a>Criar regra de grupo de segurança de rede

Nesta secção, cria uma regra do grupo de segurança da rede para permitir ligações de entrada utilizando http.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**, e depois a partir da lista de recursos clique no **myNetworkSecurityGroup** que está localizado no grupo de recursos **myResourceGroupSLB.**
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

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos,** e depois a partir da lista de recursos clique no **myVM1** que está localizado no grupo de recursos *myResourceGroupSLB.*
2. Na página **Descrição geral**, clique em **Ligar** para estabelecer o RDP para a VM.
3. Na janela de pop-up **Ligar à máquina virtual**, selecione **Transferir Ficheiro RDP** e, em seguida, abra o ficheiro RDP transferido.
4. Na janela **Ligação de Ambiente de Trabalho Remoto**, clique em **Ligar**.
5. Inicie sessão na VM com as credenciais que forneceu durante a criação desta VM. Esta ação inicia uma sessão de ambiente de trabalho remoto com a máquina virtual *myVM1*.
6. No ambiente de trabalho do servidor, navegue para **windows administrative tools**>**Windows PowerShell**.
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

## <a name="test-the-load-balancer"></a>Testar o Balanceador de Carga
1. Localize o endereço IP público do Balanceador de Carga no ecrã **Descrição geral**. Selecione **todos os serviços** no menu à esquerda, selecione **Todos os recursos,** e, em seguida, clique no **myPublicIP**.

2. Copie o endereço IP público e cole-o na barra de endereço do browser. A página predefinida do servidor Web do IIS é apresentada no browser.

      ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver o Balancer de carga distribuir tráfego pelos três VMs que executam a sua aplicação, pode atualizar o seu navegador web à força.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Remover ou adicionar VMs do conjunto de back-end
Pode ter de realizar a manutenção nas VMs que estão a executar a aplicação, como instalar atualizações do SO. Para lidar com maior volume de tráfego para a aplicação, pode ter de adicionar mais VMs. Esta secção mostra-lhe como remover ou adicionar um VM *(myVM1*) do Balancer de Carga.

### <a name="remove-vm-from-a-backend-pool"></a>Remova vM de uma piscina de backend
Para remover *o myVM1* da piscina de backend, complete os seguintes passos:

1. Selecione **todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, clique em **myLoadBalancer** na lista de recursos.
2. Em **Definições**, clique em **Conjuntos de back-end** e, em seguida, na lista de conjuntos de back-end, clique em **myBackendPool**.
3. Na página **myBackendPool,** para remover o *VM1,* selecione o ícone de exclusão no final da linha que apresenta *o myVM1*, e, em seguida, clique em **Guardar**.

Como *myVM1* já não está no conjunto de endereços de back-end, pode realizar quaisquer tarefas de manutenção na *myVM1*, como a instalação de atualizações de software. Na ausência de *VM1,* a carga é agora equilibrada em todo o *myVM2* e *myVM3*. 

### <a name="add-vm-to-a-backend-pool"></a>Adicione VM a uma piscina de backend
Para adicionar *o myVM1* de volta à piscina de backend, complete os seguintes passos:

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myVM1** da lista de recursos.
2. Na página **VM1,** em **Definições,** selecione **Networking**.
3. Na página **de Networking,** selecione o separador de **equilíbrio de carga** e, em seguida, selecione Adicionar equilíbrio de **carga**.
4. Na página de equilíbrio de **carga Adicionar,** faça o seguinte:
   1. Para **opções de equilíbrio**de carga, selecione o equilíbrio de carga **Azure**.
   2. Para **selecionar um equilíbrio de carga,** selecione *myLoadBalancer*.
   3. Para **selecionar uma piscina de backend,** selecione *myBackendPool*. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, elimine o grupo de recursos, o Balancer de Carga e todos os recursos conexos. Para tal, selecione o grupo de recursos *myResouceGroupSLB* que contém o Balancer de Carga e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um Balancer de Carga Padrão, anexou-lhe VMs, configurou a regra de tráfego do Balancer de Carga, sonda de saúde e, em seguida, testou o Balancer de Carga. Além disso, removeu uma VM do conjunto com balanceamento de carga e adicionou a VM de novo ao conjunto de endereços de back-end. Para saber mais sobre o Balanceador de Carga do Azure, avance para os tutoriais do Balanceador de Carga do Azure.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
