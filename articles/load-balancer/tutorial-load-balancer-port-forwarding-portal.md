---
title: 'Tutorial: Encaminhamento de porta de configuração - Portal Azure'
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82207892"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>Tutorial: Encaminhamento de porta de configuração em Azure Load Balancer usando o portal

O reencaminhamento de porta permite ligar-se a máquinas virtuais (VMs) numa rede virtual Azure utilizando um endereço IP público e número de porta do Azure Load Balancer. 

Neste tutorial, você configura o reencaminhamento de porta em um Balançador de Carga Azure. Saiba como:

> [!div class="checklist"]
> * Crie um balanceador de carga standard público para equilibrar o tráfego de rede em VMs. 
> * Crie uma rede virtual e VMs com uma regra de grupo de segurança de rede (NSG). 
> * Adicione os VMs ao pool de endereços back-end do balançador de carga.
> * Crie uma sonda de saúde e regras de tráfego do balanceador de carga.
> * Crie regras de encaminhamento de porta NAT de entrada de carregadores.
> * Instale e configugue o IIS nos VMs para ver o equilíbrio de carga e o encaminhamento da porta em ação.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

Para todos os passos neste tutorial, inscreva-se no portal Azure em [https://portal.azure.com](https://portal.azure.com) .

## <a name="create-a-standard-load-balancer"></a>Criar um equilibrador de carga standard

Em primeiro lugar, crie um balanceador de carga standard público que possa equilibrar a carga de tráfego sobre VMs. Um equilibrador de carga Standard suporta apenas um endereço IP público standard. Quando cria um balanceador de carga Standard, também cria um novo endereço IP público Standard, que é configurado como a extremidade frontal do balançador de carga e nomeado **LoadBalancerFrontEnd** por padrão. 

1. No lado superior esquerdo do ecrã, clique em **Criar um equilibrador**de carga de  >  **rede de**recursos  >  **Load Balancer**.
2. No separador **Básico da** página **'Criar balanceador** de carga', introduzir ou selecionar as seguintes informações, aceitar as predefinições para as definições restantes e, em seguida, selecionar **Rever + criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | **Selecione Criar novo** e digitar *MyResourceGroupLB* na caixa de texto.|
    | Nome                   | *myLoadBalancer*                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Tipo          | Selecione **Público**.                                        |
    | SKU           | Selecione **Standard**.                          |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público              | Digite *o myPublicIP* na caixa de texto.   |
    |Zona de disponibilidade| **Selecione Zona redundante**.    |
     
    >[!NOTE]
     >Certifique-se de criar o seu Balancer de Carga e todos os recursos para ele num local que suporte Zonas de Disponibilidade. Para mais informações, consulte [Regiões que suportam Zonas de Disponibilidade.](../availability-zones/az-region.md) 

3. No **separador 'Rever +' criar,** clique em **Criar.**  
  
## <a name="create-and-configure-back-end-servers"></a>Criar e configurar servidores de back-end

Crie uma rede virtual com duas máquinas virtuais e adicione os VMs à piscina traseira do seu equilibrador de carga. 

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Nesta secção, deverá substituir os seguintes parâmetros nos passos pelas informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB (Selecione o grupo de recursos existente) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa Ocidental      |
| **\<IPv4-address-space>**   | 10.3.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>Crie VMs e adicione-os à piscina traseira do balançador de carga

1. No lado superior esquerdo do portal, selecione **Criar um**  >  **datacenter Compute**  >  **Windows Server 2016**. 
   
1. Na **Criação de uma máquina virtual,** escreva ou selecione os seguintes valores no **separador Básicos:**
   - **Assinatura**  >  **Grupo de Recursos**: Desça e selecione **MyResourceGroupLB**.
   - **Nome da máquina virtual**: Tipo *MyVM1*.
   - **Região**: Selecione **Europa Ocidental.** 
   - **Nome de utilizador**: Escreva *azureuser*.
   - **Palavra-passe**: *Digite Azure1234567*. 
     Retipe a palavra-passe no campo **de palavra-passe Confirmar.**
   
1. Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos**e, em seguida, **seguinte: Networking**. 
   
   Certifique-se de que estão selecionados:
   - **Rede virtual**: **MyVNet**
   - **Sub-rede**: **MyBackendSubnet**
   
1. Em **IP público**, selecione Criar **novo,** selecione **Standard** na página **de endereço IP público** e, em seguida, selecione **OK**. 
   
1. No **âmbito do Grupo de Segurança da Rede,** selecione **Advanced** para criar um novo grupo de segurança de rede (NSG), um tipo de firewall. 
   1. No campo de grupo de segurança de **rede Configure,** selecione **Criar novo**. 
   1. Digite *MyNetworkSecurityGroup*e selecione **OK**. 
   
   >[!NOTE]
   >Observe que, por padrão, o NSG já tem uma regra de entrada para abrir a porta 3389, a porta de ambiente de trabalho remoto (RDP).
   
1. Adicione o VM a uma piscina de fundo de compensação de carga que cria:
   
   1. Em **EQUILÍBRIO DE CARGA**Coloque esta máquina virtual  >  **atrás de uma solução de equilíbrio de carga existente?** **Yes** 
   1. Para **opções de equilíbrio de carga,** desça e selecione **o balançador de carga Azure**. 
   1. Para **selecionar um equilibrador de carga,** desça e selecione **MyLoadBalancer**. 
   1. Em **Selecione uma piscina de backend**, selecione **Criar novo,** em seguida, *digite MyBackendPool*e selecione **Criar**. 
   
   ![Criar uma rede virtual](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. Selecione o separador **Gestão** ou selecione **'Gestão**  >  **Seguinte'.** Em **Monitorização**, desemisse os **diagnósticos** **da**Bota .
   
1. Selecione **Rever + criar**.
   
1. Reveja as definições e, quando a validação for bem sucedida, **selecione Criar**. 

1. Siga os passos para criar um segundo VM chamado *MyVM2,* com todas as outras definições iguais às myVM1. 
   
   Para **o Grupo de Segurança da Rede**, depois de selecionar **Advanced**, desça e selecione o **MyNetworkSecurityGroup** que já criou. 
   
   Em **Selecione uma piscina de backend,** certifique-se de que **o MyBackendPool** está selecionado. 

### <a name="create-an-nsg-rule-for-the-vms"></a>Criar uma regra NSG para os VMs

Criar uma regra de grupo de segurança de rede (NSG) para os VMs permitir ligações de internet (HTTP) de entrada.

>[!NOTE]
>Por predefinição, o NSG já tem uma regra que abre a porta 3389, a porta de ambiente de trabalho remoto (PDR).

1. Selecione **Todos os recursos** no menu da esquerda. Na lista de recursos, selecione **MyNetworkSecurityGroup** no grupo de recursos **MyResourceGroupLB.**
   
1. Em **Definições**, selecione **Regras de segurança de entrada** e, em seguida, selecione **Adicionar**.
   
1. No diálogo **de regra de segurança de entrada Adicionar,** digite ou selecione o seguinte:
   
   - **Fonte**: Selecione **Tag de Serviço**.  
   - **Etiqueta de serviço de origem**: Selecione **Internet**. 
   - **Gamas porta de destino**: Tipo *80*.
   - **Protocolo**: Selecione **TCP**. 
   - **Ação**: Selecione **Permitir**.  
   - **Prioridade**: Tipo *100*. 
   - **Nome**: Escreva *MyHTTPrule*. 
   - **Descrição**: Tipo *Permitir HTTP*. 
   
1. Selecione **Adicionar**. 
   
   ![Criar uma regra NSG](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta secção, inspecione a piscina traseira do balançador de carga e configuure uma sonda de saúde e regras de tráfego do balanceador de carga.

### <a name="view-the-back-end-address-pool"></a>Ver a piscina de endereços back-end

Para distribuir o tráfego para os VMs, o equilibrador de carga utiliza um conjunto de endereços back-end, que contém os endereços IP das interfaces de rede virtuais (NICs) que estão ligados ao equilibrador de carga. 

Criou o seu pool back-end do balanceador de carga e adicionou-lhe VMs quando criou os VMs. Também pode criar piscinas de back-end e adicionar ou remover VMs da página de **piscinas backend** do balançador de carga. 

1. Selecione **todos os recursos** no menu esquerdo e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **piscinas backend**.
   
1. Na página de **pools backend,** expanda o **MyBackendPool** e certifique-se de que tanto **o VM1** como **o VM2** estão listados.

1. Selecione **MyBackendPool**. 
   
   Na página **MyBackendPool,** em **VIRTUAL MACHINE** e **IP ADDRESS,** pode remover ou adicionar VMs disponíveis à piscina.

Pode criar novas piscinas back-end selecionando **Adicionar** na página **de piscinas backend.**

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Para permitir que o balanceador de carga monitorize o estado de VM, utilize uma sonda de saúde. A sonda de estado de funcionamento adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na respetiva resposta às verificações de estado de funcionamento. 

1. Selecione **todos os recursos** no menu esquerdo e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Sondas de estado de funcionamento** e, em seguida, selecione **Adicionar**.
   
1. Na página da **sonda de saúde Adicionar,** digite ou selecione os seguintes valores:
   
   - **Nome**: *Digite MyHealthProbe*.
   - **Protocolo**: Desça e selecione **HTTP**. 
   - **Porta**: Tipo *80*. 
   - **Caminho**: Aceite */* para o URI predefinido. Pode substituir este valor por qualquer outro URI. 
   - **Intervalo**: Tipo *15*. O intervalo é o número de segundos entre as tentativas da sonda.
   - **Limiar pouco saudável**: Tipo *2*. Este valor é o número de falhas consecutivas da sonda que ocorrem antes de um VM ser considerado insalubre.
   
1. Selecione **OK**.
   
   ![Adicione uma sonda](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

As regras de balanceador de carga definem a forma como o tráfego é distribuído pelas VMs. A regra define a configuração IP frontal para o tráfego de entrada, o pool IP back-end para receber o tráfego, e as portas de origem e destino necessárias. 

A regra do balanceador de carga chamada **MyLoadBalancerRule** ouve a porta 80 na frente-end **LoadBalancerFrontEnd**. A regra envia tráfego de rede para o pool de endereços back-end **MyBackendPool**, também na porta 80. 

1. Selecione **todos os recursos** no menu esquerdo e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **Regras de balanceamento de carga** e, em seguida, selecione **Adicionar**.
   
1. Na página de **regra de equilíbrio de carga adicionar,** digite ou selecione os seguintes valores:
   
   - **Nome**: *Digite MyLoadBalancerule*.
   - **Protocolo**: Selecione **TCP**.
   - **Porta**: Tipo *80*.
   - **Porta de backend**: Tipo *80*.
   - **Backend pool**: Selecione **MyBackendPool**.
   - **Sonda de saúde**: Selecione **MyHealthProbe**. 
   
1. Selecione **OK**.
   
   ![Adicionar uma regra de balanceador de carga](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>Criar uma regra de encaminhamento de porta NAT de entrada

Criar uma regra de tradução de endereço de rede de entrada de balançador de carga (NAT) para encaminhar o tráfego de uma porta específica do endereço IP frontal para uma porta específica de um VM de back-end.

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, selecione **MyLoadBalancer** na lista de recursos.
   
1. Em **Definições**, selecione **as regras DE ENTRADA**e, em seguida, selecione **Adicionar**. 
   
1. Na página de **regra NAT de entrada** adicionar, digite ou selecione os seguintes valores:
   
   - **Nome**: *Digite MyNATRulevM1*.
   - **Porta**: Tipo *4221*.
   - **Máquina virtual de destino**: Selecione **MyVM1** a partir do drop-down.
   - **Configuração IP da rede**: Selecione **ipconfig1** a partir do drop-down.
   - **Mapeamento portuário**: Selecione **Custom**.
   - **Porta-alvo**: Tipo *3389*.
   
1. Selecione **OK**.
   
1. Repita os passos para adicionar uma regra NAT de entrada chamada *MyNATRuleVM2,* utilizando **a porta**: *4222* e **máquina virtual target**: **MyVM2**.

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Nesta secção, instalará serviços de informação da Internet (IIS) nos servidores de back-end e personalizará a página web padrão para mostrar o nome da máquina. Em seguida, utilizará o endereço IP público do balançador de carga para testar o equilibrador de carga. 

Cada VM de back-end serve uma versão diferente da página web padrão do IIS, para que possa ver os pedidos de distribuição do balanceador de carga entre os dois VMs.

### <a name="connect-to-the-vms-with-rdp"></a>Ligue-se aos VMs com RDP

Ligue a cada VM com Desktop Remoto (RDP). 

1. No portal, selecione **Todos os recursos** no menu esquerdo. Na lista de recursos, selecione cada VM no grupo de recursos **MyResourceGroupLB.**
   
1. Na página **'Vista Geral',** selecione **Connect**e, em seguida, selecione **Download RDP file**. 
   
1. Abra o ficheiro RDP que descarregou e selecione **Connect**.
   
1. No ecrã de Segurança do Windows, selecione **Mais escolhas** e, em seguida, **Utilize uma conta diferente**. 
   
   Introduza o nome de utilizador *azureuser* e a palavra-passe *Azure1234567*, e selecione **OK**.
   
1. Responda **Sim** a qualquer pedido de certificado. 
   
   O ambiente de trabalho em VM abre-se numa nova janela. 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>Instale o IIS e substitua a página web do IIS predefinido 

Utilize o PowerShell para instalar o IIS e substitua a página web do IIS por padrão por uma página que apresente o nome do VM.

1. No MyVM1 e no MyVM2, lance **o Windows PowerShell** a partir do menu **Iniciar.** 

2. Executar os seguintes comandos para instalar o IIS e substituir a página web do IIS predefinido:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. Feche as ligações RDP com MyVM1 e MyVM2 selecionando **Disconnect**. Não desligue os VMs.

### <a name="test-load-balancing"></a>Equilíbrio da carga de ensaio

1. No portal, na página **'Visão Geral'** do **MyLoadBalancer,** copie o endereço IP público no **endereço IP público**. Passe por cima do endereço e selecione o ícone **Copy** para copiá-lo. Neste exemplo, é **40.67.218.235**. 
   
1. Cole ou digite o endereço IP público do balanceador de carga *(40.67.218.235*) na barra de endereço do seu navegador de Internet. 
   
   A página padrão personalizada do servidor web IIS aparece no navegador. A mensagem diz **hello world from MyVM1,** ou **Hello World from MyVM2**.
   
   ![Nova página padrão do IIS](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. Refresque o navegador para ver o balanceador de carga distribuir tráfego através de VMs. Por vezes aparece a página **MyVM1** e, outras vezes, aparece a página **MyVM2,** uma vez que o equilibrador de carga distribui os pedidos a cada VM de back-end.
   
   >[!NOTE]
   >Poderá necessitar de limpar a cache do seu navegador ou abrir uma nova janela do navegador entre tentativas.

## <a name="test-port-forwarding"></a>Testar o reencaminhamento de portas

Com o encaminhamento da porta, pode fazer um ambiente de trabalho remoto para um VM de back-end utilizando o endereço IP do equilibrador de carga e o valor frontal da porta definido na regra NAT. 

1. No portal, na página **'Visão Geral'** do **MyLoadBalancer,** copie o seu endereço IP público. Passe por cima do endereço e selecione o ícone **Copy** para copiá-lo. Neste exemplo, é **40.67.218.235**. 
   
1. Abra uma solicitação de comando e use o seguinte comando para criar uma sessão de desktop remota com o MyVM2, utilizando o endereço IP público do balançador de carga e a porta frontal definida na regra NAT do VM. 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. Abra o ficheiro RDP descarregado e selecione **Connect**.
   
1. No ecrã de Segurança do Windows, selecione **Mais escolhas** e, em seguida, **Utilize uma conta diferente**. 
   
   Introduza o nome de utilizador *azureuser* e a palavra-passe *Azure1234567*, e selecione **OK**.
   
1. Responda **Sim** a qualquer pedido de certificado. 
   
   O ambiente de trabalho MyVM2 abre-se numa nova janela. 

A ligação RDP é bem sucedida, uma vez que a regra NAT de entrada **MyNATRuleVM2** direciona o tráfego da porta frontal 4222 do balançador de carga para a porta 3389 do MyVM2 (a porta RDP).

## <a name="clean-up-resources"></a>Limpar os recursos

Para eliminar o equilibrador de carga e todos os recursos relacionados quando já não precisar deles, abra o grupo de recursos **MyResourceGroupLB** e selecione **Delete resource group**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um balanceador de carga pública Standard. Criou e configura recursos de rede, servidores de back-end, uma sonda de saúde e regras para o equilibrador de carga. Instalou iIS nos VMs de back-end e usou o endereço IP público do balançador de carga para testar o equilibrador de carga. Instalou e testou o encaminhamento da porta de uma porta especificada no equilibrador de carga para uma porta num VM de back-end. 

Para saber mais sobre o Azure Load Balancer, continue a ter mais tutoriais de balanço de carga.

> [!div class="nextstepaction"]
> [Tutoriais do Balanceador de Carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
