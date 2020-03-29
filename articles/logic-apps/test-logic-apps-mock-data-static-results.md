---
title: Testar aplicações lógicas com dados fictícios
description: Configurar resultados estáticos para testar aplicações lógicas com dados falsos sem afetar ambientes de produção
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: b71aae91f4a065b70537a300aa0bd7016edfd4b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790275"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Testar aplicativos lógicos com dados falsos através da criação de resultados estáticos

Ao testar as suas aplicações lógicas, pode não estar pronto para ligar ou aceder a apps, serviços e sistemas por várias razões. Normalmente, nestes cenários, você pode ter que executar diferentes caminhos de condição, forçar erros, fornecer corpos específicos de resposta de mensagem, ou até mesmo tentar saltar alguns passos. Ao configurar resultados estáticos para uma ação na sua aplicação lógica, pode ridicularizar os dados de saída a partir dessa ação. Permitir resultados estáticos numa ação não executa a ação, mas devolve os dados falsos.

Por exemplo, se configurar resultados estáticos para o Outlook 365 enviar ação de correio, o motor Logic Apps apenas devolve os dados falsos que especificou como resultados estáticos, em vez de ligar para o Outlook e enviar um e-mail.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende configurar resultados estáticos

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Configurar resultados estáticos

1. Se ainda não o fez, no [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic Apps Designer.

1. Sobre a ação em que pretende configurar resultados estáticos, siga estes passos: 

   1. No canto superior direito da ação, escolha o botão elipses (*...*) e selecione **resultado estático,** por exemplo:

      ![Selecione "Resultado estático" > "Ativar resultado estático"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Escolha **ativar resultado estático**. Para as propriedades (*) necessárias, especifique os valores de saída falsos que pretende devolver para a resposta da ação.

      Por exemplo, aqui estão as propriedades necessárias para a ação HTTP:

      | Propriedade | Descrição |
      |----------|-------------|
      | **Estado** | O estado da ação para regressar |
      | **Código de Estado** | O código de estado específico para devolver |
      | **Cabeçalhos** | O conteúdo do cabeçalho para devolver |
      |||

      ![Selecione "Ativar resultado estático"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Para introduzir os dados falsos no formato JavaScript Object Notation (JSON), escolha **a Switch para o modo JSON** (![Escolha "Mudar para o modo JSON").](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)

   1. Para propriedades opcionais, abra a lista de **campos opcionais Select** e selecione as propriedades que pretende gozar.

      ![Selecione propriedades opcionais](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Quando estiver pronto para salvar, escolha **Done**.

   No canto superior direito da ação, a barra de título![mostra agora](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)um ícone de teste de copos (Ícone para resultados estáticos), o que indica que permitiu resultados estáticos.

   ![Ícone mostrando resultados estáticos ativados](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Para encontrar execuções anteriores que utilizem dados falsos, consulte [localizar correções que usam resultados estáticos](#find-runs-mock-data) mais tarde neste tópico.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Reutilizar saídas anteriores

Se a sua aplicação lógica tiver uma execução anterior com saídas, pode reutilizar como saídas falsas, pode copiar e colar as saídas a partir dessa execução.

1. Se ainda não o fez, no [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic Apps Designer.

1. No menu principal da sua aplicação lógica, selecione **Visão Geral**.

1. Na secção **de história runs,** selecione a aplicação lógica executada que deseja.

1. No fluxo de trabalho da sua aplicação lógica, encontre e expanda a ação que tem as saídas que deseja.

1. Escolha a ligação **de saídas cruas do Show.**

1. Copie o objeto completo de notação de objetos JavaScript (JSON) ou a subsecção específica que pretende utilizar, por exemplo, a secção de saídas ou até mesmo asecção de cabeçalhos.

1. Siga os passos para abrir a caixa de **resultados estática** para a sua ação em [definir resultados estáticos](#set-up-static-results).

1. Depois da caixa de **resultados estática** abrir, escolha qualquer passo:

   * Para colar um objeto JSON completo, escolha **a Switch para o modo JSON** (![Escolha "Mudar para o modo JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)):

     ![Escolha "Mudar para o modo JSON" para obter um objeto completo](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Para colar apenas uma secção JSON, junto à etiqueta da secção, escolha **a Switch para o Modo JSON** para essa secção, por exemplo:

     ![Escolha "Mudar para o modo JSON" para saídas](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. No editor da JSON, cola o teu JSON anteriormente copiado.

   ![Modo JSON](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Quando tiver terminado, selecione **Concluído**. Ou, para voltar ao designer, escolha![o Modo Editor](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png) **da Switch** ( Escolha "Switch Editor Mode").

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Encontre correções que usam resultados estáticos

O histórico de executações da sua aplicação lógica identifica as corridas onde as ações usam resultados estáticos. Para encontrar estas corridas, siga estes passos:

1. No menu principal da sua aplicação lógica, selecione **Visão Geral**. 

1. No painel direito, sob a história de **Runs,** encontre a coluna **Resultados Estáticos.** 

   Qualquer execução que inclua ações com resultados tem a coluna **resultados estáticos** definida para **Ativado,** por exemplo:

   ![Executar história - coluna de resultados estáticos](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Para visualizar as ações que utilizam resultados estáticos, selecione o percurso que pretende onde a coluna **Resultados Estáticos** está definida para **ativada**.

   As ações que utilizam resultados estáticos mostram o ícone do copo de teste (Ícone![para resultados estáticos),](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)por exemplo:

   ![Executar história - ações que usam resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Desativar resultados estáticos

Desligar resultados estáticos não deita fora os valores da sua última configuração. Assim, quando ligar os resultados estáticos da próxima vez, pode continuar a usar os seus valores anteriores.

1. Encontre a ação onde pretende desativar as saídas estáticas. No canto superior direito da ação, escolha o![ícone do](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)copo de ensaio (Ícone para resultados estáticos).

   ![Desativar resultados estáticos](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Escolha **desativar o resultado** > estático**feito**.

   ![Desativar resultados estáticos](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Referência

Para obter mais informações sobre esta definição nas definições subjacentes ao fluxo de trabalho, consulte [resultados estáticos - Referência de Schema para Linguagem](../logic-apps/logic-apps-workflow-definition-language.md#static-results) de Definição de Fluxo de Trabalho e configuração de [tempo de execuçãoConfiguração.staticResult - Definições](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings) de configuração do tempo de execução

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [as aplicações da Lógica Azure](../logic-apps/logic-apps-overview.md)