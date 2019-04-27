---
title: 'Início rápido: Detetar rostos numa imagem com o SDK de .NET do Azure Face'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá utilizar o SDK de rostos do Azure com o C# para detetar rostos numa imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: pafarley
ms.openlocfilehash: 57605f9bd1a39435e27a2f2c56c06cf3bfb38605
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815437"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-net-sdk"></a>Início rápido: Detetar rostos numa imagem usando o .NET SDK de rostos

Neste início rápido, vai utilizar a Face do serviço SDK com C# para detetar rostos humanos numa imagem. Para um exemplo de código neste guia de introdução, consulte o projeto de Face no [inícios rápidos de csharp visão de serviços cognitivos](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face) repositório no GitHub.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura da Face API. Pode obter uma chave de subscrição de avaliação gratuita de [experimentar os serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Em alternativa, siga as instruções em [criar uma conta dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço de API de rostos e obtenha a chave.
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

1. No Visual Studio, crie um novo **aplicação de consola (.NET Framework)** do projeto e nomeie- **FaceDetection**. 
1. Se houver outros projetos na sua solução, selecione esta como o único projeto de arranque.
1. Obtenha os pacotes NuGet necessários. Com o botão direito no seu projeto no Solution Explorer e selecione **gerir pacotes NuGet**. Clique nas **navegue** separador e selecione **incluir pré-lançamento**; em seguida, localizar e instalar o pacote seguinte:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)
1. Certifique-se de que instalou as versões mais recentes de todos os pacotes de NuGet para o seu projeto. Com o botão direito no seu projeto no Solution Explorer e selecione **gerir pacotes NuGet**. Clique nas **atualizações** separador e instalar as versões mais recentes de todos os pacotes que são apresentados.

## <a name="add-face-detection-code"></a>Adicione o código de detecção de rostos

Abra o novo projeto *Program.cs* ficheiro. Aqui, irá adicionar o código necessário para carregar imagens e detetar rostos.

### <a name="include-namespaces"></a>Incluir espaços de nomes

Adicione as declarações `using` seguintes à parte superior do ficheiro *Program.cs*.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=1-7)]

### <a name="add-essential-fields"></a>Adicione campos essenciais

Adicionar a **programa** classe com os seguintes campos. Estes dados especifica como se pode ligar para o serviço de rostos e onde obter os dados de entrada. Terá de atualizar o `subscriptionKey` campo com o valor da sua chave de assinatura e poderá ter de alterar o `faceEndpoint` para que ele contém o identificador de região correto de cadeias de caracteres. Também precisará definir o `localImagePath` e/ou `remoteImageUrl` ficheiros de imagem de valores para os caminhos que apontam para real.

O `faceAttributes` campo é simplesmente uma matriz de determinados tipos de atributos. Ele irá especificar quais as informações para recuperar sobre os rostos detetados.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=9-34)]

### <a name="create-and-use-the-face-client"></a>Criar e utilizar o cliente de rostos

Em seguida, adicione a **Main** método o **programa** classe com o código a seguir. Esta ação configura um cliente de Face API.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=36-41)]

Também na **Main** método, adicione o seguinte código para utilizar o cliente de Face recentemente criado para detetar rostos numa imagem remota e local. Os métodos de deteção serão definidos em seguida. 

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=43-50)]

### <a name="detect-faces"></a>Detetar rostos

Adicione o seguinte método à classe **Programa**. Utiliza o cliente do serviço de rostos para detetar rostos numa imagem remota, referenciada por um URL. Tenha em atenção que utiliza a `faceAttributes` campo&mdash;a **DetectedFace** objetos adicionados à `faceList` terão os atributos especificados (neste caso, a idade e género).

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=52-74)]

Da mesma forma, adicione a **DetectLocalAsync** método. Utiliza o cliente do serviço de rostos para detetar rostos numa imagem do local, referenciada por um caminho de ficheiro.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=76-101)]

### <a name="retrieve-and-display-face-attributes"></a>Recuperar e exibir os atributos faciais

Em seguida, defina o **GetFaceAttributes** método. Devolve uma cadeia de caracteres com as informações de atributo relevantes.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=103-116)]

Por fim, definir o **DisplayAttributes** método para escrever dados de atributo de face a saída da consola. Em seguida, pode fechar a classe e espaço de nomes.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=118-125)]

## <a name="run-the-app"></a>Executar a aplicação

Uma resposta com êxito irá apresentar o sexo e age para cada rosto na imagem. Por exemplo:

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou uma aplicação de consola .NET simple que pode utilizar o serviço de API de rostos para detetar rostos em imagens locais e remotas. Em seguida, siga um tutorial mais aprofundado para ver como pode apresentar informações de face ao usuário de maneira intuitiva.

> [!div class="nextstepaction"]
> [Tutorial: Criar um aplicativo do WPF para detetar e analisar os rostos numa imagem](../Tutorials/FaceAPIinCSharpTutorial.md)
