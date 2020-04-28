---
title: Adicione e executar códigos de corte usando código inline
description: Aprenda a criar e executar códigos através da utilização de ações de código inline para tarefas automatizadas e fluxos de trabalho que cria com apps da Lógica Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: f7a134fd026b42d1666b8310b3fb0c10642c7bb0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75453503"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Adicione e execute códigos através do código inline em Aplicações Lógicas Azure

Quando quiser executar um código dentro da sua aplicação lógica, pode adicionar a ação **inline code** incorporada como um passo no fluxo de trabalho da sua aplicação lógica. Esta ação funciona melhor quando se quer executar código que se adequa a este cenário:

* Corre em JavaScript. Mais línguas em breve.
* Termina a correr em cinco segundos ou menos.
* Lida com dados até 50 MB de tamanho.
* Não requer trabalhar com as ações [ **variáveis** ](../logic-apps/logic-apps-create-variables-store-values.md), que ainda não são apoiadas.
* Utiliza a versão Node.js 8.11.1. Para mais informações, consulte [os objetos incorporados Standard](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects). 

  > [!NOTE]
  > A `require()` função não é suportada pela ação **Código Inline** para executar o JavaScript.

Esta ação executa o código e devolve a saída desse corte como um símbolo chamado **Result**, que pode suster em ações subsequentes na sua aplicação lógica. Para outros cenários em que pretende criar uma função para o seu código, tente [criar e chamar uma função Azure](../logic-apps/logic-apps-azure-functions.md) na sua aplicação lógica.

Neste artigo, a aplicação lógica de exemplo dispara quando um novo e-mail chega a uma conta do Office 365 Outlook. O código extrai e devolve quaisquer endereços de e-mail que apareçam no corpo de e-mail.

![Visão geral do exemplo](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* A aplicação lógica onde pretende adicionar o seu código, incluindo um gatilho. Se não tem uma aplicação lógica, consulte [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   A aplicação lógica de exemplo neste tópico usa este gatilho do Office 365 Outlook: **Quando um novo e-mail chega**

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) que está ligada à sua aplicação lógica

  > [!NOTE]
  > Certifique-se de que utiliza uma conta de integração adequada para o seu caso ou cenário de utilização. Por exemplo, as contas de integração [a nível livre destinam-se](../logic-apps/logic-apps-pricing.md#integration-accounts) apenas a cenários exploratórios e cargas de trabalho, não a cenários de produção, são limitadas na utilização e na produção, e não são suportadas por um acordo de nível de serviço (SLA). Outros níveis incorrem em custos, mas incluem suporte SLA, oferecem mais entrada, e têm limites mais elevados. Saiba mais sobre [os níveis](../logic-apps/logic-apps-pricing.md#integration-accounts)de conta de integração, [preços](https://azure.microsoft.com/pricing/details/logic-apps/)e [limites.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

## <a name="add-inline-code"></a>Adicionar código inline

1. Se ainda não o fez, no [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. No designer, adicione a ação **Inline Code** no local que deseja no fluxo de trabalho da sua aplicação lógica.

   * Para adicionar a ação no final do seu fluxo de trabalho, escolha **novo passo**.

   * Para adicionar a ação entre os passos existentes, mova o ponteiro do rato sobre a seta que liga esses passos. Escolha o sinal**+** de mais () e selecione **Adicionar uma ação**.

   Este exemplo adiciona a ação **Código Inline** sob o gatilho do Office 365 Outlook.

   ![Adicione um novo passo](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. Em **'Escolha uma ação**' na caixa de pesquisa, introduza "código inline" como filtro. A partir da lista de ações, selecione esta ação: **Execute o Código JavaScript**

   ![Selecione "Executar código JavaScript"](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   A ação aparece no designer e contém algum código de exemplo padrão, incluindo uma declaração de devolução.

   ![Ação do Código Inline com código de amostra padrão](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. Na caixa **Código,** elimine o código da amostra e introduza o código que pretende executar. Escreva código que colocaria dentro de um método, mas sem definir a assinatura do método. 

   Quando escreve uma palavra-chave reconhecida, a lista autocompleta aparece para que possa selecionar a partir de palavras-chave disponíveis, por exemplo:

   ![Lista autocompleta de palavra-chave](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Este código de exemplo snippet cria primeiro uma variável que armazena uma *expressão regular*, que especifica um padrão para combinar com o texto de entrada. O código cria então uma variável que armazena os dados do corpo de e-mail a partir do gatilho.

   ![Criar variáveis](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Para tornar os resultados do gatilho e das ações anteriores mais fáceis de referir, a lista de conteúdos dinâmicos aparece enquanto o cursor está dentro da caixa **código.** Para este exemplo, a lista mostra os resultados disponíveis do gatilho, incluindo o símbolo **do Corpo,** que agora pode selecionar.

   Depois de selecionar o símbolo **do Corpo,** a ação do `workflowContext` código inline resolve `Body` o símbolo a um objeto que refere o valor de propriedade do e-mail:

   ![Selecione resultado](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   Na caixa **código,** o seu corte pode `workflowContext` utilizar o objeto apenas para leitura como entrada. Este objeto tem subpropriedades que dão ao seu código acesso aos resultados do gatilho e ações anteriores no seu fluxo de trabalho.
   Para mais informações, consulte esta secção mais tarde neste tópico: [O gatilho de referência e a ação resultam no seu código](#workflowcontext).

   > [!NOTE]
   >
   > Se o seu código de snippet refere os nomes de ação que utilizam o operador do ponto (.) deve adicionar esses nomes de ação ao parâmetro [ **de ação** ](#add-parameters). Essas referências devem igualmente envolver os nomes de ação com parênteses quadrados ([]) e aspas, por exemplo:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   A ação do código inline `return` não requer uma `return` declaração, mas os resultados de uma declaração estão disponíveis para referência em ações posteriores através do **token resultado.** 
   Por exemplo, o código de corte devolve `match()` o resultado chamando a função, que encontra fósforos no corpo de e-mail contra a expressão regular. A ação **Compor** utiliza o **símbolo do resultado** para referir os resultados da ação do código inline e cria um único resultado.

   ![Aplicação lógica concluída](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Quando terminar, guarde a sua aplicação lógica.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>O gatilho de referência e a ação resultam no seu código

O `workflowContext` objeto tem esta estrutura, `trigger`que `workflow` inclui as, `actions`e subpropriedades:

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
| `actions` | Coleção de objetos | Resulte em objetos de ações que funcionam antes do seu código executar. Cada objeto tem um par *de valor-chave* onde a chave é o nome de uma `@actions('<action-name>')`ação, e o valor é equivalente a chamar as [ações() de função](../logic-apps/workflow-definition-language-functions-reference.md#actions) com . O nome da ação usa o mesmo nome de ação que é usado na definição de fluxo de trabalho subjacente, que substitui espaços (" ") no nome de ação por sublinhados (_). Este objeto proporciona acesso a valores de propriedade de ação a partir da execução atual de instância de fluxo de trabalho. |
| `trigger` | Objeto | O objeto de resultado do gatilho e equivalente a chamar a [função de gatilho()](../logic-apps/workflow-definition-language-functions-reference.md#trigger). Este objeto fornece acesso para desencadear valores de propriedade a partir da execução atual da instância de fluxo de trabalho. |
| `workflow` | Objeto | O objeto de fluxo de trabalho e equivalente à chamada de função de [fluxo de trabalho.](../logic-apps/workflow-definition-language-functions-reference.md#workflow) Este objeto proporciona acesso aos valores de propriedade do fluxo de trabalho, tais como o nome do fluxo de trabalho, id executar, e assim por diante, a partir da execução atual da instância de fluxo de trabalho. |
|||

No exemplo deste tópico, `workflowContext` o objeto tem estas propriedades a que o seu código pode aceder:

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

Em alguns casos, poderá ter de exigir explicitamente que a ação **do Código Inline** inclua resultados do gatilho ou ações específicas que o seu código refere como dependências adicionando os parâmetros **Trigger** ou **Actions.** Esta opção é útil para cenários em que os resultados referenciados não são encontrados no tempo de execução.

> [!TIP]
> Se planeia reutilizar o seu código, adicione referências às propriedades utilizando a caixa **código** para que o seu código inclua as referências simbólicas resolvidas, em vez de adicionar o gatilho ou ações como dependências explícitas.

Por exemplo, suponha que tem código que refere o resultado **SelectedOption** da ação de **e-mail** de aprovação enviar para o conector Do Outlook office 365. No momento da criação, o motor Logic Apps analisa o seu código para determinar se referiu algum gatilho ou resultados de ação e inclui esses resultados automaticamente. No tempo de execução, caso obtenha um erro de que o gatilho `workflowContext` ou resultado de ação referenciado não esteja disponível no objeto especificado, pode adicionar esse gatilho ou ação como uma dependência explícita. Neste exemplo, adiciona o parâmetro **de ações** e especifica que a ação do **Código Inline** inclui explicitamente o resultado da ação de **e-mail de aprovação enviar.**

Para adicionar estes parâmetros, abra a **lista de novos parâmetros E** selecione os parâmetros que deseja:

   ![Adicionar parâmetros](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parâmetro | Descrição |
   |-----------|-------------|
   | **Ações** | Inclua resultados de ações anteriores. Ver [Incluir resultados de ação](#action-results). |
   | **Acionador** | Inclua os resultados do gatilho. Ver [Incluir os resultados do gatilho](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Incluir resultados do gatilho

Se selecionar **Gatilhos,** é-lhe solicitado que inclua os resultados do gatilho.

* A partir da lista **de Gatilhos,** selecione **Sim**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Incluir resultados de ação

Se selecionar **Ações,** é solicitado pelas ações que pretende adicionar. No entanto, antes de começar a adicionar ações, precisa da versão do nome de ação que aparece na definição subjacente de fluxo de trabalho da aplicação lógica.

* Esta capacidade não suporta variáveis, loops e índices de iteração.

* Os nomes na definição de fluxo de trabalho da sua aplicação lógica usam um sublinhado (_), não um espaço.

* Para nomes de ação que utilizam o operador de pontos (.), incluem os operadores, por exemplo:

  `My.Action.Name`

1. Na barra de ferramentas de design, `actions` escolha a vista **código**, e procure no interior do atributo o nome de ação.

   Por exemplo, `Send_approval_email_` é o nome JSON para a ação **de e-mail de aprovação Enviar.**

   ![Encontre o nome da ação na JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Para voltar à vista do designer, na barra de ferramentas de vista de código, escolha **Designer**.

1. Para adicionar a primeira ação, no **Item** de Ações - 1 caixa, introduza o nome JSON da ação.

   ![Entrar na primeira ação](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Para adicionar outra ação, escolha **Adicionar novo item**.

## <a name="reference"></a>Referência

Para obter mais informações sobre a estrutura e sintaxe do **Código JavaScript** execute na definição subjacente de fluxo de trabalho da sua aplicação lógica utilizando a Linguagem definição de fluxo de trabalho, consulte a secção de [referência](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)desta ação .

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [Conectores para Aplicações Lógicas Azure](../connectors/apis-list.md)
