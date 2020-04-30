---
title: 'Tutorial: Configure port forwarding - Portal Azure'
titleSuffix: Azure Load Balancer
description: Este tutorial mostra como configurar o encaminhamento da porta utilizando o Azure Load Balancer para criar ligações a VMs numa rede virtual Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to configure port forwarding in Azure Load Balancer to remotely connect to VMs in an Azure virtual network.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: dcb151c8be0ab3a2393d0659b75985a92ac60507
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82207892"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>Tutorial: Configure o reencaminhamento da porta no Equilíbrio de Carga Azure utilizando o portal

O encaminhamento portuário permite ligar-se a máquinas virtuais (VMs) numa rede virtual Azure utilizando um endereço IP público do Azure Load Balancer e número de porta. 

Neste tutorial, você configura a porta reencaminhando-se num Equilíbrio de Carga Azure. Saiba como:

> [!div class="checklist"]
> * Crie um equilibrador de carga standard público para equilibrar o tráfego da rede sobre VMs. 
> * Crie uma rede virtual e VMs com uma regra de grupo de segurança de rede (NSG). 
> * Adicione os VMs ao conjunto de endereços de back-end do equilibrador de carga.
> * Crie uma sonda de saúde de equilíbrio de carga e regras de tráfego.
> * Criar regras de encaminhamento de porta de entrada na nat de equilíbrio de carga.
> * Instale e configure o IIS nos VMs para ver o equilíbrio de carga e o encaminhamento da porta em ação.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

Para todos os passos deste tutorial, inscreva-se no portal Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Criar um equilibrador de carga Standard

Em primeiro lugar, crie um equilibrador de carga standard público que possa equilibrar a carga de tráfego sobre VMs. Um equilibrador de carga Standard suporta apenas um endereço IP público Standard. Quando cria um balanceor de carga Standard, também cria um novo endereço IP público Standard, que é configurado como a extremidade frontal do equilibrador de carga e denominado **LoadBalancerFrontEnd** por padrão. 

1. No lado superior esquerdo do ecrã, clique em **Criar um equilíbrio** > de**carga**de**rede** > de recursos .
2. No separador **Basics** da página **'Criar balanceor de carga',** introduzir ou selecionar as seguintes informações, aceitar as predefinições para as restantes definições e, em seguida, selecionar **Rever + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **Criar novo** e digitar *MyResourceGroupLB* na caixa de texto.|
    | Nome                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Tipo          | Selecione **Public**.                                        |
    | SKU           | Selecione **Standard**.                          |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público              | Digite *o meu PublicIP* na caixa de texto.   |
    |Zona de disponibilidade| Selecione **Zona redundante**.    |
     
    >[!NOTE]
     >Certifique-se de criar o seu Balancer de Carga e todos os recursos para ele num local que suporta Zonas de Disponibilidade. Para mais informações, consulte [Regiões que suportem Zonas de Disponibilidade.](../availability-zones/az-region.md) 

3. No **'Rever + criar** separador', clique em **Criar**.  
  
## <a name="create-and-configure-back-end-servers"></a>Criar e configurar servidores back-end

Crie uma rede virtual com duas máquinas virtuais e adicione os VMs à piscina traseira do seu equilibrador de carga. 

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta secção terá de substituir os seguintes parâmetros nos passos com as informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<>de nome de grupo de recursos**  | myResourceGroupLB (Select grupo de recursos existentes) |
| **\<>de nome de rede virtual** | myVNet          |
| **\<>de nome da região**          | Europa ocidental      |
| **\<>espaço de endereçoI4**   | 10.3.0.0\16          |
| **\<>de nome de subnet**          | myBackendSubnet        |
| **\<>de endereços-endereço de subnet** | 10.3.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>Crie VMs e adicione-os ao pool de back-end do equilibrador de carga

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Compute** > **Windows Server 2016 Datacenter**. 
   
1. Em **Criar uma máquina virtual,** digite ou selecione os seguintes valores no separador **Basics:**
   - **Subscription** > **Grupo de recursos**de subscrição : Desça e selecione **MyResourceGroupLB**.
   - **Nome da máquina virtual**: Type *MyVM1*.
   - **Região**: Selecione **Europa Ocidental**. 
   - **Nome de utilizador**: Tipo *azureuser*.
   - **Palavra-passe**: Tipo *Azure1234567*. 
     Reescreva a palavra-passe no campo **de palavra-passe Confirmar.**
   
1. Selecione o separador **de rede** ou selecione **Seguinte: Discos,** em seguida **Seguinte: Networking**. 
   
   Certifique-se de que são selecionados os seguintes:
   - **Rede virtual**: **MyVNet**
   - **Subnet**: **MyBackendSubnet**
   
1. Em **IP público,** selecione **Criar novo,** selecione **Standard** na página **de endereçoip público** e, em seguida, selecione **OK**. 
   
1. No âmbito **do Network Security Group,** selecione **Advanced** para criar um novo grupo de segurança de rede (NSG), um tipo de firewall. 
   1. No campo do grupo de segurança de **rede Configure,** selecione **Criar nova**. 
   1. Digite *MyNetworkSecurityGroup*, e selecione **OK**. 
   
   >[!NOTE]
   >Observe que, por padrão, o NSG já tem uma regra de entrada para abrir a porta 3389, a porta de ambiente de trabalho remoto (RDP).
   
1. Adicione o VM a uma piscina de back-end de equilíbrio de carga que cria:
   
   1. Sob **BALANCEAMENTO** > DE CARGA**Coloque esta máquina virtual atrás de uma solução de equilíbrio**de carga existente? **Yes** 
   1. Para **as opções de equilíbrio**de carga, desça e selecione o equilíbrio de carga **Azure**. 
   1. Para **selecionar um equilíbrio de carga,** desça e selecione **MyLoadBalancer**. 
   1. Em **selecione um pool de backend,** selecione **Criar novo,** em seguida, digite *MyBackendPool*, e selecione **Criar**. 
   
   ![Criar uma rede virtual](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. Selecione o separador **Gestão** ou selecione **Next** > **Management**. Sob **monitorização,** descoloque os **diagnósticos da bota** para **desligar**.
   
1. Selecione **Rever + criar**.
   
1. Reveja as definições e, quando a validação for bem sucedida, selecione **Criar**. 

1. Siga os passos para criar um segundo VM chamado *MyVM2,* com todas as outras definições iguais às do MyVM1. 
   
   Para **o Network Security Group**, depois de selecionar O **Avançado,** desça e selecione o **MyNetworkSecurityGroup** que já criou. 
   
   Em **select uma piscina de backend,** certifique-se de que o **MyBackendPool** está selecionado. 

### <a name="create-an-nsg-rule-for-the-vms"></a>Criar uma regra NSG para os VMs

Crie uma regra do grupo de segurança de rede (NSG) para que os VMs permitam ligações à internet de entrada (HTTP).

>[!NOTE]
>Por predefinição, o NSG já tem uma regra que abre a porta 3389, a porta de ambiente de trabalho remoto (RDP).

1. Selecione **Todos os recursos** no menu da esquerda. A partir da lista de recursos, selecione **MyNetworkSecurityGroup** no grupo de recursos **MyResourceGroupLB.**
   
1. Em **Definições**, selecione **Regras de segurança de entrada** e, em seguida, selecione **Adicionar**.
   
1. No diálogo da regra de **segurança adicionar,** digite ou selecione o seguinte:
   
   - **Fonte**: Selecione **etiqueta de serviço**.  
   - **Etiqueta de serviço de origem**: Selecione **Internet**. 
   - **Gamas de porta de destino**: Tipo *80*.
   - **Protocolo**: Selecione **TCP**. 
   - **Ação**: **Selecione Permitir**.  
   - **Prioridade**: Tipo *100*. 
   - **Nome**: Type *MyHTTPRule*. 
   - **Descrição**: Tipo *Permitir HTTP*. 
   
1. Selecione **Adicionar**. 
   
   ![Criar uma regra NSG](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta secção, inspecione a piscina traseira do equilibrador de carga e configure uma sonda de saúde de equilíbrio de carga e regras de tráfego.

### <a name="view-the-back-end-address-pool"></a>Veja a piscina de endereços de back-end

Para distribuir o tráfego para os VMs, o equilibrador de carga utiliza um conjunto de endereços de back-end, que contém os endereços IP das interfaces de rede virtual (NICs) que estão ligados ao equilibrador de carga. 

Criou o seu pool de back-end de balanceadores de carga e adicionou-lhe VMs quando criou os VMs. Também pode criar piscinas de back-end e adicionar ou remover VMs da página de **piscinas backend** do equilibrador de carga. 

1. **Selecione todos os recursos** no menu esquerdo e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições,** selecione **piscinas backend**.
   
1. Na página **backend pools,** expanda **myBackendPool** e certifique-se de que tanto **vM1** como **VM2** estão listados.

1. Selecione **MyBackendPool**. 
   
   Na página **MyBackendPool,** em **virtual MACHINE** e **IP ADDRESS,** pode remover ou adicionar VMs disponíveis à piscina.

Você pode criar novas piscinas de back-end selecionando **Adicionar** na página de **piscinas Backend.**

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir que o equilibrador de carga monitorize o estado vm, utilize uma sonda de saúde. A sonda de estado de funcionamento adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na respetiva resposta às verificações de estado de funcionamento. 

1. **Selecione todos os recursos** no menu esquerdo e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Sondas de estado de funcionamento** e, em seguida, selecione **Adicionar**.
   
1. Na página **Adicionar sonda de saúde,** escreva ou selecione os seguintes valores:
   
   - **Nome**: *Digite MyHealthProbe*.
   - **Protocolo**: Desça e selecione **HTTP**. 
   - **Porta**: Tipo *80*. 
   - **Caminho**: */* Aceite para o URI padrão. Pode substituir este valor por qualquer outro URI. 
   - **Intervalo**: Tipo *15*. Intervalo é o número de segundos entre as tentativas de sonda.
   - **Limiar pouco saudável**: Tipo *2*. Este valor é o número de falhas de sonda consecutivas que ocorrem antes de um VM ser considerado insalubre.
   
1. Selecione **OK**.
   
   ![Adicione uma sonda](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

As regras de balanceador de carga definem a forma como o tráfego é distribuído pelas VMs. A regra define a configuração IP frontal para o tráfego de entrada, o pool IP de back-end para receber o tráfego, e as portas de origem e destino necessários. 

A regra do equilíbrio de carga denominada **MyLoadBalancerRule** ouve a porta 80 na extremidade frontal **LoadBalancerFrontEnd**. A regra envia tráfego de rede para a piscina de endereços back-end **MyBackendPool**, também no porto 80. 

1. **Selecione todos os recursos** no menu esquerdo e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Regras de balanceamento de carga** e, em seguida, selecione **Adicionar**.
   
1. Na página de regra de equilíbrio de **carga Adicionar,** escreva ou selecione os seguintes valores:
   
   - **Nome**: Type *MyLoadBalancerRule*.
   - **Protocolo**: Selecione **TCP**.
   - **Porta**: Tipo *80*.
   - **Porta de backend**: Tipo *80*.
   - **Piscina de backend**: Selecione **MyBackendPool**.
   - **Sonda de saúde**: Selecione **MyHealthProbe**. 
   
1. Selecione **OK**.
   
   ![Adicionar uma regra de balanceador de carga](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>Criar uma regra de encaminhamento portuário nana

Criar uma regra de tradução de endereçode rede de suporte de carga (NAT) para encaminhar o tráfego de uma porta específica do endereço IP frontal para uma porta específica de um VM de back-end.

1. **Selecione todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** da lista de recursos.
   
1. Em **Definições,** selecione **as regras NAT de entrada**e, em seguida, selecione **Adicionar**. 
   
1. Na página de **regra nat de entrada,** escreva ou selecione os seguintes valores:
   
   - **Nome**: Type *MyNATRuleVM1*.
   - **Porta**: Tipo *4221*.
   - **Máquina virtual alvo**: Selecione **MyVM1** a partir da queda.
   - **Configuração IP**da rede : Selecione **ipconfig1** a partir da queda.
   - **Mapeamento portuário**: Selecione **Costume**.
   - **Porta-alvo**: Tipo *3389*.
   
1. Selecione **OK**.
   
1. Repita os passos para adicionar uma regra NAT de entrada chamada *MyNATRuleVM2*, utilizando a **Porta**: *4222* e **a máquina virtual Target**: **MyVM2**.

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Nesta secção, irá instalar serviços de informação de Internet (IIS) nos servidores de back-end e personalizar á página web padrão para mostrar o nome da máquina. Em seguida, utilizará o endereço IP público do equilibrador de carga para testar o equilibrador de carga. 

Cada VM de back-end serve uma versão diferente da página web padrão IIS, para que possa ver o balancer de carga distribuir pedidos entre os dois VMs.

### <a name="connect-to-the-vms-with-rdp"></a>Ligue-se aos VMs com RDP

Ligue-se a cada VM com Desktop Remoto (RDP). 

1. No portal, selecione **Todos os recursos** no menu esquerdo. A partir da lista de recursos, selecione cada VM no grupo de recursos **MyResourceGroupLB.**
   
1. Na página **'Overview',** selecione **Connect**, e, em seguida, selecione **download ficheiro RDP**. 
   
1. Abra o ficheiro RDP que descarregou e selecione **Connect**.
   
1. No ecrã de Segurança do Windows, selecione **Mais escolhas** e, em seguida, **use uma conta diferente**. 
   
   Introduza o nome de utilizador *azureuser* e a palavra-passe *Azure1234567*, e selecione **OK**.
   
1. Responda **Sim** a qualquer pedido de certificado. 
   
   O ambiente de trabalho VM abre numa nova janela. 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>Instale o IIS e substitua a página web do IIS predefinido 

Utilize o PowerShell para instalar o IIS e substitua a página web do IIS por defeito por uma página que apresenta o nome do VM.

1. No MyVM1 e no MyVM2, lança o **Windows PowerShell** a partir do menu **Iniciar.** 

2. Executar os seguintes comandos para instalar o IIS e substituir a página web do IIS predefinido:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. Feche as ligações RDP com MyVM1 e MyVM2 selecionando **Desligar**. Não desligue os VMs.

### <a name="test-load-balancing"></a>Equilíbrio de carga de teste

1. No portal, na página **'Overview'** para **MyLoadBalancer,** copie o endereço IP público sob **endereço IP público**. Passe por cima do endereço e selecione o ícone **Copiar** para copiá-lo. Neste exemplo, é **40.67.218.235**. 
   
1. Colhe ou escreva o endereço IP público do equilibrador de carga *(40.67.218.235*) na barra de endereços do seu navegador de internet. 
   
   A página padrão do servidor web IIS personalizada aparece no navegador. A mensagem **lê-se ou Hello World from MyVM1**, ou **Hello World from MyVM2**.
   
   ![Nova página padrão do IIS](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. Atualize o navegador para ver o equilibrador de carga distribuir tráfego através de VMs. Por vezes aparece a página **MyVM1,** e outras vezes aparece a página **MyVM2,** uma vez que o equilibrador de carga distribui os pedidos por cada VM de back-end.
   
   >[!NOTE]
   >Poderá ter de limpar o cache do seu navegador ou abrir uma nova janela de navegador entre tentativas.

## <a name="test-port-forwarding"></a>Testar o reencaminhamento de portas

Com o encaminhamento da porta, pode remotamente o ambiente de trabalho para um VM traseiro utilizando o endereço IP do equilibrante de carga e o valor da porta frontal definido na regra NAT. 

1. No portal, na página **'Overview'** para **MyLoadBalancer,** copie o seu endereço IP público. Passe por cima do endereço e selecione o ícone **Copiar** para copiá-lo. Neste exemplo, é **40.67.218.235**. 
   
1. Abra um pedido de comando e utilize o seguinte comando para criar uma sessão remota de ambiente de trabalho com o MyVM2, utilizando o endereço IP público do equilibrador de carga e a porta frontal que definiu na regra NAT da VM. 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. Abra o ficheiro RDP descarregado e selecione **Connect**.
   
1. No ecrã de Segurança do Windows, selecione **Mais escolhas** e, em seguida, **use uma conta diferente**. 
   
   Introduza o nome de utilizador *azureuser* e a palavra-passe *Azure1234567*, e selecione **OK**.
   
1. Responda **Sim** a qualquer pedido de certificado. 
   
   O ambiente de trabalho MyVM2 abre numa nova janela. 

A ligação RDP tem sucesso, porque a regra de entrada na NAT **MyNATRuleVM2** direciona o tráfego da porta frontal do equilibrista de carga 4222 para a porta 3389 do MyVM2 (a porta RDP).

## <a name="clean-up-resources"></a>Limpar recursos

Para eliminar o equilibrador de carga e todos os recursos relacionados quando já não precisar, abra o grupo de recursos **MyResourceGroupLB** e selecione Eliminar o **grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um equilibrador de carga pública Standard. Criou e configurou recursos de rede, servidores back-end, uma sonda de saúde e regras para o equilibrador de carga. Instalou o IIS nos VMs traseiros e utilizou o endereço IP público do equilibrador de carga para testar o equilíbrio de carga. Instalou e testou a encaminhamento da porta de uma porta especificada no equilibrante de carga para uma porta num VM de back-end. 

Para saber mais sobre o Azure Load Balancer, continue a carregar tutoriais de equilibradores.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
