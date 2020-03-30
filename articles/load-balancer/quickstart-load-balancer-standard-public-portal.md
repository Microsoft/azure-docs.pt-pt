---
title: Quickstart:Criar um balancer de carga público - portal Azure
titleSuffix: Azure Load Balancer
description: Este quickstart mostra como criar um Balancer de Carga utilizando o portal Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: b424fe315737b84479283eed2d77398c8ce4f148
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78898838"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Quickstart: Criar um Balancer de carga para carregar VMs de equilíbrio utilizando o portal Azure

O balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por várias máquinas virtuais. Pode utilizar o Portal do Azure para criar um balanceador de carga que faça o balanceamento de carga das máquinas virtuais (VMs). Este quickstart mostra-lhe como carregar VMs de equilíbrio usando um Balancer de Carga público.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no portal [https://portal.azure.com](https://portal.azure.com)Azure em .

## <a name="create-a-load-balancer"></a>Criar um Balanceador de Carga

Nesta secção, cria-se um Balancer de carga que ajuda a carregar máquinas virtuais de equilíbrio. Pode criar um Balancer de Carga público ou um Balancer de Carga interna. Quando criar um Balancer de Carga público, também deve criar um novo endereço IP público que esteja configurado como o frontend (nomeado como *LoadBalancerFrontend* por padrão) para o Balancer de Carga.

1. No lado superior esquerdo do ecrã, selecione **Criar um** > equilíbrio de**carga**de**rede** > de recursos .
2. No separador **Basics** da página **'Criar balanceor de carga',** introduzir ou selecionar as seguintes informações, aceitar as predefinições para as restantes definições e, em seguida, selecionar **Rever + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **Criar novo** e digitar *o myResourceGroupSLB* na caixa de texto.|
    | Nome                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Tipo          | Selecione **Public**.                                        |
    | SKU           | Selecione **Standard** ou **Basic**. A Microsoft recomenda a Standard para cargas de trabalho de produção. |
    | Endereço IP público | Selecione **Criar novo**. Se tiver um IP público existente que gostaria de utilizar, selecione **Use existente** |
    | Nome do endereço IP público              | Digite *o meu PublicIP* na caixa de texto.   Utilizar ```-SKU Basic``` para criar um IP Público Básico. Os IPs públicos básicos não são compatíveis com o equilíbrio de carga **Standard.** A Microsoft recomenda a utilização **do Standard** para cargas de trabalho de produção.|
    | Zona de disponibilidade | Digite *zona redundante* para criar um equilibrador de carga resiliente. Para criar um Balancer de Carga zonal, selecione uma zona específica de 1, 2 ou 3 |

> [!IMPORTANT]
> O resto deste quickstart assume que o **Standard** SKU é escolhido durante o processo de seleção SKU acima.


3. No **'Rever + criar** separador', selecione **Criar**.   

    ![Criar um Balanceador de Carga Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Criar recursos de Balancer de Carga

Nesta secção, configura as definições do Balancer de Carga para um conjunto de endereços de backend, uma sonda de saúde e especifica uma regra de equilíbrio.

### <a name="create-a-backend-pool"></a>Criar uma piscina backend

Para distribuir o tráfego para os VMs, um conjunto de endereços de backend contém os endereços IP do virtual (NICs) ligados ao Balanceor de Carga. Crie o conjunto de endereços backend *myBackendPool* para incluir máquinas virtuais para equilibrar o tráfego de internet de equilíbrio de carga.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** da lista de recursos.
2. Em **Definições,** selecione **piscinas backend**e, em seguida, selecione **Adicionar**.
3. Na página adicionar uma página **de piscina de backend,** para nome, digite *myBackendPool,* como o nome para a sua piscina de backend, e, em seguida, selecione **Adicionar**.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir que o Balancer de carga monitorize o estado da sua aplicação, utilize uma sonda de saúde. A sonda de saúde adiciona ou remove dinamicamente os VMs da rotação do Balancer de Carga com base na sua resposta aos controlos de saúde. Crie uma sonda de estado de funcionamento, *myHealthProbe*, para monitorizar o estado de funcionamento das VMs.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** da lista de recursos.
2. Em **Definições,** selecione **sondas de saúde**e, em seguida, selecione **Adicionar**.
    
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Introduza *o myHealthProbe*. |
    | Protocolo | Selecione **HTTP**. |
    | Porta | Insira *80*.|
    | Intervalo | Introduza *15* para o número de **intervalos** em segundos entre as tentativas de sonda. |
    | Limiar com funcionamento incorreto | Selecione **2** para o número de falhas de **limiar não saudáveis** ou de sonda consecutivas que devem ocorrer antes de um VM ser considerado insalubre.|
    | | |
4. Selecione **OK**.

### <a name="create-a-load-balancer-rule"></a>Crie uma regra de Balanceador de Carga
Pode utilizar uma regra de Balanceador de Carga para definir a forma como o tráfego é distribuído pelas VMs. Pode definir a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Crie uma regra de Balancer de Carga *myLoadBalancerRuleWeb* para ouvir a porta 80 no *FrontendLoadBalancer* e enviar tráfego de rede equilibrado para o conjunto de endereços backend *myBackEndPool* também usando a porta 80. 

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** da lista de recursos.
2. Em **Definições,** selecione regras de **equilíbrio de carga**e, em seguida, selecione **Adicionar**.
3. Utilize estes valores para configurar a regra de balanceamento de carga:
    
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Introduza *o meuHTTPRule*. |
    | Protocolo | Selecione **TCP**. |
    | Porta | Insira *80*.|
    | Porto backend | Insira *80*. |
    | Conjunto de back-end | Selecione *myBackendPool*.|
    | Sonda de estado de funcionamento | Selecione *myHealthProbe*. |
4. Deixe o resto das predefinições e, em seguida, selecione **OK**.


## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta secção, cria-se uma rede virtual, cria três máquinas virtuais para a piscina de backend do Balancer de Carga e, em seguida, instala o IIS nas máquinas virtuais para ajudar a testar o Balancer de Carga.

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta secção terá de substituir os seguintes parâmetros nos passos com as informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<>de nome de grupo de recursos**  | myResourceGroupSLB |
| **\<>de nome de rede virtual** | myVNet          |
| **\<>de nome da região**          | Europa ocidental      |
| **\<>espaço de endereçoI4**   | 10.1.0.0\16          |
| **\<>de nome de subnet**          | myBackendSubnet        |
| **\<>de endereços-endereço de subnet** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Criar máquinas virtuais
As SKUs públicas ip e load Balancer SKUs devem corresponder. Para o Standard Load Balancer, utilize VMs com endereços IP standard na piscina de backend. Nesta secção, você vai criar três VMs (*myVM1,* *myVM2* e *myVM3*) com um endereço IP público Standard em três zonas diferentes *(Zona 1,* *Zona 2,* e *Zona 3*) que são posteriormente adicionados à piscina de backend do Balancer load que foi criado anteriormente. Se selecionar Basic, utilize VMs com endereços IP básicos.

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Compute** > **Windows Server 2019 Datacenter**. 
   
1. Em **Criar uma máquina virtual,** digite ou selecione os seguintes valores no separador **Basics:**
   - **Subscription** > **Grupo de recursos**de subscrição : Selecione **myResourceGroupSLB**.
   - **Detalhes da** > ocorrência**Nome da máquina virtual**: Digite *myVM1*.
   - **Detalhes de exemplo****Região** > selecionar Europa Ocidental . **West Europe** > 
   - **Detalhes da** > ocorrência**Opções** de disponibilidade > Selecione **zonas de disponibilidade**. 
   - **Detalhes da** > ocorrência**A zona** de disponibilidade > Selecione **1**.
   - **Conta administrador>** Insira o nome de **utilizador,** **palavra-passe** e confirme informações **de palavra-passe.**
   - Selecione o separador **de rede** ou selecione **Seguinte: Discos,** em seguida **Seguinte: Networking**.
  
1. No separador **de rede** certifique-se de que são selecionados os seguintes:
   - **Rede virtual**: *myVnet*
   - **Subnet**: *myBackendSubnet*
   - **Ip público** > selecione **Criar nova**, e na janela de **endereçoip público Criar,** para **SKU**, selecione **Standard**, e para a zona **de disponibilidade,** selecione **Zone-redundante**, e, em seguida, selecione **OK**. Se criou um Equilíbrio de Carga Básico, selecione Basic. A Microsoft recomenda a utilização do Standard SKU para cargas de trabalho de produção.
   - Para criar um novo grupo de segurança de rede (NSG), um tipo de firewall, no **Network Security Group,** selecione **Advanced**. 
       1. No campo do grupo de segurança de **rede Configure,** selecione **Criar nova**. 
       1. Digite *myNetworkSecurityGroup*, e selecione **OK**.
   - Para fazer do VM uma parte da piscina de backend do Balancer de Carga, complete os seguintes passos:
        - Em **Balanceamento de Carga**, para colocar esta máquina **Yes** **virtual atrás de uma solução de equilíbrio**de carga existente?
        - Nas **definições de equilíbrio de carga**, para opções de equilíbrio de **carga,** selecione o equilíbrio de **carga Azure**.
        - Para **selecionar um equilíbrior**de carga, *myLoadBalancer*.
        - Selecione o separador **Gestão** ou selecione **Next** > **Management**.
2. No separador **Gestão,** sob **monitorização,** desembare **os diagnósticos da bota** para **desligar**. 
1. Selecione **Rever + criar**.   
1. Reveja as definições e, em seguida, selecione **Criar**.
1. Siga os passos 2 a 6 para criar dois VMs adicionais com os seguintes valores e todas as outras definições as mesmas que *myVM1:*

    | Definição | VM 2| VM 3|
    | ------- | ----- |---|
    | Nome |  *myVM2* |*myVM3*|
    | Zona de disponibilidade | 2 |3|
    |IP público| **Padrão** SKU|**Padrão** SKU|
    | IP Público - Zona de disponibilidade| **Zona redundante** |**Zona redundante**|
    | Grupo de segurança de rede | Selecione o *grupo myNetworkSecurity* existente| Selecione o *grupo myNetworkSecurity* existente|

 ### <a name="create-nsg-rule"></a>Criar regra NSG

Nesta secção, cria uma regra do grupo de segurança da rede para permitir ligações de entrada utilizando http.

1. Selecione **todos os serviços** no menu à esquerda, selecione **Todos os recursos,** e depois a partir da lista de recursos selecione **myNetworkSecurityGroup** que está localizado no grupo de recursos **myResourceGroupSLB.**
2. Em **Definições**, selecione **Regras de segurança de entrada** e, em seguida, selecione **Adicionar**.
3. Introduza estes valores para a regra de segurança de entrada com o nome *myHTTPRule* para permitir ligações HTTP de entrada através da porta 80:
    - **Fonte**: *Etiqueta de serviço*
    -  **Etiqueta de serviço de origem**: *Internet*
    - **Gamas de porta de destino**: *80*
    - **Protocolo**: *TCP*
    - **Ação**: *Permitir*
    - **Prioridade**: *100*
    - **Nome**: *myHTTPRule* 
    - **Descrição**: "*Permitir HTTP* 
4. Selecione **Adicionar**.
5. Repita os passos para a regra do PDR de entrada, se necessário, com os seguintes valores diferentes:
   - **Gamas de porta de destino**: Tipo *3389*.
   - **Prioridade**: Tipo *200*. 
   - **Nome**: Type *MyRDPRule*. 
   - **Descrição**: Tipo *Permitir RDP*. 
 
### <a name="install-iis"></a>Instalar o IIS

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**, e depois da lista de recursos, selecione **myVM1** que está localizado no grupo de recursos *myResourceGroupSLB.*
2. Na página **Descrição geral**, selecione **Ligar** para estabelecer o RDP para a VM.
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
1. Localize o endereço IP público do Balanceador de Carga no ecrã **Descrição geral**. Selecione **todos os serviços** no menu à esquerda, selecione **Todos os recursos,** e depois selecione **myPublicIP**.

2. Copie o endereço IP público e cole-o na barra de endereço do browser. A página predefinida do servidor Web do IIS é apresentada no browser.

   ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver o Balancer de carga distribuir tráfego pelos três VMs, pode personalizar a página padrão do servidor Web IIS de cada VM e, em seguida, refrescar o seu navegador web a partir da máquina cliente.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, o Balancer de Carga e todos os recursos conexos. Para tal, selecione o grupo de recursos *(myResourceGroupSLB)* que contém o Balancer de Carga e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um Balancer de Carga Padrão, anexou-lhe VMs, configurou a regra de tráfego do Balancer de Carga, a sonda de saúde e, em seguida, testou o Balancer de Carga. Para saber mais sobre o Azure Load Balancer, continue a colocar [os tutoriais do Azure Load Balancer.](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

Saiba mais sobre as zonas de [Balancer de Carga e Disponibilidade.](load-balancer-standard-availability-zones.md)
