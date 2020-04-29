---
title: Filtrar o tráfego de internet de entrada com Adn de Firewall Azure usando o portal
description: Neste tutorial, vai aprender a implementar e configurar a DNAT do Azure Firewall com o portal do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 03/02/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 7220e48c6103352108bdb89e107bb862ee194040
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78251494"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Tutorial: Filtrar tráfego de internet de entrada com Adn de Firewall Azure utilizando o portal Azure

Pode configurar a Tradução de Endereços de Endereço de Destino de Firewall Azure (DNAT) para traduzir e filtrar o tráfego de internet de entrada para as suas subredes. Quando configurar o ADN, a ação de recolha de regras NAT está definida para **dnat**. Cada regra na coleção de regras NAT pode então ser utilizada para traduzir o endereço IP e a porta de firewall públicos para um IP e porta privados. As regras DNAT adicionam implicitamente uma regra de rede correspondente para permitir o tráfego traduzido. Pode substituir esse comportamento, ao adicionar explicitamente uma coleção de regras de rede com regras de negar que correspondem ao tráfego traduzido. Para saber mais sobre a lógica de processamento de regras do Azure Firewall, veja [Lógica de processamento de regras do Azure Firewall](rule-processing.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implementar uma firewall
> * Criar uma rota predefinida
> * Configurar uma regra DNAT
> * Testar a firewall

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Neste tutorial, vai criar duas VNets em modo peering:

- **VNet-Hub** - a firewall está nesta VNet.
- **VN-Spoke** - o servidor de carga de trabalho está nesta VNet.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Na página inicial do portal Azure, selecione **grupos de Recursos**e, em seguida, selecione **Adicionar**.
3. Em **Nome do grupo de recursos**, escreva **RG-DNAT-Test**.
4. Em **Subscrição**, selecione a sua subscrição.
5. Em **Localização do grupo de recursos**, selecione uma localização. Todos os recursos subsequentes que criar têm de estar na mesma localização.
6. Selecione **Criar**.

## <a name="set-up-the-network-environment"></a>Configurar o ambiente de rede

Em primeiro lugar, crie as VNets e, em seguida, configure o peering entre elas.

### <a name="create-the-hub-vnet"></a>Criar a VNet Hub

1. A partir da página inicial do portal Azure, selecione **Todos os serviços.**
2. Em **Rede,** selecione **redes Virtuais.**
3. Selecione **Adicionar**.
4. Em **Nome**, escreva **VN-Hub**.
5. Em **Espaço de endereços**, escreva **10.0.0.0/16**.
6. Em **Subscrição**, selecione a sua subscrição.
7. Em **Grupo de recursos**, selecione **Utilizar existente** e, em seguida, selecione **RG-DNAT-Test**.
8. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
9. Em **Sub-rede**, em **Nome**, escreva **AzureFirewallSubnet**.

     A firewall estará nesta sub-rede, e o nome da sub-rede **tem** de ser AzureFirewallSubnet.
     > [!NOTE]
     > O tamanho da sub-rede AzureFirewallSubnet é de /26. Para obter mais informações sobre o tamanho da subrede, consulte [O FaQ da Firewall Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

10. Para **o intervalo de endereços**, tipo **10.0.1.0/26**.
11. Utilize as outras definições predefinidas e, em seguida, selecione **Criar**.

### <a name="create-a-spoke-vnet"></a>Criar uma VNet spoke

1. A partir da página inicial do portal Azure, selecione **Todos os serviços.**
2. Em **Rede,** selecione **redes Virtuais.**
3. Selecione **Adicionar**.
4. Em **Nome**, escreva **VN-Spoke**.
5. Em **Espaço de endereços**, escreva **192.168.0.0/16**.
6. Em **Subscrição**, selecione a sua subscrição.
7. Em **Grupo de recursos**, selecione **Utilizar existente** e, em seguida, selecione **RG-DNAT-Test**.
8. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
9. Em **Sub-rede**, para **Nome**, escreva **SN-Workload**.

    O servidor irá estar nesta sub-rede.
10. Em **Intervalo de endereços**, escreva **192.168.1.0/24**.
11. Utilize as outras definições predefinidas e, em seguida, selecione **Criar**.

### <a name="peer-the-vnets"></a>Configurar o peering entre as VNets

Agora, configure o peering entre as duas VNets.

1. Selecione a rede virtual **VN-Hub.**
2. Em **Definições,** selecione **Peerings**.
3. Selecione **Adicionar**.
4. Tipo **Peer-HubSpoke** para o **nome do peering de VN-Hub para VN-Spoke**.
5. Selecione **VN-Spoke** para a rede virtual.
6. Tipo **Peer-SpokeHub** para **nome de peering de VN-Spoke para VN-Hub**.
7. Para permitir o tráfego reencaminhado da seção **VN-Spoke para VN-Hub** **Ativado**.
8. Selecione **OK**.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma máquina virtual de carga de trabalho e coloque-a na sub-rede **SN-Workload**.

1. A partir do menu do portal Azure, selecione **Criar um recurso**.
2. Em **Popular**, selecione **Windows Server 2016 Datacenter**.

**Noções básicas**

1. Em **Subscrição**, selecione a sua subscrição.
1. Em **Grupo de recursos**, selecione **Utilizar existente** e, em seguida, selecione **RG-DNAT-Test**.
1. Para o nome da **máquina virtual,** escreva **Srv-Workload**.
1. Para **região,** selecione o mesmo local que utilizou anteriormente.
1. Escreva um nome de utilizador e uma palavra-passe.
1. Selecione **Seguinte: Discos**.

**Discos**
1. Selecione **Seguinte: Networking**.

**Redes**

1. Para **a rede Virtual,** selecione **VN-Spoke**.
2. Em **Sub-rede**, selecione **SN-Workload**.
3. Para **o endereço IP público** selecione **Nenhum**.
4. Para **portas de entrada pública,** selecione **Nenhuma**. 
2. Deixe as outras definições predefinidas e selecione **Seguinte: Gestão**.

**Gestão**

1. Para **diagnósticos boot,** selecione **Off**.
1. Selecione **Rever + Criar**.

**Rever + Criar**

Reveja o resumo e, em seguida, selecione **Criar**. Este processo irá demorar alguns minutos a concluir.

Após a conclusão da implementação, tome nota do endereço IP privado para a máquina virtual. Será utilizado mais tarde quando configurar a firewall. Selecione o nome da máquina virtual e, em **Definições,** selecione **Networking** para encontrar o endereço IP privado.

## <a name="deploy-the-firewall"></a>Implementar a firewall

1. A partir da página inicial do portal, selecione **Criar um recurso**.
2. Selecione **Networking**, e depois **de Featured,** selecione **Ver tudo**.
3. Selecione **Firewall**, e, em seguida, selecione **Criar**. 
4. Na página **Criar uma firewall**, utilize a seguinte tabela para configurar a firewall:

   |Definição  |Valor  |
   |---------|---------|
   |Nome     |FW-DNAT-test|
   |Subscrição     |\<a sua subscrição\>|
   |Grupo de recursos     |**Utilizar existente**: RG-DNAT-Test |
   |Localização     |Selecionar a mesma localização que utilizou anteriormente|
   |Escolher uma rede virtual     |**Utilizar existente**: VN-Hub|
   |Endereço IP público     |**Criar novos**. O endereço IP público tem de ser do tipo SKU Standard.|

5. Selecione **Rever + criar**.
6. Reveja o resumo e, em seguida, selecione **Criar** para criar a firewall.

   Este processo irá demorar alguns minutos a implementar.
7. Após a implementação, vá ao grupo de recursos **RG-DNAT-Test** e selecione a firewall **de teste FW-DNAT.**
8. Anote o endereço IP privado. Vai utilizá-lo mais tarde quando criar a rota predefinida.

## <a name="create-a-default-route"></a>Criar uma rota predefinida

Na sub-rede **SN-Workload**, vai configurar a rota de saída predefinida para passar pela firewall.

1. A partir da página inicial do portal Azure, selecione **Todos os serviços.**
2. Em **Rede,** selecione **tabelas de rota.**
3. Selecione **Adicionar**.
4. Em **Nome**, escreva **RT-FWroute**.
5. Em **Subscrição**, selecione a sua subscrição.
6. Em **Grupo de recursos**, selecione **Utilizar existente** e selecione **RG-DNAT-Test**.
7. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
8. Selecione **Criar**.
9. Selecione **Refresh**e, em seguida, selecione a tabela de **rota RT-FW.**
10. Selecione **Subnets**e, em seguida, selecione **Associate**.
11. Selecione **rede Virtual**, e, em seguida, selecione **VN-Spoke**.
12. Em **Sub-rede**, selecione **SN-Workload**.
13. Selecione **OK**.
14. Selecione **Rotas,** e depois **selecione Adicionar**.
15. Em **Nome da rota**, escreva **FW-DG**.
16. Em **Prefixo de endereço**, escreva **0.0.0.0/0**.
17. Em **Tipo de salto seguinte**, selecione **Aplicação virtual**.

    O Azure Firewall é, de facto, um serviço gerido, mas a aplicação virtual funciona nesta situação.
18. Em **Endereço do próximo salto**, escreva o endereço IP privado para a firewall, que anotou anteriormente.
19. Selecione **OK**.

## <a name="configure-a-nat-rule"></a>Configurar uma regra NAT

1. Abra o **TESTE RG-DNAT**e selecione a firewall **de teste FW-DNAT.** 
2. Na página **de teste FW-DNAT,** em **Definições,** selecione **Regras**. 
3. Selecione Adicionar a recolha de **regras NAT**. 
4. Em **Nome**, escreva **RC-DNAT-01**. 
5. Em **Prioridade**, escreva **200**. 
6. Em **Regras**, em **Nome**, escreva **RL-01**.
7. Em **Protocolo**, selecione **TCP**.
8. Em **Endereços de Origem**, escreva *. 
9. Em **Endereços de Destino**, escreva o endereço IP público da firewall. 
10. Em **Portas de Destino**, escreva **3389**. 
11. Em **Endereço Traduzido**, escreva o endereço IP privado da máquina virtual Srv-Workload. 
12. Em **Porta traduzida**, escreva **3389**. 
13. Selecione **Adicionar**. 

## <a name="test-the-firewall"></a>Testar a firewall

1. Ligue uma área de trabalho remota ao endereço IP público da firewall. Deverá estar ligado à máquina virtual **Srv-Workload**.
2. Feche o ambiente de trabalho remoto.

## <a name="clean-up-resources"></a>Limpar recursos

Pode manter os recursos da firewall para o próximo tutorial. Se já não precisar dos mesmos, elimine o grupo de recursos **RG-DNAT-Test** para eliminar todos os recursos relacionados com a firewall.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implementar uma firewall
> * Criar uma rota predefinida
> * Configurar uma regra DNAT
> * Testar a firewall

Em seguida, pode monitorizar os registos do Azure Firewall.

> [!div class="nextstepaction"]
> [Tutorial: monitorizar registos do Azure Firewall](./tutorial-diagnostics.md)
