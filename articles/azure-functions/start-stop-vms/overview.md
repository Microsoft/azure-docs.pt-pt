---
title: Início/Paragem VMs v2 (pré-visualização) visão geral
description: Este artigo descreve a versão dois da funcionalidade Iniciar/Parar VMs (pré-visualização), que começa ou para o Azure Resource Manager e vMs clássicos numa programação.
ms.topic: conceptual
ms.service: azure-functions
ms.subservice: ''
ms.date: 03/29/2021
ms.openlocfilehash: 44bfbaa8b18ebeab3b74bc696a16fc4cfb6c08ec
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220939"
---
# <a name="startstop-vms-v2-preview-overview"></a>Início/Paragem VMs v2 (pré-visualização) visão geral

A função Iniciar/Parar VMs v2 (pré-visualização) inicia ou para as máquinas virtuais Azure (VMs) através de várias subscrições. Inicia ou para os VMs Azure em horários definidos pelo utilizador, fornece insights através do [Azure Application Insights,](../../azure-monitor/app/app-insights-overview.md)e envia notificações opcionais utilizando [grupos de ação](../../azure-monitor/alerts/action-groups.md). A funcionalidade pode gerir vMs do Azure Resource Manager e VMs clássicos para a maioria dos cenários.

Esta nova versão de Start/Stop VMs v2 (pré-visualização) fornece uma opção de automação de baixo custo descentralizada para clientes que querem otimizar os seus custos VM. Oferece todas as mesmas funcionalidades que a [versão original](../../automation/automation-solution-vm-management.md) disponível com a Azure Automation, mas foi concebida para tirar partido da tecnologia mais recente em Azure.

## <a name="overview"></a>Descrição Geral

O VMs de arranque/paragem v2 (pré-visualização) é redesenhado e não depende de Registos Azure Automation ou Azure Monitor, conforme exigido pela [versão anterior](../../automation/automation-solution-vm-management.md). Esta versão baseia-se em [Funções Azure](../../azure-functions/functions-overview.md) para lidar com o início do VM e parar a execução.

Uma identidade gerida é criada no Azure Ative Directory (Azure AD) para esta aplicação Azure Functions e permite que VMs de início/paragem v2 (pré-visualização) acedam facilmente a outros recursos protegidos por Azure, como as aplicações lógicas e VMs Azure. Para obter mais sobre identidades geridas em Azure AD, consulte [identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md).

É criada uma função de ponto final http trigger para suportar os cenários de programação e sequência incluídos na funcionalidade, como mostrado na tabela seguinte.

|Name |Acionador |Descrição |
|-----|--------|------------|
|AlertaSeilabilityTest |Temporizador |Esta função executa o teste de disponibilidade para garantir que a função primária **AutoStopVM** está sempre disponível.|
|AutoStop |HTTP |Esta função suporta o cenário **AutoStop,** que é a função ponto de entrada que é chamada a partir de Logic App.|
|AutoStopAvailabilityTest |Temporizador |Esta função executa o teste de disponibilidade para se certificar de que a função primária **AutoStop** está sempre disponível.|
|AutoStopvM |HTTP |Esta função é ativada automaticamente pelo alerta VM quando a condição de alerta é verdadeira.|
|CreateAutoStopAlertExecutor |Fila |Esta função obtém a informação de carga útil da função **AutoStop** para criar o alerta no VM.|
|Agendado |HTTP |Esta função destina-se ao cenário programado e sequenciado (diferenciado pelo esquema de carga útil). É a função de ponto de entrada chamada da App Lógica e leva a carga útil para processar o início do VM ou parar a operação. |
|AgendadoSSSDeseilabilityTest |Temporizador |Esta função executa o teste de disponibilidade para garantir que a função principal **Agendada** está sempre disponível.|
|VirtualMachineRequestExecutor |Fila |Esta função executa o funcionamento de arranque e paragem real no VM.|
|VirtualMachineRequestOrchestrator |Fila |Esta função obtém a informação de carga útil da função **Programado** e orquestra os pedidos de arranque e paragem de VM.|

Por exemplo, a função de gatilho HTTP **programado** é utilizada para lidar com cenários de agendamento e sequência. Da mesma forma, a função de gatilho **AutoStop** HTTP lida com o cenário de paragem automática.

As funções de gatilho baseadas na fila são necessárias para suportar esta função. Todos os gatilhos baseados no temporizador são utilizados para realizar o teste de disponibilidade e monitorizar a saúde do sistema.

 [As Azure Logic Apps](../../logic-apps/logic-apps-overview.md) são usadas para configurar e gerir os horários de início e paragem para o VM tomar medidas, chamando a função usando uma carga útil JSON. Por predefinição, durante a implementação inicial cria um total de cinco Aplicações Lógicas para os seguintes cenários:

- Agendado - As ações de arranque e paragem baseiam-se num horário que especifica contra o Azure Resource Manager e os VMs clássicos. **ststv2_vms_Scheduled_start** e **ststv2_vms_Scheduled_stop** configuram o início e paragem programados.

- Sequenciais - As ações de arranque e paragem baseiam-se num cronojo de direcionamento de VMs com etiquetas de sequenciação pré-definidas. Apenas duas tags nomeadas são suportadas - **início de sequência** e **sequências .** **ststv2_vms_Sequenced_start** e **ststv2_vms_Sequenced_stop** configuram o início e a paragem sequenciais.

    > [!NOTE]
    > Este cenário suporta apenas VMs Azure Resource Manager.

- AutoStop - Esta funcionalidade é utilizada apenas para realizar uma ação de stop contra o Azure Resource Manager e vMs clássicos com base na sua utilização de CPU. Também pode ser uma ação de tomada de *ção* programada , que cria alertas em VMs e com base na condição, o alerta é desencadeado para realizar a ação de paragem. **ststv2_vms_AutoStop** configura a funcionalidade de paragem automática.

Cada ação Start/Stop suporta a atribuição de uma ou mais subscrições, grupos de recursos ou uma lista de VMs.

Uma conta de Armazenamento Azure, que é exigida por Funções, também é utilizada por VMs de início/paragem v2 (pré-visualização) para dois fins:

   - Utiliza o Armazenamento de MesaS Azure para armazenar os metadados de operação de execução (isto é, a ação VM de início/paragem).

   - Utiliza o armazenamento da fila Azure para suportar os gatilhos baseados em filas Azure Functions.

Todos os dados de telemetria, que são registos de vestígios da execução da aplicação de função, são enviados para a sua instância de Insights de Aplicação conectada. Pode ver os dados de telemetria armazenados no Application Insights a partir de um conjunto de visualizações pré-definidas apresentadas num [dashboard Azure](../../azure-portal/azure-portal-dashboards.md)partilhado .

:::image type="content" source="media/overview/shared-dashboard-sml.png" alt-text="Início/Stop VMs painel de estado partilhado":::

As notificações por e-mail também são enviadas como resultado das ações realizadas nos VMs.

## <a name="new-releases"></a>Novos lançamentos

Quando uma nova versão de VMs start/stop v2 (pré-visualização) é lançada, o seu caso é atualizado automaticamente sem ter de ser recolocado manualmente.

## <a name="supported-scoping-options"></a>Opções de scoping suportadas

### <a name="subscription"></a>Subscrição

A deteção de uma subscrição pode ser utilizada quando é necessário realizar a ação inicial e stop em todos os VMs numa subscrição completa, e pode selecionar várias subscrições se necessário.  

Também pode especificar uma lista de VMs para excluir e irá ignorá-los da ação. Também pode usar caracteres wildcard para especificar todos os nomes que simultaneamente podem ser ignorados.

### <a name="resource-group"></a>Grupo de recursos

A deteção de um grupo de recursos pode ser utilizada quando é necessário executar a ação inicial e stop em todos os VMs, especificando um ou mais nomes de grupos de recursos e através de uma ou mais subscrições.

Também pode especificar uma lista de VMs para excluir e irá ignorá-los da ação. Também pode usar caracteres wildcard para especificar todos os nomes que simultaneamente podem ser ignorados.

### <a name="vmlist"></a>VMList

Especificar uma lista de VMs pode ser usado quando é necessário executar a ação de início e paragem num conjunto específico de máquinas virtuais e em várias subscrições. Esta opção não suporta especificar uma lista de VMs para excluir.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/)

- A sua conta foi-lhe concedida a permissão [do Contribuinte](../../role-based-access-control/built-in-roles.md#contributor) na subscrição.

- Start/Stop VMs v2 (pré-visualização) está disponível em todas as regiões de nuvem do Governo Azure global e dos EUA que estão listadas em [Produtos disponíveis por página de região](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=functions) para Funções Azure.

## <a name="next-steps"></a>Passos seguintes

Para implementar esta função, consulte [Iniciar vMs iniciar/parar](deploy.md) (pré-visualização).