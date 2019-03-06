---
title: Fluxos de trabalho de moderação - Content Moderator
titlesuffix: Azure Cognitive Services
description: Utilize fluxos de trabalho com operações de tarefa a API de revisão para automatizar as revisões de humanos em loop com base em suas diretivas de conteúdo e os limiares.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 0a13d86afe3d395cb34f592b03c1eb9daa18076b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454861"
---
# <a name="automate-moderation-reviews-with-workflows"></a>Automatizar as revisões de moderação com fluxos de trabalho

O Content Moderator inclui as ferramentas e APIs para gerir os fluxos de trabalho. Utilizar fluxos de trabalho com o [operações da API tarefa rever](review-api.md) para automatizar a criação de revisão humana em loop com base em suas diretivas de conteúdo e os limiares.

A revisão de API oferece as seguintes opções para incluir a supervisão humana em seu processo de moderação de conteúdos:

1. O **tarefa** criação como uma etapa de revisão de operações para a partir de moderação assistida por computador e humanos.
1. O **rever** operações para humanos rever para a criação, fora a etapa de moderação.
1. O **fluxo de trabalho** operações para a gestão de fluxos de trabalho que automatizam a verificação com limiares para rever a criação.

Este artigo aborda os **fluxo de trabalho** operações. Leitura a [tarefas e as revisões de](review-api.md) descrição geral para saber mais sobre a moderação de conteúdos trabalhos e revisões.

Dar saída a **predefinição** fluxo de trabalho é a melhor maneira de começar a trabalhar com noções básicas sobre fluxos de trabalho no Content Moderator.

## <a name="your-first-workflow"></a>Seu primeiro fluxo de trabalho

Seu primeiro fluxo de trabalho vem agrupado com o seu [reveja a equipe de ferramenta](https://contentmoderator.cognitive.microsoft.com/). Inscreva-se se não o tiver feito isso já.

Navegue para o [reveja os fluxos de trabalho da ferramenta](Review-Tool-User-Guide/Workflows.md) ecrã no separador Definições. Verá um **predefinição** fluxo de trabalho conforme mostrado na imagem seguinte:

![Fluxos de trabalho do moderador de conteúdo](Review-Tool-User-Guide/images/2-workflows-1.png)

### <a name="open-the-default-workflow"></a>Abrir o fluxo de trabalho predefinida

Utilize o **editar** autónoma para abrir o fluxo de trabalho a página de edição, conforme mostrado na imagem seguinte: ![Fluxo de trabalho de padrão de moderador de conteúdo](images/default-workflow-listed.PNG)

### <a name="the-designer-view"></a>A exibição de design

Verá o **Designer** separador para o fluxo de trabalho. A exibição de design mostra os seguintes passos:

1. O **condição** para o fluxo de trabalho a ser avaliada. Neste caso, o fluxo de trabalho chama o Content Moderator imagem API e verifica se o `isAdult` saída é igual a `true`.
1. O **ação** ser executadas se a condição é cumprida. Neste caso, o fluxo de trabalho cria uma análise na ferramenta de revisão da `isAdult` saída é `true`.

![Conteúdo moderador padrão fluxo de trabalho - designer](images/default-workflow-designer.png)

### <a name="the-json-view"></a>A vista JSON

Selecione o **JSON** guia para ver a definição de JSON do fluxo de trabalho.

    {
        "Type": "Logic",
        If": {
            "ConnectorName": "moderator",
            "OutputName": "isAdult",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": []
        }
        ],
        "Type": "Actions"
        }
    }

### <a name="key-learning"></a>Aprendizado essencial

Fluxos de trabalho do Content Moderator são flexíveis e fáceis de configurar. Se o designer interno não cumprir os requisitos, escrever a definição de fluxo de trabalho no **JSON** formato. Em seguida, utilize a definição de JSON com o [API do fluxo de trabalho](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) para criar e gerir o fluxo de trabalho a partir da sua aplicação.

## <a name="define-a-custom-workflow"></a>Definir um fluxo de trabalho personalizado

Capacidades de fluxo de trabalho do Content Moderator permitem definir e usar fluxos de trabalho personalizados. Utilize o [reveja os fluxos de trabalho de ferramenta procedimentos](Review-Tool-User-Guide/Workflows.md) artigo definir um fluxo de trabalho personalizado. Este fluxo de trabalho usa a capacidade de OCR do Content Moderator para extrair texto de uma imagem de exemplo. Em seguida, cria uma análise na ferramenta de revisão.

### <a name="the-sample-image"></a>A imagem de exemplo

Guardar a [imagem de exemplo](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) sua unidade local. Esta imagem precisa para seu exercício.

### <a name="the-designer-view"></a>A exibição de design

Selecione o **Designer** separador e o [tutorial de criação de fluxo de trabalho](Review-Tool-User-Guide/Workflows.md) para definir um fluxo de trabalho personalizado. A imagem seguinte mostra o designer **condição** vista. Consulte o tutorial para ver o resto dos passos.

![O Content Moderator - condição de fluxo de trabalho](Review-Tool-User-Guide/images/ocr-workflow-step-2-condition.PNG)

### <a name="the-json-view"></a>A vista JSON

Selecione o **JSON** guia para ver a seguinte definição de JSON do fluxo de trabalho personalizado. Observe como o **If-Then** instruções na definição de JSON correspondem aos passos que definiu com a exibição de design.

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

### <a name="workflow-result"></a>Resultado do fluxo de trabalho

Depois de testar o fluxo de trabalho a partir do ecrã de fluxos de trabalho, a revisão seguinte é criada. Navegue para o **imagem** separador sob **rever** para ver a sua revisão.
O fluxo de trabalho criar a revisão porque a condição primária testado positivo a presença de texto. A revisão possível destacada o **`a`** etiqueta da revisão de imagem.

![O Content Moderator - saída de fluxo de trabalho simples](images/ocr-sample-image-workflow1.PNG)


## <a name="advanced-workflow-with-combination"></a>Fluxo de trabalho avançado com combinação

### <a name="the-sample-image"></a>A imagem de exemplo

Utilizar o mesmo [imagem de exemplo](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) que foi utilizado na secção anterior.

No entanto, desta vez, altere sua condição primária para uma combinação de duas verificações. Para além de verificar para texto, verifique se o texto tem qualquer linguagem inapropriada. O fluxo de trabalho cria uma revisão, se ele encontrar texto **e** Deteta linguagem inapropriada nela.

### <a name="the-designer-view"></a>A exibição de design

Para alterar o **condição** para um **combinação**, modificar o fluxo de trabalho. A imagem seguinte mostra a nova vista, que vê no designer.

![O Content Moderator - condição de modificação de fluxo de trabalho](images/ocr-workflow-2-designer.PNG)

### <a name="the-json-view"></a>A vista JSON

Selecione o **JSON** guia para ver a seguinte definição de JSON do fluxo de trabalho personalizado modificado. Observe como o **If-Then** instruções na definição de JSON correspondem aos passos novos adicionados ao fluxo de trabalho.

    {
        "Type": "Logic",
        "If": {
        "Left": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Right": {
            "ConnectorName": "moderator",
            "OutputName": "text.HasProfanity",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition",
            "AlternateInput": "moderator.ocrText"
            },
        "Combine": "AND",
        "Type": "Combine"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

    
### <a name="workflow-result"></a>Resultado do fluxo de trabalho

Depois de testar o fluxo de trabalho novamente, achar que não existem revisão é criado. Para confirmar a ausência de qualquer revisão, navegue para o **imagem** separador sob **rever**.
O fluxo de trabalho não tiver criado a revisão porque não conseguiu detetar linguagem inapropriada no texto extraído.

![O Content Moderator - saída de modificação de fluxo de trabalho](images/ocr-workflow-2-result.PNG)


## <a name="the-workflow-api"></a>O API do fluxo de trabalho

O [operações de fluxo de trabalho](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) fornecem a interface de programação para as capacidades de fluxo de trabalho. Criar fluxos de trabalho, obter os detalhes de fluxo de trabalho e atualizar as definições de fluxo de trabalho com a API de fluxo de trabalho.

### <a name="get-all-workflow-details"></a>Detalhes de fluxo de trabalho GET [todos]

O **fluxo de trabalho-Get** operação aceita as seguintes entradas:

- **team**: O ID da equipa que criou quando configurou seu [reveja a conta de ferramenta](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: O nome do seu fluxo de trabalho. Utilize `default` para começar.
- **Ocp-Apim-Subscription-Key**: Localizado no **definições** separador. Para obter mais informações, veja [Descrição geral](overview.md).

Se a operação for bem-sucedida, o **estado de resposta** é `200 OK` e o **conteúdo da resposta** caixa apresenta a definição de fluxo de trabalho no formato JSON.
Para saber mais, leia os [guia de introdução de consola de API de fluxo de trabalho](try-review-api-job.md).

### <a name="create-or-update-workflow"></a>Criar ou atualizar o fluxo de trabalho

A operação de criação e atualização permite a criação de fluxo de trabalho a partir da API.

O **fluxo de trabalho a criação ou atualização** operação aceita as seguintes entradas:

- **team**: O ID da equipa que criou quando configurou seu [reveja a conta de ferramenta](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: O nome do seu fluxo de trabalho. Utilize `default` para começar.
- **Ocp-Apim-Subscription-Key**: Localizado no **definições** separador. Para obter mais informações, veja [Descrição geral](overview.md).

Se a operação for bem-sucedida, o **estado de resposta** é `200 OK` e o **conteúdo da resposta** caixa apresenta `true`. Para saber mais, [faça o test drive do `Create` operação](try-review-api-job.md).

## <a name="next-steps"></a>Passos Seguintes

Para saber como criar fluxos de trabalho personalizados, consulte a [reveja o tutorial de fluxo de trabalho da ferramenta](Review-Tool-User-Guide/Workflows.md). 

Faça o test drive da [consola de API de fluxo de trabalho](try-review-api-job.md) e utilize os exemplos de código da REST API. 

Por último, utilize os fluxos de trabalho personalizados com o **tarefa** operações, conforme mostrado na [consola de API de tarefa](try-review-api-job.md) e o [início rápido para trabalhos .NET](moderation-jobs-quickstart-dotnet.md).
