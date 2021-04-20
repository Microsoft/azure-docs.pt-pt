---
title: Stream ao vivo com Os Serviços de Media v3 Node.js
titleSuffix: Azure Media Services
description: Aprenda a transmitir ao vivo usando Node.js.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc, devx-track-nodejs
ms.date: 04/15/2021
ms.author: inhenkel
ms.openlocfilehash: 749d2fc845f036a2802c80c161b3fc8c171c2555
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730541"
---
# <a name="tutorial-stream-live-with-media-services-using-nodejs-and-typescript"></a>Tutorial: Stream ao vivo com serviços de mídia usando Node.js e TypeScript

> [!NOTE]
> Embora o tutorial utilize Node.js exemplos, os passos gerais são os mesmos para [REST API,](/rest/api/media/liveevents) [CLI,](/cli/azure/ams/live-event)ou [outros SDKs suportados.](media-services-apis-overview.md#sdks) 

Na Azure Media Services, [os Live Events](/rest/api/media/liveevents) são responsáveis pelo processamento de conteúdos de streaming ao vivo. Um Evento Ao Vivo fornece um ponto final de entrada (ingest URL) que você fornece a um codificadores ao vivo. O Live Event recebe streams de entrada ao vivo a partir do codificadores ao vivo e disponibiliza-o para streaming através de um ou mais [Pontos De Streaming](/rest/api/media/streamingendpoints). Os Live Events também fornecem um ponto final de pré-visualização (URL de pré-visualização) que utiliza para visualizar e validar o seu fluxo antes de ser processado e entregue. Este tutorial mostra como usar Node.js para criar **um** tipo de evento ao vivo e transmitir um live stream para ele usando [o OBS Studio](https://obsproject.com/download).

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Descarregue o código de amostra descrito no tópico.
> * Examine o código que configura e executa o streaming ao vivo.
> * Assista ao evento com [o Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) em [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net) .
> * Limpe os recursos.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

São necessários os itens seguintes para concluir o tutorial:

- Instalar [Node.js](https://nodejs.org/en/download/)
- Instalar [TypeScript](https://www.typescriptlang.org/)
- [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social.<br/>Lembre-se dos valores que utilizou para o nome do grupo de recursos e nome da conta dos Media Services.
- Siga os passos na [Access Azure Media Services API com o Azure CLI](./access-api-howto.md) e guarde as credenciais. Terá de usá-las para aceder à API e configurar o ficheiro de variáveis ambientais.
- Caminhe pelo [Configure e Conecte-se com Node.js](./configure-connect-nodejs-howto.md) primeiro para entender como usar o Node.js cliente SDK
- Instale o Visual Studio Code ou o Visual Studio.
- [Configurar o seu ambiente visual Studio Code](https://code.visualstudio.com/Docs/languages/typescript) para suportar o idioma TypeScript.

## <a name="additional-settings-for-live-streaming-software"></a>Definições adicionais para software de streaming ao vivo

- Uma câmara ou um dispositivo (como um portátil) que é usado para transmitir um evento.
- Um codificadora de software no local que codifica o fluxo de câmaras e o envia para o serviço de streaming ao vivo dos Media Services utilizando o protocolo RTMP, consulte [codificadores ao vivo recomendados no local.](encode-recommended-on-premises-live-encoders.md) O fluxo tem de ser em formato **RTMP** ou de **transmissão em fluxo uniforme**.  
- Para esta amostra, recomenda-se começar com um codificador de software como o [Open Broadcast Software OBS Studio](https://obsproject.com/download) para tornar simples o arranque.

Esta amostra pressupõe que você usará o OBS Studio para transmitir RTMP para o ponto final de ingestão. Instale primeiro o OBS Studio.
Utilize as seguintes definições de codificação no ESTÚDIO OBS:

- Codificação: NVIDIA NVENC (se disponível) ou x264
- Controlo de tarifas: CBR
- Bitrate: 2500 Kbps (ou algo razoável para o seu portátil)
- Intervalo do quadro-chave: 2 s, ou 1 s para baixa latência  
- Predefinição: Qualidade ou desempenho de baixa latência (NVENC) ou "muito rápido" utilizando x264
- Perfil: alto
- GPU: 0 (Auto)
- Quadros Max B: 2

> [!TIP]
> Certifique-se de que revê [Transmissão em fluxo em direto com os Serviços de Multimédia v3](stream-live-streaming-concept.md) antes de continuar.

## <a name="download-and-configure-the-sample"></a>Descarregue e configuure a amostra

Clone o seguinte repositório Git Hub que contém a amostra de streaming ao vivo Node.js para a sua máquina utilizando o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

O exemplo de transmissão em fluxo em direto está localizado na pasta [Dinâmica](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live).

Na pasta [AMSv3Samples](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples) copia o ficheiro denominado "sample.env" para um novo ficheiro chamado ".env" para armazenar as definições variáveis do seu ambiente que recolheu no artigo [Access Azure Media Services API com o Azure CLI](./access-api-howto.md).
Certifique-se de que o ficheiro inclui o "ponto" (.) em frente a .env" para que este funcione corretamente com a amostra de código.

O [ficheiro .env](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/sample.env) contém a sua chave de aplicação AAD e segredo, juntamente com o nome da conta e informações de subscrição necessárias para autenticar o acesso da SDK à sua conta de Media Services. O ficheiro .gitignore já está configurado para evitar a publicação deste ficheiro no seu repositório forcado. Não permita que estas credenciais sejam divulgadas, pois são segredos importantes para a sua conta.

> [!IMPORTANT]
> Esta amostra utiliza um sufixo único para cada recurso. Se cancelar a depurar ou terminar a aplicação sem a executar, acabará com vários Eventos Ao Vivo na sua conta. <br/>Certifique-se de parar os eventos ao vivo. Caso contrário, será **cobrado!** Executar o programa até à conclusão para limpar automaticamente os recursos. Se o programa falhar, ou parar inadvertidamente o depurar e sair da execução do programa, deverá verificar duas vezes o portal para confirmar que não deixou nenhum evento ao vivo nos estados de Running ou Stand-by que resultariam em taxas de faturação indesejadas.

## <a name="examine-the-typescript-code-for-live-streaming"></a>Examine o código TypeScript para transmissão em direto

Esta secção examina as funções definidas no ficheiro [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/Live/index.ts) do projeto *Live.*

A amostra cria um sufixo único para cada recurso para que não tenha colisões de nomes se executar a amostra várias vezes sem limpar.

### <a name="start-using-media-services-sdk-for-nodejs-with-typescript"></a>Comece a usar o SDK dos Serviços de Mídia para Node.js com o TypeScript

Para começar a utilizar APIs de Serviços de Mídia com Node.js, é necessário primeiro adicionar o [@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices) módulo SDK utilizando o gestor de pacotes npm

```bash
npm install @azure/arm-mediaservices
```

No package.js, isto já está configurado para si, pelo que basta executar a *instalação npm* para carregar os módulos e dependências.

1. Abra um **pedido de comando,** navegue no diretório da amostra.
1. Mude o diretório para a pasta AMSv3Samples.

    ```bash
    cd AMSv3Samples
    ```

1. Instale as embalagens utilizadas no *packages.jsno* ficheiro.

    ```bash
    npm install 
    ```

1. Lançar Código de Estúdio Visual a partir da Pasta *AMSv3Samples.* (Isto é necessário para ser lançado a partir da pasta onde se encontra a pasta *.vscode* e *tsconfig.jsnos* ficheiros.)

    ```bash
    cd ..
    code .
    ```

Abra a pasta para *Live* e abra o ficheiro *index.ts* no editor visual Studio Code.
Enquanto está no ficheiro *index.ts,* prima F5 para lançar o depurar.

### <a name="create-the-media-services-client"></a>Criar o cliente dos Serviços de Mídia

O seguinte código snippet mostra como criar o cliente dos Serviços de Mídia em Node.js.
Note que neste código estamos primeiro a definir a propriedade **longRunningOperationRetryTimeout** das AzureMediaServicesOptions para reduzir o tempo que leva para a pesquisa para o estado de uma operação de longa duração no ponto final de Gestão de Recursos Azure.  Uma vez que a maioria das operações em Live Events serão assíncronos, e pode levar algum tempo a concluir, você deve reduzir este intervalo de votação no SDK do valor padrão de 30 segundos para acelerar o tempo que leva para completar grandes operações como criar Eventos Ao Vivo, Iniciar e Parar que são todas chamadas assíncronos. Dois segundos é o valor recomendado para a maioria dos cenários de utilização.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Criar um evento em direto

Esta secção mostra como criar um tipo de evento live(LiveEventEncodType definido para Nenhum).  Para obter mais informações sobre os outros tipos disponíveis de Eventos Ao Vivo, consulte [os tipos de Eventos Ao Vivo.](live-event-outputs-concept.md#live-event-types) Além de passar, pode utilizar um Evento Ao Vivo de transcoding ao vivo para codificação de nuvem bitrate adaptativa de 720P ou 1080P.
 
Algumas coisas que você pode querer especificar ao criar o evento ao vivo são:

* O protocolo de ingestão para o Live Event (atualmente, os protocolos RTMP(S) e Smooth Streaming são suportados).<br/>Não é possível alterar a opção de protocolo enquanto o Live Event ou as suas saídas ao vivo associadas estiverem em execução. Se necessitar de protocolos diferentes, crie evento ao vivo separado para cada protocolo de streaming.  
* Restrições de IP na ingestão e na pré-visualização. Pode definir os endereços IP que podem ingerir um vídeo neste Evento Ao Vivo. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo "10.0.0.1"), um intervalo de IP com um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22') ou um intervalo de IP com um endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo , ' 10.0.0.1(255.255.252.0)').<br/>Se não forem especificados endereços IP e não houver definição de regra, nenhum endereço IP será permitido. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.<br/>Os endereços IP têm de estar num dos seguintes formatos: endereço IpV4 com quatro números ou intervalo de endereços CIDR.
* Ao criar o evento, pode especificar para o iniciar automaticamente. <br/>Quando o arranque automático estiver definido para ser verdade, o Live Event será iniciado após a criação. Isso significa que a faturação começa assim que o Evento Ao Vivo começa a funcionar. Tem de ligar explicitamente para parar o recurso Live Event para travar a faturação adicional. Para mais informações, consulte [os estados do Live Event e a faturação.](live-event-states-billing-concept.md)
Existem também modos de espera disponíveis para iniciar o Live Event num estado de custo mais baixo 'atribuído' que torna mais rápido a mudança para um estado de 'Running'. Isto é útil para situações como piscinas quentes que precisam distribuir canais rapidamente para streamers.
* Para que um URL inger seja preditivo e mais fácil de manter num codificader ao vivo baseado em hardware, desapedaça a propriedade "useStaticHostname" para verdadeiro, bem como use um GUID personalizado único no "accessToken". Para obter informações [detalhadas, consulte Live Event ingerir URLs](live-event-outputs-concept.md#live-event-ingest-urls).

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveEvent)]

### <a name="create-an-asset-to-record-and-archive-the-live-event"></a>Criar um Ativo para gravar e arquivar o evento ao vivo

Neste bloco de código, irá criar um Ativo vazio para usar como "fita" para gravar o seu arquivo de eventos ao vivo.
Ao aprender estes conceitos, o melhor é pensar no objeto "Ativo" como a fita que você inseriria num gravador de vídeo nos velhos tempos. A "Saída Ao Vivo" é a máquina de gravação. O "Live Event" é apenas o sinal de vídeo que entra na parte de trás da máquina.

Tenha em mente que o Ativo, ou "fita", pode ser criado a qualquer momento. É apenas um "Ativo" vazio que irá entregar ao objeto De saída ao vivo, o gravador nesta analogia.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateAsset)]

### <a name="create-the-live-output"></a>Criar a saída ao vivo

Nesta secção, criamos uma Saída Ao Vivo que usa o nome Ativo como entrada para dizer para onde gravar o evento ao vivo. Além disso, configuramos a janela de mudança de tempo (DVR) para ser usada na gravação.
O código de amostra mostra como configurar uma janela de 1 hora de mudança de tempo. Isto permitirá que os clientes voltem a jogar em qualquer lugar na última hora do evento.  Além disso, apenas as últimas 1 hora do evento ao vivo permanecerão no arquivo. Pode estender isto até 25 horas, se necessário.  Note também que é capaz de controlar o manifesto de saída que dá nome aos manifestos HLS e DASH nos seus caminhos DE URL quando publicado.

A Saída Ao Vivo, ou "gravador de cassetes" na nossa analogia, também pode ser criada a qualquer momento. O que significa que pode criar uma saída ao vivo antes de iniciar o fluxo de sinal, ou depois. Se precisar de acelerar as coisas, muitas vezes é útil criá-la antes de iniciar o fluxo de sinal.

As Saídas Ao Vivo começam na criação e param quando apagadas.  Quando elimina a Saída Ao Vivo, não está a excluir o Ativo ou conteúdo subjacente no ativo. Pense nisso como ejetar a fita. O Ativo com a gravação durará o tempo que quiser, e quando for ejetado (ou seja, quando a Saída Ao Vivo for eliminada) estará disponível para visualização a pedido imediatamente.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveOutput)]


### <a name="get-ingest-urls"></a>Obter URLs de inserção

Uma vez criado o Live Event, poderás obter URLs ingeridos que irás fornecer ao codificadores ao vivo. O codificadores utiliza estes URLs para inserir um live stream utilizando o protocolo RTMP

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetIngestURL)]

### <a name="get-the-preview-url"></a>Obter o URL de pré-visualização

Utilize o previewEndpoint para pré-visualizar e certifique-se de que a entrada do codificador está a ser recebida.

> [!IMPORTANT]
> Certifique-se de que o vídeo está a fluir para o URL de pré-visualização antes de continuar.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetPreviewURL)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Criar e gerir Eventos Ao Vivo e Saídas Ao Vivo

Assim que tiver o stream fluindo para o Live Event, pode iniciar o evento de streaming publicando um Localizador de Streaming para os seus clientes usarem. Isto irá disponibilizá-lo aos espectadores através do Streaming Endpoint.

Primeiro cria-se o sinal criando o "Live Event".  O sinal não flui até iniciar o Live Event e ligar o codificante à entrada.

Para parar o "gravador de cassetes", ligue para apagar no LiveOutput. Isto não apaga o **conteúdo** do seu arquivo na fita "Ativo", apenas elimina o "gravador de cassetes" e para o arquivamento. O Ativo é sempre mantido com o conteúdo de vídeo arquivado até que ligue para apagar explicitamente no próprio Ativo. Assim que eliminar o liveOutput, o conteúdo gravado do "Ativo" ainda está disponível para reproduzir através de quaisquer URLs de Localizador de Streaming já publicados. Se desejar remover a capacidade de um cliente reproduzir o conteúdo arquivado, primeiro precisaria de remover todos os localizadores do ativo e também de lavar a cache cdN no caminho URL se estiver a utilizar um CDN para entrega. Caso contrário, o conteúdo viverá na cache do CDN para a definição padrão de tempo a viver no CDN (que pode chegar a 72 horas.)

#### <a name="create-a-streaming-locator-to-publish-hls-and-dash-manifests"></a>Criar um localizador de streaming para publicar manifestos HLS e DASH

> [!NOTE]
> Quando a sua conta De Serviços de Media é criada, um ponto final de streaming **predefinido** é adicionado à sua conta no estado **Stop.** Para começar a transmitir o seu conteúdo e tirar partido da [embalagem dinâmica](encode-dynamic-packaging-concept.md) e da encriptação dinâmica, o ponto final de streaming a partir do qual pretende transmitir conteúdo tem de estar no estado **de Funcionamento.**

Quando publicar o Ativo utilizando um Localizador de Streaming, o Evento Ao Vivo (até ao comprimento da janela DVR) continuará a ser visível até ao termo ou supressão do Localizador de Streaming, o que vier primeiro. É assim que disponibiliza a gravação virtual de "fita" para o seu público ver ao vivo e a pedido. O mesmo URL pode ser usado para assistir ao evento ao vivo, janela DVR ou o ativo a pedido quando a gravação estiver completa (quando a Saída Ao Vivo é eliminada.)

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateStreamingLocator)]

#### <a name="build-the-paths-to-the-hls-and-dash-manifests"></a>Construir os caminhos para os manifestos HLS e DASH

O método BuildManifestPaths na amostra mostra como criar deterministicamente os caminhos de streaming para usar para a entrega de DASH ou HLS a vários clientes e quadros de jogadores.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#BuildManifestPaths)]

## <a name="watch-the-event"></a>Ver o evento

Para assistir ao evento, copie o URL de streaming que obteve quando executou o código descrito no Create a Streaming Localizador. Podes usar um media player à tua escolha. [O Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) está disponível para testar o seu stream em https://ampdemo.azureedge.net .

O Live Event converte automaticamente eventos em conteúdo sonoro quando parado. Mesmo depois de parar e apagar o evento, os utilizadores podem transmitir o seu conteúdo arquivado como um vídeo a pedido enquanto não eliminar o ativo. Um ativo não pode ser eliminado se for usado por um evento; o evento deve ser apagado primeiro.

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Limpar os recursos na sua conta de Serviços de Multimédia

Se executar a aplicação durante todo o percurso, irá limpar automaticamente todos os recursos utilizados na função denominada "cleanUpResources". Certifique-se de que a aplicação ou depuração corre até ao fim ou pode perder recursos e acabar com a execução de eventos ao vivo na sua conta. Verifique duas vezes no portal Azure para confirmar que todos os recursos estão limpos na sua conta dos Media Services.  

No código de amostra, consulte o método **cleanUpResources** para obter detalhes.

> [!IMPORTANT]
> Deixar o Evento Ao Vivo a funcionar incorre nos custos de faturação. Esteja ciente, se o projeto/programa falhar ou for fechado por qualquer motivo, pode deixar o Evento Ao Vivo em funcionamento em estado de faturação.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Mais documentação do programador para Node.js em Azure

- [Azure para desenvolvedores de & Node.js JavaScript](/azure/developer/javascript/)
- [Código fonte dos Serviços de Mídia no @azure/azure-sdk-for-js Git Hub repo](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentação do pacote Azure para desenvolvedores de Node.js](/javascript/api/overview/azure/)


## <a name="next-steps"></a>Passos seguintes

[Transmitir ficheiros em fluxo](stream-files-tutorial-with-api.md)