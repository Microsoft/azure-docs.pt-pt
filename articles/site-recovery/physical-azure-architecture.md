---
title: Arquitetura de recuperação de desastres de servidor físico em Recuperação do Site Azure
description: Este artigo fornece uma visão geral dos componentes e arquitetura utilizados durante a recuperação de desastres de servidores físicos no local para Azure com o serviço de recuperação do site Azure.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 089d981284986a2b6eb0ee7f1dbd401fc7ce4fcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162842"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Arquitetura da recuperação após desastre de servidores físicos para o Azure

Este artigo descreve a arquitetura e os processos utilizados quando se replica, falha e recupera servidores físicos windows e linux entre um site no local e o Azure, utilizando o serviço de Recuperação do [Site Azure.](site-recovery-overview.md)

## <a name="architectural-components"></a>Componentes da arquitetura

A tabela e o gráfico que se aseguir proporcionam uma visão de alto nível dos componentes utilizados para a replicação física do servidor ao Azure.

| **Componente** | **Requisito** | **Detalhes** |
| --- | --- | --- |
| **Azure** | Uma subscrição Azure e uma rede Azure. | Os dados replicados de máquinas físicas no local são armazenados em discos geridos pelo Azure. Os VMs azure são criados com os dados replicados quando executa uma falha no local para o Azure. As VMs do Azure ligam-se à rede virtual do Azure quando são criadas. |
| **Servidor de processos** | Instalado por padrão juntamente com o servidor de configuração. | Atua como um gateway de replicação. Recebe dados de replicação, otimiza-os com a colocação em cache, compressão e encriptação, e envia-os para o armazenamento do Azure.<br/><br/> O servidor de processos também instala o serviço mobility nos servidores que pretende replicar.<br/><br/> À medida que a sua implementação cresce, pode adicionar servidores de processo seletos adicionais para lidar com volumes maiores de tráfego de replicação. |
| **Servidor de destino mestre** | Instalado por padrão juntamente com o servidor de configuração. | Trata dos dados de replicação durante a falha de volta do Azure.<br/><br/> Para grandes implementações, pode adicionar um servidor de alvo principal adicional e separado para o failback. |
| **Servidores replicados** | O serviço de Mobilidade está instalado em cada servidor que replicar. | Recomendamos que permita a instalação automática a partir do servidor de processos. Ou pode instalar o serviço manualmente ou utilizar um método de implementação automatizado, como o 'Gestor de Configuração'. |

**Arquitetura de replicação física para o Azure**

![Componentes](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Processo de replicação

1. Configuraa a implantação, incluindo os componentes no local e o Azure. No cofre dos Serviços de Recuperação, especifica a fonte de replicação e o alvo, configura o servidor de configuração, cria uma política de replicação e ativa a replicação.
1. As máquinas replicam-se utilizando a política de replicação, e uma cópia inicial dos dados do servidor é replicada para o armazenamento do Azure.
1. Após os acabamentos iniciais da replicação, começa a replicação das alterações delta para Azure. As alterações rastreadas para uma máquina são mantidas num ficheiro com a extensão _.hrl._
   - As máquinas comunicam com o servidor de configuração na entrada da porta HTTPS 443, para gestão de replicação.
   - As máquinas enviam dados de replicação para o servidor de processo na entrada da porta HTTPS 9443 (pode ser modificada).
   - O servidor de configuração orquestra a gestão da replicação com o Azure sobre a saída da porta HTTPS 443.
   - O servidor de processos recebe dados de máquinas de origem, otimiza e encripta-os, e envia-os para o armazenamento Azure sobre a saída da porta HTTPS 443.
   - Se ativar a consistência multi-VM, as máquinas no grupo de replicação comunicam entre si pela porta 20004. A consistência multi-VM é utilizada se agrupar várias máquinas em grupos de replicação que partilhem pontos de consistência de falhas e pontos de consistência de aplicação quando é efetuada a ativação pós-falha dos mesmos. Estes grupos são úteis se as máquinas estiverem a funcionar com a mesma carga de trabalho e precisam de ser consistentes.
1. O tráfego é replicado para pontos finais públicos do armazenamento do Azure, através da Internet. Em alternativa, pode utilizar o [peering público](../expressroute/about-public-peering.md) do Azure ExpressRoute.

   > [!NOTE]
   > A replicação não é suportada sobre uma VPN site-to-site a partir de um site no local ou [peering privado](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute)Azure ExpressRoute .

**Processo de replicação física para Azure**

![Processo de replicação](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

Após a replicação ser configurada, você pode executar um exercício de recuperação de desastres (falha de teste) para verificar se tudo funciona como esperado. Depois, podes falhar e falhar quando necessário. Considere os seguintes itens:

- A ativação pós-falha planeada não é suportada.
- Não volte a um VMware VM no local. Precisa de uma infraestrutura VMware no local, mesmo quando replica servidores físicos no local para o Azure.
- Falha-se numa única máquina, ou cria planos de recuperação, para falhar juntos em várias máquinas.
- Quando executa uma falha, os VMs Azure são criados a partir de dados replicados no armazenamento Azure.
- Após o failover inicial ser desencadeado, compromete-o a começar a aceder à carga de trabalho do VM Azure.
- Quando o site no local primário estiver novamente disponível, pode fazer a reativação pós-falha.
- Criar uma infraestrutura de failback que inclua:
  - Servidor de **processo temporário em Azure**: Para falhar a partir do Azure, configura um VM Azure para funcionar como um servidor de processo, para lidar com a replicação do Azure. Pode eliminar este VM depois de terminar a falha.
  - **Ligação VPN**: Para falhar, necessita de uma ligação VPN (ou Azure ExpressRoute) da rede Azure para o local no local.
  - **Servidor**de alvo principal separado : Por padrão, a parte de trás da falha é manuseada pelo servidor alvo principal que foi instalado com o servidor de configuração no VMware no local. Se precisar de repor grandes volumes de tráfego, deverá configurar um servidor-alvo principal no local separado.
  - **Política de reativação pós-falha**: para replicar de novo para o site no local, precisa de uma política de reativação pós-falha. A política foi criada automaticamente quando criou a sua política de replicação a partir do local para o Azure.
  - **Infraestrutura VMware**: Para falhar, precisa de uma infraestrutura VMware. Não pode efetuar a reativação pós-falha num servidor físico.
- Depois de os componentes estarem no lugar, a falha ocorre em três fases:
  - **Fase 1**: Reproteger os VMs Azure de modo a que se reproduzam de Azure de volta aos VMs no local.
  - **Fase 2**: Executar uma falha no local.
  - **Fase 3**: Depois de as cargas de trabalho terem falhado, reativa-se a replicação.

**VMware falha regresso do Azure**

![Reativação pós-falha](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>Passos seguintes

Para configurar a recuperação de desastres para servidores físicos para o Azure, consulte o [guia como guiar](physical-azure-disaster-recovery.md).
