---
title: Adicione e execute os snippets de código usando código inline
description: Aprenda a criar e executar códigos cortantes usando ações de código inline para tarefas automatizadas e fluxos de trabalho que cria com apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 12/07/2020
ms.custom: devx-track-js
ms.openlocfilehash: 3f88fa38d62778bc3c4c1e29571d1d0ae4eeb5ff
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179610"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Adicione e execute os snippets de código usando código inline em Azure Logic Apps

Quando pretender executar um código dentro da sua aplicação lógica, pode adicionar a ação incorporada do Código Inline como um passo no fluxo de trabalho da sua aplicação lógica. Esta ação funciona melhor quando se pretende executar um código que se enquadre neste cenário:

* Corre em JavaScript. Mais línguas em breve.

* Termina a correr em cinco segundos ou menos.

* Trata dados até 50 MB de tamanho.

* Não é necessário trabalhar com as [ações **das Variáveis**](../logic-apps/logic-apps-create-variables-store-values.md), que ainda não são apoiadas.

* Utiliza Node.js versão 8.11.1. Para mais informações, consulte [os objetos incorporados Standard.](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)

  > [!NOTE]
  > A `require()` função não é suportada pela ação Inline Code para executar o JavaScript.

Esta ação executa o corte de código e devolve a saída desse corte como um símbolo que é nomeado `Result` . Pode utilizar este token com ações subsequentes no fluxo de trabalho da sua aplicação lógica. Para outros cenários em que pretende criar uma função para o seu código, tente [criar e chamar uma função através de Funções Azure em vez de](../logic-apps/logic-apps-azure-functions.md) na sua aplicação lógica.

Neste artigo, a aplicação lógica de exemplo dispara quando um novo e-mail chega numa conta de trabalho ou escola. O código extrai e devolve quaisquer endereços de e-mail que apareçam no corpo de e-mail.

![Screenshot que mostra uma aplicação lógica de exemplo](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* A aplicação lógica onde pretende adicionar o seu corte de código, incluindo um gatilho. Se não tiver uma aplicação lógica, consulte [Quickstart: Crie a sua primeira aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

   O exemplo neste tópico usa o gatilho do Office 365 Outlook que é nomeado **Quando um novo e-mail chega.**

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) que está ligada à sua aplicação lógica.

  * Certifique-se de que utiliza uma conta de integração adequada para o seu caso ou cenário de uso.

    Por exemplo, as contas de integração [de nível livre destinam-se](../logic-apps/logic-apps-pricing.md#integration-accounts) apenas a cenários exploratórios e a cargas de trabalho, não a cenários de produção, são limitadas na utilização e na produção, e não são suportadas por um acordo de nível de serviço (SLA). Outros níveis incorrem em custos, mas incluem suporte SLA, oferecem mais produção, e têm limites mais elevados. Saiba mais sobre [os níveis de](../logic-apps/logic-apps-pricing.md#integration-accounts)conta de integração, [preços](https://azure.microsoft.com/pricing/details/logic-apps/)e [limites.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

   * Se não quiser utilizar uma conta de integração, pode tentar utilizar [a Azure Logic Apps Preview](logic-apps-overview-preview.md)e criar uma aplicação lógica a partir do tipo de recurso Logic App **(Preview).**

     Na pré-visualização de apps lógicas Azure, **o Código Inline** é agora nomeado **Inline Code Operations** juntamente com estas outras diferenças:

     * **Execute o Código JavaScript** é agora nomeado **Executar em linha JavaScript**.

     * Se utilizar o macOS ou o Linux, as ações inline Code Operations estão atualmente indisponíveis quando utiliza a extensão Azure Logic Apps (Preview) no Código do Estúdio Visual.

     * As ações de Operações de Código Inline [atualizaram os limites](logic-apps-overview-preview.md#inline-code-limits).

     Pode começar a partir de qualquer opção aqui:

     * Crie a aplicação lógica a partir do tipo de recurso **Logic App (Preview)** [utilizando o portal Azure.](create-stateful-stateless-workflows-azure-portal.md)

     * Crie um projeto para a aplicação lógica [utilizando o Código do Estúdio Visual e a extensão Azure Logic Apps (Preview)](create-stateful-stateless-workflows-visual-studio-code.md)

## <a name="add-inline-code"></a>Adicionar código inline

1. Se ainda não o fez, no [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. No designer, escolha onde adicionar a ação Inline Code no fluxo de trabalho da sua aplicação lógica.

   * Para adicionar a ação no final do seu fluxo de trabalho, selecione **Novo passo**.

   * Para adicionar a ação entre os passos, mova o ponteiro do rato sobre a seta que liga esses passos. Selecione o sinal de mais **+** () que aparece e selecione **Adicione uma ação**.

   Este exemplo adiciona a ação Inline Code ao abrigo do gatilho do Office 365 Outlook.

   ![Adicione o novo passo sob o gatilho](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. Em **Escolha uma ação,** na caixa de pesquisa, insira `inline code` . Na lista de ações, selecione a ação **denominada Executar Código JavaScript**.

   ![Selecione a ação "Executar código JavaScript"](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   A ação aparece no designer e, por padrão, contém algum código de amostra, incluindo uma `return` declaração.

   ![Ação de Código Inline com código de amostra padrão](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. Na caixa **código,** elimine o código de amostra e introduza o seu código. Escreve o código que colocarias dentro de um método, mas sem a assinatura do método.

   Se começar a escrever uma palavra-chave reconhecida, a lista de precontos automáticos aparece para que possa selecionar a partir de palavras-chave disponíveis, por exemplo:

   ![Lista de precontos automáticos de palavras-chave](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Este exemplo de código snippet cria primeiro uma variável que armazena uma *expressão regular,* que especifica um padrão para combinar no texto de entrada. O código cria então uma variável que armazena os dados do corpo de e-mail a partir do gatilho.

   ![Criar variáveis](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Para facilitar a referência aos resultados do gatilho e às ações anteriores, a lista de conteúdos dinâmicos aparece quando o cursor está dentro da caixa **código.** Para este exemplo, a lista mostra os resultados disponíveis do gatilho, incluindo o token **Do Corpo,** que agora pode selecionar.

   Depois de selecionar o token **Do Corpo,** a ação de código inline resolve o token a um `workflowContext` objeto que faz referência ao valor da propriedade do e-mail: `Body`

   ![Selecione resultado](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   Na caixa **Código,** o seu corte pode usar o objeto apenas de leitura `workflowContext` como entrada. Este objeto inclui propriedades que dão acesso ao seu código aos resultados do gatilho e ações anteriores no seu fluxo de trabalho. Para obter mais informações, consulte [o gatilho de referência e os resultados de ação no seu código](#workflowcontext) mais tarde neste tópico.

   > [!NOTE]
   > Se o seu código cortar os nomes de ação que utilizam o operador do ponto (.), deve adicionar esses nomes de ação ao parâmetro [ **Ações**](#add-parameters). Essas referências devem igualmente incluir os nomes de ação com parênteses quadrados ([]) e aspas, por exemplo:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   A ação Do Código Inline não requer uma `return` declaração, mas os resultados de uma `return` declaração estão disponíveis para referência em ações posteriores através do **token resultado.** Por exemplo, o corte de código devolve o resultado chamando a `match()` função, que encontra correspondências no corpo de e-mail contra a expressão regular. A **ação Compose** utiliza o **token Resultado** para fazer referência aos resultados da ação do código de linha e cria um único resultado.

   ![Aplicação lógica concluída](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Quando terminar, guarde a sua aplicação lógica.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Gatilho de referência e ação resulta no seu código

O `workflowContext` objeto tem esta estrutura, que inclui as `actions` `trigger` `workflow` subpropostas, e subpropostas:

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

Esta tabela contém mais informações sobre estas subpropriedades:

| Propriedade | Tipo | Descrição |
|----------|------|-------|
| `actions` | Coleção de objetos | Resulte em objetos de ações que correm antes do corte de código. Cada objeto tem um par *de valores-chave* onde a chave é o nome de uma ação, e o valor é equivalente a chamar a [função de ações](../logic-apps/workflow-definition-language-functions-reference.md#actions) com `@actions('<action-name>')` . O nome da ação usa o mesmo nome de ação que é usado na definição de fluxo de trabalho subjacente, que substitui espaços (" ") no nome de ação por sublinhados (_). Este objeto fornece acesso aos valores de propriedade de ação a partir da série de fluxo de trabalho atual executada. |
| `trigger` | Objeto | Resultado objeto do gatilho e equivalente à [função de gatilho.).](../logic-apps/workflow-definition-language-functions-reference.md#trigger) Este objeto fornece acesso aos valores de propriedade desencadeados a partir da execução atual do fluxo de trabalho. |
| `workflow` | Objeto | O objeto de fluxo de trabalho e equivalente a chamar a [função de fluxo de trabalho().](../logic-apps/workflow-definition-language-functions-reference.md#workflow) Este objeto fornece acesso aos valores de propriedade do fluxo de trabalho, como o nome do fluxo de trabalho, iD executado, e assim por diante, a partir da atual série de fluxos de trabalho executados. |
|||

No exemplo deste tópico, o `workflowContext` objeto tem estas propriedades que o seu código pode aceder:

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>Adicionar parâmetros

Em alguns casos, poderá ter de exigir explicitamente que a ação Do Código Inline inclua resultados do gatilho ou ações específicas que o seu código refere como dependências adicionando os parâmetros **Detonar** ou **Ações.** Esta opção é útil para cenários em que os resultados referenciados não são encontrados no tempo de execução.

> [!TIP]
> Se pretender reutilizar o seu código, adicione referências às propriedades utilizando a caixa **Código** para que o seu código inclua as referências simbólicas resolvidas, em vez de adicionar o gatilho ou ações como dependências explícitas.

Por exemplo, suponha que tem código que faz referência ao resultado da **Opção Selecionada** a partir da ação de **e-mail** de aprovação enviar para o conector Do Office 365 Outlook. No momento de criação, o motor De Aplicações Lógicas analisa o seu código para determinar se já referiu algum gatilho ou resultados de ação e inclui esses resultados automaticamente. No tempo de execução, caso obtenha um erro de que o gatilho ou o resultado de ação referenciados não esteja disponível no `workflowContext` objeto especificado, pode adicionar esse gatilho ou ação como uma dependência explícita. Neste exemplo, adiciona o parâmetro **Ações** e especifica que a ação Do Código Inline inclui explicitamente o resultado da ação de **e-mail de aprovação enviar.**

Para adicionar estes parâmetros, abra a nova lista **de parâmetros** e selecione os parâmetros que pretende:

   ![Adicionar parâmetros](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parâmetro | Descrição |
   |-----------|-------------|
   | **Ações** | Inclua resultados de ações anteriores. Ver [Incluir os resultados de ação.](#action-results) |
   | **Acionador** | Inclua os resultados do gatilho. Ver [Incluir os resultados do gatilho](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Incluir resultados do gatilho

Se selecionar **Triggers,** é-lhe solicitado que inclua os resultados do gatilho.

* Na lista **De disparo,** selecione **Sim**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Incluir resultados de ação

Se selecionar **Ações,** é solicitado para as ações que pretende adicionar. No entanto, antes de começar a adicionar ações, precisa da versão do nome de ação que aparece na definição subjacente do fluxo de trabalho da aplicação lógica.

* Esta capacidade não suporta variáveis, loops e índices de iteração.

* Os nomes na definição de fluxo de trabalho da sua aplicação lógica usam um sublinhado (_), não um espaço.

* Para nomes de ação que utilizem o operador de pontos (.), incluem-se esses operadores, por exemplo:

  `My.Action.Name`

1. Na barra de ferramentas do designer, selecione **a vista de código** e procure no interior do atributo o nome de `actions` ação.

   Por exemplo, `Send_approval_email_` é o nome JSON para a ação **de e-mail de aprovação enviar.**

   ![Encontre o nome de ação em JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Para voltar à vista do designer, na barra de ferramentas de visualização de código, selecione **Designer**.

1. Para adicionar a primeira ação, no **Item Ações - 1** caixa, insira o nome JSON da ação.

   ![Insira a primeira ação](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Para adicionar outra ação, **selecione Adicione novo item**.

## <a name="reference"></a>Referência

Para obter mais informações sobre a estrutura e sintaxe do **Código JavaScript** da execução na definição subjacente do fluxo de trabalho da sua aplicação lógica utilizando a Linguagem de Definição de Fluxo de Trabalho, consulte a secção de [referência](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)desta ação.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [Conectores para Apps Azure Logic](../connectors/apis-list.md)
