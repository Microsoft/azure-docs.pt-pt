---
title: 'Tutorial: Integrar o Power BI com o serviço cognitivos de análise de texto'
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar a Análise de Texto para extrair expressões-chave de textos armazenados no Power BI.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 4489fc82f836d8c311fcd776e211670897618b54
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889482"
---
# <a name="tutorial-integrate-power-bi-with-the-text-analytics-cognitive-service"></a>Tutorial: Integrar o Power BI com o serviço cognitivos de análise de texto

O Microsoft Power BI Desktop é uma aplicação gratuita que lhe permite ligar-se, transformar e visualizar os seus dados. O serviço de Análise de Texto, que faz parte dos Serviços Cognitivos do Microsoft Azure, oferece processamento de linguagem natural. Ao ser-lhe dado texto bruto não estruturado, ele consegue extrair as expressões mais importantes, analisar sentimentos e identificar entidades conhecidas, como marcas. Em conjunto, estas ferramentas podem ajudá-lo a ver rapidamente aquilo de que os seus clientes estão a falar e como se sentem em relação a esse assunto.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilizar o Power BI Desktop para importar e transformar dados
> * Criar uma função personalizada no Power BI Desktop
> * Integrar o Power BI Desktop com a API de Expressões-Chave da Análise de Texto
> * Utilizar a API de Expressões-Chave da Análise de Texto para extrair as expressões mais importantes dos comentários dos clientes
> * Criar uma nuvem de palavras a partir dos comentários dos clientes

## <a name="prerequisites"></a>Pré-requisitos
<a name="Prerequisites"></a>

- Do Microsoft Power BI Desktop. [Transfira sem custos](https://powerbi.microsoft.com/get-started/).
- Uma conta do Microsoft Azure. [Inicie uma avaliação gratuita](https://azure.microsoft.com/free/) ou [inicie sessão](https://portal.azure.com/).
- Uma conta de API dos Serviços Cognitivos com a API de Análise de Texto. Se não tiver uma, poderá [inscrever-se](../../cognitive-services-apis-create-account.md) e utilizar o escalão gratuito de 5000 transações/mês (veja [os detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) para concluir este tutorial.
- A [chave de acesso da Análise de Texto](../how-tos/text-analytics-how-to-access-key.md) que foi gerada automaticamente durante a sua inscrição.
- De comentários de clientes. Pode utilizar os [nossos dados de exemplo](https://aka.ms/cogsvc/ta) ou seus próprios dados. Este tutorial parte do princípio de que está a utilizar os nossos dados de exemplo.

## <a name="load-customer-data"></a>Carregar dados do cliente
<a name="LoadingData"></a>

Para começar, abra o Power BI Desktop e carregue o ficheiro de valores separados por vírgula (CSV) `FabrikamComments.csv` que transferiu nos [Pré-requisitos](#Prerequisites). Este ficheiro representa um dia de atividades hipotéticas num fórum de suporte fictício de uma empresa.

> [!NOTE]
> O Power BI pode utilizar dados de várias origens, como o Facebook ou uma base de dados SQL. Saiba mais em [Integração do Facebook no Power BI](https://powerbi.microsoft.com/integrations/facebook/) e em [Integração do SQL Server no Power BI](https://powerbi.microsoft.com/integrations/sql-server/).

Na janela principal do Power BI Desktop, selecione o friso **Base**. No grupo do friso **Dados externos**, abra o menu pendente **Obter Dados** e selecione **Texto/CSV**.

![[Botão Get Data (Obter Dados)]](../media/tutorials/power-bi/get-data-button.png)

É apresentada a caixa de diálogo Open (Abrir). Navegue para a pasta Transferências ou para a pasta para onde transferiu o `FabrikamComments.csv` ficheiro. Clique em `FabrikamComments.csv` e, depois, no botão **Open** (Abrir). É apresentada a caixa de diálogo de importação do CSV.

![[Caixa de diálogo Importar CSV]](../media/tutorials/power-bi/csv-import.png)

A caixa de diálogo de importação do CSV permite-lhe verificar se o Power BI Desktop detetou corretamente o conjunto de carateres, o delimitador, as linhas de cabeçalho e os tipos de colunas. Estas informações estão todas corretas, assim, clique em **Carregar**.

Para ver os dados carregados, clique no botão **Vista de Dados**, na margem esquerda da área de trabalho do Power BI. É aberta uma tabela que contém os dados, como no Microsoft Excel.

![[Vista inicial dos dados importados]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="prepare-the-data"></a>Preparar os dados
<a name="PreparingData"></a>

Poderá ser necessário transformar os seus dados no Power BI Desktop antes que os mesmos estejam prontos para serem processados pela API de Expressões-Chave do serviço Análise de Texto.

Os dados de exemplo contêm uma coluna `subject` e uma coluna `comment`. Com a função Intercalar Colunas no Power BI Desktop, pode extrair expressões-chave dos dados nas duas colunas, em vez de apenas da coluna `comment`.

No Power BI Desktop, selecione o friso **Base**. No grupo **Dados externos**, clique em **Editar Consultas**.

![[Grupo External Data no friso Base]](../media/tutorials/power-bi/edit-queries.png)

Selecione `FabrikamComments` na lista **Consultas**, no lado esquerdo da janela, caso ainda não esteja selecionado.

Agora, selecione as colunas `subject` e `comment` na tabela. Poderá ter de se deslocar na horizontal para ver estas colunas. Primeiro, clique no cabeçalho de coluna `subject`, mantenha premida a tecla Control e clique no cabeçalho de coluna `comment`.

![[Selecionar os campos que vão ser unidos]](../media/tutorials/power-bi/select-columns.png)

Selecione o friso **Transformar**. No grupo **Text Columns** (Colunas de Texto) do friso, clique em **Intercalar colunas**. É apresentada a caixa de diálogo Merge Columns (Unir Colunas).

![[Unir campos com a caixa de diálogo Merge Columns]](../media/tutorials/power-bi/merge-columns.png)

Na caixa de diálogo Intercalar colunas, escolha `Tab` como o separador e clique em **OK**.

Também pode considerar filtrar e deixar de fora as mensagens em branco ao utilizar o filtro Remover Vazio ou remover os carateres que não podem ser impressos com a transformação Limpar. Se os seus dados tiverem uma coluna como a coluna `spamscore` no ficheiro de exemplo, poderá utilizar um filtro Número para ignorar os comentários de “spam”.

## <a name="understand-the-api"></a>Compreender a API
<a name="UnderstandingAPI"></a>

A [API de Expressões-Chave](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) do serviço Análise de Texto consegue processar até mil documentos de texto por pedido HTTP. O Power BI prefere lidar com registos um de cada vez, pelo que, neste tutorial, as nossas chamadas à API vão incluir apenas um documento para cada uma. A API de Expressões-Chave precisa dos seguintes campos para cada documento que está a ser processado.

| | |
| - | - |
| `id`  | Um identificador exclusivo para este documento dentro do pedido. A resposta também contém este campo. Desta forma, se processar mais do que um documento, poderá associar facilmente as expressões-chave extraídas ao documento do qual provêm. Neste tutorial, uma vez que está a processar apenas um documento por pedido, pode codificar o valor de `id` para ser o mesmo para cada pedido.|
| `text`  | O texto que vai ser processado. O valor deste campo é proveniente da coluna `Merged` que criou na [secção anterior](#PreparingData), que contém o texto do assunto e o texto do comentário combinados. A API de expressões chave requer que estes dados não ter mais de aproximadamente 5,120 carateres.|
| `language` | O código para o idioma natural em que o documento está escrito. Todas as mensagens nos dados de exemplo estão em inglês, pelo que pode codificar o valor `en` para este campo.|

## <a name="create-a-custom-function"></a>Criar uma função personalizada
<a name="CreateCustomFunction"></a>

Agora, está pronto para criar a função personalizada que vai integrar o Power BI e a Análise de Texto. A função recebe o texto que vai ser processado como parâmetro. Converte os dados de e para o formato JSON necessário e faz o pedido HTTP para a API de Expressões-Chave. Em seguida, a função analisa a resposta da API e devolve uma cadeia que contém uma lista separada por vírgulas das expressões-chave extraídas.

> [!NOTE]
> As funções personalizadas do Power BI Desktop são escritas na [linguagem de fórmulas M do Power Query](https://msdn.microsoft.com/library/mt211003.aspx), ou apenas “M”, para abreviar. M é uma linguagem de programação funcional com base em [F#](https://docs.microsoft.com/dotnet/fsharp/). No entanto, não é preciso ser programador para concluir este tutorial. O código necessário está incluído abaixo.

No Power BI Desktop, verifique se que está ainda na janela Editor de Consultas. Se não estiver, selecione o friso **Base** e, no grupo **Dados externos** clique em **Editar Consultas**.

Agora, no friso **Base** no grupo **Nova Consulta**, abra o menu pendente **Nova Origem** e selecione **Consulta em Branco**. 

É apresentada uma consulta nova, inicialmente denominada `Query1`, na lista Consultas. Faça duplo clique nesta entrada e dê-lhe o nome `KeyPhrases`.

Agora, no friso **Base**, no grupo **Consulta**, clique em **Editor Avançado** para abrir a janela deste editor. Elimine o código que já aparece na janela e cole o código seguinte. 

> [!NOTE]
> Os exemplos abaixo pressupõem que o ponto final da API de Análise de Texto começa com `https://westus.api.cognitive.microsoft.com`. A Análise de Texto permite-lhe criar subscrições em 13 regiões diferentes. Se se tiver inscrito no serviço noutra região, confirme que utiliza o ponto final da região que selecionou. Pode encontrar este ponto final ao iniciar sessão no [portal do Azure](https://azure.microsoft.com/features/azure-portal/), ao selecionar a sua subscrição de Análise de Texto e selecionar a página Descrição Geral.

```fsharp
// Returns key phrases from the text in a comma-separated list
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = Text.Lower(Text.Combine(jsonresp[documents]{0}[keyPhrases], ", "))
in  keyphrases
```

Substitua `YOUR_API_KEY_HERE` pela chave de acesso da Análise de Texto. Também pode encontrar esta chave ao iniciar sessão no [portal do Azure](https://azure.microsoft.com/features/azure-portal/), ao selecionar a subscrição de Análise de Texto e selecionar a página Descrição Geral. Garanta que deixa as aspas antes e depois da chave. Em seguida, clique em **Done** (Concluído).

## <a name="use-the-custom-function"></a>Utilizar a função personalizada
<a name="UseCustomFunction"></a>

Agora, pode utilizar a função personalizada para extrair expressões-chave de cada um dos comentários do cliente e armazená-las numa nova coluna na tabela. 

No Power BI Desktop, na janela do Editor de Consultas, mude novamente para a consulta `FabrikamComments`. Selecione o friso **Adicionar Coluna**. No grupo **Geral**, clique em **Invocar Função Personalizada**.

![[Botão Invoke Custom Function]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

É apresentada a caixa de diálogo Invocar Função Personalizada. Em **Nome da nova coluna**, introduza `keyphrases`. Em **Consulta de função**, selecione a função personalizada que criou, `KeyPhrases`.

Um novo campo é apresentado na caixa de diálogo, **text (optional)** (texto [opcional]). Este campo está a pedir a coluna que queremos utilizar para indicar valores para o parâmetro `text` da API de Expressões-Chave. (Lembre-se de que já codificou os valores para os parâmetros `language` e `id`.) Selecione `Merged` (a coluna que criou [anteriormente](#PreparingData) ao unir os campos de assunto e de mensagem) no menu pendente.

![[Invocar uma função personalizada]](../media/tutorials/power-bi/invoke-custom-function.png)

Por fim, clique em **OK.**

Se tudo estiver pronto, o Power BI chamará a sua função personalizada uma vez para cada linha na tabela. Ele envia as consultas para a API de Expressões-Chave e adiciona uma nova coluna à tabela para armazenar os resultados. Mas antes que isso aconteça, poderá ter de especificar as definições de autenticação e privacidade.

## <a name="authentication-and-privacy"></a>Autenticação e privacidade
<a name="Authentication"></a>

Depois de fechar a caixa de diálogo Invocar Função Personalizada, poderá ser apresentada uma faixa a pedir-lhe para especificar como se ligar à API de Expressões-Chave.

![[faixa de credenciais]](../media/tutorials/power-bi/credentials-banner.png)

Clique em **Editar Credenciais**, garantindo que `Anonymous` está selecionado na caixa de diálogo e, em seguida, clique em **Ligar**. 

> [!NOTE]
> Selecione `Anonymous` dado que o serviço de Análise de Texto efetua a sua autenticação quando utilizar a sua chave de acesso, dessa forma, o Power BI não tem de proporcionar credenciais para o pedido de HTTP em si.

![[definir a autenticação como anónima]](../media/tutorials/power-bi/access-web-content.png)

Caso veja a faixa Editar Credenciais mesmo depois de escolher o acesso anónimo, pode ter-se esquecido de colar a chave de acesso da Análise de Texto no código da `KeyPhrases` [função personalizada](#CreateCustomFunction).

De seguida, poderá aparecer uma faixa que pede que indique informações sobre a privacidade das origens de dados. 

![[faixa de privacidade]](../media/tutorials/power-bi/privacy-banner.png)

Clique em **Continuar** e escolha `Public` para cada uma das origens de dados na caixa de diálogo. Em seguida, clique em **Save** (Guardar).

![[definir a privacidade da origem de dados]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="create-the-word-cloud"></a>Criar a nuvem de palavras
<a name="WordCloud"></a>

Depois de resolver as várias faixas que apareceram, clique em **Close & Apply** (Fechar e Aplicar), no friso Base, para fechar o Query Editor (Editor de Consultas).

O Power BI Desktop demora alguns momentos a fazer os pedidos HTTP necessários. Em cada linha na tabela, a coluna `keyphrases` nova contém as expressões-chave que a API Key Phrases detetou no texto. 

Agora, irá utilizar esta coluna para gerar uma nuvem de palavras. Para começar a utilizar, clique no botão **Relatório** na janela principal do Power BI Desktop, à esquerda da área de trabalho.

> [!NOTE]
> Porquê utilizar expressões-chave extraídas para gerar um balão de palavras em vez do texto completo de todos os comentários? As expressões-chave dão-nos as palavras *importantes* dos comentários dos clientes, não apenas as palavras *mais comuns*. Além disso, o dimensionamento das palavras no balão resultante não é distorcido pela utilização frequente de uma determinada palavra num número relativamente pequeno de comentários.

Se ainda não tiver o elemento visual Word Cloud instado, instale-o. No painel Visualizations, no lado direito da área de trabalho, clique nas reticências (**...**) e escolha **Import From Store** (Importar do Arquivo). Em seguida, procure “cloud” e clique no botão **Add** (Adicionar), junto ao elemento visual Word Cloud. O Power BI instala o elemento visual Word Cloud (Nuvem de Palavras) e indica-lhe que foi instalado com êxito.

![[adicionar um elemento visual]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

Primeiro, clique no ícone Word Cloud, no painel Visualizations (Visualizações).

![[Ícone do Word Cloud no painel visualizations]](../media/tutorials/power-bi/visualizations-panel.png)

É apresentado um relatório novo na área de trabalho. Arraste o campo `keyphrases` do painel Fields (Campos) para o campo Category (Categoria), no painel Visualizations (Visualizações). O balão de palavras aparece dentro do relatório.

Agora, mude para a página Formato do painel Visualizations (Visualizações). Na categoria Stop Words (Parar Palavras), ative **Default Stop Words** (Palavras de Paragem Predefinidas) para eliminar palavras pequenas e comuns, como “de”, no balão. 

![[ativar palavras de paragem predefinidas]](../media/tutorials/power-bi/default-stop-words.png)

Um pouco mais abaixo no painel, desative **Rotate Text** (Rodar Texto) e **Title** (Título).

![[ativar o modo de detalhe]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

Clique na ferramenta Modo de Detalhe para ver melhor o balão de palavras. A ferramenta expande o balão de palavras de modo a que preencha toda a área de trabalho, conforme mostrado abaixo.

![[Balão de Palavras]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>Mais serviços da Análise de Texto
<a name="MoreServices"></a>

O serviço Análise de Texto, um dos Serviços Cognitivos que o Microsoft Azure oferece, também disponibiliza análise de sentimentos e deteção de idioma. A deteção de idioma em particular é útil se os comentários do seu cliente não estão todos em inglês.

Estas duas outras APIs são semelhantes à API de Expressões-Chave. Isto significa que pode integrá-las com o Power BI Desktop através de funções personalizadas que são quase idênticas às que criou neste tutorial. Basta criar uma consulta em branco e colar o código adequado abaixo, no Advanced Editor (Editor Avançado), como fez anteriormente. (Não se esqueça da sua chave de acesso!) Depois, tal como antes, utilize a função para adicionar uma coluna nova à tabela.

A função Sentiment Analysis, abaixo, devolve uma classificação que indica até que ponto é positivo o sentimento expresso no texto.

```fsharp
// Returns the sentiment score of the text, from 0.0 (least favorable) to 1.0 (most favorable)
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    sentiment   = jsonresp[documents]{0}[score]
in  sentiment
```

Eis duas versões de uma função Language Detection. O primeiro método retorna o código ISO do idioma (por exemplo, `en` para inglês), enquanto o segundo devolve o nome "amigável" (por exemplo, `English`). Poderá reparar que apenas a última linha do corpo difere entre ambas as versões.

```fsharp
// Returns the two-letter language code (for example, 'en' for English) of the text
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[iso6391Name]
in  language
```
```fsharp
// Returns the name (for example, 'English') of the language in which the text is written
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[name]
in  language
```

Por fim, segue-se uma variação da função Key Phrases já apresentada que devolve as expressões como um objeto de lista em vez de uma cadeia única de expressões separadas por vírgulas. 

> [!NOTE]
> A devolução de uma única cadeia simplificou o nosso exemplo de balão de palavras. As listas, por sua vez, são um formato mais flexível para trabalhar com as expressões devolvidas no Power BI. Pode utilizar o grupo Structured Column (Coluna Estruturada), no frio Transform (Transformar) do Query Editor (Editor de Consultas), para manipular os objetos de lista.

```fsharp
// Returns key phrases from the text as a list object
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = jsonresp[documents]{0}[keyPhrases]
in  keyphrases
```

## <a name="next-steps"></a>Passos Seguintes
<a name="NextSteps"></a>

Saiba mais sobre o serviço Análise de Texto, a linguagem de fórmula M no Power Query ou o Power BI.

> [!div class="nextstepaction"]
> [Text Analytics API reference](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) (Referência à API de Análise de Texto)

> [!div class="nextstepaction"]
> [Power Query M reference](//msdn.microsoft.com/library/mt211003.aspx) (Referência à linguagem M do Power Query)

> [!div class="nextstepaction"]
> [Documentação do Power BI](//powerbi.microsoft.com/documentation/powerbi-landing-page/)
