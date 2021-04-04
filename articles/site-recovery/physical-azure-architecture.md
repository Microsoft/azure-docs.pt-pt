---
title: Arquitetura de recuperação de desastres de servidor físico na Recuperação do Local de Azure
description: Este artigo fornece uma visão geral dos componentes e arquitetura usados durante a recuperação de desastres de servidores físicos no local para Azure com o serviço de Recuperação do Local Azure.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 966636e269043d81912b552711635f34b412f22f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654732"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Arquitetura da recuperação após desastre de servidores físicos para o Azure

Este artigo descreve a arquitetura e os processos utilizados quando replica, falha e recupera servidores físicos do Windows e do Linux entre um site no local e a Azure, utilizando o serviço de Recuperação do [Local Azure.](site-recovery-overview.md)

## <a name="architectural-components"></a>Componentes da arquitetura

A tabela e gráfico seguintes fornecem uma visão de alto nível dos componentes utilizados para a replicação do servidor físico para OZure.

| **Componente** | **Requisito** | **Detalhes** |
| --- | --- | --- |
| **Azure** | Uma subscrição da Azure e uma rede Azure. | Os dados replicados das máquinas físicas no local são armazenados em discos geridos azure. Os VMs Azure são criados com os dados replicados quando você executou uma falha de falha de no local para Azure. As VMs do Azure ligam-se à rede virtual do Azure quando são criadas. |
| **Servidor de processo** | Instalado por predefinição juntamente com o servidor de configuração. | Atua como um gateway de replicação. Recebe dados de replicação, otimiza-os com a colocação em cache, compressão e encriptação, e envia-os para o armazenamento do Azure.<br/><br/> O servidor de processo também instala o serviço Mobility nos servidores que pretende replicar.<br/><br/> À medida que a sua implementação cresce, pode adicionar servidores de processos adicionais e separados para lidar com volumes maiores de tráfego de replicação. |
| **Servidor de destino mestre** | Instalado por predefinição juntamente com o servidor de configuração. | Lida com dados de replicação durante a falha de volta do Azure.<br/><br/> Para grandes implementações, pode adicionar um servidor de alvo principal adicional e separado para falha. |
| **Servidores replicados** | O serviço Mobility está instalado em cada servidor que replicar. | Recomendamos que permita a instalação automática a partir do servidor de processos. Ou pode instalar o serviço manualmente ou utilizar um método de implementação automatizado, como o Gestor de Configurações. |

**Arquitetura de replicação física para o Azure**

![Componentes](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>Configurar a conectividade da rede de saída

Para que a Recuperação do Site funcione como esperado, é necessário modificar a conectividade da rede de saída para permitir que o seu ambiente se reproduza.

> [!NOTE]
> A Recuperação do Site não suporta a utilização de um representante de autenticação para controlar a conectividade da rede.

### <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Se estiver a usar um proxy de firewall baseado em URL para controlar a conectividade de saída, permita o acesso a estes URLs:

| **Nome**                  | **Comercial**                               | **Administração Pública**                                 | **Descrição** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Armazenamento                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Permite que os dados sejam escritos da VM para a conta de armazenamento em cache na região de origem. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Fornece autorização e autenticação para os URLs do serviço Site Recovery. |
| Replicação               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Permite que a VM comunique com o serviço Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Permite que a VM escreva dados de monitorização e diagnóstico do Site Recovery. |


## <a name="replication-process"></a>Processo de replicação

1. Configura a implantação, incluindo componentes no local e Azure. No cofre dos Serviços de Recuperação, especifica a fonte de replicação e o alvo, configura o servidor de configuração, cria uma política de replicação e permite a replicação.
1. As máquinas replicam-se utilizando a política de replicação, e uma cópia inicial dos dados do servidor é replicada para o armazenamento do Azure.
1. Após o final da replicação inicial, começa a replicação das alterações delta para Azure. As alterações rastreadas para uma máquina são mantidas num ficheiro com a extensão _.hrl._
   - As máquinas comunicam com o servidor de configuração na porta HTTPS 443 de entrada, para gestão de replicação.
   - As máquinas enviam dados de replicação para o servidor de processo na porta HTTPS 9443 (pode ser modificada).
   - O servidor de configuração orquestra a gestão de replicação com o Azure sobre a porta HTTPS 443 de saída.
   - O servidor de processo recebe dados de máquinas de origem, otimiza-os e encripta-os e envia-os para o armazenamento do Azure sobre a porta 443 HTTPS.
   - Se ativar a consistência multi-VM, as máquinas no grupo de replicação comunicam entre si pela porta 20004. A consistência multi-VM é utilizada se agrupar várias máquinas em grupos de replicação que partilhem pontos de consistência de falhas e pontos de consistência de aplicação quando é efetuada a ativação pós-falha dos mesmos. Estes grupos são úteis se as máquinas estiverem a executar a mesma carga de trabalho e precisarem de ser consistentes.
1. O tráfego é replicado para pontos finais públicos do armazenamento do Azure, através da Internet. Em alternativa, pode utilizar o [peering público](../expressroute/about-public-peering.md) do Azure ExpressRoute.

   > [!NOTE]
   > A replicação não é suportada sobre uma VPN site-to-site a partir de um site no local ou Azure ExpressRoute [peering privado](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute).

**Processo de replicação físico a Azure**

![Processo de replicação](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo de ativação pós-falha e de reativação pós-falha

Após a configuração da replicação, pode executar um exercício de recuperação de desastres (teste failover) para verificar se tudo funciona como esperado. Então, podes falhar e falhar quando for necessário. Considere os seguintes itens:

- A ativação pós-falha planeada não é suportada.
- É necessário voltar a um VMware VMware no local. Você precisa de uma infraestrutura VMware no local, mesmo quando você replica no local servidores físicos para Azure.
- Falhas numa única máquina, ou crias planos de recuperação, para falhares em várias máquinas em conjunto.
- Quando executam um failover, os VMs Azure são criados a partir de dados replicados no armazenamento Azure.
- Depois de desencadear o failover inicial, compromete-o a começar a aceder à carga de trabalho a partir do Azure VM.
- Quando o site no local primário estiver novamente disponível, pode fazer a reativação pós-falha.
- Crie uma infraestrutura de recuo que inclua:
  - **Servidor de processo temporário em Azure**: Para falhar de volta do Azure, configura um Azure VM para funcionar como um servidor de processo, para lidar com a replicação do Azure. Pode eliminar este VM depois de ter terminado o ressari.
  - **Ligação VPN**: Para falhar, precisa de uma ligação VPN (ou Azure ExpressRoute) da rede Azure para o local do local.
  - **Servidor-alvo principal separado**: Por padrão, a parte traseira da falha é manuseada pelo servidor alvo principal que foi instalado com o servidor de configuração no VMware VM. Se precisar de falhar em grandes volumes de tráfego, deverá configurar um servidor-alvo principal no local.
  - **Política de reativação pós-falha**: para replicar de novo para o site no local, precisa de uma política de reativação pós-falha. A política foi criada automaticamente quando criou a sua política de replicação desde as instalações até ao Azure.
  - **Infraestrutura VMware**: Para falhar, precisa de uma infraestrutura VMware. Não pode efetuar a reativação pós-falha num servidor físico.
- Depois de os componentes estarem no lugar, a falha ocorre em três fases:
  - **Fase 1**: Reprotete os VMs Azure para que se reproduzam de Azure de volta para os VMS VMware no local.
  - **Fase 2**: Executar um failover para o local do local.
  - **Fase 3**: Depois de as cargas de trabalho terem falhado, a replicação reenrável.

**VMware falha de volta de Azure**

![Reativação pós-falha](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>Passos seguintes

Para configurar a recuperação de desastres para servidores físicos para Azure, consulte o [guia de como fazer.](physical-azure-disaster-recovery.md)
