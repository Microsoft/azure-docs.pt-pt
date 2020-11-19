---
title: Ver e configurar telemetria de proteção contra DDoS
description: Saiba como visualizar e configurar a telemetria de proteção DDoS.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: eefb658c689128c1d91858ac906c09e71d05cda6
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888951"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>Ver e configurar telemetria de proteção contra DDoS

A padrão de proteção DDoS Azure fornece informações detalhadas sobre ataque e visualização com DDoS Attack Analytics. Os clientes que protegem as suas redes virtuais contra ataques de DDoS têm uma visibilidade detalhada no tráfego de ataques e as ações tomadas para mitigar o ataque através de relatórios de mitigação de ataques & registos de fluxo de mitigação. A telemetria rica é exposta através do Azure Monitor, incluindo métricas detalhadas durante a duração de um ataque DDoS. O alerta pode ser configurado para qualquer uma das métricas do Monitor Azure expostas pela Proteção DDoS. O registo pode ser integrado com [Azure Sentinel,](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection)Splunk (Azure Event Hubs), OMS Log Analytics e Azure Storage para análise avançada através da interface Azure Monitor Diagnostics.

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Alertas de configuração para métricas de proteção DDoS
> * Utilizar telemetria de proteção DDoS
> * Ver políticas de mitigação do DDoS
> * Ver alertas de proteção DDoS no Centro de Segurança Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Antes de poder completar os passos neste tutorial, tem primeiro de criar um [plano de proteção Azure DDoS Standard](manage-ddos-protection.md).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Alertas de configuração para métricas de proteção DDoS

Pode selecionar qualquer uma das métricas de proteção DDoS disponíveis para alertá-lo quando houver uma mitigação ativa durante um ataque, utilizando a configuração de alerta do Monitor Azure. Quando as condições são satisfeitas, o endereço especificado recebe um e-mail de alerta:

1. Selecione **Todos os serviços** em cima, à esquerda do portal.
2. Introduza o *Monitor* na caixa **do filtro.** Quando **o Monitor** aparecer nos resultados, selecione-o.
3. Selecione **métricas** em **SERVIÇOS PARTILHADOS**.
4. Insira, ou selecione os seus próprios valores, ou introduza os seguintes valores de exemplo, aceite os predefinidos restantes e, em seguida, selecione **OK**:

    |Definição                  |Valor                                                                                               |
    |---------                |---------                                                                                           |
    |Nome                     | Insira _myDdosAlert_.                                                                                |
    |Subscrição             | Selecione a subscrição que contém o endereço IP público para o quais pretende receber alertas.        |
    |Grupo de recursos           | Selecione o grupo de recursos que contém o endereço IP público para o quais pretende receber alertas.      |
    |Recurso                 | Selecione o endereço IP público que contém o endereço IP público para o quais pretende receber alertas. O DDoS monitoriza endereços IP públicos atribuídos a recursos dentro de uma rede virtual. Se não tiver recursos com endereços IP públicos na rede virtual, tem primeiro de criar um recurso com um endereço IP público. Pode monitorizar o endereço IP público de todos os recursos implantados através do Gestor de Recursos (não clássico) listados na [rede Virtual para serviços Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services#services-that-can-be-deployed-into-a-virtual-network), exceto para Azure App Service Environments e Azure VPN Gateway. Para continuar com este tutorial, pode rapidamente criar uma máquina virtual [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux.](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)                   |
    |Métrica                   | Selecione **sob o ataque DDoS ou não**.                                                                |
    |Limiar                | 1 - **1** significa que estás a ser atacado. **0** significa que não estás a ser atacado.                         |
    |Período                   | Selecione o valor que escolher.                                                                   |
    |Notificar via E-mail         | Verifique a caixa de verificação.                                                                                 |
    |Administrador adicional | Insira o seu endereço de e-mail se não for proprietário de e-mail, colaborador ou leitor para a subscrição. |

    Em poucos minutos após a deteção de ataques, recebe um e-mail de métricas do Azure Monitor que se parece com a seguinte imagem:

    ![Alerta de ataque](./media/manage-ddos-protection/ddos-alert.png)


Para simular um ataque DDoS para validar o seu alerta, consulte [validar a deteção de DDoS](test-through-simulations.md).

Também pode aprender mais sobre [configurar webhooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [aplicações lógicas](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criar alertas.

## <a name="use-ddos-protection-telemetry"></a>Utilizar telemetria de proteção DDoS

A telemetria para um ataque é fornecida através do Monitor Azure em tempo real. A telemetria só está disponível durante o período em que um endereço IP público esteja sob mitigação. Não se vê telemetria antes ou depois de um ataque ser atenuado.

1. Selecione **Todos os serviços** em cima, à esquerda do portal.
2. Introduza o *Monitor* na caixa **do filtro.** Quando **o Monitor** aparecer nos resultados, selecione-o.
3. Selecione **Métricas,** em **SERVIÇOS PARTILHADOS**.
4. Selecione o grupo **de Subscrição** e **Recursos** que contenha o endereço IP público para o quais deseja telemetria.
5. Selecione **Endereço IP Público** para o tipo de **recurso,** em seguida, selecione o endereço IP público específico para o quais deseja telemetria.
6. Uma série de **Métricas Disponíveis** aparecem no lado esquerdo do ecrã. Estas métricas, quando selecionadas, são grafadas na **Tabela de Métricas do Monitor Azure** no ecrã geral.
7. Selecione o tipo **de agregação** como **Max**

Os nomes métricos apresentam diferentes tipos de pacotes, e bytes vs. pacotes, com uma construção básica de nomes de etiquetas em cada métrica da seguinte forma:

- **Nome da etiqueta de queda** (por exemplo, **Pacotes de Entrada Deixou Cair DDoS**): O número de pacotes caídos/esfregados pelo sistema de proteção DDoS.
- **Nome de etiqueta reencaminhado** (por **exemplo, Pacotes de Entrada Reencaminhados DDoS**): Número de pacotes reencaminhados pelo sistema DDoS para o destino VIP – tráfego que não foi filtrado.
- **Sem nome de identificação** (por **exemplo, Pacotes de Entrada DDoS**): O número total de pacotes que entraram no sistema de esfregar – representando a soma dos pacotes caídos e reencaminhados.

Esta [regra de alerta do Azure Monitor](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20Monitor%20Alert%20-%20DDoS%20Mitigation%20Started) executará uma consulta simples para detetar quando está a ocorrer uma mitigação ativa do DDoS. Para simular um ataque DDoS para validar a telemetria, consulte [validar a deteção de DDoS](test-through-simulations.md). 

## <a name="view-ddos-mitigation-policies"></a>Ver políticas de mitigação do DDoS

A Norma de Proteção DDoS aplica três políticas de mitigação auto-sintonizadas (TCP SYN, TCP & UDP) para cada endereço IP público do recurso protegido, na rede virtual que tem DDoS ativado. Pode ver os limiares de política selecionando os  **pacotes TCP de entrada para desencadear pacotes de mitigação de DDoS** e UDP de entrada para ativar métricas **de mitigação de DDoS** com tipo **de agregação** como 'Max', como mostra a seguinte imagem:

![Ver políticas de mitigação](./media/manage-ddos-protection/view-mitigation-policies.png)

Os limiares de política são configurados automaticamente através do perfil de tráfego de rede baseado em aprendizagem automática Azure. Só quando o limiar da apólice é violado é que a mitigação do DDoS ocorre para o endereço IP sob ataque.

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Ver alertas de proteção DDoS no Centro de Segurança Azure

O Azure Security Center fornece uma lista de alertas de [segurança,](/azure/security-center/security-center-managing-and-responding-alerts)com informações para ajudar a investigar e corrigir problemas. Com esta funcionalidade, obtém-se uma visão unificada dos alertas, incluindo alertas relacionados com ataques DDoS e as ações tomadas para mitigar o ataque em pouco tempo.
Existem dois alertas específicos que verá para qualquer deteção e mitigação de ataques DDoS:

- **Ataque DDoS detetado para IP Público**: Este alerta é gerado quando o serviço de proteção DDoS deteta que um dos seus endereços IP públicos é alvo de um ataque DDoS.
- **Ataque DDoS atenuado para IP público**: Este alerta é gerado quando um ataque ao endereço IP público foi atenuado.
Para ver os alertas, abra o **Centro de Segurança** no portal Azure. Sob **proteção contra** ameaças , selecione **alertas de segurança**. A imagem que se segue mostra um exemplo dos alertas de ataque do DDoS.

![Alerta DDoS no Centro de Segurança Azure](./media/manage-ddos-protection/ddos-alert-asc.png)

Os alertas incluem informações gerais sobre o endereço IP público que está sob ataque, informações de inteligência geo e ameaças, e medidas de reparação.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

- Alertas de configuração para métricas de proteção DDoS
- Utilizar telemetria de proteção DDoS
- Ver políticas de mitigação do DDoS
- Ver alertas de proteção DDoS no Centro de Segurança Azure

Para aprender a configurar relatórios de mitigação de ataques e registos de fluxo, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Configurar registos de fluxos e relatórios de mitigação de ataques de DDoS](reports-and-flow-logs.md)