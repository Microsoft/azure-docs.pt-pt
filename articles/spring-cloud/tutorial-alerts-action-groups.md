---
title: 'Tutorial: Monitor Azure Spring Cloud recursos usando alertas e grupos de ação | Microsoft Docs'
description: Aprenda a usar alertas de Nuvem de primavera.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.custom: devx-track-java
ms.openlocfilehash: d12a48729616a5181f019f84f19779390e736cb4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879199"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Tutorial: Monitorar recursos da Nuvem de primavera usando alertas e grupos de ação

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

A Azure Spring Cloud alerta para o suporte de recursos com base em condições como armazenamento disponível, taxa de pedidos ou utilização de dados. Um alerta envia notificação quando as tarifas ou condições cumprem as especificações definidas.

Há dois passos para configurar um oleoduto de alerta: 
1. Crie um Grupo de Ação com as ações a serem tomadas quando um alerta é desencadeado, como e-mail, SMS, Runbook ou Webhook. Os Grupos de Ação podem ser reutilizadas entre diferentes alertas.
2. Estabeleça as regras de alerta. As regras ligam padrões métricos aos grupos de ação com base no recurso-alvo, métrica, condição, agregação de tempo, etc.

## <a name="prerequisites"></a>Pré-requisitos

Além dos requisitos da primavera de Azure, os procedimentos neste trabalho tutorial com uma instância de Nuvem de primavera Azure implantada.  Siga um [arranque rápido](spring-cloud-quickstart.md) para começar.

Os seguintes procedimentos inicializam tanto o **Grupo de Ação** como o **Alerta** a partir da opção **Alertas** no painel de navegação esquerdo de uma instância cloud de mola. (O procedimento também pode começar a partir da página geral do **Monitor** do portal Azure.) 

Navegue de um grupo de recursos para o seu exemplo de Cloud primavera. Selecione **Alertas** no painel esquerdo e, em seguida, **selecione Gerir as ações**:

![Página de grupo de recursos do portal screenshot](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Criar Grupo de Ação

Para iniciar o procedimento de inicialização de um novo **Grupo de Ação,** selecione **+ Adicione grupo de ação**.

![Portal screenshot Adicionar grupo de ação](media/alerts-action-groups/action-1.png)

Na página do **grupo de ação Add:**

 1. Especificar um **nome de grupo de ação** e nome **curto**.

 1. Especificar o grupo **de subscrição** e **recursos.**

 1. Especificar **o nome de ação**.

 1. Selecione **Tipo de Ação**.  Isto abrirá mais um painel sobre o direito de definir as medidas que serão tomadas na sua ativação.

 1. Defina a ação utilizando as opções no painel direito.  Este caso utiliza a notificação por e-mail.

 1. Clique em **OK** no painel de ação certo.

 1. Clique em **OK** no diálogo do **grupo de ação Add.** 

  ![Portal screenshot define ação](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Configurar alerta 

Os passos anteriores criaram um **Grupo de Ação** que utiliza o e-mail. Também pode utilizar notificações telefónicas, webhooks, funções Azure, etc. Os seguintes passos configuram um **Alerta**.

1. navegar de volta para a página **Alertas** e clicar **em Regras de Alerta de Gestão**.

   ![Portal screenshot definir alerta](media/alerts-action-groups/alerts-2.png)

1. Selecione o **recurso** para o alerta.

1. Clique **+ Nova regra de alerta**.

   ![Regra de alerta do Portal Screenshot](media/alerts-action-groups/alerts-3.png)

1. Na página **'Criar'** especifique o **RECURSO**.

1. A definição **CONDITION** fornece muitas opções para monitorizar os seus recursos **de Cloud primavera.**  Clique **em Adicionar** para abrir o painel de lógica de sinal de **configuração.**

1. Selecione uma condição. Este exemplo utiliza **a Percentagem de Utilização do CPU do Sistema.**

   ![Screenshot Portal nova regra de alerta 2](media/alerts-action-groups/alerts-3-1.png)

1. Percorra o painel de lógica de **sinal de configuração** para definir o **valor threshold** para monitorizar.

   ![Screenshot Portal nova regra de alerta 3](media/alerts-action-groups/alerts-3-2.png)

1. Clique em **Concluído**.

   Para obter mais informações sobre as condições disponíveis para monitorizar, consulte [as opções do portal do Utilizador](spring-cloud-concept-metrics.md#user-metrics-options).

1. Em **AÇÕES**, clique **em Selecionar grupo de ação**. A partir do painel **de ações** selecione o **Grupo de Ação** previamente definido .

   ![Screenshot Portal nova regra de alerta 4](media/alerts-action-groups/alerts-3-3.png) 

1. Desloque-se para baixo, e em **DETALHES ALERTA,** nomeie a regra de alerta.

1. Definir a **Severidade**.

1. Clique em **Criar regra de alerta**.

   ![Screenshot Portal nova regra de alerta 5](media/alerts-action-groups/alerts-3-4.png)

1. Verifique se a nova regra de alerta está ativada.

   ![Screenshot Portal nova regra de alerta 6](media/alerts-action-groups/alerts-4.png)

Uma regra também pode ser criada usando a página **Métricas:**

![Screenshot Portal nova regra de alerta 7](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial aprendeu a configurar alertas e grupos de ação para uma aplicação Azure Spring Cloud. Para saber mais sobre grupos de ação, consulte:

> [!div class="nextstepaction"]
> [Criar e gerir grupos de ações no portal do Azure](../azure-monitor/alerts/action-groups.md)

> [!div class="nextstepaction"]
> [Comportamento de alerta de SMS em grupos de ação](../azure-monitor/alerts/alerts-sms-behavior.md)