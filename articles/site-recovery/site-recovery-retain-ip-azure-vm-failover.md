---
title: Mantenha os endereços IP após o failover do Azure VM com a Recuperação do Site Azure
description: Descreve como reter endereços IP ao falhar sobre os VMs do Azure para a recuperação de desastres para uma região secundária com recuperação do local de Azure
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 650fb7f0877a98ef53ed3868550f9c084ecb5885
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023555"
---
# <a name="retain-ip-addresses-during-failover"></a>Reter endereços IP durante a ativação pós-falha

[A recuperação do local de Azure](site-recovery-overview.md) permite a recuperação de desastres para os VMs do Azure, replicando VMs para outra região do Azure, falhando se ocorrer uma paragem, e falhando de volta à região primária quando as coisas estão de volta ao normal.

Durante o failover, é melhor manter o endereço IP na região alvo idêntico à região de origem:

- Por predefinição, quando permite a recuperação de desastres para VMs Azure, a Recuperação do Site cria recursos-alvo baseados nas definições de recursos de origem. Para VMs Azure configurados com endereços IP estáticos, a Recuperação do Site tenta providenciar o mesmo endereço IP para o VM alvo, se não estiver a ser utilizado. Para obter uma explicação completa sobre como a Recuperação do Site lida com a endereçamento, [reveja este artigo](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- Para aplicações simples, a configuração padrão é suficiente. Para aplicações mais complexas, poderá necessitar de recursos adicionais para garantir que a conectividade funciona como esperado após o failover.


Este artigo fornece alguns exemplos para reter endereços IP em cenários de exemplo mais complexos. Os exemplos incluem:

- Failover para uma empresa com todos os recursos em execução em Azure
- Failover para uma empresa com uma implantação híbrida, e recursos que executam tanto no local como em Azure

## <a name="resources-in-azure-full-failover"></a>Recursos em Azure: falha total

A empresa A tem todas as suas aplicações em execução em Azure.

### <a name="before-failover"></a>Antes do failover

Aqui está a arquitetura antes do fracasso.

- A empresa A tem redes e sub-redes idênticas nas regiões de origem e alvo de Azure.
- Para reduzir o objetivo do tempo de recuperação (RTO), a empresa utiliza nós de réplica para SQL Server Always On, controladores de domínio, etc. Estes nós de réplica estão em um VNet diferente na região alvo, de modo que eles podem estabelecer conectividade local-a-local VPN entre as regiões de origem e alvo. Isto não é possível se o mesmo espaço de endereço IP for utilizado na fonte e no alvo.  
- Antes do failover, a arquitetura da rede é a seguinte:
    - A região primária é Azure East Asia
        - A Ásia Oriental tem um VNet **(Source VNet)** com espaço de endereço 10.1.0.0/16.
        - A Ásia Oriental tem cargas de trabalho divididas em três sub-redes no VNet:
            - **Sub-rede 1**: 10.1.1.0/24
            - **Sub-rede 2**: 10.1.2.0/24
            - **Sub-rede 3**: 10.1.3.0/24
    - Região secundária (alvo) é Azure Southeast Asia
        - O Sudeste Asiático tem um VNet de **recuperação (Recovery VNet)** idêntico ao **Source VNet**.
        - O Sudeste Asiático tem um VNet adicional **(Azure VNet)** com espaço de endereço 10.2.0.0/16.
        - **O Azure VNet** contém uma sub-rede **(Subnet 4**) com o espaço de endereço 10.2.4.0/24.
        - Os nós de réplica para SQL Server Always On, o controlador de domínio, etc. estão localizados na **Sub-rede 4**.
    - **A Source VNet** e **a Azure VNet** estão ligadas a uma ligação local-local VPN.
    - **O VNet de recuperação** não está ligado a nenhuma outra rede virtual.
    - **A Empresa A** atribui/verifica endereços IP alvo para itens replicados. O IP-alvo é o mesmo que o IP de origem para cada VM.

![Recursos em Azure antes do fracasso total](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Depois do failover

Se ocorrer uma paragem regional de origem, a Empresa A pode falhar todos os seus recursos para a região alvo.

- Com endereços IP alvo já em vigor antes do failover, a Empresa A pode orquestrar failover e estabelecer automaticamente ligações após falha entre **a Recovery VNet** e **a Azure VNet**. Isto é ilustrado no seguinte diagrama.
- Dependendo dos requisitos da aplicação, as ligações entre os dois VNets (**Recovery VNet** e **Azure VNet**) na região alvo podem ser estabelecidas antes, durante (como um passo intermédio) ou após a falha.
  - A empresa pode usar [planos de recuperação](site-recovery-create-recovery-plans.md) para especificar quando serão estabelecidas as ligações.
  - Podem ligar-se entre os VNets utilizando o espreitamento vNet ou a VPN site-to-site.
      - O espremiá-lo vNet não usa um gateway VPN e tem constrangimentos diferentes.
      - Os [preços](https://azure.microsoft.com/pricing/details/virtual-network) de peering VNet são calculados de forma diferente dos [preços](https://azure.microsoft.com/pricing/details/vpn-gateway)do VNet-to-VNet VPN Gateway . Para falhas, geralmente aconselhamos a usar o mesmo método de conectividade que as redes de origem, incluindo o tipo de ligação, para minimizar incidentes de rede imprevisíveis.

    ![Recursos em Azure totalmente falidos](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Recursos em Azure: falha de aplicação isolada

Pode ter de falhar ao nível da aplicação. Por exemplo, falhar sobre uma aplicação específica ou nível de aplicação localizado numa sub-rede dedicada.

- Neste cenário, embora possa reter endereços IP, geralmente não é aconselhável, uma vez que aumenta a probabilidade de inconsistências de conectividade. Também perderá a conectividade da sub-rede com outras sub-redes dentro do mesmo Azure VNet.
- Uma melhor maneira de fazer o failover de aplicações de nível subnet é usar diferentes endereços IP alvo para failover (se precisar de conectividade com outras sub-redes na origem VNet), ou isolar cada aplicação no seu próprio VNet dedicado na região de origem. Com esta última abordagem pode estabelecer conectividade entre redes na região de origem, e emular o mesmo comportamento quando você falha sobre a região alvo.  

Neste exemplo, a Empresa A coloca aplicações na região de origem em VNets dedicados, e estabelece conectividade entre esses VNets. Com este design, podem executar failover de aplicações isoladas e reter os endereços IP privados de origem na rede alvo.

### <a name="before-failover"></a>Antes do failover

Antes do fracasso, a arquitetura é a seguinte:

- Os VM de aplicação estão hospedados na região primária do Azure East Asia:
    - **App1** Os VMs estão localizados no VNet **Source VNet 1:** 10.1.0.0/16.
    - **App2** Os VMs estão localizados no VNet **Source VNet 2:** 10.2.0.0/16.
    - **Fonte VNet 1** tem duas sub-redes.
    - **Fonte VNet 2** tem duas sub-redes.
- A região secundária (alvo) é a Azure Southeast Asia - O Sudeste Asiático tem uma recuperação VNet **1** e **Recovery VNet 2**) que são idênticas à **Fonte VNet 1** e **Source VNet 2**.
        - **Recovery VNet 1** e **Recovery VNet 2** cada um tem duas sub-redes que combinam com as sub-redes na **Fonte VNet 1** e **Source VNet 2** - Sudeste Asiático tem um VNet adicional **(Azure VNet**) com espaço de endereço 10.3.0.0/16.
        - **O Azure VNet** contém uma sub-rede **(Subnet 4**) com o espaço de endereço 10.3.4.0/24.
        - Os nós de réplica para SQL Server Always On, o controlador de domínio, etc. estão localizados na **Sub-rede 4**.
- Existem várias ligações VPN site-to-site: 
    - **Fonte VNet 1** e **Azure VNet**
    - **Fonte VNet 2** e **Azure VNet**
    - **Fonte VNet 1** e **Source VNet 2** estão ligados ao site VPN
- **Recovery VNet 1** e **Recovery VNet 2** não estão ligados a quaisquer outros VNets.
- **Empresa A** configura as portas VPN na **Recovery VNet 1** e **Recovery VNet 2,** para reduzir o RTO.  
- **Recovery VNet1** e **Recovery VNet2** não estão ligados a nenhuma outra rede virtual.
- Para reduzir o objetivo do tempo de recuperação (RTO), os gateways VPN são configurados na **Recovery VNet1** e **no Recovery VNet2** antes do failover.

    ![Recursos em Azure antes do failover da app](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Depois do failover

Em caso de interrupção ou problema que afete uma única aplicação (em **Source VNet 2 no nosso exemplo), a Empresa A pode recuperar a aplicação afetada da seguinte forma:


- Desligue as ligações VPN entre **a Fonte VNet1** e **a Source VNet2**, e entre **a Source VNet2** e **a Azure VNet** .
- Estabelecer ligações VPN entre **Source VNet1** e **Recovery VNet2**, e entre **Recovery VNet2** e **Azure VNet**.
- Falha em VMs na **Fonte VNet2** para **Recovery VNet2**.

![Recursos em falha de aplicação Azure](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Este exemplo pode ser expandido para incluir mais aplicações e ligações de rede. A recomendação é seguir um modelo de conexão semelhante, na medida do possível, quando falhar de fonte em alvo.
- Os Gateways VPN usam endereços IP públicos e lúpulo de gateway para estabelecer ligações. Se não quiser usar endereços IP públicos, ou quiser evitar saltos extra, pode utilizar [o Azure VNet espreitando](../virtual-network/virtual-network-peering-overview.md) para espreitar redes virtuais em [regiões de Azure suportadas.](../virtual-network/virtual-network-manage-peering.md#cross-region)

## <a name="hybrid-resources-full-failover"></a>Recursos híbridos: falha total

Neste cenário, **a Empresa B** gere um negócio híbrido, com parte da infraestrutura de aplicações a funcionar no Azure, e o restante a funcionar no local. 

### <a name="before-failover"></a>Antes do failover

Aqui está como é a arquitetura da rede antes do fracasso.

- Os VM de aplicação estão hospedados em Azure East Asia.
- A Ásia Oriental tem um VNet **(Source VNet)** com espaço de endereço 10.1.0.0/16.
  - A Ásia Oriental tem cargas de trabalho divididas em três sub-redes em **Source VNet:**
    - **Sub-rede 1**: 10.1.1.0/24
    - **Sub-rede 2**: 10.1.2.0/24
    - **Sub-rede 3**: 10.1.3.0/24, utilizando uma rede virtual Azure com o espaço de endereço 10.1.0.0/16. Esta rede virtual chama-se **Source VNet**
      - A região secundária (alvo) é a Azure Southeast Asia:
  - O Sudeste Asiático tem um VNet de **recuperação (Recovery VNet)** idêntico ao **Source VNet**.
- Os VMs na Ásia Oriental estão ligados a um datacenter no local com a Azure ExpressRoute ou VPN site-to-site.
- Para reduzir o RTO, a Empresa B prevê portais de entrada no Recovery VNet no Sudeste Asiático antes de falhar.
- A Empresa B atribui/verifica endereços IP alvo para VMs replicados. O endereço IP alvo é o mesmo que o endereço IP de origem para cada VM.


![Conectividade no local-a-Azure antes do failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Depois do failover


Se ocorrer uma paragem regional de origem, a Empresa B pode falhar todos os seus recursos para a região-alvo.

- Com os endereços IP alvo já em vigor antes do failover, a Empresa B pode orquestrar o failover e estabelecer automaticamente ligações após a falha entre **a Recovery VNet** e **a Azure VNet**.
- Dependendo dos requisitos da aplicação, as ligações entre os dois VNets (**Recovery VNet** e **Azure VNet**) na região alvo podem ser estabelecidas antes, durante (como um passo intermédio) ou após a falha. A empresa pode usar [planos de recuperação](site-recovery-create-recovery-plans.md) para especificar quando serão estabelecidas as ligações.
- A ligação original entre a Azure East Asia e o datacenter no local deve ser desligada antes de estabelecer a ligação entre o Sudeste Asiático e o centro de dados no local.
- O encaminhamento no local é reconfigurado para apontar para a região alvo e gateways post failover.

![Conectividade no local-a-Azure após falha](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Recursos híbridos: falha de aplicação isolada

A empresa B não pode falhar com aplicações isoladas ao nível da sub-rede. Isto porque o espaço de endereço nos VNets de origem e recuperação é o mesmo, e a fonte original para a ligação no local está ativa.

 - Para a resiliência da aplicação a Empresa B terá de colocar cada aplicação no seu próprio Azure VNet dedicado.
 - Com cada aplicação num VNet separado, a Empresa B pode falhar em aplicações isoladas e encaminhar ligações de origem para a região alvo.

## <a name="next-steps"></a>Passos seguintes

Conheça os [planos de recuperação.](site-recovery-create-recovery-plans.md)
