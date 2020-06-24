---
title: Quickstart:Criar um balanceador de carga público - portal Azure
titleSuffix: Azure Load Balancer
description: Este quickstart mostra como criar um Balanceador de Carga utilizando o portal Azure.
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
ms.openlocfilehash: 99a2de4cd8a19d3f05b9dc37f3bcd08cd84b2e68
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052747"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Quickstart: Criar um Balanceador de Carga para carregar VMs de equilíbrio utilizando o portal Azure

O balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por várias máquinas virtuais. Pode utilizar o Portal do Azure para criar um balanceador de carga que faça o balanceamento de carga das máquinas virtuais (VMs). Este quickstart mostra-lhe como carregar VMs de equilíbrio usando um Balancer de Carga público.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Criar um Balanceador de Carga

Nesta secção, cria-se um Balancer de Carga que ajuda a carregar máquinas virtuais de equilíbrio. Pode criar um balanceador de carga público ou um balanceador de carga interno. Quando criar um Balancer de Carga público, também deve criar um novo endereço IP público configurado como frontend (denominado *LoadBalancerFrontend* por padrão) para o Balanceador de Carga.

1. No lado superior esquerdo do ecrã, selecione **Criar um**  >  equilibrador de carga de**rede de**recursos  >  **Load Balancer**.
2. No separador **Básico da** página **'Criar balanceador** de carga', introduzir ou selecionar as seguintes informações, aceitar as predefinições para as definições restantes e, em seguida, selecionar **Rever + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | **Selecione Criar novo** e *digite myResourceGroupSLB* na caixa de texto.|
    | Name                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Tipo          | Selecione **Público**.                                        |
    | SKU           | Selecione **Standard** ou **Basic**. A Microsoft recomenda a Standard para cargas de trabalho de produção. |
    | Endereço IP público | Selecione **Criar novo**. Se tiver um IP público existente que gostaria de utilizar, selecione **Use existente** |
    | Nome do endereço IP público              | Digite *o myPublicIP* na caixa de texto.   Use ```-SKU Basic``` para criar um IP público básico. Os IPs públicos básicos não são compatíveis com **o balanceador de** carga standard. A Microsoft recomenda a utilização **do Standard** para cargas de trabalho de produção.|
    | Zona de disponibilidade | Tipo *Zona redundante* para criar um equilibrador de carga resistente. Para criar um Balanceador de Carga zonal, selecione uma zona específica a partir de 1, 2 ou 3 |

> [!IMPORTANT]
> O resto deste quickstart pressupõe que **o Standard** SKU é escolhido durante o processo de seleção SKU acima.


3. No **separador 'Rever + criar',** selecione **Criar.**   

    ![Criar um Balanceador de Carga Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta secção, configura as definições do Balancer de Carga para um pool de endereços de backend, uma sonda de saúde e especifica uma regra do equilíbrio.

### <a name="create-a-backend-pool"></a>Criar uma piscina backend

Para distribuir o tráfego para os VMs, um conjunto de endereços de backend contém os endereços IP dos (NICs) virtuais ligados ao Balanceador de Carga. Crie o pool de endereços de backend *myBackendPool* para incluir máquinas virtuais para equilibrar o tráfego de internet.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.
2. Em **Definições**, selecione **pools backend**e, em seguida, selecione **Adicionar**.
3. Na página de **piscina de backend,** para nome, escreva *myBackendPool,* como o nome da sua piscina de backend e, em seguida, selecione **Add**.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir que o Balanceador de Carga monitorize o estado da sua aplicação, utilize uma sonda de saúde. A sonda de saúde adiciona ou remove os VMs da rotação do Balanceador de Carga com base na sua resposta aos controlos de saúde. Crie uma sonda de estado de funcionamento, *myHealthProbe*, para monitorizar o estado de funcionamento das VMs.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.
2. Em **Definições**, selecione **sondas de saúde**e, em seguida, selecione **Adicionar**.
    
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *o myHealthProbe*. |
    | Protocolo | Selecione **HTTP**. |
    | Porta | Insira *80*.|
    | Intervalo | Introduza *15* para o número de **intervalos** em segundos entre tentativas de sonda. |
    | Limiar com funcionamento incorreto | Selecione **2** para o número de **falhas de sonda insalubres** ou falhas de sonda consecutivas que devem ocorrer antes de um VM ser considerado insalubre.|
    | | |
4. Selecione **OK**.

### <a name="create-a-load-balancer-rule"></a>Crie uma regra de Balanceador de Carga
Pode utilizar uma regra de Balanceador de Carga para definir a forma como o tráfego é distribuído pelas VMs. Pode definir a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Crie uma regra de balanceador de carga *myLoadBalancerRuleWeb* para ouvir a porta 80 no *frontend FrontendLoadBalancer* e enviar tráfego de rede equilibrado para o pool de endereços de backend *myEndPool* também usando a porta 80. 

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.
2. Em **Definições**, selecione **regras de equilíbrio de carga**e, em seguida, selecione **Adicionar**.
3. Utilize estes valores para configurar a regra de balanceamento de carga:
    
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *myHTTPRule*. |
    | Protocolo | Selecione **TCP**. |
    | Porta | Insira *80*.|
    | Porto backend | Insira *80*. |
    | Conjunto de back-end | Selecione *myBackendPool*.|
    | Sonda de estado de funcionamento | Selecione *myHealthProbe*. |
4. Deixe o resto dos predefinidos e, em seguida, selecione **OK**.


## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta secção, cria-se uma rede virtual, cria três máquinas virtuais para o pool de backend do Balanceador de Carga e, em seguida, instala o IIS nas máquinas virtuais para ajudar a testar o Balanceador de Carga.

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta secção, deverá substituir os seguintes parâmetros nos passos pelas informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupSLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa Ocidental      |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Criar máquinas virtuais
Os SKUs IP e os SKUs do Balanceador de Carga devem coincidir. Para o Balanceador de Carga Padrão, utilize VMs com endereços IP standard no pool de backend. Nesta secção, irá criar três VMs (*myVM1,* *myVM2* e *myVM3*) com um endereço IP público standard em três zonas diferentes (*Zona 1*, *Zona 2*e *Zona 3*) que são posteriormente adicionados ao pool de backend do Balanceador de Carga que foi criado anteriormente. Se selecionar Basic, utilize VMs com endereços IP básicos.

1. No lado superior esquerdo do portal, selecione **Criar um**  >  **datacenter Compute**  >  **Windows Server 2019**de recurso . 
   
1. Na **Criação de uma máquina virtual,** escreva ou selecione os seguintes valores no **separador Básicos:**
   - **Assinatura**  >  **Grupo de Recursos**: Selecione **myResourceGroupSLB**.
   - **Detalhes da**  >  instância **Nome da máquina virtual**: *Digite myVM1*.
   - **Detalhes da**  >  instância **Região** > selecione **Europa Ocidental.**
   - **Detalhes da**  >  instância **Opções de disponibilidade** > Selecionar **zonas de disponibilidade.** 
   - **Detalhes da**  >  instância **Zona de disponibilidade** > Selecione **1**.
   - **Conta de administrador**> Introduza o **nome de utilizador,** **palavra-passe** e confirme as informações **de senha.**
   - Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos**e, em seguida, **seguinte: Networking**.
  
1. No **separador Networking** certifique-se de que estão selecionados:
   - **Rede virtual**: *myVnet*
   - **Sub-rede**: *myBackendSubnet*
   - **IP público** > **selecionar Criar novos**, e na janela de **endereços IP público,** para **SKU**, selecione **Standard**, e para zona **de disponibilidade**, selecione **Zone-redundante**, e, em seguida, selecione **OK**. Se criou um Balanceador de Carga Básica, selecione Basic. A Microsoft recomenda a utilização do Standard SKU para cargas de trabalho de produção.
   - Para criar um novo grupo de segurança de rede (NSG), um tipo de firewall, no **âmbito do Network Security Group,** selecione **Advanced**. 
       1. No campo de grupo de segurança de **rede Configure,** selecione **Criar novo**. 
       1. Digite *myNetworkSecurityGroup*e selecione **OK**.
   - Para fazer do VM uma parte da piscina de backend do Balanceador de Carga, complete os seguintes passos:
        - In **Load Balance,** for Place this virtual machine behind **Yes** **a existing load balance solution?**
        - Nas **definições de equilíbrio de carga**, para **opções de equilíbrio de carga,** selecione **o balançador de carga Azure**.
        - Para **Selecionar um equilibrador de carga**, *myLoadBalancer*.
        - Selecione o separador **Gestão** ou selecione **'Gestão**  >  **Seguinte'.**
2. No separador **Gestão,** em **Monitorização,** desemote **os diagnósticos boot** para **desligar**. 
1. Selecione **Rever + criar**.   
1. Reveja as definições e, em seguida, **selecione Criar**.
1. Siga os passos 2 a 6 para criar dois VMs adicionais com os seguintes valores e todas as outras definições iguais ao *myVM1*:

    | Definição | VM 2| VM 3|
    | ------- | ----- |---|
    | Name |  *myVM2* |*myVM3*|
    | Zona de disponibilidade | 2 |3|
    |IP público| **Padrão** SKU|**Padrão** SKU|
    | IP público - Zona de disponibilidade| **Zona redundante** |**Zona redundante**|
    | Grupo de segurança de rede | Selecione o *myNetworkSecurityGroup* existente| Selecione o *myNetworkSecurityGroup* existente|

 ### <a name="create-nsg-rule"></a>Criar regra NSG

Nesta secção, cria-se uma regra do grupo de segurança da rede para permitir ligações de entrada utilizando HTTP.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, da lista de recursos selecione **myNetworkSecurityGroup** que está localizado no grupo de recursos **myResourceGroupSLB.**
2. Em **Definições**, selecione **Regras de segurança de entrada** e, em seguida, selecione **Adicionar**.
3. Introduza estes valores para a regra de segurança de entrada com o nome *myHTTPRule* para permitir ligações HTTP de entrada através da porta 80:
    - **Fonte**: *Tag de serviço*
    -  **Etiqueta de serviço de origem**: *Internet*
    - **Gamas portuárias de destino**: *80*
    - **Protocolo**: *TCP*
    - **Ação**: *Permitir*
    - **Prioridade**: *100*
    - **Nome**: *myHTTPRule* 
    - **Descrição**: "*Permitir HTTP* 
4. Selecione **Adicionar**.
5. Repita os passos para a regra RDP de entrada, se necessário, com os seguintes valores diferentes:
   - **Gamas porta de destino**: Tipo *3389*.
   - **Prioridade**: Tipo *200*. 
   - **Nome**: *Digite myRDPRule*. 
   - **Descrição**: Tipo *Permitir RDP*. 
 
### <a name="install-iis"></a>Instalar o IIS

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**, e, em seguida, a partir da lista de recursos, selecione **myVM1** que está localizado no grupo de recursos *myResourceGroupSLB.*
2. Na página **Descrição geral**, selecione **Ligar** para estabelecer o RDP para a VM.
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
1. Localize o endereço IP público do Balanceador de Carga no ecrã **Descrição geral**. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myPublicIP**.

2. Copie o endereço IP público e cole-o na barra de endereço do browser. A página predefinida do servidor Web do IIS é apresentada no browser.

   ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver o Balancer de Carga distribuir tráfego através dos três VMs, pode personalizar a página predefinição de cada servidor Web IIS de cada VM e, em seguida, refrescar o seu navegador web a partir da máquina do cliente.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, o Balanceador de Carga e todos os recursos relacionados. Para tal, selecione o grupo de recursos *(myResourceGroupSLB)* que contém o Balanceador de Carga e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um Balancer de Carga Padrão, anexou VMs ao mesmo, configuraram a regra de tráfego do Balancer de Carga, a sonda de saúde e, em seguida, testou o Balanceador de Carga. Para saber mais sobre o Azure Load Balancer, continue para os [tutoriais do Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

Saiba mais sobre [as zonas de Balancer e Disponibilidade](load-balancer-standard-availability-zones.md)de Carga.
