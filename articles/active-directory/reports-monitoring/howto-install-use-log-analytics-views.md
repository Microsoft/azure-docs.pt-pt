---
title: Como instalar e utilizar as vistas de análise de registos [ Microsoft Docs
description: Saiba como instalar e utilizar as vistas de análise de registo para o Diretório Ativo Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b17026e4cfbe69e36c8e459aa259fe16b1c9d80d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74014424"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Instale e utilize as vistas de análise de registo para o Diretório Ativo Azure

As vistas de análise de registo sonícola sonâmlo do Azure Ative Directory ajudam-no a analisar e pesquisar os registos de atividade do Azure AD no seu inquilino Azure AD. Os registos de atividade da Azure AD incluem:

* Registos de [auditoria:](concept-audit-logs.md) O relatório de atividade de registos de auditoria dá-lhe acesso ao histórico de todas as tarefas que são realizadas no seu inquilino.
* Registos de login: Com o relatório de atividade de início de [sessão,](concept-sign-ins.md)pode determinar quem executou as tarefas que são reportadas nos registos de auditoria.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar as vistas de análise de registo, precisa de:

* Um espaço de trabalho log Analytics na sua subscrição Azure. Aprenda a criar um espaço de [trabalho log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Primeiro, complete os passos para encaminhar os registos de atividade da [AD Azure para o seu espaço](howto-integrate-activity-logs-with-log-analytics.md)de trabalho Log Analytics .
* Descarregue as vistas do [repositório GitHub](https://aka.ms/AADLogAnalyticsviews) para o seu computador local.

## <a name="install-the-log-analytics-views"></a>Instale as vistas de análise de registo

1. Navegue para o seu espaço de trabalho Log Analytics. Para isso, navegue primeiro para o [portal Azure](https://portal.azure.com) e selecione **Todos os serviços.** Escreva **o Log Analytics** na caixa de texto e selecione espaços de trabalho de Log **Analytics**. Selecione o espaço de trabalho para o quais encaminhou os registos de atividade, como parte dos pré-requisitos.
2. Selecione **'Ver Designer',** selecione **Importar** e, em seguida, selecione **Escolher Ficheiro** para importar as vistas do seu computador local.
3. Selecione as vistas que descarregou a partir dos pré-requisitos e selecione **Guardar** para salvar a importação. Faça isto para a vista de Eventos de Provisionamento de **Conta Azure AD** e a vista **Eventos de Entrada.**

## <a name="use-the-views"></a>Use as vistas

1. Navegue para o seu espaço de trabalho Log Analytics. Para isso, navegue primeiro para o [portal Azure](https://portal.azure.com) e selecione **Todos os serviços.** Escreva **o Log Analytics** na caixa de texto e selecione espaços de trabalho de Log **Analytics**. Selecione o espaço de trabalho para o quais encaminhou os registos de atividade, como parte dos pré-requisitos.

2. Uma vez no espaço de trabalho, selecione **Workspace Resumo**. Deve ver as seguintes três pontos de vista:

    * Eventos de Provisionamento de **Conta AD Azure**: Esta visão mostra relatórios relacionados com a atividade de fornecimento de auditoria, tais como o número de novos utilizadores aprovisionados e falhas de provisionamento, número de utilizadores atualizados e falhas de atualização e o número de utilizadores desprovisionados e correspondentes falhas.    
    * **Eventos de inscrição**: Esta visão mostra os relatórios mais relevantes relacionados com a monitorização da atividade de inscrição, tais como inscrições por aplicação, utilizador, dispositivo, bem como uma visão sumária que rastreia o número de inscrições ao longo do tempo.

3. Selecione qualquer uma destas vistas para entrar nos relatórios individuais. Também pode definir alertas sobre qualquer um dos parâmetros do relatório. Por exemplo, vamos alertar para cada vez que há um erro de inscrição. Para isso, primeiro selecione a visualização de **Eventos de Início** de Sessão, selecione **erros de início de hora** e, em seguida, selecione **Analytics** para abrir a página de detalhes, com a consulta real por trás do relatório. 

    ![Detalhes](./media/howto-install-use-log-analytics-views/details.png)


4. Selecione **'Definir Alerta'** e, em seguida, selecione Sempre que a pesquisa de **registo personalizada &lt;estiver indefinida&gt; ** sob a secção de critérios de **alerta.** Uma vez que queremos alertar sempre que houver um erro de inscrição, detete o **Limiar** da lógica de alerta padrão para **1** e, em seguida, selecione **Done**. 

    ![Configurar lógica de sinal](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Introduza um nome e descrição para o alerta e coloque a gravidade para **Aviso**.

    ![Criar regra](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Selecione o grupo de ação para alertar. Em geral, esta pode ser uma equipa que pretende notificar por e-mail ou mensagem de texto, ou pode ser uma tarefa automatizada usando webhooks, livros de execução, funções, aplicações lógicas ou soluções ITSM externas. Aprenda a criar e gerir grupos de [ação no portal Azure.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)

7. Selecione **Criar a regra** de alerta para criar o alerta. Agora será alertado sempre que houver um erro de inscrição.

## <a name="next-steps"></a>Passos seguintes

* [Como analisar registos de atividade com registos do Monitor Azure](howto-analyze-activity-logs-log-analytics.md)
* [Começar com registos do Monitor Azure no portal Azure](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)