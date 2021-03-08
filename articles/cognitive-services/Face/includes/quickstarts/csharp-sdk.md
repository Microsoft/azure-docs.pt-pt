---
title: Início rápido da biblioteca de clientes Face .NET
description: Utilize a biblioteca cliente Face para .NET detetar rostos, encontrar semelhantes (procura facial por imagem), identificar rostos (pesquisa de reconhecimento facial) e migrar os dados do seu rosto.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: b06a35274082e09ec0973c5123b39935a93e354d
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445124"
---
Começa com o reconhecimento facial utilizando a biblioteca do cliente Face para .NET. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas. O serviço Face fornece-lhe acesso a algoritmos avançados para detetar e reconhecer rostos humanos em imagens.

Utilize a biblioteca cliente Face para .NET para:

* [Detetar rostos numa imagem](#detect-faces-in-an-image)
* [Encontre rostos semelhantes](#find-similar-faces)
* [Criar um grupo de pessoas](#create-a-person-group)
* [Identificar um rosto](#identify-a-face)

[Documentação de referência](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1)  |  [Amostras](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Pré-requisitos


* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* O [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) ou a versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" crie um recurso Face crie um recurso Face no portal "  target="_blank"> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API face. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Utilizando o Visual Studio, crie uma nova aplicação .NET Core. 

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente 

Depois de criar um novo projeto, instale a biblioteca do cliente clicando corretamente na solução de projeto no **Solution Explorer** e selecione **Gerir pacotes NuGet**. No gestor de pacotes que abre **selecione Navegar,** verificar **Incluir pré-relançar,** e procurar `Microsoft.Azure.CognitiveServices.Vision.Face` . Selecione a versão `2.6.0-preview.1` e, em seguida, **instale**. 

#### <a name="cli"></a>[CLI](#tab/cli)

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `face-quickstart` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: *program.cs*. 

```console
dotnet new console -n face-quickstart
```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

```console
dotnet build
```

A saída de construção não deve conter avisos ou erros. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente 

Dentro do diretório de aplicações, instale a biblioteca cliente Face para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
```

---

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs)que contém os exemplos de código neste arranque rápido.


A partir do diretório do projeto, abra o ficheiro *program.cs* e adicione as `using` seguintes diretivas:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

Na classe **programo** da aplicação, crie variáveis para a chave e ponto final do seu recurso.


> [!IMPORTANT]
> Aceda ao portal do Azure. Se o recurso Face criado na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar a sua chave e ponto final na **página chave e ponto final** do recurso, sob **gestão de recursos.** 
>
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Consulte o artigo [de segurança](../../../cognitive-services-security.md) dos Serviços Cognitivos para obter mais informações.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_creds)]

No método **principal** da aplicação, adicione chamadas para os métodos utilizados neste arranque rápido. Implementará isto mais tarde.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características da biblioteca cliente Face .NET:

|Nome|Descrição|
|---|---|
|[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) | Esta classe representa a sua autorização para usar o serviço Face, e precisa dele para toda a funcionalidade Face. Você instantanea-o com as suas informações de subscrição, e você usá-lo para produzir instâncias de outras classes. |
|[Operações faciais](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations)|Esta classe lida com as tarefas básicas de deteção e reconhecimento que pode fazer com rostos humanos. |
|[Face detetado](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)|Esta classe representa todos os dados que foram detetados a partir de um único rosto numa imagem. Pode usá-lo para obter informações detalhadas sobre o rosto.|
|[Operações FaceList](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations)|Esta classe gere as construções **FaceList** armazenadas em nuvem, que armazenam um conjunto variado de rostos. |
|[PersonGroupPersonExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions)| Esta classe gere as construções **de Pessoas** armazenadas em nuvem, que armazenam um conjunto de rostos que pertencem a uma única pessoa.|
|[PersonGroupOperações](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations)| Esta classe gere as construções **do PersonGroup** armazenadas na nuvem, que armazenam um conjunto de objetos **pessoais** variados. |

## <a name="code-examples"></a>Exemplos de código

Os snippets de código abaixo mostram-lhe como fazer as seguintes tarefas com a biblioteca cliente Face para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Detetar rostos numa imagem](#detect-faces-in-an-image)
* [Encontre rostos semelhantes](#find-similar-faces)
* [Criar um grupo de pessoas](#create-a-person-group)
* [Identificar um rosto](#identify-a-face)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Num novo método, instantaneamente um cliente com o seu ponto final e chave. Crie um objeto **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials)** com a sua chave e use-o com o seu ponto final para criar um objeto **[FaceClient.](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient)**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

### <a name="declare-helper-fields"></a>Declarar campos de ajuda

São necessários os seguintes campos para várias das operações face que irá adicionar mais tarde. Na raiz da sua aula **de Programa,** defina a seguinte cadeia URL. Este URL aponta para uma pasta de imagens de amostra.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

No seu método **Principal,** defina as cordas para apontar para os diferentes tipos de modelos de reconhecimento. Mais tarde, poderá especificar qual o modelo de reconhecimento que pretende utilizar para deteção facial. Consulte [Especificar um modelo de reconhecimento](../../Face-API-How-to-Topics/specify-recognition-model.md) para obter informações sobre estas opções.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Detetar rostos numa imagem

### <a name="get-detected-face-objects"></a>Obtenha objetos faciais detetados

Crie um novo método para detetar rostos. O `DetectFaceExtract` método processa três das imagens no URL dado e cria uma lista de objetos **[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)** na memória do programa. A lista de valores **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** especifica quais as características a extrair. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

> [!TIP]
> Também pode detetar rostos numa imagem local. Consulte os [métodos IFaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ifaceoperations) tais como **DetectWithStreamAsync**.

### <a name="display-detected-face-data"></a>Mostrar dados faciais detetados

O resto do `DetectFaceExtract` método analisa e imprime os dados de atributos para cada face detetada. Cada atributo deve ser especificado separadamente na chamada API de deteção facial original (na lista **[FaceAttributeType).](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** O código seguinte processa cada atributo, mas provavelmente só terá de usar um ou poucos.

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

### <a name="create-a-person-group"></a>Criar um grupo de pessoas

O código a seguir cria um **PersonGroup** com seis objetos **Pessoais** diferentes. Associa cada **Pessoa a** um conjunto de imagens de exemplo, e depois treina para reconhecer cada pessoa pelas suas características faciais. Os objetos **pessoais** e **pessoas do Grupo** são utilizados nas operações de Verificação, Identificação e Grupo.

Declare uma variável de corda na raiz da sua classe para representar o ID do **PersonGroup** que irá criar.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

Num novo método, adicione o seguinte código. Este método realizará a operação Identificar. O primeiro bloco de código associa os nomes das pessoas às suas imagens de exemplo.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Note que este código define uma variável `sourceImageFileName` . Esta variável corresponde à imagem de origem &mdash; da imagem que contém pessoas para identificar.

Em seguida, adicione o seguinte código para criar um objeto **Pessoa** para cada pessoa no Dicionário e adicione os dados faciais das imagens apropriadas. Cada objeto **pessoa** está associado com o mesmo **PersonGroup** através da sua cadeia de ID única. Lembre-se de passar as `client` variáveis, e para este `url` `RECOGNITION_MODEL1` método.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

> [!TIP]
> Também pode criar um **PersonGroup a** partir de imagens locais. Consulte os métodos [IPersonGroupPerson](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ipersongroupperson) tais como **AddFaceFromStreamAsync**.

### <a name="train-the-persongroup"></a>Treine o PersonGroup

Uma vez extraídos dados faciais das suas imagens e classificado-os em diferentes objetos **pessoais,** deve treinar o **PersonGroup** para identificar as características visuais associadas a cada um dos seus objetos **Pessoais.** O código a seguir chama o método do **comboio** assíncronos e sonda os resultados, imprimindo o estado à consola.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

> [!TIP]
> A API face funciona num conjunto de modelos pré-construídos que são estáticos por natureza (o desempenho do modelo não vai regredir ou melhorar à medida que o serviço é executado). Os resultados que o modelo produz podem mudar se a Microsoft atualizar o backend do modelo sem migrar para uma versão totalmente nova do modelo. Para tirar partido de uma versão mais recente de um modelo, pode reforçá-lo **como** parâmetro com as mesmas imagens de inscrição.

Este grupo **Pessoa** e os seus objetos **Pessoas** associados estão agora prontos para serem utilizados nas operações de Verificação, Identificação ou Grupo.

### <a name="identify-faces"></a>Identificar rostos

O código seguinte pega na imagem de origem e cria uma lista de todos os rostos detetados na imagem. Estes são os rostos que serão identificados contra o **Grupo de Pessoas.**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

O próximo código chama a operação **IdentifyAsync** e imprime os resultados para a consola. Aqui, o serviço tenta combinar cada face da imagem de origem com uma **Pessoa** no Grupo **de Pessoas.** Isto encerra o seu método de identificação.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

## <a name="run-the-application"></a>Executar a aplicação

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Executar a aplicação clicando no botão **Debug** na parte superior da janela IDE.

#### <a name="cli"></a>[CLI](#tab/cli)

Executar o pedido do seu diretório de candidaturas com o `dotnet run` comando.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Se criou um **PersonGroup** neste arranque rápido e pretende eliminá-lo, execute o seguinte código no seu programa:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Defina o método de eliminação com o seguinte código:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a biblioteca do cliente Face para .NET para fazer tarefas básicas de reconhecimento facial. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência API face (.NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)

* [O que é o serviço Face?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs).
