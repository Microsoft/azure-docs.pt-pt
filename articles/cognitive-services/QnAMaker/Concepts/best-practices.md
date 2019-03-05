---
title: Melhores práticas - QnA Maker
titlesuffix: Azure Cognitive Services
description: Utilize estas melhores práticas para melhorar a sua base de dados de conhecimento e fornecer os melhores resultados para os utilizadores finais do seu bot de aplicação/bate-papo.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/26/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 72a3bead4ba8670187c35856f9cd4e4f4b975a30
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57311700"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Melhores práticas de uma base de dados de conhecimento do QnA Maker
O [ciclo de vida de desenvolvimento de base de dados de conhecimento](../Concepts/development-lifecycle-knowledge-base.md) orienta-o sobre como gerir a sua BDC do início ao fim. Utilize estas melhores práticas para melhorar a sua base de dados de conhecimento e fornecer os melhores resultados para os utilizadores finais do seu bot de aplicação/bate-papo.

## <a name="extraction"></a>Extração
O serviço QnA Maker está continuamente a melhorar os algoritmos que extrair QnAs de conteúdo e expandir a lista de ficheiro suportados e formatos HTML. Siga os [diretrizes](../Concepts/data-sources-supported.md) para extração de dados com base no seu tipo de documento. 

Em geral, as páginas de FAQ devem ser autónomo e não combinada com outras informações. Os manuais de produto devem ter títulos claros e preferencialmente, uma página de índice. 

## <a name="creating-good-questions-and-answers"></a>Criar boas perguntas e respostas

### <a name="good-questions"></a>Boas perguntas

As melhores perguntas são simples. Considere a palavra-chave ou a expressão para cada pergunta, em seguida, criar uma simple pergunta para essa palavra-chave ou frase. 

Adicione tantas perguntas alternativas conforme necessário, mas manter as alterações simples. A adição de mais palavras ou frases que não fazem parte da meta principal da pergunta não ajuda QnA Maker encontrar uma correspondência. 

### <a name="good-answers"></a>Boa respostas

As melhores respostas são respostas simples, mas não muito simples, como Sim e não existem respostas. Se a sua resposta deve ligar a outras origens ou fornecer uma experiência avançada com suporte de dados e links, utilize [marcação](../how-to/metadata-generateanswer-usage.md) para distinguir o tipo de resposta esperado, em seguida, enviar essa etiqueta com a consulta para obter a versão de resposta correta.

## <a name="chit-chat"></a>Chit-bate-papo
Adicionar chit-bate-papo ao seu bot, para tornar o seu bot mais conversacionais e apelativas, com esforço reduzido. Pode facilmente adicionar conjuntos de dados de chit chat para 3 personalidades predefinidas ao criar a sua BDC e alterá-los em qualquer altura. Saiba como [adicionar chit-bate-papo para sua BDC](../How-To/chit-chat-knowledge-base.md). 

### <a name="choosing-a-personality"></a>Escolher uma personalidade
Chit-bate-papo é suportada para 3 personalidades predefinidas: 

|Personalidades|
|--|
|O Professional|
|O amigo|
|Revista em Quadrinhos|

O intervalo de respostas de formal a informal e irreverente. Deve selecionar a personalidade que mais se aproxima está alinhada com o tom que pretende para o bot. Pode ver os conjuntos de dados e escolha uma que funciona como uma base para o bot e, em seguida, personalizar as respostas. 

### <a name="edit-bot-specific-questions"></a>Editar perguntas específicas de bot
Existem algumas questões específicas de bot que fazem parte do conjunto de dados chit-bate-papo e tem sido preenchidos com respostas genéricas. Altere essas respostas para melhor refletir os detalhes do seu bot. 

Recomendamos que o faça o seguinte QnAs chit-bate-papo mais específicas:

* Quem é?
* O que fazer?
* Que idade tem?
* Quem criou?
* Olá
   

## <a name="rankingscoring"></a>Classificação de classificação /
Certifique-se de que está fazendo o melhor uso dos recursos de classificação que do QnA Maker suporta. Se o fizer, irá melhorar a probabilidade de que consulta um determinado usuário foi respondida com uma resposta adequada.

### <a name="choosing-a-threshold"></a>Escolher um limiar
A pontuação de confiança de predefinição que é utilizada como um limiar é 50, no entanto, pode alterá-lo para a sua BDC com base nas suas necessidades. Uma vez que cada KB é diferente, deve testar e escolha o limiar de que é melhor adequado para seu KB. Leia mais sobre o [pontuação de confiança](../Concepts/confidence-score.md). 

### <a name="add-alternate-questions"></a>Adicionar perguntas alternativas
[Alternate perguntas](../How-To/edit-knowledge-base.md) melhorar a probabilidade de uma correspondência com uma consulta de utilizador. Perguntas alternativas são úteis quando existem múltiplas formas em que pode ser solicitada a mesma pergunta. Isto pode incluir alterações na estrutura da sentença e estilo do word.

|Consulta original|Consultas alternativas|Alterar| 
|--|--|--|
|É de estacionamento disponíveis?|Tem carro park?|estrutura de sentença|
 |Olá!|Yo<br>Olha aí!|Estilo do Word ou gíria|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Utilizar etiquetas de metadados para o filtro de perguntas e respostas

[Metadados](../How-To/edit-knowledge-base.md) acrescenta a capacidade para restringir os resultados de uma consulta de utilizador com base em etiquetas de metadados. A resposta da base de dados de conhecimento pode ser diferente consoante a marca de metadados, mesmo que a consulta é o mesmo. Por exemplo, *"onde está localizado de estacionamento"* pode ter uma resposta diferente se a localização do ramo restaurante for diferente, ou seja, os metadados são *localização: Seattle* versus *localização: Redmond*.

### <a name="use-synonyms"></a>Utilizar sinónimos
Embora haja algum suporte para sinónimos em inglês, utilize maiúsculas e minúsculas [alterações de palavras](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) para adicionar sinónimos para palavras-chave que assumir a forma diferente. Sinónimos devem ser adicionados ao nível de serviço QnA Maker e partilhados por todas as bases de dados de conhecimento no serviço.

|Original word|Sinónimos|
|--|--|
|Comprar|comprar<br>netbanking<br>banca NET|

### <a name="use-distinct-words-to-differentiate-questions"></a>Utilizar palavras diferentes para diferenciar perguntas
Algoritmo de classificação do QnA Maker, que corresponde a uma consulta de utilizador com uma pergunta na base de dados de conhecimento, funciona melhor se cada pergunta resolve uma necessidade de diferente. Repetição da mesma palavra definido entre perguntas reduz a probabilidade de que a resposta certa é escolhida para uma consulta de determinado usuário com essas palavras. 

Por exemplo, pode ter dois QnAs separados com as seguintes perguntas:

|QnAs|
|--|
|onde está o estacionamento *localização*|
|onde está o caixa Eletrônico *localização*|

Uma vez que estes dois QnAs são dizer com palavras muito semelhantes, essa semelhança poderia causar pontuações muito semelhantes para muitas consultas de utilizador que estão a dizer, como *"em que é o `<x>` localização"*. Em vez disso, tente claramente diferenciá-los com consultas como *"em que é a área de estacionamento"* e *"onde está o caixa Eletrônico"*, ao evitar palavras como "localização" que poderia ser em muitas perguntas em sua KB. 

## <a name="collaborate"></a>Colaboração
A ferramenta QnA Maker permite que os usuários [colaborar](../How-to/collaborate-knowledge-base.md) numa base de dados de conhecimento. Os utilizadores precisam de acesso para o grupo de recursos do Azure QnA Maker para acessar as bases de dados de conhecimento. Algumas organizações poderão querer terceirizar a edição de base de dados de conhecimento e a manutenção e continuará a poder proteger o acesso aos seus recursos do Azure. Esse modelo de aprovador de editor é feito ao configurar dois idêntico [serviços do QnA Maker](../How-to/set-up-qnamaker-service-azure.md) em diferentes subscrições e selecionando um para o ciclo de teste de edição. Assim que o teste estiver concluído, o conteúdo de base de dados é transferido com um [importação-exportação](../Tutorials/migrate-knowledge-base.md) processos para o serviço QnA Maker do aprovador que finalmente publicar a base de dados de conhecimento e atualizar o ponto final.

## <a name="active-learning"></a>Aprendizagem ativa

[Aprendizagem ativa](../How-to/improve-knowledge-base.md) faz o trabalho melhor de sugerir perguntas alternativas quando tem uma vasta gama de qualidade e a quantidade de consultas com base no utilizador. É importante permitir consultas de utilizador do cliente-aplicativos participar no ativo ciclo de comentários sem censura de aprendizado. Assim que as perguntas são sugeridas no portal do QnA Maker, terá de rever e aceitar ou rejeitar essas sugestões. 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-to/edit-knowledge-base.md)
