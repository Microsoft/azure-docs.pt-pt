---
title: Codificar um ficheiro remoto e transmitir utilizando o Azure Media Services v3
description: Siga os passos deste tutorial para codificar um ficheiro baseado num URL e transmitir o seu conteúdo com a Azure Media Services utilizando o REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/12/2020
ms.author: inhenkel
ms.openlocfilehash: c1798ca74493ba22d29cd9ce819d469c29cd5ec3
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2021
ms.locfileid: "98059590"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---rest"></a>Tutorial: Encode a remote file based on URL and stream the video - REST (Codificar ficheiros remotos com base no URL e transmitir o vídeo em fluxo - REST)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

O Azure Media Services permite-lhe codificar os seus ficheiros de mídia em formatos que podem ser reproduzidos numa grande variedade de navegadores e dispositivos. Por exemplo, pode querer transmitir o conteúdo nos formatos HLS ou MPEG DASH da Apple. Antes de transmissão, deve codificar o ficheiro de multimédia digital de alta qualidade. Para obter orientações sobre a codificação, veja [Conceito de codificação](encoding-concept.md).

Este tutorial mostra-lhe como codificar um ficheiro com base num URL e transmitir o vídeo com o Azure Media Services utilizando o REST. 

![Reproduzir o vídeo](./media/stream-files-tutorial-with-api/final-video.png)

Este tutorial mostrar-lhe como:    

> [!div class="checklist"]
> * Criar uma conta dos Media Services
> * Aceder à API dos Serviços de Multimédia
> * Transferir ficheiros do Postman
> * Configurar o Postman
> * Enviar pedidos através do Postman
> * Testar o URL de transmissão em fluxo
> * Limpar os recursos

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social.

    Certifique-se de que se lembra dos valores que utilizou para o nome do grupo de recursos e nome da conta dos Media Services

- Instale o cliente REST do [Postman](https://www.getpostman.com/) para executar as API REST mostradas em alguns dos tutoriais sobre AMS REST. 

    Estamos a utilizar o **Postman**, mas qualquer ferramenta REST seria adequada. Outras alternativas são: **Código de Estúdio Visual** com o plugin REST ou o **Fiddler Telerik**. 

## <a name="download-postman-files"></a>Transferir ficheiros do Postman

Clone o repositório do GitHub que contém os ficheiros de ambiente e coleção do Postman.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="access-api"></a>API de acesso

Para obter informações detalhadas, consulte [obter credenciais para aceder à API dos Serviços de Mídia](access-api-howto.md)

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

7. Faça duplo clique no ficheiro selecionado e introduza os valores que obteve após seguir os passos de [Acesso à API](#access-api).
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
4. Criar um trabalho
5. Criar um localizador de streaming
6. Listar caminhos do localizador de streaming

> [!Note]
>  Este tutorial assume que está a criar todos os recursos com nomes exclusivos.  

### <a name="get-azure-ad-token"></a>Obter o Token do Microsoft Azure AD 

1. Na janela esquerda da aplicação Postman, selecione "Passo 1: Obter ficha AAD Auth".
2. Em seguida, selecione "Obter o Token do Microsoft Azure AD para Autenticação Principal de Serviço".
3. Prima **Enviar**.

    A seguinte operação **POST** é enviada.

    ```
    https://login.microsoftonline.com/:aadTenantDomain/oauth2/token
    ```

4. A resposta volta com o token e define a variável de ambiente "AccessToken" como o valor de token. Para ver o código que define "AccessToken", clique no separador **Testes**. 

    ![Obter token do AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)


### <a name="start-a-streaming-endpoint"></a>Iniciar um ponto final de streaming

Para ativar o streaming, primeiro tem de iniciar o [Streaming Endpoint](./streaming-endpoint-concept.md) a partir do qual pretende transmitir o vídeo.

> [!NOTE]
> Só é cobrado quando o seu Streaming Endpoint está no estado de funcionamento.

1. Na janela esquerda da aplicação Postman, selecione "Streaming e Live".
2. Em seguida, selecione "Start StreamingEndpoint".
3. Prima **Enviar**.

    * É enviada a seguinte operação **POST:**

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaservices/:accountName/streamingEndpoints/:streamingEndpointName/start?api-version={{api-version}}
        ```
    * Se o pedido for bem sucedido, o `Status: 202 Accepted` é devolvido.

        Este estatuto significa que o pedido foi aceite para processamento; no entanto, o processamento não foi concluído. Pode consultar o estado de funcionamento com base no valor do `Azure-AsyncOperation` cabeçalho de resposta.

        Por exemplo, a seguinte operação GET devolve o estado da sua operação:
        
        `https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<resourceGroupName>/providers/Microsoft.Media/mediaservices/<accountName>/streamingendpointoperations/1be71957-4edc-4f3c-a29d-5c2777136a2e?api-version=2018-07-01`

        O artigo [de operações assíncronas da Azure](../../azure-resource-manager/management/async-operations.md) explica em profundidade como acompanhar o estado das operações assíncronas do Azure através de valores devolvidos na resposta.

### <a name="create-an-output-asset"></a>Criar um elemento de saída

A saída [Asset](/rest/api/media/assets) armazena o resultado da tarefa de codificação. 

1. Na janela esquerda da aplicação Do Carteiro, selecione "Ativos".
2. Em seguida, selecione "Criar ou atualizar um Elemento".
3. Prima **Enviar**.

    * É enviada a seguinte operação **PUT:**

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/assets/:assetName?api-version={{api-version}}
        ```
    * A operação tem o corpo seguinte:

        ```json
        {
        "properties": {
            "description": "My Asset",
            "alternateId" : "some GUID",
            "storageAccountName": "<replace from environment file>",
            "container": "<supply any valid container name of your choosing>"
         }
        }
        ```

> [!NOTE]
> Certifique-se de que substitui a conta de armazenamento e os nomes dos recipientes, quer pelos do ficheiro ambiente, quer forneça o seu próprio.
>
> Ao completar os passos descritos no resto deste artigo, certifique-se de que fornece parâmetros válidos nos corpos solicitados.

### <a name="create-a-transform"></a>Criar uma transformação

Ao codificar ou processar conteúdos nos Serviços de Multimédia, é um padrão comum configurar as definições de codificação como uma receita. Em seguida, deverá submeter uma **Tarefa** para aplicar essa receita a um vídeo. Ao apresentar novos empregos para cada novo vídeo, está a aplicar essa receita em todos os vídeos da sua biblioteca. Uma receita nos Serviços de Multimédia chama-se uma **Transformação**. Para obter mais informações, veja [Transforms and Jobs](./transforms-jobs-concept.md) (Transformações e Trabalhos). O exemplo descrito neste tutorial define uma receita que codifica o vídeo para transmiti-lo numa variedade de dispositivos iOS e Android. 

Ao criar uma nova instância [Transformação](/rest/api/media/transforms), tem de especificar o que pretende produzir como uma saída. O parâmetro necessário é um objeto **TransformOutput**. Cada **TransformOutput** contém uma **Predefinição**. A **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que estão a ser utilizadas para gerir o **TransformOutput** pretendido. O exemplo descrito neste artigo utiliza uma Predefinição incorporada chamada **AdaptiveStreaming**. A Predefinição codifica o vídeo de entrada para uma escala de bits gerada automaticamente (pares de resolução/velocidade de transmissão) com base na resolução e velocidade de transmissão de entrada e produz ficheiros ISO MP4 com vídeo H.264 e áudio AAC correspondente a cada par de resolução/velocidade de transmissão. Para obter informações sobre esta Predefinição, veja [Auto-generating bitrate ladder](autogen-bitrate-ladder.md) (Escala de bits gerada automaticamente).

Pode utilizar um EncoderNamedPreset incorporadi ou utilizar as predefinições personalizadas. 

> [!Note]
> Ao criar uma [Transformação](/rest/api/media/transforms), tem de verificar primeiro se já existe uma utilização o método **Obter**. Este tutorial assume que está a criar a transformação com um nome exclusivo.

1. Na janela esquerda da aplicação Postman, selecione "Codificação e Análise".
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

Uma [Tarefa](/rest/api/media/jobs) é o pedido atual para os Serviços de Multimédia aplicarem a **Transformação** criada a determinado conteúdo de vídeo ou áudio de entrada. A **Tarefa** especifica informações como a localização do vídeo de entrada e a localização da saída.

Neste exemplo, a entrada do trabalho baseia-se num URL HTTPS ("https: \/ /nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/").

1. Na janela esquerda da aplicação Postman, selecione "Codificação e Análise".
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

Normalmente, a **Tarefa** passa pelos seguintes estados: **Agendada**, **Em fila**, **Em processamento**, **Concluída** (o estado final). Se a tarefa encontrar um erro, obterá um estado de **Erro**. Se a tarefa estiver a ser cancelada, verá **A cancelar** e **Cancelada** quando terminar.

#### <a name="job-error-codes"></a>Códigos de erro das tarefas

Ver [códigos de erro](/rest/api/media/jobs/get#joberrorcode).

### <a name="create-a-streaming-locator"></a>Criar um localizador de transmissão

Após o trabalho de codificação estar concluído, o próximo passo é disponibilizar o vídeo na saída **Ativo** aos clientes para reprodução. Pode conseguir isto em dois passos: primeiro, crie um [StreamingLocator](/rest/api/media/streaminglocators) e, segundo, crie os URLs de transmissão em fluxo que os clientes podem utilizar. 

O processo de criação de um localizador de streaming chama-se publicação. Por predefinição, o localizador de streaming é válido imediatamente após a edição da API e dura até ser eliminado, a menos que configuure os tempos de início e fim opcionais. 

Ao criar um [StreamingLocator](/rest/api/media/streaminglocators), tem de especificar o **StreamingPolicyName** pretendido. Neste exemplo, estará a transmitir conteúdo claro (ou não encriptado), pelo que é utilizada a política de streaming "Predefined_ClearStreamingOnly" predefinida.

> [!IMPORTANT]
> Quando utilizar uma [StreamingPolicy](/rest/api/media/streamingpolicies) personalizada, deve conceber um conjunto limitado dessas políticas para a sua conta dos Serviços de Multimédia e utilizá-las novamente para os StreamingLocators sempre que são necessárias as mesmas opções de encriptação e os mesmos protocolos. 

A sua conta de Media Service tem uma quota para o número de entradas na **Política de Streaming.** Não deve criar uma nova **Política de Streaming** para cada localizador de streaming.

1. Na janela esquerda da aplicação Postman, selecione "Streaming Policies and Locators".
2. Em seguida, selecione "Criar um localizador de streaming (claro)".
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

Agora que o [Localizador de Streaming](/rest/api/media/streaminglocators) foi criado, você pode obter os URLs de streaming

1. Na janela esquerda da aplicação Carteiro, selecione "Políticas de Streaming".
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
    https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaservices/:accountName/streamingEndpoints/default?api-version={{api-version}}
    ```
    e certifique-se de que define os `resourceGroupName` parâmetros e `accountName` parâmetros para combinar com o ficheiro ambiental. 
    
3. Um caminho que obteve na secção anterior (Caminhos da lista).  

Como resultado, foi criado o seguinte URL HLS

```
https://amsaccount-usw22.streaming.media.azure.net/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)
```

## <a name="test-the-streaming-url"></a>Testar o URL de transmissão em fluxo


> [!NOTE]
> Certifique-se de que o **Ponto Final** de Streaming a partir do qual pretende transmitir está a funcionar.

Para testar a transmissão, este artigo utiliza o Leitor de Multimédia do Azure. 

1. Abra um navegador web e navegue para [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. Na caixa **URL:**, cole o URL criado. 
3. Prima **Atualizar Leitor**.

O Leitor de Multimédia do Azure pode ser utilizado para fins de teste, mas não deve ser utilizado num ambiente de produção. 

## <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos na conta dos Serviços de Multimédia

Geralmente, deve limpar tudo, exceto objetos que está a planear reutilizar (normalmente, reutilizar **Transforms**, e persistirá **em Streaming Localizadores,** etc.). Se quiser que a sua conta seja limpa após fazer experiências, deverá eliminar os recursos que não planeia reutilizar.  

Para eliminar um recurso, selecione a operação "Eliminar…" sob o recurso que pretende eliminar.

## <a name="clean-up-resources"></a>Limpar os recursos

Se já não precisa de nenhum dos recursos presentes no seu grupo de recursos, incluindo as contas de armazenamento que criou e os Serviços de Multimédia que carregou neste tutorial, elimine o grupo de recursos que criou anteriormente.  

Execute o seguinte comando CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="next-steps"></a>Passos seguintes

Agora que sabe como carregar, codificar e transmitir o vídeo, veja o artigo seguinte: 

> [!div class="nextstepaction"]
> [Analisar vídeos](analyze-videos-tutorial-with-api.md)
