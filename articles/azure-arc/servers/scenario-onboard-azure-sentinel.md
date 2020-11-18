---
title: A bordo do Azure Arc ativou o servidor para O Azure Sentinel
description: Saiba como adicionar os seus servidores Azure Arc habilitados ao Azure Sentinel e monitorize proativamente o seu estado de segurança.
ms.date: 11/16/2020
ms.topic: conceptual
ms.openlocfilehash: 60018e710613a27bcb5c00f38ee90be6ff46c61d
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94811108"
---
# <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>A bordo do Azure Arc permitiu servidores a Azure Sentinel

Este artigo destina-se a ajudá-lo a bordo do seu Azure Arc ativado servidor para [Azure Sentinel](../../sentinel/overview.md) e começar a recolher eventos relacionados com a segurança. O Azure Sentinel fornece uma única solução para deteção de alerta, visibilidade de ameaças, caça proativa e resposta a ameaças em toda a empresa.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que cumpriu os seguintes requisitos:

- Uma [área de trabalho do Log Analytics](../../azure-monitor/platform/data-platform-logs.md). Para obter mais informações sobre os espaços de trabalho do Log Analytics, consulte a conceção da sua implementação de [Registos de Monitores Azure.](../../azure-monitor/platform/design-logs-deployment.md)

- Azure Sentinel [ativado na sua subscrição.](../../sentinel/quickstart-onboard.md)

- A sua máquina ou servidor está ligado aos servidores ativados do Azure Arc.

## <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>A bordo do Azure Arc permitiu servidores a Azure Sentinel

O Azure Sentinel vem com uma série de conectores para soluções Microsoft, disponíveis fora da caixa e proporcionando integração em tempo real. Para máquinas físicas e virtuais, pode instalar o agente Log Analytics que recolhe os registos e os encaminha para o Azure Sentinel. Os servidores ativados pelo Arco suportam a implementação do agente Log Analytics utilizando os seguintes métodos:

- Utilizando o quadro de extensões VM.

    Esta funcionalidade nos servidores ativados do Azure Arc permite-lhe implementar a extensão VM do agente Log Analytics para um servidor windows e/ou Linux não-Azure. As extensões VM podem ser geridas utilizando os seguintes métodos nas suas máquinas híbridas ou servidores geridos por servidores ativados pelo Arc:

    - O [portal Azure](manage-vm-extensions-portal.md)
    - O [Azure CLI](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - [Modelos de gestor de recursos Azure](manage-vm-extensions-template.md)

- Usando a política de Azure.

    Utilizando esta abordagem, utiliza o agente Azure Policy [Deploy Log Analytics para máquinas Linux ou Windows Azure Arc incorporadas](../../governance/policy/samples/built-in-policies.md#monitoring) para auditar se o servidor ativado pelo Arco tiver o agente Log Analytics instalado. Se o agente não estiver instalado, implanta-o automaticamente utilizando uma tarefa de reparação. Em alternativa, se planeia monitorizar as máquinas com o Azure Monitor para VMs, em vez disso, utilize o [Enable Azure Monitor para](../../governance/policy/samples/built-in-initiatives.md#monitoring) a iniciativa VMs para instalar e configurar o agente Log Analytics.

Recomendamos a instalação do agente Log Analytics para Windows ou Linux utilizando a Política Azure.

Depois de os servidores ativados do Arco estarem ligados, os seus dados começam a ser transmitidos para o Azure Sentinel e estão prontos para começar a trabalhar. Pode ver os registos nos [livros incorporados](../../sentinel/quickstart-get-visibility.md) e começar a criar consultas no Log Analytics para [investigar os dados.](../../sentinel/tutorial-investigate-cases.md)

## <a name="next-steps"></a>Passos seguintes

Começa [a detetar ameaças com o Azure Sentinel.](../../sentinel/tutorial-detect-threats-built-in.md)