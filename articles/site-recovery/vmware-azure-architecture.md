---
title: VMware para a arquitetura da recuperação após desastre do Azure no Azure Site Recovery | Documentos da Microsoft
description: Este artigo fornece uma visão geral dos componentes e da arquitetura utilizada ao definir a recuperação após desastre de VMs de VMware no local para o Azure com o Azure Site Recovery
author: mayurigupta13
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: mayg
ms.openlocfilehash: 5f9ead8df3d0dfb2695def46087d2ea994b8b75b
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57340446"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>VMware para a arquitetura da recuperação após desastre do Azure

Este artigo descreve a arquitetura e os processos utilizados ao implementar a replicação de recuperação após desastre, ativação pós-falha e recuperação de máquinas de virtuais de VMware (VMs) entre um site de VMware no local e o Azure com o [Azure Site Recovery](site-recovery-overview.md) serviço.


## <a name="architectural-components"></a>Componentes da arquitetura

A tabela seguinte e o gráfico fornecem uma visão geral dos componentes utilizados para a recuperação de desastre do VMware para o Azure.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Uma subscrição do Azure, conta de armazenamento do Azure para a cache, de disco gerido e de rede do Azure. | Dados replicados de VMs no local são armazenados no armazenamento do Azure. VMs do Azure são criadas com os dados replicados quando executar uma ativação pós-falha do local para o Azure. As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Máquina do servidor de configuração** | Uma única máquina no local. Recomendamos que executá-la como uma VM de VMware que podem ser implementadas a partir de um modelo do OVF transferido.<br/><br/> A máquina é executada todos os componentes do Site Recovery no local, que incluem o servidor de configuração, o servidor de processos e o servidor de destino mestre. | **Servidor de configuração**: Coordena as comunicações entre no local e o Azure e gere a replicação de dados.<br/><br/> **Servidor de processos**: Instalado por predefinição no servidor de configuração. Recebe dados de replicação. otimiza-os com colocação em cache, compressão e encriptação; e envia-os para o armazenamento do Azure. O servidor de processos também instala o serviço de mobilidade de recuperação de sites do Azure nas VMs que pretende replicar, e efetua a deteção automática de máquinas no local. À medida que cresça a implementação, pode adicionar servidores de processo adicionais, em separado para processar maiores volumes de tráfego de replicação.<br/><br/> **Servidor de destino mestre**: Instalado por predefinição no servidor de configuração. Ele processa dados de replicação durante a reativação pós-falha do Azure. Para implementações maiores, pode adicionar um servidor de destino mestre adicionais, em separado para a reativação pós-falha.
**Servidores de VMware** | VMware VMs são alojadas em servidores do ESXi do vSphere no local. Recomendamos um servidor vCenter para gerir os anfitriões. | Durante a implementação da recuperação de Site, pode adicionar servidores VMware para o Cofre dos serviços de recuperação.
**Máquinas replicadas** | O serviço de mobilidade está instalado em cada VM de VMware, replicar. | Recomendamos que permite que a instalação automática do servidor de processos. Em alternativa, pode instalar o serviço manualmente ou utilizar um método de implementação automatizada, como o System Center Configuration Manager.

**Arquitetura de VMware para o Azure**

![Componentes](./media/vmware-azure-architecture/arch-enhanced.png)



## <a name="replication-process"></a>Processo de replicação

1. Quando ativa a replicação para uma VM, começa a replicação inicial para o armazenamento do Azure, através da política de replicação especificada. Tenha em atenção o seguinte:
    - Para VMs de VMware, a replicação é o nível de bloco, quase contínua, utilizando o agente de serviço de mobilidade em execução na VM.
    - As definições de política de replicação são aplicadas:
        - **Limiar RPO**. Esta definição não afeta os replicação. Ajuda com a monitorização. É gerado um evento, e, opcionalmente, enviado um e-mail, se o atual RPO excede o limite de limiar que especificar.
        - **Retenção do ponto de recuperação**. Esta definição especifica a distância back no tempo no que qual pretende ir quando ocorre uma interrupção. Máximo de retenção no armazenamento premium é de 24 horas. No armazenamento standard é 72 horas. 
        - **Instantâneos consistentes com a aplicação**. Instantâneos consistentes com a aplicação pode demorar cada 1 a 12 horas, consoante as suas necessidades de aplicação. Os instantâneos são instantâneos de Blobs do Azure standard. O agente de mobilidade em execução numa VM solicita um instantâneo VSS em conformidade com esta definição e os indicadores de ponto de ponto no tempo como uma aplicação consistente no fluxo de replicação.

2. Tráfego replica para o armazenamento do Azure pontos finais públicos na Internet. Em alternativa, pode utilizar o ExpressRoute do Azure com [peering público](../expressroute/expressroute-circuit-peerings.md#publicpeering). Replicar o tráfego através de uma rede privada virtual (VPN) do site a site a partir de um site no local para o Azure não é suportada.
3. Depois de concluída a replicação inicial, começa a replicação das alterações delta para o Azure. Alterações registadas relativas a uma máquina são enviadas para o servidor de processos.
4. Comunicação acontece da seguinte forma:

    - As VMs comunicar com o servidor de configuração no local na porta HTTPS 443 entrada, para a gestão da replicação.
    - O servidor de configuração orquestra a replicação com o Azure através da porta HTTPS 443 de saída.
    - VMs enviam dados de replicação para o servidor de processos (em execução na máquina do servidor de configuração) na porta HTTPS 9443 de entrada. Esta porta pode ser modificada.
    - O servidor de processos recebe dados de replicação, otimiza e criptografa- e envia-os para o armazenamento do Azure através da porta 443 saída.




**VMware para o processo de replicação do Azure**

![Processo de replicação](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

Após a replicação está configurada e executar um teste de recuperação após desastre (ativação pós-falha de teste) para verificar se tudo o que está a funcionar conforme esperado, pode executar a ativação pós-falha e reativação pós-falha conforme pretender.

1. Executar ativação para uma única máquina, ou criar uma recuperação planos para efetuar a ativação pós-falha de várias VMs ao mesmo tempo. A vantagem de um plano de recuperação em vez de ativação pós-falha da máquina única incluem:
    - Pode modelar dependências de aplicações, incluindo todas as VMs em toda a aplicação no plano de recuperação simples.
    - Pode adicionar scripts, os runbooks do Azure e colocar em pausa para ações manuais.
2. Depois de acionar a ativação pós-falha inicial, confirmá-lo a começar a aceder à carga de trabalho da VM do Azure.
3. Quando o seu site no local primário estiver novamente disponível, pode se preparar para reativação pós-falha. Para efetuar a reativação pós-falha, terá de configurar uma infraestrutura de reativação pós-falha, incluindo:

    * **Servidor de processo temporário no Azure**: Para efetuar a ativação pós-falha do Azure, configurar uma VM do Azure para atuar como um servidor de processos para processar a replicação do Azure. É possível eliminar esta VM após a conclusão da reativação pós-falha.
    * **Ligação VPN**: Para efetuar a reativação pós-falha, terá uma ligação VPN (ou ExpressRoute) da rede do Azure para o site no local.
    * **Servidor de destino principal separado**: Por predefinição, o servidor de destino mestre foi instalado com o servidor de configuração do VM de VMware no local processa a reativação pós-falha. Se precisar de efetuar a ativação pós-falha grandes volumes de tráfego, configure um servidor de destino principal independente no local para esta finalidade.
    * **Política de reativação pós-falha**: Para replicar para o seu site no local, terá de uma política de reativação pós-falha. Esta política é criada automaticamente quando cria uma política de replicação no local para o Azure.
4. Depois dos componentes estão em vigor, a reativação pós-falha ocorre em três ações:

    - Fase 1: Voltar a proteger as VMs do Azure para que eles replicar a partir do Azure para as VMs do VMware no local.
    -  Fase 2: Execute uma ativação pós-falha para o site no local.
    - Fase 3: Depois de tem realizarão a reativação pós-falha cargas de trabalho, reativar a replicação para as VMs no local.
    
 
**Reativação pós-falha de VMware do Azure**

![Reativação pós-falha](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Passos Seguintes

Siga [deste tutorial](vmware-azure-tutorial.md) para ativar a VMware para replicação do Azure.
