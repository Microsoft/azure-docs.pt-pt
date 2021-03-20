---
title: Como migrar o Arco Azure permitiu servidores através de regiões
description: Saiba como migrar um servidor Azure Arc ativado de uma região para outra.
ms.date: 02/10/2021
ms.topic: conceptual
ms.openlocfilehash: 251a347205d93af715add52db293d8000438df44
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650180"
---
# <a name="how-to-migrate-azure-arc-enabled-servers-across-regions"></a>Como migrar o Arco Azure permitiu servidores através de regiões

Existem cenários em que gostaria de mover o seu atual Azure Arc ativado servidor de uma região para outra. Por exemplo, percebeu que a máquina estava registada na região errada, para melhorar a gestão, ou para se mover por razões de governação.

Para migrar um Azure Arc ativado servidor de uma região Azure para outra, você tem que desinstalar as extensões VM, eliminar o recurso em Azure, e recobri-lo na outra região. Antes de efetuar estes passos, deverá auditar a máquina para verificar quais as extensões VM instaladas.

> [!NOTE]
> Enquanto as extensões instaladas continuarem a funcionar e a executar o seu funcionamento normal após este procedimento estar concluído, não será capaz de as gerir. Se tentar recolocar as extensões na máquina, poderá experimentar comportamentos imprevisíveis.

## <a name="move-machine-to-other-region"></a>Mover máquina para outra região

> [!NOTE]
> Durante esta operação, resulta em tempo de inatividade durante a migração.

1. Remova as extensões VM instaladas a partir do [portal Azure,](manage-vm-extensions-portal.md#uninstall-extension)utilizando o [Azure CLI,](manage-vm-extensions-cli.md#remove-an-installed-extension)ou utilizando [a Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension).

2. Utilize a ferramenta **azmagent** com o parâmetro [Desligar](manage-agent.md#disconnect) para desligar a máquina do Arco Azure e eliminar o recurso da máquina do Azure. Desligar a máquina dos servidores ativados pelo Arc não remove o agente 'Máquina Conectada' e não é necessário remover o agente como parte deste processo. Pode executá-lo manualmente enquanto inicia o login interativamente, ou automatizar utilizando o mesmo principal de serviço que usou para bordo de vários agentes, ou com um [token de acesso à](../../active-directory/develop/access-tokens.md)plataforma de identidade da Microsoft. Se não usou um principal de serviço para registar a máquina com servidores ativados Azure Arc, consulte o [seguinte artigo](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) para criar um principal de serviço.

3. Re-registrar o agente 'Máquina Conectada' com servidores ativados pelo Arco na outra região. Executar a `azcmagent` ferramenta com o parâmetro ['Ligar'](manage-agent.md#connect) completa este passo.

4. Recolocar as extensões VM que foram originalmente implantadas na máquina a partir de servidores ativados pelo Arc. Se implementou o agente Azure Monitor para VMs (insights) ou o agente Log Analytics utilizando uma política Azure, os agentes são reencamôde após o ciclo de [avaliação](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)seguinte .

## <a name="next-steps"></a>Passos seguintes

* As informações relativas à resolução de problemas podem ser encontradas no guia do [agente da máquina conectado de resolução de problemas.](troubleshoot-agent-onboard.md)

* Aprenda a gerir a sua máquina utilizando a [Azure Policy](../../governance/policy/overview.md), para coisas como [a configuração do hóspede](../../governance/policy/concepts/guest-configuration.md)VM , verificando que a máquina está a reportar ao espaço de trabalho esperado do Log Analytics, permitir a monitorização com a política do [Azure Monitor com VMs,](../../azure-monitor/vm/vminsights-enable-policy.md) e muito mais.