---
title: Criar variáveis para salvar os valores - Azure Logic Apps | Documentos da Microsoft
description: Para guardar e gerir valores através da criação de variáveis no Azure Logic Apps
services: logic-apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.topic: article
ms.date: 05/30/2018
ms.service: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: e525e5584e4835b0f2b73203c818c3f799b77cf5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58075719"
---
# <a name="create-variables-for-saving-and-managing-values-in-azure-logic-apps"></a>Criar variáveis para guardar e gerir os valores no Azure Logic Apps

Este artigo mostra como pode armazenar e trabalhar com valores em toda a sua aplicação lógica através da criação de variáveis. Por exemplo, as variáveis podem ajudar a contar o número de vezes que um loop for executado. Quando iterar sobre uma matriz ou a verificação de uma matriz para um item específico, pode utilizar uma variável para o número de índice para cada item da matriz de referência. 

Pode criar variáveis para tipos de dados, como o número inteiro, vírgula flutuante, booleano, cadeia de caracteres, matriz e objeto. Depois de criar uma variável, pode realizar outras tarefas, por exemplo:

* Obtenha ou referenciar o valor da variável.
* Aumentar ou diminuir a variável por um valor constante, também conhecido como *incremento* e *diminuir*.
* Atribua outro valor à variável.
* Inserir ou *acrescentar* o valor da variável como a última vez numa cadeia ou matriz.

Variáveis existem e estão globais apenas dentro da instância da aplicação lógica que as cria. Além disso, eles mantêm em qualquer iterações de loop dentro de uma instância da aplicação lógica. Ao fazer referência uma variável, utilize o nome da variável como o token, não a nome da ação, que é a forma usual de referenciam as saídas de uma ação. 

> [!IMPORTANT]
> Por predefinição, os ciclos num loop "Foreach" são executadas em paralelo. Quando utiliza variáveis em ciclos, executar o loop [sequencialmente](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) para variáveis devolvem resultados previsíveis. 

Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">Inscreva-se uma conta gratuita do Azure</a>. 

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este artigo, aqui estão os itens que precisa:

* A aplicação de lógica onde pretende criar uma variável 

  Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* R [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts) como a primeira etapa na sua aplicação lógica 

  Antes de poder adicionar ações para criar e trabalhar com variáveis, a aplicação lógica tem de começar com um acionador.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Inicializar variável

Pode criar uma variável e declarar o tipo de dados e o valor inicial - tudo dentro de uma ação na sua aplicação lógica. Só pode declarar variáveis a nível global, não está dentro âmbitos, condições e loops. 

1. Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a> ou o Visual Studio, abra a aplicação lógica no Estruturador da aplicação lógica. 

   Este exemplo utiliza o portal do Azure e uma aplicação lógica com um acionador existente.

2. Na sua aplicação lógica, no passo em que pretende adicionar uma variável, siga um destes passos: 

   * Para adicionar uma ação no último passo, escolha **novo passo** > **adicionar uma ação**.

     ![Adicionar ação](./media/logic-apps-create-variables-store-values/add-action.png)

   * Para adicionar uma ação entre etapas, mova o rato sobre a seta para a ligação para que apareça o sinal de adição (+). 
   Escolha o sinal de adição e, em seguida, escolha **adicionar uma ação**.

3. Na caixa de pesquisa, introduza "variables" como o filtro. Na lista de ações, selecione **Variables - Initialize variable**.

   ![Ação de seleção](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

4. Forneça estas informações para a variável:

   | Propriedade | Necessário | Value |  Descrição |
   |----------|----------|-------|--------------|
   | Name | Sim | <*variable-name*> | O nome para a variável incrementar | 
   | Type | Sim | <*variable-type*> | O tipo de dados para a variável | 
   | Value | Não | <*start-value*> | O valor iniciar da variável <p><p>**Sugestão**: Embora seja opcional, defina este valor como melhor prática, para que saiba sempre o valor inicial da variável. | 
   ||||| 

   ![Inicializar variável](./media/logic-apps-create-variables-store-values/initialize-variable.png)

5. Agora continue a adicionar as ações desejadas. Quando tiver terminado, na barra de ferramentas da estruturador, escolha **guardar**.

Se mudar a partir do designer para o editor de vista de código, eis a forma como o **Initialize variable** ação aparece dentro a definição de aplicação lógica, que está no formato de JavaScript Object Notation (JSON):

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

Seguem-se exemplos de alguns outros tipos de variáveis:

*Variável de cadeia de caracteres*

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

*Matriz de números inteiros*

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

*Matriz de cadeias de caracteres*

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

## <a name="get-the-variables-value"></a>Obter o valor da variável

Para obter ou fazer referência a conteúdo de uma variável, também pode utilizar o [variables() função](../logic-apps/workflow-definition-language-functions-reference.md#variables) no Estruturador da aplicação lógica e o editor de vista de código.
Ao fazer referência uma variável, utilize o nome da variável como o token, não a nome da ação, que é a forma usual de referenciam as saídas de uma ação. 

Por exemplo, esta expressão obtém os itens da variável de matriz [criado anteriormente neste artigo](#append-value) utilizando o **variables()** função. O **String ()** função devolve o conteúdo da variável no formato de cadeia de caracteres: `"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Incrementar variaável 

Para aumentar ou *incremento* adicionar uma variável por um valor constante, o **Variables - incrementar variaável** ação à sua aplicação lógica. Esta ação funciona apenas com as variáveis de número inteiro e número de vírgula flutuante.

1. No Estruturador da aplicação lógica, no passo em que deseja aumentar uma variável existente, escolha **novo passo** > **adicionar uma ação**. 

   Por exemplo, esta aplicação lógica já tem um acionador e uma ação que criamos uma variável. Por isso, adicione uma nova ação nestes passos:

   ![Adicionar ação](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Para adicionar uma ação entre os passos existentes, mova o rato sobre a seta para a ligação para que o sinal de adição (+) é apresentado. Escolha o sinal de adição e, em seguida, escolha **adicionar uma ação**.

2. Na caixa de pesquisa, introduza "variável de incremento" como o filtro. Na lista de ações, selecione **Variables - incrementar variaável**.

   ![Selecione a ação de "Variável de incremento"](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

3. Forneça estas informações para incrementar a variável:

   | Propriedade | Necessário | Value |  Descrição |
   |----------|----------|-------|--------------|
   | Name | Sim | <*variable-name*> | O nome para a variável incrementar | 
   | Value | Não | <*increment-value*> | O valor utilizado para incrementar a variável. O valor predefinido é um. <p><p>**Sugestão**: Embora seja opcional, defina este valor como melhor prática, para que saiba sempre o valor específico por incrementar a variável. | 
   |||| 

   Por exemplo: 
   
   ![Exemplo de valor de incremento](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

4. Quando tiver terminado, na barra de ferramentas da estruturador, escolha **guardar**. 

Se mudar a partir do designer para o editor de vista de código, eis a forma como o **variável de incremento** ação aparece dentro a definição de aplicação lógica, que está no formato JSON:

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

As variáveis são frequentemente utilizadas para contar o número de vezes que um loop for executado. Este exemplo mostra como criar e utilizar variáveis para esta tarefa através da criação de um loop que conte os anexos por e-mail.

1. No portal do Azure, crie uma aplicação lógica em branco. Adicione um acionador que verifica a existência de nova mensagem de e-mail e quaisquer anexos. 

   Este exemplo utiliza o acionador do Outlook do Office 365 para **quando chega um novo e-mail**. 
   Pode configurar este acionador a acionar apenas quando a mensagem de e-mail tem anexos.
   No entanto, pode utilizar qualquer conector que verifica a existência de novos e-mails com anexos, por exemplo, o conector do Outlook.com.

2. No acionador, escolha **Mostrar opções avançadas**. Para que o acionador de procurar anexos e passar esses anexos para o fluxo de trabalho da sua aplicação lógica, selecione **Sim** para estas propriedades:
   
   * **Tem Anexo** 
   * **Incluir Anexos** 

   ![Verifique a existência e incluir anexos](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

3. Adicionar a [ **Initialize variable** ação](#create-variable). Criar uma variável integer chamada **contagem** com um zero valor inicial.

   ![Adicionar ação para "Initialize variable"](./media/logic-apps-create-variables-store-values/initialize-variable.png)

4. Para percorrer cada anexo, adicione uma *para cada* loop escolhendo **novo passo** > **mais** > **adicionar um para cada**.

   ![Adicione um ciclo "for each"](./media/logic-apps-create-variables-store-values/add-loop.png)

5. No loop, clique no interior da **selecionar uma saída dos passos anteriores** caixa. Quando for apresentada a lista de conteúdo dinâmico, selecione **anexos**. 

   ![Selecionar "Attachments"](./media/logic-apps-create-variables-store-values/select-attachments.png)

   O **anexos** campo passa uma matriz que tenha os anexos de e-mail da saída do acionador em seu loop.

6. No loop "for each", selecione **adicionar uma ação**. 

   ![Selecione "Adicionar uma ação"](./media/logic-apps-create-variables-store-values/add-action-2.png)

7. Na caixa de pesquisa, introduza "variável de incremento" como o filtro. Na lista de ações, selecione **Variables - incrementar variaável**.

   > [!NOTE]
   > Certifique-se de que o **variável de incremento** ação aparece dentro do loop. Se for apresentada a ação fora do loop, arraste a ação para o loop.

8. Na **variável de incremento** ação, da **nome** lista, selecione o **contagem** variável. 

   ![Selecione a variável de "Contagem"](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

9. Sob o loop, adicione qualquer ação que envia-lhe o número de anexos. Na sua ação, incluem o valor a partir da **contagem** variável, por exemplo: 

   ![Adicionar uma ação que envia os resultados](./media/logic-apps-create-variables-store-values/send-email-results.png)

10. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**. 

### <a name="test-your-logic-app"></a>Teste a aplicação lógica

1. Se a sua aplicação lógica não estiver ativada, no menu da aplicação lógica, escolha **descrição geral**. Na barra de ferramentas, escolha **ativar**. 

2. Na barra de ferramentas da Estruturador da aplicação lógica, escolha **executar**. Este passo inicia manualmente a aplicação lógica.

3. Envie um e-mail com anexos de um ou mais para a conta de e-mail que utilizou neste exemplo.

   Este passo é acionado o acionador da aplicação lógica, que cria e executa uma instância de fluxo de trabalho da sua aplicação lógica.
   Como resultado, a aplicação lógica envia-lhe uma mensagem ou o e-mail que mostra o número de anexos do e-mail enviado por.

Se mudar a partir do designer para o editor de vista de código, eis a forma como o loop "for each" é apresentada com o **variável de incremento** ação dentro de sua definição da aplicação lógica, que está no formato JSON.

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

## <a name="decrement-variable"></a>Variável de diminuição

Para diminuir ou *diminuir* uma variável por um valor constante, siga os passos para [aumentar uma variável](#increment-value) exceto pelo fato de localizar e selecionar o **Variables - variável de diminuição**ação em vez disso. Esta ação funciona apenas com as variáveis de número inteiro e número de vírgula flutuante.

Aqui estão as propriedades para o **variável de diminuição** ação:

| Propriedade | Necessário | Value |  Descrição |
|----------|----------|-------|--------------|
| Name | Sim | <*variable-name*> | O nome para a variável diminuir | 
| Value | Não | <*increment-value*> | O valor para a variável de diminuição. O valor predefinido é um. <p><p>**Sugestão**: Embora seja opcional, defina este valor como melhor prática, para que sempre sabe o valor específico para a diminuição da variável. | 
||||| 

Se mudar a partir do designer para o editor de vista de código, eis a forma como o **variável de diminuição** ação aparece dentro a definição de aplicação lógica, que está no formato JSON.

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

## <a name="set-variable"></a>Definir variável

Para atribuir um valor diferente para uma variável existente, siga os passos para [aumentar uma variável](#increment-value) exceto que: 

1. Localize e selecione o **Variables - definir variável** ação em vez disso. 

2. Forneça o nome da variável e o valor que pretende atribuir. O novo valor e a variável tem de ter os mesmo tipo de dados.
O valor é necessário porque esta ação não tem um valor predefinido. 

Aqui estão as propriedades para o **definir variável** ação:

| Propriedade | Necessário | Value |  Descrição | 
|----------|----------|-------|--------------| 
| Name | Sim | <*variable-name*> | O nome para a variável alterar | 
| Value | Sim | <*new-value*> | O valor que pretende atribuir a variável. Ambos tem de ter os mesmo tipo de dados. | 
||||| 

> [!NOTE]
> A menos que esteja incrementando ou diminuição variáveis, alterar variáveis dentro de loops *poderá* criar resultados inesperados, uma vez que executam loops em paralelo ou ao mesmo tempo, por predefinição. Nesses casos, tente definir seu loop para são executados sequencialmente. Por exemplo, quando quiser referenciar o valor da variável dentro do loop e esperar o mesmo valor no início e no final dessa instância de loop, siga estes passos para alterar a forma como o loop for executado: 
>
> 1. No canto superior direito do seu ciclo, escolha o botão de reticências (...) e, em seguida, escolha **definições**.
> 
> 2. Sob **controlo de simultaneidade**, alterar a **substituir predefinido** na definição **no**.
>
> 3. Arrastar o **grau de paralelismo** controlo de deslize **1**.

Se mudar a partir do designer para o editor de vista de código, eis a forma como o **definir variável** ação aparece dentro a definição de aplicação lógica, que está no formato JSON. Este exemplo altera o valor atual da variável de "Contagem" a outro valor. 

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

## <a name="append-to-variable"></a>Acrescentar à variável

Para as variáveis que armazenam cadeias de caracteres ou matrizes, pode inserir ou *acrescentar* valor de uma variável como o último item nas cadeias de caracteres ou matrizes. Pode seguir os passos para [aumentar uma variável](#increment-value) , exceto que, em vez disso, a seguir estes passos: 

1. Localize e selecione uma destas ações com base em se a variável é uma cadeia de caracteres ou uma matriz: 

   * **Variáveis - acrescentar à variável da cadeia de caracteres**
   * **Variáveis - acrescentar à variável de matriz** 

2. Forneça o valor para acrescentar como o último item na matriz ou cadeia de caracteres. 
   Este valor é preciso. 

Aqui estão as propriedades para o **acrescentar a...**  ações:

| Propriedade | Necessário | Value |  Descrição | 
|----------|----------|-------|--------------| 
| Name | Sim | <*variable-name*> | O nome para a variável alterar | 
| Value | Sim | <*valor de acréscimo*> | O valor que pretende anexar, que pode ter qualquer tipo | 
|||||  

Se mudar a partir do designer para o editor de vista de código, eis a forma como o **acrescentar à variável da matriz** ação aparece dentro a definição de aplicação lógica, que está no formato JSON.
Este exemplo cria uma variável de matriz e adiciona outro valor como o último item da matriz. O resultado é uma variável atualizada que contém esta matriz: `[1,2,3,"red"]` 

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

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [conectores do Logic Apps](../connectors/apis-list.md)
