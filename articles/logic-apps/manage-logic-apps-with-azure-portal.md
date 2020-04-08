---
title: Gerir aplicações lógicas no portal Azure
description: Editar, ativar, desativar ou eliminar aplicações lógicas utilizando o portal Azure.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/06/2020
ms.openlocfilehash: a35f34ed27299077dca78630511ea3c291caff60
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804978"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Gerir aplicações lógicas no portal Azure

Você pode gerir aplicações lógicas usando o [portal Azure](https://portal.azure.com) ou [Visual Studio](manage-logic-apps-with-visual-studio.md). Este artigo mostra como editar, desativar, ativar ou eliminar aplicações lógicas no portal Azure. Se é novo em Aplicações Lógicas Azure, veja [o que são as Aplicações Lógicas Azure?](logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma aplicação lógica existente. Para aprender a criar uma aplicação lógica no portal Azure, consulte [Quickstart: Create your first workflow utilizando as Aplicações Lógicas Azure - portal Azure](quickstart-create-first-logic-app-workflow.md).

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>Encontre as suas aplicações lógicas

Para encontrar e abrir a sua aplicação lógica, siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) com a sua conta Azure.

1. Na barra de pesquisa `logic apps`Azure, introduza, e selecione **Aplicações Lógicas.**

   ![Localizar e selecionar "Aplicações Lógicas"](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. Na página apps **lógicas,** encontre e selecione a aplicação lógica que pretende gerir.

   Após a abertura do painel de **visão geral** da aplicação lógica, pode filtrar a lista que aparece na página de **Aplicações Lógicas** desta forma:

   * Pesquisar por aplicativos lógicos pelo nome
   * Filtrar aplicativos lógicos por subscrição, grupo de recursos, localização e etiquetas
   * Aplicativos lógicos de grupo por grupo de recursos, tipo, subscrição e localização

## <a name="view-logic-app-properties"></a>Ver propriedades de aplicativos lógicos

1. No portal Azure, [encontre e abra a sua aplicação lógica.](#find-logic-app)

1. A partir do menu da sua aplicação lógica, em **Definições,** selecione **Propriedades**.

1. No painel **Propriedades,** pode ver e copiar as seguintes informações sobre a sua aplicação lógica:

   * **Nome**
   * **ID de recursos**
   * **Grupo de recursos**
   * **Localização**
   * **Tipo** 
   * **Nome de subscrição**
   * **ID de subscrição**
   * **Ponto final de acesso**
   * **Endereços IP de saída de tempo de execução**
   * **Endereços IP do ponto final de acesso**
   * **Endereços IP de saída do conector**

## <a name="disable-or-enable-logic-apps"></a>Desativar ou ativar aplicações lógicas

Pode ativar ou desativar uma [única aplicação lógica](#disable-enable-single-logic-app) ou [aplicações lógicas múltiplas ao mesmo tempo](#disable-or-enable-multiple-logic-apps) no portal Azure. Também pode [ativar ou desativar aplicações lógicas no Estúdio Visual.](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app)

Desativar a sua aplicação lógica afeta os seus casos de fluxo de trabalho e corre desta forma:

* Todas as corridas em curso e pendentes continuam até terminarem. Dependendo do número destas corridas, este processo pode demorar algum tempo.

* O motor Logic Apps não vai criar ou executar novos casos de fluxo de trabalho.

* O gatilho não dispara da próxima vez que as suas condições forem satisfeitas.

* O estado do gatilho lembra-se do ponto em que a aplicação lógica foi interrompida. Assim, se reativar a aplicação lógica, o gatilho dispara para todos os itens não processados desde a última execução.

  Para impedir que a sua aplicação lógica dispare em itens não processados desde a última execução, limpe o estado do gatilho antes de reativar a aplicação lógica:

  1. No portal Azure, [encontre e abra a sua aplicação lógica.](#find-logic-app)

  1. Editar qualquer parte do gatilho da aplicação lógica.

  1. Guarde as alterações. Este passo repõe o estado atual do seu gatilho.

  1. [Reative a sua aplicação lógica.](#disable-enable-single-logic-app)

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>Desativar ou ativar aplicação lógica única

1. No portal Azure, [encontre e abra a sua aplicação lógica.](#find-logic-app)

1. No menu da sua aplicação lógica, selecione **Visão Geral**. Escolha entre estas opções:

   * Na barra de ferramentas, selecione **Desativar**.

     ![Desativar aplicação lógica única no portal Azure](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     Se a sua aplicação lógica já estiver desativada, basta ver a opção **Enable.**

   * Na barra de ferramentas, selecione **Ativar**.

     ![Ativar aplicação lógica única no portal Azure](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     Se a sua aplicação lógica já estiver ativada, basta ver a opção **Desativar.** 

   O portal Azure mostra uma notificação na barra de ferramentas Azure principal que confirma se a sua operação foi bem sucedida ou falhou.

   ![Notificação para confirmar o estado da operação](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>Desativar ou ativar aplicações lógicas múltiplas

1. No portal Azure, [encontre as aplicações lógicas](#find-logic-app) que deseja desativar ou ativar.

1. Para verificar se uma aplicação lógica está atualmente ativada ou desativada, na página de **Aplicações Lógicas,** reveja a coluna **Status** para essa aplicação lógica. 

   ![Coluna de estado de Aplicações Lógicas](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   Se a coluna **'Estado'** não estiver visível, na barra de ferramentas **Logic Apps,** selecione **Tente pré-visualização**.

   ![Ligar a pré-visualização](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. Na coluna da caixa de verificação, selecione as aplicações lógicas que pretende desativar ou ativar. Na barra de ferramentas, selecione **Desativar** ou **ativar**.

   ![Ativar ou desativar aplicações lógicas múltiplas no portal Azure](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. Quando a caixa de confirmação aparecer, selecione **Sim** para continuar.

   O portal Azure mostra uma notificação na barra de ferramentas Azure principal que confirma se a sua operação foi bem sucedida ou falhou.

## <a name="delete-logic-apps"></a>Eliminar aplicações lógicas

Pode [eliminar uma única aplicação lógica](#delete-single-logic-app) ou [eliminar aplicações lógicas múltiplas ao mesmo tempo](#delete-multiple-logic-apps) no portal Azure. Também pode [eliminar a sua aplicação lógica no Estúdio Visual.](manage-logic-apps-with-visual-studio.md#delete-your-logic-app)

A apagar a sua aplicação lógica afeta os seus casos de fluxo de trabalho desta forma:

* Todas as corridas em curso e pendentes continuam até terminarem. Dependendo do número destas corridas, este processo pode demorar algum tempo.

* O motor Logic Apps não vai criar ou executar novos casos de fluxo de trabalho.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>Eliminar aplicativo de lógica única

1. No portal Azure, [encontre e abra a sua aplicação lógica.](#find-logic-app)

1. No menu da sua aplicação lógica, selecione **Visão Geral**. Na barra de ferramentas da sua aplicação lógica, selecione **Delete**.

   ![Na barra de ferramentas lógica, selecione "Eliminar"](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. Quando a caixa de confirmação aparecer, introduza o nome da sua aplicação lógica e selecione **Delete**.

   ![Confirme para apagar a sua aplicação lógica](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   O portal Azure mostra uma notificação na barra de ferramentas Azure principal que confirma se a sua operação foi bem sucedida ou falhou.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>Eliminar aplicações lógicas múltiplas

1. No portal Azure, [encontre as aplicações lógicas que pretende eliminar.](#find-logic-app)

1. Na coluna da caixa de verificação, selecione as aplicações lógicas que pretende eliminar. Na barra de ferramentas, selecione **Delete**.

   ![Eliminar aplicações lógicas múltiplas](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. Quando a caixa de `yes`confirmação aparecer, introduza , e selecione **Eliminar**.

   ![Confirme para eliminar as suas aplicações lógicas](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   O portal Azure mostra uma notificação na barra de ferramentas Azure principal que confirma se a sua operação foi bem sucedida ou falhou.

## <a name="next-steps"></a>Passos seguintes

* [Monitorizar aplicações lógicas](monitor-logic-apps.md)
