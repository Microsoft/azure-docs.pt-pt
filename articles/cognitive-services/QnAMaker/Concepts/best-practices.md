---
title: Melhores práticas - QnA Maker
titleSuffix: Azure Cognitive Services
description: Utilize estas melhores práticas para melhorar a sua base de dados de conhecimento e fornecer os melhores resultados para os utilizadores finais do seu bot de aplicação/bate-papo.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: da587aa51b1a8aee609a5bfd04ce0d7f24ca0e5f
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955263"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Melhores práticas de uma base de dados de conhecimento do QnA Maker

O [ciclo de vida de desenvolvimento de base de dados de conhecimento](../Concepts/development-lifecycle-knowledge-base.md) orienta-o sobre como gerir a sua BDC do início ao fim. Use essas práticas recomendadas para melhorar sua base de dados de conhecimento e fornecer resultados melhores para o aplicativo cliente ou para os usuários finais do bate-papo.

## <a name="extraction"></a>Extração

O serviço QnA Maker está continuamente a melhorar os algoritmos que extrair QnAs de conteúdo e expandir a lista de ficheiro suportados e formatos HTML. Siga os [diretrizes](../Concepts/data-sources-supported.md) para extração de dados com base no seu tipo de documento. 

Em geral, as páginas de FAQ devem ser autónomo e não combinada com outras informações. Os manuais de produto devem ter títulos claros e preferencialmente, uma página de índice. 

### <a name="configuring-multi-turn"></a>Configurando o multi-Transform

Crie sua base de dados de conhecimento com a extração múltipla habilitada. Se sua base de dados de conhecimento faz ou deve oferecer suporte à hierarquia de perguntas, essa hierarquia pode ser extraída do documento ou criada depois que o documento é extraído. 

<!--is this a global setting that can only be configured at kb creation time? -->

## <a name="creating-good-questions-and-answers"></a>Criando boas perguntas e respostas

### <a name="good-questions"></a>Boas perguntas

As melhores perguntas são simples. Considere a palavra-chave ou frase para cada pergunta e, em seguida, crie uma pergunta simples para essa palavra-chave ou frase. 

Adicione quantas perguntas alternativas forem necessárias, mas mantenha as alterações simples. Adicionar mais palavras ou frases que não fazem parte do objetivo principal da pergunta não ajuda QnA Maker encontrar uma correspondência. 


### <a name="add-relevant-alternative-questions"></a>Adicionar perguntas alternativas relevantes

O usuário pode inserir perguntas com um estilo de conversa de texto `How do I add a toner cartridge to my printer?` ou uma pesquisa de palavra-chave, como. `toner cartridge` A base de dados de conhecimento deve ter os dois estilos de perguntas para retornar corretamente a melhor resposta. Se você não tiver certeza de quais palavras-chave um cliente está inserindo, use Application Insights dados para analisar consultas.

### <a name="good-answers"></a>Boas respostas

As melhores respostas são respostas simples, mas não muito simples. Não use respostas `yes` como e `no`. Se sua resposta deve ser vinculada a outras fontes ou fornecer uma experiência rica com mídia e links, use a [marcação de metadados](./knowledge-base.md#key-knowledge-base-concepts) para distinguir entre as respostas e, em seguida, [envie a consulta com as](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) `strictFilters` marcas de metadados na propriedade para obter a resposta correta Versão.

## <a name="chit-chat"></a>Chit-bate-papo
Adicionar chit-bate-papo ao seu bot, para tornar o seu bot mais conversacionais e apelativas, com esforço reduzido. Você pode adicionar facilmente conjuntos de dados do Chit-Chat de personalidades predefinidos ao criar seu KB e alterá-los a qualquer momento. Saiba como [adicionar chit-bate-papo para sua BDC](../How-To/chit-chat-knowledge-base.md). 

### <a name="choosing-a-personality"></a>Escolher uma personalidade
Chit-o chat tem suporte para vários personalidades predefinidos: 

|Personalidade |Arquivo do conjunto de QnA Maker |
|---------|-----|
|Profissional |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|AmigáveI |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Voluntariay |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Preocupar |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Entusiasmado |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

O intervalo de respostas de formal a informal e irreverente. Deve selecionar a personalidade que mais se aproxima está alinhada com o tom que pretende para o bot. Você pode exibir os [conjuntos](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)de dados e escolher um que sirva como base para o bot e, em seguida, personalizar as respostas. 

### <a name="edit-bot-specific-questions"></a>Editar perguntas específicas de bot
Existem algumas questões específicas de bot que fazem parte do conjunto de dados chit-bate-papo e tem sido preenchidos com respostas genéricas. Altere essas respostas para melhor refletir os detalhes do seu bot. 

Recomendamos que o faça o seguinte QnAs chit-bate-papo mais específicas:

* Quem é?
* O que fazer?
* Que idade tem?
* Quem criou?
* Olá
   
### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Adicionando Chit personalizado-chat com uma marca de metadados

Se você adicionar seus próprios pares de QnA Chit-Chat, certifique-se de adicionar metadados para que essas respostas sejam retornadas. O par nome/valor de metadados `editorial:chitchat`é.

## <a name="searching-for-answers"></a>Procurando respostas

A API do GenerateAnswer usa as duas perguntas e a resposta para pesquisar as melhores respostas para a consulta de um usuário.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Perguntas de pesquisa somente quando a resposta não é relevante

Use o [`RankerType=QuestionOnly`](#choosing-ranker-type) se você não quiser pesquisar respostas. 

Um exemplo disso é quando a base de dados de conhecimento é um catálogo de acrônimos como perguntas com sua forma completa como resposta. O valor da resposta não ajudará a procurar a resposta apropriada.

## <a name="rankingscoring"></a>Classificação de classificação /
Certifique-se de que está fazendo o melhor uso dos recursos de classificação que do QnA Maker suporta. Se o fizer, irá melhorar a probabilidade de que consulta um determinado usuário foi respondida com uma resposta adequada.

### <a name="choosing-a-threshold"></a>Escolher um limiar

A [Pontuação de confiança](confidence-score.md) padrão usada como um limite é de 50, no entanto, você pode [alterar o limite](confidence-score.md#set-threshold) para a KB com base em suas necessidades. Uma vez que cada KB é diferente, deve testar e escolha o limiar de que é melhor adequado para seu KB. 

### <a name="choosing-ranker-type"></a>Escolhendo o tipo de classificação
Por padrão, QnA Maker pesquisa perguntas e respostas. Se você quiser pesquisar apenas as perguntas, para gerar uma resposta, use o `RankerType=QuestionOnly` no corpo da postagem da solicitação GenerateAnswer.

### <a name="add-alternate-questions"></a>Adicionar perguntas alternativas
[Alternate perguntas](../How-To/edit-knowledge-base.md) melhorar a probabilidade de uma correspondência com uma consulta de utilizador. Perguntas alternativas são úteis quando existem múltiplas formas em que pode ser solicitada a mesma pergunta. Isto pode incluir alterações na estrutura da sentença e estilo do word.

|Consulta original|Consultas alternativas|Alterar| 
|--|--|--|
|É de estacionamento disponíveis?|Tem carro park?|estrutura de sentença|
 |Olá!|Yo<br>Olha aí!|Estilo do Word ou gíria|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Usar marcas de metadados para filtrar perguntas e respostas

Os [metadados](../How-To/edit-knowledge-base.md) adicionam a capacidade de um aplicativo cliente saber que ele não deve receber todas as respostas, mas sim restringir os resultados de uma consulta de usuário com base nas marcas de metadados. A resposta da base de dados de conhecimento pode ser diferente consoante a marca de metadados, mesmo que a consulta é o mesmo. Por exemplo, *"onde o estacionamento está localizado"* pode ter uma resposta diferente se o local da ramificação do restaurante for diferente, ou seja, os *metadados forem locais: Seattle* versus*local: Redmond*.

### <a name="use-synonyms"></a>Utilizar sinónimos
Embora haja suporte para sinônimos no idioma inglês, use as alterações de palavras que não diferenciam maiúsculas de minúsculas por meio da [API de alterações](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) para adicionar sinônimos a palavras-chave que usam um formulário diferente. Os sinônimos são adicionados no nível de serviço QnA Maker e compartilhados por todas as bases de conhecimento no serviço.

|Original word|Sinónimos|
|--|--|
|Comprar|comprar<br>NET-Banking<br>banca NET|

### <a name="use-distinct-words-to-differentiate-questions"></a>Utilizar palavras diferentes para diferenciar perguntas
O algoritmo de classificação de QnA Maker, que corresponde a uma consulta de usuário com uma pergunta na base de dados de conhecimento, funciona melhor se cada pergunta atender a uma necessidade diferente. Repetição da mesma palavra definido entre perguntas reduz a probabilidade de que a resposta certa é escolhida para uma consulta de determinado usuário com essas palavras. 

Por exemplo, pode ter dois QnAs separados com as seguintes perguntas:

|QnAs|
|--|
|onde está o estacionamento *localização*|
|Onde está o *local* do ATM|

Uma vez que estes dois QnAs são dizer com palavras muito semelhantes, essa semelhança poderia causar pontuações muito semelhantes para muitas consultas de utilizador que estão a dizer, como *"em que é o `<x>` localização"* . Em vez disso, tente diferenciar claramente as consultas como *"onde está o estacionamento"* e *"onde está o ATM"* , evitando palavras como "local" que possam estar em muitas perguntas na sua base de conhecimento. 

## <a name="collaborate"></a>Colaboração
A ferramenta QnA Maker permite que os usuários [colaborar](../How-to/collaborate-knowledge-base.md) numa base de dados de conhecimento. Os utilizadores precisam de acesso para o grupo de recursos do Azure QnA Maker para acessar as bases de dados de conhecimento. Algumas organizações poderão querer terceirizar a edição de base de dados de conhecimento e a manutenção e continuará a poder proteger o acesso aos seus recursos do Azure. Esse modelo de aprovador de editor é feito ao configurar dois idêntico [serviços do QnA Maker](../How-to/set-up-qnamaker-service-azure.md) em diferentes subscrições e selecionando um para o ciclo de teste de edição. Assim que o teste estiver concluído, o conteúdo de base de dados é transferido com um [importação-exportação](../Tutorials/migrate-knowledge-base.md) processos para o serviço QnA Maker do aprovador que finalmente publicar a base de dados de conhecimento e atualizar o ponto final.



## <a name="active-learning"></a>Aprendizagem ativa

O [aprendizado ativo](../How-to/improve-knowledge-base.md) faz o melhor trabalho de sugestão de perguntas alternativas quando tem uma ampla gama de qualidade e quantidade de consultas baseadas no usuário. É importante permitir que as consultas de usuário dos aplicativos cliente participem do loop de comentários do aprendizado ativo sem censura. Quando as perguntas são sugeridas no portal de QnA Maker, você pode **[Filtrar por sugestões](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** e, em seguida, revisar e aceitar ou rejeitar essas sugestões. 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-to/edit-knowledge-base.md)
