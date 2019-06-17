---
title: Realizar operações nos dados - Azure Logic Apps | Documentos da Microsoft
description: Converter, gerir e manipular dados saídas e formatos no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/30/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 93c24f88fcd6a002493933ef71c5c80bd2ff8c10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62120184"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Efetuar operações de dados no Azure Logic Apps

Este artigo mostra como pode trabalhar com dados nas suas aplicações lógicas ao adicionar ações para essas tarefas e muito mais:

* Crie tabelas de matrizes.
* Crie as matrizes de partir de outras matrizes com base numa condição.
* Crie tokens amigável de utilizador a partir das propriedades de objeto JavaScript Object Notation (JSON), para que pode facilmente usar essas propriedades no fluxo de trabalho.

Se não encontrar a ação que pretende aqui, tente navegar as muitas várias [funções de manipulação de dados](../logic-apps/workflow-definition-language-functions-reference.md) que fornece o Logic Apps. 

Estas tabelas resumem os dados de operações que pode utilizar e está organizados com base nos tipos de dados de origem que as operações funcionam no, mas cada descrição é apresentada por ordem alfabética.

**Ações de matriz** 

Estas ações ajudam a trabalhar com dados em matrizes.

| Ação | Descrição | 
|--------|-------------| 
| [**Criar tabela CSV**](#create-csv-table-action) | Crie uma tabela de valores separados por vírgulas (CSV) de uma matriz. | 
| [**Criar tabela HTML**](#create-html-table-action) | Crie uma tabela HTML a partir de uma matriz. | 
| [**Matriz de filtro**](#filter-array-action) | Crie um subconjunto de matriz de uma matriz com base no filtro especificado ou condição. | 
| [**Join**](#join-action) | Criar uma cadeia de caracteres a partir de todos os itens numa matriz e separar cada item pelo caractere especificado. | 
| [**Select**](#select-action) | Crie uma matriz das propriedades especificadas para todos os itens numa matriz de diferente. | 
||| 

**Ações de JSON**

Estas ações ajudam a trabalhar com dados no formato de JavaScript Object Notation (JSON).

| Ação | Descrição | 
|--------|-------------| 
| [**Compor**](#compose-action) | Crie uma mensagem ou a cadeia de caracteres, a partir de várias entradas, que podem ter vários tipos de dados. Em seguida, pode usar essa cadeia de caracteres como uma única entrada, em vez de introduzir repetidamente as mesmas entradas. Por exemplo, pode criar uma única mensagem JSON de várias entradas. | 
| [**Parse JSON**](#parse-json-action) | Crie tokens de dados amigável para as propriedades no JSON conteúdo para que possa mais fácil de utilizar as propriedades nas suas aplicações lógicas. | 
||| 

Para criar transformações de JSON mais complexas, consulte [executar transformações de JSON com modelos de fluidos avançadas](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os exemplos neste artigo, precisa destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">Inscreva-se uma conta gratuita do Azure</a>.

* A aplicação lógica em que precisa que a operação para trabalhar com dados 

  Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* R [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts) como a primeira etapa na sua aplicação lógica 

  Operações de dados estão disponíveis apenas como ações, antes de poder utilizar estas ações, iniciar a aplicação lógica com um acionador e incluem quaisquer outras ações necessárias para a criação de saídas quer.

<a name="compose-action"></a>

## <a name="compose-action"></a>Ação de composição

Para construir uma única saída, como um objeto JSON a partir de várias entradas, pode utilizar o **operações de dados - compor** ação. Suas entradas podem ter vários tipos, como números inteiros, booleanos, matrizes, objetos JSON e quaisquer outro nativo escreva que suporta Azure Logic Apps, por exemplo, o binário e XML. Em seguida, pode utilizar a saída nas ações que se seguem após a **Compose** ação. O **Compose** também ação se pode poupar da introduzir repetidamente as mesmas entradas enquanto cria o fluxo de trabalho da sua aplicação lógica. 

Por exemplo, pode construir uma mensagem JSON a partir de várias variáveis, como variáveis de cadeia de caracteres que armazenam os nomes das pessoas e o apelido e uma variável de inteiro que armazena a idade das pessoas. Aqui, o **Compose** ação aceita estas entradas:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

e cria esta saída:

`{"age":35,"fullName":"Owens,Sophie"}`

Para experimentar um exemplo, siga estes passos, utilizando o Estruturador da aplicação lógica. Ou, se preferir trabalhar no editor de vista de código, pode copiar o exemplo **Compose** e **Initialize variable** definições de ação do artigo na sua própria aplicação de lógica subjacentes do fluxo de trabalho definição: [Exemplos de código de operação de dados - compor](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a> ou o Visual Studio, abra a aplicação lógica no Estruturador da aplicação lógica. 

   Este exemplo utiliza o portal do Azure e uma aplicação lógica com um **periodicidade** acionador e vários **Initialize variable** ações. 
   Estas ações são configuradas para a criação de duas variáveis de cadeia de caracteres e uma variável de inteiro. Quando testar a sua aplicação lógica mais tarde, pode executar manualmente a sua aplicação sem aguardar o disparador seja acionado.

   ![Aplicação de lógica de exemplo inicial](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

2. Na sua aplicação lógica onde pretende criar a saída, siga um destes passos: 

   * Para adicionar uma ação no último passo, escolha **novo passo** > **adicionar uma ação**.

     ![Adicionar ação](./media/logic-apps-perform-data-operations/add-compose-action.png)

   * Para adicionar uma ação entre etapas, mova o rato sobre a seta para a ligação para que apareça o sinal de adição (+). 
   Selecione o sinal de adição e, em seguida, selecione **adicionar uma ação**.

3. Na caixa de pesquisa, introduza "compor" como o filtro. Na lista de ações, selecione a ação: **Compor**

   ![Selecione a ação de "Compor"](./media/logic-apps-perform-data-operations/select-compose-action.png)

4. Na **entradas** caixa, fornecer as entradas que pretende para a criação da saída. 

   Neste exemplo, quando clica dentro da **entradas** caixa, é apresentada uma lista de conteúdo dinâmico pelo que pode selecionar as variáveis criadas anteriormente:

   ![Selecione entradas para compor](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Eis o exemplo concluído **Compose** ação: 

   ![Ação de "Compor" concluída](./media/logic-apps-perform-data-operations/finished-compose-action.png)

5. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**.

Para obter mais informações sobre esta ação na sua definição de fluxo de trabalho subjacentes, consulte a [ação de composição](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action). 

### <a name="test-your-logic-app"></a>Teste a aplicação lógica

Para confirmar se o **Compose** ação cria os resultados esperados, envie uma notificação que inclui a saída a partir do **Compose** ação.

1. Na sua aplicação lógica, adicionar uma ação que pode enviar os resultados a partir da **Compose** ação.

2. Em ação, clique em qualquer local que pretende que os resultados sejam apresentados. Quando a lista de conteúdo dinâmico é aberto, menos os **Compose** ação, selecione **saída**. 

   Este exemplo utiliza a **Office 365 Outlook - enviar um e-mail** ação e inclui o **saída** campos no corpo e assunto do e-mail:

   ![Campos de "Saída" na ação "Enviar e-mail"](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

3. Agora, executar manualmente a aplicação lógica. Na barra de ferramentas da estruturador, escolha **executar**. 

   Com base no conector de e-mail que utilizou, seguem-se os resultados obtidos:

   ![Enviar um e-mail com os resultados de ação "Compor"](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Criar uma ação de tabela CSV

Para criar uma tabela de valores separados por vírgulas (CSV) que tem as propriedades e os valores dos objetos JavaScript Object Notation (JSON) numa matriz, utilize o **operações de dados - criar tabela CSV** ação. Em seguida, pode utilizar a tabela resultante nas ações que se seguem a **criar tabela CSV** ação. 

Se preferir trabalhar no editor de vista de código, pode copiar o exemplo **criar tabela CSV** e **Initialize variable** definições de ação do artigo na sua própria aplicação de lógica subjacentes do fluxo de trabalho definição: [Exemplos de código de operação de dados - criar tabela CSV](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example) 

1. Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a> ou o Visual Studio, abra a aplicação lógica no Estruturador da aplicação lógica. 

   Este exemplo utiliza o portal do Azure e uma aplicação lógica com um **periodicidade** acionador e uma **Initialize variable** ação. 
   A ação está definida para a criação de uma variável cujo valor inicial é uma matriz que tem algumas propriedades e valores no formato JSON. 
   Quando testar a sua aplicação lógica mais tarde, pode executar manualmente a sua aplicação sem aguardar o disparador seja acionado.

   ![Aplicação de lógica de exemplo inicial](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

2. Na sua aplicação lógica onde pretende criar a tabela CSV, siga um destes passos: 

   * Para adicionar uma ação no último passo, escolha **novo passo** > **adicionar uma ação**.

     ![Adicionar ação](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Para adicionar uma ação entre etapas, mova o rato sobre a seta para a ligação para que apareça o sinal de adição (+). 
   Selecione o sinal de adição e, em seguida, selecione **adicionar uma ação**.

3. Na caixa de pesquisa, introduza "Criar tabela csv" como o filtro. Na lista de ações, selecione a ação: **Criar tabela CSV**

   ![Selecione "Criar CSV tabela" ação](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

4. Na **partir** caixa, forneça a matriz ou uma expressão que pretende para criar a tabela. 

   Neste exemplo, quando clica dentro da **de** caixa, é apresentada uma lista de conteúdo dinâmico pelo que pode selecionar a variável criada anteriormente:

   ![Selecione a saída de matriz para a criação de tabela CSV](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   Eis o exemplo concluído **criar tabela CSV** ação: 

   ![Concluída "Criar CSV tabela" ação](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

   Por predefinição, esta ação cria automaticamente as colunas com base nos itens de matriz. 
   Para criar manualmente os cabeçalhos de coluna e os valores, escolha **Mostrar opções avançadas**. 
   Para fornecer apenas os valores personalizados, altere **colunas** ao **personalizado**. 
   Para fornecer também os cabeçalhos de coluna personalizada, altere **incluir cabeçalhos** ao **Sim**. 

   > [!TIP]
   > Para criar tokens amigáveis para as propriedades de objetos JSON, pelo que pode selecionar essas propriedades como entradas, utilize o [Parse JSON](#parse-json-action) antes de chamar os **criar tabela CSV** ação.

5. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**.

Para obter mais informações sobre esta ação na sua definição de fluxo de trabalho subjacentes, consulte a [ação de tabela](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Teste a aplicação lógica

Para confirmar se o **criar tabela CSV** ação cria os resultados esperados, envie uma notificação que inclui a saída a partir do **criar tabela CSV** ação.

1. Na sua aplicação lógica, adicionar uma ação que pode enviar os resultados a partir da **criar tabela CSV** ação.

2. Em ação, clique em qualquer local que pretende que os resultados sejam apresentados. Quando a lista de conteúdo dinâmico é aberto, menos os **criar tabela CSV** ação, selecione **saída**. 

   Este exemplo utiliza a **Office 365 Outlook - enviar um e-mail** ação e inclui o **saída** campo no corpo do e-mail:

   ![Campos de "Saída" na ação "Enviar e-mail"](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

3. Agora, executar manualmente a aplicação lógica. Na barra de ferramentas da estruturador, escolha **executar**. 

   Com base no conector de e-mail que utilizou, seguem-se os resultados obtidos:

   ![Por e-mail para "Criar CSV tabela" resultados de ação](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Criar uma ação de tabela HTML

Para criar uma tabela HTML que tem as propriedades e os valores dos objetos JavaScript Object Notation (JSON) numa matriz, utilize o **operações de dados - criar tabela HTML** ação. Em seguida, pode utilizar a tabela resultante nas ações que se seguem a **criar tabela HTML** ação.

Se preferir trabalhar no editor de vista de código, pode copiar o exemplo **criar tabela HTML** e **Initialize variable** definições de ação do artigo na sua própria aplicação de lógica subjacentes do fluxo de trabalho definição: [Exemplos de código de operação de dados - criar tabela HTML](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a> ou o Visual Studio, abra a aplicação lógica no Estruturador da aplicação lógica. 

   Este exemplo utiliza o portal do Azure e uma aplicação lógica com um **periodicidade** acionador e uma **Initialize variable** ação. 
   A ação está definida para a criação de uma variável cujo valor inicial é uma matriz que tem algumas propriedades e valores no formato JSON. 
   Quando testar a sua aplicação lógica mais tarde, pode executar manualmente a sua aplicação sem aguardar o disparador seja acionado.

   ![Aplicação de lógica de exemplo inicial](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

2. Na sua aplicação lógica onde pretende criar uma tabela HTML, siga um destes passos: 

   * Para adicionar uma ação no último passo, escolha **novo passo** > **adicionar uma ação**.

     ![Adicionar ação](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Para adicionar uma ação entre etapas, mova o rato sobre a seta para a ligação para que apareça o sinal de adição (+). 
   Selecione o sinal de adição e, em seguida, selecione **adicionar uma ação**.

3. Na caixa de pesquisa, introduza "Criar tabela html" como o filtro. Na lista de ações, selecione a ação: **Criar tabela HTML**

   ![Selecione a ação de "Tabela de criar HTML"](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

4. Na **partir** caixa, forneça a matriz ou uma expressão que pretende para criar a tabela. 

   Neste exemplo, quando clica dentro da **de** caixa, é apresentada uma lista de conteúdo dinâmico pelo que pode selecionar a variável criada anteriormente:

   ![Selecione a saída de matriz para a criação de tabela HTML](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   Eis o exemplo concluído **criar tabela HTML** ação: 

   ![Ação de "Tabela de criar HTML" concluído](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

   Por predefinição, esta ação cria automaticamente as colunas com base nos itens de matriz. 
   Para criar manualmente os cabeçalhos de coluna e os valores, escolha **Mostrar opções avançadas**. 
   Para fornecer apenas os valores personalizados, altere **colunas** ao **personalizado**. 
   Para fornecer também os cabeçalhos de coluna personalizada, altere **incluir cabeçalhos** ao **Sim**. 

   > [!TIP]
   > Para criar tokens amigáveis para as propriedades de objetos JSON, pelo que pode selecionar essas propriedades como entradas, utilize o [Parse JSON](#parse-json-action) antes de chamar os **criar tabela HTML** ação.

5. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**.

Para obter mais informações sobre esta ação na sua definição de fluxo de trabalho subjacentes, consulte a [ação de tabela](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Teste a aplicação lógica

Para confirmar se o **criar tabela HTML** ação cria os resultados esperados, envie uma notificação que inclui a saída a partir do **criar tabela HTML** ação.

1. Na sua aplicação lógica, adicionar uma ação que pode enviar os resultados a partir da **criar tabela HTML** ação.

2. Em ação, clique em qualquer local que pretende que os resultados sejam apresentados. Quando a lista de conteúdo dinâmico é aberto, menos os **criar tabela HTML** ação, selecione **saída**. 

   Este exemplo utiliza a **Office 365 Outlook - enviar um e-mail** ação e inclui o **saída** campo no corpo do e-mail:

   ![Campos de "Saída" na ação "Enviar e-mail"](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)
   
   > [!NOTE]
   > Ao incluir a tabela HTML de saída numa ação de e-mail, certifique-se de que defina o **é HTML** propriedade **Sim** no e-mail de ação avançadas do opções. Dessa forma, a ação de e-mail corretamente formata a tabela HTML.

3. Agora, executar manualmente a aplicação lógica. Na barra de ferramentas da estruturador, escolha **executar**. 

   Com base no conector de e-mail que utilizou, seguem-se os resultados obtidos:

   ![Enviar um e-mail com os resultados de ação de "Tabela de criar HTML"](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Ação de matriz de filtro

Para criar uma matriz mais pequena que tem itens, que satisfazem critérios específicos, de uma matriz existente, utilize o **operações de dados - matriz de filtro** ação. Em seguida, pode utilizar a matriz filtrada nas ações que se seguem após a **matriz de filtro** ação. 

> [!NOTE]
> Qualquer texto de filtro que utilizar na sua condição diferencia maiúsculas de minúsculas. Além disso, esta ação não é possível alterar o formato ou componentes de itens na matriz. 
> 
> Para ações utilizar a saída de matriz do **matriz de filtro** ação, essas ações devem aceitar matrizes como entrada ou poderá ter de transformar a matriz de saída em formato compatível com o outro. 

Se preferir trabalhar no editor de vista de código, pode copiar o exemplo **matriz de filtro** e **Initialize variable** definições de ação do artigo na sua própria aplicação de lógica subjacentes do fluxo de trabalho definição: [Exemplos de código de operação de dados - matriz de filtro](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example) 

1. Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a> ou o Visual Studio, abra a aplicação lógica no Estruturador da aplicação lógica. 

   Este exemplo utiliza o portal do Azure e uma aplicação lógica com um **periodicidade** acionador e uma **Initialize variable** ação. 
   A ação está definida para a criação de uma variável cujo valor inicial é uma matriz que tem alguns números inteiros de exemplo. Quando testar a sua aplicação lógica mais tarde, pode executar manualmente a sua aplicação sem aguardar o disparador seja acionado.

   > [!NOTE]
   > Embora este exemplo utiliza uma matriz de inteiros simples, esta ação é especialmente útil para as matrizes de onde pode filtrar com base nas propriedades dos objetos e os valores de objeto JSON.

   ![Aplicação de lógica de exemplo inicial](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

2. Na sua aplicação lógica onde pretende criar a matriz filtrada, siga um destes passos: 

   * Para adicionar uma ação no último passo, escolha **novo passo** > **adicionar uma ação**.

     ![Adicionar ação](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Para adicionar uma ação entre etapas, mova o rato sobre a seta para a ligação para que apareça o sinal de adição (+). 
   Selecione o sinal de adição e, em seguida, selecione **adicionar uma ação**.

3. Na caixa de pesquisa, introduza "matriz de filtro" como o filtro. Na lista de ações, selecione a ação: **Matriz de filtro**

   ![Selecione a ação "Filtrar matriz"](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

4. Na **partir** caixa, forneça a matriz ou uma expressão que pretende filtrar. 

   Neste exemplo, quando clica dentro da **de** caixa, é apresentada uma lista de conteúdo dinâmico pelo que pode selecionar a variável criada anteriormente:

   ![Selecione a saída de matriz para a criação de matriz filtrado](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

5. Para a condição, especifique os itens de matriz para comparar, selecione o operador de comparação e especificar o valor de comparação.

   Este exemplo utiliza a **item()** função para aceder a cada item da matriz ao mesmo tempo a **matriz de filtro** pesquisas de ação para a matriz de itens cujo valor é superior a 1:
   
   ![Conclusão da ação "Filtrar matriz"](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

6. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**.

Para obter mais informações sobre esta ação na sua definição de fluxo de trabalho subjacente, veja [ação de consulta](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Teste a aplicação lógica

Para confirmar se **matriz de filtro** ação cria os resultados esperados, envie uma notificação que inclui a saída a partir do **matriz de filtro** ação.

1. Na sua aplicação lógica, adicionar uma ação que pode enviar os resultados a partir da **matriz de filtro** ação.

2. Em ação, clique em qualquer local que pretende que os resultados sejam apresentados. Quando abre a lista de conteúdo dinâmico, escolha **expressão**. Para obter o resultado de matriz do **matriz de filtro** ação, introduza a expressão que inclui a **matriz de filtro** nome da ação:

   ```
   @actionBody('Filter_array')
   ```

   Este exemplo utiliza a **Office 365 Outlook - enviar um e-mail** ação e inclui as saídas resultantes a **actionBody('Filter_array')** expressão no corpo do e-mail:

   ![Ação produz na ação "Enviar e-mail"](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

3. Agora, executar manualmente a aplicação lógica. Na barra de ferramentas da estruturador, escolha **executar**. 

   Com base no conector de e-mail que utilizou, seguem-se os resultados obtidos:

   ![Enviar um e-mail com os resultados de ação "Matriz de filtro"](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Junte-se a ação

Para criar uma cadeia de caracteres que tem todos os itens de uma matriz e separar esses itens com um caráter delimitador específica, utilize o **operações de dados - associar** ação. Em seguida, pode utilizar a cadeia de caracteres nas ações que se seguem após a **associar** ação.

Se preferir trabalhar no editor de vista de código, pode copiar o exemplo **aderir** e **Initialize variable** definições de ação do artigo na sua própria aplicação de lógica subjacentes da definição de fluxo de trabalho: [Junte-se exemplos de código de operação de dados-](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example) 

1. Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a> ou o Visual Studio, abra a aplicação lógica no Estruturador da aplicação lógica. 

   Este exemplo utiliza o portal do Azure e uma aplicação lógica com um **periodicidade** acionador e uma **Initialize variable** ação. 
   Esta ação é configurada para a criação de uma variável cujo valor inicial é uma matriz que tem alguns números inteiros de exemplo. 
   Ao testar a aplicação lógica mais tarde, pode executar manualmente a sua aplicação sem aguardar o disparador seja acionado.

   ![Aplicação de lógica de exemplo inicial](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

2. Na sua aplicação lógica onde pretende criar a cadeia de caracteres a partir de uma matriz, siga um destes passos: 

   * Para adicionar uma ação no último passo, escolha **novo passo** > **adicionar uma ação**.

     ![Adicionar ação](./media/logic-apps-perform-data-operations/add-join-action.png)

   * Para adicionar uma ação entre etapas, mova o rato sobre a seta para a ligação para que apareça o sinal de adição (+). 
   Selecione o sinal de adição e, em seguida, selecione **adicionar uma ação**.

3. Na caixa de pesquisa, introduza "associar" como o filtro. Na lista de ações, selecione a ação: **Associar**

   ![Selecione a ação de "Dados operações – associar"](./media/logic-apps-perform-data-operations/select-join-action.png)

4. Na **partir** caixa, forneça a matriz que tenha os itens que pretende associar como uma cadeia de caracteres. 

   Neste exemplo, quando clica dentro da **de** caixa de lista de conteúdo dinâmico que aparece, pelo que pode selecionar a variável criada anteriormente:  

   ![Selecione a saída de matriz para criar a cadeia de caracteres](./media/logic-apps-perform-data-operations/configure-join-action.png)

5. Na **associar** , introduza o caráter que pretende para separar cada item da matriz. 

   Este exemplo utiliza dois pontos (:) como separador.

   ![Fornecer o caráter de separação](./media/logic-apps-perform-data-operations/finished-join-action.png)

6. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**.

Para obter mais informações sobre esta ação na sua definição de fluxo de trabalho subjacentes, consulte a [Junte-se a ação](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Teste a aplicação lógica

Para confirmar se o **aderir** ação cria os resultados esperados, envie uma notificação que inclui a saída do **associar** ação. 

1. Na sua aplicação lógica, adicionar uma ação que pode enviar os resultados a partir da **associar** ação.

2. Em ação, clique em qualquer local que pretende que os resultados sejam apresentados. Quando a lista de conteúdo dinâmico é aberto, menos os **aderir** ação, selecione **saída**. 

   Este exemplo utiliza a **Office 365 Outlook - enviar um e-mail** ação e inclui o **saída** campo no corpo do e-mail:

   ![Campos de "Saída" na ação "Enviar e-mail"](./media/logic-apps-perform-data-operations/send-email-join-action.png)

3. Agora, executar manualmente a aplicação lógica. Na barra de ferramentas da estruturador, escolha **executar**. 

   Com base no conector de e-mail que utilizou, seguem-se os resultados obtidos:

   ![Enviar um e-mail com resultados de ação "Join"](./media/logic-apps-perform-data-operations/join-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Analisar a ação de JSON

As propriedades de referência ou de acesso em JavaScript Object Notation (JSON) conteúdo, pode criar campos amigáveis ou tokens para as propriedades utilizando o **operações de dados - Parse JSON** ação.
Dessa forma, pode selecionar as propriedades da lista de conteúdo dinâmica ao especificar entradas para a aplicação lógica. Para esta ação, pode fornecer um esquema JSON ou gerar um esquema JSON do conteúdo JSON de exemplo ou payload.

Se preferir trabalhar no editor de vista de código, pode copiar o exemplo **Parse JSON** e **Initialize variable** definição de fluxo de trabalho subjacentes do definições de ação do artigo na sua própria aplicação lógica : [Exemplos de código de operação de dados - analisar JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example) 

1. Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a> ou o Visual Studio, abra a aplicação lógica no Estruturador da aplicação lógica. 

   Este exemplo utiliza o portal do Azure e uma aplicação lógica com um **periodicidade** acionador e uma **Initialize variable** ação. 
   A ação está definida para a criação de uma variável cujo valor inicial é um objeto JSON que tem propriedades e valores. 
   Quando testar a sua aplicação lógica mais tarde, pode executar manualmente a sua aplicação sem aguardar o disparador seja acionado.

   ![Aplicação de lógica de exemplo inicial](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

2. Na sua aplicação lógica em que pretende analisar o conteúdo JSON, siga um destes passos: 

   * Para adicionar uma ação no último passo, escolha **novo passo** > **adicionar uma ação**.

     ![Adicionar ação](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Para adicionar uma ação entre etapas, mova o rato sobre a seta para a ligação para que apareça o sinal de adição (+). 
   Selecione o sinal de adição e, em seguida, selecione **adicionar uma ação**.

3. Na caixa de pesquisa, introduza "analisar json" como o filtro. Na lista de ações, selecione a ação: **Analisar JSON**

   ![Selecione a ação "Parse JSON"](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

4. Na **conteúdo** caixa, forneça o conteúdo JSON que pretende analisar. 

   Neste exemplo, quando clica dentro da **conteúdo** caixa, é apresentada uma lista de conteúdo dinâmico pelo que pode selecionar a variável criada anteriormente:

   ![Selecione o objeto JSON para ação parse JSON](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

5. Introduza o esquema JSON que descreve o conteúdo JSON que está analisando. 

   Neste exemplo, aqui está o esquema JSON:

   ![Fornecer o esquema JSON para o objeto JSON que pretende analisar](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Se não tiver o esquema, pode gerar esse esquema do conteúdo JSON, ou *payload*, se estiver analisando. 
   
   1. Na **Parse JSON** ação, selecione **utilizar payload de amostra para gerar esquema**.

   2. Sob **introduza ou cole um payload JSON de exemplo**, forneça o conteúdo JSON e, em seguida, escolha **feito**.

      ![Introduza o conteúdo JSON para gerar o esquema](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

6. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**.

Para obter mais informações sobre esta ação na sua definição de fluxo de trabalho subjacente, veja [ação Parse JSON](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Teste a aplicação lógica

Para confirmar se o **Parse JSON** ação cria os resultados esperados, envie uma notificação que inclui a saída a partir do **Parse JSON** ação.

1. Na sua aplicação lógica, adicionar uma ação que pode enviar os resultados a partir da **Parse JSON** ação.

2. Em ação, clique em qualquer local que pretende que os resultados sejam apresentados. Quando a lista de conteúdo dinâmico é aberto, menos os **Parse JSON** ação, agora, pode selecionar as propriedades do conteúdo JSON analisado.

   Este exemplo utiliza a **Office 365 Outlook - enviar um e-mail** ação e inclui o **FirstName**, **LastName**, e **E-Mail** campos no corpo do e-mail:

   ![Propriedades JSON na ação "Enviar e-mail"](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Esta é a ação de e-mail foi concluído:

   ![Ação de e-mail foi concluído](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

3. Agora, executar manualmente a aplicação lógica. Na barra de ferramentas da estruturador, escolha **executar**. 

   Com base no conector de e-mail que utilizou, seguem-se os resultados obtidos:

   ![Enviar um e-mail com resultados de ação "Join"](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Ação de seleção

Para criar uma matriz com objetos JSON criados a partir de valores numa matriz existente, utilize o **operações de dados - selecionar** ação. Por exemplo, pode criar um objeto JSON para cada valor numa matriz de número inteiro especificando as propriedades que cada objeto JSON tem de ter e como mapear os valores da matriz de origem para essas propriedades. E embora possa alterar os componentes nesses objetos JSON, a matriz de saída tem sempre o mesmo número de itens que a matriz de origem.

> [!NOTE]
> Para ações utilizar a saída de matriz do **selecione** ação, essas ações devem aceitar matrizes como entrada ou poderá ter de transformar a matriz de saída em formato compatível com o outro. 

Se preferir trabalhar no editor de vista de código, pode copiar o exemplo **selecionar** e **Initialize variable** definições de ação do artigo na sua própria aplicação de lógica subjacentes da definição de fluxo de trabalho: [Exemplos de código de operação de dados - selecione](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a> ou o Visual Studio, abra a aplicação lógica no Estruturador da aplicação lógica. 

   Este exemplo utiliza o portal do Azure e uma aplicação lógica com um **periodicidade** acionador e uma **Initialize variable** ação. 
   A ação está definida para a criação de uma variável cujo valor inicial é uma matriz que tem alguns números inteiros de exemplo. 
   Quando testar a sua aplicação lógica mais tarde, pode executar manualmente a sua aplicação sem aguardar o disparador seja acionado.

   ![Aplicação de lógica de exemplo inicial](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

2. Na sua aplicação lógica onde pretende criar a matriz, siga um destes passos: 

   * Para adicionar uma ação no último passo, escolha **novo passo** > **adicionar uma ação**.

     ![Adicionar ação](./media/logic-apps-perform-data-operations/add-select-action.png)

   * Para adicionar uma ação entre etapas, mova o rato sobre a seta para a ligação para que apareça o sinal de adição (+). 
   Selecione o sinal de adição e, em seguida, selecione **adicionar uma ação**.

3. Na caixa de pesquisa, introduza "selecionar" como o filtro. Na lista de ações, selecione a ação: **Selecionar**

   ![Selecione a ação "Select"](./media/logic-apps-perform-data-operations/select-select-action.png)

4. Na **partir** caixa, especifique a matriz de origem que pretende.

   Neste exemplo, quando clica dentro da **de** caixa, é apresentada uma lista de conteúdo dinâmico pelo que pode selecionar a variável criada anteriormente:

   ![Selecione a matriz de origem para a ação de seleção](./media/logic-apps-perform-data-operations/configure-select-action.png)

5. Na **mapa** coluna de lado esquerdo da caixa, forneça o nome de propriedade que pretende atribuir a cada valor na matriz de origem. Na coluna da direita, especifique uma expressão que representa o valor que pretende atribuir a propriedade.

   Este exemplo especifica "Product_ID" como o nome da propriedade para atribuir a cada valor na matriz de números inteiros, utilizando o **item()** função numa expressão que acede a cada item da matriz. 

   ![Especificar a propriedade do objeto JSON e os valores para a matriz que pretende criar](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Esta é a ação foi concluída:

   ![Conclusão da ação de seleção](./media/logic-apps-perform-data-operations/finished-select-action.png)

6. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**.

Para obter mais informações sobre esta ação na sua definição de fluxo de trabalho subjacente, veja [selecionar ação](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Teste a aplicação lógica

Para confirmar se o **selecione** ação cria os resultados esperados, envie uma notificação que inclui a saída a partir do **selecione** ação.

1. Na sua aplicação lógica, adicionar uma ação que pode enviar os resultados a partir da **selecione** ação.

2. Em ação, clique em qualquer local que pretende que os resultados sejam apresentados. Quando abre a lista de conteúdo dinâmico, escolha **expressão**. Para obter o resultado de matriz do **selecionar** ação, introduza a expressão que inclui a **selecione** nome da ação:

   ```
   @actionBody('Select')
   ```

   Este exemplo utiliza a **Office 365 Outlook - enviar um e-mail** ação e inclui as saídas resultantes a **actionBody('Select')** expressão no corpo do e-mail:

   ![Ação produz na ação "Enviar e-mail"](./media/logic-apps-perform-data-operations/send-email-select-action.png)

3. Agora, executar manualmente a aplicação lógica. Na barra de ferramentas da estruturador, escolha **executar**. 

   Com base no conector de e-mail que utilizou, seguem-se os resultados obtidos:

   ![Enviar um e-mail com os resultados de ação "Select"](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [conectores do Logic Apps](../connectors/apis-list.md)
