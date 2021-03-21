---
title: Face JavaScript biblioteca cliente quickstart
description: Utilize a biblioteca do cliente Face para o JavaScript detetar rostos, encontrar semelhantes (procura facial por imagem), identificar rostos (pesquisa de reconhecimento facial) e migrar os dados do seu rosto.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/05/2020
ms.author: v-jawe
ms.openlocfilehash: b4a63f76cbcd9e98295f5edcf7ff2d06979e6556
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244931"
---
## <a name="quickstart-face-client-library-for-javascript"></a>Quickstart: Face client library for JavaScript

Começa com o reconhecimento facial utilizando a biblioteca do cliente Face para o JavaScript. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas. O serviço Face fornece-lhe acesso a algoritmos avançados para detetar e reconhecer rostos humanos em imagens.

Utilize a biblioteca do cliente Face para JavaScript para:

* [Detetar rostos numa imagem](#detect-faces-in-an-image)
* [Encontre rostos semelhantes](#find-similar-faces)
* [Criar um grupo de pessoas](#create-a-person-group)
* [Identificar um rosto](#identify-a-face)

[Documentação de referência](/javascript/api/@azure/cognitiveservices-face/)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-face)  |  [Pacote (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-face)  |  [Amostras](/samples/browse/?products=azure&term=face&languages=javascript)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A última versão de [Node.js](https://nodejs.org/en/)
* Assim que tiver a sua subscrição Azure, [Crie um recurso Face](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) no portal Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API face. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela. 

```console
mkdir myapp && cd myapp
```

Executar o `npm init` comando para criar uma aplicação de nó com um `package.json` ficheiro. 

```console
npm init
```

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente 

Instale os `ms-rest-azure` pacotes e `azure-cognitiveservices-face` NPM:

```console
npm install @azure/cognitiveservices-face @azure/ms-rest-js
```

O ficheiro da sua aplicação `package.json` será atualizado com as dependências.

Criar um ficheiro com o nome `index.js` e importar as seguintes bibliotecas:

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,]()que contém os exemplos de código neste arranque rápido.

```javascript
const msRest = require("@azure/ms-rest-js");
const Face = require("@azure/cognitiveservices-face");
const uuid = require("uuid/v4");
```

Crie variáveis para o ponto final e chave Azure do seu recurso. 

> [!IMPORTANT]
> Aceda ao portal do Azure. Se o recurso Face criado na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar a sua chave e ponto final na **página chave e ponto final** do recurso, sob **gestão de recursos.** 
>
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Consulte o artigo [de segurança](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) dos Serviços Cognitivos para obter mais informações.

```javascript
key = "<paste-your-face-key-here>"
endpoint = "<paste-your-face-endpoint-here>"
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características da biblioteca cliente Face .NET:

|Nome|Descrição|
|---|---|
|[FaceClient](/javascript/api/@azure/cognitiveservices-face/faceclient) | Esta classe representa a sua autorização para usar o serviço Face, e precisa dele para toda a funcionalidade Face. Você instantanea-o com as suas informações de subscrição, e você usá-lo para produzir instâncias de outras classes. |
|[Face](/javascript/api/@azure/cognitiveservices-face/face)|Esta classe lida com as tarefas básicas de deteção e reconhecimento que pode fazer com rostos humanos. |
|[Face detetado](/javascript/api/@azure/cognitiveservices-face/detectedface)|Esta classe representa todos os dados que foram detetados a partir de um único rosto numa imagem. Pode usá-lo para obter informações detalhadas sobre o rosto.|
|[FaceList](/javascript/api/@azure/cognitiveservices-face/facelist)|Esta classe gere as construções **FaceList** armazenadas em nuvem, que armazenam um conjunto variado de rostos. |
|[PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson)| Esta classe gere as construções **de Pessoas** armazenadas em nuvem, que armazenam um conjunto de rostos que pertencem a uma única pessoa.|
|[PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)| Esta classe gere as construções **do PersonGroup** armazenadas na nuvem, que armazenam um conjunto de objetos **pessoais** variados. |

## <a name="code-examples"></a>Exemplos de código

Os snippets de código abaixo mostram-lhe como fazer as seguintes tarefas com a biblioteca cliente Face para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Detetar rostos numa imagem](#detect-faces-in-an-image)
* [Encontre rostos semelhantes](#find-similar-faces)
* [Criar um grupo de pessoas](#create-a-person-group)
* [Identificar um rosto](#identify-a-face)

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js)que contém os exemplos de código neste arranque rápido.

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantiar um cliente com o seu ponto final e chave. Crie um objeto **[ApiKeyCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials)** com a sua chave e use-o com o seu ponto final para criar um objeto **[FaceClient.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/faceclient)**

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="credentials":::

## <a name="declare-global-values-and-helper-function"></a>Declarar valores globais e função de ajudante

São necessários os seguintes valores globais para várias das operações face que irá adicionar mais tarde.

O URL aponta para uma pasta de imagens de amostra. O UUID servirá como nome e ID para o PersonGroup que irá criar.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="globals":::

Utilizará a seguinte função para aguardar a conclusão da formação do PersonGroup.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="helpers":::

## <a name="detect-faces-in-an-image"></a>Detetar rostos numa imagem

### <a name="get-detected-face-objects"></a>Obtenha objetos faciais detetados

Crie um novo método para detetar rostos. O `DetectFaceExtract` método processa três das imagens no URL dado e cria uma lista de objetos **[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)** na memória do programa. A lista de valores **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** especifica quais as características a extrair. 

Em `DetectFaceExtract` seguida, o método analisa e imprime os dados de atributos para cada face detetada. Cada atributo deve ser especificado separadamente na chamada API de deteção facial original (na lista **[FaceAttributeType).](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** O código seguinte processa cada atributo, mas provavelmente só terá de usar um ou poucos.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="detect":::

> [!TIP]
> Também pode detetar rostos numa imagem local. Consulte os métodos [face](/javascript/api/@azure/cognitiveservices-face/face) tais como [DetectWithStreamAsync](/javascript/api/@azure/cognitiveservices-face/face#detectWithStream_msRest_HttpRequestBody__FaceDetectWithStreamOptionalParams__ServiceCallback_DetectedFace____).

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

O código seguinte requer um único rosto detetado (fonte) e procura um conjunto de outras faces (alvo) para encontrar fósforos (procura facial por imagem). Quando encontra uma correspondência, imprime o ID da face compatível com a consola.

### <a name="detect-faces-for-comparison"></a>Detetar rostos para comparação

Primeiro, defina um segundo método de deteção facial. É necessário detetar rostos em imagens antes de os comparar, e este método de deteção está otimizado para operações de comparação. Não extrai atributos faciais detalhados como na secção acima, e usa um modelo de reconhecimento diferente.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="recognize":::

### <a name="find-matches"></a>Encontrar fósforos

O método a seguir deteta rostos num conjunto de imagens-alvo e numa única imagem de origem. Depois, compara-as e encontra todas as imagens-alvo que são semelhantes à imagem de origem. Finalmente, imprime os detalhes da partida para a consola.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="find_similar":::

## <a name="identify-a-face"></a>Identificar um rosto

A operação [Identificar](/javascript/api/@azure/cognitiveservices-face/face#identify_string____FaceIdentifyOptionalParams__ServiceCallback_IdentifyResult____) tira uma imagem de uma pessoa (ou de várias pessoas) e procura encontrar a identidade de cada rosto na imagem (pesquisa de reconhecimento facial). Compara cada rosto detetado a um [PersonGroup,](/javascript/api/@azure/cognitiveservices-face/persongroup)uma base de dados de diferentes objetos [pessoais](/javascript/api/@azure/cognitiveservices-face/person) cujas características faciais são conhecidas. Para fazer a operação Identificar, primeiro tem de criar e treinar um [PersonGroup.](/javascript/api/@azure/cognitiveservices-face/persongroup)

### <a name="add-faces-to-person-group"></a>Adicionar rostos ao grupo de pessoas

Crie a seguinte função para adicionar rostos ao [Grupo de Pessoas](/javascript/api/@azure/cognitiveservices-face/persongroup).

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="add_faces":::

### <a name="wait-for-training-of-person-group"></a>Aguarde a formação do grupo de pessoas

Crie a seguinte função de ajudante para aguardar que o grupo de pessoas termine o treino.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="wait_for_training":::

### <a name="create-a-person-group"></a>Criar um grupo de pessoas

O seguinte código:
- Cria um [Grupo de Pessoas](/javascript/api/@azure/cognitiveservices-face/persongroup)
- Adiciona rostos ao grupo de pessoas, `AddFacesToPersonGroup` chamando, que definiu anteriormente.
- Treina o grupo de pessoas.
- Identifica os rostos no grupo de pessoas.

Este grupo **Pessoa** e os seus objetos **Pessoas** associados estão agora prontos para serem utilizados nas operações de Verificação, Identificação ou Grupo.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="identify":::

> [!TIP]
> Também pode criar um **PersonGroup a** partir de imagens locais. Consulte os métodos [PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson) tais como [AddFaceFromStream](/javascript/api/@azure/cognitiveservices-face/persongroupperson#addFaceFromStream_string__string__msRest_HttpRequestBody__Models_PersonGroupPersonAddFaceFromStreamOptionalParams_).

## <a name="main"></a>Principal

Finalmente, crie a `main` função e chame-a.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="main":::

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `node` comando no seu ficheiro quickstart.

```console
node index.js
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a biblioteca de clientes Face para o JavaScript para fazer tarefas de reconhecimento facial de base. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência API face (JavaScript)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/)

* [O que é o serviço Face?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js).
