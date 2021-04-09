---
title: Como criar alertas para o Azure Automation Change Tracking and Inventory
description: Este artigo diz como configurar alertas Azure para notificar sobre o estado das alterações detetadas por Change Tracking e Inventory.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/15/2020
ms.topic: conceptual
ms.openlocfilehash: 91be2f8641a061d009962cdcd03a8d56048594da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594510"
---
# <a name="how-to-create-alerts-for-change-tracking-and-inventory"></a>Como criar alertas para o Rastreio e Inventário de Alterações

Os alertas em Azure notificam-no proativamente dos resultados de trabalhos de runbook, problemas de saúde de serviço ou outros cenários relacionados com a sua conta de Automação. A Azure Automation não inclui regras de alerta pré-configuradas, mas pode criar as suas próprias com base em dados que gera. Este artigo fornece orientações sobre a criação de regras de alerta baseadas em alterações identificadas por Change Tracking e Inventory.

Se não estiver familiarizado com os alertas do Azure Monitor, consulte [a visão geral dos alertas no Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md) antes de começar. Para saber mais sobre os alertas que utilizam consultas de registo, consulte [alertas de registo no Azure Monitor](../../azure-monitor/alerts/alerts-unified-log.md).

## <a name="create-alert"></a>Criar alerta

O exemplo a seguir mostra que o ficheiro **c:\windows\system32\drivers\etc\hosts** foi modificado numa máquina. Este ficheiro é importante porque o Windows o utiliza para resolver os nomes dos anfitriões nos endereços IP. Esta operação tem precedência sobre o DNS, e pode resultar em problemas de conectividade. Também pode levar à reorientação do tráfego para sites maliciosos ou de outra forma perigosos.

![Gráfico mostrando a mudança de ficheiro dos anfitriões](./media/configure-alerts/changes.png)

Vamos usar este exemplo para discutir os passos para criar alertas sobre uma mudança.

1. Na página de **rastreio de Alterar** a partir da sua conta Dem automação, selecione Registar **Analytics**.

2. Na pesquisa de Logs, procure alterações de conteúdo no ficheiro dos **anfitriões** com a consulta `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` . Esta consulta procura alterações de conteúdo para ficheiros com nomes de caminhos totalmente qualificados que contenham a palavra `hosts` . Também pode solicitar um ficheiro específico alterando a parte do caminho para a sua forma totalmente qualificada, por exemplo, utilizando `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` .

3. Após a consulta retornar os seus resultados, selecione **Nova regra de alerta** na pesquisa de registo para abrir a página de **criação Alerta.** Também pode navegar para esta página através do **Azure Monitor** no portal Azure.

4. Verifique novamente a sua consulta e modifique a lógica de alerta. Neste caso, pretende que o alerta seja acionado se houver uma alteração detetada em todas as máquinas do ambiente.

    ![Alterar para consulta para rastrear alterações no ficheiro dos anfitriões](./media/configure-alerts/change-query.png)

5. Após a definição da lógica de alerta, atribua grupos de ação para executarem ações em resposta ao desencadeamento do alerta. Neste caso, estamos a configurar e-mails a serem enviados e um bilhete de Gestão de Serviços de TI (ITSM) a ser criado.

Siga os passos abaixo para configurar alertas para que saiba o estado de uma implementação de atualização. Se é novo nos alertas do Azure, consulte [a visão geral do Azure Alerts](../../azure-monitor/alerts/alerts-overview.md).

## <a name="configure-action-groups-for-your-alerts"></a>Configurar grupos de ação para os seus alertas

Uma vez configurados os alertas, pode criar um grupo de ação, que é um grupo de ações a utilizar em vários alertas. As ações podem incluir notificações de e-mail, runbooks, webhooks e muito mais. Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../../azure-monitor/alerts/action-groups.md).

1. Selecione um alerta e, em seguida, **selecione Criar Novos** em **Grupos de Ação**.

2. Insira um nome completo e um nome curto para o grupo de ação. A Atualização Gestão utiliza o nome curto ao enviar notificações utilizando o grupo especificado.

3. Em **Ações**, insira um nome que especifica a ação, por exemplo, **Notificação por E-mail**.

4. Para **o Tipo de Ação**, selecione o tipo apropriado, por exemplo, **mensagem de e-mail/SMS/Push/Voice**.

5. Selecione o ícone do lápis para editar os detalhes de ação.

6. Preencha o painel para o seu tipo de ação. Por exemplo, se utilizar **a mensagem de E-mail/SMS/Push/Voice** para enviar um e-mail, introduzir um nome de ação, selecionar a caixa de verificação **de e-mail,** introduzir um endereço de e-mail válido e, em seguida, selecionar **OK**.

    ![Configurar um grupo de ações de e-mail](./media/configure-alerts/configure-email-action-group.png)

7. No painel Adicionar grupo de ações, selecione **OK**.

8. Para um e-mail de alerta, pode personalizar o tema do e-mail. **Selecione Personalizar as ações** sob **regra Criar** e, em seguida, selecione **e-mail assunto**.

9. Quanto terminar, selecione **Criar regra de alerta**.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [os alertas no Azure Monitor.](../../azure-monitor/alerts/alerts-overview.md)

* Saiba mais [sobre consultas de registo](../../azure-monitor/logs/log-query-overview.md) para recuperar e analisar dados de um espaço de trabalho log analytics.

* Gerir [o uso e os custos com os Registos do Monitor Azure](../../azure-monitor/logs/manage-cost-storage.md) descreve como controlar os seus custos alterando o período de retenção de dados e como analisar e alertar sobre a utilização dos seus dados.