---
title: 'Início rápido: biblioteca de cliente facial para .NET | Microsoft Docs'
description: Introdução à biblioteca de cliente facial para .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 5602e26e2e1b327d65d411f8e92f264f1bf9fb91
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977867"
---
# <a name="quickstart-face-client-library-for-net"></a>Início rápido: biblioteca de cliente facial para .NET

Introdução à biblioteca de cliente facial para .NET. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. O serviço de API de Detecção Facial fornece acesso a algoritmos avançados para detectar e reconhecer rostos humanos em imagens.

Use a biblioteca de cliente facial para .NET para:

* [Autenticar o cliente](#authenticate-the-client)
* [Detectar faces em uma imagem](#detect-faces-in-an-image)
* [Localizar faces semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identificar uma face](#identify-a-face)
* [Tirar um instantâneo para a migração de dados](#take-a-snapshot-for-data-migration)

[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [código-fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [do NuGet ( do pacote)de](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1) | [amostras biblioteca](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configurando

### <a name="create-a-face-azure-resource"></a>Criar um recurso facial do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para a face usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também pode:

* Obtenha uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias gratuitamente. Depois de se inscrever, ele estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exiba seu recurso no [portal do Azure](https://portal.azure.com/).

Depois de obter uma chave de sua assinatura ou recurso de avaliação, [crie uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e a URL do ponto de extremidade, denominada `FACE_SUBSCRIPTION_KEY` e `FACE_ENDPOINT`, respectivamente.

### <a name="create-a-new-c-application"></a>Criar um novo C# aplicativo

Crie um novo aplicativo .NET Core em seu editor ou IDE preferido. 

Em uma janela de console (como cmd, PowerShell ou bash), use o comando `dotnet new` para criar um novo aplicativo de console com o nome `face-quickstart`. Este comando cria um projeto simples de " C# Olá, mundo" com um único arquivo de origem: *Program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Altere o diretório para a pasta de aplicativos recém-criada. Você pode criar o aplicativo com:

```dotnetcli
dotnet build
```

A saída da compilação não deve conter nenhum aviso ou erro. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

No diretório do projeto, abra o arquivo *Program.cs* no seu editor ou IDE preferido. Adicione as seguintes diretivas de `using`:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_using)]

No método de `Main` do aplicativo, crie variáveis para o ponto de extremidade e a chave do Azure do recurso.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

No diretório do aplicativo, instale a biblioteca de cliente facial para .NET com o seguinte comando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.5.0-preview.1
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de cliente estará disponível como um pacote NuGet baixável.

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir tratam de alguns dos principais recursos do SDK do .NET facial:

|Nome|Descrição|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Essa classe representa sua autorização para usar o serviço de face e você precisa dela para todas as funcionalidades de face. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes. |
|[FaceOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Essa classe manipula as tarefas básicas de detecção e reconhecimento que você pode fazer com as faces humanas. |
|[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Essa classe representa todos os dados que foram detectados de uma única face em uma imagem. Você pode usá-lo para recuperar informações detalhadas sobre a face.|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Essa classe gerencia os constructos da **facelist** armazenados na nuvem, que armazenam um conjunto de faces classificado. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Essa classe gerencia as construções de **pessoas** armazenadas na nuvem, que armazenam um conjunto de faces que pertencem a uma única pessoa.|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Essa classe gerencia as construções de grupo de **pessoas** armazenadas na nuvem, que armazenam um conjunto de objetos de **pessoa** asclassificadas. |
|[ShapshotOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Essa classe gerencia a funcionalidade de instantâneo. Você pode usá-lo para salvar temporariamente todos os dados de face baseados em nuvem e migrar esses dados para uma nova assinatura do Azure. |

## <a name="code-examples"></a>Exemplos de código

Os trechos de código a seguir mostram como realizar as seguintes tarefas com a biblioteca de cliente facial para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Detectar faces em uma imagem](#detect-faces-in-an-image)
* [Localizar faces semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identificar uma face](#identify-a-face)
* [Tirar um instantâneo para a migração de dados](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este início rápido pressupõe que você [criou variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e o ponto de extremidade de face, chamados `FACE_SUBSCRIPTION_KEY` e `FACE_ENDPOINT`.

Em um novo método, crie uma instância de um cliente com o ponto de extremidade e a chave. Crie um objeto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) com sua chave e use-o com seu ponto de extremidade para criar um objeto [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) .

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_auth)]

Você provavelmente desejará chamar esse método no método `Main`.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_client)]

## <a name="detect-faces-in-an-image"></a>Detetar rostos numa imagem

Na raiz da sua classe, defina a cadeia de caracteres de URL a seguir. Essa URL aponta para um conjunto de imagens de exemplo.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

Opcionalmente, você pode escolher qual modelo de ia usar para extrair dados das faces detectadas. Consulte [especificar um modelo de reconhecimento](../Face-API-How-to-Topics/specify-recognition-model.md) para obter informações sobre essas opções.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

A operação de detecção final usará um objeto [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) , uma URL de imagem e um modelo de reconhecimento.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Obter objetos de face detectados

No próximo bloco de código, o método `DetectFaceExtract` detecta rostos em três das imagens na URL fornecida e cria uma lista de objetos [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) na memória do programa. A lista de valores [FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) especifica quais recursos extrair. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Exibir dados de face detectados

O restante do método `DetectFaceExtract` analisa e imprime os dados de atributo para cada face detectada. Cada atributo deve ser especificado separadamente na chamada à API de detecção facial original (na lista [FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) ). O código a seguir processa todos os atributos, mas provavelmente você só precisará usar um ou alguns.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

O código a seguir usa uma única face detectada (origem) e pesquisa um conjunto de outras faces (destino) para encontrar correspondências. Quando ele encontra uma correspondência, ele imprime a ID da face correspondente no console.

### <a name="detect-faces-for-comparison"></a>Detectar faces para comparação

Primeiro, defina um segundo método de detecção facial. Você precisa detectar faces em imagens antes de compará-las, e esse método de detecção é otimizado para operações de comparação. Ele não extrai atributos de face detalhados, como na seção acima, e usa um modelo de reconhecimento diferente.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Localizar correspondências

O método a seguir detecta rostos em um conjunto de imagens de destino e em uma única imagem de origem. Em seguida, ele os compara e localiza todas as imagens de destino que são semelhantes à imagem de origem.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Imprimir correspondências

O código a seguir imprime os detalhes de correspondência no console do:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar_print)]

## <a name="create-and-train-a-person-group"></a>Criar e treinar um grupo de pessoas

O código a seguir cria um **Person** com seis objetos **Person** diferentes. Ele associa cada **pessoa** a um conjunto de imagens de exemplo e, em seguida, treina para reconhecer cada pessoa por suas características faciais. Os objetos **Person** e **Person** são usados nas operações de verificação, identificação e grupo.

Se você ainda não tiver feito isso, defina a cadeia de caracteres de URL a seguir na raiz da sua classe. Isso aponta para um conjunto de imagens de exemplo.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

O código mais adiante nesta seção especificará um modelo de reconhecimento para extrair dados de rostos e o trecho a seguir cria referências aos modelos disponíveis. Consulte [especificar um modelo de reconhecimento](../Face-API-How-to-Topics/specify-recognition-model.md) para obter informações sobre modelos de reconhecimento.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

### <a name="create-persongroup"></a>Criar um Person

Declare uma variável de cadeia de caracteres na raiz da sua classe para representar a ID do **Person** que você criará.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_declare)]

Em um novo método, adicione o código a seguir. Esse código associa os nomes de pessoas às suas imagens de exemplo.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_files)]

Em seguida, adicione o código a seguir para criar um objeto **Person** para cada pessoa no dicionário e adicionar os dados de face das imagens apropriadas. Cada objeto **Person** é associado ao mesmo **Person** por meio de sua cadeia de caracteres de ID exclusiva. Lembre-se de passar as variáveis `client`, `url`e `RECOGNITION_MODEL1` nesse método.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_create)]

### <a name="train-persongroup"></a>Treinar o Person

Depois de extrair dados de face de suas imagens e classificá-los em objetos de **pessoa** diferentes, você deve treinar o **Person** para identificar os recursos visuais associados a cada um de seus objetos **Person** . O código a seguir chama o método de **treinamento** assíncrono e pesquisa os resultados, imprimindo o status no console.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_train)]

Este grupo de **pessoas** e seus objetos **Person** associados agora estão prontos para serem usados nas operações de verificação, identificação ou grupo.

## <a name="identify-a-face"></a>Identificar uma face

A operação identificar usa uma imagem de uma pessoa (ou várias pessoas) e procura encontrar a identidade de cada face na imagem. Ele compara cada face detectada a um **Person**, um banco de dados de objetos **Person** diferentes cujos recursos facial são conhecidos.

> [!IMPORTANT]
> Para executar este exemplo, você deve primeiro executar o código em [criar e treinar um grupo de pessoas](#create-and-train-a-person-group). As variáveis usadas nessa seção&mdash;`client`, `url`e `RECOGNITION_MODEL1`&mdash;também devem estar disponíveis aqui.

### <a name="get-a-test-image"></a>Obter uma imagem de teste

Observe que o código para [criar e treinar um grupo Person](#create-and-train-a-person-group) define uma variável `sourceImageFileName`. Essa variável corresponde à imagem de origem&mdash;imagem que contém as pessoas a serem identificadas.

### <a name="identify-faces"></a>Identificar rostos

O código a seguir usa a imagem de origem e cria uma lista de todas as faces detectadas na imagem. Essas são as faces que serão identificadas em relação ao **Person**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify_sources)]

O próximo trecho de código chama a operação identificar e imprime os resultados no console. Aqui, o serviço tenta corresponder cada face da imagem de origem a uma **pessoa** **no determinado resourcegroup**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Tirar um instantâneo para a migração de dados

O recurso de instantâneos permite que você mova os dados de rosto salvos, como um **usuário**treinado, para uma assinatura de face de serviços cognitivas do Azure diferente. Talvez você queira usar esse recurso se, por exemplo, tiver criado um objeto **Person** usando uma assinatura de avaliação gratuita e desejar migrá-lo para uma assinatura paga. Consulte [migrar seus dados de face](../Face-API-How-to-Topics/how-to-migrate-face-data.md) para obter uma visão geral do recurso de instantâneos.

Neste exemplo, você vai migrar o grupo de **pessoas** que criou em [criar e treinar um usuário](#create-and-train-a-person-group). Você pode concluir essa seção primeiro ou criar suas próprias construções de dados de face para migrar.

### <a name="set-up-target-subscription"></a>Configurar assinatura de destino

Primeiro, você deve ter uma segunda assinatura do Azure com um recurso de face; Você pode fazer isso seguindo as etapas na seção [configuração](#setting-up) . 

Em seguida, defina as variáveis a seguir no método `Main` do seu programa. Você precisará criar novas variáveis de ambiente para a ID da assinatura da sua conta do Azure, bem como a chave, o ponto de extremidade e a ID da assinatura da sua nova conta (destino). 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

Para este exemplo, declare uma variável para a ID do **usuário** de destino&mdash;o objeto que pertence à nova assinatura, para a qual você copiará os dados.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>Autenticar cliente de destino

Em seguida, adicione o código para autenticar sua assinatura de face secundária.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Usar um instantâneo

O restante das operações de instantâneo deve ocorrer em um método assíncrono. 

1. A primeira etapa é **tirar** o instantâneo, que salva os dados de face da assinatura original em um local de nuvem temporário. Esse método retorna uma ID que você usa para consultar o status da operação.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take)]

1. Em seguida, consulte a ID até que a operação seja concluída.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take_wait)]

1. Em seguida, use a operação **aplicar** para gravar seus dados de rosto em sua assinatura de destino. Esse método também retorna um valor de ID.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Novamente, consulte a nova ID até que a operação seja concluída.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Por fim, conclua o bloco try/catch e conclua o método.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_trycatch)]

Neste ponto, o novo objeto **Person** deve ter os mesmos dados que o original e deve ser acessível por sua nova assinatura do Azure face (destino).

## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo no diretório do aplicativo com o comando `dotnet run`.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Se você criou um **Person** neste guia de início rápido e deseja excluí-lo, execute o seguinte código em seu programa:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_delete)]

Defina o método de exclusão com o seguinte código:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_deletepersongroup)]

Além disso, se você migrou dados usando o recurso de instantâneo neste guia de início rápido, também precisará excluir a **pessoa** salva na assinatura de destino.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a usar a biblioteca facial para .NET para tarefas de base. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência de API de Detecção Facial (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [O que é o API de Detecção Facial?](../overview.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/Face/Program.cs).
