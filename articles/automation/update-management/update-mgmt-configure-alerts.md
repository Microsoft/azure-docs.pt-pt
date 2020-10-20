---
title: Como criar alertas para a Azure Automation Update Management
description: Este artigo diz como configurar alertas do Azure para notificar sobre o estado das avaliações ou implementações de atualização.
services: automation
ms.subservice: update-management
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: d8f832f0359bd8f9caf13008939f482b440faa1a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203182"
---
# <a name="how-to-create-alerts-for-update-management"></a>Como criar alertas para a Gestão de Atualização

Os alertas em Azure notificam-no proativamente dos resultados de trabalhos de runbook, problemas de saúde de serviço ou outros cenários relacionados com a sua conta de Automação. A Azure Automation não inclui regras de alerta pré-configuradas, mas pode criar as suas próprias com base em dados que gera. Este artigo fornece orientações sobre a criação de regras de alerta utilizando as métricas incluídas na Gestão de Atualização.

## <a name="available-metrics"></a>Métricas disponíveis

A Azure Automation cria duas métricas distintas da plataforma relacionadas com a Gestão de Atualização que são recolhidas e reencaminhadas para o Azure Monitor. Estas métricas estão disponíveis para análise utilizando [o Metrics Explorer](../../azure-monitor/platform/metrics-charts.md) e para alertar utilizando uma [regra de alerta de métricas.](../../azure-monitor/platform/alerts-metric.md)

As duas métricas emitidas são:

* Funciona a máquina de implementação total de atualizações
* Execuções de implementação de atualização total

Quando usado para alertas, ambas as métricas suportam dimensões que transportam informações adicionais para ajudar a estender o alerta a um detalhe específico de implementação de atualização. A tabela seguinte mostra os detalhes sobre a métrica e as dimensões disponíveis ao configurar um alerta.

|Nome do sinal|Dimensões|Descrição
|---|---|---|
|`Total Update Deployment Runs`|- Nome de implementação de atualização<br>- Estado | Alertas sobre o estado geral de uma implementação de atualização.|
|`Total Update Deployment Machine Runs`|- Nome de implementação de atualização</br>- Estado</br>- Computador-alvo</br>- Atualizar o ID de execução de implementação    |Alertas sobre o estado de uma atualização direcionada a máquinas específicas.|

## <a name="create-alert"></a>Criar alerta

Siga os passos abaixo para configurar alertas para que saiba o estado de uma implementação de atualização. Se é novo nos alertas do Azure, consulte [a visão geral do Azure Alerts](../../azure-monitor/platform/alerts-overview.md).

1. Na sua conta Demômes, selecione **Alertas** em **Monitorização**e, em seguida, selecione **Nova regra de alerta**.

2. Na página **'Criar' regra de alerta,** a sua conta Demôm automação já está selecionada como recurso. Se quiser alterá-lo, **selecione o recurso Editar**.

3. Na página Selecione uma página de recursos, escolha Contas de **Automação** a partir da lista de retirada do **tipo de recurso.**

4. Selecione a conta Automation que pretende utilizar e, em seguida, selecione **Fazer**.

5. **Selecione Adicionar a condição** para escolher o sinal apropriado para o seu requisito.

6. Para uma dimensão, selecione um valor válido da lista. Se o valor que deseja não estiver na lista, selecione **\+** ao lado da dimensão e escreva o nome personalizado. Em seguida, selecione o valor a procurar. Se pretender selecionar todos os valores para uma dimensão, selecione o botão **Selecione. \* ** Se não escolher um valor para uma dimensão, a Gestão de Atualização ignora essa dimensão.

    ![Configurar lógica de sinal](./media/update-mgmt-manage-updates-for-vm/signal-logic.png)

7. Em **lógica de alerta,** introduza valores nos campos de **agregação** e **limiar** de tempo e, em seguida, selecione **Fazer**.

8. Na página seguinte, insira um nome e uma descrição para o alerta.

9. Desaprova o campo **Severidade** para **Informational (Sev 2)** para uma execução bem sucedida ou **informativo (Sev 1)** para uma execução falhada.

    ![A screenshot mostra a secção de detalhes de alerta de Definição com o nome da regra de alerta, descrição e severidade realçados.](./media/update-mgmt-manage-updates-for-vm/define-alert-details.png)

10. Selecione **Sim** para ativar a regra de alerta.

## <a name="configure-action-groups-for-your-alerts"></a>Configurar grupos de ação para os seus alertas

Uma vez configurados os alertas, pode criar um grupo de ação, que é um grupo de ações a utilizar em vários alertas. As ações podem incluir notificações de e-mail, runbooks, webhooks e muito mais. Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../../azure-monitor/platform/action-groups.md).

1. Selecione um alerta e, em seguida, **selecione Criar Novos** em **Grupos de Ação**.

2. Insira um nome completo e um nome curto para o grupo de ação. A Atualização Gestão utiliza o nome curto ao enviar notificações utilizando o grupo especificado.

3. Em **Ações**, insira um nome que especifica a ação, por exemplo, **Notificação por E-mail**.

4. Para **o Tipo de Ação**, selecione o tipo apropriado, por exemplo, **Email/SMS/Push/Voice**.

5. Selecione **Editar detalhes**.

6. Preencha o painel para o seu tipo de ação. Por exemplo, se utilizar **o Email/SMS/Push/Voice,** insira um nome de ação, selecione a caixa de verificação **de e-mail,** introduza um endereço de e-mail válido e, em seguida, selecione **OK**.

    ![Configurar um grupo de ações de e-mail](./media/update-mgmt-manage-updates-for-vm/configure-email-action-group.png)

7. No painel Adicionar grupo de ações, selecione **OK**.

8. Para um e-mail de alerta, pode personalizar o tema do e-mail. **Selecione Personalizar as ações** sob **regra Criar**e, em seguida, selecione **e-mail assunto**.

9. Quanto terminar, selecione **Criar regra de alerta**.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [os alertas no Azure Monitor.](../../azure-monitor/platform/alerts-overview.md)

* Saiba mais [sobre consultas de registo](../../azure-monitor/log-query/log-query-overview.md) para recuperar e analisar dados de um espaço de trabalho log analytics.

* Gerir [o uso e os custos com os Registos do Monitor Azure](../../azure-monitor/platform/manage-cost-storage.md) descreve como controlar os seus custos alterando o período de retenção de dados e como analisar e alertar sobre a utilização dos seus dados.