---
title: Realizar operações em dados
description: Converter, gerir e manipular saídas e formatos de dados em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: baa6e5732221d120ff71217a3a86a942794c53f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79283943"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Realizar operações de dados em Aplicações Lógicas Azure

Este artigo mostra como pode trabalhar com dados nas suas aplicações lógicas adicionando ações para estas tarefas e muito mais:

* Crie mesas a partir de matrizes.
* Crie matrizes a partir de outras matrizes com base numa condição.
* Crie fichas fáceis de utilizar a partir das propriedades de objetos JavaScript Object Notation (JSON) para que possa utilizar facilmente essas propriedades no seu fluxo de trabalho.

Se não encontrar a ação que pretende aqui, experimente navegar nas várias funções de [manipulação](../logic-apps/workflow-definition-language-functions-reference.md) de dados que as Aplicações Lógicas Azure proporcionam.

Estas tabelas resumem as operações de dados que pode utilizar e são organizadas com base nos tipos de dados de origem em que as operações funcionam, mas cada descrição aparece alfabeticamente.

**Ações de matriz** 

Estas ações ajudam-no a trabalhar com dados em matrizes.

| Ação | Descrição |
|--------|-------------|
| [**Criar tabela CSV**](#create-csv-table-action) | Crie uma tabela de valor separado de vírina (CSV) de uma matriz. |
| [**Criar tabela HTML**](#create-html-table-action) | Crie uma tabela HTML a partir de uma matriz. |
| [**Matriz de filtro**](#filter-array-action) | Crie um subconjunto de matriz a partir de uma matriz com base no filtro ou condição especificado. |
| [**Junte-se**](#join-action) | Crie uma cadeia de todos os itens numa matriz e separe cada item com o caracteres especificados. |
| [**Selecione**](#select-action) | Crie uma matriz a partir das propriedades especificadas para todos os itens numa matriz diferente. |
||| 

**Ações da JSON**

Estas ações ajudam-no a trabalhar com dados no formato JavaScript Object Notation (JSON).

| Ação | Descrição |
|--------|-------------|
| [**Compor**](#compose-action) | Crie uma mensagem, ou cadeia, a partir de várias inputs que podem ter vários tipos de dados. Em seguida, pode utilizar esta cadeia como uma única entrada, em vez de introduzir repetidamente as mesmas inputs. Por exemplo, pode criar uma única mensagem JSON a partir de várias inputs. |
| [**Parse JSON**](#parse-json-action) | Crie fichas de dados fáceis de utilizar para propriedades em conteúdos JSON para que possa utilizar mais facilmente as propriedades nas suas aplicações lógicas. |
|||

Para criar transformações JSON mais complexas, consulte [Executar transformações avançadas de JSON com modelos líquidos](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* A aplicação lógica onde você precisa da operação para trabalhar com dados

  Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Do Azure e](../logic-apps/logic-apps-overview.md) o [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) como o primeiro passo na sua aplicação lógica 

  As operações de dados só estão disponíveis como ações, por isso, antes de poder utilizar estas ações, iniciar a sua aplicação lógica com um gatilho e incluir quaisquer outras ações necessárias para criar as saídas que deseja.

<a name="compose-action"></a>

## <a name="compose-action"></a>Componha a ação

Para construir uma única saída, como um objeto JSON a partir de várias inputs, pode utilizar a ação **Compor.** As suas inputs podem ter vários tipos, tais como inteiros, Booleans, arrays, objetos JSON e qualquer outro tipo nativo que as Aplicações Lógicas Azure suporte, por exemplo, binário e XML. Em seguida, pode utilizar a saída em ações que se seguem após a ação **de Compor.** A ação **Compor** também pode salvá-lo de introduzir repetidamente as mesmas inputs enquanto constrói o fluxo de trabalho da sua aplicação lógica.

Por exemplo, você pode construir uma mensagem JSON a partir de múltiplas variáveis, tais como variáveis de cordas que armazenam os primeiros nomes e apelidos das pessoas, e uma variável inteiro que armazena a idade das pessoas. Aqui, a ação **Compor** aceita estes contributos:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

e cria esta saída:

`{"age":35,"fullName":"Owens,Sophie"}`

Para experimentar um exemplo, siga estes passos utilizando o Logic App Designer. Ou, se preferir trabalhar no editor de visualização de códigos, pode copiar o exemplo **Compor** e Inicializar definições de ação **variável** deste artigo na definição subjacente de fluxo de trabalho da sua própria aplicação lógica: Exemplos de código de [operação de dados - Composição](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. No [portal Azure](https://portal.azure.com) ou No Estúdio Visual, abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o portal Azure e uma aplicação lógica com um gatilho **de Recorrência** e várias ações **variáveis inicializar.** Estas ações são criadas para criar duas variáveis de cadeia e uma variável inteiro. Quando mais tarde testar a sua aplicação lógica, pode executar manualmente a sua aplicação sem esperar que o gatilho dispare.

   ![Iniciar app lógica de amostra para ação "Compor"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. Na sua aplicação lógica onde pretende criar a saída, siga um destes passos: 

   * Para adicionar uma ação sob o último passo, selecione **Novo passo**.

     ![Selecione "Novo passo" para a ação "Compor"](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * Para adicionar uma ação entre passos, mova o rato sobre**+** a seta de ligação para que o sinal de adição () apareça. Selecione o sinal de mais e, em seguida, selecione **Adicionar uma ação**.

1. Em **'Escolha uma ação**' `compose` na caixa de pesquisa, introduza como filtro. Da lista de ações, selecione a ação **Compor.**

   ![Selecione ação "Compor"](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. Na caixa **de entradas,** forneça as inputs que pretende para criar a saída.

   Para este exemplo, quando clica dentro da caixa **de Entradas,** aparece a lista de conteúdos dinâmicos para que possa selecionar as variáveis previamente criadas:

   ![Selecione inputs para usar para ação "Compor"](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Aqui está o exemplo final **Compor** ação: 

   ![Exemplo final para ação "Compor"](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. Guarde a aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

Para obter mais informações sobre esta ação na sua definição de fluxo de trabalho subjacente, consulte a [ação Compor](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action).

### <a name="test-your-logic-app"></a>Teste a sua aplicação lógica

Para confirmar se a ação **Compor** cria os resultados esperados, envie uma notificação que inclua a saída da ação **Compor.**

1. Na sua aplicação lógica, adicione uma ação que pode enviar-lhe os resultados da ação **Compor.**

1. Nessa ação, clique em qualquer lugar que queira que os resultados apareçam. Quando a lista de conteúdos dinâmicos abrir, sob a ação **Compor,** selecione **Output**.

   Este exemplo utiliza a ação **enviar um e-mail** e inclui os campos **de saída** no corpo e assunto do e-mail:

   ![Campos de "saída" para a ação "Compor"](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. Agora, execute manualmente a sua aplicação lógica. Na barra de ferramentas de design, selecione **Executar**.

   Com base no conector de e-mail que usou, aqui estão os resultados que obtém:

   ![E-mail com resultados de ação "Compor"](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Criar ação de mesa CSV

Para criar uma tabela de valor separado de vírvias (CSV) que tenha as propriedades e valores de objetos de Notação de Objetos JavaScript (JSON) numa matriz, utilize a ação da **tabela Create CSV.** Em seguida, pode utilizar a tabela resultante em ações que seguem a ação da **tabela Create CSV.**

Se preferir trabalhar no editor de visualização de códigos, pode copiar o exemplo Criar tabela **CSV** e inicializar definições de ação **variável** a partir deste artigo na definição subjacente de fluxo de trabalho da sua própria aplicação lógica: Exemplos de código de [operação de dados - Criar tabela CSV](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. No [portal Azure](https://portal.azure.com) ou No Estúdio Visual, abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o portal Azure e uma aplicação lógica com um gatilho **de Recorrência** e uma ação **variável Inicializar.** A ação é criada para criar uma variável cujo valor inicial é uma matriz que tem algumas propriedades e valores no formato JSON. Quando mais tarde testar a sua aplicação lógica, pode executar manualmente a sua aplicação sem esperar que o gatilho dispare.

   ![Aplicação lógica de amostra inicial para ação "Criar csV"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. Na sua aplicação lógica onde pretende criar a tabela CSV, siga um destes passos: 

   * Para adicionar uma ação sob o último passo, selecione **Novo passo**.

     ![Selecione "Novo passo" para a ação "Criar tabela CSV"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Para adicionar uma ação entre passos, mova o rato sobre**+** a seta de ligação para que o sinal de adição () apareça. Selecione o sinal de mais e, em seguida, selecione **Adicionar uma ação**.

1. Em **'Escolha uma ação**' `create csv table` na caixa de pesquisa, introduza como filtro. Na lista de ações, selecione a ação de **mesa Create CSV.**

   ![Selecione ação "Criar tabela CSV"](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. Na caixa **From,** forneça a matriz ou expressão que pretende para criar a mesa.

   Para este exemplo, quando clica dentro da caixa **'From',** aparece a lista de conteúdos dinâmicos para selecionar a variável previamente criada:

   ![Selecione a saída de matriz para criar a tabela CSV](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > Para criar fichas fáceis de utilizar para as propriedades em objetos JSON para que possa selecionar essas propriedades como entradas, use o [Parse JSON](#parse-json-action) antes de chamar a ação de **mesa Create CSV.**

   Aqui está o exemplo acabado **Create CSV ação** de mesa: 

   ![Exemplo final para ação "Criar cSV"](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. Guarde a aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

### <a name="customize-table-format"></a>Personalizar formato de mesa

Por predefinição, a propriedade **Colunas** é definida para criar automaticamente as colunas de tabela com base nos itens de matriz. Para especificar cabeçalhos e valores personalizados, siga estes passos:

1. Abra a lista de **Colunas** e selecione **Custom**.

1. Na propriedade **Header,** especifique o texto personalizado do cabeçalho para utilizar.

1. Na propriedade **Value,** especifique o valor personalizado a utilizar.

Para devolver valores da matriz, pode utilizar a [ `item()` função](../logic-apps/workflow-definition-language-functions-reference.md#item) com a ação de **mesa Create CSV.** Num `For_each` loop, pode utilizar a [ `items()` função](../logic-apps/workflow-definition-language-functions-reference.md#items).

Por exemplo, suponha que quer colunas de mesa que tenham apenas os valores de propriedade e não os nomes de propriedade de uma matriz. Para devolver apenas estes valores, siga estes passos para trabalhar na vista do designer ou na vista de código. Aqui está o resultado que este exemplo retorna:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Trabalho na vista do designer

Na ação, mantenha a coluna **header** vazia. Em cada linha da coluna **Valor,** dereference cada propriedade de matriz que desejar. Cada linha em **Valor** devolve todos os valores para a propriedade de matriz especificada e torna-se uma coluna na sua tabela.

1. Under **Value**, em cada linha que desejar, clique dentro da caixa de edição para que a lista de conteúdos dinâmicos apareça.

1. Na lista de conteúdos dinâmicos, selecione **Expression**.

1. No editor de expressão, introduza esta expressão que especifica o valor de propriedade da matriz que deseja, e selecione **OK**.

   `item()?['<array-property-name>']`

   Por exemplo:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Dereferência "Descrição" para "Criar tabela CSV"](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. Repita os passos anteriores para cada propriedade de matriz que deseja. Quando terminar, a sua ação parece ser este exemplo:

   ![Função "item()" em "Criar tabela CSV"](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. Para resolver expressões em versões mais descritivas, mude para a vista de código e volte para a vista do designer e, em seguida, reabra a ação colapsada:

   A ação da **tabela Create CSV** agora aparece como este exemplo:

   !["Criar mesa CSV" - expressões resolvidas, sem cabeçalhos](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>Trabalhar em vista de código

Na definição JSON da ação, dentro `columns` `header` da matriz, definiu a propriedade numa corda vazia. Para `value` cada propriedade, dereference cada propriedade de matriz que você deseja.

1. Na barra de ferramentas de design, selecione **vista código**.

1. No editor de código, na `columns` matriz da `header` ação, `value` adicione a propriedade vazia e esta expressão para cada coluna de valores de matriz que você deseja:

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

1. Volte para a vista do designer e reabra a ação em colapso.

   A ação da **tabela Create CSV** agora aparece como este exemplo, e as expressões resolveram versões mais descritivas:

   !["Criar mesa CSV" - expressões resolvidas e sem cabeçalhos](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

Para obter mais informações sobre esta ação na sua definição de fluxo de trabalho subjacente, consulte a [ação da Tabela](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Teste a sua aplicação lógica

Para confirmar se a ação da **tabela Create CSV** cria os resultados esperados, envie uma notificação que inclua a saída da ação da **tabela Create CSV.**

1. Na sua aplicação lógica, adicione uma ação que pode enviar-lhe os resultados da ação da **tabela Create CSV.**

1. Nessa ação, clique em qualquer lugar que queira que os resultados apareçam. Quando a lista de conteúdos dinâmicos se abrir, sob a ação de **tabela Create CSV,** selecione **Output**. 

   Este exemplo utiliza o Office 365 Outlook Enviar uma ação por **e-mail** e inclui o campo **de saída** no corpo do e-mail:

   ![Campos de "saída" para a ação "Criar tabela CSV"](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. Agora, execute manualmente a sua aplicação lógica. Na barra de ferramentas de design, selecione **Executar**.

   Com base no conector de e-mail que usou, aqui estão os resultados que obtém:

   ![E-mail com resultados de ação "Create CSV"](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Criar ação de mesa HTML

Para criar uma tabela HTML que tenha as propriedades e valores de objetos de notação de objetos JavaScript (JSON) numa matriz, utilize a ação de **tabela Create HTML.** Em seguida, pode utilizar a tabela resultante em ações que seguem a ação da **tabela Create HTML.**

Se preferir trabalhar no editor de visualização de códigos, pode copiar o exemplo Criar tabela **HTML** e inicializar definições de ação **variável** a partir deste artigo na definição subjacente de fluxo de trabalho da sua própria aplicação lógica: [Exemplos](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) de código de operação de dados - Criar tabela HTML 

1. No [portal Azure](https://portal.azure.com) ou No Estúdio Visual, abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o portal Azure e uma aplicação lógica com um gatilho **de Recorrência** e uma ação **variável Inicializar.** A ação é criada para criar uma variável cujo valor inicial é uma matriz que tem algumas propriedades e valores no formato JSON. Quando mais tarde testar a sua aplicação lógica, pode executar manualmente a sua aplicação sem esperar que o gatilho dispare.

   ![Aplicação lógica de amostra inicial para "Criar tabela HTML"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. Na sua aplicação lógica onde pretende criar uma tabela HTML, siga um destes passos:

   * Para adicionar uma ação sob o último passo, selecione **Novo passo**.

     ![Selecione "Novo passo" para a ação "Criar tabela HTML"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Para adicionar uma ação entre passos, mova o rato sobre**+** a seta de ligação para que o sinal de adição () apareça. Selecione o sinal de mais e, em seguida, selecione **Adicionar uma ação**.

1. Em **'Escolha uma ação**' `create html table` na caixa de pesquisa, introduza como filtro. Na lista de ações, selecione a ação de **tabela Create HTML.**

   ![Selecione ação "Criar tabela HTML"](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. Na caixa **From,** forneça a matriz ou expressão que pretende para criar a mesa.

   Para este exemplo, quando clica dentro da caixa **'From',** aparece a lista de conteúdos dinâmicos para selecionar a variável previamente criada:

   ![Selecione a saída de matriz para criar a tabela HTML](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > Para criar fichas fáceis de utilizar para as propriedades em objetos JSON para que possa selecionar essas propriedades como entradas, use o [Parse JSON](#parse-json-action) antes de chamar a ação de **tabela Create HTML.**

   Aqui está o exemplo acabado Criar ação de **tabela HTML:**

   ![Exemplo terminado para "Criar tabela HTML"](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. Guarde a aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

### <a name="customize-table-format"></a>Personalizar formato de mesa

Por predefinição, a propriedade **Colunas** é definida para criar automaticamente as colunas de tabela com base nos itens de matriz. Para especificar cabeçalhos e valores personalizados, siga estes passos:

1. Abra a lista de **Colunas** e selecione **Custom**.

1. Na propriedade **Header,** especifique o texto personalizado do cabeçalho para utilizar.

1. Na propriedade **Value,** especifique o valor personalizado a utilizar.

Para devolver valores da matriz, pode utilizar a [ `item()` função](../logic-apps/workflow-definition-language-functions-reference.md#item) com a ação da **tabela Create HTML.** Num `For_each` loop, pode utilizar a [ `items()` função](../logic-apps/workflow-definition-language-functions-reference.md#items).

Por exemplo, suponha que quer colunas de mesa que tenham apenas os valores de propriedade e não os nomes de propriedade de uma matriz. Para devolver apenas estes valores, siga estes passos para trabalhar na vista do designer ou na vista de código. Aqui está o resultado que este exemplo retorna:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Trabalho na vista do designer

Na ação, mantenha a coluna **header** vazia. Em cada linha da coluna **Valor,** dereference cada propriedade de matriz que desejar. Cada linha em **Valor** devolve todos os valores para a propriedade especificada e torna-se uma coluna na sua tabela.

1. Under **Value**, em cada linha que desejar, clique dentro da caixa de edição para que a lista de conteúdos dinâmicos apareça.

1. Na lista de conteúdos dinâmicos, selecione **Expression**.

1. No editor de expressão, introduza esta expressão que especifica o valor de propriedade da matriz que deseja, e selecione **OK**.

   `item()?['<array-property-name>']`

   Por exemplo:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Desreferenciação de propriedade em ação "Criar tabela HTML"](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. Repita os passos anteriores para cada propriedade de matriz que deseja. Quando terminar, a sua ação parece ser este exemplo:

   ![Função "item()" em "Criar tabela HTML"](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. Para resolver expressões em versões mais descritivas, mude para a vista de código e volte para a vista do designer e, em seguida, reabra a ação colapsada:

   A ação da **tabela Create HTML** aparece agora como este exemplo:

   !["Criar tabela HTML" - expressões resolvidas, sem cabeçalhos](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>Trabalhar em vista de código

Na definição JSON da ação, dentro `columns` `header` da matriz, definiu a propriedade numa corda vazia. Para `value` cada propriedade, dereference cada propriedade de matriz que você deseja.

1. Na barra de ferramentas de design, selecione **vista código**.

1. No editor de código, na `columns` matriz da `header` ação, `value` adicione a propriedade vazia e esta expressão para cada coluna de valores de matriz que você deseja:

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

1. Volte para a vista do designer e reabra a ação em colapso.

   A ação da **tabela Create HTML** aparece agora como este exemplo, e as expressões resolveram versões mais descritivas:

   !["Criar tabela HTML" - expressões resolvidas e sem cabeçalhos](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

Para obter mais informações sobre esta ação na sua definição de fluxo de trabalho subjacente, consulte a [ação da Tabela](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Teste a sua aplicação lógica

Para confirmar se a ação da **tabela Create HTML** cria os resultados esperados, envie-se uma notificação que inclua a saída da ação da tabela Create **HTML.**

1. Na sua aplicação lógica, adicione uma ação que pode enviar-lhe os resultados da ação da **tabela Create HTML.**

1. Nessa ação, clique em qualquer lugar que queira que os resultados apareçam. Quando a lista de conteúdos dinâmicos abrir, sob a ação da **tabela Create HTML,** selecione **Output**. 

   Este exemplo utiliza o Office 365 Outlook Enviar uma ação por **e-mail** e inclui o campo **de saída** no corpo do e-mail:

   ![Campos de "saída" para "Criar tabela HTML"](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > Ao incluir a saída da tabela HTML numa ação de e-mail, certifique-se de que define a propriedade **Is HTML** para **Yes** nas opções avançadas da ação de e-mail. Desta forma, a ação de e-mail formata corretamente a tabela HTML.

1. Agora, execute manualmente a sua aplicação lógica. Na barra de ferramentas de design, selecione **Executar**.

   Com base no conector de e-mail que usou, aqui estão os resultados que obtém:

   ![E-mail com resultados de "Criar tabela HTML"](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Ação de matriz de filtro

Para criar uma matriz mais pequena que tenha itens, que satisfaçam critérios específicos, a partir de uma matriz existente, utilize a ação de **matriz filter.** Em seguida, pode utilizar a matriz filtrada em ações que se seguem após a ação da **matriz do filtro.**

> [!NOTE]
> Qualquer texto de filtro que utilize na sua condição é sensível a casos. Além disso, esta ação não pode alterar o formato ou componentes dos itens na matriz. 
> 
> Para que as ações utilizem a saída da matriz a partir da ação da **matriz filter,** ou essas ações devem aceitar as matrizes como entrada, ou poderá ter de transformar a matriz de saída em outro formato compatível.
> 
> Se ligar para um ponto final http e receber uma resposta JSON, use a ação **Parse JSON** para processar a resposta JSON. 
> Caso contrário, a ação da **matriz** do filtro só pode ler o corpo de resposta e não a estrutura da carga útil JSON.

Se preferir trabalhar no editor de visualização de códigos, pode copiar o exemplo **Filter array** e inicializar definições de ação **variável** deste artigo para a definição subjacente de fluxo de trabalho da sua própria aplicação lógica: Exemplos de código de operação de [dados - Matriz de filtro](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. No [portal Azure](https://portal.azure.com) ou No Estúdio Visual, abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o portal Azure e uma aplicação lógica com um gatilho **de Recorrência** e uma ação **variável Inicializar.** A ação é criada para criar uma variável cujo valor inicial é uma matriz que tem alguns inteiros de amostra. Quando mais tarde testar a sua aplicação lógica, pode executar manualmente a sua aplicação sem esperar que o gatilho dispare.

   > [!NOTE]
   > Embora este exemplo utilize uma simples matriz de inteiros, esta ação é especialmente útil para matrizes de objetos JSON onde você pode filtrar com base nas propriedades e valores dos objetos.

   ![Aplicação lógica de amostra inicial para ação "Filter array"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. Na sua aplicação lógica onde pretende criar a matriz filtrada, siga um destes passos: 

   * Para adicionar uma ação sob o último passo, selecione **Novo passo**.

     ![Selecione "Novo passo" para a ação "Filter array"](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Para adicionar uma ação entre passos, mova o rato sobre**+** a seta de ligação para que o sinal de adição () apareça. Selecione o sinal de mais e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de `filter array` pesquisa, introduza como filtro. Na lista de ações, selecione a ação de **matriz filter.**

   ![Selecione a ação "Filter array"](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. Na caixa **From,** forneça a matriz ou expressão que pretende filtrar.

   Para este exemplo, quando clica dentro da caixa **'From',** aparece a lista de conteúdos dinâmicos para selecionar a variável previamente criada:

   ![Selecione a saída da matriz para criar matriz filtrada](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. Para a condição, especifique os itens de matriz para comparar, selecione o operador de comparação e especifique o valor de comparação.

   Este exemplo `item()` utiliza a função de acesso a cada item da matriz enquanto a ação da **matriz filter** procura itens de matriz cujo valor é maior do que um:

   ![Exemplo final para a ação "Filter array"](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. Guarde a aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

Para obter mais informações sobre esta ação na sua definição de fluxo de trabalho subjacente, consulte a [ação de Consulta](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Teste a sua aplicação lógica

Para confirmar se a ação da **matriz do filtro** cria os resultados esperados, envie uma notificação que inclua a saída da ação da **matriz** filter.

1. Na sua aplicação lógica, adicione uma ação que pode enviar-lhe os resultados da ação de **matriz filter.**

1. Nessa ação, clique em qualquer lugar que queira que os resultados apareçam. Quando a lista de conteúdos dinâmicos abrir, selecione **Expression**. Para obter a saída da **matriz** da ação da matriz filter, introduza esta expressão que inclui o nome da ação da **matriz filter:**

   `@actionBody('Filter_array')`

   Este exemplo utiliza o Office 365 Outlook Enviar uma ação **por e-mail** e inclui as saídas da expressão **actionBody ('Filter_array')** no corpo do e-mail:

   ![Saídas de ação da ação "Filter array"](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. Agora, execute manualmente a sua aplicação lógica. Na barra de ferramentas de design, selecione **Executar**.

   Com base no conector de e-mail que usou, aqui estão os resultados que obtém:

   ![E-mail com resultados de ação "Filter array"](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Junte-se à ação

Para criar uma cadeia que tenha todos os itens de uma matriz e separe esses itens com um caráter delimitador específico, use a ação **Join.** Em seguida, pode usar a corda em ações que se seguem após a ação **De Ad.I.**

Se preferir trabalhar no editor de visualização de códigos, pode copiar o exemplo **Juntar** e **inicializar** definições de ação variável deste artigo para a definição subjacente de fluxo de trabalho da sua própria aplicação lógica: Exemplos de código de [operação de dados - Juntar-se](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. No [portal Azure](https://portal.azure.com) ou No Estúdio Visual, abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o portal Azure e uma aplicação lógica com um gatilho **de Recorrência** e uma ação **variável Inicializar.** Esta ação é criada para criar uma variável cujo valor inicial é uma matriz que tem alguns inteiros de amostra. Quando testar a sua aplicação lógica mais tarde, pode executar manualmente a sua aplicação sem esperar que o gatilho dispare.

   ![Iniciar app lógica de amostra para ação "Juntar"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. Na sua aplicação lógica onde pretende criar a corda a partir de uma matriz, siga um destes passos:

   * Para adicionar uma ação sob o último passo, selecione **Novo passo**.

     ![SSelect "Novo passo" para a ação "Juntar"](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * Para adicionar uma ação entre passos, mova o rato sobre**+** a seta de ligação para que o sinal de adição () apareça. Selecione o sinal de mais e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de `join` pesquisa, introduza como filtro. Da lista de ações, selecione esta ação: **Junte-se**

   ![Selecione ação "Juntar"](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. Na caixa **From,** forneça a matriz que tem os itens que pretende juntar como uma corda.

   Para este exemplo, quando clica no interior da caixa **De,** a lista de conteúdos dinâmicos que aparece para que possa selecionar a variável previamente criada:  

   ![Selecione a saída de matriz para criar a cadeia](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. No **Juntar com** caixa, introduza o personagem que pretende separar cada item da matriz. 

   Este exemplo usa um cólon (:) como o separador.

   ![Fornecer o caráter separador](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. Guarde a aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

Para obter mais informações sobre esta ação na sua definição de fluxo de trabalho subjacente, consulte a [ação Join](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Teste a sua aplicação lógica

Para confirmar se a ação **Join** cria os resultados esperados, envie uma notificação que inclua a saída da ação **Join.**

1. Na sua aplicação lógica, adicione uma ação que pode enviar-lhe os resultados da ação **Join.**

1. Nessa ação, clique em qualquer lugar que queira que os resultados apareçam. Quando a lista de conteúdos dinâmicos abrir, sob a ação **Unir,** selecione **Output**. 

   Este exemplo utiliza o Office 365 Outlook Enviar uma ação por **e-mail** e inclui o campo **de saída** no corpo do e-mail:

   ![Campos de "saída" para a ação "Juntar"](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. Agora, execute manualmente a sua aplicação lógica. Na barra de ferramentas de design, selecione **Executar**.

   Com base no conector de e-mail que usou, aqui estão os resultados que obtém:

   ![E-mail com resultados de ação "Juntar"](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Ação Parse JSON

Para referência ou acesso a propriedades no conteúdo da JavaScript Object Notation (JSON), pode criar campos ou fichas fáceis de utilizar para essas propriedades utilizando a ação **Parse JSON.** Desta forma, pode selecionar essas propriedades da lista de conteúdos dinâmicos quando especifica as inputs para a sua aplicação lógica. Para esta ação, pode fornecer um esquema JSON ou gerar um esquema JSON a partir do conteúdo ou carga útil da amostra JSON.

Se preferir trabalhar no editor de visualização de códigos, pode copiar o exemplo **Parse JSON** e inicializar definições de ação **variável** deste artigo na definição subjacente de fluxo de trabalho da sua própria aplicação lógica: Exemplos de código de [operação de dados - Parse JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. No [portal Azure](https://portal.azure.com) ou No Estúdio Visual, abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o portal Azure e uma aplicação lógica com um gatilho **de Recorrência** e uma ação **variável Inicializar.** A ação é criada para criar uma variável cujo valor inicial é um objeto JSON que tem propriedades e valores. Quando mais tarde testar a sua aplicação lógica, pode executar manualmente a sua aplicação sem esperar que o gatilho dispare.

   ![Aplicação lógica de amostra inicial para ação "Parse JSON"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. Na sua aplicação lógica onde pretende analisar o conteúdo da JSON, siga um destes passos:

   * Para adicionar uma ação sob o último passo, selecione **Novo passo**.

     ![Selecione "Novo passo" para a ação "Parse JSON"](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Para adicionar uma ação entre passos, mova o rato sobre**+** a seta de ligação para que o sinal de adição () apareça. Selecione o sinal de mais e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de `parse json` pesquisa, introduza como filtro. Na lista de ações, selecione a ação **Parse JSON.**

   ![Selecione ação "Parse JSON"](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. Na caixa **de Conteúdo,** forneça o conteúdo JSON que pretende analisar.

   Para este exemplo, quando clica dentro da caixa de **conteúdo,** a lista de conteúdos dinâmicos aparece para que possa selecionar a variável previamente criada:

   ![Selecione o objeto JSON para a ação parse JSON](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. Introduza o esquema JSON que descreve o conteúdo JSON que está a analisar.

   Para este exemplo, aqui está o esquema JSON:

   ![Forneça schema JSON para o objeto JSON que pretende analisar](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Se não tiver o esquema, pode gerar esse esquema a partir do conteúdo JSON, ou *carga útil,* está a analisar. 
   
   1. Na ação **Parse JSON,** selecione Utilize a carga útil da **amostra para gerar esquema**.

   1. Em **'Entrar ou colar uma amostra De carga útil JSON',** forneça o conteúdo JSON e, em seguida, selecione **Done**.

      ![Introduza o conteúdo jSON para gerar o esquema](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. Guarde a aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

Para obter mais informações sobre esta ação na sua definição de fluxo de trabalho subjacente, consulte a [ação Parse JSON](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Teste a sua aplicação lógica

Para confirmar se a ação **Parse JSON** cria os resultados esperados, envie uma notificação que inclua a saída da ação **Parse JSON.**

1. Na sua aplicação lógica, adicione uma ação que pode enviar-lhe os resultados da ação **Parse JSON.**

1. Nessa ação, clique em qualquer lugar que queira que os resultados apareçam. Quando a lista de conteúdos dinâmicos abrir, sob a ação **Parse JSON,** pode agora selecionar as propriedades a partir do conteúdo da JSON analisado.

   Este exemplo utiliza o Office 365 Outlook Enviar uma ação **por e-mail** e inclui os campos **FirstName**, **LastName**e **Email** no corpo do e-mail:

   ![Propriedades da JSON na ação "Enviar um e-mail"](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Aqui está a ação de e-mail terminada:

   ![Exemplo final para ação de e-mail](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. Agora, execute manualmente a sua aplicação lógica. Na barra de ferramentas de design, selecione **Executar**. 

   Com base no conector de e-mail que usou, aqui estão os resultados que obtém:

   ![E-mail com resultados de ação "Parse JSON"](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Selecione ação

Para criar uma matriz que tenha objetos JSON construídos a partir de valores numa matriz existente, utilize a ação **Select.** Por exemplo, pode criar um objeto JSON para cada valor numa matriz inteiro, especificando as propriedades que cada objeto JSON deve ter e como mapear os valores na matriz de origem para essas propriedades. E embora possa alterar os componentes nesses objetos JSON, a matriz de saída tem sempre o mesmo número de itens que a matriz de origem.

> [!NOTE]
> Para que as ações utilizem a saída da matriz a partir da ação **Select,** ou essas ações devem aceitar as matrizes como entrada, ou poderá ter de transformar a matriz de saída em outro formato compatível. 

Se preferir trabalhar no editor de visualização de códigos, pode copiar o exemplo **Selecionar** e Inicializar definições de ação variável deste artigo para a definição subjacente de fluxo de trabalho da sua própria aplicação lógica: Exemplos de código de [operação de dados - Selecione](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) **Initialize variable** 

1. No [portal Azure](https://portal.azure.com) ou No Estúdio Visual, abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o portal Azure e uma aplicação lógica com um gatilho **de Recorrência** e uma ação **variável Inicializar.** A ação é criada para criar uma variável cujo valor inicial é uma matriz que tem alguns inteiros de amostra. Quando mais tarde testar a sua aplicação lógica, pode executar manualmente a sua aplicação sem esperar que o gatilho dispare.

   ![Iniciar aplicativo lógico de amostra para ação "Select"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. Na sua aplicação lógica onde pretende criar a matriz, siga um destes passos: 

   * Para adicionar uma ação sob o último passo, selecione **Novo passo**.

     ![Selecione "Novo passo" para a ação "Select"](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * Para adicionar uma ação entre passos, mova o rato sobre**+** a seta de ligação para que o sinal de adição () apareça. Selecione o sinal de mais e, em seguida, selecione **Adicionar uma ação**.

1. Em **'Escolha uma ação**', selecione **Incorporado**' . Na caixa de `select` pesquisa, introduza como filtro. Na lista de ações, selecione a ação **Select.**

   ![Selecione a ação "Selecione"](./media/logic-apps-perform-data-operations/select-select-action.png)

1. Na caixa **From,** especifique a matriz de origem que deseja.

   Para este exemplo, quando clica dentro da caixa **'From',** aparece a lista de conteúdos dinâmicos para selecionar a variável previamente criada:

   ![Selecione a matriz de origem para selecionar a ação](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. Na coluna da caixa do **Mapa,** forneça o nome de propriedade que pretende atribuir a cada valor na matriz de origem. Na coluna da direita, especifique uma expressão que represente o valor que pretende atribuir à propriedade.

   Este exemplo especifica "Product_ID" como o nome da propriedade para atribuir cada `item()` valor na matriz inteiro, utilizando a função numa expressão que aceda a cada item da matriz. 

   ![Especificar propriedade e valores de objetoJSON para criar matriz](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Aqui está a ação final:

   ![Exemplo final para ação "Select"](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. Guarde a aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

Para obter mais informações sobre esta ação na sua definição de fluxo de trabalho subjacente, consulte [Selecionar a ação](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Teste a sua aplicação lógica

Para confirmar se a ação **Select** cria os resultados esperados, envie uma notificação que inclua a saída da ação **Select.**

1. Na sua aplicação lógica, adicione uma ação que pode enviar-lhe os resultados da ação **Select.**

1. Nessa ação, clique em qualquer lugar que queira que os resultados apareçam. Quando a lista de conteúdos dinâmicos abrir, selecione **Expression**. Para obter a saída da matriz da ação **Select,** introduza esta expressão que inclui o nome da ação **Select:**

   `@actionBody('Select')`

   Este exemplo utiliza o Office 365 Outlook Enviar uma `@actionBody('Select')` ação por **e-mail** e inclui as saídas da expressão no corpo do e-mail:

   ![Saídas de ação da ação "Select"](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. Agora, execute manualmente a sua aplicação lógica. Na barra de ferramentas de design, selecione **Executar**.

   Com base no conector de e-mail que usou, aqui estão os resultados que obtém:

   ![E-mail com resultados de ação "Selecione"](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba sobre [conectores de Aplicações Lógicas](../connectors/apis-list.md)
