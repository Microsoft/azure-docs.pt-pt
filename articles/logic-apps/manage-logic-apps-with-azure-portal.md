---
title: Gerir aplicativos lógicos no portal Azure
description: Editar, ativar, desativar ou eliminar aplicações lógicas utilizando o portal Azure.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 07/20/2020
ms.openlocfilehash: d50f577a7170982be004cc8957114f79675fbc6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006103"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Gerir aplicativos lógicos no portal Azure

Pode gerir aplicações lógicas utilizando o [portal Azure](https://portal.azure.com) ou [o Visual Studio](manage-logic-apps-with-visual-studio.md). Este artigo mostra como editar, desativar, ativar ou eliminar aplicações lógicas no portal Azure. Se é novo em Azure Logic Apps, veja [o que é Azure Logic Apps?](logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma aplicação lógica existente. Para aprender a criar uma aplicação lógica no portal Azure, consulte [Quickstart: Crie o seu primeiro fluxo de trabalho utilizando apps Azure Logic - portal Azure](quickstart-create-first-logic-app-workflow.md).

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>Encontre as suas aplicações lógicas

Para encontrar e abrir a sua aplicação lógica, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com a sua conta do Azure.

1. Na barra de pesquisa Azure, insira `logic apps` e selecione **Aplicações Lógicas**.

   ![Screenshot do menu do portal Azure, com As Aplicações Lógicas pesquisadas e selecionadas na barra de pesquisa.](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. Na página **De Aplicações Lógicas,** encontre e selecione a aplicação lógica que pretende gerir.

   Depois de o painel de **visão geral** da aplicação lógica abrir, pode filtrar a lista que aparece na página **de Aplicações Lógicas** desta forma:

   * Pesquisa de aplicativos lógicos pelo nome
   * Filtrar aplicativos lógicos por subscrição, grupo de recursos, localização e tags
   * Aplicativos de lógica de grupo por grupo de recursos, tipo, subscrição e localização

## <a name="view-logic-app-properties"></a>Ver propriedades de aplicativos lógicos

1. No portal Azure, [encontre e abra a sua aplicação lógica.](#find-logic-app)

1. A partir do menu da sua aplicação lógica, em **Definições,** selecione **Properties**.

1. No painel **Propriedades,** pode ver e copiar as seguintes informações sobre a sua aplicação lógica:

   * **Nome**
   * **ID de recursos**
   * **Grupo de recursos**
   * **Localização**
   * **Tipo** 
   * **Nome da assinatura**
   * **ID da Subscrição**
   * **Ponto final de acesso**
   * **Endereços IP de saída de tempo de execução**
   * **Acesso endereços IP de ponto final**
   * **Endereços IP de saída do conector**

## <a name="disable-or-enable-logic-apps"></a>Desativar ou ativar aplicações lógicas

Pode ativar ou desativar uma [única aplicação lógica](#disable-enable-single-logic-app) ou [várias aplicações lógicas ao mesmo tempo](#disable-or-enable-multiple-logic-apps) no portal Azure. Também pode [ativar ou desativar aplicações lógicas no Visual Studio](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app).

Desativar a sua aplicação lógica afeta as suas instâncias de fluxo de trabalho e corre desta forma:

* Todas as corridas em curso e pendentes continuam até terminarem. Dependendo do número destas execuções, este processo pode levar algum tempo.

* O motor De Aplicações Lógicas não criará nem executará novos casos de fluxo de trabalho.

* O gatilho não dispara da próxima vez que as condições estiverem reunidas.

* O estado do gatilho lembra-se do ponto em que a aplicação lógica foi interrompida. Assim, se voltar a ativar a aplicação lógica, o gatilho dispara para todos os itens não processados desde a última execução.

  Para impedir que a sua aplicação lógica dispare em itens não processados desde a última execução, limpe o estado do gatilho antes de voltar a ativar a aplicação lógica:

  1. No portal Azure, [encontre e abra a sua aplicação lógica.](#find-logic-app)

  1. Editar qualquer parte do gatilho da aplicação lógica.

  1. Guarde as alterações. Este passo repõe o estado atual do seu gatilho.

  1. [Re-capacitar a sua aplicação lógica](#disable-enable-single-logic-app).

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>Desativar ou ativar uma aplicação lógica única

1. No portal Azure, [encontre e abra a sua aplicação lógica.](#find-logic-app)

1. No menu da sua aplicação lógica, selecione **Overview**. Escolha entre estas opções:

   * Na barra de ferramentas, **selecione Desativar**.

     ![Screenshot da barra de ferramentas da aplicação lógica, mostrando o botão de desativação selecionado.](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     Se a sua aplicação lógica já estiver desativada, só vê a opção **Enable.**

   * Na barra de ferramentas, selecione **Ativar**.

     ![Screenshot da barra de ferramentas da aplicação lógica, mostrando o botão Enable selecionado.](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     Se a sua aplicação lógica já estiver ativada, só vê a opção **Desativar.** 

   O portal Azure mostra uma notificação na barra de ferramentas Azure principal que confirma se a sua operação foi bem sucedida ou falhou.

   ![Screenshot do portal Azure, mostrando notificação para confirmar o estado da operação](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>Desativar ou ativar várias aplicações lógicas

1. No portal Azure, [encontre as aplicações lógicas](#find-logic-app) que pretende desativar ou ativar.

1. Para verificar se uma aplicação lógica está atualmente ativada ou desativada, na página **De Aplicações Lógicas,** reveja a coluna **Status** para essa aplicação lógica. 

   ![Screenshot da página do portal Azure Logic Apps, mostrando a lista de aplicações lógicas classificadas pela coluna Status.](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   Se a coluna **Status** não estiver visível, na barra de ferramentas **Logic Apps,** selecione **'Teste' de pré-visualização**.

   ![Screenshot da página de Aplicações lógicas do portal Azure, mostrando o botão de pré-visualização selecionado.](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. Na coluna caixa de verificação, selecione as aplicações lógicas que pretende desativar ou ativar. Na barra de ferramentas, selecione **Desativar** ou **Ativar**.

   ![Screenshot da página de Aplicações lógicas do portal Azure, mostrando botões de ativação e desativação para várias aplicações lógicas.](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. Quando a caixa de confirmação aparecer, selecione **Sim** para continuar.

   O portal Azure mostra uma notificação na barra de ferramentas Azure principal que confirma se a sua operação foi bem sucedida ou falhou.

## <a name="delete-logic-apps"></a>Eliminar aplicativos lógicos

Pode [eliminar uma única aplicação lógica](#delete-single-logic-app) ou [eliminar várias aplicações lógicas ao mesmo tempo](#delete-multiple-logic-apps) no portal Azure. Também pode [eliminar a sua aplicação lógica no Visual Studio.](manage-logic-apps-with-visual-studio.md#delete-your-logic-app)

A eliminação da sua aplicação lógica afeta os seus casos de fluxo de trabalho desta forma:

* Todas as corridas em curso e pendentes continuam até terminarem. Dependendo do número destas execuções, este processo pode levar algum tempo.

* O motor De Aplicações Lógicas não criará nem executará novos casos de fluxo de trabalho.

> [!NOTE]
> Se eliminar e recriar uma aplicação de lógica infantil, deve voltar a levantar a aplicação lógica dos pais. A aplicação infantil recriada terá metadados diferentes.
> Se não voltar a sair da aplicação lógica dos pais depois de recriar o seu filho, as suas chamadas para a aplicação de lógica infantil falharão com um erro de "não autorizado". Este comportamento aplica-se a aplicações lógicas de pais e filhos, por exemplo, aquelas que usam artefactos em contas de integração ou chamam funções de Azure.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>Eliminar aplicação lógica única

1. No portal Azure, [encontre e abra a sua aplicação lógica.](#find-logic-app)

1. No menu da sua aplicação lógica, selecione **Overview**. Na barra de ferramentas da sua aplicação lógica, selecione **Delete**.

   ![Screenshot da barra de ferramentas da aplicação lógica, mostrando o botão Delete selecionado.](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. Quando a caixa de confirmação aparecer, insira o nome da sua aplicação lógica e selecione **Delete**.

   ![Screenshot de Logic Apps solicitam para confirmar a eliminação de uma única aplicação lógica.](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   O portal Azure mostra uma notificação na barra de ferramentas Azure principal que confirma se a sua operação foi bem sucedida ou falhou.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>Eliminar várias aplicações lógicas

1. No portal Azure, [encontre as aplicações lógicas que pretende eliminar.](#find-logic-app)

1. Na coluna caixa de verificação, selecione as aplicações lógicas que pretende eliminar. Na barra de ferramentas, **selecione Delete**.

   ![Screenshot da página De Aplicações Lógicas, mostrando várias aplicações lógicas numa lista selecionada para eliminação.](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. Quando a caixa de confirmação aparecer, introduza `yes` e selecione **Delete**.

   ![Screenshot de Aplicações Lógicas solicitam para confirmar a eliminação de várias aplicações lógicas.](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   O portal Azure mostra uma notificação na barra de ferramentas Azure principal que confirma se a sua operação foi bem sucedida ou falhou.

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>Gerir versões de aplicativos lógicos

Pode utilizar o portal Azure para o controlo de versão das suas aplicações lógicas. Pode encontrar o histórico de versão da sua aplicação lógica e promover versões anteriores.

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>Localizar e ver versões anteriores

1. No portal Azure, [encontre a aplicação lógica que pretende gerir.](#find-logic-app)

1. No menu da sua aplicação lógica, em **Ferramentas de Desenvolvimento,** selecione **Versões**.

   ![Screenshot da aplicação lógica no portal Azure, mostrando a seleção da página de Versões sob ferramentas de desenvolvimento.](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. Selecione a **versão** da sua aplicação lógica para gerir a partir da lista. Pode introduzir o identificador **versão** na barra de pesquisa para filtrar a lista.

1. Na página da **versão History,** verá os detalhes da versão anterior no modo apenas de leitura. Pode selecionar entre os modos de **visualização** de Apps Lógicas e **Código.**

   ![Screenshot da página de versão Logic Apps History, mostrando opções de visualização de código e visualização do designer.](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>Promover versões anteriores

1. No histórico da versão da sua aplicação lógica, [encontre e selecione a versão que pretende promover.](#find-version-history)

1. Na página da **versão História,** selecione **Promover**.

   ![Screenshot do histórico da versão da logic app, mostrando botão para promover uma versão anterior.](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. Na página **De Android Apps Designer** que abre, edite a versão que está a promover conforme necessário. Pode alternar entre os modos de visualização **de Design** e **Código.** Também pode atualizar **parâmetros,** **modelos** e **conectores**.

   ![Screenshot do Logic Apps Designer, mostrando botão para promover uma versão anterior de uma aplicação lógica.](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. Para guardar quaisquer atualizações e terminar a promoção da versão anterior, **selecione Save**. (Ou, para cancelar as alterações, **selecione Descartar**.) 

   Quando [volta a ver o histórico da versão da sua aplicação lógica,](#find-version-history) a versão promovida aparece no topo da lista e tem um novo identificador.

## <a name="next-steps"></a>Passos seguintes

* [Monitorizar aplicações lógicas](monitor-logic-apps.md)
