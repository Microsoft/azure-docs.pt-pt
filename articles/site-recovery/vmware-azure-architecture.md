---
title: VMware para a arquitetura da replicação do Azure no Azure Site Recovery | Documentos da Microsoft
description: Este artigo fornece uma visão geral dos componentes e da arquitetura utilizada ao replicar VMs de VMware no local para o Azure com o Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: 498c41324bfc85f6f91acc8000df4c34856cf428
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2018
ms.locfileid: "44715759"
---
# <a name="vmware-to-azure-replication-architecture"></a>VMware para a arquitetura da replicação do Azure

Este artigo descreve a arquitetura e os processos utilizados ao replicar, efetuar a ativação pós-falha e recuperar máquinas virtuais de VMware (VMs) entre um site de VMware no local e o Azure, utilizando [do Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Componentes da arquitetura

A tabela seguinte e o gráfico fornecem uma visão geral dos componentes utilizados para a replicação de VMware para o Azure.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Uma subscrição do Azure, uma conta de armazenamento do Azure e uma rede do Azure. | Dados replicados de VMs no local são armazenados na conta de armazenamento. VMs do Azure são criadas com os dados replicados quando executar uma ativação pós-falha do local para o Azure. As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Máquina do servidor de configuração** | Uma única máquina no local. Recomendamos que executá-la como uma VM de VMware que podem ser implementadas a partir de um modelo do OVF transferido.<br/><br/> A máquina é executada todos os componentes do Site Recovery no local, que incluem o servidor de configuração, o servidor de processos e o servidor de destino mestre. | **Servidor de configuração**: coordena as comunicações entre no local e o Azure e gere a replicação de dados.<br/><br/> **Servidor de processos**: instalado por predefinição no servidor de configuração. Recebe dados de replicação. otimiza-os com colocação em cache, compressão e encriptação; e envia-os para o armazenamento do Azure. O servidor de processos também instala o serviço de mobilidade de recuperação de sites do Azure nas VMs que pretende replicar, e efetua a deteção automática de máquinas no local. À medida que cresça a implementação, pode adicionar servidores de processo adicionais, em separado para processar maiores volumes de tráfego de replicação.<br/><br/> **Servidor de destino mestre**: instalado por predefinição no servidor de configuração. Ele processa dados de replicação durante a reativação pós-falha do Azure. Para implementações maiores, pode adicionar um servidor de destino mestre adicionais, em separado para a reativação pós-falha.
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

2. Tráfego replica para o armazenamento do Azure pontos finais públicos na Internet. Em alternativa, pode utilizar o ExpressRoute do Azure com [peering público](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). Replicar o tráfego através de uma rede privada virtual (VPN) do site a site a partir de um site no local para o Azure não é suportada.
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

    * **Servidor de processo temporário no Azure**: para reativação pós-falha do Azure, configurar uma VM do Azure para atuar como um servidor de processos para processar a replicação do Azure. É possível eliminar esta VM após a conclusão da reativação pós-falha.
    * **Ligação VPN**: para efetuar a reativação pós-falha, terá de uma ligação de VPN (ou ExpressRoute) da rede do Azure para o site no local.
    * **Servidor de destino principal separado**: por predefinição, o servidor de destino mestre foi instalado com o servidor de configuração do VM de VMware no local processa a reativação pós-falha. Se precisar de efetuar a ativação pós-falha grandes volumes de tráfego, configure um servidor de destino principal independente no local para esta finalidade.
    * **Política de reativação pós-falha**: para replicar de novo para o site no local, precisa de uma política de reativação pós-falha. Esta política é criada automaticamente quando cria uma política de replicação no local para o Azure.
4. Depois dos componentes estão em vigor, a reativação pós-falha ocorre em três ações:

    - Fase 1: Voltar a proteger as VMs do Azure para que eles replicar a partir do Azure para as VMs do VMware no local.
    -  Fase 2: Executar uma ativação pós-falha para o site no local.
    - Fase 3: Depois de tem realizarão a reativação pós-falha cargas de trabalho, reativar a replicação para as VMs no local.
    
 
**Reativação pós-falha de VMware do Azure**

![Reativação pós-falha](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Passos Seguintes

Siga [deste tutorial](vmware-azure-tutorial.md) para ativar a VMware para replicação do Azure.
