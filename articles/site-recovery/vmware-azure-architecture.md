---
title: VMware VM arquitetura de recuperação de desastres na Recuperação do Local de Azure
description: Este artigo fornece uma visão geral dos componentes e arquitetura utilizados na criação de recuperação de desastres de VMware VMs para Azure com Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 4b1b8a0cfa98d48d7cb92474c1572f17c79ffd0d
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498957"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>VMware para Azure arquitetura de recuperação de desastres

Este artigo descreve a arquitetura e os processos utilizados quando implementa a replicação, falha e recuperação de máquinas virtuais VMware (VMs) entre um site VMware no local e Azure utilizando o serviço de Recuperação do [Local Azure.](site-recovery-overview.md)


## <a name="architectural-components"></a>Componentes da arquitetura

A tabela e gráficos seguintes proporcionam uma visão de alto nível dos componentes utilizados para a recuperação de desastres VMware para Azure.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Azure** | Uma subscrição Azure, conta de Armazenamento Azure para cache, Disco Gerido e Rede Azure. | Os dados replicados a partir de VMs no local são armazenados no armazenamento do Azure. Os VMs Azure são criados com os dados replicados quando você executou uma falha de falha de no local para Azure. As VMs do Azure ligam-se à rede virtual do Azure quando são criadas.
**Máquina de servidor de configuração** | Uma única máquina no local. Recomendamos que o execute como um VMware VMware que pode ser implantado a partir de um modelo OVF descarregado.<br/><br/> A máquina executa todos os componentes de recuperação do local, que incluem o servidor de configuração, o servidor de processo e o servidor alvo principal. | **Servidor de configuração**: Coordena as comunicações entre as instalações e o Azure e gere a replicação de dados.<br/><br/> **Servidor de processo**: Instalado por predefinição no servidor de configuração. Recebe dados de replicação; otimiza-o com caching, compressão e encriptação; e envia-o para o Azure Storage. O servidor de processo também instala o Azure Site Recovery Mobility Service nas VMs que pretende replicar e realiza a descoberta automática das máquinas no local. À medida que a sua implementação cresce, pode adicionar servidores de processos adicionais e separados para lidar com volumes maiores de tráfego de replicação.<br/><br/> **Servidor alvo principal**: Instalado por predefinição no servidor de configuração. Trata os dados de replicação durante o failback do Azure. Para grandes implementações, pode adicionar um servidor de alvo principal adicional e separado para falha.
**Servidores de VMware** | VMware VMs estão hospedados em servidores vSphere ESXi. Recomendamos um servidor vCenter para gerir os anfitriões. | Durante a implementação da Recuperação do Local, adiciona servidores VMware ao cofre dos Serviços de Recuperação.
**Máquinas replicadas** | O Serviço de Mobilidade está instalado em cada VMware VM que replica. | Recomendamos que permita a instalação automática a partir do servidor de processos. Em alternativa, pode instalar o serviço manualmente ou utilizar um método de implementação automatizado, como o Gestor de Configurações.

![Diagrama mostrando VMware para relacionamentos de arquitetura de replicação de Azure.](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>Configurar a conectividade da rede de saída

Para que a Recuperação do Site funcione como esperado, é necessário modificar a conectividade da rede de saída para permitir que o seu ambiente se reproduza.

> [!NOTE]
> A Recuperação do Site não suporta a utilização de um representante de autenticação para controlar a conectividade da rede.

### <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Se estiver a usar um proxy de firewall baseado em URL para controlar a conectividade de saída, permita o acesso a estes URLs:

| **Nome**                  | **Comercial**                               | **Administração Pública**                                 | **Descrição** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Armazenamento                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | Permite que os dados sejam escritos da VM para a conta de armazenamento em cache na região de origem. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Fornece autorização e autenticação para os URLs do serviço Site Recovery. |
| Replicação               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Permite que a VM comunique com o serviço Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Permite que a VM escreva dados de monitorização e diagnóstico do Site Recovery. |

## <a name="replication-process"></a>Processo de replicação

1. Quando ativa a replicação de um VM, começa a replicação inicial do armazenamento do Azure, utilizando a política de replicação especificada. Tenha em atenção o seguinte:
    - Para VMware VMs, a replicação é de nível de bloco, quase contínuo, utilizando o agente de serviço de mobilidade em execução no VM.
    - Quaisquer definições de política de replicação são aplicadas:
        - **Limiar de RPO**. Esta definição não afeta a replicação. Ajuda na monitorização. Um evento é levantado, e opcionalmente um e-mail enviado, se o RPO atual exceder o limite de limiar que você especifica.
        - **Retenção de ponto de recuperação**. Esta definição especifica até onde deve ir quando ocorre uma interrupção. A retenção máxima no armazenamento premium é de 24 horas. No armazém padrão são 72 horas. 
        - **Instantâneos consistentes com aplicações.** O instantâneo consistente da aplicação pode ser tirado a cada 1 a 12 horas, dependendo das necessidades da sua aplicação. As fotos são instantâneos de bolhas Azure padrão. O agente de mobilidade em execução num VM solicita uma imagem VSS de acordo com esta configuração, e os marcadores apontam no tempo como um ponto consistente de aplicação no fluxo de replicação.

2. O tráfego replica-se para os pontos finais públicos de armazenamento Azure através da internet. Alternadamente, pode utilizar o Azure ExpressRoute com [a Microsoft a espreitar](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). A replicação do tráfego sobre uma rede privada virtual local (VPN) de um site no local para Azure não é suportado.
3. A operação inicial de replicação garante que todos os dados da máquina no momento da replicação de ativação são enviados para a Azure. Após o final da replicação inicial, começa a replicação das alterações delta para Azure. As alterações rastreadas para uma máquina são enviadas para o servidor de processo.
4. A comunicação acontece da seguinte forma:

    - Os VMs comunicam com o servidor de configuração no local na entrada HTTPS 443, para gestão de replicação.
    - O servidor de configuração orquestra a replicação com o Azure sobre a saída https 443.
    - Os VMs enviam dados de replicação para o servidor de processo (em execução na máquina do servidor de configuração) na entrada HTTPS 9443. Esta porta pode ser modificada.
    - O servidor de processo recebe dados de replicação, otimiza e encripta-os e envia-os para o armazenamento Azure sobre a porta 443 de saída.
5. Os registos de dados de replicação aterram pela primeira vez numa conta de armazenamento de cache em Azure. Estes registos são processados e os dados são armazenados num Disco Gerido Azure (chamado de disco de sementes asr). Os pontos de recuperação são criados neste disco.

![Diagrama mostrando o VMware ao processo de replicação do Azure.](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="resynchronization-process"></a>Processo de ressincronização

1. Por vezes, durante a replicação inicial ou durante a transferência de alterações delta, pode haver problemas de conectividade de rede entre a máquina de origem para processar o servidor ou entre o servidor de processo para o Azure. Qualquer uma delas pode levar a falhas na transferência de dados para o Azure momentaneamente.
2. Para evitar problemas de integridade dos dados e minimizar os custos de transferência de dados, a Recuperação do Site marca uma máquina para a ressincronização.
3. Uma máquina também pode ser marcada para ressincronização em situações como seguir para manter a consistência entre a máquina de origem e os dados armazenados em Azure
    - Se uma máquina sofre de paragem forçada
    - Se uma máquina sofrer alterações de configuração como o redimensionamento do disco (modificando o tamanho do disco de 2 TB a 4 TB)
4. A ressincronização envia apenas dados delta para a Azure. Transferência de dados entre as instalações e a Azure por minimização através da computação de dados entre a máquina de origem e os dados armazenados em Azure.
5. Por padrão, a ressincronização está programada para funcionar automaticamente fora do horário de expediente. Se não quiser esperar pela ressincronização padrão fora de horas, pode ressincronizar manualmente um VM. Para isso, vá ao portal Azure, selecione o VM > **Resynchronize**.
6. Se a ressincronização predefinida falhar fora do horário de expediente e for necessária uma intervenção manual, então é gerado um erro na máquina específica no portal Azure. Pode resolver o erro e ativar a ressincronização manualmente.
7. Após a conclusão da ressincronização, a replicação das alterações delta será retomada.

## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

Depois de configurar a replicação e executar um exercício de recuperação de desastres (teste failover) para verificar se está tudo a funcionar como esperado, pode correr por defeito e falhar como necessário.

1. Você corre falha para uma única máquina, ou cria um plano de recuperação para falhar em vários VMs ao mesmo tempo. A vantagem de um plano de recuperação em vez de uma única falha na máquina inclui:
    - Pode modelar as dependências de aplicações, incluindo todos os VMs em toda a aplicação num único plano de recuperação.
    - Pode adicionar scripts, runbooks Azure e fazer uma pausa para ações manuais.
2. Depois de desencadear o failover inicial, compromete-o a começar a aceder à carga de trabalho a partir do Azure VM.
3. Quando o seu site principal no local estiver novamente disponível, pode preparar-se para o fail back. Para falhar, é necessário criar uma infraestrutura de recuo, incluindo:

    * **Servidor de processo temporário em Azure**: Para falhar de volta do Azure, configura um Azure VM para agir como um servidor de processo para lidar com a replicação do Azure. É possível eliminar esta VM após a conclusão da reativação pós-falha.
    * **Ligação VPN**: Para falhar, precisa de uma ligação VPN (ou ExpressRoute) da rede Azure para o local do local.
    * **Servidor-alvo principal separado**: Por padrão, o servidor alvo principal que foi instalado com o servidor de configuração nas pegas VMware VMware no local falha. Se precisar de falhar em grandes volumes de tráfego, crie um servidor-alvo principal separado no local para este fim.
    * **Política de reativação pós-falha**: para replicar de novo para o site no local, precisa de uma política de reativação pós-falha. Esta política é criada automaticamente quando se cria uma política de replicação desde as instalações até ao Azure.
4. Após a colocação dos componentes, o recuo ocorre em três ações:

    - Fase 1: Reprotete os VMs Azure para que se reproduzam de Azure de volta para os VMS VMware no local.
    -  Fase 2: Executar um failover para o local do local.
    - Fase 3: Depois de as cargas de trabalho terem falhado, é possível reenerte a replicação para os VMs no local.
    
 

![Diagrama mostrando vMware failback de Azure.](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Passos seguintes

Siga [este tutorial](vmware-azure-tutorial.md) para permitir a replicação do VMware para a Azure.
