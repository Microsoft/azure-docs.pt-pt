---
title: 'Quickstart: Encarar a biblioteca de clientes para .NET'
description: Comece com a biblioteca de clientes Face para .NET com este arranque rápido.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: e51937dfe2afa0e92ce98b4c305555b53896e5f1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "78401779"
---
# <a name="quickstart-face-client-library-for-net"></a>Quickstart: Encarar a biblioteca de clientes para .NET

Começar com a biblioteca de clientes Face para .NET. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas. O serviço Face fornece-lhe acesso a algoritmos avançados para detetar e reconhecer rostos humanos em imagens.

Utilize a biblioteca do cliente Face para .NET para:

* [Detetar rostos numa imagem](#detect-faces-in-an-image)
* [Encontrar rostos semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identifique um rosto](#identify-a-face)
* [Tire uma foto para a migração de dados](#take-a-snapshot-for-data-migration)

[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | Pacote[de código fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | da biblioteca[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1) | [Samples](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configuração

### <a name="create-a-face-azure-resource"></a>Criar um recurso Face Azure

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para face utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [o Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na sua máquina local. Também pode:

* Obtenha uma chave de [teste](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias de graça. Depois de se inscrever, estará disponível no site do [Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Veja o seu recurso no [portal Azure.](https://portal.azure.com/)

Depois de obter uma chave da sua subscrição ou recurso experimental, [crie uma variável ambiental](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para o URL chave e final, nomeado `FACE_SUBSCRIPTION_KEY` e, `FACE_ENDPOINT`respectivamente.

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Crie uma nova aplicação .NET Core no seu editor ou IDE preferido. 

Numa janela de consola (como cmd, PowerShell `dotnet new` ou Bash), utilize o comando `face-quickstart`para criar uma nova aplicação de consola com o nome . Este comando cria um simples projeto C# "Hello World" com um único ficheiro fonte: *Program.cs*. 

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

A partir do diretório do projeto, abra o ficheiro *Program.cs* no seu editor ou IDE preferido. Adicione as `using` seguintes diretivas:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_using)]

No método da `Main` aplicação, crie variáveis para o ponto final e chave Azure do seu recurso.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>Instale a biblioteca do cliente

Dentro do diretório de aplicações, instale a biblioteca do cliente Face para .NET com o seguinte comando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.5.0-preview.1
```

Se estiver a usar o Visual Studio IDE, a biblioteca de clientes está disponível como um pacote NuGet transferível.

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características do Face .NET SDK:

|Nome|Descrição|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Esta classe representa a sua autorização para usar o serviço Face, e você precisa dele para todas as funcionalidades Face. Você instantaneamente com a sua informação de subscrição, e você usa-o para produzir casos de outras classes. |
|[FaceOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Esta classe lida com as tarefas básicas de deteção e reconhecimento que você pode fazer com rostos humanos. |
|[Detetou Face](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Esta classe representa todos os dados que foram detetados a partir de um único rosto numa imagem. Pode usá-lo para obter informações detalhadas sobre o rosto.|
|[FacelistOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Esta classe gere as construções **FaceList** armazenadas em nuvem, que armazenam um conjunto variado de rostos. |
|[Extensões pessoais](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Esta classe gere as construções da **Pessoa** armazenada na nuvem, que armazenam um conjunto de rostos que pertencem a uma única pessoa.|
|[Operações pessoais](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Esta classe gere as construções do **PersonGroup** armazenados em nuvem, que armazenam um conjunto de objetos **pessoas** variados. |
|[Operações Shapshot](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Esta classe gere a funcionalidade Snapshot. Pode usá-lo para guardar temporariamente todos os seus dados face baseados na nuvem e migrar esses dados para uma nova subscrição do Azure. |

## <a name="code-examples"></a>Exemplos de código

Os códigos abaixo mostram-lhe como fazer as seguintes tarefas com a biblioteca do cliente Face para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Detetar rostos numa imagem](#detect-faces-in-an-image)
* [Encontrar rostos semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identifique um rosto](#identify-a-face)
* [Tire uma foto para a migração de dados](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este quickstart assume que [criaste variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a `FACE_SUBSCRIPTION_KEY` `FACE_ENDPOINT`chave face e ponto final, nomeado e .

Num novo método, instanteie um cliente com o seu ponto final e chave. Crie um objeto **[ApiKeyServiceClientCredenciais](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials?view=azure-dotnet)** com a sua chave e use-o com o seu ponto final para criar um objeto **[FaceClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)**

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_auth)]

Provavelmente vai querer chamar este `Main` método no método.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_client)]

## <a name="detect-faces-in-an-image"></a>Detetar rostos numa imagem

Na raiz da sua classe, defina a seguinte cadeia de URL. Este URL aponta para um conjunto de imagens de amostra.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

Opcionalmente, pode escolher qual modelo de IA usar para extrair dados do ou rosto detetados. Consulte [especificar um modelo de reconhecimento](../Face-API-How-to-Topics/specify-recognition-model.md) de informações sobre estas opções.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

A operação de deteção final terá um objeto **[FaceClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** um URL de imagem e um modelo de reconhecimento.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Obter objetos faciais detetados

No próximo bloco de `DetectFaceExtract` código, o método deteta rostos em três das imagens no URL dado e cria uma lista de objetos **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** na memória do programa. A lista de valores **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** especifica quais as funcionalidades a extrair. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Mostrar dados faciais detetados

O resto `DetectFaceExtract` do método analisa e imprime os dados do atributo para cada rosto detetado. Cada atributo deve ser especificado separadamente na chamada API de deteção facial original (na lista **[FaceAttributeType).](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** O código seguinte processa cada atributo, mas provavelmente só terá de usar um ou alguns.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

O código seguinte pega num único rosto detetado (fonte) e procura um conjunto de outras faces (alvo) para encontrar fósforos. Quando encontra uma correspondência, imprime a identificação da face compatível com a consola.

### <a name="detect-faces-for-comparison"></a>Detetar rostos para comparação

Primeiro, defina um segundo método de deteção facial. É necessário detetar rostos em imagens antes de as comparar, e este método de deteção está otimizado para operações de comparação. Não extrai atributos faciais detalhados como na secção acima, e usa um modelo de reconhecimento diferente.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Encontre fósforos

O método seguinte deteta rostos num conjunto de imagens-alvo e numa única imagem de origem. Em seguida, compara-as e encontra todas as imagens-alvo que são semelhantes à imagem de origem.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Imprimir fósforos

O código que se segue imprime os detalhes da partida para a consola:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar_print)]

## <a name="create-and-train-a-person-group"></a>Criar e treinar um grupo de pessoas

O seguinte código cria um **PersonGroup** com seis objetos **pessoa** diferentes. Associa cada **Pessoa** a um conjunto de imagens de exemplo, e depois treina para reconhecer cada pessoa pelas suas características faciais. Os objetos **Pessoais** e **PersonGroup** são utilizados nas operações de Verificação, Identificação e Grupo.

Se ainda não o fez, defina a seguinte cadeia de URL na raiz da sua classe. Isto aponta para um conjunto de imagens de amostra.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

O código mais tarde nesta secção especificará um modelo de reconhecimento para extrair dados de rostos, e o seguinte corte cria referências aos modelos disponíveis. Consulte [especificar um modelo de reconhecimento](../Face-API-How-to-Topics/specify-recognition-model.md) de informação sobre modelos de reconhecimento.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

### <a name="create-persongroup"></a>Criar persongroup

Declare uma variável de cadeia na raiz da sua classe para representar a identificação do **PersonGroup** que irá criar.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_declare)]

Num novo método, adicione o seguinte código. Este código associa os nomes das pessoas às suas imagens de exemplo.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_files)]

Em seguida, adicione o seguinte código para criar um objeto **Pessoa** para cada pessoa no Dicionário e adicione os dados faciais a partir das imagens apropriadas. Cada objeto **pessoa** está associado ao mesmo **PersonGroup** através da sua cadeia de ID única. Lembre-se de `client`passar `url`as `RECOGNITION_MODEL1` variáveis, e entrar neste método.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_create)]

### <a name="train-persongroup"></a>Grupo de Pessoas do Comboio

Depois de extrair dados faciais das suas imagens e os classificar em diferentes objetos **pessoais,** deve treinar o **PersonGroup** para identificar as características visuais associadas a cada um dos seus objetos **Pessoais.** O código seguinte chama o método do **comboio** assíncrono e vota os resultados, imprimindo o estado à consola.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_train)]

Este grupo **Pessoa** e os seus **objetos** pessoais associados estão agora prontos para serem utilizados nas operações de Verificação, Identificação ou Grupo.

## <a name="identify-a-face"></a>Identifique um rosto

A operação Identificar tira uma imagem de uma pessoa (ou de várias pessoas) e procura encontrar a identidade de cada rosto na imagem. Compara cada rosto detetado a um **PersonGroup,** uma base de dados de diferentes objetos **da Pessoa** cujas características faciais são conhecidas.

> [!IMPORTANT]
> Para executar este exemplo, primeiro deve executar o código em [Create e treinar um grupo de pessoas](#create-and-train-a-person-group). As variáveis utilizadas&mdash;`client` `url`nessa `RECOGNITION_MODEL1` &mdash;secção, e também devem estar disponíveis aqui.

### <a name="get-a-test-image"></a>Obtenha uma imagem de teste

Note que o código para [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group) define uma variável `sourceImageFileName`. Esta variável corresponde à&mdash;imagem de origem da imagem que contém pessoas para identificar.

### <a name="identify-faces"></a>Identificar rostos

O código seguinte pega na imagem de origem e cria uma lista de todos os rostos detetados na imagem. Estes são os rostos que serão identificados contra o **Grupo Pessoa.**

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify_sources)]

O próximo código chama a operação Identificar e imprime os resultados para a consola. Aqui, o serviço tenta combinar cada rosto desde a imagem de origem a uma **pessoa** no grupo **pessoa.**

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Tire uma foto para a migração de dados

A funcionalidade Snapshots permite mover os dados do Face guardados, como um **PersonGroup**treinado, para uma subscrição diferente do Face dos Serviços Cognitivos Azure. Pode querer utilizar esta funcionalidade se, por exemplo, tiver criado um objeto **PersonGroup** usando uma subscrição de teste gratuito e quiser emigrar para uma subscrição paga. Consulte a [Migração dos dados](../Face-API-How-to-Topics/how-to-migrate-face-data.md) do seu rosto para obter uma visão geral da funcionalidade Snapshots.

Neste exemplo, irá migrar o **PersonGroup** que criou na [Create e formar um grupo de pessoas.](#create-and-train-a-person-group) Pode completar a secção primeiro, ou criar a sua própria construção de dados Face para migrar.

### <a name="set-up-target-subscription"></a>Configurar a subscrição do alvo

Em primeiro lugar, deve ter uma segunda subscrição Azure com recurso Face; pode fazê-lo seguindo os passos na secção [Configuração.](#setting-up) 

Em seguida, defina as `Main` seguintes variáveis no método do seu programa. Terá de criar novas variáveis ambientais para o ID de subscrição da sua conta Azure, bem como a chave, ponto final e id de subscrição da sua nova conta (target). 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

Para este exemplo, declare uma variável para o ID do target **PersonGroup**&mdash;o objeto que pertence à nova subscrição, para a qual irá copiar os seus dados.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>Autenticar cliente-alvo

Em seguida, adicione o código para autenticar a subscrição do Face secundário.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Use uma foto

As restantes operações instantâneas devem ser efetuadas num método assíncrono. 

1. O primeiro passo é **tirar** o instantâneo, que guarda os dados faciais da sua subscrição original para uma localização temporária na nuvem. Este método devolve um ID que utiliza para consultar o estado da operação.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take)]

1. Em seguida, consultar o ID até que a operação esteja concluída.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take_wait)]

1. Em seguida, utilize a operação **de aplicação** para escrever os dados do seu rosto para a subscrição do alvo. Este método também devolve um valor de ID.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Mais uma vez, consultar o novo ID até que a operação esteja concluída.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Por fim, complete o bloco de tentativas/capturas e termine o método.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_trycatch)]

Neste ponto, o seu novo objeto **PersonGroup** deve ter os mesmos dados que o original e deve estar acessível a partir da sua nova subscrição (target) Azure Face.

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação a `dotnet run` partir do seu diretório de candidatura com o comando.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Se criou um **PersonGroup** neste arranque rápido e pretende eliminá-lo, execute o seguinte código no seu programa:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_delete)]

Defina o método de eliminação com o seguinte código:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_deletepersongroup)]

Além disso, se tiver dados migrados utilizando a funcionalidade Snapshot neste arranque rápido, também terá de eliminar o **PersonGroup** guardado para a subscrição do alvo.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a biblioteca Face para .NET para fazer tarefas de base. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência face API (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [O que é o serviço Face?](../overview.md)
* O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/Face/Program.cs).
