---
title: Configure a monitorização em Saúde dos hóspedes (pré-visualização)
description: Descreve como modificar a monitorização predefinida para a saúde do hóspede (pré-visualização) utilizando o portal Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/14/2020
ms.openlocfilehash: 6bda6c9e5f6e23e9e15c12fd507645fc72159302
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104583450"
---
# <a name="configure-monitoring-in-vm-insights-guest-health-preview"></a>Configure a monitorização em Saúde dos hóspedes (pré-visualização)
A saúde do hóspede permite-lhe ver a saúde de uma máquina virtual como definida por um conjunto de medições de desempenho que são amostradas a intervalos regulares. Este artigo descreve como pode modificar a monitorização predefinida utilizando o portal Azure. Descreve também conceitos fundamentais de monitores necessários para [configurar a monitorização utilizando uma regra de recolha de dados.](vminsights-health-configure-dcr.md)

## <a name="open-monitor-configuration"></a>Configuração do monitor aberto
Abra a configuração do monitor no portal Azure selecionando o monitor e, em seguida, o **separador Configuração.**

[![Configuração de detalhes do monitor](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)

## <a name="enable-or-disable-monitors"></a>Ativar ou desativar monitores
Tanto os monitores unitários como os monitores agregados têm uma definição de estado do monitor de **saúde** que lhe permite ativar ou desativar o monitor. Quando um monitor está ativado, então a sua saúde é exibida e usada para definir a saúde do VM. Quando um monitor é desativado, a sua saúde não é calculada e não é utilizada para definir a saúde do VM.

| Definições | Descrição |
|:---|:---|
| Ativado | O monitor está ativado independentemente da configuração do seu progenitor. |
| Desativado | O monitor é desativado independentemente da configuração do seu progenitor. |
| O mesmo que os pais | O monitor será ativado ou desativado dependendo da configuração do seu progenitor. |

Quando um monitor é desativado, todos os critérios são mostrados como não disponíveis como mostrado no exemplo seguinte.

![Monitor desativado](media/vminsights-health-configure/disabled-monitor.png)


> [!NOTE]
> Se um monitor parental estiver desativado, qualquer monitor infantil também é desativado. Se ativar explicitamente o monitor da criança, o progenitor também estará ativado, mas o seu estado de configuração permanecerá o mesmo. Neste caso, receberá a seguinte mensagem no monitor dos pais.
>
> *Há uma discrepância, uma vez que o estado configurado do monitor é "incapacitado", mas o Estado de Saúde não reflete isso. Isto porque, quer as alterações configuradas estejam a ser propagadas, ou qualquer um dos seus monitores infantis foi explicitamente ativado.*

## <a name="enable-or-disable-virtual-machine"></a>Ativar ou desativar a máquina virtual
Pode desativar a monitorização de um VM para parar temporariamente todos os monitores. Pode desativar a monitorização de um VM, por exemplo, quando estiver a fazer manutenção.

| Definições | Descrição |
|:---|:---|
| Ativado  | O estado de saúde do computador é exibido. |
| Desativado | O estado de saúde do computador é mostrado como **desativado.** Os alertas não são criados. |

## <a name="health-state-logic"></a>Lógica do estado de saúde
A lógica do estado de saúde para um monitor de unidade define os critérios para a definição do seu estado de saúde. Pode especificar quantos estados de saúde um monitor tem e o limiar de como cada estado de saúde é calculado.

![Critérios de saúde da amostra](media/vminsights-health-configure/sample-health-criteria.png)

Monitores agregados não especificam nenhuma lógica do estado de saúde. O seu estado de saúde é definido pelos monitores da unidade sob eles, de acordo com o seu desenrolar de saúde.

Os monitores da unidade têm dois ou três estados de saúde. Cada um terá sempre um estado saudável e opcionalmente um estado de aviso, um estado crítico, ou ambos. Estados de advertência e críticos exigem critérios únicos que definem quando o monitor é definido para este estado. O Estado Saudável não tem critérios, uma vez que este estado é definido quando os critérios para os outros Estados não são cumpridos.

No exemplo seguinte, a utilização do CPU é definida para os seguintes estados de saúde:

- Crítico se for superior a 90%.
- Atenção se for maior ou igual a 80%.
- Saudável se tiver menos de 80%. Isto está implícito, uma vez que é a condição em que nenhuma das outras condições se aplica.

## <a name="next-steps"></a>Passos seguintes

- [Configure os monitores em escala utilizando as regras de recolha de dados.](vminsights-health-configure-dcr.md)
