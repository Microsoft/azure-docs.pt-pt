---
title: Teste para DevOps para apps LUIS
description: Como testar a sua aplicação de Compreensão linguística (LUIS) num ambiente DevOps.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/3/2020
ms.openlocfilehash: cd2fd8dc8c10864089b198db1ca1089f994a3ffb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788456"
---
# <a name="testing-for-luis-devops"></a>Teste para LUIS DevOps

Os engenheiros de software que estão a desenvolver uma aplicação de Compreensão de Idiomas (LUIS) podem aplicar práticas de DevOps em torno do [controlo de origem,](luis-concept-devops-sourcecontrol.md) [construções automatizadas,](luis-concept-devops-automation.md) [testes](luis-concept-devops-testing.md)e [gestão de lançamentos](luis-concept-devops-automation.md#release-management) seguindo estas orientações.

Em metodologias ágeis de desenvolvimento de software, o teste desempenha um papel integral na construção de software de qualidade. Todas as alterações significativas a uma aplicação LUIS devem ser acompanhadas por testes concebidos para testar a nova funcionalidade que o desenvolvedor está a construir na app. Estes testes são verificados no seu repositório de código fonte juntamente com a `.lu` fonte da sua aplicação LUIS. A implementação da alteração termina quando a app satisfaz os testes.

Os testes são uma parte crítica dos fluxos de [trabalho ci/CD](luis-concept-devops-automation.md). Quando as alterações a uma aplicação LUIS são propostas num pedido de pull (PR) ou após a fusão das alterações no seu ramo principal, então os fluxos de trabalho do CI devem executar os testes para verificar se as atualizações não causaram regressões.

## <a name="how-to-do-unit-testing-and-batch-testing"></a>Como fazer testes de unidade e testes de lote

Existem dois tipos diferentes de testes para uma aplicação LUIS que você precisa realizar em fluxos de trabalho de integração contínua:

- **Testes de unidade** - Testes relativamente simples que verificam a funcionalidade chave da sua aplicação LUIS. Um teste de unidade passa quando a intenção esperada e as entidades esperadas são devolvidas para uma determinada declaração de teste. Todos os testes de unidade devem passar para que o teste termine com sucesso.  
Este tipo de testes é semelhante aos [testes interativos](./luis-concept-test.md) que pode fazer no [portal LUIS.](https://www.luis.ai/)

- **Testes em** lote - O teste de lote é um teste abrangente no seu modelo treinado atual para medir o seu desempenho. Ao contrário dos testes de unidade, os testes de lote não são testes | falha. A expectativa com os testes de lote não é que todos os testes devolvam as intenções esperadas e as entidades esperadas. Em vez disso, um teste de lote ajuda-o a ver a precisão de cada intenção e entidade na sua aplicação e ajuda-o a comparar com o tempo à medida que faz melhorias.  
Este tipo de teste é o mesmo que o [teste batch](./luis-how-to-batch-test.md) que você pode realizar interativamente no portal LUIS.

Pode utilizar testes unitários desde o início do seu projeto. O teste de lote só é realmente de valor depois de ter desenvolvido o esquema da sua app LUIS e estiver a trabalhar para melhorar a sua precisão.

Para testes de unidade e testes de lote, certifique-se de que as suas expressões de teste são mantidas separadas das suas expressões de treino. Se testar os mesmos dados que treina, terá a falsa impressão de que a sua aplicação está a ter um bom desempenho quando está apenas a adaptar-se aos dados de teste. Os testes devem ser invisíveis pelo modelo para testar o quão bem está a generalizar.

### <a name="writing-tests"></a>Testes de escrita

Quando escreve um conjunto de testes, para cada teste é necessário definir:

* Expressão de teste
* Intenção esperada
* Entidades esperadas.

Utilize a [sintaxe do ficheiro de lote](./luis-how-to-batch-test.md#batch-syntax-template-for-intents-with-entities) LUIS para definir um grupo de testes num ficheiro com formato JSON. Por exemplo:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities":
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

Algumas ferramentas de teste, como [a NLU. Os DevOps](https://github.com/microsoft/NLU.DevOps) também suportam ficheiros de teste formatados pelo LUDown.

#### <a name="designing-unit-tests"></a>Conceber testes de unidade

Os testes de unidade devem ser concebidos para testar a funcionalidade central da sua aplicação LUIS. Em cada iteração, ou sprint, do desenvolvimento da sua aplicação, deverá escrever um número suficiente de testes para verificar se a funcionalidade chave que está a implementar na iteração está a funcionar corretamente.

Em cada teste de unidade, para uma determinada expressão de teste, pode:

* Teste que a intenção correta é devolvida
* Teste que as entidades 'chave' - aquelas que são fundamentais para a sua solução - estão a ser devolvidas.
* Teste que a [pontuação de previsão](./luis-concept-prediction-score.md) para intenção e entidades excede um limiar que você define. Por exemplo, pode decidir que só considerará que um teste passou se a pontuação de previsão para a intenção e para as suas entidades-chave exceder 0,75.

Nos testes unitários, é uma boa ideia testar que as suas entidades-chave foram devolvidas na resposta de previsão, mas para ignorar quaisquer falsos positivos. *Falsos positivos* são entidades que se encontram na resposta de previsão mas que não estão definidas nos resultados esperados para o seu teste. Ao ignorar falsos positivos, torna-se menos onerosa para os testes de unidade de autor, permitindo-lhe ainda focar-se em testar que os dados que são fundamentais para a sua solução estão a ser devolvidos numa resposta de previsão.

> [!TIP]
> A [NLU. A ferramenta DevOps](https://github.com/microsoft/NLU.DevOps) suporta todas as suas necessidades de testes LUIS. O `compare` comando quando utilizado no modo de teste [unitário](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) irá afirmar que todos os testes passam, e ignorará resultados falsos positivos para entidades que não estão rotuladas nos resultados esperados.

#### <a name="designing-batch-tests"></a>Conceber testes de lote

Os conjuntos de testes de lote devem conter um grande número de casos de teste, projetados para testar em todas as intenções e todas as entidades na sua app LUIS. Consulte [os testes de lote no portal LUIS](./luis-how-to-batch-test.md) para obter informações sobre a definição de um conjunto de testes de lote.

### <a name="running-tests"></a>Testes de execução

O portal LUIS oferece funcionalidades para ajudar nos testes interativos:

* [**O teste interativo**](./luis-concept-test.md) permite-lhe submeter uma amostra de expressão e obter uma resposta de intenções e entidades reconhecidas pela LUIS. Verifique o sucesso do teste através de uma inspeção visual.

* [**O teste do lote**](./luis-how-to-batch-test.md) utiliza um ficheiro de teste de lote como entrada para validar a sua versão ativa treinada para medir a sua precisão de previsão. Um teste de lote ajuda-o a ver a precisão de cada intenção e entidade na sua versão ativa, apresentando resultados com um gráfico.

#### <a name="running-tests-in-an-automated-build-workflow"></a>Executando testes num fluxo de trabalho de construção automatizada

As funcionalidades de teste interativo no portal LUIS são úteis, mas para devOps, os testes automatizados realizados num fluxo de trabalho CI/CD trazem certos requisitos:

* As ferramentas de teste devem funcionar num passo de fluxo de trabalho num servidor de construção. Isto significa que as ferramentas devem ser capazes de funcionar na linha de comando.
* As ferramentas de ensaio devem ser capazes de executar um grupo de testes contra um ponto final e verificar automaticamente os resultados esperados em função dos resultados reais.
* Se os testes falharem, as ferramentas de teste devem devolver um código de estado para parar o fluxo de trabalho e "falhar a construção".

A LUIS não oferece uma ferramenta de linha de comando ou uma API de alto nível que oferece estas funcionalidades. Recomendamos que utilize o [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) ferramenta para executar testes e verificar resultados, tanto na linha de comando como durante testes automatizados dentro de um fluxo de trabalho CI/CD.

As capacidades de teste que estão disponíveis no portal LUIS não requerem um ponto final publicado e fazem parte das capacidades de autoria do LUIS. Quando estiver a implementar testes num fluxo de trabalho automatizado de construção, tem de publicar a versão da aplicação LUIS para ser testada num ponto final para que ferramentas de teste como a NLU. Os DevOps podem enviar pedidos de previsão como parte dos testes.

> [!TIP]
> * Se estiver a implementar a sua própria solução de teste e código de escrita para enviar declarações de teste para um ponto final, lembre-se que se estiver a utilizar a chave de autoria LUIS, a taxa de transação permitida está limitada a 5TPS. Ou acelera a taxa de envio ou usa uma chave de previsão.
> * Ao enviar consultas de teste para um ponto final, lembre-se de utilizar `log=false` na sequência de consulta do seu pedido de previsão. Isto garante que as suas declarações de teste não são registadas pela LUIS e acabam na lista de comentários de comentários de ponto final apresentada pela funcionalidade de [aprendizagem ativa](./luis-concept-review-endpoint-utterances.md) LUIS e, consequentemente, adicionadas acidentalmente às declarações de treino da sua app.

#### <a name="running-unit-tests-at-the-command-line-and-in-cicd-workflows"></a>Testes de unidade de execução na linha de comando e em fluxos de trabalho CI/CD

Podes usar a [NLU. Pacote DevOps](https://github.com/microsoft/NLU.DevOps) para fazer testes na linha de comando:

* Usa a NLU. DevOps [comando de teste](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) para submeter testes de um ficheiro de teste para um ponto final e para capturar os resultados reais da previsão em um ficheiro.
* Usa a NLU. Os DevOps [comparam](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) o comando para comparar os resultados reais com os resultados esperados definidos no ficheiro de teste de entrada. O `compare` comando gera saída de teste NUnit, e quando utilizado no modo de teste de [unidade](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) por utilização da `--unit-test` bandeira, irá afirmar que todos os testes passam.

### <a name="running-batch-tests-at-the-command-line-and-in-cicd-workflows"></a>Executando testes de lote na linha de comando e em fluxos de trabalho CI/CD

Também pode usar o NLU. Pacote DevOps para fazer testes de lote na linha de comando.

* Usa a NLU. DevOps [comando de teste](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) para submeter testes de um ficheiro de teste para um ponto final e para capturar os resultados reais da previsão em um ficheiro, tal como nos testes de unidade.
* Usa a NLU. DevOps [comparam comando](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) no [modo de teste](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#performance-test-mode) de desempenho para medir o desempenho da sua app Também pode comparar o desempenho da sua aplicação com um benchmark de desempenho de base, por exemplo, os resultados do mais recente compromisso com o lançamento principal ou atual. No modo de teste de desempenho, o `compare` comando gera resultados de teste nUnit e [de teste](./luis-glossary.md#batch-test) de lote no formato JSON.

## <a name="luis-non-deterministic-training-and-the-effect-on-testing"></a>Luis formação não determinística e o efeito nos testes

Quando a LUIS está a formar um modelo, como uma intenção, precisa de dados positivos - as expressões de formação etiquetadas que forneceu para treinar a app para o modelo - e dados negativos - dados que *não* são exemplos válidos da utilização desse modelo. Durante o treino, a LUIS constrói os dados negativos de um modelo a partir de todos os dados positivos que forneceu para os outros modelos, mas em alguns casos que podem produzir um desequilíbrio de dados. Para evitar este desequilíbrio, a LUIS analisa um subconjunto dos dados negativos de forma não determinística para otimizar para um conjunto de treino mais equilibrado, melhor desempenho do modelo e tempo de treino mais rápido.

O resultado desta formação não determinística é que você pode obter uma resposta de previsão ligeiramente [diferente entre diferentes sessões de treino](./luis-concept-prediction-score.md), geralmente para intenções e/ou entidades onde a [pontuação](./luis-concept-prediction-score.md) de previsão não é alta.

Se pretender desativar a formação não determinística para as versões de aplicações LUIS que está a construir para efeitos de teste, utilize as [definições de versão API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com a `UseAllTrainingData` definição definida para `true` .

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a implementação de fluxos de trabalho ci/CD](luis-concept-devops-automation.md)
* Saiba como [implementar DevOps para LUIS com GitHub](luis-how-to-devops-with-github.md)