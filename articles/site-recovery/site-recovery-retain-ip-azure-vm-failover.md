---
title: Mantenha endereços IP após falha do Azure VM com recuperação do site Azure
description: Descreve como reter endereços IP ao falhar sobre Os VMs Azure para recuperação de desastres para uma região secundária com recuperação do site Azure
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 650fb7f0877a98ef53ed3868550f9c084ecb5885
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257566"
---
# <a name="retain-ip-addresses-during-failover"></a>Reter endereços IP durante a failover

[A Recuperação](site-recovery-overview.md) do Sítio Azure permite a recuperação de desastres para os VMs azure, replicando VMs para outra região de Azure, falhando se ocorrer uma paragem, e falhando na região primária quando as coisas voltaram ao normal.

Durante o failover, é melhor manter o endereço IP na região-alvo idêntico à região-fonte:

- Por padrão, ao ativar a recuperação de desastres para VMs Azure, a Recuperação do Site cria recursos-alvo baseados em configurações de recursos de origem. Para VMs Azure configurados com endereços IP estáticos, a Recuperação do Site tenta fornecer o mesmo endereço IP para o VM alvo, se não estiver a ser utilizado. Para obter uma explicação completa de como a Recuperação do Site lida com o endereço, [reveja este artigo](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- Para aplicações simples, a configuração predefinida é suficiente. Para aplicações mais complexas, poderá ser necessário fornecer recursos adicionais para garantir que a conectividade funcione como esperado após o fracasso.


Este artigo fornece alguns exemplos para manter endereços IP em cenários de exemplo mais complexos. Os exemplos incluem:

- Failover para uma empresa com todos os recursos em funcionamento no Azure
- Failover para uma empresa com implantação híbrida, e recursos que executam tanto no local como no Azure

## <a name="resources-in-azure-full-failover"></a>Recursos em Azure: falha total

A Empresa A tem todas as suas aplicações em funcionamento no Azure.

### <a name="before-failover"></a>Antes do fracasso

Aqui está a arquitetura antes do fracasso.

- A empresa A tem redes e subredes idênticas nas regiões de origem e alvo de Azure.
- Para reduzir o objetivo de tempo de recuperação (RTO), a empresa utiliza nós de réplica para O Servidor SQL Always On, controladores de domínio, etc. Estes nódosos de réplica estão em um VNet diferente na região alvo, para que possam estabelecer a conectividade vpn site-to-site entre a fonte e as regiões-alvo. Isto não é possível se o mesmo espaço de endereço IP for usado na fonte e no alvo.  
- Antes do fracasso, a arquitetura da rede é a seguinte:
    - A região primária é a Ásia Oriental de Azure
        - A Ásia Oriental tem uma VNet **(Source VNet)** com espaço de endereço 10.1.0.0.0/16.
        - A Ásia Oriental tem cargas de trabalho divididas em três subnets na VNet:
            - **Subposição 1:** 10.1.1.0/24
            - **Subposição 2**: 10.1.2.0/24
            - **Subposição 3**: 10.1.3.0/24
    - Região secundária (alvo) é Azure Sudeste Asiático
        - O Sudeste Asiático tem uma VNet de recuperação (**Recovery VNet**) idêntica à **Source VNet**.
        - O Sudeste Asiático tem um VNet adicional (**Azure VNet)** com espaço de endereço 10.2.0.0.0/16.
        - **O Azure VNet** contém uma**sub-rede (Subnet 4**) com espaço de endereço 10.2.4.0/24.
        - Os nós de réplica para o Servidor SQL Always On, controlador de domínio, etc. estão localizados na **Subnet 4**.
    - **Fonte VNet** e **Azure VNet** estão ligados a uma ligação vpN site-to-site.
    - **A Recovery VNet** não está ligada a nenhuma outra rede virtual.
    - **Empresa A** atribui/verifica endereços IP alvo para itens replicados. O IP alvo é o mesmo que o IP de origem para cada VM.

![Recursos em Azure antes da falha total](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Depois do fracasso

Se ocorrer uma falha regional de origem, a Empresa A pode falhar em todos os seus recursos para a região alvo.

- Com endereços IP alvo já em vigor antes da falha, a Empresa A pode orquestrar o failover e estabelecer automaticamente ligações após a falha entre **recovery VNet** e **Azure VNet**. Isto é ilustrado no diagrama a seguir.
- Dependendo dos requisitos da aplicação, as ligações entre os dois VNets (**Recovery VNet** e **Azure VNet**) na região alvo podem ser estabelecidas antes, durante (como um passo intermédio) ou após a falha.
  - A empresa pode usar planos de [recuperação](site-recovery-create-recovery-plans.md) para especificar quando serão estabelecidas as ligações.
  - Podem ligar-se entre os VNets utilizando o peering VNet ou o VPN site-to-site.
      - O VNet peering não utiliza um gateway de VPN e tem restrições de diferentes.
      - Os [preços](https://azure.microsoft.com/pricing/details/virtual-network) de peering VNet são calculados de forma diferente do [preço](https://azure.microsoft.com/pricing/details/vpn-gateway)vNet-to-VNet VPN Gateway . Para falhas, geralmente aconselhamos a usar o mesmo método de conectividade que as redes de origem, incluindo o tipo de ligação, para minimizar incidentes imprevisíveis da rede.

    ![Recursos em Azure falha plena](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Recursos em Azure: falha de app isolada

Pode ter de falhar ao nível da aplicação. Por exemplo, falhar sobre uma aplicação ou nível de aplicação específico localizado numa subnet dedicada.

- Neste cenário, embora possa reter endereçoip, geralmente não é aconselhável, uma vez que aumenta a probabilidade de inconsistências de conectividade. Também perderá a conectividade da sub-rede com outras subredes dentro do mesmo Azure VNet.
- Uma melhor maneira de fazer falha na aplicação de nível de subnet é usar diferentes endereços IP alvo para falha (se precisar de conectividade com outras subredes na Fonte VNet), ou isolar cada aplicação no seu próprio VNet dedicado na região de origem. Com esta última abordagem pode estabelecer conectividade entre redes na região de origem, e imitar o mesmo comportamento quando falha na região alvo.  

Neste exemplo, a Empresa A coloca aplicações na região de origem em VNets dedicados, e estabelece conectividade entre esses VNets. Com este design, podem executar falhas de aplicação isoladas e reter os endereços IP privados de origem na rede alvo.

### <a name="before-failover"></a>Antes do fracasso

Antes do fracasso, a arquitetura é a seguinte:

- Os VMs de aplicação estão hospedados na região principal de Azure East Asia:
    - **App1** Os VMs estão localizados em VNet **Source VNet 1:** 10.1.0.0.0/16.
    - **App2** Os VMs estão localizados em VNet **Source VNet 2**: 10.2.0.0.0/16.
    - **Fonte VNet 1** tem duas subredes.
    - **Fonte VNet 2** tem duas subredes.
- A região secundária (alvo) é o Azure Southeast Asia - Sudeste Asiático tem uma recuperação VNets (**Recovery VNet 1** e **Recovery VNet 2**) que são idênticas à Fonte **VNet 1** e Fonte **VNet 2**.
        - **Recovery VNet 1** e Recovery **VNet 2** têm duas subredes que correspondem às subredes em Source **VNet 1** e Source **VNet 2** - Sudeste Asiático tem um VNet adicional (**Azure VNet**) com espaço de endereço 10.3.0.0/16.
        - **Azure VNet** contém uma sub-rede **(Subnet 4**) com espaço de endereço 10.3.4.0/24.
        - Os nós de réplica do Servidor SQL Always On, controlador de domínio, etc. estão localizados na **Subnet 4**.
- Existem uma série de ligações VPN site-to-site: 
    - **Fonte VNet 1** e **Azure VNet**
    - **Fonte VNet 2** e **Azure VNet**
    - **Fonte VNet 1** e **Source VNet 2** estão ligados com VPN site-to-site
- **Recuperação VNet 1** e **Recovery VNet 2** não estão ligados a quaisquer outros VNets.
- **Empresa A** configura gateways VPN na **Recovery VNet 1** e Recovery **VNet 2,** para reduzir o RTO.  
- **Recuperação VNet1** e **Recovery VNet2** não estão ligados a qualquer outra rede virtual.
- Para reduzir o objetivo de tempo de recuperação (RTO), os gateways VPN são configurados em **Recovery VNet1** e **Recovery VNet2** antes da falha.

    ![Recursos em Azure antes do fracasso da app](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Depois do fracasso

Em caso de interrupção ou problema que afete uma única aplicação (em **Source VNet 2 no nosso exemplo), a Empresa A pode recuperar a aplicação afetada da seguinte forma:


- Desligue as ligações VPN entre **a Fonte VNet1** e **a Fonte VNet2,** e entre **fonte VNet2** e **Azure VNet** .
- Estabelecer ligações VPN entre **a Fonte VNet1** e **recovery VNet2**, e entre **recovery VNet2** e **Azure VNet**.
- Falhar sobre VMs em **Source VNet2** para **Recovery VNet2**.

![Recursos na app Azure falham](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Este exemplo pode ser expandido para incluir mais aplicações e ligações de rede. A recomendação é seguir um modelo de conexão semelhante, na medida do possível, quando falhar de origem para alvo.
- Os Gateways VPN utilizam endereços IP públicos e lúpulo de gateway para estabelecer ligações. Se não quiser utilizar endereços IP públicos, ou quiser evitar lúpulo extra, pode utilizar o [Azure VNet a espreitar](../virtual-network/virtual-network-peering-overview.md) para que redes virtuais entre [regiões de Azure apoiadas.](../virtual-network/virtual-network-manage-peering.md#cross-region)

## <a name="hybrid-resources-full-failover"></a>Recursos híbridos: falha total

Neste cenário, a **Empresa B** gere um negócio híbrido, com parte da infraestrutura de aplicação a funcionar no Azure, e o restante a funcionar no local. 

### <a name="before-failover"></a>Antes do fracasso

Aqui está o aspeto da arquitetura da rede antes do fracasso.

- Os VMs de aplicação estão hospedados em Azure East Asia.
- A Ásia Oriental tem uma VNet **(Source VNet)** com espaço de endereço 10.1.0.0.0/16.
  - A Ásia Oriental tem cargas de trabalho divididas em três subnets na **Source VNet:**
    - **Subposição 1:** 10.1.1.0/24
    - **Subposição 2**: 10.1.2.0/24
    - **Sub-rede 3**: 10.1.3.0/24, utilizando uma rede virtual Azure com espaço de endereço 10.1.0.0/16. Esta rede virtual chama-se **Source VNet**
      - A região secundária (alvo) é azure sudeste asiático:
  - O Sudeste Asiático tem uma VNet de recuperação (**Recovery VNet**) idêntica à **Source VNet**.
- VMs no leste asiático estão ligados a um datacenter no local com Azure ExpressRoute ou VPN site-to-site.
- Para reduzir o RTO, a empresa B disponibiliza gateways na Recovery VNet no Sudeste Asiático de Azure antes do fracasso.
- A empresa B atribui/verifica os endereços IP do alvo para VMs replicados. O endereço IP alvo é o mesmo que o endereço IP de origem para cada VM.


![Conectividade no local-para-Azure antes do fracasso](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Depois do fracasso


Se ocorrer uma falha regional de origem, a Empresa B pode falhar em todos os seus recursos para a região-alvo.

- Com endereços IP alvo já em vigor antes da falha, a Empresa B pode orquestrar a falha e estabelecer automaticamente ligações após a falha entre **recovery VNet** e **Azure VNet**.
- Dependendo dos requisitos da aplicação, as ligações entre os dois VNets (**Recovery VNet** e **Azure VNet**) na região alvo podem ser estabelecidas antes, durante (como um passo intermédio) ou após a falha. A empresa pode usar planos de [recuperação](site-recovery-create-recovery-plans.md) para especificar quando serão estabelecidas as ligações.
- A ligação original entre o Azure East Asia e o centro de dados no local deve ser desligada antes de estabelecer a ligação entre o Azure Southeast Asia e o centro de dados no local.
- O encaminhamento no local é reconfigurado para apontar para a região alvo e gateways post failover.

![Conectividade no local-para-Azure após falha](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Recursos híbridos: falha de app isolada

A empresa B não pode falhar sobre aplicações isoladas ao nível da subnet. Isto porque o espaço de endereço na fonte e recuperação vNets é o mesmo, e a fonte original para a ligação no local está ativa.

 - Para a resiliência da aplicação, a Empresa B terá de colocar cada aplicação no seu próprio Azure VNet dedicado.
 - Com cada aplicação num VNet separado, a Empresa B pode falhar sobre aplicações isoladas e ligações de origem de rotas para a região alvo.

## <a name="next-steps"></a>Passos Seguintes

Conheça os planos de [recuperação.](site-recovery-create-recovery-plans.md)
