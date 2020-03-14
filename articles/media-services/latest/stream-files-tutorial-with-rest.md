---
title: Codificar um ficheiro remoto e transmitir utilizando o Azure Media Services v3
description: Siga os passos deste tutorial para codificar um ficheiro com base num URL e transmitir o seu conteúdo com o Azure Media Services utilizando o REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/05/2019
ms.author: juliako
ms.openlocfilehash: d4175f2508edab1cf54e415652e9e9cb37b879b1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79238637"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---rest"></a>Tutorial: Codificar um ficheiro remoto com base em URL e transmitir o vídeo - REST

O Azure Media Services permite-lhe codificar os seus ficheiros de mídia em formatos que podem ser reproduzidos numa grande variedade de navegadores e dispositivos. Por exemplo, pode querer transmitir o conteúdo nos formatos HLS ou MPEG DASH da Apple. Antes de transmissão, deve codificar o ficheiro de multimédia digital de alta qualidade. Para obter orientações sobre a codificação, veja [Conceito de codificação](encoding-concept.md).

Este tutorial mostra-lhe como codificar um ficheiro com base num URL e transmitir o vídeo com a Azure Media Services utilizando o REST. 

![Reproduzir o vídeo](./media/stream-files-tutorial-with-api/final-video.png)

Este tutorial mostrar-lhe como:    

> [!div class="checklist"]
> * Criar uma conta dos Media Services
> * Aceder à API dos Serviços de Multimédia
> * Transferir ficheiros do Postman
> * Configurar o Postman
> * Enviar pedidos através do Postman
> * Testar o URL de transmissão em fluxo
> * Limpar recursos

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta de Media Services.](create-account-cli-how-to.md)

    Lembre-se dos valores que utilizou para o nome do grupo de recursos e nome da conta Media Services

- Instale o cliente REST do [Postman](https://www.getpostman.com/) para executar as API REST mostradas em alguns dos tutoriais sobre AMS REST. 

    Estamos a utilizar o **Postman**, mas qualquer ferramenta REST seria adequada. Outras alternativas são: **Visual Studio Code** com o plug-in REST ou **Telerik Fiddler**. 

## <a name="download-postman-files"></a>Transferir ficheiros do Postman

Clone o repositório do GitHub que contém os ficheiros de ambiente e coleção do Postman.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="configure-postman"></a>Configurar o Postman

### <a name="configure-the-environment"></a>Configurar o ambiente 

1. Abra a aplicação **do Carteiro.**
2. À direita do ecrã, selecione a opção **Gerir ambiente**.

    ![Gerir ambiente](./media/develop-with-postman/postman-import-env.png)
4. Na caixa de diálogo **Gerir ambiente**, clique em **Importar**.
2. Navegue até ao ficheiro `Azure Media Service v3 Environment.postman_environment.json` que foi transferido quando clonou `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. O ambiente **Ambiente v3 do Serviço de Multimédia do Azure** é adicionado.

    > [!Note]
    > Atualize as variáveis de acesso com valores obtidos na secção **Aceder à API dos Serviços de Multimédia** acima.

7. Faça duplo clique no ficheiro selecionado e introduza os valores que obteve após seguir os passos de [Acesso à API](#access-the-media-services-api).
8. Feche a caixa de diálogo.
9. Selecione o **Ambiente v3 do Serviço de Multimédia do Azure** na lista pendente.

    ![Escolher ambiente](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Configurar a coleção

1. Clique em **Importar** para importar o ficheiro de coleção.
1. Navegue até ao ficheiro `Media Services v3.postman_collection.json` que foi transferido quando clonou `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`
3. Escolha o ficheiro **Media Services v3.postman_collection.json**.

    ![Importar um ficheiro](./media/develop-with-postman/postman-import-collection.png)

## <a name="send-requests-using-postman"></a>Enviar pedidos através do Postman

Nesta secção, enviamos pedidos que são relevantes para a codificação e criação de URL para que possa transmitir o seu ficheiro. Especificamente, são enviados os seguintes pedidos:

1. Obter o Token do Microsoft Azure AD para Autenticação Principal de Serviço
1. Iniciar um ponto final de streaming
2. Criar um elemento de saída
3. Criar uma Transformação
4. Criar um Trabalho
5. Criar um localizador de streaming
6. Lista de caminhos do Localizador de Streaming

> [!Note]
>  Este tutorial assume que está a criar todos os recursos com nomes exclusivos.  

### <a name="get-azure-ad-token"></a>Obter o Token do Microsoft Azure AD 

1. Na janela esquerda da aplicação Postman, selecione "Step 1: Get AAD Auth token".
2. Em seguida, selecione "Obter o Token do Microsoft Azure AD para Autenticação Principal de Serviço".
3. Prima **Enviar**.

    A seguinte operação **POST** é enviada.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. A resposta volta com o token e define a variável de ambiente "AccessToken" como o valor de token. Para ver o código que define "AccessToken", clique no separador **Testes**. 

    ![Obter token do AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)


### <a name="start-a-streaming-endpoint"></a>Iniciar um ponto final de streaming

Para permitir o streaming, primeiro tem de iniciar o [Streaming Endpoint](https://docs.microsoft.com/azure/media-services/latest/streaming-endpoint-concept) a partir do qual pretende transmitir o vídeo.

> [!NOTE]
> Só é cobrado quando o seu Ponto Final de Streaming estiver em estado de corrida.

1. Na janela esquerda da aplicação Postman, selecione "Streaming e Live".
2. Em seguida, selecione "Iniciar o StreamingEndpoint".
3. Prima **Enviar**.

    * É enviada a seguinte operação **POST:**

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaservices/:accountName/streamingEndpoints/:streamingEndpointName/start?api-version={{api-version}}
        ```
    * Se o pedido for bem sucedido, o `Status: 202 Accepted` é devolvido.

        Este estatuto significa que o pedido foi aceite para processamento; no entanto, o processamento não foi concluído. Pode consultar o estado de funcionamento com base no valor do cabeçalho de resposta `Azure-AsyncOperation`.

        Por exemplo, a seguinte operação GET devolve o estado da sua operação:
        
        `https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<resourceGroupName>/providers/Microsoft.Media/mediaservices/<accountName>/streamingendpointoperations/1be71957-4edc-4f3c-a29d-5c2777136a2e?api-version=2018-07-01`

        O artigo de [operações assíncronos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) da pista do Azure explica em profundidade como acompanhar o estado das operações assíncronas do Azure através de valores devolvidos na resposta.

### <a name="create-an-output-asset"></a>Criar um elemento de saída

A saída [Asset](https://docs.microsoft.com/rest/api/media/assets) armazena o resultado da tarefa de codificação. 

1. Na janela esquerda da aplicação Postman, selecione "Assets".
2. Em seguida, selecione "Criar ou atualizar um Elemento".
3. Prima **Enviar**.

    * A operação **PUT** seguinte é enviada:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/assets/:assetName?api-version={{api-version}}
        ```
    * A operação tem o corpo seguinte:

        ```json
        {
        "properties": {
            "description": "My Asset",
            "alternateId" : "some GUID"
         }
        }
        ```

### <a name="create-a-transform"></a>Criar uma transformação

Ao codificar ou processar conteúdos nos Serviços de Multimédia, é comum configurar as definições de codificação como uma receita. Em seguida, deverá submeter uma **Tarefa** para aplicar essa receita a um vídeo. Ao submeter novos empregos para cada novo vídeo, está a aplicar essa receita a todos os vídeos da sua biblioteca. Uma receita nos Serviços de Multimédia chama-se uma **Transformação**. Para obter mais informações, veja [Transforms and Jobs](transform-concept.md) (Transformações e Trabalhos). O exemplo descrito neste tutorial define uma receita que codifica o vídeo para transmiti-lo numa variedade de dispositivos iOS e Android. 

Ao criar uma nova instância [Transformar](https://docs.microsoft.com/rest/api/media/transforms), tem de especificar a saída resultante que pretende. O parâmetro necessário é um objeto **TransformOutput**. Cada **TransformOutput** contém uma **Predefinição**. A **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que estão a ser utilizadas para gerir o **TransformOutput** pretendido. O exemplo descrito neste artigo utiliza uma Predefinição incorporada chamada **AdaptiveStreaming**. A Predefinição codifica o vídeo de entrada para uma escala de bits gerada automaticamente (pares de resolução/velocidade de transmissão) com base na resolução e velocidade de transmissão de entrada e produz ficheiros ISO MP4 com vídeo H.264 e áudio AAC correspondente a cada par de resolução/velocidade de transmissão. Para obter informações sobre esta Predefinição, veja [Auto-generating bitrate ladder](autogen-bitrate-ladder.md) (Escala de bits gerada automaticamente).

Pode utilizar um EncoderNamedPreset incorporadi ou utilizar as predefinições personalizadas. 

> [!Note]
> Ao criar uma [Transformação](https://docs.microsoft.com/rest/api/media/transforms), tem de verificar primeiro se já existe uma utilização o método **Obter**. Este tutorial assume que está a criar a transformação com um nome exclusivo.

1. Na janela esquerda da aplicação Postman, selecione "Encoding and Analysis".
2. Em seguida, selecione "Criar Transformação".
3. Prima **Enviar**.

    * A seguinte operação **PUT** é enviada.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
        ```
    * A operação tem o corpo seguinte:

        ```json
        {
            "properties": {
                "description": "Standard Transform using an Adaptive Streaming encoding preset from the library of built-in Standard Encoder presets",
                "outputs": [
                    {
                    "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                    "preset": {
                        "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                        "presetName": "AdaptiveStreaming"
                    }
                    }
                ]
            }
        }
        ```

### <a name="create-a-job"></a>Criar uma tarefa

Uma [Tarefa](https://docs.microsoft.com/rest/api/media/jobs) é o pedido atual para os Serviços de Multimédia aplicarem a **Transformação** criada a determinado conteúdo de vídeo ou áudio de entrada. A **Tarefa** especifica informações como a localização do vídeo de entrada e a localização para a saída.

Neste exemplo, a entrada do trabalho baseia-se num URL HTTPS ("https:\//nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/").

1. Na janela esquerda da aplicação Postman, selecione "Encoding and Analysis".
2. Em seguida, selecione "Criar ou Atualizar Tarefa".
3. Prima **Enviar**.

    * A seguinte operação **PUT** é enviada.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
        ```
    * A operação tem o corpo seguinte:

        ```json
        {
        "properties": {
            "input": {
            "@odata.type": "#Microsoft.Media.JobInputHttp",
            "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
            "files": [
                    "Ignite-short.mp4"
                ]
            },
            "outputs": [
            {
                "@odata.type": "#Microsoft.Media.JobOutputAsset",
                "assetName": "testAsset1"
            }
            ]
        }
        }
        ```

A tarefa demora algum tempo a terminar, por isso irá querer receber uma notificação quando for concluída. Para ver o progresso do trabalho, recomendamos a utilização do Event Grid. Foi concebido para ter uma elevada disponibilidade, um desempenho consistente e um dimensionamento dinâmico. Com o Event Grid, as aplicações podem escutar e reagir a eventos a partir de praticamente todos os serviços do Azure, bem como de origens personalizadas. O processamento de eventos simples, reativo e baseado em HTTP ajuda-o a criar soluções eficientes através da filtragem e do encaminhamento inteligente de eventos.  Veja [Route events to a custom web endpoint](job-state-events-cli-how-to.md) (Encaminhar eventos para um ponto final de Web personalizado).

Normalmente, a **Tarefa** passa pelos seguintes estados: **Agendada**, **Em fila**, **Em processamento**, **Concluída** (o estado final). Se a tarefa encontrar um erro, obterá um estado de **Erro**. Se a tarefa estiver prestes a ser cancelada, obterá **A cancelar** e **Cancelada** quando terminar.

#### <a name="job-error-codes"></a>Códigos de erro das tarefas

Ver [Códigos de Erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="create-a-streaming-locator"></a>Criar um localizador de transmissão

Depois de concluído o trabalho de codificação, o próximo passo é disponibilizar o vídeo na saída **Do Ativo** aos clientes para reprodução. Pode conseguir isto em dois passos: primeiro, crie um [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) e, segundo, crie os URLs de transmissão em fluxo que os clientes podem utilizar. 

O processo de criação de um localizador de streaming chama-se publicação. Por predefinição, o localizador de streaming é válido imediatamente após a efetuação das chamadas API, e dura até que seja eliminado, a menos que configure os tempos de início e fim opcionais. 

Ao criar um [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), tem de especificar o **StreamingPolicyName** pretendido. Neste exemplo, irá transmitir conteúdo in-the-clear (ou não encriptado), pelo que é utilizada a política de streaming clara predefinida "Predefined_ClearStreamingOnly".

> [!IMPORTANT]
> Quando utilizar uma [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizada, deve conceber um conjunto limitado dessas políticas para a sua conta dos Serviços de Multimédia e utilizá-las novamente para os StreamingLocators sempre que são necessárias as mesmas opções de encriptação e os mesmos protocolos. 

A sua conta de Serviço de Media tem uma quota para o número de entradas de Política de **Streaming.** Não deve criar uma nova Política de Streaming para cada localizador de **streaming.**

1. Na janela esquerda da aplicação Postman, selecione "Streaming Policies and Locators".
2. Em seguida, selecione "Criar um Localizador de Streaming (claro)".
3. Prima **Enviar**.

    * A seguinte operação **PUT** é enviada.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName?api-version={{api-version}}
        ```
    * A operação tem o corpo seguinte:

        ```json
        {
          "properties": {
            "streamingPolicyName": "Predefined_ClearStreamingOnly",
            "assetName": "testAsset1",
            "contentKeys": [],
            "filters": []
         }
        }
        ```

### <a name="list-paths-and-build-streaming-urls"></a>Listar caminhos e criar URL de transmissão

#### <a name="list-paths"></a>Listar caminhos

Agora que o Localizador de [Streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) foi criado, você pode obter os URLs de streaming

1. Na janela esquerda da aplicação Postman, selecione "Streaming Policies".
2. Em seguida, selecione "Listar Caminhos".
3. Prima **Enviar**.

    * A seguinte operação **POST** é enviada.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName/listPaths?api-version={{api-version}}
        ```
        
    * A operação não tem corpo:
        
4. Anote um dos caminhos que pretende utilizar para transmissão pois utilizá-lo-á na próxima secção. Neste caso, foram devolvidos os seguintes caminhos:
    
    ```
    "streamingPaths": [
        {
            "streamingProtocol": "Hls",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)"
            ]
        },
        {
            "streamingProtocol": "Dash",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=mpd-time-csf)"
            ]
        },
        {
            "streamingProtocol": "SmoothStreaming",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest"
            ]
        }
    ]
    ```

#### <a name="build-the-streaming-urls"></a>Criar os URL de transmissão

Nesta secção, criemos um URL de transmissão HLS. Os URL são compostos pelos seguintes valores:

1. O protocolo através do qual os dados são enviados. Neste caso, "https".

    > [!NOTE]
    > Se um leitor estiver alojado num site de https, confirme que atualiza o URL para “https”.

2. Nome do anfitrião do StreamingEndpoint. Neste caso, o nome é "amsaccount-usw22.streaming.media.azure.net".

    Para obter o nome de anfitrião, pode utilizar a seguinte operação GET:
    
    ```
    https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingEndpoints/default?api-version={{api-version}}
    ```
    
3. Um caminho que obteve na secção anterior (Caminhos da lista).  

Como resultado, foi criado o seguinte URL HLS

```
https://amsaccount-usw22.streaming.media.azure.net/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)
```

## <a name="test-the-streaming-url"></a>Testar o URL de transmissão em fluxo


> [!NOTE]
> Certifique-se de que o **ponto final** de streaming a partir do qual pretende transmitir está em execução.

Para testar a transmissão, este artigo utiliza o Leitor de Multimédia do Azure. 

1. Abra um browser e navegue para [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Na caixa **URL:** , cole o URL criado. 
3. Prima **Atualizar Leitor**.

O Leitor de Multimédia do Azure pode ser utilizado para fins de teste, mas não deve ser utilizado num ambiente de produção. 

## <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos na conta dos Serviços de Multimédia

Geralmente, deve limpar tudo, exceto objetos que planeia reutilizar (normalmente, irá reutilizar **Transforms**, e persistirá localizadores de **streaming,** etc.). Se quiser que a sua conta seja limpa após fazer experiências, deverá eliminar os recursos que não planeia reutilizar.  

Para eliminar um recurso, selecione a operação "Eliminar…" sob o recurso que pretende eliminar.

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar de nenhum dos recursos presentes no seu grupo de recursos, incluindo a conta dos Serviços de Multimédia e a conta de armazenamento que criou para este tutorial, elimine o grupo de recursos anteriormente criado.  

Executar o seguinte comando CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="next-steps"></a>Passos seguintes

Agora que sabe como carregar, codificar e transmitir o vídeo, veja o artigo seguinte: 

> [!div class="nextstepaction"]
> [Analisar vídeos](analyze-videos-tutorial-with-api.md)
