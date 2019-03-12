---
title: Filtrar tráfego de entrada com DNAT do Azure Firewall com o portal do Azure
description: Neste tutorial, vai aprender a implementar e configurar a DNAT do Azure Firewall com o portal do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/28/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: df57faad770b252228b6c55d4caff775acfe3594
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531169"
---
# <a name="tutorial-filter-inbound-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Tutorial: Filtrar tráfego de entrada com DNAT do Azure Firewall com o portal do Azure

Pode configurar a Tradução de Endereços de Rede de Destino (DNAT) do Azure Firewall para traduzir e filtrar o tráfego de entrada para as suas sub-redes. Ao configurar DNAT, a ação de coleção de regra NAT está definida como **Dnat**. Cada regra na coleção de regras NAT pode então ser utilizada para traduzir o endereço IP e a porta de firewall públicos para um IP e porta privados. As regras DNAT adicionam implicitamente uma regra de rede correspondente para permitir o tráfego traduzido. Pode substituir esse comportamento, ao adicionar explicitamente uma coleção de regras de rede com regras de negar que correspondem ao tráfego traduzido. Para saber mais sobre a lógica de processamento de regras do Azure Firewall, veja [Lógica de processamento de regras do Azure Firewall](rule-processing.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implementar uma firewall
> * Criar uma rota predefinida
> * Configurar uma regra DNAT
> * Testar a firewall

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Neste tutorial, vai criar duas VNets em modo peering:

- **VNet-Hub** - a firewall está nesta VNet.
- **VN-Spoke** - o servidor de carga de trabalho está nesta VNet.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Na página de boas-vindas do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique em **Adicionar**.
3. Em **Nome do grupo de recursos**, escreva **RG-DNAT-Test**.
4. Em **Subscrição**, selecione a sua subscrição.
5. Em **Localização do grupo de recursos**, selecione uma localização. Todos os recursos subsequentes que criar têm de estar na mesma localização.
6. Clique em **Criar**.

## <a name="set-up-the-network-environment"></a>Configurar o ambiente de rede

Em primeiro lugar, crie as VNets e, em seguida, configure o peering entre elas.

### <a name="create-the-hub-vnet"></a>Criar a VNet Hub

1. Na página de boas-vindas do portal do Azure, clique em **Todos os serviços**.
2. Em **Rede**, clique em **Redes virtuais**.
3. Clique em **Adicionar**.
4. Em **Nome**, escreva **VN-Hub**.
5. Em **Espaço de endereços**, escreva **10.0.0.0/16**.
6. Em **Subscrição**, selecione a sua subscrição.
7. Em **Grupo de recursos**, selecione **Utilizar existente** e, em seguida, selecione **RG-DNAT-Test**.
8. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
9. Em **Sub-rede**, em **Nome**, escreva **AzureFirewallSubnet**.

     A firewall estará nesta sub-rede, e o nome da sub-rede **tem** de ser AzureFirewallSubnet.
     > [!NOTE]
     > O tamanho mínimo da sub-rede AzureFirewallSubnet é /26.
10. Em **Intervalo de endereços**, escreva **10.0.1.0/24**.
11. Utilize as outras predefinições e, em seguida, clique em **Criar**.

### <a name="create-a-spoke-vnet"></a>Criar uma VNet spoke

1. Na página de boas-vindas do portal do Azure, clique em **Todos os serviços**.
2. Em **Rede**, clique em **Redes virtuais**.
3. Clique em **Adicionar**.
4. Em **Nome**, escreva **VN-Spoke**.
5. Em **Espaço de endereços**, escreva **192.168.0.0/16**.
6. Em **Subscrição**, selecione a sua subscrição.
7. Em **Grupo de recursos**, selecione **Utilizar existente** e, em seguida, selecione **RG-DNAT-Test**.
8. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
9. Em **Sub-rede**, para **Nome**, escreva **SN-Workload**.

    O servidor irá estar nesta sub-rede.
10. Em **Intervalo de endereços**, escreva **192.168.1.0/24**.
11. Utilize as outras predefinições e, em seguida, clique em **Criar**.

### <a name="peer-the-vnets"></a>Configurar o peering entre as VNets

Agora, configure o peering entre as duas VNets.

#### <a name="hub-to-spoke"></a>Hub para spoke

1. Clique na rede virtual **VN-Hub**.
2. Em **Definições**, clique em **Peerings**.
3. Clique em **Adicionar**.
4. Escreva **Peer-HubSpoke** para o nome.
5. Selecione **VN-Spoke** para a rede virtual.
6. Clique em **OK**.

#### <a name="spoke-to-hub"></a>Spoke para hub

1. Clique na rede virtual **VN-Spoke**.
2. Em **Definições**, clique em **Peerings**.
3. Clique em **Adicionar**.
4. Escreva **Peer-SpokeHub** para o nome.
5. Selecione **VN-Hub** para a rede virtual.
6. Clique em **Permitir tráfego reencaminhado**.
7. Clique em **OK**.

## <a name="create-a-virtual-machine"></a>Crie uma máquina virtual

Crie uma máquina virtual de carga de trabalho e coloque-a na sub-rede **SN-Workload**.

1. Na página de boas-vindas do portal do Azure, clique em **Todos os serviços**.
2. Em **Computação**, clique em **Máquinas virtuais**.
3. Clique em **Adicionar** e clique em **Windows Server**, clique em **Windows Server 2016 Datacenter** e, em seguida, clique em **Criar**.

**Noções básicas**

1. Em **Nome**, escreva **Srv-Workload**.
5. Escreva um nome de utilizador e uma palavra-passe.
6. Em **Subscrição**, selecione a sua subscrição.
7. Em **Grupo de recursos**, clique em **Utilizar existente** e, em seguida, selecione **RG-DNAT-Test**.
8. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
9. Clique em **OK**.

**Tamanho**

1. Escolha um tamanho adequado para uma máquina virtual de teste com o Windows Server. Por exemplo, **B2ms** (8 GB de RAM, 16 GB de armazenamento).
2. Clique em **Selecionar**.

**Definições**

1. Em **Rede**, para **Rede virtual**, selecione **VN-Spoke**.
2. Em **Sub-rede**, selecione **SN-Workload**.
3. Clique em **Endereço IP público** e, em seguida, clique em **Nenhum**.
4. Em **Selecionar portas de entrada públicas**, selecione **Não existem portas de entrada públicas**. 
2. Mantenha as outras predefinições e clique em **OK**.

**Resumo**

Reveja o resumo e clique em **Criar**. Este processo irá demorar alguns minutos a concluir.

Após a conclusão da implementação, tome nota do endereço IP privado para a máquina virtual. Será utilizado mais tarde quando configurar a firewall. Clique no nome da máquina virtual e, em **Definições**, clique em **Rede** para encontrar o endereço IP privado.

## <a name="deploy-the-firewall"></a>Implementar a firewall

1. Na página de boas-vindas do portal, clique em **Criar um recurso**.
2. Clique em **Rede**e, depois de **Destaques**, clique em **Ver tudo**.
3. Clique em **Firewall**e, em seguida, clique em **Criar**. 
4. Na página **Criar uma firewall**, utilize a seguinte tabela para configurar a firewall:

   |Definição  |Valor  |
   |---------|---------|
   |Name     |FW-DNAT-test|
   |Subscrição     |\<a sua subscrição\>|
   |Grupo de recursos     |**Utilizar existente**: RG-DNAT-Test |
   |Localização     |Selecionar a mesma localização que utilizou anteriormente|
   |Escolher uma rede virtual     |**Utilizar existente**: VN-Hub|
   |Endereço IP público     |**Crie um novo**. O endereço IP público tem de ser do tipo SKU Standard.|

5. Clique em **Rever + criar**.
6. Reveja o resumo e clique em **Criar** para criar a firewall.

   Este processo irá demorar alguns minutos a implementar.
7. Depois de concluída a implementação, vá para o grupo de recursos **RG-DNAT-Test** e clique na firewall **FW-DNAT-test**.
8. Anote o endereço IP privado. Vai utilizá-lo mais tarde quando criar a rota predefinida.

## <a name="create-a-default-route"></a>Criar uma rota predefinida

Na sub-rede **SN-Workload**, vai configurar a rota de saída predefinida para passar pela firewall.

1. Na página de boas-vindas do portal do Azure, clique em **Todos os serviços**.
2. Em **Rede**, clique em **Tabelas de rotas**.
3. Clique em **Adicionar**.
4. Em **Nome**, escreva **RT-FWroute**.
5. Em **Subscrição**, selecione a sua subscrição.
6. Em **Grupo de recursos**, selecione **Utilizar existente** e selecione **RG-DNAT-Test**.
7. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
8. Clique em **Criar**.
9. Clique em **Atualizar**  e, em seguida, clique na tabela de rotas **RT-FWroute**.
10. Clique em **Sub-redes** e, em seguida, clique em **Associar**.
11. Clique em **Rede virtual** e selecione **VN-Spoke**.
12. Em **Sub-rede**, clique em **SN-Workload**.
13. Clique em **OK**.
14. Clique em **Rotas** e, em seguida, clique em **Adicionar**.
15. Em **Nome da rota**, escreva **FW-DG**.
16. Em **Prefixo de endereço**, escreva **0.0.0.0/0**.
17. Em **Tipo de salto seguinte**, selecione **Aplicação virtual**.

    O Azure Firewall é, de facto, um serviço gerido, mas a aplicação virtual funciona nesta situação.
18. Em **Endereço do próximo salto**, escreva o endereço IP privado para a firewall, que anotou anteriormente.
19. Clique em **OK**.

## <a name="configure-a-nat-rule"></a>Configurar uma regra NAT

1. Abra **RG-DNAT-Test**e clique na firewall **FW-DNAT-test**. 
2. Na página **FW-DNAT-test**, em **Definições**, clique em **Regras**. 
3. Clique em **coleção de regras de NAT adicionar**. 
4. Em **Nome**, escreva **RC-DNAT-01**. 
5. Em **Prioridade**, escreva **200**. 
6. Em **Regras**, em **Nome**, escreva **RL-01**.
7. Em **Protocolo**, selecione **TCP**.
8. Em **Endereços de Origem**, escreva *. 
9. Em **Endereços de Destino**, escreva o endereço IP público da firewall. 
10. Em **Portas de Destino**, escreva **3389**. 
11. Em **Endereço Traduzido**, escreva o endereço IP privado da máquina virtual Srv-Workload. 
12. Em **Porta traduzida**, escreva **3389**. 
13. Clique em **Adicionar**. 

## <a name="test-the-firewall"></a>Testar a firewall

1. Ligue uma área de trabalho remota ao endereço IP público da firewall. Deverá estar ligado à máquina virtual **Srv-Workload**.
2. Feche o ambiente de trabalho remoto.

## <a name="clean-up-resources"></a>Limpar recursos

Pode manter os recursos da firewall para o próximo tutorial. Se já não precisar dos mesmos, elimine o grupo de recursos **RG-DNAT-Test** para eliminar todos os recursos relacionados com a firewall.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implementar uma firewall
> * Criar uma rota predefinida
> * Configurar uma regra DNAT
> * Testar a firewall

Em seguida, pode monitorizar os registos do Azure Firewall.

> [!div class="nextstepaction"]
> [Tutorial: Monitorize registos de Firewall do Azure](./tutorial-diagnostics.md)
