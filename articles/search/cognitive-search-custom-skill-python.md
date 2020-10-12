---
title: Exemplo de habilidade personalizada (Python)
titleSuffix: Azure Cognitive Search
description: Para os desenvolvedores de Python, aprenda as ferramentas e técnicas para construir uma habilidade personalizada usando Azure Functions e Visual Studio. As habilidades personalizadas contêm modelos ou lógica definidos pelo utilizador que pode adicionar a um pipeline de indexação enriquecido em IA na Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 40e20ad4bab0275b44cd868521c7dc70dec52567
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936134"
---
# <a name="example-create-a-custom-skill-using-python"></a>Exemplo: Criar uma habilidade personalizada usando Python

Neste exemplo de skillset de pesquisa cognitiva Azure, você aprenderá a criar uma habilidade personalizada da API web usando Python e Visual Studio Code. O exemplo utiliza uma [Função Azure](https://azure.microsoft.com/services/functions/) que implementa a [interface de habilidade personalizada.](cognitive-search-custom-skill-interface.md)

A habilidade personalizada é simples por design (concatena duas cordas) para que você possa se concentrar nas ferramentas e tecnologias usadas para o desenvolvimento de habilidades personalizadas em Python. Uma vez que tenha sucesso com uma habilidade simples, pode ramificar-se com cenários mais complexos.

## <a name="prerequisites"></a>Pré-requisitos

+ Reveja a [interface de habilidade personalizada](cognitive-search-custom-skill-interface.md) para uma introdução na interface entrada/saída que uma habilidade personalizada deve implementar.

+ Crie o seu ambiente. Seguimos [este tutorial de ponta a ponta](/azure/python/tutorial-vs-code-serverless-python-01) para configurar a Função Azure sem servidor usando extensões visual Studio Code e Python. O tutorial conduz-o através da instalação das seguintes ferramentas e componentes: 

  + [Python 3.75](https://www.python.org/downloads/release/python-375/)
  + [Visual Studio Code](https://code.visualstudio.com/)
  + [Extensão python para código de estúdio visual](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Azure Functions Core Tools](../azure-functions/functions-run-local.md#v2)
  + [Extensão das Funções do Azure para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>Criar uma Função do Azure

Este exemplo usa uma Função Azure para demonstrar o conceito de hospedar uma API web, mas outras abordagens são possíveis. Desde que cumpra os requisitos de [interface para uma habilidade cognitiva,](cognitive-search-custom-skill-interface.md)a abordagem que toma é irrelevante. As Funções Azure, no entanto, facilitam a criação de uma habilidade personalizada.

### <a name="create-a-function-app"></a>Criar uma aplicação de funções

O modelo do projeto das Funções do Azure no Visual Studio Code cria um projeto que pode ser publicado numa aplicação de funções no Azure. As aplicações de funções permitem-lhe agrupar funções como unidades lógicas para a gestão, implementação e partilha de recursos.

1. No Código do Estúdio Visual, prima F1 para abrir a paleta de comando. Na paleta de comando, procure e selecione `Azure Functions: Create new project...` .

1. Escolha um local de diretório para o seu espaço de trabalho do projeto e escolha **Select**.

    > [!NOTE]
    > Estes passos foram concebidos para serem concluídos fora de um espaço de trabalho. Por esta razão, não selecione uma pasta de projeto que faça parte de um espaço de trabalho.

1. Selecione um idioma para o seu projeto de aplicação de função. Para este tutorial, selecione **Python**.
1. Selecione a versão Python (a versão 3.7.5 é suportada por Funções Azure)
1. Selecione um modelo para a primeira função do seu projeto. Selecione **o gatilho HTTP** para criar uma função DEtonada HTTP na nova aplicação de função.
1. Fornecer um nome de função. Neste caso, vamos usar **o Concatenator** 
1. Selecione **Função** como nível de Autorização. Isto significa que forneceremos uma [chave de função](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) para chamar o ponto final HTTP da função. 
1. Selecione como gostaria de abrir o seu projeto. Para este passo, **selecione Adicionar ao espaço de trabalho** para criar a aplicação de função no espaço de trabalho atual.

O Visual Studio Code cria o projeto da aplicação de funções numa nova área de trabalho. Este projeto contém os ficheiros de configuração [host.json](../azure-functions/functions-host-json.md) e [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file), bem como os ficheiros de projetos específico de idiomas. 

Uma nova função http desencadeada também é criada na pasta **Concatenator** do projeto de aplicação de função. Dentro dele haverá um ficheiro chamado " \_ \_ init__.py", com este conteúdo:

```py
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )

```

Agora vamos modificar esse código para seguir a [interface de habilidade personalizada).](cognitive-search-custom-skill-interface.md) Modificar o código com o seguinte conteúdo:

```py
import logging
import azure.functions as func
import json

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    try:
        body = json.dumps(req.get_json())
    except ValueError:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )
    
    if body:
        result = compose_response(body)
        return func.HttpResponse(result, mimetype="application/json")
    else:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )


def compose_response(json_data):
    values = json.loads(json_data)['values']
    
    # Prepare the Output before the loop
    results = {}
    results["values"] = []
    
    for value in values:
        output_record = transform_value(value)
        if output_record != None:
            results["values"].append(output_record)
    return json.dumps(results, ensure_ascii=False)

## Perform an operation on a record
def transform_value(value):
    try:
        recordId = value['recordId']
    except AssertionError  as error:
        return None

    # Validate the inputs
    try:         
        assert ('data' in value), "'data' field is required."
        data = value['data']        
        assert ('text1' in data), "'text1' field is required in 'data' object."
        assert ('text2' in data), "'text2' field is required in 'data' object."
    except AssertionError  as error:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Error:" + error.args[0] }   ]       
            })

    try:                
        concatenated_string = value['data']['text1'] + " " + value['data']['text2']  
        # Here you could do something more interesting with the inputs

    except:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Could not complete operation for record." }   ]       
            })

    return ({
            "recordId": recordId,
            "data": {
                "text": concatenated_string
                    }
            })
```

O **método transform_value** executa uma operação num único registo. Pode modificar o método para satisfazer as suas necessidades específicas. Lembre-se de fazer qualquer validação de entrada necessária e de devolver quaisquer erros e avisos produzidos se a operação não puder ser concluída para o registo.

### <a name="debug-your-code-locally"></a>Depurar o seu código localmente

O Código do Estúdio Visual facilita a depurar o código. Prima 'F5' ou vá ao menu **Debug** e selecione **Iniciar Debugging**.

Pode definir quaisquer pontos de rutura no código, atingindo 'F9' na linha de interesse.

Assim que começar a depurar, a sua função funcionará localmente. Você pode usar uma ferramenta como Carteiro ou Violinista para emitir o pedido para local local. Observe a localização do seu ponto final local na janela do terminal. 

## <a name="publish-your-function"></a>Publique a sua função

Quando estiver satisfeito com o comportamento da função, pode publicá-lo.

1. No Código do Estúdio Visual, prima F1 para abrir a paleta de comando. Na paleta de comando, procure e selecione **Implementar para a Aplicação de Função...**. 

1. Selecione a Subscrição Azure onde pretende implementar a sua aplicação.

1. Selecione **+ Criar nova aplicação de função em Azure**

1. Insira um nome globalmente único para a sua aplicação de função.

1. Selecione a versão Python (Python 3.7.x funciona para esta função).

1. Selecione uma localização para o novo recurso (por exemplo, West US 2).

Neste momento, serão criados os recursos necessários na sua subscrição do Azure para acolher a nova Função Azure em Azure. Aguarde pela conclusão da implementação. A janela de saída irá mostrar-lhe o estado do processo de implantação.

1. No [portal Azure,](https://portal.azure.com)navegue por **Todos os Recursos** e procure a função que publicou pelo seu nome. Se lhe deu o nome **de Concatenator,** selecione o recurso.

1. Clique no **botão URL</> Obter Função.** Isto permitir-lhe-á copiar o URL para ligar para a função.

## <a name="test-the-function-in-azure"></a>Teste a função em Azure

Agora que tem a chave de anfitrião predefinido, teste a sua função da seguinte forma:

```http
POST [Function URL you copied above]
```

### <a name="request-body"></a>Corpo do Pedido
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "text1":  "Hello",
                "text2":  "World"
            }
        },
        {
            "recordId": "e2",
            "data": "This is an invalid input"
        }
    ]
}
```

Este exemplo deve produzir o mesmo resultado que viu anteriormente ao executar a função no ambiente local.

## <a name="connect-to-your-pipeline"></a>Ligue-se ao seu oleoduto

Agora que tens uma nova habilidade personalizada, podes adicioná-la à tua habilidade. O exemplo abaixo mostra como chamar a habilidade de concatenar o título e o autor do documento num único campo a que chamamos merged_title_author. `[your-function-url-here]`Substitua-o pelo URL da sua nova Função Azure.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new search custom skill",
        "uri": "https://[your-function-url-here]",        
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "text1",
              "source": "/document/metadata_title"
            },
            {
              "name": "text2",
              "source": "/document/metadata_author"
            },
          ],
          "outputs": [
            {
              "name": "text",
              "targetName": "merged_title_author"
            }
          ]
      }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes
Parabéns! Criaste a tua primeira habilidade personalizada. Agora pode seguir o mesmo padrão para adicionar a sua própria funcionalidade personalizada. Clique nos seguintes links para saber mais.

+ [Power Skills: um repositório de habilidades personalizadas](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Adicione uma habilidade personalizada a um oleoduto de enriquecimento de IA](cognitive-search-custom-skill-interface.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)
+ [Criar Skillset (REST)](/rest/api/searchservice/create-skillset)
+ [Como mapear campos enriquecidos](cognitive-search-output-field-mapping.md)