---
title: Glossário - LUIS
description: O glossário explica os termos que poderá encontrar enquanto trabalha com o Serviço LUIS API.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/08/2020
ms.openlocfilehash: 7c65c8272172cab9f5361d16141bf7b229037480
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786949"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Glossário de compreensão linguística do vocabulário e conceitos comuns
O glossário de Compreensão linguística (LUIS) explica termos que poderá encontrar enquanto trabalha com o serviço LUIS.

## <a name="active-version"></a>Versão ativa

A versão ativa é a [versão](luis-how-to-manage-versions.md) da sua app que é atualizada quando faz alterações ao modelo utilizando o portal LUIS. No portal LUIS, se pretender fazer alterações a uma versão que não é a versão ativa, tem de definir a versão como ativa.

## <a name="active-learning"></a>Aprendizagem ativa

A aprendizagem ativa é uma técnica de aprendizagem automática na qual o modelo de aprendizagem da máquina é usado para identificar novos exemplos informativos para rotular. No LUIS, a aprendizagem ativa refere-se à adição de expressões do tráfego de ponto final cujas previsões atuais não são claras para melhorar o seu modelo. Clique em "rever as expressões do ponto final", para ver as expressões para rotular.

Veja também:
* [Informação conceptual](luis-concept-review-endpoint-utterances.md)
* [Tutoriais rever declarações de ponto final](luis-tutorial-review-endpoint-utterances.md)
* Como melhorar a app LUIS [através da revisão das expressões de ponto final](luis-how-to-review-endpoint-utterances.md)

## <a name="application-app"></a>Aplicação (App)

No LUIS, a sua aplicação, ou app, é uma coleção de modelos aprendidos com máquinas, construídos no mesmo conjunto de dados, que trabalha em conjunto para prever intenções e entidades para um determinado cenário. Cada aplicação tem um ponto final de previsão separado.

Se estiver a construir um bot HR, poderá ter um conjunto de intenções, tais como "Agendar horário de férias", "inquirir sobre benefícios" e "atualizar informações pessoais" e entidades para cada uma dessas intenções que agrupar numa única aplicação.

## <a name="authoring"></a>Criação

A autoria é a capacidade de criar, gerir e implementar uma app LUIS, quer utilizando o portal LUIS, quer através das APIs de autoria.

### <a name="authoring-key"></a>Chave de autoria

A [chave de autoria](luis-how-to-azure-subscription.md) é usada para autoria da aplicação. Não é utilizado para consultas de ponto final de nível de produção. Para obter mais informações, consulte [os limites da chave.](luis-limits.md#key-limits)

### <a name="authoring-resource"></a>Recurso de autoria

O seu [recurso de autoria](luis-how-to-azure-subscription.md#azure-resources-for-luis) LUIS é um item manejável que está disponível através do Azure. O recurso é o seu acesso às capacidades de autoria, formação e publicação associadas do serviço Azure. O recurso inclui autenticação, autorização e informações de segurança necessárias para aceder ao serviço Azure associado.

O recurso de autoria tem um Azure "tipo" de `LUIS-Authoring` .

## <a name="batch-test"></a>Teste de lote

O teste em lote é a capacidade de validar os modelos atuais da app LUIS com um conjunto de testes consistente e conhecido de expressões de utilizador. O teste do lote é definido num [ficheiro formatado JSON](./luis-how-to-batch-test.md#batch-test-file).


Veja também:
* [Conceitos](./luis-how-to-batch-test.md)
* [Como executar](luis-how-to-batch-test.md) um teste de lote
* [Tutorial](./luis-how-to-batch-test.md) - crie e execute um teste de lote

### <a name="f-measure"></a>Medida F

Nos testes de lote, uma medida da precisão do teste.

### <a name="false-negative-fn"></a>Falso negativo (FN)

Nos testes de lote, os pontos de dados representam expressões em que a sua aplicação previu incorretamente a ausência da intenção/entidade alvo.

### <a name="false-positive-fp"></a>Falso positivo (FP)

Nos testes de lote, os pontos de dados representam expressões em que a sua aplicação previu incorretamente a existência da intenção/entidade alvo.

### <a name="precision"></a>Precisão
Nos testes de lote, a precisão (também chamada de valor preditivo positivo) é a fração de expressões relevantes entre as expressões recuperadas.

Exemplo de um ensaio de lote de animais é o número de ovinos que foram previstos divididos pelo número total de animais (ovinos e não ovinos).

### <a name="recall"></a>Chamar de volta

Nos testes de lote, lembre-se (também conhecido como sensibilidade), é a capacidade de a LUIS generalizar.

Exemplo para um ensaio de lote de animais é o número de ovinos que foram previstos divididos pelo número total de ovinos disponíveis.

### <a name="true-negative-tn"></a>Verdadeiro negativo (TN)

Um verdadeiro negativo é quando a sua aplicação corretamente não prevê qualquer correspondência. Nos testes de lote, ocorre um verdadeiro negativo quando a sua aplicação prevê uma intenção ou entidade para um exemplo que não foi rotulado com essa intenção ou entidade.

### <a name="true-positive-tp"></a>Verdadeiro positivo (TP)

Verdadeiro positivo (TP) Um verdadeiro positivo é quando a sua aplicação prevê corretamente uma correspondência. Nos testes de lote, ocorre um verdadeiro positivo quando a sua app prevê uma intenção ou entidade para um exemplo que foi rotulado com essa intenção ou entidade.

## <a name="classifier"></a>Classificador

Um classificador é um modelo aprendido por máquina que prevê em que categoria ou classe uma entrada se encaixa.

Uma [intenção](#intent) é um exemplo de um classificador.

## <a name="collaborator"></a>Colaborador

Um colaborador é conceptualmente a mesma coisa que um [contribuinte.](#contributor) Um colaborador tem acesso quando um proprietário adiciona o endereço de e-mail do colaborador a uma app que não é controlada com o controlo de acesso baseado em funções Azure (Azure RBAC). Se ainda estiver a utilizar colaboradores, deve migrar a sua conta LUIS e utilizar recursos de autoria do LUIS para gerir colaboradores com a Azure RBAC.

## <a name="contributor"></a>Contribuinte

Um contribuinte não é o [proprietário](#owner) da app, mas tem as mesmas permissões para adicionar, editar e apagar as intenções, entidades, expressões. Um colaborador fornece o controlo de acesso baseado em funções Azure (Azure RBAC) a uma aplicação LUIS.

Veja também:
* [Como adicionar](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource) contribuintes

## <a name="descriptor"></a>Descritor

Um descritor é o termo anteriormente usado para uma funcionalidade de aprendizagem [automática](#features).

## <a name="domain"></a>Domínio

No contexto LUIS, um domínio é uma área de conhecimento. O seu domínio é específico do seu cenário. Diferentes domínios usam linguagem e terminologia específicas que têm significado no contexto do domínio. Por exemplo, se estiver a construir uma aplicação para tocar música, a sua aplicação teria termos e linguagem específicas para a música – palavras como "música, faixa, álbum, letra, lado b, artista". Por exemplo, ver domínios [pré-construídos.](#prebuilt-domain)

## <a name="endpoint"></a>Ponto final

### <a name="authoring-endpoint"></a>Ponto final de autoria

O URL de ponta de autoria luis é onde você autoriza, treina e publica a sua app. O URL de ponto final contém a região ou subdomínio personalizado da aplicação publicada, bem como o ID da aplicação.

Saiba mais sobre a autoria da sua aplicação programáticamente a partir da [referência Developer](developer-reference-resource.md#rest-endpoints)

### <a name="prediction-endpoint"></a>Ponto final de previsão

O URL de ponto final de previsão LUIS é onde você submete consultas LUIS após a [app LUIS](#application-app) é da autoria e publicação. O URL de ponto final contém a região ou subdomínio personalizado da aplicação publicada, bem como o ID da aplicação. Pode encontrar o ponto final na página de **[recursos Azure](luis-how-to-azure-subscription.md)** da sua aplicação, ou pode obter o URL de ponto final a partir da API [Get App Info.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37)

O seu acesso ao ponto final de previsão é autorizado com a chave de previsão LUIS.

## <a name="entity"></a>Entidade

[As entidades](luis-concept-entity-types.md) são palavras em expressões que descrevem a informação usada para cumprir ou identificar uma intenção. Se a sua entidade for complexa e quiser que o seu modelo identifique peças específicas, pode dividir o seu modelo em subentidades. Por exemplo, pode querer que o modelo preveja um endereço, mas também as subentidades da rua, cidade, estado e zipcode. As entidades também podem ser usadas como funcionalidades para modelos. A sua resposta da app LUIS incluirá tanto as intenções previstas como todas as entidades.

### <a name="entity-extractor"></a>Extrator de entidades

Um extrator de entidades por vezes conhecido apenas como um extrator é o tipo de modelo aprendido por máquina que a LUIS usa para prever entidades.

### <a name="entity-schema"></a>Esquema de entidade

O esquema da entidade é a estrutura que define para entidades aprendidas com máquinas com subentidades. O ponto final de previsão devolve todas as entidades e subentidades extraídas definidas no esquema.

### <a name="entitys-subentity"></a>Subgrupo da entidade

Uma sub-entidade é uma entidade infantil de uma entidade de aprendizagem automática.

### <a name="non-machine-learning-entity"></a>Entidade não-máquina de aprendizagem

Uma entidade que usa texto correspondente para extrair dados:
* Entidade de lista
* Entidade de expressão regular

### <a name="list-entity"></a>Entidade de lista

Uma [entidade de lista](reference-entity-list.md) representa um conjunto fixo e fechado de palavras relacionadas juntamente com os seus sinónimos. As entidades de lista são correspondências exatas, ao contrário das entidades aprendidas com máquinas.

A entidade será prevista se uma palavra na entidade de lista for incluída na lista. Por exemplo, se tiver uma entidade de lista chamada "tamanho" e tiver as palavras "pequeno, médio, grande" na lista, então a entidade de tamanho será prevista para todas as expressões onde as palavras "pequeno", "médio" ou "grande" são usadas independentemente do contexto.

### <a name="regular-expression"></a>Expressão regular

Uma [entidade de expressão regular](reference-entity-regular-expression.md) representa uma expressão regular. As entidades de expressão regular são fósforos exatos, ao contrário das entidades aprendidas com máquinas.
### <a name="prebuilt-entity"></a>Entidade pré-construída

Ver entrada do modelo [pré-construído para entidade pré-construída](#prebuilt-entity)

## <a name="features"></a>Funcionalidades

No machine learning, uma característica é uma característica que ajuda o modelo a reconhecer um conceito particular. É uma dica que LUIS pode usar, mas não uma regra difícil.

Este termo também é referido como uma **[característica de aprendizagem automática](luis-concept-feature.md)**.

Estas dicas são usadas em conjunto com os rótulos para aprender a prever novos dados. O LUIS suporta ambas as listas de frases e utiliza outros modelos como funcionalidades.

### <a name="required-feature"></a>Recurso necessário

Uma característica necessária é uma forma de limitar a saída de um modelo LUIS. Quando uma funcionalidade para uma entidade é marcada como necessário, a funcionalidade deve estar presente no exemplo para que a entidade seja prevista, independentemente do que o modelo aprendido da máquina preveja.

Considere um exemplo onde tem uma funcionalidade de número pré-incorporado que marcou conforme exigido na entidade de quantidade para um bot de encomenda de menu. Quando o seu bot `I want a bajillion large pizzas?` vê, o bajillion não será previsto como uma quantidade, independentemente do contexto em que aparece. Bajillion não é um número válido e não será previsto pelo número de entidade pré-construída.

## <a name="intent"></a>Intenção

Uma [intenção](luis-concept-intent.md) representa uma tarefa ou ação que o utilizador quer realizar. É um propósito ou objetivo expresso na entrada de um utilizador, como reservar um voo ou pagar uma conta. Em LUIS, uma expressão como um todo é classificada como uma intenção, mas partes da expressão são extraídas como entidades

## <a name="labeling-examples"></a>Exemplos de rotulagem

A rotulagem, ou marcação, é o processo de associar um exemplo positivo ou negativo a um modelo.

### <a name="labeling-for-intents"></a>Rotulagem para intenções
No LUIS, as intenções dentro de uma app são mutuamente exclusivas. Isto significa que quando se adiciona uma expressão a uma intenção, é considerado um exemplo _positivo_ para essa intenção e um exemplo _negativo_ para todas as outras intenções. Exemplos negativos não devem ser confundidos com a intenção "Nenhum", que representa expressões que estão fora do âmbito da app.

### <a name="labeling-for-entities"></a>Rotulagem para entidades
Em LUIS, [rotula-se](label-entity-example-utterance.md) uma palavra ou frase no exemplo de uma intenção com uma entidade como um exemplo _positivo._ A rotulagem mostra a intenção do que deve prever para esta expressão. As expressões rotuladas são usadas para treinar a intenção.

## <a name="luis-app"></a>Aplicativo LUIS

Consulte a definição para [aplicação (app)](#application-app).

## <a name="model"></a>Modelação

Um modelo (aprendido com máquina) é uma função que faz uma previsão sobre os dados de entrada. No LUIS, referimo-nos a classificadores de intenção e extratores de entidades genericamente como "modelos", e referimo-nos a uma coleção de modelos que são treinados, publicados e consultados em conjunto como uma "app".

## <a name="normalized-value"></a>Valor normalizado

Adiciona valores às entidades da sua [lista.](#list-entity) Cada um desses valores pode ter uma lista de um ou mais sinónimos. Apenas o valor normalizado é devolvido na resposta.

## <a name="overfitting"></a>Sobreajuste

A adaptação excessiva acontece quando o modelo está fixado nos exemplos específicos e não é capaz de generalizar bem.

## <a name="owner"></a>Proprietário

Cada aplicação tem um proprietário que é a pessoa que criou a app. O proprietário gere permissões para a aplicação no portal Azure.

## <a name="phrase-list"></a>Lista de frases

Uma [lista de frases](luis-concept-feature.md) é um tipo específico de funcionalidade de aprendizagem automática que inclui um grupo de valores (palavras ou frases) que pertencem à mesma classe e devem ser tratados da mesma forma (por exemplo, nomes de cidades ou produtos).

## <a name="prebuilt-model"></a>Modelo pré-construído

Um [modelo pré-construído](luis-concept-prebuilt-model.md) é uma intenção, entidade ou coleção de ambos, juntamente com exemplos rotulados. Estes modelos pré-construídos comuns podem ser adicionados à sua app para reduzir o trabalho de desenvolvimento do modelo necessário para a sua aplicação.

### <a name="prebuilt-domain"></a>Domínio pré-construído

Um domínio pré-construído é uma aplicação LUIS configurada para um domínio específico, como domótica (HomeAutomation) ou reservas de restaurantes (RestaurantReservation). As intenções, as declarações e as entidades estão configuradas para este domínio.

### <a name="prebuilt-entity"></a>Entidade pré-construída

Uma entidade pré-construída é uma entidade que a LUIS fornece para tipos comuns de informação, tais como número, URL e e-mail. Estes são criados com base em dados públicos. Pode optar por adicionar uma entidade pré-construída como entidade autónoma, ou como recurso a uma entidade

### <a name="prebuilt-intent"></a>Intenção pré-construída

Uma intenção pré-construída é uma intenção que o LUIS fornece para tipos comuns de informação e vem com as suas próprias expressões de exemplo rotuladas.

## <a name="prediction"></a>Predição

Uma previsão é um pedido de REPOUSO ao serviço de previsão Azure LUIS que acolhe novos dados (expressão de utilizador), e aplica a aplicação treinada e publicada a esses dados para determinar que intenções e entidades são encontradas.

### <a name="prediction-key"></a>Chave de previsão

A [chave de previsão](luis-how-to-azure-subscription.md) (anteriormente conhecida como chave de subscrição) é a chave associada ao serviço LUIS que criou no Azure que autoriza o seu uso do ponto final de previsão.

Esta chave não é a chave de autoria. Se tiver uma chave de ponto final de previsão, deve ser usada para quaisquer pedidos de ponto final em vez da chave de autoria. Pode ver a sua chave de previsão atual dentro do URL do ponto final na página de recursos do Azure no site da LUIS. É o valor do par de nome/valor da chave de subscrição.

### <a name="prediction-resource"></a>Recurso de previsão

O seu recurso de previsão LUIS é um item manejável que está disponível através do Azure. O recurso é o seu acesso à previsão associada do serviço Azure. O recurso inclui previsões.

O recurso de previsão tem um Azure "tipo" de `LUIS` .

### <a name="prediction-score"></a>Classificação da predição

A [pontuação](luis-concept-prediction-score.md) é um número de 0 e 1 que é uma medida de quão confiante o sistema é que uma determinada expressão de entrada corresponde a uma determinada intenção. Uma pontuação mais próxima de 1 significa que o sistema está muito confiante sobre a sua saída e uma pontuação mais próxima de 0 significa que o sistema está confiante de que a entrada não corresponde a uma determinada saída. Pontuações no meio significam que o sistema não tem certeza de como tomar a decisão.

Por exemplo, pegue num modelo que seja usado para identificar se algum texto do cliente inclui uma encomenda de alimentos. Pode dar uma pontuação de 1 para "Eu gostaria de pedir um café" (o sistema está muito confiante de que isto é uma ordem) e uma pontuação de 0 para "a minha equipa ganhou o jogo ontem à noite" (o sistema está muito confiante de que isto não é uma ordem). E pode ter uma pontuação de 0,5 para "vamos tomar um chá" (não sabe se isto é uma ordem ou não).

## <a name="programmatic-key"></a>Chave programática

Renomeado para [a chave de autoria.](#authoring-key)

## <a name="publish"></a>Publicar

[Publicar](luis-how-to-publish-app.md) significa disponibilizar uma versão ativa da LUIS no [ponto final](#endpoint)de encenação ou de produção.

## <a name="quota"></a>Quota

A quota LUIS é a limitação do nível de subscrição do Azure. A quota LUIS pode ser limitada por ambos os pedidos por segundo (Estado HTTP 429) e o total de pedidos num mês (Estado HTTP 403).

## <a name="schema"></a>Esquema

O seu esquema inclui as suas intenções e entidades, juntamente com as subentírias. O esquema está inicialmente planeado para então iterado ao longo do tempo. O esquema não inclui configurações de aplicações, funcionalidades ou exemplos de expressões.

## <a name="sentiment-analysis"></a>Análise de Sentimentos
A análise do sentimento fornece valores positivos ou negativos das expressões fornecidas pela [Text Analytics](../text-analytics/overview.md).

## <a name="speech-priming"></a>Escorva da fala

A preparação da fala melhora o reconhecimento de palavras e frases faladas que são comumente usadas no seu cenário com [serviços de fala.](../speech-service/overview.md) Para aplicações ativadas para a escorva da fala, todos os exemplos com rótulo LUIS são usados para melhorar a precisão do reconhecimento da fala, criando um modelo de fala personalizado para esta aplicação específica. Por exemplo, num jogo de xadrez, você quer ter certeza de que quando o utilizador diz "Move knight", não é interpretado como "Move night". A aplicação LUIS deve incluir exemplos em que "cavaleiro" é rotulado como uma entidade.

## <a name="starter-key"></a>Chave de arranque

Uma chave gratuita para usar quando começar a usar o LUIS.

## <a name="synonyms"></a>Sinónimos

Nas [entidades da lista](reference-entity-list.md)LUIS, pode criar um valor normalizado, que cada um pode ter uma lista de sinónimos. Por exemplo, se criar uma entidade de tamanho que tenha valores normalizados de pequenos, médios, grandes e extra-grandes. Pode criar sinónimos para cada valor como este:

|Valor nãomalizado| Sinónimos|
|--|--|
|Pequeno| o pequenino, 8 onça|
|Médio| regular, 12 onça|
|Grande| grande, 16 onça|
|Xtra grande| o maior, 24 onças|

O modelo devolverá o valor normalizado para a entidade quando qualquer um dos sinónimos for visto na entrada.

## <a name="test"></a>Teste

[Testar](luis-concept-test.md) uma aplicação LUIS significa visualizar previsões de modelos.

## <a name="timezone-offset"></a>Compensação do tempomo-de-tempo

O ponto final inclui [o timezoneOffset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Este é o número em minutos que pretende adicionar ou remover da entidade pré-construída dataV2. Por exemplo, se a expressão for "a que horas é agora?", a data que oV2 devolveu é a hora atual para o pedido do cliente. Se o seu pedido de cliente vier de um bot ou de outra aplicação que não seja igual ao utilizador do seu bot, deverá passar no offset entre o bot e o utilizador.

Consulte [o fuso horário de alteração da entidade datatimeV2 pré-construída](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
Um [símbolo](luis-language-support.md#tokenization) é a menor unidade de texto que LUIS pode reconhecer. Isto difere ligeiramente entre as línguas.

Para **inglês**, um símbolo é um vão contínuo (sem espaços ou pontuação) de letras e números. Um espaço não é um símbolo.

|Expressão|Contagem de token|Explicação|
|--|--|--|
|`Dog`|1|Uma única palavra sem pontuação ou espaços.|
|`RMT33W`|1|Um número de localizador de registos. Pode ter números e letras, mas não tem qualquer pontuação.|
|`425-555-5555`|5|Um número de telefone. Cada marca de pontuação é um único símbolo, por isso  `425-555-5555` seriam 5 fichas:<br>`425`<br>`-`<br>`555`<br>`-`<br>`5555` |
|`https://luis.ai`|7|`https`<br>`:`<br>`/`<br>`/`<br>`luis`<br>`.`<br>`ai`<br>|

## <a name="train"></a>Preparar

[A formação](luis-how-to-train.md) é o processo de ensino do LUIS sobre quaisquer alterações à versão ativa desde o último treino.

### <a name="training-data"></a>Dados de preparação

Os dados de formação são o conjunto de informações necessárias para formar um modelo. Isto inclui o esquema, expressões, funcionalidades e configurações de aplicação.

### <a name="training-errors"></a>Erros de treino

Erros de treino são previsões nos seus dados de treino que não correspondem às suas etiquetas.

## <a name="utterance"></a>Expressão

Uma expressão é a entrada [do](luis-concept-utterance.md) utilizador que é curta representativa de uma frase numa conversa. É uma frase de linguagem natural como "reserve 2 bilhetes para Seattle na próxima terça-feira". Exemplos são adicionados para treinar o modelo e o modelo prevê novas expressões no tempo de execução

## <a name="version"></a>Versão

Uma [versão](luis-how-to-manage-versions.md) LUIS é uma instância específica de uma aplicação LUIS associada a um ID de aplicação LUIS e ao ponto final publicado. Cada aplicação LUIS tem pelo menos uma versão.
