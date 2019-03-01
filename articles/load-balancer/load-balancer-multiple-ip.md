---
title: Balanceamento de carga em várias configurações de IP - portal do Azure
titlesuffix: Azure Load Balancer
description: Balanceamento de carga entre configurações de IP primárias e secundárias.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0fd865a6890721d3889fc5cef39d9888fe1fb689
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57191820"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Balanceamento de carga em várias configurações de IP utilizando o portal do Azure

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)


Neste artigo, vamos mostrar-lhe como utilizar o Balanceador de carga do Azure com vários endereços IP num controlador de interface de rede secundárias (NIC). O diagrama seguinte ilustra o nosso cenário:

![Cenário do Balanceador de carga](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

Em nosso cenário, estamos a utilizar a seguinte configuração:

- Duas máquinas virtuais (VMs) que estejam a executar o Windows.
- Cada VM tem um site primário e um NIC secundário.
- Cada NIC secundário tem duas configurações de IP.
- Cada VM aloja dois Web sites: contoso.com e fabrikam.com.
- Cada Web site está vinculado a uma configuração de IP na NIC secundário.
- O Azure Load Balancer é usado para expor dois front-end endereços IP, um para cada Web site. Os endereços front-end são utilizados para distribuir o tráfego para a respetiva configuração de IP para cada Web site.
- O mesmo número de porta é utilizado para os endereços IP Front-end e back-end de conjunto de endereços IP.

## <a name="prerequisites"></a>Pré-requisitos

Nosso exemplo de cenário pressupõe que tem um grupo de recursos chamado **contosofabrikam** que está configurada da seguinte forma:

- O grupo de recursos inclui uma rede virtual denominada **myVNet**.
- O **myVNet** rede inclui duas VMs com o nome **VM1** e **VM2**.
- A VM1 e VM2 estão no mesmo conjunto nomeada de disponibilidade **myAvailset**. 
- A VM1 e VM2 tem uma NIC primário com o nome **VM1NIC1** e **VM2NIC1**, respectivamente. 
- A VM1 e VM2 tem uma NIC secundário com o nome **VM1NIC2** e **VM2NIC2**, respectivamente.

Para obter mais informações sobre como criar VMs com várias NICs, consulte [criar uma VM com vários NICs com o PowerShell](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Executar em várias configurações de IP de balanceamento de carga

Conclua os seguintes passos para alcançar o cenário descrito neste artigo.

### <a name="step-1-configure-the-secondary-nics"></a>Passo 1: Configurar os NICs secundários

Para cada VM na sua rede virtual, adicione a configuração de IP para o NIC secundário:  

1. Navegue para o portal do Azure: http://portal.azure.com. Inicie sessão com a sua conta do Azure.

2. No canto superior esquerdo do ecrã, selecione o **grupo de recursos** ícone. Em seguida, selecione o grupo de recursos onde estão localizadas as VMs (por exemplo, **contosofabrikam**). O **grupos de recursos** painel apresenta todos os recursos e NICs para as VMs.

3. Para o NIC secundário de cada VM, adicione a configuração de IP:

    1. Selecione o NIC secundário que pretende configurar.
    
    2. Selecione **configurações de IP**. No painel seguinte, junto à parte superior, selecione **adicionar**.

    3. Sob **configurações de IP adicionar**, adicionar uma segunda configuração de IP para o NIC: 

        1. Introduza um nome para a configuração de IP secundário. (Por exemplo, para a VM1 e VM2, o nome da configuração de IP **VM1NIC2 ipconfig2** e **VM2NIC2 ipconfig2**, respectivamente.)

        2. Para o **endereço IP privado**, **alocação** definição, selecione **estático**.

        3. Selecione **OK**.

Após a segunda configuração de IP para o NIC secundário é concluído, será apresentado sob o **configurações de IP** definições para a NIC de determinado.

### <a name="step-2-create-the-load-balancer"></a>Passo 2: Criar o balanceador de carga

Crie o seu Balanceador de carga para a configuração:

1. Navegue para o portal do Azure: http://portal.azure.com. Inicie sessão com a sua conta do Azure.

2. No canto superior esquerdo do ecrã, selecione **criar um recurso** > **rede** > **Balanceador de carga**. Em seguida, selecione **criar**.

3. Sob **criar Balanceador de carga**, escreva um nome para o Balanceador de carga. Neste cenário, estamos a utilizar o nome **mylb**.

4. Sob **endereço IP público**, criar um novo IP público denominado **PublicIP1**.

5. Sob **grupo de recursos**, selecione o grupo de recursos existente para as suas VMs (por exemplo, **contosofabrikam**). Selecione a localização para implementar o seu Balanceador de carga e, em seguida, selecione **OK**.

O Balanceador de carga começa a implementar. Implementação pode demorar alguns minutos a concluir com êxito. Após a implementação estiver concluída, o Balanceador de carga é apresentado como um recurso no seu grupo de recursos.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Passo 3: Configure o conjunto IP Front-end

Para cada Web site (contoso.com e fabrikam.com), configure o conjunto IP Front-end num Balanceador de carga:

1. No portal, selecione **mais serviços**. Na caixa de filtro, escreva **endereço IP público** e, em seguida, selecione **endereços IP públicos**. No painel seguinte, junto à parte superior, selecione **adicionar**.

2. Configurar dois endereços IP públicos (**PublicIP1** e **PublicIP2**) para ambos os Web sites (contoso.com e fabrikam.com):

    1. Escreva um nome para o seu endereço IP Front-end.

    2. Para **grupo de recursos**, selecione o grupo de recursos existente para as suas VMs (por exemplo, **contosofabrikam**).

    3. Para **localização**, selecione a mesma localização que as VMs.

    4. Selecione **OK**.

    Depois dos endereços IP públicos são criados, estes são apresentados sob a **IP público** endereços.

3. <a name="step3-3"></a>No portal, selecione **mais serviços**. Na caixa de filtro, escreva **Balanceador de carga** e, em seguida, selecione **Balanceador de carga**. 

4. Selecione o Balanceador de carga (**mylb**) que pretende adicionar o pool de IP Front-end.

5. Sob **configurações**, selecione **configuração de IP de front-end**. No painel seguinte, junto à parte superior, selecione **adicionar**.

6. Escreva um nome para o seu endereço IP Front-end (por exemplo, **contosofe** ou **fabrikamfe**).

7. <a name="step3-7"></a>Selecione **endereço IP**. Sob **endereço IP público escolher**, selecione os endereços IP para o front-end (**PublicIP1** ou **PublicIP2**).

8. Criar o segundo endereço IP de front-end, repetindo <a href="#step3-3">passo 3</a> através de <a href="#step3-7">passo 7</a> nesta secção.

Depois do conjunto de front-end está configurado, os endereços IP serão apresentados em seu Balanceador de carga **configuração do IP de front-end** definições. 
    
### <a name="step-4-configure-the-back-end-pool"></a>Passo 4: Configurar o conjunto de back-end

Para cada Web site (contoso.com e fabrikam.com), configure o conjunto de endereços de back-end num Balanceador de carga:
        
1. No portal, selecione **mais serviços**. Na caixa de filtro, escreva **Balanceador de carga** e, em seguida, selecione **Balanceador de carga**.

2. Selecione o Balanceador de carga (**mylb**) que pretende adicionar o conjunto de back-end para.

3. Sob **configurações**, selecione **conjuntos back-end**. Escreva um nome para o seu conjunto de back-end (por exemplo, **contosopool** ou **fabrikampool**). No painel seguinte, junto à parte superior, selecione **adicionar**. 

4. Para **associado à**, selecione **conjunto de disponibilidade**.

5. Para **conjunto de disponibilidade**, selecione **myAvailset**.

6. Adicione as configurações de IP de rede de destino para ambas as VMs: 

    ![Configurar conjuntos de back-end de Balanceador de carga](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. Para **máquina virtual de destino**, selecione a VM que pretende adicionar ao agrupamento de back-end (por exemplo, **VM1** ou **VM2**).

    2. Para **configuração do IP de rede**, selecione a configuração de IP da NIC secundário para a VM que selecionou no passo anterior (por exemplo, **VM1NIC2 ipconfig2** ou **VM2NIC2 ipconfig2** ).

7. Selecione **OK**.

Depois do conjunto de back-end estiver configurado, os endereços serão apresentados em seu Balanceador de carga **conjunto back-end** definições.

### <a name="step-5-configure-the-health-probe"></a>Passo 5: Configurar a sonda de estado de funcionamento

Configure uma sonda de estado de funcionamento para o Balanceador de carga:

1. No portal, selecione **mais serviços**. Na caixa de filtro, escreva **Balanceador de carga** e, em seguida, selecione **Balanceador de carga**.

2. Selecione o Balanceador de carga (**mylb**) que pretende adicionar a sonda de estado de funcionamento para.

3. Sob **configurações**, selecione **sonda de estado de funcionamento**. No painel seguinte, junto à parte superior, selecione **adicionar**. 

4. Escreva um nome para a sonda de estado de funcionamento (por exemplo, **HTTP**). Selecione **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Passo 6: Configurar regras de balanceamento de carga

Para cada Web site (contoso.com e fabrikam.com), configure a regras de balanceamento de carga:
    
1. <a name="step6-1"></a>Sob **configurações**, selecione **regras de balanceamento de carga**. No painel seguinte, junto à parte superior, selecione **adicionar**. 

2. Para **Name**, escreva um nome para a regra de balanceamento de carga (por exemplo, **HTTPc** para contoso.com, ou **HTTPf** para fabrikam.com).

3. Para **endereço IP de front-end**, selecione o endereço IP Front-end que criou anteriormente (por exemplo, **contosofe** ou **fabrikamfe**).

4. Para **porta** e **porta de back-end**, mantenha o valor predefinido **80**.

5. Para **IP flutuante (devolução direta do servidor)**, selecione **desativado**.

6. <a name="step6-6"></a>Selecione **OK**.

7. Criar a segunda regra de Balanceador de carga, repetindo <a href="#step6-1">passo 1</a> através de <a href="#step6-6">passo 6</a> nesta secção.

Depois das regras são configuradas, são apresentados em seu Balanceador de carga **regras de balanceamento de carga** definições.

### <a name="step-7-configure-dns-records"></a>Passo 7: Configurar registos DNS

Como o último passo, configure os registos de recursos DNS para apontar para os respectivos endereços IP Front-end para o Balanceador de carga. Pode alojar os seus domínios no DNS do Azure. Para obter mais informações sobre como utilizar o DNS do Azure com o Balanceador de carga, veja [utilizando o Azure DNS com outros serviços do Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre como combinar serviços no Azure em de balanceamento de carga [com os serviços de balanceamento de carga no Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Saiba como pode utilizar diferentes tipos de registos para gerir e resolver problemas relacionados com o Balanceador de carga no [registos do Azure Monitor para o Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
