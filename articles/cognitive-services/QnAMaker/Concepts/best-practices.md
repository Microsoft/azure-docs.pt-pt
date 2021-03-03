---
title: Melhores práticas - QnA Maker
description: Utilize estas melhores práticas para melhorar a sua base de conhecimentos e fornecer melhores resultados aos utilizadores finais da sua aplicação/chat Bot.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 90d526a58f4223fc70be5414ce5722238ce2c735
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733436"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Boas práticas de uma base de conhecimento da QnA Maker

O [ciclo de vida de desenvolvimento](../Concepts/development-lifecycle-knowledge-base.md) de base de conhecimento guia-o sobre como gerir o seu KB do início ao fim. Utilize estas melhores práticas para melhorar a sua base de conhecimentos e fornecer melhores resultados à aplicação do seu cliente ou aos utilizadores finais do Chat Bot.

## <a name="extraction"></a>Extração

O serviço QnA Maker está continuamente a melhorar os algoritmos que extraem QnAs do conteúdo e a expandir a lista de formatos de ficheiros e HTML suportados. Siga as [diretrizes](../Concepts/data-sources-and-content.md) para a extração de dados com base no seu tipo de documento.

Em geral, as páginas de FAQ devem ser autónomas e não combinadas com outras informações. Os manuais do produto devem ter posições claras e, de preferência, uma página de índice.

### <a name="configuring-multi-turn"></a>Configurar multi-volta

[Crie a sua base](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) de conhecimento com extração multi-volta ativada. Se a sua base de conhecimentos fizer ou apoiar a hierarquia de perguntas, esta hierarquia pode ser extraída do documento ou criada após a extração do documento.

## <a name="creating-good-questions-and-answers"></a>Criação de boas perguntas e respostas

### <a name="good-questions"></a>Boas perguntas

As melhores perguntas são simples. Considere a palavra-chave ou frase para cada pergunta e, em seguida, crie uma pergunta simples para essa palavra ou frase-chave.

Adicione as perguntas alternativas que precisar, mas mantenha as alterações simples. Adicionar mais palavras ou frases que não fazem parte do principal objetivo da pergunta não ajuda a QnA Maker a encontrar um fósforo.


### <a name="add-relevant-alternative-questions"></a>Adicionar questões alternativas relevantes

O seu utilizador pode introduzir perguntas com um estilo de texto de conversação ou com uma chamada de `How do I add a toner cartridge to my printer?` palavras-chave, como `toner cartridge` . A base de conhecimento deve ter ambos os estilos de perguntas para devolver corretamente a melhor resposta. Se não tiver a certeza de que palavras-chave um cliente está a introduzir, utilize os dados do Application Insights para analisar consultas.

### <a name="good-answers"></a>Boas respostas

As melhores respostas são respostas simples, mas não muito simples. Não utilize respostas como `yes` e `no` . . Se a sua resposta deve ligar-se a outras fontes ou fornecer uma experiência rica com meios e links, use [a marcação de metadados](../how-to/edit-knowledge-base.md#add-metadata) para distinguir entre respostas e, em seguida, [submeta a consulta](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) com etiquetas de metadados na propriedade para obter a versão de resposta `strictFilters` correta.

|Resposta|Pedidos de loucura|
|--|--|
|Desligue o portátil Surface com o botão de alimentação no teclado.|* Combinações de chaves para dormir, desligar e reiniciar.<br>* Como arrancar um portátil surface<br>* Como alterar o BIOS para um portátil Surface<br>* Diferenças entre dormir, desligar e reiniciar|
|O atendimento ao cliente está disponível via telefone, Skype e mensagem de texto 24 horas por dia.|* Informações de contacto para vendas.<br> * Locais de escritório e loja e horas para uma visita presencial.<br> * Acessórios para um portátil Surface.|

## <a name="chit-chat"></a>Chit-Chat
Adicione chit-chat ao seu bot, para tornar o seu bot mais conversador e envolvente, com baixo esforço. Pode facilmente adicionar conjuntos de dados de chit-chat a partir de personalidades pré-definidas ao criar o seu KB e alterá-los a qualquer momento. Saiba como [adicionar chit-chat ao seu KB](../How-To/chit-chat-knowledge-base.md).

Chit-chat é suportado em [muitas línguas.](../how-to/chit-chat-knowledge-base.md#language-support)

### <a name="choosing-a-personality"></a>Escolher uma personalidade
Chit-chat é apoiado para várias personalidades predefinidas:

|Personalidade |Ficheiro de dataset do fabricante QnA |
|---------|-----|
|Profissional |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Amigável |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Espirituoso |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Cuidar |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Entusiasta |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

As respostas vão do formal ao informal e irreverente. Deve selecionar a personalidade mais próxima alinhada com o tom que deseja para o seu bot. Pode ver os [conjuntos de dados](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)e escolher um que sirva de base para o seu bot e, em seguida, personalizar as respostas.

### <a name="edit-bot-specific-questions"></a>Editar questões específicas do bot
Existem algumas questões específicas do bot que fazem parte do conjunto de dados de chit-chat, e foram preenchidas com respostas genéricas. Altere estas respostas para que reflita melhor os seus detalhes do bot.

Recomendamos tornar os seguintes QnAs de chit-chat mais específicos:

* Qual é a sua função?
* O que é que se pode fazer?
* Que idade tens?
* Quem te criou?
* Hello

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Adicionar chit-chat personalizado com uma etiqueta de metadados

Se adicionar os seus próprios pares QnA de chit-chat, certifique-se de adicionar metadados para que estas respostas sejam devolvidas. O par de nome/valor dos metadados é `editorial:chitchat` .

## <a name="searching-for-answers"></a>À procura de respostas

A GenerateAnswer API utiliza ambas as perguntas e a resposta para procurar as melhores respostas para a consulta de um utilizador.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Procurar perguntas apenas quando a resposta não é relevante

Use o [`RankerType=QuestionOnly`](#choosing-ranker-type) se não quiser procurar respostas.

Um exemplo disso é quando a base de conhecimento é um catálogo de siglas como perguntas com a sua forma completa como resposta. O valor da resposta não ajudará a procurar a resposta adequada.

## <a name="rankingscoring"></a>Ranking/Pontuação
Certifique-se de que está a fazer o melhor uso das funcionalidades de classificação que o QnA Maker suporta. Ao fazê-lo, melhorará a probabilidade de uma determinada consulta do utilizador ser respondida com uma resposta adequada.

### <a name="choosing-a-threshold"></a>Escolher um limiar

A [pontuação](confidence-score.md) de confiança padrão que é usada como um limiar é 0, no entanto pode [alterar o limiar](confidence-score.md#set-threshold) para o seu KB com base nas suas necessidades. Uma vez que cada KB é diferente, você deve testar e escolher o limiar que é mais adequado para o seu KB.

### <a name="choosing-ranker-type"></a>Escolher o tipo Ranker
Por predefinição, o QnA Maker procura através de perguntas e respostas. Se quiser pesquisar apenas através de perguntas, para gerar uma resposta, utilize `RankerType=QuestionOnly` o corpo POST do pedido GenerateAnswer.

### <a name="add-alternate-questions"></a>Adicionar perguntas alternativas
[Perguntas alternativas](../How-To/edit-knowledge-base.md) melhoram a probabilidade de uma correspondência com uma consulta do utilizador. Perguntas alternativas são úteis quando há múltiplas formas de fazer a mesma pergunta. Isto pode incluir alterações na estrutura da frase e estilo palavra.

|Consulta original|Consultas alternativas|Alterar|
|--|--|--|
|O estacionamento está disponível?|Tem estacionamento?|estrutura de frase|
 |Olá|Yo<br>Olá, olá!|estilo palavra ou gíria|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Utilize etiquetas de metadados para filtrar perguntas e respostas

[Os metadados](../How-To/edit-knowledge-base.md) adicionam a capacidade de uma aplicação do cliente saber que não deve levar todas as respostas, mas sim reduzir os resultados de uma consulta do utilizador com base em etiquetas de metadados. A resposta base de conhecimento pode diferir com base na etiqueta de metadados, mesmo que a consulta seja a mesma. Por exemplo, *"onde está o estacionamento localizado"* pode ter uma resposta diferente se a localização da filial do restaurante for diferente - ou seja, os metadados são *Localização: Seattle* versus *Localização: Redmond*.

### <a name="use-synonyms"></a>Use sinónimos
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)
Embora exista algum suporte para sinónimos na língua inglesa, use alterações de palavras insensíveis através da [API alterações](/rest/api/cognitiveservices/qnamaker/alterations/replace) para adicionar sinónimos a palavras-chave que assumem diferentes formas. Os sinónimos são adicionados ao nível de serviço do QnA Maker e **partilhados por todas as bases de conhecimento do serviço.**

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)
Embora exista algum suporte para sinónimos na língua inglesa, use alterações de palavras insensíveis através da [API alterações](/rest/api/cognitiveservices/qnamaker/alterations/replace) para adicionar sinónimos a palavras-chave que assumem diferentes formas. Os sinónimos no QnA Maker geridos (Preview) são **adicionados por base de conhecimento**.

|Palavra original|Sinónimos|
|--|--|
|comprar|purchase<br>banca líquida<br>banca líquida|

---

### <a name="use-distinct-words-to-differentiate-questions"></a>Use palavras distintas para diferenciar questões
O algoritmo de classificação do QnA Maker, que corresponde a uma consulta do utilizador com uma pergunta na base de conhecimento, funciona melhor se cada pergunta abordar uma necessidade diferente. A repetição da mesma palavra entre perguntas reduz a probabilidade de a resposta certa ser escolhida para uma determinada consulta do utilizador com essas palavras.

Por exemplo, pode ter dois QNAs separados com as seguintes perguntas:

|Rio QnAs|
|--|
|onde é o *local* de estacionamento|
|onde está a *localização* do multibanco|

Uma vez que estes dois QnAs são expressos com palavras muito semelhantes, esta semelhança pode causar pontuações muito semelhantes para muitas consultas de utilizador que são fraseada como  *"onde está a `<x>` localização"*. Em vez disso, tente diferenciar claramente as consultas como  *"onde está o parque de estacionamento"* e *"onde está o multibanco",* evitando palavras como "localização" que poderia estar em muitas perguntas no seu KB.

## <a name="collaborate"></a>Colaborar
O QnA Maker permite que os utilizadores colaborem numa base de conhecimento. Os utilizadores precisam de acesso ao grupo de recursos Azure QnA Maker para aceder às bases de conhecimento. Algumas organizações podem querer subcontratar a base de conhecimentos de edição e manutenção, e ainda ser capaz de proteger o acesso aos seus recursos Azure. Este modelo de editor-aprovação é feito através da criação de dois [serviços QnA Maker idênticos](../How-to/set-up-qnamaker-service-azure.md) em diferentes subscrições e selecionando um para o ciclo de testes de edição. Uma vez concluído o teste, os conteúdos da base de conhecimento são transferidos com um processo [de importação-exportação](../Tutorials/migrate-knowledge-base.md) para o serviço QnA Maker do aprovador que irá finalmente publicar a base de conhecimento e atualizar o ponto final.



## <a name="active-learning"></a>Aprendizagem ativa

[A aprendizagem ativa](../How-to/use-active-learning.md) faz o melhor trabalho ao sugerir questões alternativas quando tem uma vasta gama de qualidade e quantidade de consultas baseadas no utilizador. É importante permitir que as consultas de utilizador de aplicações de clientes participem no ciclo de feedback de aprendizagem ativa sem censura. Uma vez que as perguntas são sugeridas no portal QnA Maker, pode **[filtrar por sugestões](../How-To/improve-knowledge-base.md)** e depois rever e aceitar ou rejeitar essas sugestões.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-to/edit-knowledge-base.md)
