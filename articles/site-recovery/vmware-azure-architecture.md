---
title: Arquitetura de recuperação de desastre de VM VMware no Azure Site Recovery
description: Este artigo fornece uma visão geral dos componentes e da arquitetura usada ao configurar a recuperação de desastre de VMs VMware locais para o Azure com o Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 8bfbc6783df4f902d25b2a4791708990a327edc8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663068"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>Arquitetura de recuperação de desastre do VMware para o Azure

Este artigo descreve a arquitetura e os processos usados quando você implanta a replicação de recuperação de desastre, o failover e a recuperação de VMs (máquinas virtuais) VMware entre um site VMware local e o Azure usando o serviço [Azure site Recovery](site-recovery-overview.md) .


## <a name="architectural-components"></a>Componentes da arquitetura

A tabela e o gráfico a seguir fornecem uma exibição de alto nível dos componentes usados para a recuperação de desastres do VMware no Azure.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Uma assinatura do Azure, uma conta de armazenamento do Azure para cache, disco gerenciado e rede do Azure. | Os dados replicados de VMs locais são armazenados no armazenamento do Azure. As VMs do Azure são criadas com os dados replicados quando você executa um failover do local para o Azure. As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Computador do servidor de configuração** | Um único computador local. Recomendamos que você o execute como uma VM VMware que pode ser implantada de um modelo OVF baixado.<br/><br/> O computador executa todos os componentes de Site Recovery locais, que incluem o servidor de configuração, o servidor de processo e o servidor de destino mestre. | **Servidor de configuração**: coordena as comunicações entre o local e o Azure e gerencia a replicação de dados.<br/><br/> **Servidor de processo**: instalado por padrão no servidor de configuração. Ele recebe dados de replicação; otimiza-o com caching, compactação e criptografia; e o envia para o armazenamento do Azure. O servidor de processo também instala o Azure Site Recovery Mobility Service nas VMs que pretende replicar e realiza a descoberta automática das máquinas no local. À medida que sua implantação cresce, você pode adicionar servidores de processo adicionais e separados para lidar com volumes maiores de tráfego de replicação.<br/><br/> **Servidor de destino mestre**: instalado por padrão no servidor de configuração. Ele lida com os dados de replicação durante o failback do Azure. Para grandes implantações, você pode adicionar um servidor de destino mestre adicional e separado para failback.
**Servidores de VMware** | As VMs do VMware são hospedadas em servidores vSphere ESXi locais. Recomendamos um servidor vCenter para gerenciar os hosts. | Durante a implantação de Site Recovery, você adiciona servidores VMware ao cofre dos serviços de recuperação.
**Máquinas replicadas** | O serviço de mobilidade é instalado em cada VM VMware que você replica. | Recomendamos que você permita a instalação automática do servidor de processo. Como alternativa, você pode instalar o serviço manualmente ou usar um método de implantação automatizado, como System Center Configuration Manager.

**Arquitetura de VMware para o Azure**

![Componentes](./media/vmware-azure-architecture/arch-enhanced.png)



## <a name="replication-process"></a>Processo de replicação

1. Quando você habilita a replicação para uma VM, a replicação inicial para o armazenamento do Azure começa, usando a política de replicação especificada. Tenha em atenção o seguinte:
    - Para VMs VMware, a replicação é de nível de bloco, quase contínua, usando o agente de serviço de mobilidade em execução na VM.
    - Todas as configurações de política de replicação são aplicadas:
        - **Limite de RPO**. Essa configuração não afeta a replicação. Ele ajuda com o monitoramento. Um evento é gerado e, opcionalmente, um email enviado, se o RPO atual exceder o limite especificado.
        - **Retenção do ponto de recuperação**. Essa configuração especifica quanto tempo você deseja ir quando ocorrer uma interrupção. A retenção máxima no armazenamento Premium é de 24 horas. No armazenamento Standard, são 72 horas. 
        - **Instantâneos consistentes com o aplicativo**. O instantâneo consistente com o aplicativo pode levar a cada 1 a 12 horas, dependendo das necessidades do seu aplicativo. Os instantâneos são instantâneos de blob do Azure padrão. O agente de mobilidade em execução em uma VM solicita um instantâneo do VSS de acordo com essa configuração e indica o ponto no tempo como um ponto consistente do aplicativo no fluxo de replicação.

2. O tráfego é replicado para pontos de extremidade públicos do armazenamento do Azure pela Internet. Como alternativa, você pode usar o Azure ExpressRoute com [o emparelhamento da Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Não há suporte para a replicação de tráfego em uma VPN (rede virtual privada) site a site de um site local para o Azure.
3. Após a conclusão da replicação inicial, a replicação de alterações delta para o Azure é iniciada. As alterações controladas para um computador são enviadas para o servidor de processo.
4. A comunicação acontece da seguinte maneira:

    - As VMs se comunicam com o servidor de configuração local na porta HTTPS 443 de entrada para o gerenciamento de replicação.
    - O servidor de configuração orquestra a replicação com o Azure sobre a porta HTTPS 443 de saída.
    - As VMs enviam dados de replicação para o servidor de processo (em execução no computador do servidor de configuração) na porta HTTPS 9443 de entrada. Essa porta pode ser modificada.
    - O servidor de processo recebe dados de replicação, otimiza-os e criptografa-os e envia-os para o armazenamento do Azure pela porta 443 de saída.
5. Os logs de dados de replicação primeiro se esterram em uma conta de armazenamento de cache no Azure. Esses logs são processados e os dados são armazenados em um disco gerenciado do Azure (chamado de disco de semente ASR). Os pontos de recuperação são criados neste disco.




**Processo de replicação do VMware para o Azure**

![Processo de replicação](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

Depois que a replicação é configurada e você executa uma análise de recuperação de desastre (failover de teste) para verificar se tudo está funcionando conforme o esperado, você pode executar o failover e o failback conforme necessário.

1. Você executa uma falha em um único computador ou cria um plano de recuperação para fazer failover de várias VMs ao mesmo tempo. A vantagem de um plano de recuperação em vez de um failover de máquina única inclui:
    - Você pode modelar dependências de aplicativo incluindo todas as VMs em todo o aplicativo em um único plano de recuperação.
    - Você pode adicionar scripts, runbooks do Azure e pausar para ações manuais.
2. Depois de disparar o failover inicial, você o confirma para começar a acessar a carga de trabalho da VM do Azure.
3. Quando o site primário local estiver disponível novamente, você poderá se preparar para realizar o failback. Para fazer failback, você precisa configurar uma infraestrutura de failback, incluindo:

    * **Servidor de processo temporário no Azure**: para fazer failback do Azure, você configura uma VM do Azure para atuar como um servidor de processo para lidar com a replicação do Azure. É possível eliminar esta VM após a conclusão da reativação pós-falha.
    * **Conexão VPN**: para fazer failback, você precisa de uma conexão VPN (ou ExpressRoute) da rede do Azure para o site local.
    * **Servidor de destino mestre separado**: por padrão, o servidor de destino mestre que foi instalado com o servidor de configuração na VM do VMware local lida com o failback. Se você precisar realizar o failback de grandes volumes de tráfego, configure um servidor de destino mestre local separado para essa finalidade.
    * **Política de reativação pós-falha**: para replicar de novo para o site no local, precisa de uma política de reativação pós-falha. Essa política é criada automaticamente quando você cria uma política de replicação do local para o Azure.
4. Depois que os componentes estiverem em vigor, o failback ocorrerá em três ações:

    - Estágio 1: proteger novamente as VMs do Azure para que elas sejam replicadas do Azure de volta para as VMs do VMware locais.
    -  Estágio 2: executar um failover para o site local.
    - Estágio 3: após o failback das cargas de trabalho, você reabilita a replicação para as VMs locais.
    
 
**Failback do VMware do Azure**

![Reativação pós-falha](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Passos seguintes

Siga [este tutorial](vmware-azure-tutorial.md) para habilitar a replicação do VMware para o Azure.
