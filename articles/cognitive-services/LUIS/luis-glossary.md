---
title: Glossário - LUIS
description: O glossário explica termos que pode encontrar enquanto trabalha com o Serviço LUIS API.
ms.topic: reference
ms.date: 05/08/2020
ms.openlocfilehash: 1513099decc21a7d219bfcb84563619640028550
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681612"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Glossário de compreensão linguística do vocabulário e conceitos comuns
O glossário da Compreensão linguística (LUIS) explica termos que pode encontrar enquanto trabalha com o serviço LUIS.

## <a name="active-version"></a>Versão ativa

A versão ativa é a [versão](luis-how-to-manage-versions.md) da sua app que é atualizada quando faz alterações no modelo utilizando o portal LUIS. No portal LUIS, se quiser fazer alterações a uma versão que não seja a versão ativa, precisa de definir essa versão como ativa.

## <a name="active-learning"></a>Aprendizagem ativa

A aprendizagem ativa é uma técnica de aprendizagem automática em que o modelo aprendido pela máquina é usado para identificar novos exemplos informativos para rotular. No LUIS, a aprendizagem ativa refere-se à adição de expressões do tráfego de ponto final cujas previsões atuais não são claras para melhorar o seu modelo. Clique em "review endpoint utterances", para ver as expressões para rotular.

Veja também:
* [Informação conceptual](luis-concept-review-endpoint-utterances.md)
* [Tutorial revisão de declarações de ponto final](luis-tutorial-review-endpoint-utterances.md)
* Como melhorar a app LUIS [através da revisão das declarações de endpoint](luis-how-to-review-endpoint-utterances.md)

## <a name="application-app"></a>Aplicação (App)

No LUIS, a sua aplicação, ou app, é uma coleção de modelos aprendidos com máquinas, construídos no mesmo conjunto de dados, que trabalha em conjunto para prever intenções e entidades para um determinado cenário. Cada aplicação tem um ponto final de previsão separado.

Se estiver a construir um bot hr, poderá ter um conjunto de intenções, como "Agendar o tempo de licença", "perguntar sobre benefícios" e "atualizar informações pessoais" e entidades para cada uma dessas intenções que agrupa numa única aplicação.

## <a name="authoring"></a>Criação

A autoria é a capacidade de criar, gerir e implementar uma app LUIS, quer utilizando o portal LUIS ou a autoria de APIs.

### <a name="authoring-key"></a>Chave de autoria

A [chave de autor](luis-concept-keys.md) é usada para ser autora da aplicação. Não utilizado para consultas de ponto final de nível de produção. Para mais informações, consulte [os limites-chave](luis-limits.md#key-limits).

### <a name="authoring-resource"></a>Recurso de autoria

O seu [recurso de autoria](luis-concept-keys.md#azure-resources-for-luis) LUIS é um item manejável que está disponível através do Azure. O recurso é o seu acesso às capacidades de autoria, formação e publicação associadas do serviço Azure. O recurso inclui autenticação, autorização e informações de segurança que necessita para aceder ao serviço Azure associado.

O recurso de autoria tem um "tipo" Azure de `LUIS-Authoring` .

## <a name="batch-test"></a>Teste do lote

O teste do lote é a capacidade de validar os modelos da aplicação LUIS atual com um conjunto de comentários consistente e conhecido de expressões de utilizadores. O teste do lote é definido num [ficheiro formatado JSON](luis-concept-batch-test.md#batch-file-format).

Veja também:
* [Conceitos](luis-concept-batch-test.md)
* [Como executar](luis-how-to-batch-test.md) um teste de lote
* [Tutorial](luis-tutorial-batch-testing.md) - crie e execute um teste de lote

### <a name="f-measure"></a>Medida F

Nos testes de lote, uma medida da precisão do teste.

### <a name="false-negative-fn"></a>Falso negativo (FN)

Nos testes de lote, os pontos de dados representam declarações nas quais a sua aplicação previu incorretamente a ausência da intenção/entidade alvo.

### <a name="false-positive-fp"></a>Falso positivo (FP)

Nos testes de lote, os pontos de dados representam declarações nas quais a sua aplicação previu incorretamente a existência da intenção/entidade alvo.

### <a name="precision"></a>Precisão
Nos testes de lote, a precisão (também chamada de valor preditivo positivo) é a fração de expressões relevantes entre as expressões recuperadas.

Um exemplo para um ensaio de lote animal é o número de ovinos que foram previstos divididos pelo número total de animais (ovinos e não-ovelhas).

### <a name="recall"></a>Recall

Nos testes de lote, a recolha (também conhecida como sensibilidade), é a capacidade de a LUIS generalizar.

Um exemplo para um teste de lote animal é o número de ovinos que foram previstos divididos pelo número total de ovinos disponíveis.

### <a name="true-negative-tn"></a>Verdadeiro negativo (TN)

Um verdadeiro negativo é quando a sua aplicação corretamente não prevê qualquer correspondência. Nos testes de lote, ocorre um verdadeiro negativo quando a sua aplicação prevê uma intenção ou entidade para um exemplo que não tenha sido rotulado com essa intenção ou entidade.

### <a name="true-positive-tp"></a>Verdadeiro positivo (TP)

Verdadeiro positivo (TP) Um verdadeiro positivo é quando a sua aplicação corretamente prevê uma correspondência. Nos testes de lote, ocorre um verdadeiro positivo quando a sua aplicação prevê uma intenção ou entidade para um exemplo que tenha sido rotulado com essa intenção ou entidade.

## <a name="classifier"></a>Classificador

Um classificador é um modelo aprendido com máquinas que prevê em que categoria ou classe uma entrada se encaixa.

Uma [intenção](#intent) é um exemplo de um classificador.

## <a name="collaborator"></a>Colaborador

Um colaborador é conceptualmente a mesma coisa que um [colaborador.](#contributor) Um colaborador tem acesso quando um proprietário adiciona o endereço de e-mail do colaborador a uma app que não é controlada com acesso baseado em papéis (RBAC). Se ainda estiver a utilizar colaboradores, deve migrar a sua conta LUIS e utilizar recursos de autor da LUIS para gerir os colaboradores com o RBAC.

## <a name="contributor"></a>Contribuinte

Um colaborador não é o [proprietário](#owner) da app, mas tem as mesmas permissões para adicionar, editar e apagar as intenções, entidades, declarações. Um colaborador fornece acesso baseado em papéis (RBAC) a uma aplicação LUIS.

Veja também:
* [Como](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource) adicionar contribuintes

## <a name="descriptor"></a>Descritor

Um descritor é o termo anteriormente usado para uma [funcionalidade](#features)de aprendizagem automática.

## <a name="domain"></a>Domain

No contexto LUIS, um domínio é uma área de conhecimento. O seu domínio é específico do seu cenário. Diferentes domínios usam linguagem e terminologia específicas que têm significado no contexto do domínio. Por exemplo, se estiver a construir uma aplicação para reproduzir música, a sua aplicação teria termos e linguagem específicas para a música – palavras como "música, faixa, álbum, letras, lado B, artista". Por exemplo, por exemplo, domínios, ver [domínios pré-construídos](#prebuilt-domain).

## <a name="endpoint"></a>Ponto Final

### <a name="authoring-endpoint"></a>Ponto final de autoria

O URL de ponto final da LUIS é onde você é autor, treina e publica sua app. O URL de ponto final contém a região ou subdomínio personalizado da aplicação publicada, bem como o ID da aplicação.

Saiba mais sobre a autoria da sua app programáticamente a partir da [referência do Developer](developer-reference-resource.md#rest-endpoints)

### <a name="prediction-endpoint"></a>Ponto final de previsão

O URL final de previsão luis é onde você submete consultas LUIS após a [aplicação LUIS](#application-app) ser autora e publicada. O URL de ponto final contém a região ou subdomínio personalizado da aplicação publicada, bem como o ID da aplicação. Pode encontrar o ponto final na página de recursos do **[Azure](luis-how-to-azure-subscription.md)** da sua aplicação, ou pode obter o URL final da API get [App Info.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37)

O seu acesso ao ponto final da previsão está autorizado com a chave de previsão DO.

## <a name="entity"></a>Entidade

[As entidades](luis-concept-entity-types.md) são palavras em expressões que descrevem informações usadas para cumprir ou identificar uma intenção. Se a sua entidade for complexa e quiser que o seu modelo identifique peças específicas, pode dividir o seu modelo em subentidades. Por exemplo, você pode querer que você modelo para prever um endereço, mas também as subentidades de rua, cidade, estado e zipcode. As entidades também podem ser usadas como funcionalidades para modelos. A sua resposta da app LUIS incluirá tanto as intenções previstas como todas as entidades.

### <a name="entity-extractor"></a>Extrator de entidade

Um extrator de entidade por vezes conhecido apenas como um extrator é o tipo de modelo aprendido por máquina que a LUIS usa para prever entidades.

### <a name="entity-schema"></a>Entidade schema

A entidade é a estrutura que define para entidades aprendidas com máquinas com subentidades. O ponto final de previsão devolve todas as entidades e subentidades extraídas definidas no esquema.

### <a name="entitys-subentity"></a>Subentidade da entidade

Uma subentidade é uma entidade infantil de uma entidade de machine-learning.

### <a name="non-machine-learning-entity"></a>Entidade não-aprendizagem automática

Uma entidade que utiliza texto correspondente para extrair dados:
* Entidade de lista
* Entidade de expressão regular

### <a name="list-entity"></a>Entidade de lista

Uma [entidade da lista](reference-entity-list.md) representa um conjunto fixo e fechado de palavras relacionadas juntamente com os seus sinónimos. As entidades da lista são correspondências exatas, ao contrário de entidades aprendidas com máquinas.

A entidade será prevista se uma palavra na entidade da lista estiver incluída na lista. Por exemplo, se tiver uma entidade de lista chamada "tamanho" e tiver as palavras "pequeno, médio, grande" na lista, então a entidade de dimensão será prevista para todas as expressões em que as palavras "pequeno", "médio" ou "grande" são usados independentemente do contexto.

### <a name="regular-expression"></a>Expressão regular

Uma [entidade de expressão regular](reference-entity-regular-expression.md) representa uma expressão regular. As entidades de expressão regular são fósforos exatos, ao contrário das entidades aprendidas com máquinas.
### <a name="prebuilt-entity"></a>Entidade pré-construída

Ver entrada do modelo pré-construído para [entidade pré-construída](#prebuilt-entity)

## <a name="features"></a>Funcionalidades

No machine learning, uma característica é uma característica que ajuda o modelo a reconhecer um conceito particular. É uma insinuação que luis pode usar, mas não uma regra difícil.

Este termo também é referido como uma **[funcionalidade de aprendizagem automática.](luis-concept-feature.md)**

Estas dicas são usadas em conjunto com os rótulos para aprender a prever novos dados. O LUIS suporta ambas as listas de frases e utiliza outros modelos como funcionalidades.

### <a name="required-feature"></a>Característica necessária

Uma característica necessária é uma forma de limitar a saída de um modelo LUIS. Quando uma funcionalidade para uma entidade é marcada conforme necessário, a funcionalidade deve estar presente no exemplo para que a entidade seja prevista, independentemente do que a máquina aprendeu modelo prevê.

Considere um exemplo em que tem uma funcionalidade de número pré-construído que marcou conforme exigido na entidade de quantidade para um bot de encomenda de menu. Quando o seu bot `I want a bajillion large pizzas?` vir, bajillion não será previsto como uma quantidade, independentemente do contexto em que aparece. Bajillion não é um número válido e não será previsto pela entidade pré-construída.

## <a name="intent"></a>Intenção

Uma [intenção](luis-concept-intent.md) representa uma tarefa ou ação que o utilizador quer realizar. É um propósito ou objetivo expresso na entrada de um utilizador, como reservar um voo, ou pagar uma conta. No LUIS, uma expressão como um todo é classificada como uma intenção, mas partes da expressão são extraídas como entidades

## <a name="labeling-examples"></a>Exemplos de rotulagem

Rotulagem, ou marcação, é o processo de associar um exemplo positivo ou negativo a um modelo.

### <a name="labeling-for-intents"></a>Rotulagem para intenções
No LUIS, as intenções dentro de uma app são mutuamente exclusivas. Isto significa que, quando se acrescenta uma expressão a uma intenção, é considerado um exemplo _positivo_ para essa intenção e um exemplo _negativo_ para todas as outras intenções. Exemplos negativos não devem ser confundidos com a intenção "Nenhum", que representa expressões que estão fora do âmbito da app.

### <a name="labeling-for-entities"></a>Rotulagem para entidades
No LUIS, [label](label-entity-example-utterance.md) rotula-se uma palavra ou frase no exemplo de uma intenção com uma entidade como um exemplo _positivo._ A rotulagem mostra a intenção do que deve prever para esta expressão. As expressões rotuladas são usadas para treinar a intenção.

## <a name="luis-app"></a>App LUIS

Consulte a definição de [aplicação (app)](#application-app).

## <a name="model"></a>Modelo

Um modelo (machine learned) é uma função que faz uma previsão nos dados de entrada. No LUIS, referimo-nos a classificadores de intenções e extratores de entidades genericamente como "modelos", e referimo-nos a uma coleção de modelos que são treinados, publicados e consultados em conjunto como uma "app".

## <a name="normalized-value"></a>Valor normalizado

Acrescenta valores às suas [entidades de lista.](#list-entity) Cada um desses valores pode ter uma lista de um ou mais sinónimos. Apenas o valor normalizado é devolvido na resposta.

## <a name="overfitting"></a>Sobreajuste

O excesso de montagem acontece quando o modelo está fixado nos exemplos específicos e não é capaz de generalizar bem.

## <a name="owner"></a>Proprietário

Cada aplicação tem um proprietário que é a pessoa que criou a app. O proprietário gere permissões para a aplicação no portal Azure.

## <a name="phrase-list"></a>Lista de frases

Uma lista de [frases](luis-concept-feature.md) é um tipo específico de funcionalidade de aprendizagem automática que inclui um grupo de valores (palavras ou frases) que pertencem à mesma classe e devem ser tratados da mesma forma (por exemplo, nomes de cidades ou produtos).

## <a name="prebuilt-model"></a>Modelo pré-construído

Um [modelo pré-construído](luis-concept-prebuilt-model.md) é uma intenção, entidade ou coleção de ambos, juntamente com exemplos rotulados. Estes modelos pré-construídos comuns podem ser adicionados à sua app para reduzir o trabalho de desenvolvimento do modelo necessário para a sua aplicação.

### <a name="prebuilt-domain"></a>Domínio pré-construído

Um domínio pré-construído é uma aplicação LUIS configurada para um domínio específico, como domótica (HomeAutomation) ou reservas de restaurantes (RestaurantReservation). As intenções, as declarações e as entidades estão configuradas para este domínio.

### <a name="prebuilt-entity"></a>Entidade pré-construída

Uma entidade pré-construída é uma entidade que a LUIS fornece para tipos comuns de informação, como número, URL e e-mail. Estes são criados com base em dados públicos. Pode optar por adicionar uma entidade pré-construída como entidade autónoma, ou como uma funcionalidade a uma entidade

### <a name="prebuilt-intent"></a>Intenção pré-construída

Uma intenção pré-construída é uma intenção que a LUIS fornece para tipos comuns de informação e vem com as suas próprias expressões de exemplo rotulados.

## <a name="prediction"></a>Previsão

Uma previsão é um pedido de REST ao serviço de previsão Azure LUIS que acolhe novos dados (expressão do utilizador), e aplica a aplicação treinada e publicada a esses dados para determinar que intenções e entidades são encontradas.

### <a name="prediction-key"></a>Chave de previsão

A [chave de previsão](luis-concept-keys.md) (anteriormente conhecida como chave de subscrição) é a chave associada ao serviço LUIS que criou em Azure que autoriza o seu uso do ponto final de previsão.

Esta chave não é a chave da autoria. Se tiver uma chave de ponta de previsão, deve ser utilizada para quaisquer pedidos de ponto final em vez da chave de autor. Pode ver a sua chave de previsão atual dentro do URL de ponto final na página de recursos do Azure no site da LUIS. É o valor do nome/par de valor da chave de assinatura.

### <a name="prediction-resource"></a>Recurso de previsão

O seu recurso de previsão LUIS é um item manejável que está disponível através do Azure. O recurso é o seu acesso à previsão associada do serviço Azure. O recurso inclui previsões.

O recurso de previsão tem um "tipo" Azure de `LUIS` .

### <a name="prediction-score"></a>Classificação da predição

A [pontuação](luis-concept-prediction-score.md) é um número de 0 e 1 que é uma medida de quão confiante é o sistema que uma determinada expressão de entrada corresponde a uma determinada intenção. Uma pontuação mais próxima de 1 significa que o sistema está muito confiante sobre a sua saída e uma pontuação mais próxima de 0 significa que o sistema está confiante de que a entrada não corresponde a uma determinada saída. Pontuações no meio significam que o sistema não tem a certeza de como tomar a decisão.

Por exemplo, pegue um modelo que é usado para identificar se algum texto do cliente inclui uma encomenda de alimentos. Pode dar uma pontuação de 1 para "Eu gostaria de pedir um café" (o sistema está muito confiante de que esta é uma ordem) e uma pontuação de 0 para "a minha equipa ganhou o jogo ontem à noite" (o sistema está muito confiante de que isto não é uma ordem). E pode ter uma pontuação de 0,5 para "vamos tomar um chá" (não tem certeza se este é um pedido ou não).

## <a name="programmatic-key"></a>Chave programática

Renomeado para [chave de autoria](#authoring-key).

## <a name="publish"></a>Publicar

[Publicação](luis-how-to-publish-app.md) significa disponibilizar uma versão ativa luis na encenação ou [no ponto final](#endpoint)da produção.

## <a name="quota"></a>Quota

A quota LUIS é a limitação do nível de subscrição do Azure. A quota LUIS pode ser limitada por ambos os pedidos por segundo (Http Status 429) e pelo total de pedidos num mês (Http Status 403).

## <a name="schema"></a>Esquema

O seu esquema inclui as suas intenções e entidades, juntamente com as subentidades. O esquema está inicialmente planeado para então iterado ao longo do tempo. O esquema não inclui definições de aplicativos, funcionalidades ou pronunciações de exemplo.

## <a name="sentiment-analysis"></a>Análise de Sentimentos
A análise do sentimento fornece valores positivos ou negativos das expressões fornecidas pelo [Text Analytics](../text-analytics/overview.md).

## <a name="speech-priming"></a>Preparação do discurso

A preparação do discurso melhora o reconhecimento de palavras e frases faladas que são comumente usadas no seu cenário com [os Serviços de Fala](../speech-service/overview.md). Para a preparação de aplicações ativas pela fala, todos os exemplos marcados pela LUIS são usados para melhorar a precisão do reconhecimento da fala, criando um modelo de fala personalizado para esta aplicação específica. Por exemplo, num jogo de xadrez, queres ter a certeza que quando o utilizador diz "Move knight", não é interpretado como "Move night". A aplicação LUIS deve incluir exemplos em que "knight" é rotulado como uma entidade.

## <a name="starter-key"></a>Chave de arranque

Uma chave gratuita para usar quando começar a usar o LUIS.

## <a name="synonyms"></a>Sinónimos

Nas [entidades da lista](reference-entity-list.md)LUIS, pode criar um valor normalizado, que cada um pode ter uma lista de sinónimos. Por exemplo, se criar uma entidade de tamanho que tenha valores normalizados de pequenos, médios, grandes e extra-grandes. Pode criar sinónimos para cada valor como este:

|Valor nomalizado| Sinónimos|
|--|--|
|Pequeno| o pequenino, 8 onças|
|Médio| regular, 12 onças|
|Grande| grande, 16 onças|
|Xtra grande| o maior, 24 onças|

O modelo devolverá o valor normalizado para a entidade quando qualquer um dos sinónimos for visto na entrada.

## <a name="test"></a>Testar

[Testar](luis-concept-test.md) uma aplicação LUIS significa visualizar previsões de modelos.

## <a name="timezone-offset"></a>Compensação do fuso horário

O ponto final inclui [timezoneOffset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Este é o número em minutos que pretende adicionar ou remover da entidade pré-construída datetimeV2. Por exemplo, se a expressão for "a que horas é agora?", a data de dmissão do V2 é a hora atual para o pedido do cliente. Se o pedido do seu cliente vier de um bot ou de outra aplicação que não seja o mesmo que o utilizador do seu bot, deverá passar no offset entre o bot e o utilizador.

Consulte [alterar o fuso horário da entidade datav2 pré-construída](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Certificado de
Um [símbolo](luis-language-support.md#tokenization) é a menor unidade de texto que a LUIS pode reconhecer. Isto difere ligeiramente entre as línguas.

Para **inglês,** um símbolo é um espaço contínuo (sem espaços ou pontuação) de letras e números. Um espaço não é um símbolo.

|Frase|Contagem de token|Explicação|
|--|--|--|
|`Dog`|1|Uma única palavra sem pontuação ou espaços.|
|`RMT33W`|1|Um número de localizador de registo. Pode ter números e letras, mas não tem qualquer pontuação.|
|`425-555-5555`|5|Um número de telefone. Cada marca de pontuação é um único símbolo, pelo que `425-555-5555` seriam 5 fichas:<br>`425`<br>`-`<br>`555`<br>`-`<br>`5555` |
|`https://luis.ai`|7|`https`<br>`:`<br>`/`<br>`/`<br>`luis`<br>`.`<br>`ai`<br>|

## <a name="train"></a>Preparar

A formação é o processo de ensino da LUIS sobre quaisquer alterações à versão ativa desde a última [formação.](luis-how-to-train.md)

### <a name="training-data"></a>Dados de preparação

Os dados de formação são o conjunto de informação que é necessária para formar um modelo. Isto inclui o esquema, as expressões, funcionalidades e as definições de aplicação.

### <a name="training-errors"></a>Erros de treino

Erros de treino são previsões nos seus dados de treino que não correspondem aos seus rótulos.

## <a name="utterance"></a>Expressão

Uma [expressão](luis-concept-utterance.md) é a entrada do utilizador que é breve representante de texto de uma frase numa conversa. É uma frase de linguagem natural como "reservar 2 bilhetes para Seattle na próxima terça-feira". As expressões exemplo são adicionadas para treinar o modelo e o modelo prevê uma nova expressão em tempo de execução

## <a name="version"></a>Versão

Uma [versão](luis-how-to-manage-versions.md) LUIS é um exemplo específico de uma aplicação LUIS associada a uma aplicação LUIS ID e ao ponto final publicado. Cada aplicação LUIS tem pelo menos uma versão.
