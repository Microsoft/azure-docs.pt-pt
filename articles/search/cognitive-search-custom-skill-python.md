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
ms.openlocfilehash: 06a247c9e65ce386034a50650e46994bbbe9074a
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76152175"
---
# <a name="example-create-a-custom-skill-using-python"></a>Exemplo: criar uma habilidade personalizada usando Python

Neste exemplo do Azure pesquisa cognitiva Qualifications, você aprenderá a criar uma habilidade personalizada da API Web usando Python e Visual Studio Code. O exemplo usa uma [função do Azure](https://azure.microsoft.com/services/functions/) que implementa a [interface de habilidade personalizada](cognitive-search-custom-skill-interface.md).

A habilidade personalizada é simples por design (ela concatena duas cadeias de caracteres) para que você possa se concentrar nas ferramentas e tecnologias usadas para o desenvolvimento de habilidades personalizadas no Python. Depois de ter uma habilidade simples, você pode ramificar com cenários mais complexos.

## <a name="prerequisites"></a>Pré-requisitos

+ Examine a [interface de habilidade personalizada](cognitive-search-custom-skill-interface.md) para obter uma introdução à interface de entrada/saída que deve ser implementada por uma habilidade personalizada.

+ Configure seu ambiente. Seguimos [este tutorial de ponta a ponta](https://docs.microsoft.com/azure/python/tutorial-vs-code-serverless-python-01) para configurar a função do Azure sem servidor usando as extensões Visual Studio Code e Python. O tutorial o conduz pela instalação das seguintes ferramentas e componentes: 

  + [Python 3,75](https://www.python.org/downloads/release/python-375/)
  + [Visual Studio Code](https://code.visualstudio.com/)
  + [Extensão do Python para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Azure Functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local#v2)
  + [Extensão das Funções do Azure para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>Criar uma Função do Azure

Este exemplo usa uma função do Azure para demonstrar o conceito de Hospedagem de uma API da Web, mas outras abordagens são possíveis. Desde que você atenda aos [requisitos de interface de uma habilidade cognitiva](cognitive-search-custom-skill-interface.md), a abordagem que você tomar é imprima. O Azure Functions, no entanto, facilita a criação de uma habilidade personalizada.

### <a name="create-a-function-app"></a>Criar uma aplicação de função

O modelo do projeto das Funções do Azure no Visual Studio Code cria um projeto que pode ser publicado numa aplicação de funções no Azure. As aplicações de funções permitem-lhe agrupar funções como unidades lógicas para a gestão, implementação e partilha de recursos.

1. Em Visual Studio Code, pressione F1 para abrir a paleta de comandos. Na paleta de comandos, procure e selecione `Azure Functions: Create new project...`.

1. Escolha um local de diretório para o espaço de trabalho do projeto e escolha **selecionar**.

    > [!NOTE]
    > Essas etapas foram projetadas para serem concluídas fora de um espaço de trabalho. Por esse motivo, não selecione uma pasta de projeto que faça parte de um espaço de trabalho.

1. Selecione um idioma para seu projeto de aplicativo de funções. Para este tutorial, selecione **Python**.
1. Selecione a versão do Python, (a versão 3.7.5 é suportada por Azure Functions)
1. Selecione um modelo para a primeira função do projeto. Selecione **gatilho http** para criar uma função disparada por http no novo aplicativo de funções.
1. Forneça um nome de função. Nesse caso, vamos usar o **concatenador** 
1. Selecione **função** como o nível de autorização. Isso significa que forneceremos uma [chave de função](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) para chamar o ponto de extremidade http da função. 
1. Selecione como você gostaria de abrir seu projeto. Para esta etapa, selecione **Adicionar ao espaço de trabalho** para criar o aplicativo de funções no espaço de trabalho atual.

O Visual Studio Code cria o projeto da aplicação de funções numa nova área de trabalho. Este projeto contém os ficheiros de configuração [host.json](../azure-functions/functions-host-json.md) e [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file), bem como os ficheiros de projetos específico de idiomas. 

Uma nova função disparada por HTTP também é criada na pasta **concatenador** do projeto de aplicativo de funções. Dentro dele, haverá um arquivo chamado "\__init__. py", com este conteúdo:

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

Agora, vamos modificar esse código para seguir a [interface de habilidade personalizada](cognitive-search-custom-skill-interface.md). Modifique o código com o seguinte conteúdo:

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

O método **transform_value** executa uma operação em um único registro. Você pode modificar o método para atender às suas necessidades específicas. Lembre-se de fazer qualquer validação de entrada necessária e retornar erros e avisos produzidos se a operação não pôde ser concluída para o registro.

### <a name="debug-your-code-locally"></a>Depurar seu código localmente

Visual Studio Code facilita a depuração do código. Pressione ' F5 ' ou vá para o menu **depurar** e selecione **Iniciar Depuração**.

Você pode definir qualquer ponto de interrupção no código pressionando ' F9 ' na linha de interesse.

Depois de iniciar a depuração, sua função será executada localmente. Você pode usar uma ferramenta como o postmaster ou o Fiddler para emitir a solicitação para o localhost. Observe o local do ponto de extremidade local na janela do terminal. 

## <a name="publish-your-function"></a>Publicar sua função

Quando estiver satisfeito com o comportamento da função, você poderá publicá-lo.

1. Em Visual Studio Code, pressione F1 para abrir a paleta de comandos. Na paleta de comandos, procure e selecione **implantar em aplicativo de funções...** . 

1. Selecione a assinatura do Azure em que você deseja implantar seu aplicativo.

1. Selecione **+ criar novo aplicativo de funções no Azure**

1. Insira um nome globalmente exclusivo para seu aplicativo de funções.

1. Selecione a versão do Python (o Python 3.7. x funciona para essa função).

1. Selecione um local para o novo recurso (por exemplo, oeste dos EUA 2).

Neste ponto, os recursos necessários serão criados em sua assinatura do Azure para hospedar a nova função do Azure no Azure. Aguarde pela conclusão da implementação. A janela saída mostrará o status do processo de implantação.

1. Na [portal do Azure](https://portal.azure.com), navegue até **todos os recursos** e procure a função que você publicou por seu nome. Se você tiver nomeado **concatenador**de ti, selecione o recurso.

1. Clique no botão **</> URL da função Get** . Isso permitirá que você copie a URL para chamar a função.

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

Agora que você tem uma nova habilidade personalizada, você pode adicioná-la ao seu Skill. O exemplo a seguir mostra como chamar a habilidade para concatenar o título e o autor do documento em um único campo que chamamos de merged_title_author. Substitua `[your-function-url-here]` pela URL do seu novo Azure function.

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

+ [Habilidades de energia: um repositório de habilidades personalizadas](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Adicionar uma habilidade personalizada a um pipeline de enriquecimento de ia](cognitive-search-custom-skill-interface.md)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Criar conconhecimento (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos aprimorados](cognitive-search-output-field-mapping.md)
