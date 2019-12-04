---
title: Executar operações em dados
description: Converter, gerenciar e manipular saídas de dados e formatos em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: f98daf301e8e17ad3f0bfb850ded1a8ed8bce417
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793115"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Executar operações de dados em aplicativos lógicos do Azure

Este artigo mostra como você pode trabalhar com dados em seus aplicativos lógicos adicionando ações para essas tarefas e muito mais:

* Crie tabelas a partir de matrizes.
* Crie matrizes de outras matrizes com base em uma condição.
* Crie tokens amigáveis de propriedades de objeto JavaScript Object Notation (JSON) para que você possa usar facilmente essas propriedades em seu fluxo de trabalho.

Se você não encontrar a ação desejada aqui, tente navegar pelas várias funções de [manipulação de dados](../logic-apps/workflow-definition-language-functions-reference.md) que o aplicativo lógico do Azure fornece.

Essas tabelas resumem as operações de dados que você pode usar e são organizadas com base nos tipos de dados de origem em que as operações funcionam, mas cada descrição aparece em ordem alfabética.

**Ações de matriz** 

Essas ações ajudam você a trabalhar com dados em matrizes.

| Ação | Descrição |
|--------|-------------|
| [**Criar tabela CSV**](#create-csv-table-action) | Crie uma tabela de valores separados por vírgulas (CSV) de uma matriz. |
| [**Criar tabela HTML**](#create-html-table-action) | Crie uma tabela HTML de uma matriz. |
| [**Filtrar matriz**](#filter-array-action) | Crie um subconjunto de matriz de uma matriz com base no filtro ou na condição especificada. |
| [**Em**](#join-action) | Crie uma cadeia de caracteres de todos os itens em uma matriz e separe cada item com o caractere especificado. |
| [**Não**](#select-action) | Crie uma matriz a partir das propriedades especificadas para todos os itens em uma matriz diferente. |
||| 

**Ações JSON**

Essas ações ajudam você a trabalhar com dados no formato JavaScript Object Notation (JSON).

| Ação | Descrição |
|--------|-------------|
| [**Compose**](#compose-action) | Crie uma mensagem, ou uma cadeia de caracteres, de várias entradas que podem ter vários tipos de dados. Você pode usar essa cadeia de caracteres como uma única entrada, em vez de inserir repetidamente as mesmas entradas. Por exemplo, você pode criar uma única mensagem JSON de várias entradas. |
| [**Analisar JSON**](#parse-json-action) | Crie tokens de dados amigáveis para propriedades no conteúdo JSON para que você possa usar com mais facilidade as propriedades em seus aplicativos lógicos. |
|||

Para criar transformações JSON mais complexas, consulte [executar transformações JSON avançadas com modelos líquidos](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* O aplicativo lógico em que você precisa da operação para trabalhar com dados

  Se você não estiver familiarizado com os aplicativos lógicos, examine [o que é o aplicativo lógico do Azure?](../logic-apps/logic-apps-overview.md) e o [início rápido: Crie seu primeiro aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) como a primeira etapa em seu aplicativo lógico 

  As operações de dados estão disponíveis somente como ações, portanto, antes de poder usar essas ações, inicie o aplicativo lógico com um gatilho e inclua as outras ações necessárias para criar as saídas desejadas.

<a name="compose-action"></a>

## <a name="compose-action"></a>Ação de composição

Para construir uma única saída, como um objeto JSON de várias entradas, você pode usar a ação **compor** . Suas entradas podem ter vários tipos, como inteiros, Boolianos, matrizes, objetos JSON e qualquer outro tipo nativo ao qual o aplicativo lógico do Azure dá suporte, por exemplo, binary e XML. Você pode usar a saída em ações que acompanham após a ação de **composição** . A ação **compor** também pode evitar que você insira repetidamente as mesmas entradas enquanto cria o fluxo de trabalho do aplicativo lógico.

Por exemplo, você pode construir uma mensagem JSON de várias variáveis, como variáveis de cadeia de caracteres que armazenam nomes e sobrenomes de pessoas e uma variável de inteiro que armazena as idades das pessoas. Aqui, a ação de **composição** aceita estas entradas:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

e cria essa saída:

`{"age":35,"fullName":"Owens,Sophie"}`

Para testar um exemplo, siga estas etapas usando o designer de aplicativo lógico. Ou, se você preferir trabalhar no editor de modo de exibição de código, você pode copiar as definições de ação de **composição** e **inicialização** de exemplo deste artigo para a definição de fluxo de trabalho subjacente de seu próprio aplicativo lógico: [exemplos de código de operação de dados-Compose](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no designer de aplicativo lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho de **recorrência** e várias ações de **variável de inicialização** . Essas ações são configuradas para criar duas variáveis de cadeia de caracteres e uma variável de inteiro. Quando você testar o aplicativo lógico posteriormente, poderá executar o aplicativo manualmente sem esperar que o gatilho seja acionado.

   ![Iniciando o aplicativo lógico de exemplo para a ação "compor"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. Em seu aplicativo lógico em que você deseja criar a saída, siga uma destas etapas: 

   * Para adicionar uma ação na última etapa, selecione **nova etapa**.

     ![Selecione "nova etapa" para a ação "compor"](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição ( **+** ) seja exibido. Selecione o sinal de adição e, em seguida, selecione **Adicionar uma ação**.

1. Em **escolher uma ação**, na caixa de pesquisa, insira `compose` como filtro. Na lista ações, selecione a ação **compor** .

   ![Selecione a ação "compor"](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. Na caixa **entradas** , forneça as entradas desejadas para criar a saída.

   Para este exemplo, quando você clica dentro da caixa **entradas** , a lista de conteúdo dinâmico é exibida para que você possa selecionar as variáveis criadas anteriormente:

   ![Selecionar entradas a serem usadas para a ação "compor"](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Aqui está a ação de **composição** de exemplo concluída: 

   ![Exemplo concluído para a ação "compor"](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, selecione **salvar**.

Para obter mais informações sobre essa ação em sua definição de fluxo de trabalho subjacente, consulte a [ação compor](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action).

### <a name="test-your-logic-app"></a>Testar seu aplicativo lógico

Para confirmar se a ação de **composição** cria os resultados esperados, envie a si mesmo uma notificação que inclua a saída da ação de **composição** .

1. Em seu aplicativo lógico, adicione uma ação que possa enviar os resultados da ação **compor** .

1. Nessa ação, clique em qualquer lugar em que você deseja que os resultados sejam exibidos. Quando a lista de conteúdo dinâmico for aberta, na ação **compor** , selecione **saída**.

   Este exemplo usa a ação **enviar um email** e inclui os campos de **saída** no corpo e no assunto do email:

   ![Campos de "saída" para a ação "compor"](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. Agora, execute manualmente seu aplicativo lógico. Na barra de ferramentas do designer, selecione **executar**.

   Com base no conector de email que você usou, aqui estão os resultados obtidos:

   ![Enviar por email com resultados de ação de "compor"](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Criar ação de tabela CSV

Para criar uma tabela de valores separados por vírgulas (CSV) que tenha as propriedades e valores de objetos JavaScript Object Notation (JSON) em uma matriz, use a ação **criar tabela CSV** . Em seguida, você pode usar a tabela resultante em ações que seguem a ação **criar tabela CSV** .

Se você preferir trabalhar no editor de exibição de código, copie o exemplo **criar tabela CSV** e inicializar definições de ação de **variável** deste artigo em sua própria definição de fluxo de trabalho subjacente do aplicativo lógico: exemplos de código de [operação de dados – criar tabela CSV](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no designer de aplicativo lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho de **recorrência** e uma ação de **variável de inicialização** . A ação é configurada para criar uma variável cujo valor inicial é uma matriz que tem algumas propriedades e valores no formato JSON. Quando você testar o aplicativo lógico posteriormente, poderá executar o aplicativo manualmente sem esperar que o gatilho seja acionado.

   ![Iniciando o aplicativo lógico de exemplo para a ação "criar tabela CSV"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. Em seu aplicativo lógico em que você deseja criar a tabela CSV, siga uma destas etapas: 

   * Para adicionar uma ação na última etapa, selecione **nova etapa**.

     ![Selecione "nova etapa" para a ação "criar tabela CSV"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição ( **+** ) seja exibido. Selecione o sinal de adição e, em seguida, selecione **Adicionar uma ação**.

1. Em **escolher uma ação**, na caixa de pesquisa, insira `create csv table` como filtro. Na lista ações, selecione a ação **criar tabela CSV** .

   ![Selecione a ação "criar tabela CSV"](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. Na caixa **de** , forneça a matriz ou expressão que você deseja para criar a tabela.

   Para este exemplo, quando você clica dentro da caixa de, a lista **de** conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecione a saída da matriz para criar a tabela CSV](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > Para criar tokens amigáveis para as propriedades em objetos JSON para que você possa selecionar essas propriedades como entradas, use o [JSON de análise](#parse-json-action) antes de chamar a ação **criar tabela CSV** .

   Aqui está o exemplo de ação de **criação de tabela CSV** concluída: 

   ![Exemplo concluído para a ação "criar tabela CSV"](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, selecione **salvar**.

### <a name="customize-table-format"></a>Personalizar formato de tabela

Por padrão, a propriedade **Columns** é definida para criar automaticamente as colunas da tabela com base nos itens da matriz. Para especificar cabeçalhos e valores personalizados, siga estas etapas:

1. Abra a lista **colunas** e selecione **personalizado**.

1. Na propriedade **header** , especifique o texto do cabeçalho personalizado para usar em vez disso.

1. Na propriedade **valor** , especifique o valor personalizado a ser usado em vez disso.

Para retornar valores da matriz, você pode usar a [função`item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) com a ação **criar tabela CSV** . Em um loop de `For_each`, você pode usar a [função`items()`](../logic-apps/workflow-definition-language-functions-reference.md#items).

Por exemplo, suponha que você deseja colunas de tabela que tenham apenas os valores de propriedade e não os nomes de propriedade de uma matriz. Para retornar apenas esses valores, siga estas etapas para trabalhar no modo de exibição de designer ou na exibição de código. Este é o resultado que este exemplo retorna:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Trabalhar no modo de exibição de designer

Na ação, mantenha a coluna de **cabeçalho** vazia. Em cada linha na coluna **valor** , desfaça referência a cada propriedade de matriz que você deseja. Cada linha sob **valor** retorna todos os valores para a propriedade de matriz especificada e torna-se uma coluna em sua tabela.

1. Em **valor**, em cada linha desejada, clique dentro da caixa de edição para que a lista de conteúdo dinâmico seja exibida.

1. Na lista conteúdo dinâmico, selecione **expressão**.

1. No editor de expressão, insira esta expressão que especifica o valor da propriedade de matriz que você deseja e selecione **OK**.

   `item()?['<array-property-name>']`

   Por exemplo:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Desreferenciar "Description" para "criar tabela CSV"](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. Repita as etapas anteriores para cada propriedade de matriz desejada. Quando terminar, sua ação será parecida com este exemplo:

   ![função "Item ()" em "criar tabela CSV"](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. Para resolver expressões em versões mais descritivas, alterne para o modo de exibição de código e de volta para o modo de exibição de designer e, em seguida, reabra a ação recolhida:

   A ação **criar tabela CSV** agora aparece como este exemplo:

   !["Criar tabela CSV"-expressões resolvidas, sem cabeçalhos](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>Trabalhar no modo de exibição de código

Na definição de JSON da ação, dentro da matriz de `columns`, defina a propriedade `header` como uma cadeia de caracteres vazia. Para cada propriedade de `value`, desfaça referência a cada propriedade de matriz que você deseja.

1. Na barra de ferramentas do designer, selecione **modo de exibição de código**.

1. No editor de código, na matriz de `columns` da ação, adicione a propriedade `header` vazia e essa expressão `value` para cada coluna de valores de matriz que você deseja:

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

1. Volte para o modo de exibição de designer e reabra a ação recolhida.

   A ação **criar tabela CSV** agora aparece como este exemplo, e as expressões foram resolvidas para versões mais descritivas:

   !["Criar tabela CSV"-expressões resolvidas e nenhum cabeçalho](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

Para obter mais informações sobre essa ação em sua definição de fluxo de trabalho subjacente, consulte a [ação da tabela](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Testar seu aplicativo lógico

Para confirmar se a ação **criar tabela CSV** cria os resultados esperados, envie-se a você mesmo uma notificação que inclui a saída da ação **criar tabela CSV** .

1. Em seu aplicativo lógico, adicione uma ação que possa enviar os resultados da ação **criar tabela CSV** .

1. Nessa ação, clique em qualquer lugar em que você deseja que os resultados sejam exibidos. Quando a lista de conteúdo dinâmico for aberta, na ação **criar tabela CSV** , selecione **saída**. 

   Este exemplo usa a ação **enviar um email** para o Outlook do Office 365 e inclui o campo de **saída** no corpo do email:

   ![Campos de "saída" para a ação "criar tabela CSV"](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. Agora, execute manualmente seu aplicativo lógico. Na barra de ferramentas do designer, selecione **executar**.

   Com base no conector de email que você usou, aqui estão os resultados obtidos:

   ![Enviar por email com os resultados da ação "criar tabela CSV"](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Ação de criar tabela HTML

Para criar uma tabela HTML que tenha as propriedades e valores de objetos JavaScript Object Notation (JSON) em uma matriz, use a ação **criar tabela HTML** . Em seguida, você pode usar a tabela resultante em ações que seguem a ação **criar tabela HTML** .

Se você preferir trabalhar no editor de exibição de código, copie o exemplo **criar tabela HTML** e inicializar definições de ação de **variável** deste artigo em sua própria definição de fluxo de trabalho subjacente do aplicativo lógico: exemplos de [código de operação de dados – criar tabela HTML](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no designer de aplicativo lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho de **recorrência** e uma ação de **variável de inicialização** . A ação é configurada para criar uma variável cujo valor inicial é uma matriz que tem algumas propriedades e valores no formato JSON. Quando você testar o aplicativo lógico posteriormente, poderá executar o aplicativo manualmente sem esperar que o gatilho seja acionado.

   ![Iniciando aplicativo lógico de exemplo para "criar tabela HTML"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. Em seu aplicativo lógico em que você deseja criar uma tabela HTML, siga uma destas etapas:

   * Para adicionar uma ação na última etapa, selecione **nova etapa**.

     ![Selecione "nova etapa" para a ação "criar tabela HTML"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição ( **+** ) seja exibido. Selecione o sinal de adição e, em seguida, selecione **Adicionar uma ação**.

1. Em **escolher uma ação**, na caixa de pesquisa, insira `create html table` como filtro. Na lista ações, selecione a ação **criar tabela HTML** .

   ![Selecione a ação "criar tabela HTML"](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. Na caixa **de** , forneça a matriz ou expressão que você deseja para criar a tabela.

   Para este exemplo, quando você clica dentro da caixa de, a lista **de** conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecione a saída da matriz para criar a tabela HTML](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > Para criar tokens amigáveis para as propriedades em objetos JSON para que você possa selecionar essas propriedades como entradas, use o [JSON de análise](#parse-json-action) antes de chamar a ação **criar tabela HTML** .

   Aqui está o exemplo de ação de **criação de tabela HTML** concluída:

   ![Exemplo concluído para "criar tabela HTML"](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, selecione **salvar**.

### <a name="customize-table-format"></a>Personalizar formato de tabela

Por padrão, a propriedade **Columns** é definida para criar automaticamente as colunas da tabela com base nos itens da matriz. Para especificar cabeçalhos e valores personalizados, siga estas etapas:

1. Abra a lista **colunas** e selecione **personalizado**.

1. Na propriedade **header** , especifique o texto do cabeçalho personalizado para usar em vez disso.

1. Na propriedade **valor** , especifique o valor personalizado a ser usado em vez disso.

Para retornar valores da matriz, você pode usar a [função`item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) com a ação **criar tabela HTML** . Em um loop de `For_each`, você pode usar a [função`items()`](../logic-apps/workflow-definition-language-functions-reference.md#items).

Por exemplo, suponha que você deseja colunas de tabela que tenham apenas os valores de propriedade e não os nomes de propriedade de uma matriz. Para retornar apenas esses valores, siga estas etapas para trabalhar no modo de exibição de designer ou na exibição de código. Este é o resultado que este exemplo retorna:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Trabalhar no modo de exibição de designer

Na ação, mantenha a coluna de **cabeçalho** vazia. Em cada linha na coluna **valor** , desfaça referência a cada propriedade de matriz que você deseja. Cada linha sob **valor** retorna todos os valores para a propriedade especificada e torna-se uma coluna em sua tabela.

1. Em **valor**, em cada linha desejada, clique dentro da caixa de edição para que a lista de conteúdo dinâmico seja exibida.

1. Na lista conteúdo dinâmico, selecione **expressão**.

1. No editor de expressão, insira esta expressão que especifica o valor da propriedade de matriz que você deseja e selecione **OK**.

   `item()?['<array-property-name>']`

   Por exemplo:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Propriedade de desreferência na ação "criar tabela HTML"](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. Repita as etapas anteriores para cada propriedade de matriz desejada. Quando terminar, sua ação será parecida com este exemplo:

   ![função "Item ()" em "criar tabela HTML"](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. Para resolver expressões em versões mais descritivas, alterne para o modo de exibição de código e de volta para o modo de exibição de designer e, em seguida, reabra a ação recolhida:

   A ação **criar tabela HTML** agora aparece como este exemplo:

   !["Criar tabela HTML"-expressões resolvidas, sem cabeçalhos](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>Trabalhar no modo de exibição de código

Na definição de JSON da ação, dentro da matriz de `columns`, defina a propriedade `header` como uma cadeia de caracteres vazia. Para cada propriedade de `value`, desfaça referência a cada propriedade de matriz que você deseja.

1. Na barra de ferramentas do designer, selecione **modo de exibição de código**.

1. No editor de código, na matriz de `columns` da ação, adicione a propriedade `header` vazia e essa expressão `value` para cada coluna de valores de matriz que você deseja:

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

1. Volte para o modo de exibição de designer e reabra a ação recolhida.

   A ação **criar tabela HTML** agora aparece como este exemplo, e as expressões foram resolvidas para versões mais descritivas:

   !["Criar tabela HTML"-expressões resolvidas e nenhum cabeçalho](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

Para obter mais informações sobre essa ação em sua definição de fluxo de trabalho subjacente, consulte a [ação da tabela](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Testar seu aplicativo lógico

Para confirmar se a ação **criar tabela HTML** cria os resultados esperados, envie-se a você mesmo uma notificação que inclui a saída da ação **criar tabela HTML** .

1. Em seu aplicativo lógico, adicione uma ação que possa enviar os resultados da ação **criar tabela HTML** .

1. Nessa ação, clique em qualquer lugar em que você deseja que os resultados sejam exibidos. Quando a lista de conteúdo dinâmico for aberta, na ação **criar tabela HTML** , selecione **saída**. 

   Este exemplo usa a ação **enviar um email** para o Outlook do Office 365 e inclui o campo de **saída** no corpo do email:

   ![Campos de "saída" para "criar tabela HTML"](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > Ao incluir a saída da tabela HTML em uma ação de email, certifique-se de definir a propriedade **HTML is** como **Sim** nas opções avançadas da ação de email. Dessa forma, a ação de email formata corretamente a tabela HTML.

1. Agora, execute manualmente seu aplicativo lógico. Na barra de ferramentas do designer, selecione **executar**.

   Com base no conector de email que você usou, aqui estão os resultados obtidos:

   ![Email com resultados de "criar tabela HTML"](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Filtrar ação de matriz

Para criar uma matriz menor que tenha itens, que atendam a critérios específicos, de uma matriz existente, use a ação **Filtrar matriz** . Em seguida, você pode usar a matriz filtrada em ações que seguem após a ação **Filtrar matriz** .

> [!NOTE]
> Qualquer texto de filtro que você usa em sua condição diferencia maiúsculas de minúsculas. Além disso, essa ação não pode alterar o formato ou os componentes dos itens na matriz. 
> 
> Para ações para usar a saída de matriz da ação **Filtrar matriz** , essas ações devem aceitar matrizes como entrada ou você pode ter que transformar a matriz de saída em outro formato compatível.

Se você preferir trabalhar no editor de exibição de código, poderá copiar a **matriz de filtro** de exemplo e inicializar definições de ação de **variável** deste artigo em sua própria definição de fluxo de trabalho subjacente do aplicativo lógico: [exemplos de código de operação de dados – filtrar matriz](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no designer de aplicativo lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho de **recorrência** e uma ação de **variável de inicialização** . A ação é configurada para criar uma variável cujo valor inicial é uma matriz que tem alguns inteiros de exemplo. Quando você testar o aplicativo lógico posteriormente, poderá executar o aplicativo manualmente sem esperar que o gatilho seja acionado.

   > [!NOTE]
   > Embora este exemplo use uma matriz de inteiros simples, essa ação é especialmente útil para matrizes de objetos JSON, em que você pode filtrar com base nas propriedades e nos valores dos objetos.

   ![Iniciando aplicativo lógico de exemplo para a ação "filtrar matriz"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. Em seu aplicativo lógico em que você deseja criar a matriz filtrada, siga uma destas etapas: 

   * Para adicionar uma ação na última etapa, selecione **nova etapa**.

     ![Selecione "nova etapa" para a ação "filtrar matriz"](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição ( **+** ) seja exibido. Selecione o sinal de adição e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira `filter array` como seu filtro. Na lista ações, selecione a ação **Filtrar matriz** .

   ![Selecione a ação "filtrar matriz"](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. Na caixa **de** , forneça a matriz ou expressão que você deseja filtrar.

   Para este exemplo, quando você clica dentro da caixa de, a lista **de** conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecione a saída da matriz para criar a matriz filtrada](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. Para a condição, especifique os itens de matriz a serem comparados, selecione o operador de comparação e especifique o valor de comparação.

   Este exemplo usa a função `item()` para acessar cada item na matriz, enquanto a ação de **matriz de filtro** procura itens de matriz cujo valor é maior que um:

   ![Exemplo concluído para a ação "filtrar matriz"](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, selecione **salvar**.

Para obter mais informações sobre essa ação em sua definição de fluxo de trabalho subjacente, consulte [ação de consulta](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Testar seu aplicativo lógico

Para confirmar se a ação da **matriz de filtro** cria os resultados esperados, envie a si mesmo uma notificação que inclua a saída da ação **Filtrar matriz** .

1. Em seu aplicativo lógico, adicione uma ação que possa enviar os resultados da ação **Filtrar matriz** .

1. Nessa ação, clique em qualquer lugar em que você deseja que os resultados sejam exibidos. Quando a lista de conteúdo dinâmico for aberta, selecione **expressão**. Para obter a saída da matriz da ação **Filtrar matriz** , insira esta expressão que inclui o nome da ação da **matriz de filtro** :

   `@actionBody('Filter_array')`

   Este exemplo usa a ação **enviar um email** do Outlook do Office 365 e inclui as saídas da expressão **actionBody (' Filter_array ')** no corpo do email:

   ![Saídas de ação da ação "filtrar matriz"](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. Agora, execute manualmente seu aplicativo lógico. Na barra de ferramentas do designer, selecione **executar**.

   Com base no conector de email que você usou, aqui estão os resultados obtidos:

   ![Enviar por email com os resultados da ação "filtrar matriz"](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Ação de junção

Para criar uma cadeia de caracteres que tenha todos os itens de uma matriz e separar os itens com um caractere delimitador específico, use a ação de **junção** . Em seguida, você pode usar a cadeia de caracteres em ações que seguem após a ação de **junção** .

Se você preferir trabalhar no editor de exibição de código, copie as definições de ação de **junção** de exemplo e de **inicialização** deste artigo para sua própria definição de fluxo de trabalho subjacente do aplicativo lógico: [exemplos de código de operação de dados-junção](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no designer de aplicativo lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho de **recorrência** e uma ação de **variável de inicialização** . Essa ação é configurada para criar uma variável cujo valor inicial é uma matriz que tem alguns inteiros de exemplo. Ao testar seu aplicativo lógico mais tarde, você pode executar o aplicativo manualmente sem esperar que o gatilho seja acionado.

   ![Iniciando aplicativo lógico de exemplo para a ação "ingressar"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. Em seu aplicativo lógico em que você deseja criar a cadeia de caracteres de uma matriz, siga uma destas etapas:

   * Para adicionar uma ação na última etapa, selecione **nova etapa**.

     ![SSelect "nova etapa" para a ação "ingressar"](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição ( **+** ) seja exibido. Selecione o sinal de adição e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira `join` como seu filtro. Na lista ações, selecione esta ação: **ingressar**

   ![Selecione a ação "ingressar"](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. Na caixa **de** , forneça a matriz que contém os itens que você deseja unir como uma cadeia de caracteres.

   Para este exemplo, quando você clica dentro da caixa de, a lista **de** conteúdo dinâmico que aparece para que você possa selecionar a variável criada anteriormente:  

   ![Selecione a saída da matriz para criar a cadeia de caracteres](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. Na caixa **unir com** , insira o caractere desejado para separar cada item da matriz. 

   Este exemplo usa dois-pontos (:) como o separador.

   ![Forneça o caractere separador](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, selecione **salvar**.

Para obter mais informações sobre essa ação em sua definição de fluxo de trabalho subjacente, consulte a [ação de junção](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Testar seu aplicativo lógico

Para confirmar se a ação de **junção** cria os resultados esperados, envie a si mesmo uma notificação que inclua a saída da ação de **junção** .

1. Em seu aplicativo lógico, adicione uma ação que possa enviar os resultados da ação de **junção** .

1. Nessa ação, clique em qualquer lugar em que você deseja que os resultados sejam exibidos. Quando a lista de conteúdo dinâmico é aberta, sob a ação de **junção** , selecione **saída**. 

   Este exemplo usa a ação **enviar um email** para o Outlook do Office 365 e inclui o campo de **saída** no corpo do email:

   ![Campos de "saída" para a ação "junção"](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. Agora, execute manualmente seu aplicativo lógico. Na barra de ferramentas do designer, selecione **executar**.

   Com base no conector de email que você usou, aqui estão os resultados obtidos:

   ![Enviar por email com os resultados da ação "ingressar"](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Analisar ação JSON

Para referenciar ou acessar propriedades no conteúdo JavaScript Object Notation (JSON), você pode criar campos amigáveis ou tokens para essas propriedades usando a ação **analisar JSON** . Dessa forma, você pode selecionar essas propriedades na lista de conteúdo dinâmico quando especificar entradas para seu aplicativo lógico. Para essa ação, você pode fornecer um esquema JSON ou gerar um esquema JSON a partir do conteúdo JSON de exemplo ou da carga.

Se você preferir trabalhar no editor de modo de exibição de código, você pode copiar o exemplo de **análise JSON** e inicializar definições de ação de **variável** deste artigo para sua própria definição de fluxo de trabalho subjacente do seu aplicativo lógico: [exemplos de código de operação de dados – analisar JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no designer de aplicativo lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho de **recorrência** e uma ação de **variável de inicialização** . A ação é configurada para criar uma variável cujo valor inicial é um objeto JSON que tem propriedades e valores. Quando você testar o aplicativo lógico posteriormente, poderá executar o aplicativo manualmente sem esperar que o gatilho seja acionado.

   ![Iniciando aplicativo lógico de exemplo para a ação "analisar JSON"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. Em seu aplicativo lógico em que você deseja analisar o conteúdo JSON, siga uma destas etapas:

   * Para adicionar uma ação na última etapa, selecione **nova etapa**.

     ![Selecione "nova etapa" para a ação "analisar JSON"](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição ( **+** ) seja exibido. Selecione o sinal de adição e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira `parse json` como seu filtro. Na lista ações, selecione a ação **analisar JSON** .

   ![Selecione a ação "analisar JSON"](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. Na caixa **conteúdo** , forneça o conteúdo JSON que você deseja analisar.

   Para este exemplo, quando você clica dentro da caixa de **conteúdo** , a lista de conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecione o objeto JSON para a ação analisar JSON](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. Insira o esquema JSON que descreve o conteúdo JSON que você está analisando.

   Neste exemplo, este é o esquema JSON:

   ![Forneça o esquema JSON para o objeto JSON que você deseja analisar](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Se você não tiver o esquema, poderá gerar esse esquema a partir do conteúdo JSON, ou da *carga*, que está analisando. 
   
   1. Na ação **analisar JSON** , selecione **usar conteúdo de exemplo para gerar o esquema**.

   1. Em **Inserir ou colar um exemplo de carga JSON**, forneça o conteúdo JSON e, em seguida, selecione **concluído**.

      ![Insira o conteúdo JSON para gerar o esquema](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, selecione **salvar**.

Para obter mais informações sobre essa ação na definição de fluxo de trabalho subjacente, consulte [analisar ação JSON](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Testar seu aplicativo lógico

Para confirmar se a ação de **análise JSON** cria os resultados esperados, envie-se a você mesmo uma notificação que inclui a saída da ação **analisar JSON** .

1. Em seu aplicativo lógico, adicione uma ação que possa enviar os resultados da ação **analisar JSON** .

1. Nessa ação, clique em qualquer lugar em que você deseja que os resultados sejam exibidos. Quando a lista de conteúdo dinâmico é aberta, sob a ação **analisar JSON** , agora você pode selecionar as propriedades do conteúdo JSON analisado.

   Este exemplo usa a ação **enviar um email** do Outlook do Office 365 e inclui os campos **FirstName**, **LastName**e **email** no corpo do email:

   ![Propriedades JSON na ação "enviar um email"](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Esta é a ação de email concluída:

   ![Exemplo concluído para ação de email](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. Agora, execute manualmente seu aplicativo lógico. Na barra de ferramentas do designer, selecione **executar**. 

   Com base no conector de email que você usou, aqui estão os resultados obtidos:

   ![Enviar por email com os resultados da ação "analisar JSON"](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Selecionar ação

Para criar uma matriz que tenha objetos JSON criados a partir de valores em uma matriz existente, use a ação **selecionar** . Por exemplo, você pode criar um objeto JSON para cada valor em uma matriz de inteiros especificando as propriedades que cada objeto JSON deve ter e como mapear os valores na matriz de origem para essas propriedades. E, embora você possa alterar os componentes nesses objetos JSON, a matriz de saída sempre tem o mesmo número de itens que a matriz de origem.

> [!NOTE]
> Para ações usar a saída de matriz da ação **Select** , essas ações devem aceitar matrizes como entrada ou você pode ter que transformar a matriz de saída em outro formato compatível. 

Se você preferir trabalhar no editor de exibição de código, poderá copiar o exemplo **selecionar** e **inicializar** definições de ação de variável deste artigo em sua própria definição de fluxo de trabalho subjacente do aplicativo lógico: [exemplos de código de operação de dados-selecione](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no designer de aplicativo lógico.

   Este exemplo usa o portal do Azure e um aplicativo lógico com um gatilho de **recorrência** e uma ação de **variável de inicialização** . A ação é configurada para criar uma variável cujo valor inicial é uma matriz que tem alguns inteiros de exemplo. Quando você testar o aplicativo lógico posteriormente, poderá executar o aplicativo manualmente sem esperar que o gatilho seja acionado.

   ![Iniciando o aplicativo lógico de exemplo para a ação "selecionar"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. Em seu aplicativo lógico em que você deseja criar a matriz, siga uma destas etapas: 

   * Para adicionar uma ação na última etapa, selecione **nova etapa**.

     ![Selecione "nova etapa" para a ação "selecionar"](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição ( **+** ) seja exibido. Selecione o sinal de adição e, em seguida, selecione **Adicionar uma ação**.

1. Em **escolher uma ação**, selecione **interno**. Na caixa de pesquisa, insira `select` como seu filtro. Na lista ações, selecione a ação **selecionar** .

   ![Selecionar a ação "selecionar"](./media/logic-apps-perform-data-operations/select-select-action.png)

1. Na caixa **de** , especifique a matriz de origem desejada.

   Para este exemplo, quando você clica dentro da caixa de, a lista **de** conteúdo dinâmico é exibida para que você possa selecionar a variável criada anteriormente:

   ![Selecionar matriz de origem para a ação de seleção](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. Na coluna do lado esquerdo da caixa **mapa** , forneça o nome da propriedade que você deseja atribuir a cada valor na matriz de origem. Na coluna à direita, especifique uma expressão que represente o valor que você deseja atribuir à propriedade.

   Este exemplo especifica "Product_ID" como o nome da propriedade para atribuir cada valor na matriz de inteiros usando a função `item()` em uma expressão que acessa cada item da matriz. 

   ![Especificar a propriedade e os valores do objeto JSON para criar a matriz](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Aqui está a ação concluída:

   ![Exemplo concluído para a ação "selecionar"](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, selecione **salvar**.

Para obter mais informações sobre essa ação em sua definição de fluxo de trabalho subjacente, consulte [Selecionar ação](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Testar seu aplicativo lógico

Para confirmar se a ação **Select** cria os resultados esperados, envie a si mesmo uma notificação que inclua a saída da ação **selecionar** .

1. Em seu aplicativo lógico, adicione uma ação que possa enviar os resultados da ação **Select** .

1. Nessa ação, clique em qualquer lugar em que você deseja que os resultados sejam exibidos. Quando a lista de conteúdo dinâmico for aberta, selecione **expressão**. Para obter a saída da matriz da ação **Select** , insira esta expressão que inclui o nome da ação **Select** :

   `@actionBody('Select')`

   Este exemplo usa a ação **enviar um email** do Outlook do Office 365 e inclui as saídas da expressão `@actionBody('Select')` no corpo do email:

   ![Saídas de ação da ação "selecionar"](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. Agora, execute manualmente seu aplicativo lógico. Na barra de ferramentas do designer, selecione **executar**.

   Com base no conector de email que você usou, aqui estão os resultados obtidos:

   ![Enviar por email com os resultados da ação "selecionar"](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [conectores de aplicativos lógicos](../connectors/apis-list.md)
