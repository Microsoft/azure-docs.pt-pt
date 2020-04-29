---
title: Boas práticas - QnA Maker
description: Utilize estas boas práticas para melhorar a sua base de conhecimentos e fornecer melhores resultados aos utilizadores finais da sua aplicação/chat bot.
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 9a6f7f7d6edc4544942476050a1ed3c2011af7fb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80053122"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Boas práticas de uma base de conhecimento qnA Maker

O ciclo de vida de desenvolvimento da base de [conhecimento](../Concepts/development-lifecycle-knowledge-base.md) guia-o sobre como gerir o seu KB do início ao fim. Utilize estas boas práticas para melhorar a sua base de conhecimento e fornecer melhores resultados à aplicação do seu cliente ou aos utilizadores finais do chat bot.

## <a name="extraction"></a>Extração

O serviço QnA Maker está continuamente a melhorar os algoritmos que extraem QnAs do conteúdo e expande a lista de formatos de ficheiros suportados e HTML. Siga as [diretrizes](../Concepts/content-types.md) para a extração de dados com base no seu tipo de documento.

Em geral, as páginas de FAQ devem ser autónomas e não combinadas com outras informações. Os manuais do produto devem ter rubricas claras e, de preferência, uma página de índice.

### <a name="configuring-multi-turn"></a>Configurar multi-turn

[Crie a sua base](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) de conhecimento com extração multi-volta ativada. Se a sua base de conhecimento fizer ou deve apoiar a hierarquia da questão, esta hierarquia pode ser extraída do documento ou criada após a extração do documento.

## <a name="creating-good-questions-and-answers"></a>Criar boas perguntas e respostas

### <a name="good-questions"></a>Boas perguntas

As melhores perguntas são simples. Considere a palavra-chave ou frase para cada pergunta e, em seguida, crie uma pergunta simples para essa palavra-chave ou frase.

Adicione as perguntas alternativas que precisar, mas mantenha as alterações simples. Adicionar mais palavras ou frases que não fazem parte do objetivo principal da pergunta não ajuda o Fabricante qnA a encontrar uma correspondência.


### <a name="add-relevant-alternative-questions"></a>Adicionar questões alternativas relevantes

O utilizador pode introduzir perguntas com um `How do I add a toner cartridge to my printer?` estilo de texto conversacional ou uma pesquisa de palavras-chave como `toner cartridge`. A base de conhecimentos deve ter ambos os estilos de perguntas para devolver corretamente a melhor resposta. Se não tiver a certeza de que palavras-chave um cliente está a introduzir, utilize os dados do Application Insights para analisar consultas.

### <a name="good-answers"></a>Boas respostas

As melhores respostas são respostas simples, mas não muito simples. Não utilize respostas `yes` `no`como e . Se a sua resposta deve ligar-se a outras fontes ou fornecer uma experiência rica com meios e links, use [a marcação](../how-to/edit-knowledge-base.md#add-metadata) de metadados para distinguir entre respostas e, em seguida, [submeta a consulta](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) com etiquetas de metadados na `strictFilters` propriedade para obter a versão correta de resposta.

|Resposta|Solicitações de follup-up|
|--|--|
|Desligue o portátil Surface com o botão de alimentação no teclado.|* Combinações-chave para dormir, desligar e reiniciar.<br>* Como arrancar um portátil Surface<br>* Como mudar o BIOS para um portátil Surface<br>* Diferenças entre o sono, desligar e reiniciar|
|O atendimento ao cliente está disponível via telefone, Skype e mensagem de texto 24 horas por dia.|* Informações de contacto para vendas.<br> * Locais de escritório e loja e horas para uma visita presencial.<br> * Acessórios para um portátil Surface.|

## <a name="chit-chat"></a>Chit-Chat
Adicione chit-chat ao seu bot, para tornar o seu bot mais conversador e envolvente, com baixo esforço. Pode facilmente adicionar conjuntos de dados de chit-chat a partir de personalidades pré-definidas ao criar o seu KB, e alterá-los a qualquer momento. Aprenda a [adicionar chit-chat ao seu KB](../How-To/chit-chat-knowledge-base.md).

O chit-chat é suportado em [muitas línguas.](../how-to/chit-chat-knowledge-base.md#language-support)

### <a name="choosing-a-personality"></a>Escolher uma personalidade
O Chit-chat é suportado para várias personalidades predefinidas:

|Personalidade |Ficheiro de dataset do fabricante qna |
|---------|-----|
|Profissional |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Amigável |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Espirituoso |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Cuidar |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Entusiasta |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

As respostas vão de formais a informais e irreverentes. Deve selecionar a personalidade mais próxima alinhada com o tom que deseja para o seu bot. Pode ver os [conjuntos](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)de dados e escolher um que sirva de base para o seu bot e, em seguida, personalizar as respostas.

### <a name="edit-bot-specific-questions"></a>Editar perguntas específicas para bot
Existem algumas perguntas específicas para bots que fazem parte do conjunto de dados do chit-chat, e foram preenchidas com respostas genéricas. Altere estas respostas para melhor refletir os detalhes do seu bot.

Recomendamos tornar os seguintes QnAs de chit-chat mais específicos:

* Quem és?
* O que é que se pode fazer?
* Que idade tens?
* Quem te criou?
* Hello

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Adicionar chit-chat personalizado com uma etiqueta de metadados

Se adicionar os seus pares QnA chit-chat, certifique-se de adicionar metadados para que estas respostas sejam devolvidas. O nome/par de `editorial:chitchat`valores dos metadados é .

## <a name="searching-for-answers"></a>À procura de respostas

A GenerateAnswer API utiliza ambas as perguntas e a resposta para procurar as melhores respostas para a consulta de um utilizador.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Pesquisar perguntas apenas quando a resposta não é relevante

Use [`RankerType=QuestionOnly`](#choosing-ranker-type) o se não quiser pesquisar respostas.

Um exemplo disso é quando a base de conhecimento é um catálogo de siglas como perguntas com a sua forma completa como resposta. O valor da resposta não ajudará a procurar a resposta adequada.

## <a name="rankingscoring"></a>Ranking/Pontuação
Certifique-se de que está a fazer o melhor uso do ranking que o QnA Maker suporta. Ao fazê-lo, melhorará a probabilidade de uma determinada consulta de utilizador ser respondida com uma resposta adequada.

### <a name="choosing-a-threshold"></a>Escolher um limiar

A [pontuação de confiança](confidence-score.md) padrão que é usada como um limiar é 0, no entanto pode [alterar o limiar](confidence-score.md#set-threshold) para o seu KB com base nas suas necessidades. Uma vez que cada KB é diferente, você deve testar e escolher o limiar que é mais adequado para o seu KB.

### <a name="choosing-ranker-type"></a>Escolher o tipo Ranker
Por padrão, o QnA Maker procura através de perguntas e respostas. Se quiser pesquisar apenas através de perguntas, `RankerType=QuestionOnly` para gerar uma resposta, utilize o corpo post do pedido GenerateAnswer.

### <a name="add-alternate-questions"></a>Adicionar perguntas alternativas
[Perguntas alternativas](../How-To/edit-knowledge-base.md) melhoram a probabilidade de um jogo com uma consulta do utilizador. Perguntas alternativas são úteis quando há várias formas de fazer a mesma pergunta. Isto pode incluir alterações na estrutura da frase e no estilo de palavra.

|Consulta original|Consultas alternativas|Alterar|
|--|--|--|
|O estacionamento está disponível?|Tem estacionamento?|estrutura da frase|
 |Olá|Yo<br>Olá, olá!|estilo palavra ou gíria|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Use etiquetas de metadados para filtrar perguntas e respostas

[Os metadados](../How-To/edit-knowledge-base.md) adicionam a capacidade de uma aplicação do cliente saber que não deve aceitar todas as respostas, mas sim reduzir os resultados de uma consulta de utilizador com base em etiquetas de metadados. A resposta da base de conhecimento pode diferir com base na etiqueta de metadados, mesmo que a consulta seja a mesma. Por exemplo, *"onde está localizado o estacionamento"* pode ter uma resposta diferente se a localização do ramo do restaurante for diferente - isto é, os metadados são *Localização: Seattle* versus *Localização: Redmond*.

### <a name="use-synonyms"></a>Use sinónimos
Embora exista algum apoio para sinónimos na língua inglesa, use alterações de palavras insensíveis através da [API alterações](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) para adicionar sinónimos a palavras-chave que tomam formadiferente. Os sinónimos são adicionados ao nível de serviço qnA Maker e partilhados por todas as bases de conhecimento do serviço.

|Palavra original|Sinónimos|
|--|--|
|comprar|purchase<br>net-banking<br>banca líquida|

### <a name="use-distinct-words-to-differentiate-questions"></a>Use palavras distintas para diferenciar questões
O algoritmo de classificação do QnA Maker, que combina com uma consulta do utilizador com uma pergunta na base de conhecimentos, funciona melhor se cada pergunta abordar uma necessidade diferente. A repetição da mesma palavra definida entre perguntas reduz a probabilidade de a resposta certa ser escolhida para uma determinada consulta do utilizador com essas palavras.

Por exemplo, pode ter dois QnAs separados com as seguintes perguntas:

|QnAs|
|--|
|onde é o *local* de estacionamento|
|onde está a *localização* do multibanco|

Uma vez que estes dois QnAs são formulados com palavras muito semelhantes, esta semelhança pode causar pontuações muito semelhantes para muitas consultas de utilizadores que são ditas como *"onde é a `<x>` localização".* Em vez disso, tente diferenciar claramente com consultas como *"onde está o estacionamento"* e *"onde está o multibanco",* evitando palavras como "localização" que poderia estar em muitas questões no seu KB.

## <a name="collaborate"></a>Colaborar
O QnA Maker permite que os utilizadores [colaborem](../How-to/collaborate-knowledge-base.md) numa base de conhecimento. Os utilizadores precisam de acesso ao grupo de recursos Azure QnA Maker para aceder às bases de conhecimento. Algumas organizações podem querer subcontratar a base de conhecimento de edição e manutenção, e ainda assim ser capaz de proteger o acesso aos seus recursos Azure. Este modelo de aprovação de editores é feito através da criação de dois [serviços idênticos da QnA Maker](../How-to/set-up-qnamaker-service-azure.md) em diferentes subscrições e selecionando um para o ciclo de testes de edição. Uma vez terminados os testes, os conteúdos da base de conhecimentos são transferidos com um processo [de importação-exportação](../Tutorials/migrate-knowledge-base.md) para o serviço QnA Maker do aprovador que irá finalmente publicar a base de conhecimentos e atualizar o ponto final.



## <a name="active-learning"></a>Aprendizagem ativa

[A aprendizagem ativa](../How-to/use-active-learning.md) faz o melhor trabalho de sugerir questões alternativas quando tem uma vasta gama de consultas baseadas no utilizador. É importante permitir que as consultas de utilizador de aplicações de clientes participem no ciclo de feedback de aprendizagem ativa sem censura. Uma vez sugeridas perguntas no portal QnA Maker, pode **[filtrar por sugestões](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** e depois rever e aceitar ou rejeitar essas sugestões.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-to/edit-knowledge-base.md)
