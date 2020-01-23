---
title: Arquitetura de recuperação de desastres do servidor físico no Azure Site Recovery
description: Este artigo fornece uma visão geral dos componentes e da arquitetura usados durante a recuperação de desastres de servidores físicos locais para o Azure com o serviço de Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 72f21babd4d12e69cd346d8693e5ed4fe9117134
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513954"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Servidor físico para arquitetura de recuperação de desastre do Azure

Este artigo descreve a arquitetura e os processos usados quando você faz a replicação, o failover e a recuperação de servidores físicos do Windows e Linux entre um site local e o Azure, usando o serviço de [Azure site Recovery](site-recovery-overview.md) .


## <a name="architectural-components"></a>Componentes da arquitetura

A tabela e o gráfico a seguir fornecem uma exibição de alto nível dos componentes usados para replicação de servidor físico para o Azure.  

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Uma assinatura do Azure e uma rede do Azure. | Os dados replicados de computadores físicos locais são armazenados em Azure Managed disks. As VMs do Azure são criadas com os dados replicados quando você executa um failover do local para o Azure. As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Servidor de configuração** | Uma única máquina física local ou VMware VM é implantada para executar todos os componentes de Site Recovery locais. A VM executa o servidor de configuração, o servidor de processo e o servidor de destino mestre. | O servidor de configuração coordena as comunicações entre o local e o Azure, e gere a replicação de dados.
 **Servidor de processos**:  | Instalado por padrão junto com o servidor de configuração. | Atua como um gateway de replicação. Recebe dados de replicação, otimiza-os com a colocação em cache, compressão e encriptação, e envia-os para o armazenamento do Azure.<br/><br/> O servidor de processo também instala o serviço de mobilidade nos servidores que você deseja replicar.<br/><br/> À medida que sua implantação cresce, você pode adicionar servidores de processo adicionais e separados para lidar com volumes maiores de tráfego de replicação.
 **Servidor de destino mestre** | Instalado por padrão junto com o servidor de configuração. | Processa dados de replicação durante a reativação pós-falha a partir do Azure.<br/><br/> Para grandes implantações, você pode adicionar um servidor de destino mestre adicional e separado para failback.
**Servidores replicados** | O serviço de mobilidade é instalado em cada servidor que você replica. | Recomendamos que você permita a instalação automática do servidor de processo. Como alternativa, você pode instalar o serviço manualmente ou usar um método de implantação automatizado, como Configuration Manager.

**Arquitetura física para o Azure**

![Componentes](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Processo de replicação

1. Você configura a implantação, incluindo componentes locais e do Azure. No cofre dos serviços de recuperação, você especifica a origem e o destino da replicação, configura o servidor de configuração, cria uma política de replicação e habilita a replicação.
2. Os computadores replicam de acordo com a política de replicação e uma cópia inicial dos dados do servidor é replicada para o armazenamento do Azure.
3. Após a conclusão da replicação inicial, a replicação de alterações delta para o Azure é iniciada. As alterações registadas relativas a uma máquina são guardadas num ficheiro .hrl.
    - Os computadores se comunicam com o servidor de configuração na porta HTTPS 443 de entrada para o gerenciamento de replicação.
    - Os computadores enviam dados de replicação para o servidor de processo na porta HTTPS 9443 de entrada (pode ser modificado).
    - O servidor de configuração orquestra a gestão da replicação com o Azure através da porta HTTPS 443 de saída.
    - O servidor de processos recebe os dados das máquinas de origem, otimiza-os, encripta-os e envia-os para o armazenamento do Azure através da porta 443 de saída.
    - Se ativar a consistência multi-VM, as máquinas no grupo de replicação comunicam entre si pela porta 20004. A consistência multi-VM é utilizada se agrupar várias máquinas em grupos de replicação que partilhem pontos de consistência de falhas e pontos de consistência de aplicação quando é efetuada a ativação pós-falha dos mesmos. Isto é útil se as máquinas estiverem a executar a mesma carga de trabalho e tiverem de ser consistentes.
4. O tráfego é replicado para pontos finais públicos do armazenamento do Azure, através da Internet. Em alternativa, pode utilizar o [peering público](../expressroute/about-public-peering.md) do Azure ExpressRoute. A replicação de tráfego através de uma rede de VPNs a partir de um site no local para o Azure não é suportada.


**Processo de replicação de físico para o Azure**

![Processo de replicação](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

Depois que a replicação é configurada e você executa uma análise de recuperação de desastre (failover de teste) para verificar se tudo está funcionando conforme o esperado, você pode executar o failover e o failback conforme necessário. Tenha em atenção que:

- A ativação pós-falha planeada não é suportada.
- Você deve fazer failback para uma VM VMware local. Isso significa que você precisa de uma infraestrutura VMware local, mesmo quando você Replica servidores físicos locais para o Azure.
- Você faz failover de um único computador ou cria planos de recuperação para fazer failover de vários computadores juntos.
- Quando você executa um failover, as VMs do Azure são criadas a partir de dados replicados no armazenamento do Azure.
- Depois de disparar o failover inicial, você o confirma para começar a acessar a carga de trabalho da VM do Azure.
- Quando o site no local primário estiver novamente disponível, pode fazer a reativação pós-falha.
- Você precisa configurar uma infraestrutura de failback, incluindo:
    - **Servidor de processo temporário no Azure**: para fazer failback do Azure, você configura uma VM do Azure para atuar como um servidor de processo, para lidar com a replicação do Azure. É possível eliminar esta VM após a conclusão da reativação pós-falha.
    - **Conexão VPN**: para fazer failback, você precisa de uma conexão VPN (ou Azure ExpressRoute) da rede do Azure para o site local.
    - **Servidor de destino mestre separado**: por padrão, o servidor de destino mestre que foi instalado com o servidor de configuração, na VM do VMware local, lida com o failback. No entanto, se você precisar fazer failback de grandes volumes de tráfego, deverá configurar um servidor de destino mestre local separado para essa finalidade.
    - **Política de reativação pós-falha**: para replicar de novo para o site no local, precisa de uma política de reativação pós-falha. Isso foi criado automaticamente quando você criou sua política de replicação do local para o Azure.
    - **Infraestrutura do VMware**: você precisa de uma infraestrutura VMware para failback. Não pode efetuar a reativação pós-falha num servidor físico.
- Depois que os componentes estiverem em vigor, o failback ocorrerá em três estágios:
    - Estágio 1: proteger novamente as VMs do Azure para que elas sejam replicadas do Azure de volta para as VMs do VMware locais.
    - Estágio 2: executar um failover para o site local.
    - Estágio 3: após o failback das cargas de trabalho, você reabilitará a replicação.

**Failback do VMware do Azure**

![Reativação pós-falha](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Passos seguintes

Siga [este tutorial](physical-azure-disaster-recovery.md) para habilitar o servidor físico para a replicação do Azure.
