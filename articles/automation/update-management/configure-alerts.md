---
title: Como criar alertas para a Azure Automation Update Management
description: Este artigo diz como configurar alertas do Azure para notificar sobre o estado das avaliações ou implementações de atualização.
services: automation
ms.subservice: update-management
ms.date: 03/15/2021
ms.topic: conceptual
ms.openlocfilehash: 224a7b5457a099fd763ac657349fc5497824ab76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601432"
---
# <a name="how-to-create-alerts-for-update-management"></a>Como criar alertas para a Gestão de Atualização

Os alertas em Azure notificam-no proativamente dos resultados de trabalhos de runbook, problemas de saúde de serviço ou outros cenários relacionados com a sua conta de Automação. A Azure Automation não inclui regras de alerta pré-configuradas, mas pode criar as suas próprias com base em dados que gera. Este artigo fornece orientações sobre a criação de regras de alerta utilizando as métricas incluídas na Gestão de Atualização.

## <a name="available-metrics"></a>Métricas disponíveis

A Azure Automation cria duas métricas distintas da plataforma relacionadas com a Gestão de Atualização que são recolhidas e reencaminhadas para o Azure Monitor. Estas métricas estão disponíveis para análise utilizando [o Metrics Explorer](../../azure-monitor/essentials/metrics-charts.md) e para alertar utilizando uma [regra de alerta de métricas.](../../azure-monitor/alerts/alerts-metric.md)

As duas métricas emitidas são:

* Funciona a máquina de implementação total de atualizações
* Execuções de implementação de atualização total

Quando usado para alertas, ambas as métricas suportam dimensões que transportam informações adicionais para ajudar a estender o alerta a um detalhe específico de implementação de atualização. A tabela seguinte mostra os detalhes sobre a métrica e as dimensões disponíveis ao configurar um alerta.

|Nome do sinal|Dimensões|Description
|---|---|---|
|`Total Update Deployment Runs`|- Nome de implementação de atualização<br>- Estado | Alertas sobre o estado geral de uma implementação de atualização.|
|`Total Update Deployment Machine Runs`|- Nome de implementação de atualização</br>- Estado</br>- Computador-alvo</br>- Atualizar o ID de execução de implementação    |Alertas sobre o estado de uma atualização direcionada a máquinas específicas.|

## <a name="create-alert"></a>Criar alerta

Siga os passos abaixo para configurar alertas para que saiba o estado de uma implementação de atualização. Se é novo nos alertas do Azure, consulte [a visão geral do Azure Alerts](../../azure-monitor/alerts/alerts-overview.md).

1. Na sua conta Demômes, selecione **Alertas** em **Monitorização** e, em seguida, selecione **Nova regra de alerta**.

1. Na página **'Criar' regra de alerta,** a sua conta Demôm automação já está selecionada como recurso. Se quiser alterá-lo, **selecione o recurso Editar**.

1. Na página Selecione uma página de recursos, escolha Contas de **Automação** a partir da lista de retirada do **tipo de recurso.**

1. Selecione a conta Automation que pretende utilizar e, em seguida, selecione **Fazer**.

1. **Selecione Adicionar a condição** para escolher o sinal apropriado para o seu requisito.

1. Para uma dimensão, selecione um valor válido da lista. Se o valor que deseja não estiver na lista, selecione **\+** ao lado da dimensão e escreva o nome personalizado. Em seguida, selecione o valor a procurar. Se pretender selecionar todos os valores para uma dimensão, selecione o botão **Selecione. \*** Se não escolher um valor para uma dimensão, a Gestão de Atualização ignora essa dimensão.

    ![Configurar lógica de sinal](./media/manage-updates-for-vm/signal-logic.png)

1. Em **lógica de alerta,** introduza valores nos campos de **agregação** e **limiar** de tempo e, em seguida, selecione **Fazer**.

1. Na página seguinte, insira um nome e uma descrição para o alerta.

1. Desaprova o campo **Severidade** para **Informational (Sev 2)** para uma execução bem sucedida ou **informativo (Sev 1)** para uma execução falhada.

    ![A screenshot mostra a secção de detalhes de alerta de Definição com o nome da regra de alerta, descrição e severidade realçados.](./media/manage-updates-for-vm/define-alert-details.png)

1. Selecione **Sim** para ativar a regra de alerta.

## <a name="configure-action-groups-for-your-alerts"></a>Configurar grupos de ação para os seus alertas

Uma vez configurados os alertas, pode criar um grupo de ação, que é um grupo de ações a utilizar em vários alertas. As ações podem incluir notificações de e-mail, runbooks, webhooks e muito mais. Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../../azure-monitor/alerts/action-groups.md).

1. Selecione um alerta e, em seguida, **selecione Adicionar grupos de ação** em **Ações**. Isto mostrará o **grupo de ação Selecione um grupo de ação para anexar a este painel de regras de alerta.**

   :::image type="content" source="./media/manage-updates-for-vm/select-an-action-group.png" alt-text="Utilização e custos estimados.":::

1. Selecione a caixa de verificação para o grupo Action para anexar e pressionar Select.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [os alertas no Azure Monitor.](../../azure-monitor/alerts/alerts-overview.md)

* Saiba mais [sobre consultas de registo](../../azure-monitor/logs/log-query-overview.md) para recuperar e analisar dados de um espaço de trabalho log analytics.

* Gerir [o uso e os custos com os Registos do Monitor Azure](../../azure-monitor/logs/manage-cost-storage.md) descreve como controlar os seus custos alterando o período de retenção de dados e como analisar e alertar sobre a utilização dos seus dados.