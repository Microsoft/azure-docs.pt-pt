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
ms.openlocfilehash: 711d753203aeaeba50cea692053a37fcab2e9c7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93027708"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Testar aplicativos de lógica com dados falsos, configurando resultados estáticos

Ao testar as suas aplicações lógicas, pode não estar pronto para realmente ligar ou aceder a apps, serviços e sistemas por várias razões. Normalmente nestes cenários, você pode ter que executar diferentes caminhos de condição, erros de força, fornecer corpos específicos de resposta de mensagem, ou até mesmo tentar saltar alguns passos. Ao configurar resultados estáticos para uma ação na sua aplicação lógica, pode simular dados de saída dessa ação. Permitir resultados estáticos numa ação não executa a ação, mas devolve os dados falsos.

Por exemplo, se configurar resultados estáticos para o Outlook 365 enviar ação de correio, o motor De Aplicações Lógicas apenas devolve os dados falsos especificados como resultados estáticos, em vez de ligar para o Outlook e enviar um e-mail.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende configurar resultados estáticos

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Configurar resultados estáticos

1. Se ainda não o fez, no [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic Apps Designer.

1. Na ação onde pretende configurar resultados estáticos, siga estes passos: 

   1. No canto superior direito da ação, escolha o botão elipses *(...*) e selecione **o resultado estático,** por exemplo:

      ![Selecione "Resultado estático" > "Ativar resultado estático"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Escolha **Ativar O Resultado Estático**. Para as propriedades (*) necessárias, especifique os valores de saída simulados que deseja devolver para a resposta da ação.

      Por exemplo, aqui estão as propriedades necessárias para a ação HTTP:

      | Propriedade | Descrição |
      |----------|-------------|
      | **Estado** | O estado da ação para regressar |
      | **Código de Estado** | O código de estado específico para devolver |
      | **Cabeçalhos** | O conteúdo do cabeçalho para devolver |
      |||

      ![Selecione "Ativar o Resultado Estático"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Para introduzir os dados falsos no formato JavaScript Object Notation (JSON), escolha **o Modo Switch para json** ![ (Escolha "Mudar para o modo JSON" ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) ).

   1. Para propriedades opcionais, abra a lista **de campos opcionais Select** e selecione as propriedades que pretende ridicularizar.

      ![Selecione propriedades opcionais](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Quando estiver pronto para guardar, escolha **Feito**.

   No canto superior direito da ação, a barra de título mostra agora um ícone de copo de teste ![ (Ícone para resultados estáticos), ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) o que indica que permitiu resultados estáticos.

   ![Ícone mostrando resultados estáticos ativados](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Para encontrar execuções anteriores que utilizem dados falsos, consulte [Encontrar corre que utilize resultados estáticos](#find-runs-mock-data) mais tarde neste tópico.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Reutilizar saídas anteriores

Se a sua aplicação lógica tiver uma execução anterior com saídas que pode reutilizar como saídas falsas, pode copiar e colar as saídas dessa execução.

1. Se ainda não o fez, no [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic Apps Designer.

1. No menu principal da sua aplicação lógica, selecione **Overview**.

1. Na secção **história de Runs,** selecione a aplicação lógica executada que pretende.

1. No fluxo de trabalho da sua aplicação lógica, encontre e expanda a ação que tem as saídas que deseja.

1. Escolha a ligação **de saídas cruas do Show.**

1. Copie o objeto completo de Notação de Objetos JavaScript (JSON) ou a subsecção específica que pretende utilizar, por exemplo, a secção de saídas ou até mesmo apenas a secção de cabeçalhos.

1. Siga os passos para abrir a caixa **de resultados estática** para a sua ação em [Configurar resultados estáticos](#set-up-static-results).

1. Depois de abrir a caixa **de resultados estático,** escolha qualquer passo:

   * Para colar um objeto JSON completo, escolha **Mudar para o modo JSON** ![ (Escolha "Mudar para o modo JSON" ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) ):

     ![Escolha "Mudar para o modo JSON" para objeto completo](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Para colar apenas uma secção JSON, junto à etiqueta dessa secção, escolha **o Modo Switch para JSON** para esta secção, por exemplo:

     ![Escolha "Mudar para o modo JSON" para saídas](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. No editor da JSON, cole o seu JSON anteriormente copiado.

   ![Modo JSON](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Quando tiver terminado, selecione **Concluído**. Ou, para voltar ao designer, escolha o **Modo De Editor da Switch** ![ (Escolha o "Modo de Editor de Switch"). ](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Encontrar corridas que utilizem resultados estáticos

O histórico de execuções da sua aplicação lógica identifica as execuções onde as ações utilizam resultados estáticos. Para encontrar estas corridas, siga estes passos:

1. No menu principal da sua aplicação lógica, selecione **Overview**. 

1. No painel direito, na **história de Runs,** encontre a coluna **Resultados Estáticos.** 

   Qualquer execução que inclua ações com resultados tem a coluna **Resultados Estáticos** definida como **Ativada,** por exemplo:

   ![História de execução - coluna de resultados estáticos](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Para visualizar as ações que utilizam resultados estáticos, selecione a execução desejada onde a coluna **Resultados Estáticos** está definida para **Ativação**.

   As ações que utilizam resultados estáticos mostram o ícone do copo de teste ![ (Ícone para resultados estáticos), ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) por exemplo:

   ![Run history - ações que usam resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Desativar os resultados estáticos

Desligar os resultados estáticos não deita fora os valores da sua última configuração. Assim, quando ligar os resultados estáticos da próxima vez, pode continuar a usar os seus valores anteriores.

1. Encontre a ação onde pretende desativar as saídas estáticas. No canto superior direito da ação, escolha o ícone do copo de teste ![ (Ícone para resultados estáticos). ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)

   ![A screenshot mostra uma ação H T T P onde pode selecionar o ícone do copo de teste.](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Escolha **desativar o resultado estático**  >  **feito**.

   ![A screenshot mostra a opção Desativar o Resultado Estático que pode selecionar.](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Referência

Para obter mais informações sobre esta definição nas definições de fluxo de trabalho subjacentes, consulte [resultados estáticos - Referência de esquema para linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md#static-results) e [configuração de tempo de execução.staticResult - Configurações de configuração de tempo de execução](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a Azure Logic Apps](../logic-apps/logic-apps-overview.md)