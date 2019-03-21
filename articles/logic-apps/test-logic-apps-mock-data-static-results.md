---
title: Testar aplicações lógicas com dados fictícios - Azure Logic Apps
description: Configurar resultados estáticos para aplicações lógicas com dados fictícios de teste sem afetar os ambientes de produção
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 03/18/2019
ms.openlocfilehash: 0fbe56ceeeba71bcbb5ef358cd66de15e36508fc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58165101"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Testar aplicações lógicas com dados fictícios ao configurar resultados estáticos

Ao testar as suas aplicações lógicas, poderá não estar preparado para realmente chamar ou aceder a aplicações, serviços e sistemas por vários motivos. Normalmente, nestes cenários, poderá ter de executar os caminhos de condição diferentes, forçar erros, fornecer corpos de resposta de mensagem específica ou até mesmo tentar ignorar alguns passos. Ao configurar resultados estáticos para uma ação na sua aplicação lógica, pode simular dados de saída da ação. Ativar os resultados estáticos de uma ação não executa a ação, mas devolve os dados fictícios em vez disso.

Por exemplo, se configurar resultados estáticos para o Outlook 365 enviar a ação de email, o motor do Logic Apps apenas retorna os dados fictícios que especificou como estáticos resultados, em vez de chamar o Outlook e enviar um e-mail.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação de lógica onde pretende configurar resultados estáticos

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Configurar resultados estáticos

1. Se ainda não o fez, no [portal do Azure](https://portal.azure.com), abra a aplicação lógica no Designer de aplicações lógicas.

1. Na ação onde pretende configurar resultados estáticos, siga estes passos: 

   1. No canto superior direito da ação, selecione as reticências (*...* ) e selecione **estático resultado**, por exemplo:

      ![Selecione "Resultado estático" > "Ativar estático resultado"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Escolher **ativar o resultado estático**. Para obter as propriedades necessárias (*), especifique os valores de saída fictício que queira retornar para resposta a ação.

      Por exemplo, aqui estão as propriedades necessárias para a ação de HTTP:

      | Propriedade | Descrição |
      |----------|-------------|
      | **Estado** | Estado da ação a devolver |
      | **Código de estado** | O código de estado específicos para devolver |
      | **Headers** (Cabeçalhos) | O conteúdo do cabeçalho para retornar |
      |||

      ![Selecionar "Ativar o resultado estático"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Para introduzir os dados fictícios no formato de JavaScript Object Notation (JSON), escolha **mudar para o modo de JSON** (![escolha "Comutador para modo JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)).

   1. Para as propriedades opcionais, abra a **selecionar campos opcionais** listar e selecionar as propriedades que pretende simular.

      ![Selecionar propriedades opcionais](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Quando estiver pronto para guardar, escolha **feito**.

   No canto no canto superior direito da ação, a barra de título mostra agora um ícone de beaker de teste (![ícone para resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)), que indica que ativou resultados estáticos.

   ![Mostrar ícone ativada resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Para obter execuções anteriores que utilizam dados fictícios, consulte [encontrar execuções que utilizam resultados estáticos](#find-runs-mock-data) mais adiante neste tópico.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Reutilizar saídas anteriores

Se a sua aplicação lógica tiver anterior executar com saídas que pode reutilizar como saídas fictícios, pode copiar e colar as saídas do que executar.

1. Se ainda não o fez, no [portal do Azure](https://portal.azure.com), abra a aplicação lógica no Designer de aplicações lógicas.

1. No menu principal da sua aplicação lógica, selecione **descrição geral**.

1. Na **histórico de execuções** secção, selecione a aplicação de lógica executar mesmo.

1. Fluxo de trabalho da sua aplicação lógica, localize e expanda a ação que tem saídas que quer.

1. Escolha o **Mostrar saídas em bruto** ligação.

1. Copie o objeto JavaScript Object Notation (JSON) completo ou a subsecção específica que pretende utilizar, por exemplo, a secção de saídas ou até mesmo apenas a secção de cabeçalhos.

1. Siga os passos para abrir o **resultado estático** caixa para a sua ação no [configurar resultados estáticos](#set-up-static-results).

1. Depois do **resultado estático** caixa é aberto, escolha qualquer passo:

   * Para colar um objeto JSON completo, escolha **mudar para o modo de JSON** (![escolha "Comutador para modo JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)):

     ![Escolher "Comutador para modo JSON" para o objeto completo](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Colar apenas uma seção do JSON, junto à etiqueta nessa seção, escolha **mudar para o modo JSON** daquela seção, por exemplo:

     ![Escolha "Comutador para modo JSON" para saídas](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. No editor de JSON, cole o JSON anteriormente copiado.

   ![Modo JSON](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Quando tiver terminado, selecione **Concluído**. Ou, para voltar ao estruturador, escolha **modo de edição de comutador** (![escolha "Modo de edição de comutador"](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)).

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Encontre as execuções que utilizam resultados estáticos

Histórico de execuções da sua aplicação lógica identifica as execuções de onde as ações utilizam resultados estáticos. Para encontrar essas execuções, siga estes passos:

1. No menu principal da sua aplicação lógica, selecione **descrição geral**. 

1. No painel direito, sob **histórico de execuções**, localizar o **resultados estático** coluna. 

   Qualquer execução que inclui ações com resultados tem o **resultados estático** coluna definida como **ativado**, por exemplo:

   ![Executar histórico - coluna de resultados estático](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Para ver as ações que utilizar resultados estáticos, selecione o run desejar onde o **resultados estático** coluna está definida como **ativado**.

   Ações que utilizar estáticos resultados mostram o beaker de teste (![ícone para resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)) ícone, por exemplo:

   ![Executar histórico - ações que utilizar resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Desativar resultados estáticos

Desativar resultados estáticos não jogar fora os valores de configuração do seu último. Então, quando ativar resultados estáticos da próxima vez, pode continuar a utilizar os seus valores anteriores.

1. Encontre a ação em que pretende desativar saídas estáticas. No canto superior direito da ação, escolha o ícone de beaker de teste (![ícone para resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)).

   ![Desativar resultados estáticos](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Escolher **desativar resultado estático** > **feito**.

   ![Desativar resultados estáticos](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [Azure Logic Apps](../logic-apps/logic-apps-overview.md)