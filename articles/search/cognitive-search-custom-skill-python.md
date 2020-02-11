---
title: Exemplo de habilidade personalizada (Python)
titleSuffix: Azure Cognitive Search
description: Para desenvolvedores do Python, conheça as ferramentas e técnicas para criar uma habilidade personalizada usando o Azure Functions e o Visual Studio. Habilidades personalizadas contêm modelos definidos pelo usuário ou lógica que você pode adicionar a um pipeline de indexação aprimorado do ia no Azure Pesquisa Cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: e4f27e582e6b920e9de068fe2c3270af19ad8a17
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122419"
---
# <a name="example-create-a-custom-skill-using-python"></a>Exemplo: criar uma habilidade personalizada usando Python

Neste exemplo do Azure pesquisa cognitiva Qualifications, você aprenderá a criar uma habilidade personalizada da API Web usando Python e Visual Studio Code. O exemplo utiliza uma [Função Azure](https://azure.microsoft.com/services/functions/) que implementa a [interface de habilidades personalizadas.](cognitive-search-custom-skill-interface.md)

A habilidade personalizada é simples por design (ela concatena duas cadeias de caracteres) para que você possa se concentrar nas ferramentas e tecnologias usadas para o desenvolvimento de habilidades personalizadas no Python. Depois de ter uma habilidade simples, você pode ramificar com cenários mais complexos.

## <a name="prerequisites"></a>Pré-requisitos

+ Reveja a [interface de habilidade personalizada](cognitive-search-custom-skill-interface.md) para uma introdução na interface de entrada/saída que uma habilidade personalizada deve implementar.

+ Configure seu ambiente. Seguimos [este tutorial](https://docs.microsoft.com/azure/python/tutorial-vs-code-serverless-python-01) de ponta a ponta para configurar a função Azure sem servidor usando o Código de Estúdio Visual e as extensões Python. O tutorial o conduz pela instalação das seguintes ferramentas e componentes: 

  + [Python 3.75](https://www.python.org/downloads/release/python-375/)
  + [Visual Studio Code](https://code.visualstudio.com/)
  + [Extensão python para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Ferramentas centrais de funções azure](https://docs.microsoft.com/azure/azure-functions/functions-run-local#v2)
  + [Extensão das Funções do Azure para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>Criar uma Função do Azure

Este exemplo usa uma função do Azure para demonstrar o conceito de Hospedagem de uma API da Web, mas outras abordagens são possíveis. Desde que cumpra os requisitos de [interface para uma habilidade cognitiva,](cognitive-search-custom-skill-interface.md)a abordagem que toma é imaterial. O Azure Functions, no entanto, facilita a criação de uma habilidade personalizada.

### <a name="create-a-function-app"></a>Criar uma aplicação de função

O modelo do projeto das Funções do Azure no Visual Studio Code cria um projeto que pode ser publicado numa aplicação de funções no Azure. As aplicações de funções permitem-lhe agrupar funções como unidades lógicas para a gestão, implementação e partilha de recursos.

1. Em Visual Studio Code, pressione F1 para abrir a paleta de comandos. Na paleta de comando, procure e selecione `Azure Functions: Create new project...`.

1. Escolha um local de diretório para o seu espaço de trabalho do projeto e escolha **Select**.

    > [!NOTE]
    > Essas etapas foram projetadas para serem concluídas fora de um espaço de trabalho. Por esse motivo, não selecione uma pasta de projeto que faça parte de um espaço de trabalho.

1. Selecione um idioma para seu projeto de aplicativo de funções. Para este tutorial, selecione **Python**.
1. Selecione a versão do Python, (a versão 3.7.5 é suportada por Azure Functions)
1. Selecione um modelo para a primeira função do projeto. Selecione **o gatilho HTTP** para criar uma função ativada em HTTP na nova aplicação de função.
1. Forneça um nome de função. Neste caso, vamos usar a **Concatenator** 
1. Selecione **Função** como nível de Autorização. Isto significa que forneceremos uma [chave de função](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) para chamar o ponto final http da função. 
1. Selecione como você gostaria de abrir seu projeto. Para este passo, selecione **Adicionar ao espaço** de trabalho para criar a aplicação de funções no espaço de trabalho atual.

O Visual Studio Code cria o projeto da aplicação de funções numa nova área de trabalho. Este projeto contém os ficheiros de configuração [host.json](../azure-functions/functions-host-json.md) e [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file), bem como os ficheiros de projetos específico de idiomas. 

Uma nova função desencadeada pelo HTTP também é criada na pasta **Concatenator** do projeto da aplicação de funções. No seu interior haverá um ficheiro chamado "\_\_init__.py", com este conteúdo:

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

Agora vamos modificar este código para seguir a [interface de habilidade personalizada).](cognitive-search-custom-skill-interface.md) Modifique o código com o seguinte conteúdo:

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

O método **transform_value** executa uma operação num único disco. Você pode modificar o método para atender às suas necessidades específicas. Lembre-se de fazer qualquer validação de entrada necessária e retornar erros e avisos produzidos se a operação não pôde ser concluída para o registro.

### <a name="debug-your-code-locally"></a>Depurar seu código localmente

Visual Studio Code facilita a depuração do código. Prima 'F5' ou vá ao menu **Debug** e selecione **Iniciar depuração**.

Você pode definir qualquer ponto de interrupção no código pressionando ' F9 ' na linha de interesse.

Depois de iniciar a depuração, sua função será executada localmente. Você pode usar uma ferramenta como o postmaster ou o Fiddler para emitir a solicitação para o localhost. Observe o local do ponto de extremidade local na janela do terminal. 

## <a name="publish-your-function"></a>Publicar sua função

Quando estiver satisfeito com o comportamento da função, você poderá publicá-lo.

1. Em Visual Studio Code, pressione F1 para abrir a paleta de comandos. Na paleta de comando, procure e selecione **Implementar para app de função...** . 

1. Selecione a assinatura do Azure em que você deseja implantar seu aplicativo.

1. Selecione **+ Criar nova app de funções em Azure**

1. Insira um nome globalmente exclusivo para seu aplicativo de funções.

1. Selecione a versão do Python (o Python 3.7. x funciona para essa função).

1. Selecione um local para o novo recurso (por exemplo, oeste dos EUA 2).

Neste ponto, os recursos necessários serão criados em sua assinatura do Azure para hospedar a nova função do Azure no Azure. Aguarde pela conclusão da implementação. A janela saída mostrará o status do processo de implantação.

1. No [portal Azure,](https://portal.azure.com)navegue para **Todos os Recursos** e procure a função que publicou pelo seu nome. Se lhe deu o nome **de Concatenator,** selecione o recurso.

1. Clique no botão **URL da função .lt;/> Get Function** URL. Isso permitirá que você copie a URL para chamar a função.

## <a name="test-the-function-in-azure"></a>Testar a função no Azure

Agora que você tem a chave de host padrão, teste sua função da seguinte maneira:

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

Este exemplo deve produzir o mesmo resultado que você viu anteriormente ao executar a função no ambiente local.

## <a name="connect-to-your-pipeline"></a>Conectar-se ao seu pipeline

Agora que você tem uma nova habilidade personalizada, você pode adicioná-la ao seu Skill. O exemplo abaixo mostra como chamar a habilidade de concatenar o Título e o Autor do documento num único campo a que chamamos merged_title_author. Substitua `[your-function-url-here]` com o URL da sua nova Função Azure.

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
Parabéns! Você criou sua primeira habilidade personalizada. Agora você pode seguir o mesmo padrão para adicionar sua própria funcionalidade personalizada. Clique nos links a seguir para saber mais.

+ [Power Skills: um repositório de habilidades personalizadas](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Adicione uma habilidade personalizada a um oleoduto de enriquecimento de IA](cognitive-search-custom-skill-interface.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
+ [Criar Skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos enriquecidos](cognitive-search-output-field-mapping.md)
