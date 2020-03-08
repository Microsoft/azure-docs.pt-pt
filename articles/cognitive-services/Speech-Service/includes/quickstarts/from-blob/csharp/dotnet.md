---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: dapine
ms.openlocfilehash: 959e035dc632e84595bdb54c7202f19991697fdb
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925716"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=vs&pivots=programmming-language-csharp)
> * [Criar um recurso azure speech](../../../../get-started.md)
> * [Faça upload de um ficheiro fonte para uma bolha Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="open-your-project-in-visual-studio"></a>Abra o seu projeto no Estúdio Visual

O primeiro passo é garantir que tem o seu projeto aberto no Estúdio Visual.

1. Lançar O Estúdio Visual 2019.
2. Carregue o seu projeto e abra `Program.cs`.

## <a name="add-a-reference-to-newtonsoftjson"></a>Adicione uma referência a Newtonsoft.Json

1. No Solution Explorer, clique no projeto **Helloworld** e, em seguida, selecione **Gerir pacotes NuGet** para mostrar o NuGet Package Manager.

1. No canto superior direito, encontre a caixa de entrega da Fonte de **Embalagem** e certifique-se de que **`nuget.org`** é selecionada.

1. No canto superior esquerdo, **selecione Browse**.

1. Na caixa de pesquisa, digite *newtonsoft.json* e selecione **Enter**.

1. A partir dos resultados da pesquisa, selecione o pacote [**Newtonsoft.Json**](https://www.nuget.org/packages/Newtonsoft.Json) e, em seguida, selecione **Instalar** para instalar a versão mais recente estável.

1. Aceite todos os acordos e licenças para iniciar a instalação.

   Após a instalação da embalagem, aparece uma confirmação na janela da Consola do Gestor de **Pacotes.**

## <a name="start-with-some-boilerplate-code"></a>Comece com um pouco de código de placa de caldeira

Vamos adicionar um código que funciona como um esqueleto para o nosso projeto.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=6-43,138,277)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>Invólucros JSON

À medida que os pedidos da API rest's tomam em formato JSON e também devolvem resultados em JSON poderíamos interagir com eles usando apenas cordas, mas isso não é recomendado.
Para facilitar a gestão dos pedidos e respostas, declararemos algumas classes para serializar/desserializar o JSON.

Vá em frente e coloque as suas declarações depois de `TranscribeAsync`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=140-276)]

## <a name="create-and-configure-an-http-client"></a>Criar e configurar um Cliente http
A primeira coisa que precisamos é de um Cliente Http que tenha um URL base correto e conjunto de autenticação.
Insira este código na [!code-csharp`TranscribeAsync` [](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=46-50)]

## <a name="generate-a-transcription-request"></a>Gerar um pedido de transcrição
Em seguida, vamos gerar o pedido de transcrição. Adicione este código a `TranscribeAsync` [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=52-57)]

## <a name="send-the-request-and-check-its-status"></a>Envie o pedido e verifique o seu estado
Agora publicamos o pedido no serviço de Discurso e verificamos o código de resposta inicial. Este código de resposta indicará simplesmente se o serviço recebeu o pedido. O serviço devolverá um Url nos cabeçalhos de resposta que é o local onde armazenará o estado da transcrição.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=59-70)]

## <a name="wait-for-the-transcription-to-complete"></a>Aguarde a transcrição para completar
Uma vez que o serviço processa a transcrição assincronicamente, precisamos de fazer sondagens para o seu estatuto de vez em quando. Vamos verificar a cada 5 segundos.

Podemos verificar o estado recuperando o conteúdo no Url que obtivemos quando o pedido publicou. Quando recuperarmos o conteúdo, desserializámo-lo numa das nossas aulas de ajuda para facilitar a interação.

Aqui está o código de votação com o status display para tudo, exceto uma conclusão bem sucedida, vamos fazê-lo a seguir.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=72-106,121-137)]

## <a name="display-the-transcription-results"></a>Mostrar os resultados da transcrição
Uma vez concluído o serviço com sucesso, os resultados serão armazenados em outro Url que podemos obter da resposta do estado.

Aqui fazemos um pedido para baixar esses resultados em um arquivo temporário antes de lê-los e desserializá-los.
Uma vez carregados os resultados, podemos imprimi-los à consola.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=107-120)]

## <a name="check-your-code"></a>Verifique o seu código
Neste ponto, o seu código deve ser assim: (Adicionámos alguns comentários a esta versão) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=6-277)]

## <a name="build-and-run-your-app"></a>Construa e execute a sua app

Agora está pronto para construir a sua app e testar o nosso reconhecimento de voz usando o serviço de Discurso.

1. **Compile o código** - A partir da barra de menu sécpor do Estúdio Visual, escolha **Build** > **Build Solution**.
2. **Inicie a sua aplicação** - A partir da barra de menus, escolha **Debug** > **Começar dedepuração** ou prima **F5**.
3. **Comece a reconhecer** - Vai instá-lo a falar uma frase em inglês. O seu discurso é enviado para o serviço da Fala, transcrito como texto, e renderizado na consola.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
