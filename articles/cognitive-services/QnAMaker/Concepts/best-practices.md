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
ms.date: 01/27/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2fd85e43fb2aa53299b4e37eca5163b7da8fc6ec
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843808"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Melhores práticas de uma base de dados de conhecimento do QnA Maker

O [ciclo de vida de desenvolvimento de base de dados de conhecimento](../Concepts/development-lifecycle-knowledge-base.md) orienta-o sobre como gerir a sua BDC do início ao fim. Utilize estas boas práticas para melhorar a sua base de conhecimento e fornecer melhores resultados à aplicação do seu cliente ou aos utilizadores finais do chat bot.

## <a name="extraction"></a>Extração

O serviço QnA Maker está continuamente a melhorar os algoritmos que extrair QnAs de conteúdo e expandir a lista de ficheiro suportados e formatos HTML. Siga os [diretrizes](../Concepts/content-types.md) para extração de dados com base no seu tipo de documento.

Em geral, as páginas de FAQ devem ser autónomo e não combinada com outras informações. Os manuais de produto devem ter títulos claros e preferencialmente, uma página de índice.

### <a name="configuring-multi-turn"></a>Configurar multi-turn

[Crie a sua base](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) de conhecimento com extração multi-volta ativada. Se a sua base de conhecimento fizer ou deve apoiar a hierarquia da questão, esta hierarquia pode ser extraída do documento ou criada após a extração do documento.

<!--is this a global setting that can only be configured at kb creation time? -->

## <a name="creating-good-questions-and-answers"></a>Criar boas perguntas e respostas

### <a name="good-questions"></a>Boas perguntas

As melhores perguntas são simples. Considere a palavra-chave ou frase para cada pergunta e, em seguida, crie uma pergunta simples para essa palavra-chave ou frase.

Adicione as perguntas alternativas que precisar, mas mantenha as alterações simples. Adicionar mais palavras ou frases que não fazem parte do objetivo principal da pergunta não ajuda o Fabricante qnA a encontrar uma correspondência.


### <a name="add-relevant-alternative-questions"></a>Adicionar questões alternativas relevantes

O utilizador pode introduzir perguntas com um estilo de texto, `How do I add a toner cartridge to my printer?` ou uma pesquisa de palavras-chave como `toner cartridge`. A base de conhecimentos deve ter ambos os estilos de perguntas para devolver corretamente a melhor resposta. Se não tiver a certeza de que palavras-chave um cliente está a introduzir, utilize os dados do Application Insights para analisar consultas.

### <a name="good-answers"></a>Boas respostas

As melhores respostas são respostas simples, mas não muito simples. Não utilize respostas como `yes` e `no`. Se a sua resposta deve ligar-se a outras fontes ou fornecer uma experiência rica com meios e links, use [a marcação](../how-to/edit-knowledge-base.md#add-metadata) de metadados para distinguir entre respostas e, em seguida, [submeta a consulta](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) com etiquetas de metadados na propriedade `strictFilters` para obter a versão correta de resposta.

|Resposta|Solicitações de follup-up|
|--|--|
|Desligue o portátil Surface com o botão de alimentação no teclado.|* Combinações-chave para dormir, desligar e reiniciar.<br>* Como arrancar um portátil Surface<br>* Como mudar o BIOS para um portátil Surface<br>* Diferenças entre o sono, desligar e reiniciar|
|O atendimento ao cliente está disponível via telefone, Skype e mensagem de texto 24 horas por dia.|* Informações de contacto para vendas.<br> * Locais de escritório e loja e horas para uma visita presencial.<br> * Acessórios para um portátil Surface.|

## <a name="chit-chat"></a>Chit-bate-papo
Adicionar chit-bate-papo ao seu bot, para tornar o seu bot mais conversacionais e apelativas, com esforço reduzido. Pode facilmente adicionar conjuntos de dados de chit-chat a partir de personalidades pré-definidas ao criar o seu KB, e alterá-los a qualquer momento. Saiba como [adicionar chit-bate-papo para sua BDC](../How-To/chit-chat-knowledge-base.md).

O chit-chat é suportado em [muitas línguas.](../how-to/chit-chat-knowledge-base.md#language-support)

### <a name="choosing-a-personality"></a>Escolher uma personalidade
O Chit-chat é suportado para várias personalidades predefinidas:

|Personalidade |Ficheiro de dataset do fabricante qna |
|---------|-----|
|Profissional |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|AmigáveI |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Espirituoso |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Cuidar |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Entusiasta |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

O intervalo de respostas de formal a informal e irreverente. Deve selecionar a personalidade que mais se aproxima está alinhada com o tom que pretende para o bot. Pode ver os [conjuntos](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)de dados e escolher um que sirva de base para o seu bot e, em seguida, personalizar as respostas.

### <a name="edit-bot-specific-questions"></a>Editar perguntas específicas de bot
Existem algumas questões específicas de bot que fazem parte do conjunto de dados chit-bate-papo e tem sido preenchidos com respostas genéricas. Altere essas respostas para melhor refletir os detalhes do seu bot.

Recomendamos que o faça o seguinte QnAs chit-bate-papo mais específicas:

* Quem é?
* O que fazer?
* Que idade tem?
* Quem criou?
* Olá

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Adicionar chit-chat personalizado com uma etiqueta de metadados

Se adicionar os seus pares QnA chit-chat, certifique-se de adicionar metadados para que estas respostas sejam devolvidas. O nome/par de valores dos metadados é `editorial:chitchat`.

## <a name="searching-for-answers"></a>À procura de respostas

A GenerateAnswer API utiliza ambas as perguntas e a resposta para procurar as melhores respostas para a consulta de um utilizador.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Pesquisar perguntas apenas quando a resposta não é relevante

Use o [`RankerType=QuestionOnly`](#choosing-ranker-type) se não quiser pesquisar respostas.

Um exemplo disso é quando a base de conhecimento é um catálogo de siglas como perguntas com a sua forma completa como resposta. O valor da resposta não ajudará a procurar a resposta adequada.

## <a name="rankingscoring"></a>Classificação de classificação /
Certifique-se de que está fazendo o melhor uso dos recursos de classificação que do QnA Maker suporta. Se o fizer, irá melhorar a probabilidade de que consulta um determinado usuário foi respondida com uma resposta adequada.

### <a name="choosing-a-threshold"></a>Escolher um limiar

A [pontuação de confiança](confidence-score.md) padrão que é usada como um limiar é de 50, no entanto pode [alterar o limiar](confidence-score.md#set-threshold) para o seu KB com base nas suas necessidades. Uma vez que cada KB é diferente, deve testar e escolha o limiar de que é melhor adequado para seu KB.

### <a name="choosing-ranker-type"></a>Escolher o tipo Ranker
Por padrão, QnA Maker pesquisa perguntas e respostas. Se você quiser pesquisar apenas as perguntas, para gerar uma resposta, use o `RankerType=QuestionOnly` no corpo da POSTAgem da solicitação GenerateAnswer.

### <a name="add-alternate-questions"></a>Adicionar perguntas alternativas
[Alternate perguntas](../How-To/edit-knowledge-base.md) melhorar a probabilidade de uma correspondência com uma consulta de utilizador. Perguntas alternativas são úteis quando existem múltiplas formas em que pode ser solicitada a mesma pergunta. Isto pode incluir alterações na estrutura da sentença e estilo do word.

|Consulta original|Consultas alternativas|Alterar|
|--|--|--|
|É de estacionamento disponíveis?|Tem carro park?|estrutura de sentença|
 |Olá!|Yo<br>Olha aí!|Estilo do Word ou gíria|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Use etiquetas de metadados para filtrar perguntas e respostas

[Os metadados](../How-To/edit-knowledge-base.md) adicionam a capacidade de uma aplicação do cliente saber que não deve aceitar todas as respostas, mas sim reduzir os resultados de uma consulta de utilizador com base em etiquetas de metadados. A resposta da base de dados de conhecimento pode ser diferente consoante a marca de metadados, mesmo que a consulta é o mesmo. Por exemplo, *"onde está localizado de estacionamento"* pode ter uma resposta diferente se a localização do ramo restaurante for diferente, ou seja, os metadados são *localização: Seattle* versus *localização: Redmond*.

### <a name="use-synonyms"></a>Utilizar sinónimos
Embora exista algum apoio para sinónimos na língua inglesa, use alterações de palavras insensíveis através da [API alterações](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) para adicionar sinónimos a palavras-chave que tomam formadiferente. Os sinónimos são adicionados ao nível de serviço qnA Maker e partilhados por todas as bases de conhecimento do serviço.

|Original word|Sinónimos|
|--|--|
|Comprar|comprar<br>net-banking<br>banca NET|

### <a name="use-distinct-words-to-differentiate-questions"></a>Utilizar palavras diferentes para diferenciar perguntas
O algoritmo de classificação do QnA Maker, que combina com uma consulta do utilizador com uma pergunta na base de conhecimentos, funciona melhor se cada pergunta abordar uma necessidade diferente. Repetição da mesma palavra definido entre perguntas reduz a probabilidade de que a resposta certa é escolhida para uma consulta de determinado usuário com essas palavras.

Por exemplo, pode ter dois QnAs separados com as seguintes perguntas:

|QnAs|
|--|
|onde está o estacionamento *localização*|
|onde está a *localização* do multibanco|

Uma vez que estes dois QnAs são dizer com palavras muito semelhantes, essa semelhança poderia causar pontuações muito semelhantes para muitas consultas de utilizador que estão a dizer, como *"em que é o `<x>` localização"* . Em vez disso, tente diferenciar claramente com consultas como *"onde está o estacionamento"* e *"onde está o multibanco",* evitando palavras como "localização" que poderia estar em muitas questões no seu KB.

## <a name="collaborate"></a>Colaboração
A ferramenta QnA Maker permite que os usuários [colaborar](../How-to/collaborate-knowledge-base.md) numa base de dados de conhecimento. Os utilizadores precisam de acesso para o grupo de recursos do Azure QnA Maker para acessar as bases de dados de conhecimento. Algumas organizações poderão querer terceirizar a edição de base de dados de conhecimento e a manutenção e continuará a poder proteger o acesso aos seus recursos do Azure. Esse modelo de aprovador de editor é feito ao configurar dois idêntico [serviços do QnA Maker](../How-to/set-up-qnamaker-service-azure.md) em diferentes subscrições e selecionando um para o ciclo de teste de edição. Assim que o teste estiver concluído, o conteúdo de base de dados é transferido com um [importação-exportação](../Tutorials/migrate-knowledge-base.md) processos para o serviço QnA Maker do aprovador que finalmente publicar a base de dados de conhecimento e atualizar o ponto final.



## <a name="active-learning"></a>Aprendizagem ativa

[A aprendizagem ativa](../How-to/improve-knowledge-base.md) faz o melhor trabalho de sugerir questões alternativas quando tem uma vasta gama de consultas baseadas no utilizador. É importante permitir que as consultas de utilizador de aplicações de clientes participem no ciclo de feedback de aprendizagem ativa sem censura. Uma vez sugeridas perguntas no portal QnA Maker, pode **[filtrar por sugestões](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** e depois rever e aceitar ou rejeitar essas sugestões.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-to/edit-knowledge-base.md)
