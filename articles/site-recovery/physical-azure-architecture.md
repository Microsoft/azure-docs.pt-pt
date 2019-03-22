---
title: Arquitetura para a recuperação de desastres do servidor físico para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo fornece uma visão geral dos componentes e da arquitetura utilizada durante a recuperação após desastre de servidores físicos no local para o Azure com o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 9476713bdca185fd84289fca3cf7aa304ad3f9fb
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311429"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Servidor físico para a arquitetura da recuperação após desastre do Azure

Este artigo descreve a arquitetura e os processos utilizados ao replicar, efetuar a ativação pós-falha e recuperar servidores físicos do Windows e Linux entre um site no local e o Azure, utilizando o [do Azure Site Recovery](site-recovery-overview.md) serviço.


## <a name="architectural-components"></a>Componentes da arquitetura

A tabela seguinte e o gráfico fornecem uma visão geral dos componentes utilizados para a replicação de servidor físico para o Azure.  

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Uma subscrição do Azure, uma conta de armazenamento do Azure e uma rede do Azure. | Dados replicados de VMs no local são armazenados na conta de armazenamento. VMs do Azure são criadas com os dados replicados quando executa uma ativação pós-falha do local para o Azure. As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Servidor de configuração** | Uma única máquina no local física ou VM do VMware é implementada para executar todos os locais componentes do Site Recovery. A VM executa o servidor de configuração, o servidor de processos e o servidor de destino mestre. | O servidor de configuração coordena as comunicações entre o local e o Azure, e gere a replicação de dados.
 **Servidor de processos**:  | Instalado por predefinição, juntamente com o servidor de configuração. | Atua como um gateway de replicação. Recebe dados de replicação, otimiza-os com a colocação em cache, compressão e encriptação, e envia-os para o armazenamento do Azure.<br/><br/> O servidor de processos também instala o serviço de mobilidade em servidores que pretende replicar.<br/><br/> À medida que cresça a implementação, pode adicionar servidores de processo adicionais, em separado para processar maiores volumes de tráfego de replicação.
 **Servidor de destino mestre** | Instalado por predefinição, juntamente com o servidor de configuração. | Processa dados de replicação durante a reativação pós-falha a partir do Azure.<br/><br/> Para implementações maiores, pode adicionar um servidor de destino mestre adicionais, em separado para a reativação pós-falha.
**Servidores replicados** | O serviço de mobilidade está instalado em cada servidor, replicar. | Recomendamos que permite que a instalação automática do servidor de processos. Em alternativa pode instalar o serviço manualmente ou utilizar um método de implementação automatizada, como o System Center Configuration Manager.

**Físico para a arquitetura do Azure**

![Componentes](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Processo de replicação

1. Configurar a implementação, incluindo no local e os componentes do Azure. No cofre dos serviços de recuperação, especifique a origem de replicação e de destino, configurar o servidor de configuração, criar uma política de replicação e ative a replicação.
2. Replicar máquinas em conformidade com a política de replicação e uma cópia inicial dos dados de servidor é replicado para o armazenamento do Azure.
3. Depois de concluída a replicação inicial, começa a replicação das alterações delta para o Azure. As alterações registadas relativas a uma máquina são guardadas num ficheiro .hrl.
    - As máquinas comunicar com o servidor de configuração na porta HTTPS 443 entrada, para a gestão da replicação.
    - As máquinas enviam dados de replicação para o servidor de processos na porta HTTPS 9443 entrada (pode ser modificada).
    - O servidor de configuração orquestra a gestão da replicação com o Azure através da porta HTTPS 443 de saída.
    - O servidor de processos recebe os dados das máquinas de origem, otimiza-os, encripta-os e envia-os para o armazenamento do Azure através da porta 443 de saída.
    - Se ativar a consistência multi-VM, as máquinas no grupo de replicação comunicam entre si pela porta 20004. A consistência multi-VM é utilizada se agrupar várias máquinas em grupos de replicação que partilhem pontos de consistência de falhas e pontos de consistência de aplicação quando é efetuada a ativação pós-falha dos mesmos. Isto é útil se as máquinas estiverem a executar a mesma carga de trabalho e tiverem de ser consistentes.
4. O tráfego é replicado para pontos finais públicos do armazenamento do Azure, através da Internet. Em alternativa, pode utilizar o [peering público](../expressroute/expressroute-circuit-peerings.md#publicpeering) do Azure ExpressRoute. A replicação de tráfego através de uma rede de VPNs a partir de um site no local para o Azure não é suportada.


**Físico para o processo de replicação do Azure**

![Processo de replicação](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

Após a replicação está configurada e que já executou um teste de recuperação após desastre (ativação pós-falha de teste) para verificar que tudo está a funcionar conforme esperado, pode executar a ativação pós-falha e reativação pós-falha conforme pretender. Tenha em atenção que:

- A ativação pós-falha planeada não é suportada.
- Tem reativação pós-falha para um VM de VMware no local. Isso significa que precisa de uma infraestrutura de VMware no local, mesmo quando replicar servidores físicos no local para o Azure.
- Efetuar a ativação pós-falha de uma máquina individual ou criar planos de recuperação, para efetuar a ativação pós-falha de várias máquinas em conjunto.
- Ao executar uma ativação pós-falha, as VMs do Azure são criadas a partir de dados replicados no armazenamento do Azure.
- Depois de acionar a ativação pós-falha inicial, confirmá-lo a começar a aceder à carga de trabalho da VM do Azure.
- Quando o site no local primário estiver novamente disponível, pode fazer a reativação pós-falha.
- Terá de configurar uma infraestrutura de reativação pós-falha, incluindo:
    - **Servidor de processo temporário no Azure**: Para efetuar a ativação pós-falha do Azure, configurar uma VM do Azure para atuar como um servidor de processos, para processar a replicação do Azure. É possível eliminar esta VM após a conclusão da reativação pós-falha.
    - **Ligação VPN**: Para efetuar a reativação pós-falha, terá uma ligação VPN (ou Azure ExpressRoute) da rede do Azure para o site no local.
    - **Servidor de destino principal separado**: Por predefinição, o servidor de destino mestre foi instalado com o servidor de configuração, sobre o VM, de VMware no local processa a reativação pós-falha. No entanto, se precisar de efetuar a ativação pós-falha grandes volumes de tráfego, deve configurar um servidor de destino principal independente no local para esta finalidade.
    - **Política de reativação pós-falha**: Para replicar para o seu site no local, terá de uma política de reativação pós-falha. Isso foi criado automaticamente quando criou a política de replicação no local para o Azure.
    - **Infraestrutura do VMware**: Precisa de uma infraestrutura do VMware para reativação pós-falha. Não pode efetuar a reativação pós-falha num servidor físico.
- Depois dos componentes estão em vigor, a reativação pós-falha ocorre em três fases:
    - Fase 1: Voltar a proteger as VMs do Azure para que eles replicar a partir do Azure para as VMs do VMware no local.
    - Fase 2: Execute uma ativação pós-falha para o site no local.
    - Fase 3: Depois de tem realizarão a reativação pós-falha cargas de trabalho, reativar a replicação.

**Reativação pós-falha de VMware do Azure**

![Reativação pós-falha](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Passos Seguintes

Siga [deste tutorial](physical-azure-disaster-recovery.md) para ativar o servidor físico para replicação do Azure.
