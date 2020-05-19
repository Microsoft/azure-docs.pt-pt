---
title: Recolha de dados
description: Saiba que dados de exemplo recolher durante o desenvolvimento da sua app
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 7abb3736eb9d7c73465ffa646b79e8e7dd7ae88b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599460"
---
# <a name="data-collection-for-your-app"></a>Recolha de dados para a sua app

Uma aplicação de Compreensão de Línguas (LUIS) precisa de dados como parte do desenvolvimento de apps.

## <a name="data-used-in-luis"></a>Dados utilizados no LUIS

O LUIS utiliza o texto como dados para treinar e testar a sua app LUIS para classificação para [intenções](luis-concept-intent.md) e para extração de [entidades.](luis-concept-entity-types.md) Você precisa de um conjunto de dados grande o suficiente para que você tenha dados suficientes para criar conjuntos de dados separados tanto para treino como teste que tenham a diversidade e distribuição chamados especificamente abaixo.  Os dados de cada um destes conjuntos não devem sobrepor-se.

## <a name="training-data-selection-for-example-utterances"></a>Seleção de dados de formação, por exemplo, expressões

Selecione expressões para o seu conjunto de formação com base nos seguintes critérios:

* **Os dados reais são os melhores:**
    * **Dados reais da aplicação do cliente**: Selecione expressões que sejam dados reais da sua aplicação cliente.  Se o cliente enviar um formulário web com a sua investigação hoje, e você estiver construindo um bot, você pode começar usando os dados do formulário web.
    * **Dados de multidões**: Se não tiver dados existentes, considere as declarações de crowd sourcing.  Tente obter declarações de fontes de multidão da sua população utilizadora real para o seu cenário para obter a melhor aproximação dos dados reais que a sua aplicação verá. As proclamações humanas de origem multidão são melhores do que as expressões geradas por computador.  Quando se constrói um conjunto de dados de expressões sintéticas geradas em padrões específicos, faltará grande parte da variação natural que verá com as pessoas a criarem as expressões e não acabarão por generalizar bem na produção.
* **Diversidade de dados:**
    * **Diversidade da região**: Certifique-se de que os dados de cada intenção são o mais diversos possível, incluindo _a formulação_ (escolha de palavras) e a _gramática._  Se está a ensinar uma intenção sobre as políticas de RH sobre os dias de férias, certifique-se de que tem expressões que representam os termos que são usados para todas as regiões que está a servir.  Por exemplo, na Europa as pessoas podem perguntar sobre `taking a holiday` e nos EUA as pessoas podem perguntar `taking vacation days` sobre.
    * **Diversidade linguística**: Se tiver utilizadores com várias línguas nativas que se comunicam numa segunda língua, certifique-se de ter expressões que representem falantes não nativos.
    * **Diversidade de entrada**: Considere o seu caminho de entrada de dados. Se estiver a recolher dados de uma pessoa, departamento ou dispositivo de entrada (microfone), é provável que falte diversidade que será importante para a sua aplicação aprender sobre todos os caminhos de entrada.
    * Diversidade de **pontuação**: Considere que as pessoas usam diferentes níveis de pontuação em aplicações de texto e certifique-se de que tem uma diversidade de como a pontuação é usada. Se estiver a usar dados que vêm da fala, não terá qualquer pontuação, por isso os seus dados também não devem.
* **Distribuição de dados**: Certifique-se de que os dados espalhados pelas intenções representam a mesma disseminação de dados que a sua aplicação de cliente recebe. Se a sua app LUIS classificar as declarações que são pedidos para agendar uma licença (50%), mas também verá declarações sobre a inquirição sobre os dias de férias restantes (20%), aprovando folhas (20%) e alguns fora de âmbito e chit chat (10%) em seguida, o seu conjunto de dados deve ter as percentagens de amostra de cada tipo de expressão.
* **Utilize todos os formulários**de dados : Se a sua aplicação LUIS irá retirar dados em várias formas, certifique-se de incluir esses formulários nas suas declarações de formação. Por exemplo, se a sua aplicação de cliente tomar a entrada de texto da dactilografada, precisa de ter expressões geradas de fala a texto, bem como declarações dactilografadas.  Verá variações diferentes na forma como as pessoas falam da forma como escrevem, bem como diferentes erros no reconhecimento da fala e nos erros.  Toda esta variação deve estar representada nos seus dados de formação.
* **Exemplos positivos e negativos**: Para ensinar uma app LUIS, tem de aprender sobre qual é a intenção (positiva) e o que não é (negativo). No LUIS, as declarações só podem ser positivas para uma única intenção. Quando uma expressão é adicionada a uma intenção, o LUIS automaticamente faz desse mesmo exemplo um exemplo negativo para todas as outras intenções.
* **Dados fora do âmbito da aplicação**: Se a sua aplicação verá expressões que se aleitem fora das suas intenções definidas, certifique-se de fornecer esses. Os exemplos que não são atribuídos a uma determinada intenção definida serão rotulados com a intenção **de Nenhum.**  É importante ter exemplos realistas para a intenção **de Nenhum** prever corretamente expressões que estão fora do âmbito das intenções definidas.

    Por exemplo, se estiver a criar um bot hr focado no tempo de licença e tiver três intenções:
    * agendar ou editar uma licença
    * perguntar sobre os dias de licença disponíveis
    * aprovar/desaprovar licença

    Você quer ter certeza de que tem expressões que cobrem ambas as intenções, mas também que cobrem potenciais expressões fora desse âmbito que a aplicação deve servir como estas:
    * `What are my medical benefits?`
    * `Who is my HR rep?`
    * `tell me a joke`
* **Exemplos raros**: A sua aplicação terá de ter exemplos raros, bem como exemplos comuns.  Se a sua aplicação nunca viu exemplos raros, não será capaz de identificá-los na produção. Se estiver a utilizar dados reais, poderá prever com maior precisão como a sua aplicação LUIS funcionará em produção.

### <a name="quality-instead-of-quantity"></a>Qualidade em vez de quantidade

Considere a qualidade dos seus dados existentes antes de adicionar mais dados.  Com o LUIS, estás a usar o Machine Teaching.  A combinação dos seus rótulos e as funcionalidades de machine learning que define é o que a sua aplicação LUIS utiliza.  Não depende apenas da quantidade de rótulos para fazer a melhor previsão.  A diversidade de exemplos e a sua representação do que a sua app LUIS verá na produção é a parte mais importante.

### <a name="preprocessing-data"></a>Dados de pré-processamento

Os seguintes passos de pré-processamento ajudarão a construir uma melhor app LUIS:

* **Remova os duplicados**: As expressões duplicadas não vão doer, mas também não ajudam, pelo que removê-las poupará tempo de rotulagem.
* Aplique o **mesmo pré-processo de aplicação de cliente**: Se a sua aplicação de cliente, que chama o ponto final da previsão LUIS, aplicar o processamento de dados em tempo de execução antes de enviar o texto para a LUIS, deverá treinar a app LUIS em dados que são tratados da mesma forma. Por exemplo, se a sua aplicação de cliente utilizar [bing Spell Check](../bing-spell-check/overview.md) para corrigir a ortografia nas inputs para LUIS, corrija as suas declarações de treino e teste antes de adicionar ao LUIS.
* **Não aplique novos processos**de limpeza que a aplicação do cliente não utiliza : Se a sua aplicação de cliente aceitar texto gerado por discursos diretamente sem qualquer limpeza, como gramática ou pontuação, as suas declarações precisam de refletir o mesmo, incluindo qualquer pontuação em falta e qualquer outro mal-reconhecimento que terá de prestar contas.
* **Não limpe os dados**: Não se livre de informações mal formadas que possa obter do reconhecimento da fala, das teclas acidentais ou do texto enevoado/mal escrito. Se a sua aplicação verá inputs como estes, é importante que seja treinado e testado neles. Adicione uma intenção de _entrada mal formada_ se não espera que a sua aplicação a compreenda. Marque estes dados para ajudar a sua aplicação LUIS a prever a resposta correta no prazo de execução. A sua aplicação ao cliente pode escolher uma resposta adequada a declarações ininteligíveis, tais como `Please try again` .

### <a name="labeling-data"></a>Dados de rotulagem

* **Texto de etiqueta como se estivesse correto**: As declarações de exemplo devem ter todas as formas de uma entidade rotulada. Isto inclui texto que é mal escrito, enevoado e mal traduzido.

### <a name="data-review-after-luis-app-is-in-production"></a>Análise de dados após app LUIS está em produção

[Reveja as declarações finais](luis-concept-review-endpoint-utterances.md) para monitorizar o tráfego real de expressão uma vez que tenha implementado uma aplicação para a produção.  Isto permite-lhe atualizar as suas declarações de treino com dados reais, o que irá melhorar a sua aplicação. Qualquer aplicação construída com dados de cenários de origem de multidões ou não real terá de ser melhorada com base no seu uso real.

## <a name="test-data-selection-for-batch-testing"></a>Testar a seleção de dados para testes de lote

Todos os princípios acima indicados para as declarações de treino aplicam-se às expressões que deve utilizar para o seu conjunto de [testes](luis-concept-batch-test.md). Garantir que a distribuição entre intenções e entidades espelha a distribuição real o mais próximo possível.

Não reutilize as declarações do seu conjunto de treino no seu conjunto de testes. Isto distorce indevidamente os seus resultados e não lhe dará a indicação certa de como a sua aplicação LUIS irá funcionar em produção.

Uma vez publicada a primeira versão da sua aplicação, deverá atualizar o seu conjunto de testes com expressões de tráfego real para garantir que o seu conjunto de testes reflete a sua distribuição de produção e poderá monitorizar o desempenho realista ao longo do tempo.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como o LUIS altera os seus dados antes da previsão](luis-concept-data-alteration.md)
