---
title: Equilíbrio de carga em várias configurações IP - Portal Azure
titleSuffix: Azure Load Balancer
description: Neste artigo, aprenda sobre o equilíbrio de carga através das configurações ip primárias e secundárias usando o portal Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 4bf74986462ecb2659505f8a1261b9b24aba3fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74077011"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Equilíbrio de carga em várias configurações IP utilizando o portal Azure

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)


Neste artigo, vamos mostrar-lhe como usar o Azure Load Balancer com vários endereços IP num controlador de interface de rede secundário (NIC). O diagrama seguinte ilustra o nosso cenário:

![Cenário do Balanceador de carga](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

No nosso cenário, estamos a usar a seguinte configuração:

- Duas máquinas virtuais (VMs) que estão a executar o Windows.
- Cada VM tem um NIC primário e secundário.
- Cada NIC secundário tem duas configurações IP.
- Cada VM acolhe dois websites: contoso.com e fabrikam.com.
- Cada website está ligado a uma configuração IP no NIC secundário.
- O Azure Load Balancer é usado para expor dois endereços IP front-end, um para cada website. The front-end addresses are used to distribute traffic to the respective IP configuration for each website.
- O mesmo número de porta é utilizado tanto para endereços IP front-end como para endereços IP do pool de back-end.

## <a name="prerequisites"></a>Pré-requisitos

O nosso exemplo de cenário pressupõe que você tem um grupo de recursos chamado **contosofabrikam** que está configurado da seguinte forma:

- O grupo de recursos inclui uma rede virtual chamada **myVNet**.
- A rede **myVNet** inclui dois VMs chamados **VM1** e **VM2**.
- VM1 e VM2 estão no mesmo conjunto de disponibilidade chamado **myAvailset**. 
- VM1 e VM2 têm cada um um NIC primário chamado **VM1NIC1** e **VM2NIC1,** respectivamente. 
- VM1 e VM2 têm cada um um NIC secundário chamado **VM1NIC2** e **VM2NIC2,** respectivamente.

Para obter mais informações sobre a criação de VMs com vários NICs, consulte [Criar um VM com vários NICs utilizando powerShell](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Execute o equilíbrio de carga em várias configurações IP

Complete os seguintes passos para alcançar o cenário descrito neste artigo.

### <a name="step-1-configure-the-secondary-nics"></a>Passo 1: Configurar os NICs secundários

Para cada VM na sua rede virtual, adicione a configuração IP para o NIC secundário:  

1. Navegue pelo portal Azure: https://portal.azure.com. Inscreva-se na sua conta Azure.

2. Na parte superior esquerda do ecrã, selecione o ícone **do Grupo de Recursos.** Em seguida, selecione o grupo de recursos onde estão localizados os seus VMs (por exemplo, **contosofabrikam).** O painel de **grupos de recursos** exibe todos os recursos e NICs para os VMs.

3. Para o NIC secundário de cada VM, adicione a configuração IP:

    1. Selecione o NIC secundário que pretende configurar.
    
    2. Selecione **configurações IP**. No painel seguinte, perto da parte superior, **selecione Adicionar**.

    3. Em **configurações de Add IP,** adicione uma segunda configuração IP ao NIC: 

        1. Introduza um nome para a configuração IP secundária. (Por exemplo, para VM1 e VM2, nomeie a configuração IP **VM1NIC2-ipconfig2** e **VM2NIC2-ipconfig2,** respectivamente.)

        2. Para o **endereço IP privado**, Definição de **atribuição,** selecione **Static**.

        3. Selecione **OK**.

Após a configuração ip segunda para o NIC secundário estar completa, é apresentado sob as configurações ip **configurações** para o NIC dado.

### <a name="step-2-create-the-load-balancer"></a>Passo 2: Criar o balanceador de carga

Crie o seu equilíbrio de carga para a configuração:

1. Navegue pelo portal Azure: https://portal.azure.com. Inscreva-se na sua conta Azure.

2. Na parte superior esquerda do ecrã, selecione **Criar um** > equilíbrio de**carga**de**rede** > de recursos . Em seguida, selecione **Criar**.

3. Em **Criar um equilíbrior**de carga, digite um nome para o seu equilibrador de carga. Neste cenário, estamos a usar o nome **mylb.**

4. Sob **o endereço IP público,** crie um novo IP público chamado **PublicIP1**.

5. No **Grupo de Recursos**, selecione o grupo de recursos existente para os seus VMs (por exemplo, **contosofabrikam).** Selecione a localização para implantar o seu equilibrador de carga e, em seguida, selecione **OK**.

O equilibrador de carga começa a ser implantado. A implantação pode levar alguns minutos para completar com sucesso. Após a implementação estar concluída, o equilibrador de carga é apresentado como um recurso no seu grupo de recursos.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Passo 3: Configure a piscina IP frontal

Para cada website (contoso.com e fabrikam.com), configure o conjunto IP frontal no seu equilibrador de carga:

1. No portal, selecione **Mais serviços.** Na caixa de filtro, digite o **endereço IP público** e, em seguida, selecione **endereços IP públicos**. No painel seguinte, perto da parte superior, **selecione Adicionar**.

2. Configure dois endereços IP públicos **(PublicIP1** e **PublicIP2)** para ambos os websites (contoso.com e fabrikam.com):

   1. Digite um nome para o seu endereço IP frontal.

   2. Para **o Grupo de Recursos,** selecione o grupo de recursos existente para os seus VMs (por exemplo, **contosofabrikam).**

   3. Para **localização,** selecione a mesma localização que os VMs.

   4. Selecione **OK**.

      Após a criação dos endereços IP públicos, estes são exibidos nos endereços **IP públicos.**

3. <a name="step3-3"></a>No portal, selecione **Mais serviços.** Na caixa de filtro, digite o **equilibrador** de carga e, em seguida, selecione **Load Balancer**. 

4. Selecione o equilibrador de carga **(mylb)** a que pretende adicionar a piscina IP frontal.

5. Em **Definições,** selecione **a configuração IP frontend**. No painel seguinte, perto da parte superior, **selecione Adicionar**.

6. Digite um nome para o seu endereço IP frontal (por exemplo, **contosofe** ou **fabrikamfe).**

7. <a name="step3-7"></a>Selecione **endereço IP**. Em **'Escolha o endereço IP Público',** selecione os endereços IP para o seu front-front -front **-( PublicIP1** ou **PublicIP2**).

8. Crie o segundo endereço IP frontal repetindo o <a href="#step3-3">passo 3</a> ao <a href="#step3-7">passo 7</a> nesta secção.

Depois de configurar a piscina frontal, os endereços IP são apresentados sob as definições de configuração ip do seu equilíbrio de carga **Frontend IP.** 
    
### <a name="step-4-configure-the-back-end-pool"></a>Passo 4: Configure a piscina traseira

Para cada website (contoso.com e fabrikam.com), configure o conjunto de endereços de back-end no seu equilibrador de carga:
        
1. No portal, selecione **Mais serviços.** Na caixa de filtro, digite o **equilibrador** de carga e, em seguida, selecione **Load Balancer**.

2. Selecione o equilibrador de carga **(mylb)** a que pretende adicionar a piscina traseira.

3. Em **definições,** selecione **Backend Pools**. Digite um nome para a sua piscina de back-end (por exemplo, **contosopool** ou **fabrikampool).** No painel seguinte, perto da parte superior, **selecione Adicionar**. 

4. Para **Associadoado a**, selecione **conjunto de disponibilidade**.

5. Para **o conjunto de disponibilidade,** selecione **myAvailset**.

6. Adicione as configurações IP da rede-alvo para ambos os VMs: 

    ![Configure piscinas traseiras para equilibrador de carga](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. Para **a máquina virtual Target,** selecione o VM que pretende adicionar à piscina traseira (por exemplo, **VM1** ou **VM2**).

    2. Para **a configuração IP da rede,** selecione a configuração IP do NIC secundário para o VM que selecionou no passo anterior (por exemplo, **VM1NIC2-ipconfig2** ou **VM2NIC2-ipconfig2**).

7. Selecione **OK**.

Depois de configurar a piscina traseira, os endereços são apresentados sob as **definições** do seu balanço de reserva de carga.

### <a name="step-5-configure-the-health-probe"></a>Passo 5: Configurar a sonda de saúde

Configure uma sonda de saúde para o seu equilibrador de carga:

1. No portal, selecione **Mais serviços.** Na caixa de filtro, digite o **equilibrador** de carga e, em seguida, selecione **Load Balancer**.

2. Selecione o equilibrador de carga **(mylb)** a que pretende adicionar a sonda de saúde.

3. Em **Definições,** selecione **Health probe**. No painel seguinte, perto da parte superior, **selecione Adicionar**. 

4. Digite um nome para a sonda de saúde (por exemplo, **HTTP**). Selecione **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Passo 6: Configure regras de equilíbrio de carga

Para cada website (contoso.com e fabrikam.com), configure as regras de equilíbrio de carga:
    
1. <a name="step6-1"></a>Em **Definições,** selecione regras de **equilíbrio de carga**. No painel seguinte, perto da parte superior, **selecione Adicionar**. 

2. Para **Nome**, digite um nome para a regra de equilíbrio de carga (por exemplo, **HTTPc** para contoso.com ou **HTTPf** para fabrikam.com).

3. Para **o endereço IP Frontend,** selecione o endereço IP frontal que criou anteriormente (por exemplo, **contosofe** ou **fabrikamfe).**

4. Para a porta **Port** e **Backend,** mantenha o valor predefinido **80**.

5. Para **o IP flutuante (devolução do servidor direto)** selecione **Desativado**.

6. <a name="step6-6"></a>Selecione **OK**.

7. Crie a segunda regra do equilíbrio de carga repetindo o <a href="#step6-1">passo 1</a> ao <a href="#step6-6">passo 6</a> nesta secção.

Depois de configuradas as regras, são apresentadas sob as definições de regras de equilíbrio de balanço de carga do seu equilibrador de **carga.**

### <a name="step-7-configure-dns-records"></a>Passo 7: Configurar registos de DNS

Como último passo, configure os seus registos de recursos DNS para apontar para os respetivos endereços IP frontais para o seu equilibrador de carga. Pode hospedar os seus domínios em Azure DNS. Para obter mais informações sobre a utilização de DNS Azure com Balancer de Carga, consulte [Utilizar o Azure DNS com outros serviços Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre como combinar serviços de equilíbrio de carga em Azure em [Serviços de equilíbrio de carga em Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Saiba como pode utilizar diferentes tipos de registos para gerir e resolver problemas em [registos do Monitor Azure para o Equilíbrio de Carga Seleções Azure](../load-balancer/load-balancer-monitor-log.md).
