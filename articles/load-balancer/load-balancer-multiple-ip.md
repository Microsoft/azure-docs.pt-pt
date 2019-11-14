---
title: Balanceamento de carga em várias configurações de IP-portal do Azure
titleSuffix: Azure Load Balancer
description: Neste artigo, saiba mais sobre o balanceamento de carga nas configurações de IP primário e secundário usando o portal do Azure.
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
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077011"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Balanceamento de carga em várias configurações de IP usando o portal do Azure

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)


Neste artigo, vamos mostrar como usar Azure Load Balancer com vários endereços IP em um controlador de interface de rede (NIC) secundário. O diagrama a seguir ilustra nosso cenário:

![Cenário do Balanceador de carga](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

Em nosso cenário, estamos usando a seguinte configuração:

- Duas VMs (máquinas virtuais) que estão executando o Windows.
- Cada VM tem uma NIC primária e uma secundária.
- Cada NIC secundária tem duas configurações de IP.
- Cada VM hospeda dois sites: contoso.com e fabrikam.com.
- Cada site está associado a uma configuração de IP na NIC secundária.
- Azure Load Balancer é usado para expor dois endereços IP de front-end, um para cada site. Os endereços front-end são usados para distribuir o tráfego para a respectiva configuração de IP para cada site.
- O mesmo número de porta é usado para endereços IP de front-end e endereços IP do pool de back-end.

## <a name="prerequisites"></a>Pré-requisitos

Nosso exemplo de cenário pressupõe que você tenha um grupo de recursos chamado **contosofabrikam** configurado da seguinte maneira:

- O grupo de recursos inclui uma rede virtual chamada **myVNet**.
- A rede **myVNet** inclui duas VMs chamadas **VM1** e **VM2**.
- VM1 e VM2 estão no mesmo conjunto de disponibilidade chamado **Mydispset**. 
- VM1 e VM2 têm uma NIC primária chamada **VM1NIC1** e **VM2NIC1**, respectivamente. 
- VM1 e VM2 têm cada uma NIC secundária chamada **VM1NIC2** e **VM2NIC2**, respectivamente.

Para obter mais informações sobre como criar VMs com várias NICs, consulte [criar uma VM com várias NICs usando o PowerShell](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Executar balanceamento de carga em várias configurações de IP

Conclua as etapas a seguir para obter o cenário descrito neste artigo.

### <a name="step-1-configure-the-secondary-nics"></a>Etapa 1: configurar as NICs secundárias

Para cada VM em sua rede virtual, adicione a configuração de IP para a NIC secundária:  

1. Navegue até o portal do Azure: https://portal.azure.com. Entre com sua conta do Azure.

2. No canto superior esquerdo da tela, selecione o ícone **grupo de recursos** . Em seguida, selecione o grupo de recursos onde suas VMs estão localizadas (por exemplo, **contosofabrikam**). O painel **grupos de recursos** exibe todos os recursos e NICs para as VMs.

3. Para a NIC secundária de cada VM, adicione a configuração de IP:

    1. Selecione a NIC secundária que você deseja configurar.
    
    2. Selecione **configurações de IP**. No próximo painel, próximo à parte superior, selecione **Adicionar**.

    3. Em **Adicionar configurações de IP**, adicione uma segunda configuração de IP à NIC: 

        1. Insira um nome para a configuração de IP secundário. (Por exemplo, para VM1 e VM2, nomeie a configuração de IP **VM1NIC2-ipconfig2** e **VM2NIC2-ipconfig2**, respectivamente.)

        2. Para o **endereço IP privado**, configuração de **alocação** , selecione **estático**.

        3. Selecione **OK**.

Depois que a segunda configuração de IP para a NIC secundária for concluída, ela será exibida nas configurações de **IP** para a NIC fornecida.

### <a name="step-2-create-the-load-balancer"></a>Passo 2: Criar o balanceador de carga

Crie o balanceador de carga para a configuração:

1. Navegue até o portal do Azure: https://portal.azure.com. Entre com sua conta do Azure.

2. No canto superior esquerdo da tela, selecione **criar um recurso** > **rede** > **Load Balancer**. Em seguida, selecione **criar**.

3. Em **criar balanceador de carga**, digite um nome para o balanceador de carga. Nesse cenário, estamos usando o nome **mylb**.

4. Em **endereço IP público**, crie um novo IP público chamado **PublicIP1**.

5. Em **grupo de recursos**, selecione o grupo de recursos existente para suas VMs (por exemplo, **contosofabrikam**). Selecione o local para o qual implantar o balanceador de carga e, em seguida, selecione **OK**.

O balanceador de carga começa a ser implantado. A implantação pode levar alguns minutos para ser concluída com êxito. Após a conclusão da implantação, o balanceador de carga é exibido como um recurso em seu grupo de recursos.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Etapa 3: configurar o pool de IPS de front-end

Para cada site (contoso.com e fabrikam.com), configure o pool de IPS de front-end em seu balanceador de carga:

1. No portal, selecione **mais serviços**. Na caixa filtro, digite **endereço IP público** e, em seguida, selecione **endereços IP públicos**. No próximo painel, próximo à parte superior, selecione **Adicionar**.

2. Configure dois endereços IP públicos (**PublicIP1** e **PublicIP2**) para ambos os sites (contoso.com e fabrikam.com):

   1. Digite um nome para o endereço IP de front-end.

   2. Para **grupo de recursos**, selecione o grupo de recursos existente para suas VMs (por exemplo, **contosofabrikam**).

   3. Para **local**, selecione o mesmo local que as VMs.

   4. Selecione **OK**.

      Depois que os endereços IP públicos são criados, eles são exibidos sob os endereços **IP públicos** .

3. <a name="step3-3"></a>No portal, selecione **mais serviços**. Na caixa filtro, digite **balanceador de carga** e, em seguida, selecione **Load Balancer**. 

4. Selecione o balanceador de carga (**mylb**) ao qual você deseja adicionar o pool de IPS de front-end.

5. Em **configurações**, selecione **configuração de IP de front-end**. No próximo painel, próximo à parte superior, selecione **Adicionar**.

6. Digite um nome para seu endereço IP de front-end (por exemplo, **contosofe** ou **fabrikamfe**).

7. <a name="step3-7"></a>Selecione **endereço IP**. Em **escolher endereço IP público**, selecione os endereços IP para o front-end (**PublicIP1** ou **PublicIP2**).

8. Crie o segundo endereço IP de front-end repetindo a <a href="#step3-3">etapa 3</a> até a <a href="#step3-7">etapa 7</a> nesta seção.

Depois que o pool de front-end estiver configurado, os endereços IP serão exibidos em seus parâmetros de **configuração de IP de frontend** do balanceador de carga. 
    
### <a name="step-4-configure-the-back-end-pool"></a>Etapa 4: configurar o pool de back-ends

Para cada site (contoso.com e fabrikam.com), configure o pool de endereços de back-end no balanceador de carga:
        
1. No portal, selecione **mais serviços**. Na caixa filtro, digite **balanceador de carga** e, em seguida, selecione **Load Balancer**.

2. Selecione o balanceador de carga (**mylb**) ao qual você deseja adicionar o pool de back-ends.

3. Em **configurações**, selecione **pools de back-end**. Digite um nome para o pool de back-end (por exemplo, **contosopool** ou **fabrikampool**). No próximo painel, próximo à parte superior, selecione **Adicionar**. 

4. Para **associado a**, selecione **conjunto de disponibilidade**.

5. Para **conjunto de disponibilidade**, selecione **mydispset**.

6. Adicione as configurações de IP de rede de destino para ambas as VMs: 

    ![Configurar pools de back-end para o balanceador de carga](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. Para **máquina virtual de destino**, selecione a VM que você deseja adicionar ao pool de back-ends (por exemplo, **VM1** ou **VM2**).

    2. Para **configuração de IP de rede**, selecione a configuração de IP da NIC secundária para a VM que você selecionou na etapa anterior (por exemplo, **VM1NIC2-ipconfig2** ou **VM2NIC2-ipconfig2**).

7. Selecione **OK**.

Após a configuração do pool de back-end, os endereços são exibidos nas configurações do **pool de back-end** do balanceador de carga.

### <a name="step-5-configure-the-health-probe"></a>Etapa 5: configurar a investigação de integridade

Configurar uma investigação de integridade para o balanceador de carga:

1. No portal, selecione **mais serviços**. Na caixa filtro, digite **balanceador de carga** e, em seguida, selecione **Load Balancer**.

2. Selecione o balanceador de carga (**mylb**) ao qual você deseja adicionar a investigação de integridade.

3. Em **configurações**, selecione **investigação de integridade**. No próximo painel, próximo à parte superior, selecione **Adicionar**. 

4. Digite um nome para a investigação de integridade (por exemplo, **http**). Selecione **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Etapa 6: configurar regras de balanceamento de carga

Para cada site (contoso.com e fabrikam.com), configure as regras de balanceamento de carga:
    
1. <a name="step6-1"></a>Em **configurações**, selecione **regras de balanceamento de carga**. No próximo painel, próximo à parte superior, selecione **Adicionar**. 

2. Para **nome**, digite um nome para a regra de balanceamento de carga (por exemplo, **HTTPc** para contoso.com ou **HTTPf** para fabrikam.com).

3. Para **endereço IP de frontend**, selecione o endereço IP de front-end que você criou anteriormente (por exemplo, **contosofe** ou **fabrikamfe**).

4. Para **porta** e **porta de back-end**, mantenha o valor padrão **80**.

5. Para **IP flutuante (retorno de servidor direto)** , selecione **desabilitado**.

6. <a name="step6-6"></a>Selecione **OK**.

7. Crie a segunda regra do balanceador de carga repetindo a <a href="#step6-1">etapa 1</a> até a <a href="#step6-6">etapa 6</a> nesta seção.

Depois que as regras são configuradas, elas são exibidas nas configurações de **regras de balanceamento** de carga do balanceador de carga.

### <a name="step-7-configure-dns-records"></a>Etapa 7: Configurar registros DNS

Como a última etapa, configure os registros de recursos de DNS para apontar para os respectivos endereços IP de front-end para o balanceador de carga. Você pode hospedar seus domínios no DNS do Azure. Para obter mais informações sobre como usar o DNS do Azure com o Load Balancer, consulte [usando o DNS do Azure com outros serviços do Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre como combinar serviços de balanceamento de carga no Azure [usando serviços de balanceamento de carga no Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Saiba como você pode usar diferentes tipos de logs para gerenciar e solucionar problemas do balanceador de carga em [logs de Azure monitor para Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
