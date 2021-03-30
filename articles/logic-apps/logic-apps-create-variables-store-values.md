---
title: Criar e gerir variáveis para armazenar e passar valores
description: Aprenda a armazenar, gerir, usar e passar valores utilizando variáveis nas suas tarefas automatizadas e fluxo de trabalho que cria com apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: b486b94a74d98f5630bd0bf40ebf0864c2ec5ab8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91333907"
---
# <a name="store-and-manage-values-by-using-variables-in-azure-logic-apps"></a>Armazenar e gerir valores através de variáveis no Azure Logic Apps

Este artigo mostra como criar e trabalhar com variáveis que utiliza para armazenar valores na sua aplicação lógica. Por exemplo, as variáveis podem ajudá-lo a rastrear o número de vezes que um loop corre. Para iterar sobre uma matriz ou verificar uma matriz para um item específico, pode utilizar uma variável para referenciar o número de índice de cada item de matriz.

Pode criar variáveis para tipos de dados como inteiro, flutuar, boolean, string, array e objeto. Depois de criar uma variável, pode executar outras tarefas, por exemplo:

* Obter ou referenciar o valor da variável.
* Aumente ou diminua a variável por um valor constante, também conhecido como *incremento* e *decremento.*
* Atribua um valor diferente à variável.
* Insira ou *aplie* o valor da variável como o último item numa cadeia ou matriz.

As variáveis existem e são globais apenas dentro da instância de aplicação lógica que as cria. Além disso, persistem em qualquer iterações em loop dentro de uma instância de aplicação lógica. Quando se refere uma variável, use o nome da variável como símbolo, não o nome da ação, que é a forma habitual de fazer referência às saídas de uma ação.

> [!IMPORTANT]
> Por predefinição, os ciclos num loop "Para cada" funcionam em paralelo. Quando utilizar variáveis em loops, passe o loop [sequencialmente](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) para que as variáveis retornem resultados previsíveis.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver subscrição, [inscreva-se numa conta Azure gratuita.](https://azure.microsoft.com/free/)

* A aplicação lógica onde quer criar a variável

  Se é novo em aplicações lógicas, [reveja O que é Azure Logic Apps e](../logic-apps/logic-apps-overview.md) [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) como o primeiro passo na sua aplicação lógica

  Antes de poder adicionar ações para criar e trabalhar com variáveis, a sua aplicação lógica deve começar com um gatilho.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Inicializar variável

Pode criar uma variável e declarar o seu tipo de dados e valor inicial - tudo dentro de uma ação na sua aplicação lógica. Só é possível declarar variáveis a nível global, não dentro de âmbitos, condições e loops.

1. No [portal Azure](https://portal.azure.com) ou Visual Studio, abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o portal Azure e uma aplicação lógica com um gatilho existente.

1. Na sua aplicação lógica, sob o passo em que pretende adicionar uma variável, siga um destes passos: 

   * Para adicionar uma ação sob o último passo, selecione **Novo passo**.

     ![Screenshot que mostra a ação "New Step" selecionada na página "Logic app designer".](./media/logic-apps-create-variables-store-values/add-action.png)

   * Para adicionar uma ação entre os passos, mova o rato sobre a seta de ligação de modo a que apareça o sinal de mais **+** (). Selecione o sinal de mais e, em seguida, **selecione Adicione uma ação**.

1. Em **Escolha uma ação,** na caixa de pesquisa, introduza `variables` como filtro. Na lista de ações, **selecione Initialize variable.**

   ![Selecione ação](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

1. Forneça esta informação sobre a sua variável como descrito abaixo:

   | Propriedade | Necessário | Valor |  Descrição |
   |----------|----------|-------|--------------|
   | **Nome** | Yes | <*nome variável*> | O nome para a variável para incrementar |
   | **Tipo** | Yes | <*tipo variável*> | O tipo de dados para a variável |
   | **Valor** | No | <*valor inicial*> | O valor iniciar da variável <p><p>**Dica**: Embora opcional, desate este valor como uma boa prática para que conheça sempre o valor inicial da sua variável. |
   |||||

   Por exemplo:

   ![Inicializar variável](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Agora continua a adicionar as ações que queres. Quando terminar, na barra de ferramentas do designer, **selecione Save**.

Se mudar do designer para o editor de visualização de código, eis a forma como a ação **variável Initialize** aparece na definição da sua aplicação lógica, que está no formato JavaScript Object Notation (JSON):

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   }
},
```

> [!NOTE]
> Embora a ação **variável Initialize** tenha uma `variables` secção estruturada como uma matriz, a ação pode criar apenas uma variável de cada vez. Cada nova variável requer uma ação **variável inicializ.**

Aqui estão exemplos para outros tipos variáveis:

*Variável de corda*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myStringVariable",
               "type": "String",
               "value": "lorem ipsum"
          } ]
      },
      "runAfter": {}
   }
},
```

*Variável booleana*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myBooleanVariable",
               "type": "Boolean",
               "value": false
          } ]
      },
      "runAfter": {}
   }
},
```

*Matriz com inteiros*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": [1, 2, 3]
          } ]
      },
      "runAfter": {}
   }
},
```

*Matriz com cordas*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": ["red", "orange", "yellow"]
          } ]
      },
      "runAfter": {}
   }
},
```

<a name="get-value"></a>

## <a name="get-the-variables-value"></a>Obtenha o valor da variável

Para recuperar ou referenciar os conteúdos de uma variável, também pode utilizar a [função variáveis()](../logic-apps/workflow-definition-language-functions-reference.md#variables) no Logic App Designer e no editor de visualização de código. Ao fazer referência a uma variável, use o nome da variável como símbolo, não o nome da ação, que é a forma habitual de referenciar as saídas de uma ação.

Por exemplo, esta expressão obtém os itens da variável matriz [criada anteriormente neste artigo](#append-value) utilizando a `variables()` função. A `string()` função devolve o conteúdo da variável em formato de cadeia: `"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Variável incremento 

Para aumentar ou *incrementar* uma variável por um valor constante, adicione a ação **variável Incremento** à sua aplicação lógica. Esta ação funciona apenas com variáveis inteiros e flutuantes.

1. No Logic App Designer, sob o passo em que pretende aumentar uma variável existente, selecione **Novo passo**. 

   Por exemplo, esta aplicação lógica já tem um gatilho e uma ação que criou uma variável. Então, adicione uma nova ação sob estes passos:

   ![Adicionar ação](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Para adicionar uma ação entre os passos existentes, mova o rato sobre a seta de ligação para que apareça o sinal de mais (+). Selecione o sinal de mais e, em seguida, **selecione Adicione uma ação**.

1. Na caixa de pesquisa, introduza a "variável incremento" como filtro. Na lista de ações, selecione **Incremento variável**.

   ![Selecione ação "Incremento variável"](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

1. Forneça esta informação para incrementar a sua variável:

   | Propriedade | Necessário | Valor |  Descrição |
   |----------|----------|-------|--------------|
   | **Nome** | Yes | <*nome variável*> | O nome para a variável para incrementar |
   | **Valor** | No | <*incremento-valor*> | O valor utilizado para incrementar a variável. O valor predefinido é um. <p><p>**Dica**: Embora opcional, desa esta medida seja uma boa prática para que conheça sempre o valor específico para incrementar a sua variável. |
   ||||

   Por exemplo:

   ![Exemplo de valor de incremento](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

1. Quando terminar, na barra de ferramentas do designer, **selecione Save**.

Se mudar do designer para o editor de visualização de código, eis a forma como a ação **variável Incremento** aparece dentro da definição da sua aplicação lógica, que está no formato JSON:

```json
"actions": {
   "Increment_variable": {
      "type": "IncrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

## <a name="example-create-loop-counter"></a>Exemplo: Criar contador de loop

As variáveis são comumente usadas para contar o número de vezes que um loop corre. Este exemplo mostra como cria e utiliza variáveis para esta tarefa, criando um loop que conta os anexos num e-mail.

1. No portal Azure, crie uma aplicação lógica em branco. Adicione um gatilho que verifica novos e-mails e quaisquer anexos.

   Este exemplo utiliza o gatilho do Office 365 Outlook para **quando um novo e-mail chega**. Só pode configurar este gatilho quando o e-mail tiver anexos. No entanto, pode utilizar qualquer conector que verifique novos e-mails com anexos, como o conector Outlook.com.

1. No gatilho, para verificar se há anexos e passar esses anexos para o fluxo de trabalho da sua aplicação lógica, selecione **Sim** para estas propriedades:

   * **Tem Anexo**
   * **Incluir Anexos**

   ![Verifique e inclua anexos](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

1. Adicione a ação [ **variável Initialize**](#create-variable). Crie uma variável de inteiro nomeada `Count` que tenha um valor inicial zero.

   ![Adicione ação para "Inicializar variável"](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Para pedalar através de cada acessório, adicione um *para cada* laço.

   1. Sob a ação **variável Initialize,** selecione **Novo passo**.

   1. Em **Escolha uma ação**, selecione **Built-in**. Na caixa de pesquisa, introduza `for each` como filtro de pesquisa e selecione Para cada **um**.

      ![Adicione um laço "para cada"](./media/logic-apps-create-variables-store-values/add-loop.png)

1. No loop, clique no interior da Versão Selecionar uma saída da caixa **de passos anteriores.** Quando aparecer a lista de conteúdos dinâmicos, selecione **Anexos**.

   ![Selecionar "Attachments"](./media/logic-apps-create-variables-store-values/select-attachments.png)

   A propriedade **Attachments** passa por uma matriz, que tem os anexos de e-mail da saída do gatilho, para o seu loop.

1. No **For cada** ciclo, selecione **Adicione uma ação**.

   ![Selecione "Adicionar uma ação"](./media/logic-apps-create-variables-store-values/add-action-2.png)

1. Na caixa de pesquisa, introduza a "variável incremento" como filtro. Na lista de ações, selecione **Incremento variável**.

   > [!NOTE]
   > Certifique-se de que a ação **variável Incremento** aparece no interior do laço. Se a ação aparecer fora do circuito, arraste a ação para o laço.

1. Na ação **variável Incremento,** a partir da lista **Nome,** selecione a variável **Count.**

   ![Selecione a variável "Count"](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

1. Sob o loop, adicione qualquer ação que lhe envie o número de anexos. Na sua ação, inclua o valor da variável **Count,** por exemplo:

   ![Adicione uma ação que envia resultados](./media/logic-apps-create-variables-store-values/send-email-results.png)

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

### <a name="test-your-logic-app"></a>Teste o seu aplicativo de lógica

1. Se a sua aplicação lógica não estiver ativada, no menu de aplicações lógicas, selecione **Overview**. Na barra de ferramentas, selecione **Ativar**.

1. Na barra de ferramentas Logic App Designer, selecione **Run**. Este passo inicia manualmente a sua aplicação lógica.

1. Envie um e-mail com um ou mais anexos para a conta de e-mail que utilizou neste exemplo.

   Este passo dispara o gatilho da aplicação lógica, que cria e executa uma instância para o fluxo de trabalho da sua aplicação lógica. Como resultado, a aplicação lógica envia-lhe uma mensagem ou e-mail que mostra o número de anexos no e-mail que enviou.

Se mudar do designer para o editor de visualização de código, eis a forma como o **Loop Para aparece** juntamente com a ação **variável Incremento** dentro da definição de aplicação lógica, que está no formato JSON.

```json
"actions": {
   "For_each": {
      "type": "Foreach",
      "actions": {
         "Increment_variable": {
           "type": "IncrementVariable",
            "inputs": {
               "name": "Count",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['Attachments']",
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="decrement-value"></a>

## <a name="decrement-variable"></a>Variável de decremento

Para diminuir ou *decretar* uma variável por um valor constante, siga os passos para [aumentar uma variável,](#increment-value) exceto que encontre e selecione a ação **variável Decrement.** Esta ação funciona apenas com variáveis inteiros e flutuantes.

Aqui estão as propriedades para a ação **variável Decrement:**

| Propriedade | Necessário | Valor |  Descrição |
|----------|----------|-------|--------------|
| **Nome** | Yes | <*nome variável*> | O nome para a variável para decrédão | 
| **Valor** | No | <*incremento-valor*> | O valor para decrementar a variável. O valor predefinido é um. <p><p>**Dica**: Embora opcional, deslome este valor como uma boa prática para que conheça sempre o valor específico para decrementar a sua variável. |
||||| 

Se mudar do designer para o editor de visualização de código, eis a forma como a ação **variável Decrement** aparece dentro da definição da sua aplicação lógica, que está no formato JSON.

```json
"actions": {
   "Decrement_variable": {
      "type": "DecrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

<a name="assign-value"></a>

## <a name="set-variable"></a>Conjunto variável

Para atribuir um valor diferente a uma variável existente, siga os passos para [aumentar uma variável,](#increment-value) exceto que:

1. Em vez disso, encontre e selecione a ação **variável set.**

1. Forneça o nome e o valor variável que pretende atribuir. Tanto o novo valor como a variável devem ter o mesmo tipo de dados. O valor é necessário porque esta ação não tem um valor padrão.

Aqui estão as propriedades para a ação **variável set:**

| Propriedade | Necessário | Valor |  Descrição |
|----------|----------|-------|--------------|
| **Nome** | Yes | <*nome variável*> | O nome para a variável para mudar |
| **Valor** | Yes | <*novo valor*> | O valor que pretende atribuir a variável. Ambos devem ter o mesmo tipo de dados. |
||||| 

> [!NOTE]
> A menos que esteja a incrementar ou a decrementar variáveis, a alteração de variáveis dentro dos loops *pode* criar resultados inesperados porque os loops são executados em paralelo, ou simultaneamente, por padrão. Para estes casos, tente configurar o seu loop para funcionar sequencialmente. Por exemplo, quando pretender fazer referência ao valor variável dentro do laço e esperar o mesmo valor no início e no fim dessa instância de loop, siga estes passos para alterar a forma como o loop funciona: 
>
> 1. No canto superior direito do seu loop, selecione o botão elipse **(...**) e, em seguida, selecione **Definições**.
> 
> 2. Sob **o Controlo de Concurrency,** altere a definição de **Prespução de Substituição** para **On**.
>
> 3. Arraste o **grau de paralelismo** para **1**.

Se mudar do designer para o editor de visualização de código, eis a forma como a ação **variável set** aparece dentro da definição da sua aplicação lógica, que está no formato JSON. Este exemplo altera o `Count` valor atual da variável para outro valor.

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   },
   "Set_variable": {
      "type": "SetVariable",
      "inputs": {
         "name": "Count",
         "value": 100
      },
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="append-value"></a>

## <a name="append-to-variable"></a>Apêndice à variável

Para variáveis que armazenam cordas ou matrizes, pode inserir ou *anexar* o valor de uma variável como o último item nessas cordas ou matrizes. Pode seguir os passos para [aumentar uma variável,](#increment-value) exceto que siga estes passos: 

1. Encontre e selecione uma destas ações com base no facto de a sua variável ser uma cadeia ou uma matriz: 

   * **Apêndice à variável de cordas**
   * **Apêndice para matriz variável** 

1. Forneça o valor para anexar como o último item na cadeia ou matriz. Este valor é preciso.

Aqui estão as propriedades do **Apêndice para...** ações:

| Propriedade | Necessário | Valor |  Descrição |
|----------|----------|-------|--------------|
| **Nome** | Yes | <*nome variável*> | O nome para a variável para mudar |
| **Valor** | Yes | <*valor apêndice*> | O valor que quer anexar, que pode ter qualquer tipo |
|||||

Se mudar do designer para o editor de visualização de código, eis a forma como o **Apêndice para a** ação variável de matriz aparece dentro da definição da sua aplicação lógica, que está no formato JSON. Este exemplo cria uma variável de matriz, e adiciona outro valor como o último item na matriz. O seu resultado é uma variável atualizada que contém esta matriz: `[1,2,3,"red"]`

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
            "name": "myArrayVariable",
            "type": "Array",
            "value": [1, 2, 3]
         } ]
      },
      "runAfter": {}
   },
   "Append_to_array_variable": {
      "type": "AppendToArrayVariable",
      "inputs": {
         "name": "myArrayVariable",
         "value": "red"
      },
      "runAfter": {
        "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais [sobre os conectores de Aplicações Lógicas](../connectors/apis-list.md)
