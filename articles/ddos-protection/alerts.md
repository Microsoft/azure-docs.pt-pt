---
title: Ver e configurar alertas de proteção DDoS para norma de proteção DDoS Azure
description: Saiba como visualizar e configurar alertas de proteção DDoS para norma de proteção DDoS Azure.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: a5639d583d9b98f6527e47bf5db213cb191ebeb7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575295"
---
# <a name="view-and-configure-ddos-protection-alerts"></a>Ver e configurar os alertas da proteção contra DDoS

A padrão de proteção DDoS Azure fornece informações detalhadas sobre ataque e visualização com DDoS Attack Analytics. Os clientes que protegem as suas redes virtuais contra ataques de DDoS têm uma visibilidade detalhada no tráfego de ataques e as ações tomadas para mitigar o ataque através de relatórios de mitigação de ataques & registos de fluxo de mitigação. A telemetria rica é exposta através do Azure Monitor, incluindo métricas detalhadas durante a duração de um ataque DDoS. O alerta pode ser configurado para qualquer uma das métricas do Monitor Azure expostas pela Proteção DDoS. O registo pode ser integrado com [Azure Sentinel,](../sentinel/connect-azure-ddos-protection.md)Splunk (Azure Event Hubs), OMS Log Analytics e Azure Storage para análise avançada através da interface Azure Monitor Diagnostics.

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Alertas de configuração através do Monitor Azure
> * Configurar alertas através do portal
> * Ver alertas no Centro de Segurança Azure
> * Valide e teste os seus alertas

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Antes de poder completar os passos neste tutorial, tem primeiro de criar um [plano de proteção Azure DDoS Standard](manage-ddos-protection.md) e o DDoS Protection Standard deve ser ativado numa rede virtual.
- O DDoS monitoriza endereços IP públicos atribuídos a recursos dentro de uma rede virtual. Se não tiver recursos com endereços IP públicos na rede virtual, tem primeiro de criar um recurso com um endereço IP público. Pode monitorizar o endereço IP público de todos os recursos implantados através do Gestor de Recursos (não clássico) listados na [rede Virtual para serviços Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (incluindo Equiliadores de Carga Azure onde as máquinas virtuais de backend estão na rede virtual), exceto para Ambientes de Serviço de Aplicações Azure. Para continuar com este tutorial, pode rapidamente criar uma máquina virtual [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux.](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)     

## <a name="configure-alerts-through-azure-monitor"></a>Alertas de configuração através do Monitor Azure

Com estes modelos, poderá configurar alertas para todos os endereços IP públicos em que ativou o início de sessão de diagnóstico. Assim, para utilizar estes modelos de alerta, primeiro necessitará de um espaço de trabalho log analytics com definições de diagnóstico ativadas. Ver [visualização e configurar a registo de diagnóstico do DDoS](diagnostic-logging.md).

### <a name="azure-monitor-alert-rule"></a>Regra de alerta do Monitor Azure

Esta [regra de alerta do Azure Monitor](https://aka.ms/DDOSmitigationstatus) executará uma consulta simples para detetar quando está a ocorrer uma mitigação ativa do DDoS. Isto indica um potencial ataque. Grupos de ação podem ser usados para invocar ações como resultado do alerta.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520Monitor%2520Alert%2520-%2520DDoS%2520Mitigation%2520Started%2FDDoSMitigationStarted.json)

### <a name="azure-monitor-alert-rule-with-logic-app"></a>Regra de alerta do Monitor Azure com App Lógica

Este [modelo](https://aka.ms/ddosalert) implementa os componentes necessários de um alerta de mitigação DDoS enriquecido: regra de alerta do Monitor Azure, grupo de ação e App Lógica. O resultado do processo é um alerta de e-mail com detalhes sobre o endereço IP em ataque, incluindo informações sobre o recurso associado ao IP. O proprietário do recurso é adicionado como destinatário do e-mail, juntamente com a equipa de segurança. É também realizado um teste básico de disponibilidade de aplicações e os resultados estão incluídos no alerta de e-mail.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FDDoS%2520Mitigation%2520Alert%2520Enrichment%2FEnrich-DDoSAlert.json)

## <a name="configure-alerts-through-portal"></a>Configurar alertas através do portal

Pode selecionar qualquer uma das métricas de proteção DDoS disponíveis para alertá-lo quando houver uma mitigação ativa durante um ataque, utilizando a configuração de alerta do Monitor Azure. 

1. Inscreva-se no [portal Azure](https://portal.azure.com/) e navegue no seu Plano de Proteção DDoS.
2. Em **Monitorização**, selecione **Métricas**.
3. Na barra de navegação cinzenta, selecione **Nova regra de alerta**. 
4. Insira, ou selecione os seus próprios valores, ou introduza os seguintes valores de exemplo, aceite os predefinidos restantes e, em seguida, **selecione Criar a regra de alerta**:

    |Definição                  |Valor                                                                                               |
    |---------                |---------                                                                                           |
    | Âmbito                   | Selecione **Selecionar um recurso**. </br> Selecione a **Subscrição** que contém o endereço IP público que pretende registar, selecione **Endereço IP Público** para tipo de **recurso,** selecione o endereço IP público específico para o quais pretende registar métricas. </br> Selecione **Concluído**. | 
    | Condição | **Selecione a condição de seleção**. </br> Sob o nome do sinal, selecione **Under DDoS attack or not**. </br> No **Operador**, selecione **Maior ou igual a**. </br> Sob **o tipo de agregação,** selecione **Máximo**. </br> Sob **o valor limiar**, insira *1*. Para o **ataque do DDoS ou não,** **0** significa que não estás a ser atacado enquanto **1** significa que estás a ser atacado. </br> Selecione **Concluído**. | 
    | Ações | Selecione **Adicionar grupos de ações**. </br> Selecione **Criar grupo de ações**. </br> Em **Notificações**, sob **o tipo de Notificação**, selecione **mensagem de e-mail/SMS/Push/Voice**. </br> Under **Name**, _insira MyUnderAttackEmailAlert_. </br> Clique no botão de edição, em seguida, selecione **Email** e quantas opções necessita e, em seguida, selecione **OK**. </br> Selecione **Rever + criar**. | 
    | Detalhes da regra de alerta | Sob **o nome da regra de alerta,** Insira _MyDdosAlert_. |

Em poucos minutos após a deteção de ataques, deverá receber um e-mail das métricas do Azure Monitor que se assemesse à seguinte imagem:

![Alerta de ataque](./media/manage-ddos-protection/ddos-alert.png)

Também pode aprender mais sobre [configurar webhooks](../azure-monitor/alerts/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [aplicações lógicas](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criar alertas.

## <a name="view-alerts-in-azure-security-center"></a>Ver alertas no Centro de Segurança Azure

O Azure Security Center fornece uma lista de alertas de [segurança,](../security-center/security-center-managing-and-responding-alerts.md)com informações para ajudar a investigar e corrigir problemas. Com esta funcionalidade, obtém-se uma visão unificada dos alertas, incluindo alertas relacionados com ataques DDoS e as ações tomadas para mitigar o ataque em pouco tempo.
Existem dois alertas específicos que verá para qualquer deteção e mitigação de ataques DDoS:

- **Ataque DDoS detetado para IP Público**: Este alerta é gerado quando o serviço de proteção DDoS deteta que um dos seus endereços IP públicos é alvo de um ataque DDoS.
- **Ataque DDoS atenuado para IP público**: Este alerta é gerado quando um ataque ao endereço IP público foi atenuado.
Para ver os alertas, abra o **Centro de Segurança** no portal Azure. Sob **proteção contra** ameaças , selecione **alertas de segurança**. A imagem que se segue mostra um exemplo dos alertas de ataque do DDoS.

![Alerta DDoS no Centro de Segurança Azure](./media/manage-ddos-protection/ddos-alert-asc.png)

Os alertas incluem informações gerais sobre o endereço IP público que está sob ataque, informações de inteligência geo e ameaças, e medidas de reparação.

## <a name="validate-and-test"></a>Validar e testar

Para simular um ataque DDoS para validar os seus alertas, consulte [validar a deteção de DDoS](test-through-simulations.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

- Alertas de configuração através do Monitor Azure
- Configurar alertas através do portal
- Ver alertas no Centro de Segurança Azure
- Valide e teste os seus alertas

Para aprender a testar e simular um ataque DDoS, consulte o guia de testes de simulação:

> [!div class="nextstepaction"]
> [Testar através de simulações](test-through-simulations.md)
