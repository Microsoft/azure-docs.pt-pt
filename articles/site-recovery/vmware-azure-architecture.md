---
title: Arquitetura de recuperação de desastres VMware VM na recuperação do site azure
description: Este artigo fornece uma visão geral dos componentes e arquitetura utilizados na criação de recuperação de desastres de VMware VMware VMware no local para Azure com recuperação do site Azure
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 77b4dd4c0efbe6d03e64865f18c2c87614aaecb5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80632529"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>VMware para arquitetura de recuperação de desastres de Azure

Este artigo descreve a arquitetura e os processos utilizados quando implementa a replicação de recuperação de desastres, a falha e a recuperação de máquinas virtuais VMware (VMs) entre um site vMware no local e o Azure utilizando o serviço de recuperação do [site Azure.](site-recovery-overview.md)


## <a name="architectural-components"></a>Componentes da arquitetura

A tabela e o gráfico que se aseguir proporcionam uma visão de alto nível dos componentes utilizados para a recuperação de desastres vMware para o Azure.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Uma subscrição Azure, conta de Armazenamento Azure para cache, Disque Gerível e rede Azure. | Os dados replicados a partir de VMs no local são armazenados no armazenamento azure. Os VMs azure são criados com os dados replicados quando executa uma falha no local para o Azure. As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Máquina de servidor de configuração** | Uma única máquina no local. Recomendamos que o execute como vMware VM que pode ser implantado a partir de um modelo OVF descarregado.<br/><br/> A máquina executa todos os componentes de recuperação do site no local, que incluem o servidor de configuração, servidor de processos e servidor de alvo principal. | Servidor de **configuração**: Coordena as comunicações entre as instalações e o Azure e gere a replicação de dados.<br/><br/> **Servidor de processos**: Instalado por defeito no servidor de configuração. Recebe dados de replicação; otimiza-o com cache, compressão e encriptação; e envia-o para o Armazém Azure. O servidor de processo também instala o Azure Site Recovery Mobility Service nas VMs que pretende replicar e realiza a descoberta automática das máquinas no local. À medida que a sua implementação cresce, pode adicionar servidores de processo seletos adicionais para lidar com volumes maiores de tráfego de replicação.<br/><br/> **Servidor de alvo principal**: Instalado por predefinição no servidor de configuração. Trata os dados de replicação durante o recuo do Azure. Para grandes implementações, pode adicionar um servidor de alvo principal adicional e separado para o failback.
**Servidores de VMware** | VMware VMs estão hospedados em servidores vSphere ESXi no local. Recomendamos um servidor vCenter para gerir os anfitriões. | Durante a implementação da Recuperação do Site, adicione servidores VMware ao cofre dos Serviços de Recuperação.
**Máquinas replicadas** | O Serviço de Mobilidade está instalado em cada VMware VM que você replica. | Recomendamos que permita a instalação automática a partir do servidor de processos. Em alternativa, pode instalar o serviço manualmente ou utilizar um método de implementação automatizado, como o 'Gestor de Configuração'.

**Arquitetura de VMware para o Azure**

![Componentes](./media/vmware-azure-architecture/arch-enhanced.png)


## <a name="replication-process"></a>Processo de replicação

1. Quando ativa a replicação de um VM, começa a replicação inicial ao armazenamento Azure, utilizando a política de replicação especificada. Tenha em atenção o seguinte:
    - Para VMware VMs, a replicação é de nível de bloco, quase contínua, utilizando o agente de serviço de Mobilidade em execução no VM.
    - São aplicadas quaisquer definições de política de replicação:
        - **Limiar de RPO.** Esta definição não afeta a replicação. Ajuda na monitorização. Um evento é levantado, e opcionalmente um e-mail enviado, se o RPO atual exceder o limite de limiar que especifica.
        - **Retenção do ponto de recuperação.** Esta definição especifica até onde quer ir no tempo quando ocorre uma perturbação. A retenção máxima no armazenamento premium é de 24 horas. No armazenamento normal são 72 horas. 
        - **Fotos consistentes com aplicativos.** O instantâneo consistente com aplicações pode ser tirado a cada 1 a 12 horas, dependendo das necessidades da sua aplicação. As fotos são imagens padrão de bolhas Azure. O agente de mobilidade que executa um VM solicita uma imagem instantânea VSS de acordo com este cenário, e marca esse ponto-a-tempo como um ponto consistente de aplicação no fluxo de replicação.

2. O tráfego replica-se para os pontos finais do armazenamento Azure através da internet. Alternadamente, pode utilizar o Azure ExpressRoute com [o peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)da Microsoft . A replicação do tráfego através de uma rede privada virtual (VPN) de um site no local para Azure não é suportada.
3. O funcionamento inicial da replicação garante que todos os dados da máquina no momento da replicação ativa são enviados para o Azure. Após os acabamentos iniciais da replicação, começa a replicação das alterações delta para Azure. As alterações rastreadas para uma máquina são enviadas para o servidor de processos.
4. A comunicação acontece da seguinte forma:

    - Os VMs comunicam com o servidor de configuração no local na entrada da porta HTTPS 443, para gestão de replicação.
    - O servidor de configuração orquestra a replicação com azure sobre a saída da porta HTTPS 443.
    - Os VMs enviam dados de replicação para o servidor de processo (funcionando na máquina do servidor de configuração) na entrada da porta HTTPS 9443. Esta porta pode ser modificada.
    - O servidor de processos recebe dados de replicação, otimiza e encripta-os, e envia-os para o armazenamento Azure sobre a saída da porta 443.
5. Os dados de replicação registam a primeira terra numa conta de armazenamento de cache em Azure. Estes registos são processados e os dados são armazenados num Disco Gerido Azure (chamado de disco de sementes asr). Os pontos de recuperação são criados neste disco.

**Processo de replicação de VMware para Azure**

![Processo de replicação](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="resynchronization-process"></a>Processo de ressincronização

1. Por vezes, durante a replicação inicial ou durante a transferência de alterações delta, pode haver problemas de conectividade de rede entre a máquina de origem para processar o servidor ou entre o servidor de processos para o Azure. Qualquer uma delas pode levar a falhas na transferência de dados para o Azure momentaneamente.
2. Para evitar problemas de integridade de dados e minimizar os custos de transferência de dados, a Recuperação do Site marca uma máquina para ressincronização.
3. Uma máquina também pode ser marcada para ressincronização em situações como seguir para manter a consistência entre a máquina de origem e os dados armazenados em Azure
    - Se uma máquina for submetida a uma desativação forçada
    - Se uma máquina sofrer alterações configuracionais como o redimensionamento do disco (modificando o tamanho do disco de 2 TB a 4 TB)
4. A ressincronização envia apenas dados delta para o Azure. Transferência de dados entre as instalações e o Azure por minimização por verificação de dados entre a máquina-fonte e os dados armazenados no Azure.
5. Por padrão, a ressincronização está programada para ser executada automaticamente fora do horário de expediente. Se não quiser esperar pela ressincronização predefinida fora de horas, pode ressincronizar manualmente um VM. Para isso, vá ao portal Azure, selecione o VM > **Ressincronizar.**
6. Se a ressincronização por defeito falhar fora do horário de expediente e for necessária uma intervenção manual, então um erro é gerado na máquina específica no portal Azure. Pode resolver o erro e desencadear a ressincronização manualmente.
7. Após a conclusão da ressincronização, a replicação das alterações delta será retomada.

## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

Depois da replicação ser configurada e executar um exercício de recuperação de desastres (falha de teste) para verificar se está tudo a funcionar como esperado, pode correr failover e failback como necessário.

1. Executa falha para uma única máquina, ou cria planos de recuperação para falhar em vários VMs ao mesmo tempo. A vantagem de um plano de recuperação em vez de uma única falha na máquina incluem:
    - Você pode modelar dependências de aplicativos, incluindo todos os VMs em toda a app em um único plano de recuperação.
    - Pode adicionar scripts, livros de execução Azure e fazer uma pausa para ações manuais.
2. Depois de desencadear a falha inicial, compromete-a a começar a aceder à carga de trabalho do VM Azure.
3. Quando o seu site principal no local estiver novamente disponível, pode preparar-se para voltar a falhar. Para falhar, é necessário criar uma infraestrutura de retrocesso, incluindo:

    * Servidor de **processo temporário em Azure**: Para falhar a partir do Azure, configura um VM Azure para funcionar como um servidor de processo para lidar com a replicação do Azure. É possível eliminar esta VM após a conclusão da reativação pós-falha.
    * **Ligação VPN**: Para falhar, necessita de uma ligação VPN (ou ExpressRoute) da rede Azure para o local no local.
    * **Servidor**de alvo principal separado : Por padrão, o servidor alvo principal que foi instalado com o servidor de configuração no VMware VM no local manipula falha. Se precisar de reativar grandes volumes de tráfego, instale um servidor-alvo principal no local separado para o efeito.
    * **Política de reativação pós-falha**: para replicar de novo para o site no local, precisa de uma política de reativação pós-falha. Esta política é criada automaticamente quando se cria uma política de replicação a partir do local para o Azure.
4. Após a colocação dos componentes, o failback ocorre em três ações:

    - Fase 1: Reproteja os VMs Azure de modo a que se reproduzam de Azure de volta aos VMs no local.
    -  Fase 2: Executar uma falha no local.
    - Fase 3: Depois de as cargas de trabalho terem falhado, reativa a replicação para os VMs no local.
    
 
**VMware falha regresso do Azure**

![Reativação pós-falha](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Passos seguintes

Siga [este tutorial](vmware-azure-tutorial.md) para permitir a replicação do VMware para o Azure.
