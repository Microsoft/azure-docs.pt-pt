---
title: 'Início rápido: criar um Standard Load Balancer-portal do Azure'
titleSuffix: Azure Load Balancer
description: Este guia de início rápido mostra como criar um Standard Load Balancer usando o portal do Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Standard Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: d15223dfe6d9ce710f2a3d402a49203ef169132e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225197"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Início Rápido: Criar um Balanceador de Carga Standard para fazer o balanceamento de carga das VMs com o portal do Azure

O balanceamento de carga oferece um nível mais elevado de disponibilidade e dimensionamento ao propagar os pedidos recebidos por várias máquinas virtuais. Pode utilizar o Portal do Azure para criar um balanceador de carga que faça o balanceamento de carga das máquinas virtuais (VMs). Este início rápido mostra-lhe como fazer o balanceamento de carga de VMs mediante a utilização de um Balanceador de Carga Standard.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Criar um Balanceador de Carga Standard

Nesta seção, você cria um Standard Load Balancer que ajuda a balancear a carga de máquinas virtuais. O Balanceador de Carga Standard só suporta endereços IP Públicos Standard. Quando cria um Balanceador de Carga Standard, também tem de criar um endereço IP Público Standard novo que seja configurado como o front-end (denominado *LoadBalancerFrontend* por predefinição) para o mesmo. 

1. No canto superior esquerdo da tela, selecione **criar um recurso** > **rede** > **Load Balancer**.
2. Na guia **noções básicas** da página **criar balanceador de carga** , insira ou selecione as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **revisar + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **criar novo** e digite *myResourceGroupSLB* na caixa de texto.|
    | Nome                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Tipo          | Selecione **público**.                                        |
    | SKU           | Selecione **padrão**.                          |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público              | Digite *myPublicIP* na caixa de texto.   |
    |Zona de disponibilidade| Selecione a **zona com redundância**.    |
3. Na guia **revisar + criar** , selecione **criar**.   

    ![Criar um Balanceador de Carga Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Criar Load Balancer recursos

Nesta seção, você define Load Balancer configurações para um pool de endereços de back-end, uma investigação de integridade e especifica uma regra de balanceador.

### <a name="create-a-backend-address-pool"></a>Criar um conjunto de endereços de back-end

Para distribuir o tráfego para as VMs, um pool de endereços de back-end contém os endereços IP das NICs (virtual) conectados à Load Balancer. Crie o pool de endereços de back-end *myBackendPool* para incluir máquinas virtuais para tráfego de Internet de balanceamento de carga.

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.
2. Em **configurações**, selecione **pools de back-end**e, em seguida, selecione **Adicionar**.
3. Na página **Adicionar um pool de back-end** , em nome, digite *myBackendPool*, como o nome do pool de back-end e, em seguida, selecione **Adicionar**.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir que o Load Balancer monitore o status do seu aplicativo, use uma investigação de integridade. A investigação de integridade adiciona ou remove dinamicamente as VMs da rotação de Load Balancer com base em sua resposta às verificações de integridade. Crie uma sonda de estado de funcionamento, *myHealthProbe*, para monitorizar o estado de funcionamento das VMs.

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.
2. Em **configurações**, selecione **investigações de integridade**e, em seguida, selecione **Adicionar**.
    
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *myHealthProbe*. |
    | Protocolo | Selecione **http**. |
    | Porta | Insira *80*.|
    | Intervalo | Digite *15* para o número de **intervalo** em segundos entre as tentativas de investigação. |
    | Limite não íntegro | Selecione **2** para número de **limites não íntegros** ou falhas de investigação consecutivas que devem ocorrer antes que uma VM seja considerada não íntegra.|
    | | |
4. Selecione **OK**.

### <a name="create-a-load-balancer-rule"></a>Crie uma regra de Balanceador de Carga
Pode utilizar uma regra de Balanceador de Carga para definir a forma como o tráfego é distribuído pelas VMs. Pode definir a configuração de IP de front-end do tráfego de entrada e o conjunto de IPs de back-end para receber o tráfego, juntamente com a porta de origem e de destino necessárias. Crie uma regra de Load Balancer *myLoadBalancerRuleWeb* para ouvir a porta 80 no front-end *FrontendLoadBalancer* e enviar o tráfego de rede com balanceamento de carga para o pool de endereços de back-end *myBackEndPool* também usando a porta 80. 

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.
2. Em **configurações**, selecione **regras de balanceamento de carga**e, em seguida, selecione **Adicionar**.
3. Utilize estes valores para configurar a regra de balanceamento de carga:
    
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *myHTTPRule*. |
    | Protocolo | Selecione **TCP**. |
    | Porta | Insira *80*.|
    | Porta de back-end | Insira *80*. |
    | Pool de back-end | Selecione *myBackendPool*.|
    | Sonda de estado de funcionamento | Selecione *myHealthProbe*. |
4. Deixe o restante dos padrões e, em seguida, selecione **OK**.


## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, você criará uma rede virtual, criará três máquinas virtuais para o pool de back-end do Load Balancer e, em seguida, instalará o IIS nas máquinas virtuais para ajudar a testar o Load Balancer.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
1. No lado superior esquerdo da tela, selecione **criar um recurso** > **rede** > **rede virtual**.

1. Em **criar rede virtual**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Introduza *myVNet*. |
    | Espaço de endereços | Insira *10.1.0.0/16*. |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | Selecione o recurso existente- *myResourceGroupSLB*. |
    | Localização | Selecione **Europa Ocidental**.|
    | Nome da sub-rede | Introduza *myBackendSubnet*. |
    | Sub-rede - Intervalo de endereços | Insira *10.1.0.0/24*. |
1. Deixe o restante dos padrões e selecione **criar**.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais
Standard Load Balancer só dá suporte a VMs com endereços IP padrão no pool de back-end. Nesta seção, você criará três VMs (*myVM1*, *myVM2* e *MYVM3*) com um endereço IP público padrão em três zonas diferentes (*zona 1*, *zona 2*e *zona 3*) que são adicionadas posteriormente ao pool de back-end do Standard Load Balancer que foi criado anteriormente.

1. No lado superior esquerdo do portal, selecione **criar um recurso** > **computação** > **Windows Server 2019 datacenter**. 
   
1. Em **criar uma máquina virtual**, digite ou selecione os seguintes valores na guia **noções básicas** :
   - Grupo de **recursos**de > de **assinatura** : selecione **myResourceGroupSLB**.
   - **Detalhes da instância** > **nome da máquina virtual**: digite *myVM1*.
   - **Detalhes da instância** > **região** > selecione **Europa Ocidental**.
   - **Detalhes da instância** > **opções de disponibilidade** > selecionar **zonas de disponibilidade**. 
   - **Detalhes da instância** > **zona de disponibilidade** > selecione **1**.
   - **Conta de administrador**> Insira as informações de **nome de usuário**, **senha** e **Confirmar senha** .
   - Selecione a guia **rede** ou selecione **Avançar: discos**e **Avançar: rede**.
  
1. Na guia **rede** , verifique se os itens a seguir estão selecionados:
   - **Rede virtual**: *myVnet*
   - **Sub-rede**: *myBackendSubnet*
   - **> IP público** selecione **criar novo**e, na janela **criar endereço IP público** , para **SKU**, selecione **Standard**e para zona de **disponibilidade**, selecione **com redundância de zona**e, em seguida, selecione **OK**.
   - Para criar um novo NSG (grupo de segurança de rede), um tipo de firewall, em **grupo de segurança de rede**, selecione **avançado**. 
       1. No campo **Configurar grupo de segurança de rede** , selecione **criar novo**. 
       1. Digite *myNetworkSecurityGroup*e selecione **OK**.
   - Para tornar a VM uma parte do pool de back-end Load Balancer, conclua as seguintes etapas:
        - No **balanceamento de carga**, para **posicionar esta máquina virtual atrás de uma solução de balanceamento de carga existente?** , selecione **Sim**.
        - Em **configurações de balanceamento**de carga, para **Opções de balanceamento de carga**, selecione **Azure Load Balancer**.
        - Para **selecionar um balanceador de carga**, *myLoadBalancer*.
        - Selecione a guia **Gerenciamento** ou selecione **próximo** **Gerenciamento**de > .
2. Na guia **Gerenciamento** , em **monitoramento**, defina **diagnóstico de inicialização** como **desativado**. 
1. Selecione **Rever + criar**.   
1. Examine as configurações e, em seguida, selecione **criar**.
1. Siga as etapas 2 a 6 para criar duas VMs adicionais com os seguintes valores e todas as outras configurações, como *myVM1*:

    | Definição | VM 2| VM 3|
    | ------- | ----- |---|
    | Nome |  *myVM2* |*myVM3*|
    | Zona de disponibilidade | 2 |3|
    |IP público| **Padrão** SKU|**Padrão** SKU|
    | IP público-zona de disponibilidade| **Com redundância de zona** |**Com redundância de zona**|
    | Grupo de segurança de rede | Selecionar o *grupo myNetworkSecurity* existente| Selecionar o *grupo myNetworkSecurity* existente|

 ### <a name="create-nsg-rule"></a>Criar regra NSG

Nesta seção, você cria uma regra de grupo de segurança de rede para permitir conexões de entrada usando HTTP.

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos**e, na lista de recursos, selecione **myNetworkSecurityGroup** que está localizado no grupo de recursos **myResourceGroupSLB** .
2. Em **Definições**, selecione **Regras de segurança de entrada** e, em seguida, selecione **Adicionar**.
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
 
### <a name="install-iis"></a>Instalar o IIS

1. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos**e, em seguida, na lista recursos, selecione **myVM1** que está localizado no grupo de recursos *myResourceGroupSLB* .
2. Na página **Descrição geral**, selecione **Ligar** para estabelecer o RDP para a VM.
5. Inicie sessão na VM com as credenciais que forneceu durante a criação desta VM. Esta ação inicia uma sessão de ambiente de trabalho remoto com a máquina virtual *myVM1*.
6. No ambiente de trabalho do servidor, navegue para **Ferramentas Administrativas do Windows**>**Windows PowerShell**.
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

## <a name="test-the-load-balancer"></a>Testar o Load Balancer
1. Localize o endereço IP público do Balanceador de Carga no ecrã **Descrição geral**. Selecione **todos os serviços** no menu à esquerda, selecione **todos os recursos**e, em seguida, selecione **myPublicIP**.

2. Copie o endereço IP público e cole-o na barra de endereço do browser. A página predefinida do servidor Web do IIS é apresentada no browser.

   ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Para ver a Load Balancer distribuir o tráfego entre todas as três VMs, você pode personalizar a página padrão do servidor Web do IIS de cada VM e, em seguida, forçar a atualização do navegador da Web no computador cliente.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos, Load Balancer e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos (*myResourceGroupSLB*) que contém o Load Balancer e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um Standard Load Balancer, as VMs anexadas a ele, configurou a regra de tráfego Load Balancer, a investigação de integridade e testou a Load Balancer. Para saber mais sobre o Balanceador de Carga do Azure, avance para os tutoriais do Balanceador de Carga do Azure.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
