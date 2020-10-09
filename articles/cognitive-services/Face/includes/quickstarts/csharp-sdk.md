---
title: Início rápido da biblioteca de clientes Face .NET
description: Utilize a biblioteca cliente Face para .NET detetar rostos, encontrar semelhantes (procura facial por imagem), identificar rostos (pesquisa de reconhecimento facial) e migrar os dados do seu rosto.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 09/17/2020
ms.author: pafarley
ms.openlocfilehash: 6ef0791eeec169bb925b8f667523203beaacdd2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91859707"
---
Começa com o reconhecimento facial utilizando a biblioteca do cliente Face para .NET. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas. O serviço Face fornece-lhe acesso a algoritmos avançados para detetar e reconhecer rostos humanos em imagens.

Utilize a biblioteca cliente Face para .NET para:

* [Detetar rostos numa imagem](#detect-faces-in-an-image)
* [Encontre rostos semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identificar um rosto](#identify-a-face)

[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1)  |  [Amostras](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Pré-requisitos

* A versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" crie um recurso Face crie um recurso Face no portal "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API face. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* Depois de obter uma chave e ponto final, [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para o URL chave e ponto final, nomeado `FACE_SUBSCRIPTION_KEY` `FACE_ENDPOINT` e, respectivamente.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Crie uma nova aplicação .NET Core no seu editor preferido ou IDE. 

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `face-quickstart` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: *Program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

```dotnetcli
dotnet build
```

A saída de construção não deve conter avisos ou erros. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

A partir do diretório do projeto, abra o ficheiro *Program.cs* no seu editor preferido ou IDE. Adicione as `using` seguintes diretivas:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

No método da `Main` aplicação, crie variáveis para o ponto final e chave Azure do seu recurso.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Dentro do diretório de aplicações, instale a biblioteca cliente Face para .NET com o seguinte comando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
```

Se estiver a utilizar o Visual Studio IDE, a biblioteca do cliente está disponível como um pacote NuGet transferível.

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características da biblioteca cliente Face .NET:

|Nome|Descrição|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Esta classe representa a sua autorização para usar o serviço Face, e precisa dele para toda a funcionalidade Face. Você instantanea-o com as suas informações de subscrição, e você usá-lo para produzir instâncias de outras classes. |
|[Operações faciais](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Esta classe lida com as tarefas básicas de deteção e reconhecimento que pode fazer com rostos humanos. |
|[Face detetado](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Esta classe representa todos os dados que foram detetados a partir de um único rosto numa imagem. Pode usá-lo para obter informações detalhadas sobre o rosto.|
|[Operações FaceList](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Esta classe gere as construções **FaceList** armazenadas em nuvem, que armazenam um conjunto variado de rostos. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Esta classe gere as construções **de Pessoas** armazenadas em nuvem, que armazenam um conjunto de rostos que pertencem a uma única pessoa.|
|[PersonGroupOperações](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Esta classe gere as construções **do PersonGroup** armazenadas na nuvem, que armazenam um conjunto de objetos **pessoais** variados. |
|[Operações Shapshot](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Esta classe gere a funcionalidade Snapshot. Pode usá-lo para guardar temporariamente todos os seus dados face baseados na nuvem e migrar esses dados para uma nova subscrição do Azure. |

## <a name="code-examples"></a>Exemplos de código

Os snippets de código abaixo mostram-lhe como fazer as seguintes tarefas com a biblioteca cliente Face para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Detetar rostos numa imagem](#detect-faces-in-an-image)
* [Encontre rostos semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identificar um rosto](#identify-a-face)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este quickstart assume que [criou variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a sua chave face e ponto final, nomeado `FACE_SUBSCRIPTION_KEY` e `FACE_ENDPOINT` .

Num novo método, instantaneamente um cliente com o seu ponto final e chave. Crie um objeto **[ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials?view=azure-dotnet)** com a sua chave e use-o com o seu ponto final para criar um objeto **[FaceClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

Provavelmente vai querer chamar este método no `Main` método.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_client)]

### <a name="declare-helper-fields"></a>Declarar campos de ajuda

São necessários os seguintes campos para várias das operações face que irá adicionar mais tarde. Na raiz da sua classe, defina a seguinte cadeia de URL. Este URL aponta para uma pasta de imagens de amostra.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

Defina as cordas para apontar para os diferentes tipos de modelos de reconhecimento. Mais tarde, poderá especificar qual o modelo de reconhecimento que pretende utilizar para deteção facial. Consulte [Especificar um modelo de reconhecimento](../../Face-API-How-to-Topics/specify-recognition-model.md) para obter informações sobre estas opções.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Detetar rostos numa imagem

Adicione a chamada de método a seguir ao seu método **principal.** Vai definir o método a seguir. A operação de Deteção final levará um objeto **[FaceClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** um URL de imagem e um modelo de reconhecimento.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Obtenha objetos faciais detetados

No bloco de código seguinte, o `DetectFaceExtract` método deteta rostos em três das imagens no URL dado e cria uma lista de objetos **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** na memória do programa. A lista de valores **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** especifica quais as características a extrair. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Mostrar dados faciais detetados

O resto do `DetectFaceExtract` método analisa e imprime os dados de atributos para cada face detetada. Cada atributo deve ser especificado separadamente na chamada API de deteção facial original (na lista **[FaceAttributeType).](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** O código seguinte processa cada atributo, mas provavelmente só terá de usar um ou poucos.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

O código seguinte requer um único rosto detetado (fonte) e procura um conjunto de outras faces (alvo) para encontrar fósforos (procura facial por imagem). Quando encontra uma correspondência, imprime o ID da face compatível com a consola.

### <a name="detect-faces-for-comparison"></a>Detetar rostos para comparação

Primeiro, defina um segundo método de deteção facial. É necessário detetar rostos em imagens antes de os comparar, e este método de deteção está otimizado para operações de comparação. Não extrai atributos faciais detalhados como na secção acima, e usa um modelo de reconhecimento diferente.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Encontrar fósforos

O método a seguir deteta rostos num conjunto de imagens-alvo e numa única imagem de origem. Depois, compara-as e encontra todas as imagens-alvo que são semelhantes à imagem de origem.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Imprimir fósforos

O seguinte código imprime os detalhes da correspondência para a consola:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>Identificar um rosto

A operação Identificar tira uma imagem de uma pessoa (ou de várias pessoas) e procura encontrar a identidade de cada rosto na imagem (pesquisa de reconhecimento facial). Compara cada rosto detetado a um **PersonGroup,** uma base de dados de diferentes objetos **pessoais** cujas características faciais são conhecidas. Para fazer a operação Identificar, primeiro precisa criar e formar um **PersonGroup**

### <a name="create-and-train-a-person-group"></a>Criar e treinar um grupo de pessoas

O código a seguir cria um **PersonGroup** com seis objetos **Pessoais** diferentes. Associa cada **Pessoa a** um conjunto de imagens de exemplo, e depois treina para reconhecer cada pessoa pelas suas características faciais. Os objetos **pessoais** e **pessoas do Grupo** são utilizados nas operações de Verificação, Identificação e Grupo.

#### <a name="create-persongroup"></a>Criar Grupo de Pessoas

Declare uma variável de corda na raiz da sua classe para representar o ID do **PersonGroup** que irá criar.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

Num novo método, adicione o seguinte código. Este método realizará a operação Identificar. O primeiro bloco de código associa os nomes das pessoas às suas imagens de exemplo.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Em seguida, adicione o seguinte código para criar um objeto **Pessoa** para cada pessoa no Dicionário e adicione os dados faciais das imagens apropriadas. Cada objeto **pessoa** está associado com o mesmo **PersonGroup** através da sua cadeia de ID única. Lembre-se de passar as `client` variáveis, e para este `url` `RECOGNITION_MODEL1` método.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

#### <a name="train-persongroup"></a>Grupo de Pessoas do Comboio

Uma vez extraídos dados faciais das suas imagens e classificado-os em diferentes objetos **pessoais,** deve treinar o **PersonGroup** para identificar as características visuais associadas a cada um dos seus objetos **Pessoais.** O código a seguir chama o método do **comboio** assíncronos e sonda os resultados, imprimindo o estado à consola.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

Este grupo **Pessoa** e os seus objetos **Pessoas** associados estão agora prontos para serem utilizados nas operações de Verificação, Identificação ou Grupo.

### <a name="get-a-test-image"></a>Obtenha uma imagem de teste

Note que o código para [criar e treinar um grupo de pessoas](#create-and-train-a-person-group) define uma variável `sourceImageFileName` . Esta variável corresponde à imagem de origem &mdash; da imagem que contém pessoas para identificar.

### <a name="identify-faces"></a>Identificar rostos

O código seguinte pega na imagem de origem e cria uma lista de todos os rostos detetados na imagem. Estes são os rostos que serão identificados contra o **Grupo de Pessoas.**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

O próximo código chama a operação **IdentifyAsync** e imprime os resultados para a consola. Aqui, o serviço tenta combinar cada face da imagem de origem com uma **Pessoa** no Grupo **de Pessoas.** Isto encerra o seu método de identificação.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a sua aplicação de reconhecimento facial a partir do diretório de aplicações com o `dotnet run` comando.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Se criou um **PersonGroup** neste arranque rápido e pretende eliminá-lo, execute o seguinte código no seu programa:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Defina o método de eliminação com o seguinte código:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a biblioteca do cliente Face para .NET para fazer tarefas de reconhecimento facial de base. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência API face (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [O que é o serviço Face?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs).
