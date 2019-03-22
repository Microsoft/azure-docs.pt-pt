---
title: Entidade Simple, a lista de frase
titleSuffix: Azure Cognitive Services
description: Neste tutorial, extrair dados de aprendidas de máquina de mensagens em fila de nome da tarefa de emprego de uma expressão com a entidade Simple. Para aumentar a precisão de extração, adicione uma lista de expressões de termos específicos da entidade simples.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: diberry
ms.openlocfilehash: b9ddd82d18b7f67f61e5c1589511ea6db4fa18a3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58100297"
---
# <a name="tutorial-extract-names-with-simple-entity-and-a-phrase-list"></a>Tutorial: Extrair nomes de entidade simple e uma lista de frase

Neste tutorial, irá extrair dados de aprendizagem automática do nome do cargo a partir de uma expressão com a entidade **Simple** (Simples). Para aumentar a precisão de extração, adicione uma lista de expressões de termos específicos da entidade simples.

A entidade simples deteta um único conceito de dados contido em palavras ou expressões.

**Neste tutorial, vai aprender a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Aplicação de exemplo de importação
> * Adicionar entidade simple 
> * Adicionar a lista de frase para impulsionar o sinal de palavras
> * Preparar 
> * Publicar 
> * Obter as intenções e as entidades do ponto final

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]


## <a name="simple-entity"></a>Entidade simples

Este tutorial adiciona uma nova entidade simples para extrair o nome do cargo. O objetivo da entidade simples nesta aplicação LUIS é ensinar ao LUIS o que é um nome de trabalho e onde pode ser encontrado numa expressão. A parte da expressão que corresponde ao nome do cargo pode variar de expressão para expressão com base na escolha de palavras e no comprimento da expressão. O LUIS precisa de exemplos de nomes de cargos em todas as intenções que utilizam nomes de cargos.  

A entidade simples é uma boa opção para este tipo de dados quando:

* Os dados são um único conceito.
* Os dados não são bem formatados, como uma expressão regular.
* Os dados não são comuns, como uma entidade pré-concebida do número de telefone ou de dados.
* Os dados não correspondem exatamente a uma lista de palavras conhecidas, como uma entidade de lista.
* Os dados não contêm outros itens de dados, como uma entidade composta ou hierárquica.

Considere as seguintes expressões de um chatbot:

|Expressão|Nome da tarefa passível de extração|
|:--|:--|
|Quero candidatar-me ao novo trabalho de contabilidade.|contabilidade|
|Submeter o meu currículo para o novo cargo de engenharia.|engenharia|
|Preencher a candidatura para o trabalho 123456|123456|

O nome do trabalho é difícil de determinar porque um nome pode ser um substantivo, verbo ou uma expressão de várias palavras. Por exemplo:

|Tarefas|
|--|
|engenheiro|
|engenheiro de software|
|engenheiro de software sénior|
|chefe da equipa de engenharia |
|controlador de tráfego aéreo|
|operador de veículos motorizados|
|motorista de ambulância|
|encarregado|
|operador de extrusora|
|mecânico afinador de máquinas|

Esta aplicação LUIS tem nomes de trabalhos em várias intenções. Ao identificar estas palavras nas expressões de todas as intenções, o LUIS aprende mais sobre o nome de um cargo e onde pode ser encontrado nas expressões.

Depois de as entidades serem marcadas nas expressões de exemplo, é importante adicionar uma lista de expressões para aumentar o sinal da entidade simples. Uma lista de expressões **não** é utilizada como uma correspondência exata e não precisa de ser todos os valores possíveis esperados. 

## <a name="import-example-app"></a>Aplicação de exemplo de importação

1.  Transfira e guarde o [ficheiro JSON da aplicação](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/intentonly.json) partir do tutorial de objetivos.

2. Importe o JSON para uma nova aplicação.

3. Na secção **Gerir**, no separador **Versões**, clone a versão e dê-lhe o nome `simple`. A clonagem é uma excelente forma de utilizar várias funcionalidades do LUIS sem afetar a versão original. Como o nome da versão é utilizado como parte da rota de URL, o nome não pode conter carateres que não sejam válidos num URL.

## <a name="mark-entities-in-example-utterances-of-an-intent"></a>Entidades de Mark em expressões de exemplo de um objetivo

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Na página **Intents** (Intenções), selecione a intenção **ApplyForJob**. 

1. Na expressão, `I want to apply for the new accounting job`, selecione `accounting`, introduza `Job` no campo superior do menu de pop-up e, em seguida, selecione **Create new entity** (Criar nova entidade) no menu de pop-up. 

    [![Captura de ecrã do LUIS, com a intenção de 'ApplyForJob' com criar passos de entidade realçados](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "captura de ecrã do LUIS, com a intenção de 'ApplyForJob' com criar passos de entidade realçados")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

1. Na janela de pop-up, verifique o nome e o tipo da entidade e selecione **Done** (Concluído).

    ![Criar um diálogo modal de pop-up de entidade simples com o nome do Trabalho e o tipo simples](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

1. Em expressões de com restantes, marcar as palavras relacionadas à tarefa com **tarefa** entidade ao selecionar a palavra ou frase, em seguida, selecionar **tarefa** no menu de pop-up. 

    [![Captura de ecrã do LUIS etiquetagem entidade de tarefa realçada](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "captura de ecrã do LUIS etiquetagem entidade de tarefa realçada")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)


## <a name="add-more-example-utterances-and-mark-entity"></a>Adicionar mais expressões de exemplo e marcar a entidade

Entidades simples precisam muitos exemplos para ter uma confiança elevada de predição. 
 
1. Adicione mais expressões e marque as palavras ou expressões de trabalho como entidade **Job** (Trabalho). 

    |Expressão|Entidade de trabalho|
    |:--|:--|
    |Estou a candidatar-me à receção de Gestor de Programas em I&D|Gestor de Programas|
    |Segue a minha candidatura a cozinheiro de linha.|cozinheiro de linha|
    |Envio em anexo o meu currículo para a vaga de conselheiro de acampamento.|conselheiro de acampamento|
    |Este é o meu CV. para assistente administrativo.|para assistente administrativo|
    |Quero candidatar-me à oferta de emprego de gestão em vendas.|gestão, vendas|
    |Este é o meu currículo para o novo cargo de contabilidade.|contabilidade|
    |Incluo a minha candidatura para barman.|barman|
    |Estou a enviar a minha candidatura para reparador de telhados e construtor civil.|reparador de telhados, construtor civil|
    |O meu CV para motorista de autocarro está aqui.|motorista de autocarro|
    |Sou enfermeira licenciada. Aqui está o meu currículo.|enfermeira licenciada|
    |Gostaria de submeter a minha documentação para o cargo de professor que vi no jornal.|professor|
    |Este é o meu CV. para a vaga de repositor de frutas e vegetais.|repositor|
    |Candidatar a trabalho de aplicação de mosaicos.|mosaicos|
    |Currículo anexado para arquiteto paisagista.|arquiteto paisagista|
    |Envio em anexo o meu curriculum vitae para professor de biologia.|professor de biologia|
    |Gostaria de candidatar-me ao cargo na área de fotografia.|fotografia|

## <a name="mark-job-entity-in-other-intents"></a>Entidade de tarefa de Mark em outros objetivos

1. Selecione **Intents** (Intenções) no menu esquerdo.

1. Selecione **GetJobInformation** na lista de intenções. 

1. As tarefas nas expressões de exemplo da etiqueta

    Se existirem mais expressões de exemplo numa intenção, a intenção de outro, essa intenção tem uma maior probabilidade de que está a ser o mais alto intext prevista. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Preparar a aplicação para que as alterações à intenção podem ser testadas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicar a aplicação, para que o modelo preparado é consultável do ponto final

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter previsão intenções e entidades do ponto final 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá para o final do URL no endereço e introduza `Here is my c.v. for the engineering job`. O último parâmetro querystring é `q`, a expressão **query**. Esta expressão não é igual a qualquer uma das expressões identificadas, pelo que é um bom teste e deve devolver as expressões `ApplyForJob`.

    ```json
    {
      "query": "Here is my c.v. for the engineering job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.98052007
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.98052007
        },
        {
          "intent": "GetJobInformation",
          "score": 0.03424581
        },
        {
          "intent": "None",
          "score": 0.0015820954
        }
      ],
      "entities": [
        {
          "entity": "engineering",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 34,
          "score": 0.668959737
        }
      ]
    }
    ```
    
    O LUIS encontrou a intenção correta, **ApplyForJob**, e extraiu a entidade correta, **Job**, com um valor de `engineering`.


## <a name="names-are-tricky"></a>Os nomes são complicados
A aplicação LUIS encontrou a intenção correta com um índice elevado de confiança e extraiu o nome do trabalho, mas os nomes são complicados. Experimente a expressão `This is the lead welder paperwork`.  

No seguinte JSON, o LUIS responde com a intenção correta, `ApplyForJob`, mas não extraiu o nome do trabalho `lead welder`. 

```json
{
  "query": "This is the lead welder paperwork",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.860295951
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.860295951
    },
    {
      "intent": "GetJobInformation",
      "score": 0.07265678
    },
    {
      "intent": "None",
      "score": 0.00482481951
    }
  ],
  "entities": []
}
```

Uma vez que um nome pode ser qualquer coisa, o LUIS prevê as entidades com maior exatidão se tiver uma lista de expressões e palavras para melhorar o sinal.

## <a name="to-boost-signal-of-the-job-related-words-add-a-phrase-list-of-job-related-words"></a>Para aumentar o sinal das palavras relacionados com tarefas, adicione uma lista de frase de palavras relacionados com tarefas

Abra o [tarefas-frase-list.csv](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) partir do repositório GitHub de exemplos do Azure. A lista é mais de 1000 palavras de tarefa e expressões. Dê uma vista de olhos à lista para encontrar palavras relacionadas com trabalho que são relevantes para si. Se as suas palavras ou expressões não estiverem na lista, adicione-as.

1. Na secção **Build** (Criar) da aplicação LUIS, selecione **Phrase lists** (Listas de expressões), que se encontra no menu **Improve app performance** (Melhorar o desempenho da aplicação).

1. Selecione **Create new phrase list** (Criar nova lista de expressões). 

1. Atribua à nova lista de expressões o nome `JobNames` e copie a lista de jobs-phrase-list.csv para a caixa de texto **Values** (Valores). Selecione Enter. 

    [![Captura de ecrã de criar a caixa de diálogo do novo frase lista pop-up](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "captura de ecrã de criar a caixa de diálogo do novo frase lista pop-up")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Se pretender mais palavras adicionadas à lista de frase, selecione **Recommand** , em seguida, reveja as novas **valores relacionados** e adicione todos os que são relevantes. 

    Certifique-se de manter os **estes valores são intercambiáveis** verificado uma vez que estes valores devem todos ser tratados como sinónimos para as tarefas. Saiba mais sobre intercambiáveis e noninterchangeable [frase conceitos de lista](luis-concept-feature.md#how-to-use-phrase-lists).

1. Selecione **Save** (Guardar) para ativar a lista de expressões.

    [![Captura de ecrã de criar a caixa de diálogo do novo frase lista pop-up com palavras na caixa de valores de lista de frase](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "captura de ecrã de criar a caixa de diálogo do novo frase lista pop-up com palavras na caixa de valores de lista de frase")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

1. Formar e publicar a aplicação novamente para utilizar a lista de frase.

1. Repita a consulta no ponto final com a mesma expressão: `This is the lead welder paperwork.`

    A resposta JSON inclui a entidade extraída:

    ```json
      {
      "query": "This is the lead welder paperwork.",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.983076453
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.983076453
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0120766377
        },
        {
          "intent": "None",
          "score": 0.00248388131
        }
      ],
      "entities": [
        {
          "entity": "lead welder",
          "type": "Job",
          "startIndex": 12,
          "endIndex": 22,
          "score": 0.8373154
        }
      ]
    }
    ```

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Objetivos sem tutorial de entidades](luis-quickstart-intents-only.md)
* [Entidade Simple](luis-concept-entity-types.md) informações concetuais
* [Lista de frase](luis-concept-feature.md) informações concetuais
* [Como dar formação](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, a aplicação Recursos Humanos utiliza uma entidade de aprendizagem automática simples para localizar nomes de cargos nas expressões. Como os nomes de cargos podem ter uma grande variedade de palavras ou expressões, a aplicação precisou de uma lista de expressões para melhorar as palavras dos nomes de cargos. 

> [!div class="nextstepaction"]
> [Adicionar uma entidade keyphrase criada previamente](luis-quickstart-intent-and-key-phrase.md)
