---
title: Recolha de dados
description: Saiba quais os dados de exemplo a recolher durante o desenvolvimento da sua app
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: c901d4024292ad03a9195ad0cbd226f473917ee4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98738089"
---
# <a name="data-collection-for-your-app"></a>Recolha de dados para a sua aplicação

Uma aplicação de Compreensão de Idiomas (LUIS) precisa de dados como parte do desenvolvimento de aplicações.

## <a name="data-used-in-luis"></a>Dados utilizados no LUIS

A LUIS utiliza o texto como dados para treinar e testar a sua app LUIS para classificação de [intenções](luis-concept-intent.md) e para extração de [entidades.](luis-concept-entity-types.md) Precisa de um conjunto de dados suficientemente grande para que tenha dados suficientes para criar conjuntos de dados separados para treino e teste que tenham a diversidade e distribuição chamadas especificamente abaixo.  Os dados de cada um destes conjuntos não devem sobrepor-se.

## <a name="training-data-selection-for-example-utterances"></a>Seleção de dados de formação, por exemplo, expressões

Selecione as expressões para o seu conjunto de treino com base nos seguintes critérios:

* **Os dados reais são os melhores:**
    * **Dados reais da aplicação do cliente**: Selecione expressões que são dados reais da sua aplicação ao cliente.  Se o cliente enviar um formulário web com o seu inquérito hoje, e você estiver construindo um bot, você pode começar usando os dados do formulário web.
    * **Dados de origem de multidões**: Se não tiver quaisquer dados existentes, considere as expressões de crowdsourcing.  Tente obter declarações de fontes de multidão da sua população de utilizadores reais para que o seu cenário obtenha a melhor aproximação dos dados reais que a sua aplicação irá ver. As proclamações humanas de origem multidão são melhores do que expressões geradas por computador.  Quando se constrói um conjunto de dados de expressões sintéticas geradas em padrões específicos, faltará grande parte da variação natural que verá com as pessoas a criar as expressões e não acabará por generalizar-se bem na produção.
* **Diversidade de dados:**
    * **Diversidade da região**: Certifique-se de que os dados para cada intenção são tão diversos quanto possível, _incluindo frases_ (escolha de palavras) e _gramática._  Se você está ensinando uma intenção sobre as políticas de RH sobre dias de férias, certifique-se de ter expressões que representam os termos que são usados para todas as regiões que você está servindo.  Por exemplo, na Europa as pessoas podem perguntar `taking a holiday` sobre e nos EUA as pessoas podem perguntar sobre `taking vacation days` .
    * **Diversidade linguística**: Se tiver utilizadores com várias línguas nativas que se comunicam numa segunda língua, certifique-se de ter expressões que representem falantes não nativos.
    * **Diversidade de entradas**: Considere o seu caminho de entrada de dados. Se estiver a recolher dados de uma pessoa, departamento ou dispositivo de entrada (microfone) provavelmente está a perder diversidade que será importante para a sua app aprender sobre todos os caminhos de entrada.
    * **Diversidade de pontuação**: Considere que as pessoas usam diferentes níveis de pontuação em aplicações de texto e certifique-se de que tem uma diversidade de como a pontuação é usada. Se estiver a usar dados que vêm da fala, não terá qualquer pontuação, por isso os seus dados também não.
* **Distribuição de dados**: Certifique-se de que os dados espalhados pelas intenções representam a mesma disseminação de dados que a sua aplicação do cliente recebe. Se a sua app LUIS classificar as expressões que são pedidos para agendar uma licença (50%), mas também verá declarações sobre inquirição sobre os dias de licença (20%), aprovação de licenças (20%) e alguns fora de alcance e chit chat (10%) então o seu conjunto de dados deve ter as percentagens de amostra de cada tipo de expressão.
* **Utilize todos os formulários de dados**: Se a sua aplicação LUIS retirar dados de várias formas, certifique-se de incluir esses formulários nas suas expressões de formação. Por exemplo, se a sua aplicação de cliente tomar a entrada de texto da fala e dactilografada, precisa de ter expressões geradas de voz a texto, bem como declarações dactilografadas.  Você verá diferentes variações na forma como as pessoas falam da forma como escrevem, bem como diferentes erros no reconhecimento da fala e tipografias.  Toda esta variação deve estar representada nos seus dados de treino.
* **Exemplos positivos e negativos**: Para ensinar uma app LUIS, tem de aprender sobre qual é a intenção (positiva) e o que não é (negativo). Em LUIS, as declarações só podem ser positivas para uma única intenção. Quando uma expressão é adicionada a uma intenção, LUIS automaticamente faz desse mesmo exemplo um exemplo negativo para todas as outras intenções.
* **Dados fora do âmbito de aplicação**: Se a sua aplicação verá expressões que ficam fora das suas intenções definidas, certifique-se de fornecer essas. Os exemplos que não forem atribuídos a uma determinada intenção definida serão rotulados com a intenção **de Nenhum.**  É importante ter exemplos realistas para o **None** pretender prever corretamente expressões que estão fora do âmbito das intenções definidas.

    Por exemplo, se estiver a criar um bot HR focado no tempo de licença e tiver três intenções:
    * agendar ou editar uma licença
    * inquirir sobre dias de férias disponíveis
    * aprovar/desaprovar licença

    Você quer ter certeza de ter expressões que cobrem ambas as intenções, mas também que cobrem potenciais expressões fora desse âmbito que a aplicação deve servir como estas:
    * `What are my medical benefits?`
    * `Who is my HR rep?`
    * `tell me a joke`
* **Exemplos raros**: A sua aplicação terá de ter exemplos raros, bem como exemplos comuns.  Se a sua aplicação nunca tiver visto exemplos raros, não será capaz de identificá-los em produção. Se estiver a utilizar dados reais, poderá prever com maior precisão como a sua aplicação LUIS funcionará na produção.

### <a name="quality-instead-of-quantity"></a>Qualidade em vez de quantidade

Considere a qualidade dos seus dados existentes antes de adicionar mais dados.  Com o LUIS, estás a usar o Ensino das Máquinas.  A combinação das suas etiquetas e das funcionalidades de machine learning que define é o que a sua aplicação LUIS utiliza.  Não depende apenas da quantidade de rótulos para fazer a melhor previsão.  A diversidade de exemplos e a sua representação do que a sua app LUIS verá na produção é a parte mais importante.

### <a name="preprocessing-data"></a>Dados de pré-processamento

As seguintes etapas de pré-processamento ajudarão a construir uma melhor aplicação LUIS:

* **Remover duplicados**: As expressões duplicadas não doem, mas também não ajudam, pelo que removê-las poupará tempo de rotulagem.
* **Aplicar o mesmo pré-processo de aplicação cliente:** Se a sua aplicação ao cliente, que liga para o ponto final de previsão LUIS, aplicar o processamento de dados em tempo de execução antes de enviar o texto para o LUIS, deverá treinar a app LUIS em dados que são tratados da mesma forma. Por exemplo, se a sua aplicação de cliente utilizar [Bing Spell Check](../bing-spell-check/overview.md) para corrigir a ortografia das entradas para o LUIS, corrija as suas declarações de treino e teste antes de adicionar ao LUIS.
* **Não aplique novos processos de limpeza que a aplicação do cliente não utiliza:** Se a aplicação do seu cliente aceitar texto gerado pela fala diretamente sem qualquer limpeza, como gramática ou pontuação, as suas expressões precisam de refletir o mesmo, incluindo qualquer pontuação em falta e qualquer outro reconhecimento errado que terá de prestar contas.
* **Não limpe os dados**: Não se livre de entradas mal formadas que possa obter do reconhecimento de voz, de teclas acidentais ou de texto mal escrito/mal escrito. Se a sua aplicação verá entradas como estas, é importante que seja treinada e testada nelas. Adicione uma intenção _de entrada mal formada_ se não espera que a sua app a compreenda. Rotule estes dados para ajudar a sua aplicação LUIS a prever a resposta correta no tempo de execução. A sua aplicação ao cliente pode escolher uma resposta adequada a expressões ininteligíveis, tais como `Please try again` .

### <a name="labeling-data"></a>Dados de rotulagem

* **Rotular texto como se estivesse correto**: As expressões de exemplo devem ter todas as formas de uma entidade rotulada. Isto inclui texto que é mal escrito, mal escrito e mal traduzido.

### <a name="data-review-after-luis-app-is-in-production"></a>Análise de dados após app LUIS está em produção

[Reveja as declarações de ponto final](luis-concept-review-endpoint-utterances.md) para monitorizar o tráfego real de declarações depois de ter implementado uma aplicação para a produção.  Isto permite-lhe atualizar as suas declarações de treino com dados reais, que irão melhorar a sua aplicação. Qualquer aplicação construída com dados de cenários de multidões ou não reais terá de ser melhorada com base no seu uso real.

## <a name="test-data-selection-for-batch-testing"></a>Seleção de dados de teste para testes de lote

Todos os princípios acima referidos para as declarações de treino aplicam-se às expressões que deve utilizar para o seu [conjunto de testes](./luis-how-to-batch-test.md). Garantir a distribuição através de intenções e entidades espelham a distribuição real o mais próximo possível.

Não reutilhe as expressões do seu conjunto de treino no seu conjunto de testes. Isto distorça indevidamente os seus resultados e não lhe dará a indicação certa de como a sua app LUIS irá funcionar na produção.

Uma vez publicada a primeira versão da sua aplicação, deverá atualizar o seu conjunto de testes com expressões de tráfego real para garantir que o seu conjunto de testes reflete a sua distribuição de produção e pode monitorizar o desempenho realista ao longo do tempo.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como o LUIS altera os seus dados antes da previsão](luis-concept-data-alteration.md)