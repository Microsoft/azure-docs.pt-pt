---
title: Ligue máquinas híbridas ao Azure do Windows Admin Center
description: Neste artigo, aprende-se a instalar o agente e a ligar máquinas ao Azure utilizando servidores ativados pelo Azure Arc do Windows Admin Center.
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: d47e3dd4434d719f890b64e4cdfb12a189c0632a
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133794"
---
# <a name="connect-hybrid-machines-to-azure-from-windows-admin-center"></a>Ligue máquinas híbridas ao Azure do Windows Admin Center

Pode ativar os servidores ativados do Azure Arc para uma ou mais máquinas Windows no seu ambiente, realizando manualmente um conjunto de passos. Ou pode utilizar o [Windows Admin Center](/windows-server/manage/windows-admin-center/understand/what-is) para implantar o agente 'Máquina Conectada' e registar os seus servidores no local sem ter de realizar quaisquer passos fora desta ferramenta.

## <a name="prerequisites"></a>Pré-requisitos

* Servidores ativados pelo Arc - Reveja os [pré-requisitos](agent-overview.md#prerequisites) e verifique se a sua subscrição, a sua conta Azure e os recursos cumprem os requisitos.

* Windows Admin Center - Reveja os requisitos para preparar o [seu ambiente](/windows-server/manage/windows-admin-center/deploy/prepare-environment) para implementar e configurar a [integração do Azure ](/windows-server/manage/windows-admin-center/azure/azure-integration).

* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Os servidores-alvo do Windows que pretende gerir devem ter conectividade com a Internet para aceder ao Azure.

### <a name="security"></a>Segurança

Este método de implementação requer que tenha direitos de administrador na máquina ou servidor do Windows alvo para instalar e configurar o agente. Também precisa de ser membro do papel dos utilizadores do [**Gateway.**](/windows-server/manage/windows-admin-center/plan/user-access-options#gateway-access-roles)

## <a name="deploy"></a>Implementar

Execute os seguintes passos para configurar o servidor do Windows com servidores ativados pelo Arc.

1. Inscreva-se no Windows Admin Center.

1. A partir da lista de ligações na página **'Vista Geral',** na lista de servidores Windows conectados, selecione um servidor da lista para ligar à sua.

1. A partir do painel de esquerda, selecione **serviços híbridos Azure** .

1. Na página de **serviços híbridos Azure,** selecione **Serviços Discover Azure** .

1. Na página de **serviços Discover Azure,** sob **políticas e soluções do Alavancagem Azure para gerir os seus servidores com o Azure Arc,** selecione **Configurar** .

1. Na página **Definições\Azure Arc para servidores,** se solicitado autenticar para Azure e, em seguida, selecionar **Iniciar** .

1. Na página **'Ligar o servidor', a Azure,** fornecer o seguinte:

    1. Na lista de entrega de **assinaturas Azure,** selecione a subscrição Azure.
    1. Para **o grupo de recursos** , selecione **New** para criar um novo grupo de recursos, ou na lista de down-down do grupo **De recurso,** selecione um grupo de recursos existente para registar e gerir a máquina a partir de.
    1. Na lista de drop-down da **Região,** selecione a região Azure para armazenar os metadados dos servidores.
    1. Se a máquina ou servidor estiver a comunicar através de um servidor proxy para se ligar à internet, selecione a opção Utilize o **servidor proxy** . Especifique o endereço IP do servidor proxy ou o nome e o número de porta que a máquina utilizará para comunicar com o servidor proxy.

1. Selecione **Configurar** para proceder à configuração do servidor do Windows com servidores ativados Azure Arc.

O servidor windows irá ligar-se ao Azure, descarregar o agente 'Máquina Conectada', instalá-lo e registar-se-á com servidores ativados pelo Azure Arc. Para acompanhar o progresso, **selecione Notificações** no menu.

Para confirmar a instalação do Agente de Máquinas Conectados, no Windows Admin Center selecione [**Eventos**](/windows-server/manage/windows-admin-center/use/manage-servers#events) do painel esquerdo para rever os eventos *do MsiInstaller* no Registo de Eventos de Aplicação.

## <a name="verify-the-connection-with-azure-arc"></a>Verificar a ligação com o Azure Arc

Depois de instalar o agente e configurá-lo para ligar aos servidores ativados do Azure Arc, vá ao portal Azure para verificar se o servidor se ligou com sucesso. Veja a sua máquina no [portal Azure](https://portal.azure.com).

:::image type="content" source="./learn/media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Uma ligação de máquina bem sucedida" border="false":::

## <a name="next-steps"></a>Passos seguintes

* As informações relativas à resolução de problemas podem ser encontradas no guia do [agente da máquina conectado de resolução de problemas.](troubleshoot-agent-onboard.md)

* Aprenda a gerir a sua máquina utilizando [a Azure Policy](../../governance/policy/overview.md), para coisas como [a configuração do hóspede](../../governance/policy/concepts/guest-configuration.md)VM , verificando se a máquina está a reportar ao espaço de trabalho esperado do Log Analytics, permitir a monitorização com o [Azure Monitor com VMs](../../azure-monitor/insights/vminsights-enable-policy.md), e muito mais.

* Saiba mais sobre o [agente Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md) O agente Log Analytics para Windows e Linux é necessário quando pretende recolher dados de monitorização do sistema operativo e da carga de trabalho, geri-lo utilizando livros de aplicação da Automação ou funcionalidades como Update Management, ou utilizar outros serviços Azure como [o Azure Security Center](../../security-center/security-center-intro.md).