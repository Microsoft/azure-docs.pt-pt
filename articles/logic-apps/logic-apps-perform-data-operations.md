---
title: Realizar operações em dados
description: Converter, gerir e manipular saídas e formatos de dados em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: baa6e5732221d120ff71217a3a86a942794c53f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84710376"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Realizar operações de dados em Azure Logic Apps

Este artigo mostra como pode trabalhar com dados nas suas aplicações lógicas adicionando ações para estas tarefas e muito mais:

* Criar tabelas a partir de matrizes.
* Crie matrizes de outras matrizes com base numa condição.
* Crie fichas fáceis de utilizar a partir de propriedades de objetos JavaScript Object (JSON) para que possa utilizar facilmente essas propriedades no seu fluxo de trabalho.

Se não encontrar a ação que deseja aqui, tente navegar nas várias [funções de manipulação de dados](../logic-apps/workflow-definition-language-functions-reference.md) que a Azure Logic Apps fornece.

Estas tabelas resumem as operações de dados que pode utilizar e são organizadas com base nos tipos de dados de origem em que as operações funcionam, mas cada descrição aparece alfabeticamente.

**Ações de matriz** 

Estas ações ajudam-no a trabalhar com dados em matrizes.

| Ação | Descrição |
|--------|-------------|
| [**Criar tabela CSV**](#create-csv-table-action) | Crie uma tabela de valor separado em vírgula (CSV) a partir de uma matriz. |
| [**Criar tabela HTML**](#create-html-table-action) | Crie uma tabela HTML a partir de uma matriz. |
| [**Matriz de filtro**](#filter-array-action) | Crie um subconjunto de matriz a partir de uma matriz com base no filtro ou condição especificados. |
| [**Participar**](#join-action) | Crie uma cadeia a partir de todos os itens numa matriz e separe cada item com o carácter especificado. |
| [**Selecione**](#select-action) | Crie uma matriz a partir das propriedades especificadas para todos os itens numa matriz diferente. |
||| 

**Ações da JSON**

Estas ações ajudam-no a trabalhar com dados no formato JavaScript Object Notation (JSON).

| Ação | Descrição |
|--------|-------------|
| [**Compose**](#compose-action) | Crie uma mensagem, ou cadeia, a partir de múltiplas entradas que podem ter vários tipos de dados. Em seguida, pode utilizar esta cadeia como uma única entrada, em vez de introduzir repetidamente as mesmas entradas. Por exemplo, pode criar uma única mensagem JSON a partir de várias entradas. |
| [**Parse JSON**](#parse-json-action) | Crie fichas de dados fáceis de utilizar para propriedades em conteúdo JSON para que possa utilizar mais facilmente as propriedades nas suas aplicações lógicas. |
|||

Para criar transformações JSON mais complexas, consulte [Executar transformações avançadas de JSON com modelos líquidos.](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* A aplicação lógica onde você precisa da operação para trabalhar com dados

  Se é novo em aplicações lógicas, [reveja O que é Azure Logic Apps e](../logic-apps/logic-apps-overview.md) [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) como o primeiro passo na sua aplicação lógica 

  As operações de dados estão disponíveis apenas como ações, por isso, antes de poder utilizar estas ações, inicie a sua aplicação lógica com um gatilho e inclua quaisquer outras ações necessárias para criar as saídas que pretende.

<a name="compose-action"></a>

## <a name="compose-action"></a>Composição

Para construir uma única saída, como um objeto JSON a partir de múltiplas entradas, pode utilizar a ação **Compose.** As suas entradas podem ter vários tipos, tais como inteiros, Booleans, arrays, objetos JSON e qualquer outro tipo nativo que a Azure Logic Apps suporta, por exemplo, binário e XML. Em seguida, pode utilizar a saída em ações que se seguem após a ação **Compose.** A ação **Compose** também pode salvá-lo de introduzir repetidamente as mesmas entradas enquanto constrói o fluxo de trabalho da sua aplicação lógica.

Por exemplo, você pode construir uma mensagem JSON a partir de múltiplas variáveis, tais como variáveis de cordas que armazenam os primeiros nomes e apelidos das pessoas, e uma variável inteiro que armazena a idade das pessoas. Aqui, a ação **Compose** aceita estas entradas:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

e cria esta saída:

`{"age":35,"fullName":"Owens,Sophie"}`

Para experimentar um exemplo, siga estes passos utilizando o Logic App Designer. Ou, se preferir trabalhar no editor de visualização de código, pode copiar o exemplo **Compose** e Inicializar definições de ação **variável** deste artigo na definição subjacente do fluxo de trabalho da sua própria aplicação lógica: Exemplos de [código de operação de dados - Compor](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. No [portal Azure](https://portal.azure.com) ou Visual Studio, abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o portal Azure e uma aplicação lógica com um gatilho **de Recorrência** e várias ações **variáveis Inicialize.** Estas ações são criadas para criar duas variáveis de corda e uma variável inteiro. Quando mais tarde testar a sua aplicação lógica, pode executar manualmente a sua aplicação sem esperar que o gatilho dispare.

   ![Início da aplicação lógica da amostra para ação "Compor"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. Na sua aplicação lógica onde pretende criar a saída, siga um destes passos: 

   * Para adicionar uma ação sob o último passo, selecione **Novo passo**.

     ![Selecione "Novo passo" para ação "Compor"](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * Para adicionar uma ação entre os passos, mova o rato sobre a seta de ligação para que apareça o sinal de mais **+** (). Selecione o sinal de mais e, em seguida, **selecione Adicione uma ação**.

1. Em **Escolha uma ação,** na caixa de pesquisa, introduza `compose` como filtro. Na lista de ações, selecione a ação **Compose.**

   ![Selecione ação "Compor"](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. Na caixa **Entradas,** forneça as entradas desejada para criar a saída.

   Para este exemplo, quando clica dentro da caixa **'Inputs',** aparece a lista de conteúdos dinâmicos para que possa selecionar as variáveis previamente criadas:

   ![Selecione entradas para usar para ação "Compor"](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Aqui está o exemplo acabado **Compor** ação: 

   ![Exemplo terminado para ação "Compor"](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

Para obter mais informações sobre esta ação na definição de fluxo de trabalho subjacente, consulte a ação [Compose](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action).

### <a name="test-your-logic-app"></a>Teste o seu aplicativo de lógica

Para confirmar se a ação Compose cria os **resultados esperados,** envie-lhe uma notificação que inclua a saída da ação **Compose.**

1. Na sua aplicação lógica, adicione uma ação que lhe possa enviar os resultados da ação **Compose.**

1. Nessa ação, clique em qualquer lugar que queira que os resultados apareçam. Quando a lista de conteúdos dinâmicos abrir, sob a ação **Compose,** selecione **Output**.

   Este exemplo utiliza a **ação enviar um email** e inclui os campos **de Saída** no corpo e assunto do e-mail:

   ![Campos de "saída" para a ação "Compor"](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. Agora, executar manualmente a sua aplicação lógica. Na barra de ferramentas do designer, selecione **Run**.

   Com base no conector de e-mail que usou, aqui estão os resultados que obtém:

   ![E-mail com resultados de ação "Compor"](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Criar ação de mesa CSV

Para criar uma tabela de valor separado em vírgula (CSV) que tenha as propriedades e valores dos objetos JavaScript Object Notation (JSON) numa matriz, utilize a ação da **tabela Create CSV.** Em seguida, pode utilizar a tabela resultante em ações que seguem a ação da **tabela Create CSV.**

Se preferir trabalhar no editor de visualização de código, pode copiar o exemplo **Criar tabela CSV** e rubricar definições de ação **variável** deste artigo na definição subjacente do fluxo de trabalho da sua própria aplicação lógica: Exemplos de [código de operação de dados - Criar tabela CSV](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. No [portal Azure](https://portal.azure.com) ou Visual Studio, abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o portal Azure e uma aplicação lógica com um gatilho **de Recorrência** e uma ação **variável Inicialize.** A ação é criada para criar uma variável cujo valor inicial é uma matriz que tem algumas propriedades e valores em formato JSON. Quando mais tarde testar a sua aplicação lógica, pode executar manualmente a sua aplicação sem esperar que o gatilho dispare.

   ![Início da aplicação lógica da amostra para ação "Criar mesa CSV"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. Na sua aplicação lógica onde pretende criar a tabela CSV, siga um destes passos: 

   * Para adicionar uma ação sob o último passo, selecione **Novo passo**.

     ![Selecione "Novo passo" para a ação "Criar tabela CSV"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Para adicionar uma ação entre os passos, mova o rato sobre a seta de ligação para que apareça o sinal de mais **+** (). Selecione o sinal de mais e, em seguida, **selecione Adicione uma ação**.

1. Em **Escolha uma ação,** na caixa de pesquisa, introduza `create csv table` como filtro. A partir da lista de ações, selecione a ação da **tabela Create CSV.**

   ![Selecione ação "Criar mesa CSV"](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. Na caixa **From,** forneça a matriz ou expressão que deseja para criar a tabela.

   Para este exemplo, quando clica dentro da caixa **'A',** aparece a lista de conteúdos dinâmicos para que possa selecionar a variável previamente criada:

   ![Selecione a saída da matriz para criar a tabela CSV](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > Para criar fichas fáceis de utilizar para as propriedades em objetos JSON para que possa selecionar essas propriedades como entradas, utilize o [Parse JSON](#parse-json-action) antes de chamar a ação da **tabela Create CSV.**

   Aqui está o exemplo acabado Criar ação **de mesa CSV:** 

   ![Exemplo final para ação "Criar tabela CSV"](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

### <a name="customize-table-format"></a>Personalizar formato de mesa

Por predefinição, a propriedade **Colunas** está definida para criar automaticamente as colunas de tabela com base nos itens de matriz. Para especificar os cabeçalhos e valores personalizados, siga estes passos:

1. Abra a lista **de Colunas** e selecione **Custom**.

1. Na propriedade **Header,** especifique o texto do cabeçalho personalizado para utilizar.

1. Na propriedade **Value,** especifique o valor personalizado a utilizar.

Para devolver valores da matriz, pode utilizar a [ `item()` função](../logic-apps/workflow-definition-language-functions-reference.md#item) com a ação da **tabela Create CSV.** Num `For_each` loop, pode utilizar a [ `items()` função](../logic-apps/workflow-definition-language-functions-reference.md#items).

Por exemplo, suponha que você quer colunas de mesa que tenham apenas os valores de propriedade e não os nomes de propriedade de um conjunto. Para devolver apenas estes valores, siga estes passos para trabalhar à vista do designer ou à vista de código. Aqui está o resultado que este exemplo devolve:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Trabalho na vista do designer

Na ação, mantenha a coluna **do Cabeçalho** vazia. Em cada linha na coluna **Value,** desrefera cada propriedade de matriz que você deseja. Cada linha em **Valor** devolve todos os valores para a propriedade de matriz especificada e torna-se uma coluna na sua tabela.

1. Under **Value**, em cada linha que deseja, clique dentro da caixa de edição para que apareça a lista de conteúdos dinâmicos.

1. Na lista de conteúdos dinâmicos, selecione **Expression**.

1. No editor de expressão, insira esta expressão que especifica o valor da propriedade de matriz que deseja, e selecione **OK**.

   `item()?['<array-property-name>']`

   Por exemplo:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Dereferência "Descrição" para "Criar tabela CSV"](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. Repita os passos anteriores para cada propriedade de matriz que deseja. Quando terminar, a sua ação parece-se com este exemplo:

   ![função "item()" em "Criar tabela CSV"](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. Para resolver expressões em versões mais descritivas, mude para a visualização de código e volte à vista do designer e, em seguida, reabra a ação colapsada:

   A ação **da tabela Create CSV** aparece agora como este exemplo:

   !["Criar mesa CSV" - expressões resolvidas, sem cabeçalhos](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>Trabalhar na vista de código

Na definição JSON da ação, dentro da `columns` matriz, definir a `header` propriedade para uma corda vazia. Para cada `value` propriedade, desrefera cada propriedade de matriz que você deseja.

1. Na barra de ferramentas do designer, selecione **Code view**.

1. No editor de código, na matriz da `columns` ação, adicione a propriedade vazia e esta expressão para cada coluna de `header` `value` valores de matriz que deseja:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Por exemplo:

   ```json
   "Create_CSV_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "CSV",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Mude de volta para a vista do designer, e reabra a ação colapsada.

   A ação **da tabela Create CSV** aparece agora como este exemplo, e as expressões resolveram versões mais descritivas:

   !["Criar mesa CSV" - expressões resolvidas e sem cabeçalhos](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

Para obter mais informações sobre esta ação na definição de fluxo de trabalho subjacente, consulte a [ação da Tabela.](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action)

### <a name="test-your-logic-app"></a>Teste o seu aplicativo de lógica

Para confirmar se a ação da **tabela Create CSV** cria os resultados esperados, envie-se uma notificação que inclua a saída da ação da **tabela Create CSV.**

1. Na sua aplicação lógica, adicione uma ação que lhe possa enviar os resultados da ação da **tabela Create CSV.**

1. Nessa ação, clique em qualquer lugar que queira que os resultados apareçam. Quando a lista de conteúdos dinâmicos abrir, sob a ação da **tabela Create CSV,** selecione **Output**. 

   Este exemplo utiliza o Office 365 Outlook **Enviar por email e** incluir o campo **Output** no corpo do e-mail:

   ![Campos de "saída" para a ação "Criar tabela CSV"](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. Agora, executar manualmente a sua aplicação lógica. Na barra de ferramentas do designer, selecione **Run**.

   Com base no conector de e-mail que usou, aqui estão os resultados que obtém:

   ![E-mail com resultados de ação "Criar tabela CSV"](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Criar ação de mesa HTML

Para criar uma tabela HTML que tenha as propriedades e valores de objetos JavaScript Object Notation (JSON) numa matriz, utilize a ação de **mesa Create HTML.** Em seguida, pode utilizar a tabela resultante em ações que seguem a ação da **tabela Create HTML.**

Se preferir trabalhar no editor de visualização de código, pode copiar o exemplo **Criar tabela HTML** e rubricar definições de ação **variável** deste artigo na definição subjacente do fluxo de trabalho da sua própria aplicação lógica: Exemplos de [código de operação de dados - Criar tabela HTML](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. No [portal Azure](https://portal.azure.com) ou Visual Studio, abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o portal Azure e uma aplicação lógica com um gatilho **de Recorrência** e uma ação **variável Inicialize.** A ação é criada para criar uma variável cujo valor inicial é uma matriz que tem algumas propriedades e valores em formato JSON. Quando mais tarde testar a sua aplicação lógica, pode executar manualmente a sua aplicação sem esperar que o gatilho dispare.

   ![Iniciar app lógica de amostra para "Criar tabela HTML"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. Na sua aplicação lógica onde pretende criar uma tabela HTML, siga um destes passos:

   * Para adicionar uma ação sob o último passo, selecione **Novo passo**.

     ![Selecione "Novo passo" para a ação "Criar tabela HTML"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Para adicionar uma ação entre os passos, mova o rato sobre a seta de ligação para que apareça o sinal de mais **+** (). Selecione o sinal de mais e, em seguida, **selecione Adicione uma ação**.

1. Em **Escolha uma ação,** na caixa de pesquisa, introduza `create html table` como filtro. A partir da lista de ações, selecione a ação da **tabela Create HTML.**

   ![Selecione ação "Criar tabela HTML"](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. Na caixa **From,** forneça a matriz ou expressão que deseja para criar a tabela.

   Para este exemplo, quando clica dentro da caixa **'A',** aparece a lista de conteúdos dinâmicos para que possa selecionar a variável previamente criada:

   ![Selecione a saída da matriz para criar a tabela HTML](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > Para criar fichas fáceis de utilizar para as propriedades em objetos JSON para que possa selecionar essas propriedades como entradas, utilize o [Parse JSON](#parse-json-action) antes de chamar a ação da **tabela Create HTML.**

   Aqui está o exemplo acabado Criar ação **de mesa HTML:**

   ![Exemplo final para "Criar tabela HTML"](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

### <a name="customize-table-format"></a>Personalizar formato de mesa

Por predefinição, a propriedade **Colunas** está definida para criar automaticamente as colunas de tabela com base nos itens de matriz. Para especificar os cabeçalhos e valores personalizados, siga estes passos:

1. Abra a lista **de Colunas** e selecione **Custom**.

1. Na propriedade **Header,** especifique o texto do cabeçalho personalizado para utilizar.

1. Na propriedade **Value,** especifique o valor personalizado a utilizar.

Para devolver valores da matriz, pode utilizar a [ `item()` função](../logic-apps/workflow-definition-language-functions-reference.md#item) com a ação de **tabela Create HTML.** Num `For_each` loop, pode utilizar a [ `items()` função](../logic-apps/workflow-definition-language-functions-reference.md#items).

Por exemplo, suponha que você quer colunas de mesa que tenham apenas os valores de propriedade e não os nomes de propriedade de um conjunto. Para devolver apenas estes valores, siga estes passos para trabalhar à vista do designer ou à vista de código. Aqui está o resultado que este exemplo devolve:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Trabalho na vista do designer

Na ação, mantenha a coluna **do Cabeçalho** vazia. Em cada linha na coluna **Value,** desrefera cada propriedade de matriz que você deseja. Cada linha em **Valor** devolve todos os valores para a propriedade especificada e torna-se uma coluna na sua tabela.

1. Under **Value**, em cada linha que deseja, clique dentro da caixa de edição para que apareça a lista de conteúdos dinâmicos.

1. Na lista de conteúdos dinâmicos, selecione **Expression**.

1. No editor de expressão, insira esta expressão que especifica o valor da propriedade de matriz que deseja, e selecione **OK**.

   `item()?['<array-property-name>']`

   Por exemplo:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Propriedade de dereferência em ação "Create HTML table"](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. Repita os passos anteriores para cada propriedade de matriz que deseja. Quando terminar, a sua ação parece-se com este exemplo:

   ![Função "item()" em "Criar tabela HTML"](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. Para resolver expressões em versões mais descritivas, mude para a visualização de código e volte à vista do designer e, em seguida, reabra a ação colapsada:

   A ação da **tabela Create HTML** aparece agora como este exemplo:

   !["Create HTML table" - expressões resolvidas, sem cabeçalhos](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>Trabalhar na vista de código

Na definição JSON da ação, dentro da `columns` matriz, definir a `header` propriedade para uma corda vazia. Para cada `value` propriedade, desrefera cada propriedade de matriz que você deseja.

1. Na barra de ferramentas do designer, selecione **Code view**.

1. No editor de código, na matriz da `columns` ação, adicione a propriedade vazia e esta expressão para cada coluna de `header` `value` valores de matriz que deseja:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Por exemplo:

   ```json
   "Create_HTML_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "HTML",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Mude de volta para a vista do designer, e reabra a ação colapsada.

   A ação da **tabela Create HTML** aparece agora como este exemplo, e as expressões resolveram versões mais descritivas:

   !["Create HTML table" - expressões resolvidas e sem cabeçalhos](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

Para obter mais informações sobre esta ação na definição de fluxo de trabalho subjacente, consulte a [ação da Tabela.](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action)

### <a name="test-your-logic-app"></a>Teste o seu aplicativo de lógica

Para confirmar se a ação da **tabela Create HTML** cria os resultados esperados, envie a si mesmo uma notificação que inclui a saída da ação da tabela Create **HTML.**

1. Na sua aplicação lógica, adicione uma ação que lhe possa enviar os resultados da ação da **tabela Create HTML.**

1. Nessa ação, clique em qualquer lugar que queira que os resultados apareçam. Quando a lista de conteúdos dinâmicos abrir, sob a ação da **tabela Create HTML,** selecione **Output**. 

   Este exemplo utiliza o Office 365 Outlook **Enviar por email e** incluir o campo **Output** no corpo do e-mail:

   ![Campos de "saída" para "Criar tabela HTML"](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > Ao incluir a saída da tabela HTML numa ação de e-mail, certifique-se de que define a propriedade **É HTML** para **Sim** nas opções avançadas da ação de e-mail. Desta forma, a ação por e-mail forma corretamente a tabela HTML.

1. Agora, executar manualmente a sua aplicação lógica. Na barra de ferramentas do designer, selecione **Run**.

   Com base no conector de e-mail que usou, aqui estão os resultados que obtém:

   ![E-mail com resultados "Criar tabela HTML"](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Ação de matriz de filtro

Para criar uma matriz menor que tenha itens, que satisfaçam critérios específicos, a partir de uma matriz existente, utilize a ação da **matriz do filtro.** Em seguida, pode utilizar o conjunto filtrado em ações que se seguem após a ação da matriz do **filtro.**

> [!NOTE]
> Qualquer texto de filtro que utilize no seu estado é sensível a casos. Além disso, esta ação não pode alterar o formato ou componentes de itens na matriz. 
> 
> Para que as ações utilizem a saída da matriz da ação da matriz do **filtro,** ou essas ações devem aceitar matrizes como entrada, ou pode ter de transformar o conjunto de saída em outro formato compatível.
> 
> Se ligar para um ponto final HTTP e receber uma resposta JSON, utilize a ação **Parse JSON** para processar a resposta JSON. 
> Caso contrário, a ação **da matriz do filtro** pode ler apenas o corpo de resposta e não a estrutura da carga útil JSON.

Se preferir trabalhar no editor de visualização de código, pode copiar o exemplo **Filter array** e Inicializar definições de ação **variável** deste artigo na definição subjacente do fluxo de trabalho da sua própria aplicação lógica: Exemplos de [código de operação de dados - Conjunto de filtros](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. No [portal Azure](https://portal.azure.com) ou Visual Studio, abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o portal Azure e uma aplicação lógica com um gatilho **de Recorrência** e uma ação **variável Inicialize.** A ação é criada para criar uma variável cujo valor inicial é uma matriz que tem alguns inteiros de amostra. Quando mais tarde testar a sua aplicação lógica, pode executar manualmente a sua aplicação sem esperar que o gatilho dispare.

   > [!NOTE]
   > Embora este exemplo utilize uma matriz de inteiros simples, esta ação é especialmente útil para matrizes de objetos JSON onde você pode filtrar com base nas propriedades e valores dos objetos.

   ![Início da aplicação lógica da amostra para ação "Filter array"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. Na sua aplicação lógica onde pretende criar a matriz filtrada, siga um destes passos: 

   * Para adicionar uma ação sob o último passo, selecione **Novo passo**.

     ![Selecione "Novo passo" para a ação "Filter array"](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Para adicionar uma ação entre os passos, mova o rato sobre a seta de ligação para que apareça o sinal de mais **+** (). Selecione o sinal de mais e, em seguida, **selecione Adicione uma ação**.

1. Na caixa de pesquisa, introduza `filter array` como filtro. Na lista de ações, selecione a ação **da matriz do filtro.**

   ![Selecione ação "Filter array"](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. Na caixa **'From',** forneça a matriz ou a expressão que pretende filtrar.

   Para este exemplo, quando clica dentro da caixa **'A',** aparece a lista de conteúdos dinâmicos para que possa selecionar a variável previamente criada:

   ![Selecione a saída da matriz para criar matriz filtrada](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. Para a condição, especifique os itens de matriz para comparar, selecione o operador de comparação e especifique o valor de comparação.

   Este exemplo utiliza a `item()` função de aceder a cada item na matriz enquanto a ação da **matriz do filtro** procura itens de matriz cujo valor é maior do que um:

   ![Exemplo terminado para ação "Filter array"](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

Para obter mais informações sobre esta ação na definição de fluxo de trabalho subjacente, consulte [a ação de consulta](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Teste o seu aplicativo de lógica

Para confirmar se a ação **do filtro** cria os resultados esperados, envie-se uma notificação que inclua a saída da ação da matriz **do filtro.**

1. Na sua aplicação lógica, adicione uma ação que lhe possa enviar os resultados da ação da **matriz do filtro.**

1. Nessa ação, clique em qualquer lugar que queira que os resultados apareçam. Quando a lista de conteúdos dinâmicos abrir, selecione **Expression**. Para obter a saída da matriz da ação da matriz do **filtro,** insira esta expressão que inclui o nome da ação da matriz do **filtro:**

   `@actionBody('Filter_array')`

   Este exemplo utiliza o Office 365 Outlook Enviar uma ação **por e-mail** e inclui as saídas da expressão **actionBody ('Filter_array')** no corpo do e-mail:

   ![Saídas de ação da ação "Filter array"](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. Agora, executar manualmente a sua aplicação lógica. Na barra de ferramentas do designer, selecione **Run**.

   Com base no conector de e-mail que usou, aqui estão os resultados que obtém:

   ![E-mail com resultados de ação "Filter array"](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Junte-se à ação

Para criar uma cadeia que tenha todos os itens de uma matriz e separe esses itens com um carácter delimiter específico, use a ação **'Unir'.** Em seguida, pode utilizar a corda em ações que se seguem após a ação **"Join".**

Se preferir trabalhar no editor de visualização de código, pode copiar o exemplo **Juntar** e **Inicializar** definições de ação variável deste artigo na definição subjacente do fluxo de trabalho da sua própria aplicação lógica: Exemplos de [código de operação de dados - Junte-se](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. No [portal Azure](https://portal.azure.com) ou Visual Studio, abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o portal Azure e uma aplicação lógica com um gatilho **de Recorrência** e uma ação **variável Inicialize.** Esta ação é criada para criar uma variável cujo valor inicial é uma matriz que tem alguns inteiros de amostra. Quando testar a sua aplicação lógica mais tarde, pode executar manualmente a sua aplicação sem esperar que o gatilho dispare.

   ![Iniciar app lógica de amostra para ação "Join"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. Na sua aplicação lógica onde pretende criar a cadeia a partir de uma matriz, siga um destes passos:

   * Para adicionar uma ação sob o último passo, selecione **Novo passo**.

     ![SSelect "Novo passo" para ação "Join"](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * Para adicionar uma ação entre os passos, mova o rato sobre a seta de ligação para que apareça o sinal de mais **+** (). Selecione o sinal de mais e, em seguida, **selecione Adicione uma ação**.

1. Na caixa de pesquisa, introduza `join` como filtro. Na lista de ações, selecione esta ação: **Junte-se**

   ![Selecione ação "Join"](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. Na caixa **From,** forneça a matriz que tem os itens que pretende juntar como uma corda.

   Para este exemplo, quando clicar dentro da caixa **' ' De's'**  

   ![Selecione a saída da matriz para criar a cadeia](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. Na **caixa 'Juntar-se** com' caixa, insira o carácter que pretende para separar cada item de matriz. 

   Este exemplo usa um cólon (:) como o separador.

   ![Fornecer o caráter separador](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

Para obter mais informações sobre esta ação na definição de fluxo de trabalho subjacente, consulte a [ação "Join".](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action)

### <a name="test-your-logic-app"></a>Teste o seu aplicativo de lógica

Para confirmar se a ação **Join** cria os resultados esperados, envie-se uma notificação que inclua a saída da ação **Join.**

1. Na sua aplicação lógica, adicione uma ação que lhe possa enviar os resultados da ação **Join.**

1. Nessa ação, clique em qualquer lugar que queira que os resultados apareçam. Quando a lista de conteúdos dinâmicos abrir, no âmbito da ação **'Juntar',** selecione **Output**. 

   Este exemplo utiliza o Office 365 Outlook **Enviar por email e** incluir o campo **Output** no corpo do e-mail:

   ![Campos de "output" para a ação "Join"](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. Agora, executar manualmente a sua aplicação lógica. Na barra de ferramentas do designer, selecione **Run**.

   Com base no conector de e-mail que usou, aqui estão os resultados que obtém:

   ![E-mail com resultados de ação "Junte-se"](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Ação parse JSON

Para obter propriedades de referência ou acesso no conteúdo JavaScript Object Notation (JSON), pode criar campos ou fichas fáceis de utilizar para essas propriedades utilizando a ação **Parse JSON.** Desta forma, pode selecionar essas propriedades a partir da lista de conteúdos dinâmicos quando especificar entradas para a sua aplicação lógica. Para esta ação, você pode fornecer um esquema JSON ou gerar um esquema JSON a partir do conteúdo ou carga útil da sua amostra JSON.

Se preferir trabalhar no editor de visualização de código, pode copiar o exemplo **Parse JSON** e Inicializar definições de ação **variável** deste artigo na definição subjacente do fluxo de trabalho da sua própria aplicação lógica: Exemplos de [código de operação de dados - Parse JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. No [portal Azure](https://portal.azure.com) ou Visual Studio, abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o portal Azure e uma aplicação lógica com um gatilho **de Recorrência** e uma ação **variável Inicialize.** A ação é criada para criar uma variável cujo valor inicial é um objeto JSON que tem propriedades e valores. Quando mais tarde testar a sua aplicação lógica, pode executar manualmente a sua aplicação sem esperar que o gatilho dispare.

   ![Início da aplicação lógica da amostra para ação "Parse JSON"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. Na sua aplicação lógica onde pretende analisar o conteúdo do JSON, siga um destes passos:

   * Para adicionar uma ação sob o último passo, selecione **Novo passo**.

     ![Selecione "Novo passo" para a ação "Parse JSON"](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Para adicionar uma ação entre os passos, mova o rato sobre a seta de ligação para que apareça o sinal de mais **+** (). Selecione o sinal de mais e, em seguida, **selecione Adicione uma ação**.

1. Na caixa de pesquisa, introduza `parse json` como filtro. Na lista de ações, selecione a ação **Parse JSON.**

   ![Selecione ação "Parse JSON"](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. Na caixa **contentar-se,** forneça o conteúdo JSON que pretende analisar.

   Para este exemplo, quando clica dentro da caixa **de Conteúdo,** aparece a lista de conteúdos dinâmicos para que possa selecionar a variável previamente criada:

   ![Selecione objeto JSON para ação de parse JSON](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. Insira o esquema JSON que descreve o conteúdo JSON que está a analisar.

   Para este exemplo, aqui está o esquema JSON:

   ![Forneça o esquema JSON para o objeto JSON que deseja analisar](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Se não tiver o esquema, pode gerar esse esquema a partir do conteúdo JSON, ou *carga útil,* está a analisar. 
   
   1. Na ação **Parse JSON,** **selecione Utilize a carga útil da amostra para gerar esquema**.

   1. Em **Entrar ou colar uma amostra de carga JSON,** fornecer o conteúdo JSON e, em seguida, selecionar **Fazer**.

      ![Introduza o conteúdo JSON para gerar o esquema](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

Para obter mais informações sobre esta ação na definição de fluxo de trabalho subjacente, consulte [a ação Parse JSON](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Teste o seu aplicativo de lógica

Para confirmar se a ação **Parse JSON** cria os resultados esperados, envie-se uma notificação que inclua a saída da ação **Parse JSON.**

1. Na sua aplicação lógica, adicione uma ação que lhe possa enviar os resultados da ação **Parse JSON.**

1. Nessa ação, clique em qualquer lugar que queira que os resultados apareçam. Quando a lista de conteúdos dinâmicos abrir, sob a ação **Parse JSON,** pode agora selecionar as propriedades a partir do conteúdo JSON analisado.

   Este exemplo utiliza o Office 365 Outlook Enviar uma ação **por e-mail** e inclui os campos **FirstName**, **LastName** e **Email** no corpo do e-mail:

   ![Propriedades JSON na ação "Enviar um e-mail"](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Aqui está a ação de e-mail acabada:

   ![Exemplo terminado para ação de e-mail](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. Agora, executar manualmente a sua aplicação lógica. Na barra de ferramentas do designer, selecione **Run**. 

   Com base no conector de e-mail que usou, aqui estão os resultados que obtém:

   ![E-mail com resultados de ação "Parse JSON"](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Selecione ação

Para criar uma matriz que tenha objetos JSON construídos a partir de valores numa matriz existente, utilize a ação **Select.** Por exemplo, pode criar um objeto JSON para cada valor numa matriz de inteiros, especificando as propriedades que cada objeto JSON deve ter e como mapear os valores na matriz de origem para essas propriedades. E embora possa alterar os componentes nesses objetos JSON, a matriz de saída tem sempre o mesmo número de itens que a matriz de origem.

> [!NOTE]
> Para que as ações utilizem a saída da matriz a partir da ação **Select,** ou essas ações devem aceitar matrizes como entrada, ou pode ter que transformar o conjunto de saída em outro formato compatível. 

Se preferir trabalhar no editor de visualização de código, pode copiar o exemplo **Selecionar** e **Inicializar** definições de ação variável deste artigo na definição subjacente do fluxo de trabalho da sua própria aplicação lógica: Exemplos de [código de operação de dados - Selecione](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. No [portal Azure](https://portal.azure.com) ou Visual Studio, abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o portal Azure e uma aplicação lógica com um gatilho **de Recorrência** e uma ação **variável Inicialize.** A ação é criada para criar uma variável cujo valor inicial é uma matriz que tem alguns inteiros de amostra. Quando mais tarde testar a sua aplicação lógica, pode executar manualmente a sua aplicação sem esperar que o gatilho dispare.

   ![Iniciar app lógica de amostra para ação "Select"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. Na sua aplicação lógica onde pretende criar a matriz, siga um destes passos: 

   * Para adicionar uma ação sob o último passo, selecione **Novo passo**.

     ![Selecione "Novo passo" para ação "Selecione"](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * Para adicionar uma ação entre os passos, mova o rato sobre a seta de ligação para que apareça o sinal de mais **+** (). Selecione o sinal de mais e, em seguida, **selecione Adicione uma ação**.

1. Em **Escolha uma ação**, selecione **Built-in**. Na caixa de pesquisa, introduza `select` como filtro. Na lista de ações, selecione a ação **Select.**

   ![Selecione a ação "Selecione"](./media/logic-apps-perform-data-operations/select-select-action.png)

1. Na caixa **'Partir',** especifique a matriz de origem desejada.

   Para este exemplo, quando clica dentro da caixa **'A',** aparece a lista de conteúdos dinâmicos para que possa selecionar a variável previamente criada:

   ![Selecione o conjunto de origem para selecionar ação](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. Na coluna da caixa do **Mapa** à esquerda, forneça o nome da propriedade que pretende atribuir cada valor na matriz de origem. Na coluna da direita, especifique uma expressão que represente o valor que pretende atribuir ao imóvel.

   Este exemplo especifica "Product_ID" como o nome da propriedade para atribuir cada valor na matriz de inteiros, utilizando a `item()` função numa expressão que acede a cada item de matriz. 

   ![Especifique a propriedade e valores do objeto JSON para criar matriz](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Aqui está a ação final:

   ![Exemplo terminado para ação "Selecione"](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

Para obter mais informações sobre esta ação na definição de fluxo de trabalho subjacente, consulte [ação Select](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Teste o seu aplicativo de lógica

Para confirmar se a ação **Select** cria os resultados esperados, envie-se uma notificação que inclua a saída da ação **Select.**

1. Na sua aplicação lógica, adicione uma ação que lhe possa enviar os resultados da ação **Select.**

1. Nessa ação, clique em qualquer lugar que queira que os resultados apareçam. Quando a lista de conteúdos dinâmicos abrir, selecione **Expression**. Para obter a saída da matriz a partir da ação **Select,** insira esta expressão que inclui o nome da ação **Select:**

   `@actionBody('Select')`

   Este exemplo utiliza o Office 365 Outlook **Enviar por email** e incluir as saídas da expressão no corpo do `@actionBody('Select')` e-mail:

   ![Saídas de ação da ação "Select"](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. Agora, executar manualmente a sua aplicação lógica. Na barra de ferramentas do designer, selecione **Run**.

   Com base no conector de e-mail que usou, aqui estão os resultados que obtém:

   ![E-mail com resultados de ação "Selecione"](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais [sobre os conectores de Aplicações Lógicas](../connectors/apis-list.md)
