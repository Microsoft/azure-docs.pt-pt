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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80240366"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Tutorial: Carregar o tráfego de internet para VMs usando o portal Azure

O balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por várias máquinas virtuais. Neste tutorial, você aprende sobre os diferentes componentes do Azure Standard Load Balancer que distribuem o tráfego de internet para VMs e fornecem alta disponibilidade. Saiba como:


> [!div class="checklist"]
> * Criar um equilibrador de carga Azure
> * Criar recursos do balanceador de carga
> * Criar máquinas virtuais e instalar o servidor IIS
> * Ver Balanceador de Carga em ação
> * Adicione e remova VMs de um equilibrador de carga

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Criar um Balanceador de Carga Standard

Nesta secção, cria-se um Balancer de Carga Padrão que ajuda a carregar máquinas virtuais de equilíbrio. O Balanceador de Carga Standard só suporta endereços IP Públicos Standard. Quando cria um Balanceador de Carga Standard, também tem de criar um novo endereço IP Público Standard que seja configurado como o front-end (denominado *LoadBalancerFrontend* por predefinição) para o mesmo. 

1. No lado superior esquerdo do ecrã, clique em **Criar um equilibrador**de carga de  >  **rede de**recursos  >  **Load Balancer**.
2. No separador **Básico da** página **'Criar balanceador** de carga', introduzir ou selecionar as seguintes informações, aceitar as predefinições para as definições restantes e, em seguida, selecionar **Rever + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | **Selecione Criar novo** e *digite myResourceGroupSLB* na caixa de texto.|
    | Nome                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Tipo          | Selecione **Público**.                                        |
    | SKU           | Selecione **Standard**.                          |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público              | Digite *o myPublicIP* na caixa de texto.   |
    |Zona de disponibilidade| **Selecione Zona redundante**.    |

3. No **separador 'Rever +' criar,** clique em **Criar.**

   ![Criar um Balanceador de Carga Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta secção, configura as definições do Balancer de Carga para um pool de endereços de backend, uma sonda de saúde e especifica uma regra do equilíbrio.

### <a name="create-a-backend-address-pool"></a>Criar um conjunto de endereços de back-end

Para distribuir o tráfego para os VMs, um conjunto de endereços de backend contém os endereços IP dos (NICs) virtuais ligados ao Balanceador de Carga. Crie o pool de endereços de backend *myBackendPool* para incluir máquinas virtuais para equilibrar o tráfego de internet.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, clique no **myLoadBalancer** da lista de recursos.
2. Em **Definições**, clique em **Conjuntos de back-end** e, em seguida, clique em **Adicionar**.
3. Na página de **piscina de backend,** para nome, escreva *myBackendPool,* como o nome da sua piscina de backend e, em seguida, selecione **Add**.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir que o Balanceador de Carga monitorize o estado da sua aplicação, utilize uma sonda de saúde. A sonda de saúde adiciona ou remove os VMs da rotação do Balanceador de Carga com base na sua resposta aos controlos de saúde. Crie uma sonda de estado de funcionamento, *myHealthProbe*, para monitorizar o estado de funcionamento das VMs.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, clique no **myLoadBalancer** da lista de recursos.
2. Em **Definições**, clique em **Sondas de estado de funcionamento** e, em seguida, clique em **Adicionar**.
3. Utilize estes valores para criar a sonda de estado de funcionamento:
     
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *o myHealthProbe*. |
    | Protocolo | Selecione **HTTP**. |
    | Porta | Insira *80*.|
    | Intervalo | Introduza *15* para o número de **intervalos** em segundos entre tentativas de sonda. |
    | Limiar com funcionamento incorreto | Selecione *2* para o número de **falhas de sonda insalubres** ou falhas de sonda consecutivas que devem ocorrer antes de um VM ser considerado insalubre.|
    
4. Selecione **OK**.

### <a name="create-a-load-balancer-rule"></a>Crie uma regra de Balanceador de Carga

Pode utilizar uma regra de Balanceador de Carga para definir a forma como o tráfego é distribuído pelas VMs. Pode definir a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Crie uma regra de balanceador de carga *myLoadBalancerRuleWeb* para ouvir a porta 80 no *frontend FrontendLoadBalancer* e enviar tráfego de rede equilibrado para o pool de endereços de backend *myEndPool* também usando a porta 80.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, clique no **myLoadBalancer** da lista de recursos.
2. Em **Definições**, clique em **Regras de balanceamento de carga** e, em seguida, clique em **Adicionar**.
3. Utilize estes valores para configurar a regra de equilíbrio de carga:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *myHTTPRule*. |
    | Protocolo | Selecione **TCP**. |
    | Porta | Insira *80*.|
    | Porta de back-end | Insira *80*. |
    | Conjunto de back-end | Selecione *myBackendPool*.|
    | Sonda de estado de funcionamento | Selecione *myHealthProbe*. |
    
4. Deixe o resto das predefinições e selecione **OK**.

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta secção, cria-se uma rede virtual, cria três máquinas virtuais para o pool de backend do Balanceador de Carga e, em seguida, instala o IIS nas máquinas virtuais para ajudar a testar o Balanceador de Carga.

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta secção, deverá substituir os seguintes parâmetros nos passos pelas informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupSLB (Selecione o grupo de recursos existente) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa Ocidental      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

O Balancer de Carga Padrão só suporta VMs com endereços IP standard no pool de backend. Nesta secção, você irá criar três VMs (*myVM1,* *myVM2*, e *myVM3*) com um endereço IP público standard em três zonas diferentes (*Zona 1*, *Zona 2*e *Zona 3*) que são adicionados ao pool de backend do Balanceador de Carga Padrão que foi criado anteriormente.

1. No lado superior esquerdo do portal, selecione **Criar um**  >  **datacenter Compute**  >  **Windows Server 2016**. 
   
1. Na **Criação de uma máquina virtual,** escreva ou selecione os seguintes valores no **separador Básicos:**
   - **Assinatura**  >  **Grupo de Recursos**: Selecione **myResourceGroupSLB**.
   - **Detalhes da**  >  instância **Nome da máquina virtual**: *Digite myVM1*.
   - **Detalhes da**  >  instância **Região** > selecione **Europa Ocidental.**
   - **Detalhes da**  >  instância **Opções de disponibilidade** > Selecionar **zonas de disponibilidade.** 
   - **Detalhes da**  >  instância **Zona de disponibilidade** > Selecione **1**.
  
1. Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos**e, em seguida, **seguinte: Networking**. 
   
   - Certifique-se de que estão selecionados:
       - **Rede virtual**: **myVnet**
       - **Sub-rede**: **myBackendSubnet**
       - **IP público** > selecionar **Criar novos,** e na janela **de endereços IP públicos,** para **SKU,** selecione **Standard**, e para zona **de Disponibilidade,** selecione **Zona redundante**
      
   - Para criar um novo grupo de segurança de rede (NSG), um tipo de firewall, no **âmbito do Network Security Group,** selecione **Advanced**. 
       1. No campo de grupo de segurança de **rede Configure,** selecione **Criar novo**. 
       1. Digite *myNetworkSecurityGroup*e selecione **OK**.

   - Para fazer do VM uma parte da piscina de backend do Balanceador de Carga, complete os seguintes passos:
        - In **Load Balance,** for Place this virtual machine behind **Yes** **a existing load balance solution?**
        - Nas **definições de equilíbrio de carga**, para **opções de equilíbrio de carga,** selecione **o balançador de carga Azure**.
        - Para **Selecionar um equilibrador de carga**, *myLoadBalancer*. 
1. Selecione o separador **Gestão** ou selecione **'Gestão**  >  **Seguinte'.** Em **Monitorização**, desemisse os **diagnósticos** **da**Bota . 
1. Selecione **Rever + criar**.   
1. Reveja as definições e, em seguida, **selecione Criar**.
1. Siga os passos para criar dois VMs adicionais - *myVM2* e *myVM3*, com um endereço IP público Standard SKU na **zona de disponibilidade** **2** e **3** respectivamente, e todas as outras configurações iguais ao *myVM1*.  

### <a name="create-network-security-group-rule"></a>Criar regra de grupo de segurança de rede

Nesta secção, cria-se uma regra do grupo de segurança da rede para permitir ligações de entrada utilizando HTTP.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, da lista de recursos clique no **myNetworkSecurityGroup** que está localizado no grupo de recursos **myResourceGroupSLB.**
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

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, da lista de recursos clique no **myVM1** que está localizado no grupo de recursos *myResourceGroupSLB.*
2. Na página **Descrição geral**, clique em **Ligar** para estabelecer o RDP para a VM.
3. Na janela de pop-up **Ligar à máquina virtual**, selecione **Transferir Ficheiro RDP** e, em seguida, abra o ficheiro RDP transferido.
4. Na janela **Ligação de Ambiente de Trabalho Remoto**, clique em **Ligar**.
5. Inicie sessão na VM com as credenciais que forneceu durante a criação desta VM. Esta ação inicia uma sessão de ambiente de trabalho remoto com a máquina virtual *myVM1*.
6. No ambiente de trabalho do servidor, navegue para o **Windows Administrative Tools**Windows > **PowerShell**.
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

## <a name="test-the-load-balancer"></a>Testar o balançador de carga
1. Localize o endereço IP público do Balanceador de Carga no ecrã **Descrição geral**. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, clique no **myPublicIP**.

2. Copie o endereço IP público e cole-o na barra de endereço do browser. A página predefinida do servidor Web do IIS é apresentada no browser.

      ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver o Balancer de Carga distribuir tráfego pelos três VMs que executam a sua aplicação, pode forçar a atualização do seu navegador web.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Remover ou adicionar VMs do conjunto de back-end
Pode ter de realizar a manutenção nas VMs que estão a executar a aplicação, como instalar atualizações do SO. Para lidar com maior volume de tráfego para a aplicação, pode ter de adicionar mais VMs. Esta secção mostra-lhe como remover ou adicionar um VM *(myVM1*) do Balanceador de Carga.

### <a name="remove-vm-from-a-backend-pool"></a>Remova VM de uma piscina de backend
Para remover *o myVM1* da piscina de backend, complete os seguintes passos:

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, clique no **myLoadBalancer** da lista de recursos.
2. Em **Definições**, clique em **Conjuntos de back-end** e, em seguida, na lista de conjuntos de back-end, clique em **myBackendPool**.
3. Na página **myBackendPool,** para remover *VM1* selecione o ícone de exclusão no final da linha que exibe o *myVM1*e, em seguida, clique em **Guardar**.

Como *myVM1* já não está no conjunto de endereços de back-end, pode realizar quaisquer tarefas de manutenção na *myVM1*, como a instalação de atualizações de software. Na ausência de *VM1,* a carga está agora equilibrada através *do myVM2* e *do myVM3*. 

### <a name="add-vm-to-a-backend-pool"></a>Adicione VM a uma piscina de backend
Para adicionar *o myVM1* de volta à piscina de backend, complete os seguintes passos:

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myVM1** da lista de recursos.
2. Na página **VM1,** em **Definições,** selecione **Networking**.
3. Na página **'Rede',** selecione o **separador de equilíbrio de carga** e, em seguida, selecione Adicionar o equilíbrio de **carga**.
4. Na página de equilíbrio de **carga adicionar,** faça o seguinte:
   1. Para **opções de equilíbrio de carga**, selecione o **equilibrador de carga Azure**.
   2. Para **Selecionar um equilibrador de carga,** selecione *myLoadBalancer*.
   3. Para **selecionar uma piscina de backend**, selecione *myBackendPool*. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não forem necessários, elimine o grupo de recursos, o Balanceador de Carga e todos os recursos relacionados. Para tal, selecione o grupo de recursos *myResouceGroupSLB* que contém o Balanceador de Carga e, em seguida,  **selecione Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um Balancer de Carga Padrão, anexou VMs ao mesmo, configuraram a regra de tráfego do Balanceador de Carga, a sonda de saúde e, em seguida, testou o Balanceador de Carga. Além disso, removeu uma VM do conjunto com balanceamento de carga e adicionou a VM de novo ao conjunto de endereços de back-end. Para saber mais sobre o Balanceador de Carga do Azure, avance para os tutoriais do Balanceador de Carga do Azure.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
